NaijaBased - System Architecture Diagram
graph TB
    subgraph "🌐 Client Layer"
        A1[💻 Web Browser]
        A2[📱 Mobile App]
        A3[📲 USSD Gateway]
    end

    subgraph "⚡ CDN & Edge"
        B1[🌍 Cloudflare CDN]
        B2[🖼️ Image Optimization]
        B3[🛡️ WAF / DDoS Protection]
    end

    subgraph "⚖️ Load Balancing"
        C1[🔀 Nginx Load Balancer]
        C2[🔄 Auto-scaling Group]
    end

    subgraph "🖥️ Application Layer"
        D1[📦 Web Server 1]
        D2[📦 Web Server 2]
        D3[📦 Web Server 3]
        D4[📦 Web Server N]
        D5[📁 Static Assets]
    end

    subgraph "⚡ Caching Layer"
        E1[🔥 Redis Cluster]
        E2[💾 File Cache]
        E3[⚙️ OpCache]
        E4[🎯 Session Storage]
    end

    subgraph "📨 Queue Layer"
        F1[🐇 RabbitMQ]
        F2[📧 Email Queue]
        F3[📱 SMS Queue]
        F4[🖼️ Image Processing Queue]
        F5[🔔 Notification Queue]
    end

    subgraph "💾 Database Layer"
        G1[🗄️ MySQL Master]
        G2[📚 MySQL Slave 1]
        G3[📚 MySQL Slave 2]
        G4[📚 MySQL Slave N]
        G5[💽 Automated Backups]
    end

    subgraph "🔌 Third Party Services"
        H1[💳 Paystack]
        H2[📨 Termii / Mocean]
        H3[✉️ Brevo]
        H4[🗺️ Google Maps]
        H5[📦 Logistics APIs]
    end

    subgraph "📊 Monitoring"
        I1[📈 New Relic]
        I2[🚨 Sentry]
        I3[📉 Custom Dashboards]
        I4[🔔 Alert Manager]
    end

    %% Connections
    A1 --> B1
    A2 --> B1
    A3 --> C1
    
    B1 --> C1
    B1 --> B2
    B1 --> B3
    
    C1 --> C2
    C2 --> D1
    C2 --> D2
    C2 --> D3
    C2 --> D4
    
    D1 --> D5
    D2 --> D5
    D3 --> D5
    D4 --> D5
    
    D1 --> E1
    D2 --> E1
    D3 --> E1
    D4 --> E1
    
    D1 --> E2
    D1 --> E3
    D1 --> E4
    
    D1 --> F1
    D2 --> F1
    D3 --> F1
    D4 --> F1
    
    F1 --> F2
    F1 --> F3
    F1 --> F4
    F1 --> F5
    
    D1 --> G1
    D2 --> G1
    D3 --> G1
    D4 --> G1
    
    G1 --> G2
    G1 --> G3
    G1 --> G4
    G1 --> G5
    
    D1 --> H1
    D1 --> H2
    D1 --> H3
    D1 --> H4
    D1 --> H5
    
    D1 --> I1
    D1 --> I2
    D1 --> I3
    I3 --> I4
    
    %% Styling
    classDef client fill:#e1f5fe,stroke:#01579b,stroke-width:2px;
    classDef cdn fill:#fff3e0,stroke:#e65100,stroke-width:2px;
    classDef lb fill:#f3e5f5,stroke:#4a148c,stroke-width:2px;
    classDef app fill:#e8f5e9,stroke:#1b5e20,stroke-width:2px;
    classDef cache fill:#fff8e1,stroke:#ff6f00,stroke-width:2px;
    classDef queue fill:#ffebee,stroke:#b71c1c,stroke-width:2px;
    classDef db fill:#e0f7fa,stroke:#006064,stroke-width:2px;
    classDef third fill:#f9fbe7,stroke:#827717,stroke-width:2px;
    classDef monitor fill:#efebe9,stroke:#3e2723,stroke-width:2px;
    
    class A1,A2,A3 client;
    class B1,B2,B3 cdn;
    class C1,C2 lb;
    class D1,D2,D3,D4,D5 app;
    class E1,E2,E3,E4 cache;
    class F1,F2,F3,F4,F5 queue;
    class G1,G2,G3,G4,G5 db;
    class H1,H2,H3,H4,H5 third;
    class I1,I2,I3,I4 monitor;

📊 Data Flow Diagram
sequenceDiagram
    participant User as 👤 User
    participant Browser as 🌐 Browser
    participant CDN as ⚡ Cloudflare
    participant LB as ⚖️ Load Balancer
    participant App as 🖥️ Web Server
    participant Cache as 🔥 Redis
    participant DB as 🗄️ MySQL
    participant Queue as 📨 RabbitMQ
    participant Paystack as 💳 Paystack
    participant Email as ✉️ Brevo
    participant SMS as 📱 Termii

    User->>Browser: 1. Click "Buy Now"
    Browser->>CDN: 2. Request page
    CDN->>LB: 3. Cache miss
    LB->>App: 4. Route request
    App->>Cache: 5. Check session
    Cache-->>App: 6. Session data
    App->>DB: 7. Query product
    DB-->>App: 8. Product data
    App->>Paystack: 9. Initialize payment
    Paystack-->>App: 10. Payment URL
    App->>Cache: 11. Cache product
    App->>Queue: 12. Queue email
    Queue->>Email: 13. Send confirmation
    Email-->>User: 14. Email receipt
    App->>Queue: 15. Queue SMS
    Queue->>SMS: 16. Send alert
    SMS-->>User: 17. SMS notification
    App-->>Browser: 18. JSON response
    Browser-->>User: 19. Show payment modal
    User->>Paystack: 20. Complete payment
    Paystack-->>App: 21. Webhook (async)
    App->>DB: 22. Update order
    App->>Queue: 23. Queue seller notification
    App-->>Paystack: 24. 200 OK

