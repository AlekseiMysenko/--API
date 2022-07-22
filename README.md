## Пример работы с API

### Тестировал онлайн-сервис "Кредитный калькулятор". 
### Для тестирования SOAP использовал SoapUI 5.7.0

### Интеграция делится на Rest-сервис и Soap-сервис.

## 1. SOAP

### Интерфейс Soap необходим для предоставления уменьшенного функционала калькулятора кредитов потенциальным клиентам.

### Доступ к API предоставляется по следующему URL (Endpoint):
http://creditcalculator.pointschool.ru/calculator/importCreditParams?ws=1

### WSDL документ
https://github.com/AlekseiMysenko/API-Soap/blob/main/BankCreditScheme3.wsdl

### XSD схема
https://github.com/AlekseiMysenko/API-Soap/blob/main/BankCreditScheme.xsd

### Список используемых тегов в запросе:

| Параметр | Тип | Описание |
|:--------:|:---:|:--------|
|DATE|Datetime|Дата и время в формате YYYY-MM-DDTHH-MM-SS|
|MessageGUID|GUID|Сгенерированный гуид для уникальной идентификации сообщения. Паттерн: ([0-9a-fA-F]){8}-([0-9a-fA-F]){4}-([0-9a-fA-F]){4}-([0-9a-fA-F]){4}-([0-9a-fA-F]){12}|
|Amount|Int|Сумма кредита от 100000 до 500000 руб|
|Fee|Int|Сумма первоначального взноса. От 0 до 50% от суммы кредита|
|Term|Int|Срок кредита. От 12 до 60 месяцев|
|client|boolean|Является ли человек нашим клиентом. 0 - нет, 1 - клиент нашего банка|
|PreviousConviction|boolean|Есть ли у человека судимость. 0 - нет. 1 - есть|

### Groovy-вставки
```
${=new Date().format("yyyy-MM-dd'T'HH:mm:ss")} - Для даты в формате 2019-02-06T13:22:17
```
```
${=java.util.UUID.randomUUID()} - Для уникального идентификатора сообщения 44708c78-2efe-11ea-978f-2e728ce88125
```
### Запрос SOAP
```
<soapenv:Envelope>
   <soapenv:Header>
      <imp:HeaderRequest>
         <imp:Date>${=new Date().format("yyyy-MM-dd'T'HH:mm:ssXXX")}</imp:Date>
         <imp:MessageGUID>${=java.util.UUID.randomUUID()}</imp:MessageGUID>
      </imp:HeaderRequest>
   </soapenv:Header>
   <soapenv:Body>
      <imp:importCreditParams imp:client="1" imp:PreviousConviction="0">
         <imp:Credit>
            <imp:Amount>100000</imp:Amount>
            <imp:Fee>40000</imp:Fee>
            <imp:Term>20</imp:Term>
         </imp:Credit>
      </imp:importCreditParams>
   </soapenv:Body>
</soapenv:Envelope>
```
### Ответ SOAP
#### Интерфейс возвращает ответ XML формате
```
<Envelope>
   <header>
      <Date>2018-09-13T23:30:17+04:00</Date>
      <messageGUID>df8aeffc-5c02-4715-92c0-236955a50562</messageGUID>
   </header>
   <body>
      <CreditResponce>
         <Interest>52,45 %</Interest>
         <MonthlyPayment>2724 Р</MonthlyPayment>
         <OverPayments>9911 Р</OverPayments>
         <Total>42599 Р</Total>
      </CreditResponce>
   </body>
</Envelope>
```
### Параметры ответа Soap

| Параметр | Тип | Описание |
|:--------:|:---:|:--------|
|Date|Datetime|Дата и время в формате YYYY-MM-DD HH-MM-SS|
|messageGUID|Int|Сгенерированный гуид для уникальной идентификации сообщения|
|Interest|string|Процентная ставка|
|MonthlyPayment|string|Ежемесячный платеж|
|OverPayments|string|Переплата по кредиту|
|Total|string|Выплаты за весь срок кредита|

![Пример запрос-ответ](https://github.com/AlekseiMysenko/API-Soap/blob/main/Скриншот%2021-07-2022%20223130.jpg)

### В SoapUI 5.7.0 создал Test Suit с позитивными и негативными проверками, а также завел найденые баги.

![Запросы и баги](https://github.com/AlekseiMysenko/API-Soap/blob/main/Скриншот%2021-07-2022%20223358.jpg)

## 2. REST

### Rest-сервис имеет доступ до базы данных с клиентами.
### При взаимодействии с сервисом можно читать, добавлять, редактировать и удалять записи в таблице клиентов.

#### Сервис находится по ссылке 
http://creditcalculator.pointschool.ru/users/api/bank

#### Метод передачи GET

#### JSON-интерфейс (с пустыми параметрами):
http://creditcalculator.pointschool.ru/users/api/bank?users=&series=&number=

#### JSON-интерфейс (все записи):
http://creditcalculator.pointschool.ru/users/api/bank?users=all

### Параметры запроса Rest на чтение
|Параметр|Тип|Описание|
|:--------:|:---:|:--------|
|users|string|Поиск только по фамилии среди клиентов. Значение all для получения всех записей|
|series|int|Поиск по серии паспорта клиента|
|number|int|Поиск по номеру паспорта клиента|
##### Все параметры необязательны, но для поиска должен быть выбран как минимум один из них.

### Ответ REST
```
[{
   "name": "Ярмоненко Феофан Венедиктович",
   "passport": "43 00 542251",
   "created": "2018-08-24 07:20:17",
   "updated": "2018-08-24 07:20:17"
}]
```
### Параметры ответа
|Параметр|Тип|Описание|
|:--------:|:---:|:--------|
|name|string|Фамилия Имя Отчество клиента|
|passport|int|Серия и номер паспорта клиента|
|created|datatime|Дата создания записи|
|updated|datatime|Дата обновления записи|



