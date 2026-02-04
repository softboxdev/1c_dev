# Тест по интеграциям 1С: REST API и брокеры сообщений

**Время выполнения:** 30 минут  
**Формат:** 20 вопросов  
**Тип:** Выбор одного или нескольких вариантов, краткий ответ

---

## Часть 1: Основы REST API (1-10 вопросы)

### 1. Что означает аббревиатура REST?
- A) Remote Execution and Storage Technology
- B) Representational State Transfer ✅
- C) Resource Exchange Standard Template
- D) Rapid Enterprise System Transfer

### 2. Какие HTTP методы соответствуют CRUD операциям? (выберите все верные)
- A) GET → SELECT ✅
- B) POST → INSERT ✅
- C) PUT → UPDATE ✅
- D) DELETE → DELETE ✅
- E) PATCH → MODIFY ✅

### 3. Какой HTTP код означает "Ресурс не найден"?
- A) 200
- B) 400
- C) 404 ✅
- D) 500

### 4. Что означает принцип "stateless" в REST?
- A) Сервер не хранит состояние клиента между запросами ✅
- B) Клиент не может изменять ресурсы
- C) API не требует аутентификации
- D) Все ответы кэшируются

### 5. Какой заголовок HTTP указывает на формат отправляемых данных?
- A) Accept
- B) Content-Type ✅
- C) Authorization
- D) User-Agent

### 6. Каков правильный формат для Bearer Token аутентификации?
- A) `Authorization: Token abc123`
- B) `Authorization: Bearer abc123` ✅
- C) `X-Auth-Token: abc123`
- D) `API-Key: abc123`

### 7. Какой HTTP метод используется для частичного обновления ресурса?
- A) PUT
- B) POST
- C) PATCH ✅
- D) UPDATE

### 8. Что означает HTTP код 201?
- A) Успешный запрос
- B) Ресурс создан ✅
- C) Нет содержимого
- D) Запрос принят в обработку

### 9. Какой заголовок обычно возвращается при успешном создании ресурса (201)?
- A) Content-Type
- B) Location ✅
- C) ETag
- D) Cache-Control

### 10. Что такое "endpoint" в контексте REST API?
- A) Точка завершения программы
- B) URL для доступа к конкретному ресурсу или коллекции ресурсов ✅
- C) Метод аутентификации
- D) Формат данных

---

## Часть 2: RabbitMQ и очереди сообщений (11-15 вопросы)

### 11. Почему 1С не может работать с RabbitMQ напрямую через AMQP?
- A) 1С не поддерживает TCP соединения
- B) Отсутствуют нативные библиотеки для протокола AMQP ✅
- C) RabbitMQ не совместим с Windows
- D) AMQP требует специального оборудования

### 12. Какие существуют обходные пути для интеграции 1С с RabbitMQ? (выберите все верные)
- A) Использование REST API RabbitMQ ✅
- B) Промежуточный сервис на Python/Java ✅
- C) Прямое подключение к базе данных RabbitMQ
- D) Использование файлового обмена

### 13. Какой порт используется для HTTP API RabbitMQ Management?
- A) 5672
- B) 15672 ✅
- C) 80
- D) 443

### 14. Что означает ACK в контексте очередей сообщений?
- A) Acknowledgement - подтверждение обработки сообщения ✅
- B) Access Control Key
- C) Automated Content Knowledge
- D) Asynchronous Communication Kernel

### 15. Какой метод REST API RabbitMQ используется для отправки сообщений?
```
A) POST /api/queues/{vhost}/{queue}/publish
B) POST /api/exchanges/{vhost}/{exchange}/publish ✅
C) PUT /api/messages/{queue}
D) GET /api/send/{queue}
```

---

## Часть 3: Практические задачи (16-20 вопросы)

### 16. Напишите HTTP запрос для получения пользователя с ID 123 в формате JSON (базовый URL: https://api.example.com/v1/):
```
______
```

### 17. Какие два основных недостатка использования REST API RabbitMQ вместо прямого AMQP подключения?
1. ______
2. ______

### 18. Напишите код на языке 1С для отправки POST запроса к /api/users с JSON телом:
```bsl
// Ваш код здесь
```

### 19. Объясните разницу между PUT и PATCH методами:
```
______
______
```

### 20. Что произойдет с сообщением в очереди RabbitMQ, если consumer получит его, но не отправит ACK?
```
______
```

---

