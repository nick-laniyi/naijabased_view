🏗️ System Architecture
1. Overview
NaijaBased uses a modular monolith architecture - a single deployable application with clear separation of concerns through modules. This approach balances development speed with maintainability.

Why Modular Monolith?
Approach	Pros	Cons	Our Choice
Monolith	Simple, fast deployment	Scales poorly, hard to maintain	❌
Modular Monolith	Balanced, organized, easy refactoring	Single point of failure	✅
Microservices	Independent scaling, tech diversity	Complex ops, network latency	❌ (Future)
2. High-Level Architecture
text
Client Layer: Web Browser, Mobile Browser, USSD Gateway
         ↓
CDN & Edge: Cloudflare CDN, Image Optimization
         ↓
Load Balancer: Nginx/Apache LB
         ↓
Application Layer: Web Server 1, Web Server 2, Web Server 3, Static Assets
         ↓
Caching Layer: Redis Cache, File Cache, OpCache
         ↓
Queue Layer: RabbitMQ → Email Queue, SMS Queue, Image Queue
         ↓
Database Layer: MySQL Master → MySQL Slave 1, MySQL Slave 2, Backup
         ↓
Third Party Services: Paystack, Termii/Mocean, Brevo, Google Maps
3. Directory Structure (Modular)
text
naijabased/
├── 📁 app/                    # Core application code
│   ├── 📁 Http/
│   │   ├── 📁 Controllers/   # MVC Controllers
│   │   ├── 📁 Middleware/    # Auth, CSRF, Rate limiting
│   │   └── 📁 Requests/      # Form requests
│   ├── 📁 Models/            # Database models
│   └── 📁 Services/          # Business logic
│
├── 📁 modules/               # Feature modules
│   ├── 📁 Auth/             # Authentication module
│   ├── 📁 Users/            # User management
│   ├── 📁 Marketplace/      # E-commerce features
│   ├── 📁 Businesses/       # Business directory
│   ├── 📁 Events/           # Event management
│   ├── 📁 Jobs/            # Job board
│   ├── 📁 Communities/      # Social groups
│   └── 📁 Payments/         # Payment processing
│
├── 📁 api/                  # RESTful endpoints
│   ├── 📁 v1/              # API version 1
│   └── 📁 v2/              # Future version
│
├── 📁 resources/            # Frontend assets
│   ├── 📁 views/           # PHP templates
│   ├── 📁 js/              # JavaScript modules
│   ├── 📁 css/             # Stylesheets
│   └── 📁 images/          # Static images
│
├── 📁 database/            # Database layer
│   ├── 📁 migrations/      # Schema changes
│   └── 📁 seeds/          # Test data
│
├── 📁 config/             # Configuration
│   ├── app.php           # App settings
│   ├── database.php      # DB credentials
│   └── services.php      # API keys
│
└── 📁 storage/           # User-generated content
    ├── 📁 uploads/       # Images, files
    ├── 📁 logs/          # Application logs
    └── 📁 cache/         # Cached files
4. Request Lifecycle
text
User → Browser → CDN → Nginx → PHP-FPM → Redis → MySQL → Paystack → Browser → User
 1. Click "Buy Now"
 2. Request page (CDN cache miss)
 3. FastCGI to PHP-FPM
 4. Check session in Redis
 5. Query product from MySQL
 6. Initialize payment with Paystack
 7. Cache product in Redis
 8. Return JSON response
 9. Show payment modal
5. Design Patterns Implemented
5.1 Repository Pattern
php
// Centralized data access
interface BusinessRepositoryInterface {
    public function find($id);
    public function findByLocation($city, $category);
    public function getVerified();
}

class BusinessRepository implements BusinessRepositoryInterface {
    private $db;
    
    public function findByLocation($city, $category) {
        // Optimized query with caching
        return $this->cache->remember("business:{$city}:{$category}", 3600, function() {
            return $this->db->query(...);
        });
    }
}
5.2 Service Layer Pattern
php
// Business logic separation
class PaymentService {
    private $gateway; // Paystack
    
