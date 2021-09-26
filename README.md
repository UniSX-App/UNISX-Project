# UNISX-Project

## Цель проекта
Целью проекта является создание сервиса, предназначенного для выпуска синтетических токенов, цена которых привязана к цене акций и индексов на бирже, а также производных инструментов – опционов, фьючерсов, кредитных свопов.

## Минимально жизнеспособный продукт (MVP)
На начальном этапе предполагается создать минимально жизнеспособный продукт с минимальными усилиями и затратами.
Проект разрабатывается на платформе UMA.
Задачей MVP является проверка концепции и уточнение параметров проекта.
На этапе MVP для ускорения разработки предлагается создать два синтетических токена, привязанных к биржевым индексам и ETF, см. https://github.com/UMAprotocol/UMIPs/pull/399

## Архитектура MVC-B
При разработке проекта предполагается использование архитектуры MVC-B (Model, View, Control, Blockchain):
![image](https://user-images.githubusercontent.com/89580052/134306689-7cf6fd5b-630c-43b1-9a68-08d0b979301b.png)

1. Пользовательский интерфейс (VIEW LOGIC) - веб или мобильное приложение, взаимодействующее с управляющей логикой.
2. Модель данных (DATA MODEL) – схема локальных данных приложения, включая документы и большие файлы.
3. Управляющая логика (CONTROL LOGIC) – алгоритм, осуществляющий взаимодействие между пользовательским интерфейсом, моделью данных приложения и интерфейсом блокчейна (API).
4. Логика блокчейна (BLOCKCHAIN LOGIC) – расширение управляющей логики и модели данных на блокчейн. 
-	Управляющая логика дополняется смарт контрактами. 
-	Модель данных дополняется транзакциями.

## Пользовательский интерфейс
### ВЕБ-интерфейс
Предполагается создание ВЕБ-интерфейса, позволяющего пользователю выпустить синтетический токен, цена которого привязана к цене биржевого актива, поместить синтетический токен в пул ликвидности DEX, а также осуществлять стейкинг токенов проекта внутри проекта. За выполнение каждой из этих трех операций пользователь получает вознаграждение в токенах управления GT, токенах платформы OPIUM, а также токены пулов ликвидности LP.
ВЕБ-интерфейс включает следующие основные страницы:
![image](https://user-images.githubusercontent.com/89580052/134307104-9fa38231-6202-43a0-9429-1a80682244a8.png)

**Home** – домашняя страница, содержит общие сведения или рекламную информацию. При 
**Connect Wallet** – страница или всплывающее окно с перечнем кошельков к которым можно подключиться. Вызывается с домашней страницы если кошелек пользователя еще не подключен.
**Instruments/Portfolio** – страница на которой отображается перечень инструментов которые может выпустить пользователь, а также портфель пользователя.
**Mint** – интерфейс для выпуска и сжигания синтетических токенов.
**Pool** – интерфейс для передачи токенов в пулы ликвидности DEX и возврата из пулов.
**Stake** – интерфейс стейкинга токенов проекта.
**Vote** – интерфейс голосования.
**Doc** – документация сервиса.
**FAQ** – часто встречающиеся вопросы и ответы на них.
**About Us** – Сведения о команде проекта.
 
### Элементы интерфейса
На странице приложения отображаются следующие элементы:

![SPACsynt - contracts-UI2 (1)](https://user-images.githubusercontent.com/89580052/134804321-e3e0da96-291f-436b-913d-6772db1a8806.jpg)

**Элемент 1)** В правом верхнем углу располагается кнопка подключения к кошельку пользователя Connect Walet. Слева от этой кнопки - информационное поле, содержащее наименование сети и адрес кошелька.

**Элемент 2)** В левом верхнем углу располагается кнопка перехода к внешнему интерфейсу голосования Vote.

**Элемент 3)** Интерфейс INSTRUMENTS / PORTFOLIO, отображающий возможности выпуска производных и портфель пользователя.
Пользователь может выбрать строку в таблице INSTRUMENTS или PORTFOLIO. В соответствии с выбранной строкой предзаполняются данные в интерфейсах Элемента 2.

**Элемент 4)** Группа интерфейсов операций (MINT/POOL/STAKE) - пользователь выбирает вкладку, соответствующую требуемой ему операции – минтинг, помещение в пул ликвидности, стейкинг. 
Часть полей в этом элементе заполняется автоматически на основе выбранной в первом элементе строки.

#### Интерфейс MINT
Выпуск и сжигание синтетических токенов. 