🏭 Deployment Architecture
graph TD
    subgraph "☁️ AWS Region - eu-west-2 (London)"
        
        subgraph "🌐 VPC - 10.0.0.0/16"
            
            subgraph "🔒 Public Subnet A (10.0.1.0/24)"
                LB1[🔀 Nginx LB]
                NAT1[🌍 NAT Gateway]
            end
            
            subgraph "🔒 Public Subnet B (10.0.2.0/24)"
                LB2[🔀 Nginx LB]
                NAT2[🌍 NAT Gateway]
            end
            
            subgraph "🛡️ Private Subnet A (10.0.10.0/24)"
                WEB1[📦 Web Server 1]
                WEB2[📦 Web Server 2]
            end
            
            subgraph "🛡️ Private Subnet B (10.0.11.0/24)"
                WEB3[📦 Web Server 3]
                WEB4[📦 Web Server 4]
            end
            
            subgraph "💾 Private Subnet C (10.0.20.0/24)"
                REDIS1[🔥 Redis Master]
                REDIS2[🔥 Redis Replica]
                MQ1[🐇 RabbitMQ]
            end
            
            subgraph "🗄️ Private Subnet D (10.0.30.0/24)"
                DB1[🗄️ MySQL Master]
                DB2[📚 MySQL Slave]
                DB3[📚 MySQL Slave]
            end
            
            subgraph "📁 Private Subnet E (10.0.40.0/24)"
                EFSS[📁 EFS Storage]
            end
            
        end
        
        S3[☁️ S3 Static Assets]
        R53[🌐 Route 53]
        CW[📊 CloudWatch]
        
    end
    
    subgraph "🌍 Edge"
        CF[⚡ Cloudflare]
    end
    
    CF --> R53
    R53 --> LB1
    R53 --> LB2
    
    LB1 --> WEB1
    LB1 --> WEB2
    LB2 --> WEB3
    LB2 --> WEB4
    
    WEB1 --> REDIS1
    WEB2 --> REDIS1
    WEB3 --> REDIS1
    WEB4 --> REDIS1
    
    REDIS1 --> REDIS2
    
    WEB1 --> MQ1
    WEB2 --> MQ1
    WEB3 --> MQ1
    WEB4 --> MQ1
    
    WEB1 --> DB1
    WEB2 --> DB1
    WEB3 --> DB1
    WEB4 --> DB1
    
    DB1 --> DB2
    DB1 --> DB3
    
    WEB1 --> EFSS
    WEB2 --> EFSS
    WEB3 --> EFSS
    WEB4 --> EFSS
    
    WEB1 --> S3
    WEB2 --> S3
    WEB3 --> S3
    WEB4 --> S3
    
    WEB1 --> CW
    WEB2 --> CW
    WEB3 --> CW
    WEB4 --> CW
    DB1 --> CW
    REDIS1 --> CW
    
    NAT1 --> INTERNET[🌐 Internet]
    NAT2 --> INTERNET

🔄 CI/CD Pipeline
graph LR
    subgraph "👨‍💻 Developer"
        CODE[✏️ Code Commit]
    end
    
    subgraph "🐙 GitHub"
        REPO[📦 Repository]
        ACTIONS[⚙️ GitHub Actions]
    end
    
    subgraph "🧪 Testing"
        UNIT[✅ Unit Tests]
        LINT[🔍 Linting]
        SEC[🛡️ Security Scan]
        INT[🔄 Integration Tests]
    end
    
    subgraph "📦 Build"
        COMPOSER[📦 Composer]
        NPM[📦 npm]
        ASSETS[🎨 Assets]
    end
    
    subgraph "🚀 Deploy"
        DEPLOY[📤 Deployer]
        MIGRATE[🗄️ Migrations]
        CACHE[🔥 Cache Clear]
        HEALTH[💓 Health Check]
    end
    
    subgraph "🌍 Environments"
        DEV[🧪 Development]
        STAGING[🧪 Staging]
        PROD[🚀 Production]
    end
    
    CODE --> REPO
    REPO --> ACTIONS
    
    ACTIONS --> UNIT
    ACTIONS --> LINT
    ACTIONS --> SEC
    ACTIONS --> INT
    
    UNIT --> COMPOSER
    LINT --> NPM
    SEC --> ASSETS
    
    COMPOSER --> DEPLOY
    NPM --> DEPLOY
    ASSETS --> DEPLOY
    
    DEPLOY --> MIGRATE
    MIGRATE --> CACHE
    CACHE --> HEALTH
    
    HEALTH --> DEV
    DEV --> STAGING
    STAGING --> PROD
    
    PROD --> MONITOR[📊 Monitoring]
    MONITOR --> ALERT[🔔 Alerts]
    ALENT -.-> ROLLBACK[↩️ Auto Rollback]