## Пример работы с API (Soap)

### Тестировал онлайн-сервис "Кредитный калькулятор". 

### Интерфейс Soap необходим для предоставления уменьшенного функционала калькулятора кредитов потенциальным клиентам.

### Доступ к API предоставляется по следующему URL (Endpoint):
http://creditcalculator.pointschool.ru/calculator/importCreditParams?ws=1

### WSDL документ доступен по адресу
http://creditcalculator.pointschool.ru/soap/BankCreditScheme3.wsdl

### XSD схема
http://creditcalculator.pointschool.ru/soap/BankCreditScheme.xsd

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
