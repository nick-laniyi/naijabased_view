NaijaBased - Performance Optimization & Scalability
1. Performance Philosophy
Core Principles:

Speed is a Feature: Every millisecond matters

Measure First, Optimize Later: Data-driven decisions

80/20 Rule: 80% of gains come from 20% of efforts

Mobile First: Optimize for limited bandwidth

Continuous Improvement: Performance is never "done"

2. Performance Metrics & SLAs
2.1 Key Performance Indicators
Metric	Target	Current	Status
Time to First Byte (TTFB)	<200ms	180ms	✅
First Contentful Paint (FCP)	<1.0s	0.9s	✅
Largest Contentful Paint (LCP)	<1.5s	1.2s	✅
First Input Delay (FID)	<100ms	65ms	✅
Cumulative Layout Shift (CLS)	<0.1	0.08	✅
Time to Interactive (TTI)	<2.0s	1.5s	✅
Total Blocking Time (TBT)	<200ms	150ms	✅
API Response Time (p95)	<300ms	245ms	✅
API Response Time (p99)	<500ms	420ms	✅
Error Rate	<0.5%	0.3%	✅
Uptime	99.95%	99.98%	✅
Cache Hit Ratio	>85%	87%	✅
Database Query Time	<50ms	48ms	✅
2.2 Performance Budgets
javascript
// Performance budget dashboard
const PERFORMANCE_BUDGET = {
  javascript: 250,     // KB
  css: 50,            // KB
  html: 30,           // KB
  images: 500,        // KB
  fonts: 50,          // KB
  total: 880,         // KB
  requests: 45,       // Number of requests
  api_latency: 300    // ms (p95)
};
3. Frontend Optimization
3.1 Critical CSS
html
<!-- Inline critical CSS for above-the-fold content -->
<style>
  /* Critical path CSS - minimal, essential styles */
  header, nav, .hero { display: block; }
  .btn { background: #2ecc71; color: white; }
  /* Less than 10KB */
</style>

<!-- Load non-critical CSS asynchronously -->
<link rel="preload" href="/assets/css/main.css" as="style" onload="this.onload=null;this.rel='stylesheet'">
<noscript><link rel="stylesheet" href="/assets/css/main.css"></noscript>
3.2 JavaScript Optimization
javascript
// Defer non-critical JavaScript
<script defer src="/assets/js/app.js"></script>

// Lazy load modules
import('./components/Feed.js').then(module => {
  const Feed = module.default;
  new Feed().init();
});

// Use requestIdleCallback for non-critical tasks
requestIdleCallback(() => {
  // Analytics, preloading, etc.
  preloadNextPage();
  prefetchUserData();
}, { timeout: 2000 });
3.3 Image Optimization
php
// Automatic WebP conversion with fallback
class ImageOptimizer {
    public function optimize($source_path, $target_path) {
        $image_info = getimagesize($source_path);
        $mime = $image_info['mime'];
        
        // Convert to WebP if supported
        if (function_exists('imagewebp')) {
            switch($mime) {
                case 'image/jpeg':
                    $image = imagecreatefromjpeg($source_path);
                    imagewebp($image, $target_path . '.webp', 80);
                    break;
                case 'image/png':
                    $image = imagecreatefrompng($source_path);
                    imagepalettetotruecolor($image);
                    imagealphablending($image, true);
                    imagesavealpha($image, true);
                    imagewebp($image, $target_path . '.webp', 80);
                    break;
            }
            imagedestroy($image);
        }
        
        // Generate responsive sizes
        $this->generateResponsiveSizes($source_path, [320, 640, 1024, 1920]);
    }
}

// HTML with responsive images
<picture>
    <source srcset="/uploads/image-320.webp 320w,
                    /uploads/image-640.webp 640w,
                    /uploads/image-1024.webp 1024w"
            type="image/webp">
    <img src="/uploads/image-640.jpg" 
         srcset="/uploads/image-320.jpg 320w,
                 /uploads/image-640.jpg 640w,
                 /uploads/image-1024.jpg 1024w"
         sizes="(max-width: 640px) 100vw, 640px"
         alt="Product image"
         loading="lazy">
</picture>
3.4 Font Optimization
css
/* Use font-display: swap to avoid invisible text */
@font-face {
    font-family: 'Inter';
    src: url('/assets/fonts/inter.woff2') format('woff2');
    font-weight: 400;
    font-style: normal;
    font-display: swap;
}

/* Preload critical fonts */
<link rel="preload" href="/assets/fonts/inter.woff2" as="font" type="font/woff2" crossorigin>

/* Subset fonts for Nigerian languages */
@font-face {
    font-family: 'Inter';
    src: url('/assets/fonts/inter-latin.woff2') format('woff2');
    unicode-range: U+0000-00FF, U+0131, U+0152-0153, U+02BB-02BC, U+02C6, U+02DA, U+02DC, U+2000-206F, U+2074, U+20AC, U+2122, U+2191, U+2193, U+2212, U+2215, U+FEFF, U+FFFD;
}
3.5 Caching Strategy
javascript
// Service Worker with stale-while-revalidate
self.addEventListener('fetch', event => {
    event.respondWith(
        caches.open('v1').then(cache => {
            return cache.match(event.request).then(cached => {
                const network = fetch(event.request).then(response => {
                    cache.put(event.request, response.clone());
                    return response;
                });
                return cached || network;
            });
        })
    );
});

// Cache API responses
fetch('/api/feed', {
    headers: {
        'Cache-Control': 'max-age=300, stale-while-revalidate=60'
    }
});
3.6 Frontend Performance Results
Optimization	Before	After	Improvement
Lighthouse Score	45	92	+47
Page Weight	2.8MB	450KB	84%
Requests	78	32	59%
Time to Interactive	4.2s	1.5s	64%
First Contentful Paint	2.8s	0.9s	68%
4. Backend Optimization
4.1 Database Query Optimization
sql
-- BEFORE: Slow query (3.2s)
SELECT * FROM marketplace_items 
WHERE category = 'Electronics' 
  AND city = 'Lagos' 
  AND price BETWEEN 10000 AND 50000 
  AND status = 'available'
ORDER BY created_at DESC 
LIMIT 20;

-- AFTER: Optimized query (0.12s)
-- 1. Add composite index
CREATE INDEX idx_marketplace_discovery ON marketplace_items
(category, city, price, status, created_at);

-- 2. Select only needed columns
SELECT id, title, price, images, city, created_at 
FROM marketplace_items 
WHERE category = 'Electronics' 
  AND city = 'Lagos' 
  AND price BETWEEN 10000 AND 50000 
  AND status = 'available'
ORDER BY created_at DESC 
LIMIT 20;

-- 3. Use EXPLAIN to verify
EXPLAIN SELECT ... -- Confirm index usage
4.2 Query Caching with Redis
php
class QueryCache {
    private $redis;
    private $ttl = 300; // 5 minutes
    
    public function remember($key, $callback) {
        $cached = $this->redis->get($key);
        
        if ($cached) {
            return unserialize($cached);
        }
        
        $result = $callback();
        $this->redis->setex($key, $this->ttl, serialize($result));
        
        return $result;
    }
    
    public function invalidate($pattern) {
        $keys = $this->redis->keys($pattern);
        if (!empty($keys)) {
            $this->redis->del($keys);
        }
    }
}

// Usage
$feed = $cache->remember('feed:user:12345', function() use ($db) {
    return $db->query("SELECT ...")->fetchAll();
});
4.3 Database Partitioning
sql
-- Partition by state for business listings
ALTER TABLE business_listings
PARTITION BY LIST COLUMNS(state) (
    PARTITION p_lagos VALUES IN ('Lagos'),
    PARTITION p_abuja VALUES IN ('Abuja', 'FCT'),
    PARTITION p_rivers VALUES IN ('Rivers'),
    PARTITION p_kaduna VALUES IN ('Kaduna'),
    PARTITION p_kano VALUES IN ('Kano'),
    PARTITION p_other VALUES IN (DEFAULT)
);

-- Partition by month for transactions
ALTER TABLE transactions
PARTITION BY RANGE (UNIX_TIMESTAMP(created_at)) (
    PARTITION p202601 VALUES LESS THAN (UNIX_TIMESTAMP('2026-02-01')),
    PARTITION p202602 VALUES LESS THAN (UNIX_TIMESTAMP('2026-03-01')),
    PARTITION p202603 VALUES LESS THAN (UNIX_TIMESTAMP('2026-04-01')),
    PARTITION p_future VALUES LESS THAN MAXVALUE
);
4.4 Pagination Optimization
php
// Cursor-based pagination (instead of OFFSET)
function getMarketplaceItems($cursor = null, $limit = 20) {
    $sql = "SELECT id, title, price, created_at 
            FROM marketplace_items 
            WHERE status = 'available'";
    
    if ($cursor) {
        $sql .= " AND created_at < ? AND id != ?";
        $params = [$cursor['created_at'], $cursor['id']];
    }
    
    $sql .= " ORDER BY created_at DESC, id DESC LIMIT ?";
    $params[] = $limit;
    
    return $db->query($sql, $params)->fetchAll();
}

// Benefits: O(1) vs O(n) for large offsets
4.5 Eager Loading (N+1 Problem)
php
// BAD: N+1 queries
$items = $db->query("SELECT * FROM marketplace_items LIMIT 20")->fetchAll();
foreach ($items as $item) {
    $seller = $db->query("SELECT * FROM users WHERE id = ?", [$item['user_id']])->fetch();
}

// GOOD: Single query with JOIN
$items = $db->query("
    SELECT i.*, u.username, u.avatar 
    FROM marketplace_items i
    JOIN users u ON i.user_id = u.id
    WHERE i.status = 'available'
    ORDER BY i.created_at DESC
    LIMIT 20
")->fetchAll();
4.6 Backend Performance Results
Operation	Before	After	Improvement
Homepage load	450ms	120ms	73%
Search query	3.2s	0.3s	91%
Marketplace listing	380ms	95ms	75%
Checkout process	2.1s	0.8s	62%
User profile load	290ms	65ms	78%
Event discovery	420ms	110ms	74%
Job listings	350ms	85ms	76%
5. Caching Strategy
5.1 Multi-Layer Caching
text
┌─────────────────┐
│  Browser Cache  │  Static assets: 1 day
└────────┬────────┘
         ↓
┌─────────────────┐
│   CDN (Edge)    │  Images, CSS, JS: 7 days
└────────┬────────┘
         ↓
┌─────────────────┐
│  Redis (RAM)    │  Sessions, queries: 5 min - 1 hour
└────────┬────────┘
         ↓
┌─────────────────┐
│ File Cache (SSD)│  HTML fragments: 1 hour
└────────┬────────┘
         ↓
┌─────────────────┐
│   Database      │  Primary storage
└─────────────────┘
5.2 Cache Configuration
php
class CacheConfig {
    const TTL = [
        'session' => 7200,        // 2 hours
        'user_profile' => 300,    // 5 minutes
        'feed' => 60,            // 1 minute
        'marketplace_list' => 300, // 5 minutes
        'business_details' => 600, // 10 minutes
        'event_list' => 300,     // 5 minutes
        'job_list' => 600,       // 10 minutes
        'static_html' => 3600,   // 1 hour
        'api_response' => 60,    // 1 minute
        'search_results' => 300, // 5 minutes
    ];
    
    const CACHE_PREFIX = 'naijabased:v1:';
}

// Cache keys
$key = CacheConfig::CACHE_PREFIX . "business:{$id}";
$cached = $redis->get($key);

if (!$cached) {
    $data = $db->query("SELECT * FROM business_listings WHERE id = ?", [$id])->fetch();
    $redis->setex($key, CacheConfig::TTL['business_details'], serialize($data));
}
5.3 Cache Invalidation Strategies
php
class CacheInvalidator {
    // Tag-based invalidation
    public function invalidateBusiness($business_id) {
        $tags = ["business", "business:{$business_id}", "category", "location"];
        
        foreach ($tags as $tag) {
            $keys = $this->redis->sMembers("tag:{$tag}");
            if ($keys) {
                $this->redis->del($keys);
            }
        }
    }
    
    // Pattern-based invalidation
    public function invalidateCity($city) {
        $pattern = CacheConfig::CACHE_PREFIX . "business:{$city}:*";
        $keys = $this->redis->keys($pattern);
        if ($keys) {
            $this->redis->del($keys);
        }
    }
    
    // Time-based invalidation (automatic via TTL)
    // Event-based invalidation (webhook triggers)
    public function handlePaymentSuccess($order_id) {
        $this->redis->del(CacheConfig::CACHE_PREFIX . "order:{$order_id}");
        $this->redis->del(CacheConfig::CACHE_PREFIX . "user:{$user_id}:orders");
    }
}
5.4 Cache Hit Ratios
Cache Layer	Hit Ratio	Miss Ratio	Avg Response
Browser Cache	72%	28%	0ms (local)
CDN	89%	11%	15ms
Redis	87%	13%	3ms
File Cache	65%	35%	8ms
Overall	83%	17%	12ms
6. CDN & Edge Computing
6.1 Cloudflare Configuration
javascript
// Cloudflare Workers for edge caching
addEventListener('fetch', event => {
    event.respondWith(handleRequest(event.request));
});

async function handleRequest(request) {
    const url = new URL(request.url);
    
    // Cache static assets at edge
    if (url.pathname.match(/\.(jpg|jpeg|png|gif|webp|css|js|woff2)$/)) {
        return fetch(request, {
            cf: {
                cacheTtl: 604800, // 7 days
                cacheEverything: true,
                cacheKey: url.pathname
            }
        });
    }
    
    // API responses - shorter TTL
    if (url.pathname.startsWith('/api/')) {
        return fetch(request, {
            cf: {
                cacheTtl: 60, // 1 minute
                cacheEverything: true
            }
        });
    }
    
    return fetch(request);
}
6.2 CDN Performance Impact
Metric	Without CDN	With CDN	Improvement
Global Avg Load Time	1.8s	0.9s	50%
Lagos (Nigeria)	1.2s	0.4s	67%
New York (USA)	2.4s	0.8s	67%
London (UK)	2.1s	0.7s	67%
Bandwidth Cost	$0.12/GB	$0.02/GB	83%
Origin Load	100%	11%	89%
7. Code Optimization
7.1 PHP OpCache
ini
; php.ini
opcache.enable=1
opcache.memory_consumption=256
opcache.interned_strings_buffer=16
opcache.max_accelerated_files=10000
opcache.revalidate_freq=60
opcache.fast_shutdown=1
opcache.enable_cli=0
opcache.validate_timestamps=1
opcache.revalidate_path=0
opcache.save_comments=1
opcache.load_comments=1
7.2 Composer Optimizations
json
{
    "scripts": {
        "optimize": [
            "composer dump-autoload --optimize",
            "php artisan config:cache",
            "php artisan route:cache"
        ]
    },
    "config": {
        "optimize-autoloader": true,
        "preferred-install": "dist",
        "sort-packages": true
    }
}
7.3 PHP 8.1 Performance Features
php
// JIT Compilation (PHP 8.0+)
opcache.jit = tracing
opcache.jit_buffer_size = 100M

// Match expressions (faster than switch)
$status = match($input) {
    'active' => 1,
    'pending' => 2,
    'closed' => 3,
    default => 0
};

// Constructor property promotion
class User {
    public function __construct(
        private int $id,
        private string $name,
        private string $email
    ) {}
}
8. Load Testing & Scalability
8.1 Load Testing Results
bash
# Using k6 for load testing
k6 run --vus 1000 --duration 30s loadtest.js
Test Scenario: Homepage + Feed

Concurrent Users	Avg Response	Error Rate	CPU	Memory
100	65ms	0%	12%	450MB
500	120ms	0%	35%	620MB
1,000	210ms	0.1%	68%	890MB
2,000	380ms	0.3%	89%	1.2GB
5,000	650ms	0.8%	95%	1.8GB
Test Scenario: Search API

Concurrent Users	Avg Response	Error Rate	CPU	Memory
100	45ms	0%	8%	380MB
500	95ms	0%	28%	520MB
1,000	185ms	0.2%	58%	780MB
2,000	320ms	0.5%	82%	1.1GB
8.2 Horizontal Scaling Triggers
yaml
# Auto-scaling configuration
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
    
  database_read_replicas:
    min: 2
    max: 5
    metric: connection_count
    threshold: 500
8.3 Database Connection Pooling
php
// Persistent connections
$pdo = new PDO('mysql:host=localhost;dbname=naijabased', 'user', 'pass', [
    PDO::ATTR_PERSISTENT => true,
    PDO::ATTR_TIMEOUT => 30,
    PDO::MYSQL_ATTR_INIT_COMMAND => "SET NAMES utf8mb4"
]);

// Connection pooling with ProxySQL
$proxysql = new PDO('mysql:host=127.0.0.1;port=6033;dbname=naijabased', 'user', 'pass');
9. Queue & Async Processing
9.1 RabbitMQ Configuration
php
class QueueService {
    private $connection;
    private $channel;
    
    public function __construct() {
        $this->connection = new AMQPStreamConnection(
            'localhost', 5672, 'guest', 'guest'
        );
        $this->channel = $this->connection->channel();
        $this->channel->queue_declare('email_queue', false, true, false, false);
    }
    
    public function push($job, $data) {
        $message = new AMQPMessage(json_encode([
            'job' => $job,
            'data' => $data,
            'attempts' => 0,
            'created_at' => time()
        ]), [
            'delivery_mode' => AMQPMessage::DELIVERY_MODE_PERSISTENT
        ]);
        
        $this->channel->basic_publish($message, '', 'email_queue');
    }
    
    public function worker() {
        $this->channel->basic_qos(null, 1, null);
        $this->channel->basic_consume('email_queue', '', false, false, false, false, function($msg) {
            $payload = json_decode($msg->body, true);
            
            try {
                // Process job
                $this->processJob($payload['job'], $payload['data']);
                $msg->delivery_info['channel']->basic_ack($msg->delivery_info['delivery_tag']);
            } catch (Exception $e) {
                // Retry logic
                if ($payload['attempts'] < 3) {
                    $payload['attempts']++;
                    $this->push($payload['job'], $payload['data']);
                }
                $msg->delivery_info['channel']->basic_nack($msg->delivery_info['delivery_tag']);
            }
        });
        
        while (count($this->channel->callbacks)) {
            $this->channel->wait();
        }
    }
}
9.2 Queue Throughput
Queue Type	Daily Volume	Peak Volume	Avg Processing
Email	200,000	15,000/hr	200ms
SMS	50,000	5,000/hr	150ms
Image Processing	25,000	2,000/hr	1.2s
Notification	500,000	30,000/hr	50ms
Webhook	150,000	10,000/hr	300ms
Report Generation	5,000	500/hr	5s
10. Monitoring & Alerting
10.1 Performance Monitoring Dashboard
javascript
// Custom metrics tracking
class PerformanceMonitor {
    trackPageLoad() {
        const metrics = {
            ttfb: performance.timing.responseStart - performance.timing.requestStart,
            fcp: performance.getEntriesByName('first-contentful-paint')[0]?.startTime,
            lcp: this.getLCP(),
            cls: this.getCLS(),
            fid: this.getFID()
        };
        
        navigator.sendBeacon('/api/analytics/perf', JSON.stringify(metrics));
    }
    
    trackAPIResponse(endpoint, duration, status) {
        fetch('/api/analytics/api-perf', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ endpoint, duration, status })
        });
    }
}
10.2 Alerting Rules
javascript
{
  "critical": {
    "api_error_rate": ">2% for 2 minutes",
    "payment_failure": ">1% for 1 minute",
    "database_down": "immediate",
    "server_down": "immediate",
    "disk_space": "<10%"
  },
  "warning": {
    "response_time": ">500ms for 5 minutes",
    "queue_backlog": ">5000 messages",
    "cache_miss": ">30%",
    "cpu_usage": ">80% for 10 minutes",
    "memory_usage": ">85%"
  },
  "info": {
    "deployment": "success/failure",
    "backup": "completed/failed",
    "daily_active_users": ">15,000"
  }
}
11. Performance Optimization Results
11.1 Overall Improvements (2024-2026)
Metric	Jan 2024	Feb 2026	Improvement
Page Load Time	4.2s	1.1s	73%
API Response	650ms	145ms	78%
Search Time	3.2s	0.3s	91%
Cache Hit Ratio	45%	87%	+42%
Server Cost	$2,800/mo	$1,700/mo	39%
Concurrent Users	2,000	10,000+	400%
Database Queries	45/req	12/req	73%
Page Weight	2.8MB	450KB	84%
Lighthouse Score	45	92	+47
11.2 Cost Savings
Area	Before	After	Annual Savings
Bandwidth	$1,200/mo	$450/mo	$9,000
Server Resources	$2,800/mo	$1,700/mo	$13,200
CDN	$800/mo	$200/mo	$7,200
Database	$1,500/mo	$900/mo	$7,200
Total	$6,300/mo	$3,250/mo	$36,600/year
12. Performance Roadmap
Q2 2026
Implement HTTP/3

Add Brotli compression

Deploy Elasticsearch for search

Achieve 95+ Lighthouse score

Q3 2026
Migrate to PHP 8.3

Implement GraphQL API

Add predictive prefetching

Achieve 99.99% uptime

Q4 2026
Database sharding implementation

Global CDN expansion (Africa regions)

Real-time analytics pipeline

100ms API response time (p95)

13. Performance Checklist
✅ Pre-Launch Checklist
Enable OpCache and JIT

Configure CDN

Implement Redis caching

Optimize images

Minify CSS/JS

Enable Gzip/Brotli

Set cache headers

Database indexes added

Slow query log enabled

Load testing completed

✅ Weekly Tasks
Review slow query log

Check cache hit ratios

Monitor error rates

Analyze performance metrics

Review CDN analytics

✅ Monthly Tasks
Full performance audit

Database maintenance (OPTIMIZE, ANALYZE)

Review and update indexes

Load test with current traffic patterns

Performance budget review

This performance document is continuously updated. Last reviewed: February 2026