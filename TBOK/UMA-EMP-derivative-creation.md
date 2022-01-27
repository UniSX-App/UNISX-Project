# Как создается срочный дериватив UMA
[UMA Development Guide](https://docs.umaproject.org/build-walkthrough/build-process)
## [The ExpiringMultiParty (EMP) Contract Template](https://docs.umaproject.org/synthetic-tokens/what-are-synthetic-assets#the-expiringmultiparty-emp-contract-template)
Можно написать [бесценные](https://docs.umaproject.org/synthetic-tokens/what-are-synthetic-assets#priceless-synthetic-tokens) шаблоны финансовых контрактов для создания различных видов финансовых продуктов. 
Первый тип синтетических контрактов на UMA используется для создания синтетических токенов с истекающим сроком действия и называется ExpiringMultiParty contract (EMP).<br>
EMP позволяет спонсорам токенов (т.е. пользователям, которые выпускают новые синтетические токены) обеспечивать свою позицию в определенной валюте обеспечения. 
В любое время после истечения срока действия EMP любой держатель синтетических токенов может обменять свои токены на расчетную стоимость, 
которая выражена в валюте обеспечения и привязана к цене price identifier контракта EMP по отметке времени истечения срока действия контракта. 
Расчетное значение определяется через 2 часа с момента истечения срока действия Оптимистичным Оракулом UMA. 
В редких случаях, когда цена оспаривается Оптимистичным Оракулом, будет вызываться Механизм проверки данных UMA (DVM) для разрешения спора в течение 48 часов.<br>
Больше о контракте EMP можно увидеть [здесь](https://docs.umaproject.org/synthetic-tokens/expiring-synthetic-tokens).<br>
## Для каждого EMP дериватива требуется:
1) EMP contract и синтетический токен:
Они создаются одной транзакцией c помощью ExpiringMultiPartyCreator, так что могут считаться неотделимыми друг от друга.
При создании EMP contract передаются следующие параметры: 
- priceFeedIdentifier, 
- syntheticName, 
- syntheticSymbol,
- expirationTimestamp, 
- collateralAddress, 
- collateralRequirement, 
- и другие параметры (disputeBondPercentage, sponsorDisputeRewardPercentage, disputerDisputeRewardPercentage, minSponsorTokens, timerAddress, liquidationLivenes, financialProductLibraryAddress)<br>
При создании экземпляра синтетического токена для существующего EMP contract необходимо указать количество токенов обеспечения и количество выпускаемых синтетических токенов.
2) Боты – используются для получения котировок извне и обработки ситуаций liquidation и dispute через DVM:
- Disputer
- Liquidator
Боты могут использоваться стандартные или быть модифицированы под требования создаваемого дериватива.
3) Price feed с помощью которого боты получают котировки по API
В репозитории UMA имеется файл (https://github.com/UMAprotocol/protocol/blob/master/packages/financial-templates-lib/src/price-feed/DefaultPriceFeedConfigs.ts), где для каждого идентификатора прописано, откуда брать цену, по какой формуле считать (если там есть какие-то расчеты). Этот файл затем входит в состав ботов. 
Бот достает Price identifier из контракта, смотрит в этом файле какой price feed использовать, получает цену.
Для базового актива мы используем price feed, который обращается к API MarketStake. 
4) Для того, чтобы включить Price feed в файл в репозитории UMA и связать его с соответствующим Price identifier или Funding rate identifier требуется пройти процедуру согласования UMIP
5) Однажды зарегистрировав Price Feed мы можем создавать на ее основе различные EMP контракты указывая в ExpiringMultiPartyCreator разные значения для следующих параметров:
- syntheticName, 
- syntheticSymbol,
- expirationTimestamp, 
- collateralAddress, 
- collateralRequirement


