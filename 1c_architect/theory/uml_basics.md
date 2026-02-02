
## **Синтаксис для диаграмм развертывания в Mermaid**

### **Основной синтаксис:**

```
%% Изначально был deploymentDiagram, но сейчас используется:
deployDiagram
```

**Но есть проблема:** В последних версиях Mermaid диаграммы развертывания были **переработаны** и теперь используют другой подход. Давайте рассмотрим реальные примеры:

### **Пример 1: Простая диаграмма развертывания**

```mermaid
graph TB
    subgraph "Клиентская часть"
        A[Браузер пользователя]
        B[Мобильное приложение]
    end
    
    subgraph "Серверная часть"
        C[Веб-сервер Nginx]
        D[Сервер приложений]
        E[База данных]
    end
    
    A --> C
    B --> C
    C --> D
    D --> E
```

### **Пример 2: Более сложная архитектура**

```mermaid
flowchart TD
    subgraph "Пользователи"
        U1[Десктоп браузер]
        U2[Мобильное устройство]
    end
    
    subgraph "AWS Cloud"
        subgraph "Публичная подсеть"
            LB[Load Balancer]
            WS1[Веб-сервер 1]
            WS2[Веб-сервер 2]
        end
        
        subgraph "Приватная подсеть"
            AS[Сервер приложений]
            DB[(База данных)]
            Cache[(Redis кеш)]
        end
    end
    
    subgraph "Внешние сервисы"
        PAY[Платежная система]
        EMAIL[Email сервис]
    end
    
    U1 --> LB
    U2 --> LB
    LB --> WS1
    LB --> WS2
    WS1 --> AS
    WS2 --> AS
    AS --> DB
    AS --> Cache
    AS --> PAY
    AS --> EMAIL
```

### **Пример 3: Контейнерная архитектура**

```mermaid
flowchart LR
    subgraph "Docker Host"
        subgraph "Контейнер 1"
            NG[Nginx<br/>Порт: 80]
        end
        
        subgraph "Контейнер 2"
            APP[Node.js приложение<br/>Порт: 3000]
        end
        
        subgraph "Контейнер 3"
            PSQL[(PostgreSQL<br/>Порт: 5432)]
        end
        
        subgraph "Контейнер 4"
            RD[(Redis<br/>Порт: 6379)]
        end
    end
    
    NG --> APP
    APP --> PSQL
    APP --> RD
```

### **Пример 4: Полная система с пояснениями**

```mermaid
flowchart TD
    Client[Клиентское устройство] --> DNS[DNS сервис]
    DNS --> CDN[CDN Cloudflare]
    
    CDN --> LoadBalancer
    
    subgraph "Кластер серверов"
        LoadBalancer[Балансировщик нагрузки] --> WebServer1
        LoadBalancer --> WebServer2
        LoadBalancer --> WebServer3
        
        WebServer1[Веб-сервер 1] --> AppServer
        WebServer2[Веб-сервер 2] --> AppServer
        WebServer3[Веб-сервер 3] --> AppServer
    end
    
    AppServer[Сервер приложений] --> PrimaryDB[(Primary DB)]
    AppServer --> ReadReplica[(Read Replica)]
    AppServer --> Cache[(Redis)]
    AppServer --> ObjectStorage[S3 Storage]
    
    PrimaryDB --> ReadReplica
    
    %% Стилизация
    classDef client fill:#e1f5fe
    classDef network fill:#f3e5f5
    classDef server fill:#e8f5e8
    classDef storage fill:#fff3e0
    classDef database fill:#ffebee
    
    class Client client
    class DNS,CDN,LoadBalancer network
    class WebServer1,WebServer2,WebServer3,AppServer server
    class ObjectStorage storage
    class PrimaryDB,ReadReplica,Cache database
```

## **Ключевые моменты:**

1. **В текущей версии Mermaid** для диаграмм развертывания лучше использовать:
   - `graph` / `flowchart` для структурных схем
   - `subgraph` для группировки компонентов

2. **Основные элементы:**
   - Прямоугольники `[компонент]` для узлов
   - Скругленные прямоугольники `(база данных)` для хранилищ
   - Стрелки `-->` для связей
   - Подграфы `subgraph` для группировки

3. **Для настоящих UML Deployment Diagrams** лучше использовать специализированные инструменты:
   - PlantUML
   - Draw.io
   - Lucidchart
   - Visual Paradigm