    public function processPayment($amount, $email) {
        // Idempotency check
        // Rate limiting
        // Fraud detection
        // Logging
        // Queue fallback
    }
}
5.3 Observer Pattern
php
// Event-driven architecture
class UserRegistered {
    public function handle($user) {
        event(new SendWelcomeEmail($user));
        event(new AwardSignupPoints($user));
        event(new NotifyAdmins($user));
        event(new TrackAnalytics($user));
    }
}
5.4 Factory Pattern
php
// Dynamic service instantiation
class SMSProviderFactory {
    public static function make($provider = null) {
        $provider = $provider ?? config('sms.default');
        
        return match($provider) {
            'termii' => new TermiiService(),
            'mocean' => new MoceanService(),
            'twilio' => new TwilioService(),
        };
    }
}
6. Caching Strategy
Multi-Level Caching
Level	Technology	Storage	TTL	Use Case
L1	Browser Cache	Local	1 hour	Static assets
L2	CDN	Edge	1 day	Images, CSS, JS
L3	Redis	Memory	5 min	Session, queries
L4	File Cache	Disk	1 hour	HTML fragments
Cache Invalidation
php
class CacheManager {
    // Tag-based invalidation
    public function invalidateBusiness($businessId) {
        Cache::tags(['business', "business:{$businessId}"])->flush();
    }
    
    // Pattern-based invalidation
    public function invalidateCity($city) {
        $keys = Redis::keys("business:{$city}:*");
        Redis::del($keys);
    }
}
7. Database Architecture
Master-Slave Replication
php
// Read/Write splitting
class Database {
    public function write($query, $params) {
        return $this->master->execute($query, $params);
    }
    
    public function read($query, $params) {
        // Load balancing across slaves
        $slave = $this->getRandomSlave();
        return $slave->execute($query, $params);
    }
}
Sharding Strategy
User data: Sharded by user_id mod 10

Business listings: Partitioned by state

Transactions: Time-based partitioning (monthly)

Messages: Sharded by conversation_id

8. Security Architecture
text
Firewall → WAF → Rate Limiter → Input Validation → CSRF Protection → XSS Filter → SQL Prevention → Output Encoding
Defense in Depth
Network: Cloudflare WAF, DDoS protection

Application: Input validation, prepared statements

Data: Encryption at rest, AES-256 for sensitive data

Access: RBAC, 2FA, session management

Audit: Comprehensive logging, SIEM integration

9. Scalability Design
Horizontal Scaling Triggers
CPU > 70% for 5 minutes → Add web server

DB connections > 500 → Add read replica

Redis memory > 80% → Cluster expansion

Queue length > 10,000 → Add workers

Auto-scaling Configuration
yaml
autoscaling:
  web_servers:
    min: 2
    max: 10
    metric: cpu_utilization
    threshold: 70
    cooldown: 300
  
  workers:
    min: 1
    max: 5
    metric: queue_size
    threshold: 1000
10. Monitoring & Observability
Key Metrics
Business Metrics: DAU, MAU, conversion rate, LTV

Technical Metrics: Response time, error rate, throughput

Infrastructure: CPU, memory, disk I/O, network

Security: Failed logins, suspicious activities

Alerting Rules
javascript
{
  "critical": {
    "error_rate": ">5% for 5 minutes",
    "payment_failure": ">2% for 1 minute",
    "database_down": "immediate"
  },
  "warning": {
    "response_time": ">2s for 10 minutes",
    "queue_backlog": ">5000 messages",
    "cache_miss": ">60%"
  }
}
11. Disaster Recovery
RTO/RPO Targets
RTO (Recovery Time Objective): 4 hours

RPO (Recovery Point Objective): 15 minutes

Backup Strategy
Database: Binlog replication + Daily snapshots

Files: S3 sync every hour

Config: Version control + encrypted backups

Recovery Playbook
Promote read replica to master

Restore latest backup if needed

Validate data integrity

Switch DNS

Notify stakeholders

12. Technical Decisions & Trade-offs
✅ Decisions That Paid Off
Modular monolith → Faster development, easier debugging

Redis for sessions → Zero downtime deployments

Prepared statements from day one → No SQL injection

Queue for async tasks → 200ms response time even under load

🔄 Things I'd Do Differently
Testing → Write tests earlier, not after features

API documentation → OpenAPI/Swagger from the start

Feature flags → Gradual rollouts would prevent issues

Type hints → PHP 8 strict typing everywhere

💡 Key Learnings
Premature optimization is the root of all evil

Monitoring before scaling - know your bottlenecks

Simplicity beats complexity 90% of the time

User feedback > Your assumptions