![SPACsynt - contracts-MINT-UI (5)](https://user-images.githubusercontent.com/89580052/134817232-23fc1b70-e6d9-4d05-8092-e8ee3fccaaea.jpg)

Пользователь выбирает строку инструмента в таблице инструментов или в таблице портфеля (если пользователь уже выпускал такие токены), на вкладке автоматически заполняются: 

- поля левой части: 

    = Collateral (наименование токена), 

    = Synt ticker (наименование синтетика), 

    = количество соответствующих токенов в кошельке пользователя (зеленые цифры под названием токенов); 
    
- поля правой части: 
    
    = Collateral (наименование токена), 
    
    = количество соответствующих токенов в кошельке пользователя (зеленые цифры под названием токенов), 
    
    = количество токенов обеспечения для уже выпущенных синтетиков (поле слева от наименование токена обеспечения), 
    
    = Collateral Ratio = (Synt amount / Collateral amount) * Synt price, 
    
    = Liquidation Price - показывает при каком значении цены появляется риск ликвидации.

Для **выпуска** синтетического токена пользователь должен выполнить следующие действия:
- ввести количество токенов обеспечения, которое пользователь готов использовать для выпуска синтетических токенов, количество выпускаемых синтетиков заполняется автоматически с учетом collateral requirement *ИЛИ*
- ввести количество синтетических токенов, которое поьзователь желает выпустить, требуемое количество токенов обеспечения заполняется автоматически с учетом collateral requirement
- нажать кнопку MINT.

Для **сжигания** токена пользователь должен выполнить следующие действия:
- выбрать строку соответствующего токена в таблице портфеля
- ввести количество токенов обеспечения, которое пользователь желает получить, количество сжигаемых синтетиков заполняется автоматически с учетом collateral requirement *ИЛИ*
- ввести количество сжигаемых синтетических токенов, возвращаемое пользователю количество токенов обеспечения заполняется автоматически с учетом collateral requirement
- нажать кнопку BURN.

Поле Ticker заполняются по данным выбранной строки таблицы Instruments.
Поля UNISX Estimated, UMA Estimated, Collateral (calculated) – заполняются по результатам вычислений – надо сделать соответствующие функции-пустышки.
Collateral Type – список из массива, заполняемого по данным файла JSON.
Number – вводится пользователем.

Зеленые числа под полями с названием коллатерал или синтетика - количество соответствующих токенов в кошельке клиента (или в портфеле если это синтетик, а пользователь его спонсор). 
**Liquidation Price** - показывает при каком значении цены появляется риск ликвидации.
**Collateral Ratio** = (Synt amount / Collateral amount) * Synt price

#### Интерфейс POOL
Помещение/ возврат токенов в пул ликвидности DEX. Возможно с плечом.

![SPACsynt - contracts-POOL-UI (2)](https://user-images.githubusercontent.com/89580052/134803927-d1c7648c-6aea-403a-88e4-c96dc05dc61d.jpg)

Для помещения токенов проекта в пул ликвидности пользователь должен выбрать строку соответствующего токена в таблице портфеля, выбрать DEX и пул ликвидности, указать количество токенов, помещаемых в пул. Требуемое количество стейблкоинов (или тругих токенов пула), возможное вознаграждение рассчитываются и заполняются автоматически.
После этого пользователь нажимает кнопку POOL.
Для возврата токенов пользователь должен выбрать соответствующую строку с LP токеном в таблице портфеля и нажать кнопку UNPOOL.

#### Интерфейс STAKE
Перемещение/ возврат токенов проекта на контракт UniSX.Staker.

![SPACsynt - contracts-STAKE-UI (1)](https://user-images.githubusercontent.com/89580052/134803939-bf8e2cb9-0845-4d7a-8371-adbf1a30eed1.jpg)

Для того чтобы переместить токены проекта на контракт требуется выбрать соответствующую строку в таблице портфеля, указать количество токенов и нажать кнопку STAKE. После перемещения токены продолжают отображаться в таблице портфеля, но в отдельной строке с пометкой.
Для возврата токенов необходимо выбрать соответствующую строку с пометкой, указать количество и нажать кнопку UNSTAKE

#### Интерфейс REG
Служебный интерфейс для регистрации новых UniSX и удаления деривативов по которым принято такое решение по итогам голосования.

## Структура модулей
Система включает два типа модулей: УПРАВЛЯЮЩАЯ ЛОГИКА (off-chain) и ЛОГИКА БЛОКЧЕЙН (on-chain), взаимодействующие между собой и с пользователем:
![SPACsynt - contracts-Architecture 3](https://user-images.githubusercontent.com/89580052/134308055-99b53e17-ce76-4dde-9765-921a730a2f1a.jpg)
