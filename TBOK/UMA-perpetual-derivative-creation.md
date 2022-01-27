# Как создается бессрочный дериватив UMA

[UMA Development Guide](https://docs.umaproject.org/build-walkthrough/build-process)

## [The Perpetual Contract Template](https://docs.umaproject.org/synthetic-tokens/what-are-synthetic-assets#the-perpetual-contract-template)
Другим шаблоном контракта, одобренным для использования на UMA, является perpetual, который можно использовать для создания [синтетических токенов](https://docs.umaproject.org/synthetic-tokens/what-are-synthetic-assets), которые следуют за движением цены любого актива без даты истечения срока действия.

Как и [EMP (Expiting Multi Party)](https://docs.umaproject.org/synthetic-tokens/what-are-synthetic-assets#the-expiringmultiparty-emp-contract-template), perpetual позволяет спонсорам токенов обеспечивать свою позицию в определенной валюте обеспечения и отслеживать цену другого актива с помощью определенной ценовой ленты. Синтетический токен может быть выкуплен спонсорами токенов в любое время за его стоимость в базовом обеспечении, а стоимость синтетического токена по отношению к базовому обеспечению повышается или понижается с помощью механизма ставок финансирования. Изменяя значение синтетического, механизм ставки финансирования эффективно выплачивает спонсору токена комиссию, когда цена бессрочного актива торгуется с премией к базовому индексу, и выплачивает держателю токена комиссию, когда бессрочный актив торгуется со скидкой к базовому индексу. Цель ставки финансирования состоит в том, чтобы связать движение цены синтетического продукта с движением индекса.

Важно отметить, что цена синтетика не будет точно отражать отслеживаемую цену, точно так же, как цена золотого ETF не точно отражает цену золота. Однако с помощью механизма ставки финансирования (**funding rate**) стоимость синтетического продукта должна расти или снижаться пропорционально отслеживаемому индексу.

Новые ставки финансирования могут быть предложены через оптимистичный oracle, и при ликвидации спонсоров токенов будут учитываться как отслеживаемая цена, так и совокупная ставка финансирования, чтобы определить, достаточно ли у спонсора залогового обеспечения. Как и при ликвидации, ставки финансирования могут быть оспорены и урегулированы держателями токенов UMA через DVM.

## Для каждого perpetual дериватива требуется:

**1)** [Perpetual contract](https://github.com/UMAprotocol/protocol/tree/master/packages/core/contracts/financial-templates/perpetual-multiparty) и синтетический токен:
-	Они создаются одной транзакцией c помощью PerpetualCreator, так что могут считаться неотделимыми друг от друга.
-	При создании Perpetual contract в качестве параметров передаются два идентификатора: [Price identifier](https://docs.umaproject.org/synthetic-tokens/glossary#price-identifier) и Funding rate identifier

**2)** Боты – используются для получения котировок извне и обработки ситуаций liquidation и dispute через DVM:
-	[Disputer](https://github.com/UMAprotocol/protocol/tree/master/packages/disputer/src)
-	[Liquidator](https://github.com/UMAprotocol/protocol/tree/master/packages/liquidator/src)
-	[Funding rate proposer](https://github.com/UMAprotocol/protocol/tree/master/packages/funding-rate-proposer/src)
-	[Funding rate disputer](https://github.com/UMAprotocol/protocol/tree/master/packages/disputer) - тоже самое что Disputer для Price identifier, но только для funding rate

                        *Боты могут использоваться стандартные или быть модифицированы под требования создаваемого дериватива.*

**3)** [Price feed](https://github.com/UMAprotocol/protocol/tree/master/packages/financial-templates-lib/src/price-feed) с помощью которого боты получают котировки по API
-	В репозитории UMA имеется файл (https://github.com/UMAprotocol/protocol/blob/master/packages/financial-templates-lib/src/price-feed/DefaultPriceFeedConfigs.ts), где для каждого идентификатора прописано, откуда брать цену, по какой формуле считать (если там есть какие-то расчеты). Если примут наш UMIP, то мы внесем изменения в этот файл. Этот файл затем входит в состав ботов. 
-	Бот достает Price identifier или Funding rate identifier из контракта, смотрит в этом файле какой price feed использовать, получает цену.
-	Для базового актива мы используем price feed, который обращается к API MarketStake. 
-	Для funding rate надо сделать более сложный price feed, который берет цену синтетического токена, например с uniswap, цену базового актива, и вычисляет funding rate.
- [Price Feed Configuration](https://docs.umaproject.org/developers/pf-configuration)

**4)** Для того, чтобы включить Price feed в файл в репозитории UMA и связать его с соответствующим Price identifier или Funding rate identifier требуется пройти [процедуру](https://docs.umaproject.org/uma-tokenholders/guidance-on-adding-price-identifiers) согласования [UMIP](https://github.com/UMAprotocol/UMIPs/blob/master/price-identifier-template.md)
-	Предполагается, что потребуется отдельные UMIP для Price identifier и для Funding rate identifier.
-	Есть предположение, что все можно описать и согласовать в одном UMIP
