Брокеры сообщений — это программные компоненты, которые выступают посредниками в обмене данными между различными системами, сервисами или приложениями. Они принимают сообщения от отправителей (продюсеров), обрабатывают их и перенаправляют соответствующим получателям (потребителям или подписчикам). Это позволяет снизить связанность компонентов системы, обеспечить асинхронность, масштабируемость и надёжность обмена данными. [```1```](https://timeweb.cloud/blog/brokery-soobshchenij)[```2```](https://selectel.ru/blog/message-broker/)[```4```](https://reg.cloud/blog/brokery-soobshchenij/)[```3```](https://journal.sweb.ru/article/brokery-soobscheniy-kak-rabotayut-sistemy-ocheredey-i-zachem-oni-nuzhny)

## Основные принципы работы брокеров сообщений

**Ключевые сущности:**
* **Продюсер (отправитель)** — компонент, который генерирует сообщения.
* **Потребитель (подписчик)** — компонент, который обрабатывает сообщения.
* **Топик (тема)** — логическая единица, объединяющая сообщения по определённому критерию. Используется в модели «публикация-подписка». [```1```](https://timeweb.cloud/blog/brokery-soobshchenij)
* **Очередь** — структура данных, где временно хранятся сообщения до их обработки. [```3```](https://journal.sweb.ru/article/brokery-soobscheniy-kak-rabotayut-sistemy-ocheredey-i-zachem-oni-nuzhny)

**Модели обмена данными:**
1. **Point-to-Point («точка-точка»)**. Сообщение обрабатывается только одним получателем. Подходит для сценариев, где важна последовательная обработка, например, финансовых транзакций. [```2```](https://selectel.ru/blog/message-broker/)
2. **Publish/Subscribe («публикация-подписка»)**. Один или несколько продюсеров публикуют сообщения в топик, а все подписанные на него потребители получают копии этих сообщений. Используется для распространения уведомлений, логов и т. д.. [```1```](https://timeweb.cloud/blog/brokery-soobshchenij)[```2```](https://selectel.ru/blog/message-broker/)

**Функции брокеров:**
* **Валидация** — проверка формата и содержания сообщений.
* **Маршрутизация** — определение места назначения для каждого сообщения.
* **Хранение** — временное сохранение сообщений, если получатель недоступен.
* **Доставка** — передача сообщений потребителям. [```2```](https://selectel.ru/blog/message-broker/)

## Популярные брокеры сообщений

* **Apache Kafka** — высокопроизводительный брокер, ориентированный на обработку больших объёмов данных в реальном времени. Использует концепцию топиков и разделов. [```11```](https://geekflare.com/dev/top-message-brokers/)[```12```](https://timeweb.cloud/tutorials/microservices/populyarnye-brokery-soobshchenij)
* **RabbitMQ** — брокер, работающий по протоколу AMQP. Поддерживает очереди и обмены (exchanges), которые маршрутизируют сообщения в очереди. [```11```](https://geekflare.com/dev/top-message-brokers/)[```12```](https://timeweb.cloud/tutorials/microservices/populyarnye-brokery-soobshchenij)
* **NATS** — лёгковесный брокер с моделью публикации/подписки. Ориентирован на простоту использования и высокую производительность. [```12```](https://timeweb.cloud/tutorials/microservices/populyarnye-brokery-soobshchenij)
* **Apache ActiveMQ** — брокер с открытым исходным кодом, поддерживающий множество протоколов. [```11```](https://geekflare.com/dev/top-message-brokers/)[```13```](https://www.ihc.ru/articles/o-brokerakh-soobshhenij-i-ikh-osobennostyakh.html)

## Интеграция с 1С:Документооборот

Интеграция 1С:Документооборота с брокерами сообщений позволяет автоматизировать обмен данными между системой документооборота и другими сервисами, обеспечить асинхронную обработку событий, масштабируемость и надёжность. [```1```](https://timeweb.cloud/blog/brokery-soobshchenij)[```6```](https://www.intervolga.ru/cases/optimization-edo/)

**Возможные сценарии интеграции:**
* отправка уведомлений о событиях в документообороте (например, создание, изменение или подписание документа);
* обработка событий, связанных с документами (например, запуск бизнес-процессов при поступлении нового документа);
* интеграция с внешними системами для обмена данными о документах (например, с системами ЭДО, хранилищами данных). [```1```](https://timeweb.cloud/blog/brokery-soobshchenij)

**Способы интеграции 1С с брокерами сообщений:**
1. **Внешние компоненты.** Разработка специальных компонентов для взаимодействия с брокером. Например, для Kafka существуют внешние компоненты на Rust/C++, Java, .NET. Для RabbitMQ можно использовать компоненты вроде PinkRabbitMQ. [```15```](https://dzen.ru/a/ZmV2J4HU2WoZzJQU)[```21```](https://www.koderline.ru/expert/narabotki/article-ispolzovanie-sompact-topikov-v-apache-kafka-i-integratsiya-s-1s-predpriyatie/)[```18```](https://habr.com/ru/companies/rdv-it/articles/944300/)[```9```](https://implecs.ru/about-us/blog/rabbit/)
2. **Использование посредников.** Создание промежуточного сервиса, который предоставляет API для взаимодействия с брокером. [```21```](https://www.koderline.ru/expert/narabotki/article-ispolzovanie-sompact-topikov-v-apache-kafka-i-integratsiya-s-1s-predpriyatie/)
3. **Через 1С:Шину или 1С:Исполнитель.** Эти платформы позволяют настраивать обмен сообщениями между 1С и брокерами. Например, 1С:Шина поддерживает обмен данными с RabbitMQ через протокол AMQP и с Apache ActiveMQ Artemis через стандарт JMS. [```17```](https://1cmycloud.com/console/help/esb/4.1/docs/topics/doc00797.html)[```8```](https://its.1c.ru/db/content/metod8dev/src/developers/dataexchange/esb/i8105995.htm)
4. **Вызов REST API.** Если брокер поддерживает REST API, можно взаимодействовать с ним через HTTP-запросы. [```21```](https://www.koderline.ru/expert/narabotki/article-ispolzovanie-sompact-topikov-v-apache-kafka-i-integratsiya-s-1s-predpriyatie/)

**Пример интеграции с RabbitMQ через 1С:Шину:**
1. В RabbitMQ создаётся очередь сообщений.
2. В среде разработки 1С:Шины создаётся проект с описанием схемы интеграции.
3. На сервере 1С:Шины заполняются параметры подключения к RabbitMQ (адрес, логин, пароль и т. д.).
4. Создаётся информационная система и включается в процесс интеграции.
5. В базе 1С:Предприятия добавляется сервис интеграции, загружаются каналы, пишется код обработки полученных сообщений. [```17```](https://1cmycloud.com/console/help/esb/4.1/docs/topics/doc00797.html)[```8```](https://its.1c.ru/db/content/metod8dev/src/developers/dataexchange/esb/i8105995.htm)

