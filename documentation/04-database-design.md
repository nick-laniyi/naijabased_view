📊 NaijaBased - Database Design & Architecture
1. Database Philosophy
Core Principles:

Data Integrity: Foreign keys, constraints, ACID compliance

Performance: Indexing strategy, query optimization, partitioning

Scalability: Master-slave replication, sharding readiness

Nigerian Context: Location data, currency, phone number formats

Auditability: Created/updated timestamps, soft deletes, audit logs

2. Entity Relationship Diagram (Text Representation)
text
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│     users       │       │   businesses    │       │ marketplace_items│
├─────────────────┤       ├─────────────────┤       ├─────────────────┤
│ id (PK)         │◄──────│ user_id (FK)    │       │ id (PK)         │
│ username        │       │ id (PK)         │       │ user_id (FK)    │◄────┐
│ email           │       │ business_name   │       │ title           │     │
│ password        │       │ slug            │       │ price           │     │
│ full_name       │       │ category        │       │ condition       │     │
│ avatar          │       │ city            │       │ city            │     │
│ phone           │       │ state           │       │ status          │     │
│ is_verified     │       │ is_verified     │       │ created_at      │     │
│ is_admin        │       │ status          │       └─────────────────┘     │
│ created_at      │       └─────────────────┘                              │
└─────────────────┘                                                       │
         │                                         ┌─────────────────┐     │
         │                                         │    orders       │     │
         ▼                                         ├─────────────────┤     │
┌─────────────────┐       ┌─────────────────┐      │ id (PK)         │     │
│    profiles     │       │     events      │      │ buyer_id (FK)   │◄────┤
├─────────────────┤       ├─────────────────┤      │ seller_id (FK)  │◄────┤
│ id (PK)         │       │ id (PK)         │      │ item_id (FK)    │◄────┘
│ user_id (FK)    │◄──────│ user_id (FK)    │      │ amount          │
│ bio             │       │ title           │      │ status          │
│ location        │       │ start_date      │      │ payment_ref     │
│ website         │       │ end_date        │      │ created_at      │
│ social_links    │       │ location        │      └─────────────────┘
└─────────────────┘       │ ticket_price    │               │
         │                │ capacity        │               │
         │                └─────────────────┘               │
         │                         │                        │
         │                         ▼                        ▼
┌─────────────────┐       ┌─────────────────┐      ┌─────────────────┐
│   followers     │       │   bookings      │      │   payments      │
├─────────────────┤       ├─────────────────┤      ├─────────────────┤
│ id (PK)         │       │ id (PK)         │      │ id (PK)         │
│ follower_id (FK)│       │ event_id (FK)   │      │ order_id (FK)   │
│ following_id (FK)│      │ user_id (FK)    │      │ reference       │
│ created_at      │       │ ticket_type     │      │ amount          │
└─────────────────┘       │ quantity        │      │ status          │
                          │ qr_code         │      │ channel         │
                          └─────────────────┘      │ paid_at         │
                                                   └─────────────────┘
3. Core Tables Schema
3.1 Users Table
sql
CREATE TABLE `users` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `username` varchar(50) COLLATE utf8mb4_unicode_ci NOT NULL,
  `email` varchar(100) COLLATE utf8mb4_unicode_ci NOT NULL,
  `password` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `full_name` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `avatar` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT 'default-avatar.png',
  `phone` varchar(20) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `bio` text COLLATE utf8mb4_unicode_ci,
  `location` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `state` varchar(50) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `city` varchar(50) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `is_verified` tinyint(1) DEFAULT 0,
  `is_admin` tinyint(1) DEFAULT 0,
  `is_business` tinyint(1) DEFAULT 0,
  `is_professional` tinyint(1) DEFAULT 0,
  `remember_token` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `reset_token` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `reset_expires` datetime DEFAULT NULL,
  `last_login` datetime DEFAULT NULL,
  `login_count` int(11) DEFAULT 0,
  `status` enum('active','suspended','banned','pending') COLLATE utf8mb4_unicode_ci DEFAULT 'pending',
  `created_at` timestamp NOT NULL DEFAULT current_timestamp(),
  `updated_at` timestamp NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp(),
  `deleted_at` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `username` (`username`),
  UNIQUE KEY `email` (`email`),
  UNIQUE KEY `phone` (`phone`),
  KEY `idx_status` (`status`),
  KEY `idx_location` (`state`,`city`),
  KEY `idx_created` (`created_at`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
Size: ~50,000+ records
Growth: ~5,000 new users/month
Partitioning: None (vertical scaling)

3.2 Business Listings Table
sql
CREATE TABLE `business_listings` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `user_id` int(11) NOT NULL,
  `business_name` varchar(200) COLLATE utf8mb4_unicode_ci NOT NULL,
  `slug` varchar(200) COLLATE utf8mb4_unicode_ci NOT NULL,
  `description` text COLLATE utf8mb4_unicode_ci,
  `category` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `subcategory` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `address` text COLLATE utf8mb4_unicode_ci,
  `city` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `state` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `lga` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `landmark` varchar(200) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `latitude` decimal(10,8) DEFAULT NULL,
  `longitude` decimal(11,8) DEFAULT NULL,
  `phone` varchar(20) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `whatsapp` varchar(20) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `email` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `website` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `social_media` text COLLATE utf8mb4_unicode_ci COMMENT 'JSON: instagram, twitter, facebook',
  `logo` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `cover_image` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `images` text COLLATE utf8mb4_unicode_ci COMMENT 'JSON array of image paths',
  `business_hours` text COLLATE utf8mb4_unicode_ci COMMENT 'JSON: mon-sun hours',
  `services` text COLLATE utf8mb4_unicode_ci COMMENT 'JSON array of services',
  `price_range` varchar(20) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `established_year` year(4) DEFAULT NULL,
  `employees` varchar(50) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `is_verified` tinyint(1) DEFAULT 0,
  `verified_at` datetime DEFAULT NULL,
  `verified_by` int(11) DEFAULT NULL,
  `is_featured` tinyint(1) DEFAULT 0,
  `featured_until` date DEFAULT NULL,
  `verification_level` enum('basic','verified','premium') COLLATE utf8mb4_unicode_ci DEFAULT 'basic',
  `status` enum('pending','approved','rejected','suspended','closed') COLLATE utf8mb4_unicode_ci DEFAULT 'pending',
  `views` int(11) DEFAULT 0,
  `unique_views` int(11) DEFAULT 0,
  `reviews_count` int(11) DEFAULT 0,
  `rating_avg` decimal(3,2) DEFAULT 0.00,
  `claims_count` int(11) DEFAULT 0,
  `created_at` timestamp NOT NULL DEFAULT current_timestamp(),
  `updated_at` timestamp NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp(),
  `deleted_at` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `slug` (`slug`),
  KEY `user_id` (`user_id`),
  KEY `idx_category` (`category`),
  KEY `idx_location` (`state`,`city`),
  KEY `idx_status` (`status`),
  KEY `idx_verified` (`is_verified`),
  KEY `idx_featured` (`is_featured`),
  KEY `idx_rating` (`rating_avg`),
  FULLTEXT KEY `ft_search` (`business_name`, `description`, `category`, `city`),
  CONSTRAINT `business_listings_ibfk_1` FOREIGN KEY (`user_id`) REFERENCES `users` (`id`) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
Size: ~2,500+ records
Growth: ~200 new businesses/month
Partitioning: By state (36 partitions)

3.3 Marketplace Items Table
sql
CREATE TABLE `marketplace_items` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `user_id` int(11) NOT NULL,
  `business_id` int(11) DEFAULT NULL,
  `title` varchar(200) COLLATE utf8mb4_unicode_ci NOT NULL,
  `slug` varchar(200) COLLATE utf8mb4_unicode_ci NOT NULL,
  `description` text COLLATE utf8mb4_unicode_ci,
  `price` decimal(10,2) NOT NULL,
  `negotiable` tinyint(1) DEFAULT 0,
  `original_price` decimal(10,2) DEFAULT NULL,
  `category` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `subcategory` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `condition` enum('new','like_new','used','refurbished','for_parts') COLLATE utf8mb4_unicode_ci DEFAULT 'new',
  `brand` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `model` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `year` year(4) DEFAULT NULL,
  `color` varchar(50) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `specifications` text COLLATE utf8mb4_unicode_ci COMMENT 'JSON',
  `location` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `city` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `state` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `delivery_options` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL COMMENT 'pickup, shipping, both',
  `shipping_cost` decimal(10,2) DEFAULT NULL,
  `warranty` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `images` text COLLATE utf8mb4_unicode_ci COMMENT 'JSON array',
  `featured_image` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `video_url` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `status` enum('available','reserved','sold','deleted','expired') COLLATE utf8mb4_unicode_ci DEFAULT 'available',
  `views` int(11) DEFAULT 0,
  `unique_views` int(11) DEFAULT 0,
  `saves_count` int(11) DEFAULT 0,
  `shares_count` int(11) DEFAULT 0,
  `questions_count` int(11) DEFAULT 0,
  `is_featured` tinyint(1) DEFAULT 0,
  `featured_until` date DEFAULT NULL,
  `is_promoted` tinyint(1) DEFAULT 0,
  `promotion_level` enum('basic','boost','spotlight') DEFAULT NULL,
  `promotion_expires` datetime DEFAULT NULL,
  `created_at` timestamp NOT NULL DEFAULT current_timestamp(),
  `updated_at` timestamp NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp(),
  `deleted_at` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `slug` (`slug`),
  KEY `user_id` (`user_id`),
  KEY `business_id` (`business_id`),
  KEY `idx_category` (`category`),
  KEY `idx_location` (`state`,`city`),
  KEY `idx_price` (`price`),
  KEY `idx_status` (`status`),
  KEY `idx_created` (`created_at`),
  FULLTEXT KEY `ft_search` (`title`, `description`, `category`),
  CONSTRAINT `marketplace_items_ibfk_1` FOREIGN KEY (`user_id`) REFERENCES `users` (`id`) ON DELETE CASCADE,
  CONSTRAINT `marketplace_items_ibfk_2` FOREIGN KEY (`business_id`) REFERENCES `business_listings` (`id`) ON DELETE SET NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
Size: ~15,000+ records
Growth: ~1,000 new items/month
Partitioning: By date (quarterly)

3.4 Events Table
sql
CREATE TABLE `events` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `user_id` int(11) NOT NULL,
  `community_id` int(11) DEFAULT NULL,
  `business_id` int(11) DEFAULT NULL,
  `title` varchar(200) COLLATE utf8mb4_unicode_ci NOT NULL,
  `slug` varchar(200) COLLATE utf8mb4_unicode_ci NOT NULL,
  `description` text COLLATE utf8mb4_unicode_ci,
  `short_description` varchar(500) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `event_type` varchar(50) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `format` enum('physical','virtual','hybrid') COLLATE utf8mb4_unicode_ci DEFAULT 'physical',
  `start_date` datetime NOT NULL,
  `end_date` datetime DEFAULT NULL,
  `registration_deadline` datetime DEFAULT NULL,
  `location` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `venue` varchar(200) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `city` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `state` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `address` text COLLATE utf8mb4_unicode_ci,
  `latitude` decimal(10,8) DEFAULT NULL,
  `longitude` decimal(11,8) DEFAULT NULL,
  `meeting_link` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `cover_image` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `gallery` text COLLATE utf8mb4_unicode_ci COMMENT 'JSON array',
  `capacity` int(11) DEFAULT NULL,
  `attendees_count` int(11) DEFAULT 0,
  `waitlist_count` int(11) DEFAULT 0,
  `is_free` tinyint(1) DEFAULT 0,
  `has_tickets` tinyint(1) DEFAULT 0,
  `ticket_tiers` text COLLATE utf8mb4_unicode_ci COMMENT 'JSON array',
  `min_price` decimal(10,2) DEFAULT NULL,
  `max_price` decimal(10,2) DEFAULT NULL,
  `currency` varchar(10) COLLATE utf8mb4_unicode_ci DEFAULT 'NGN',
  `status` enum('draft','published','cancelled','completed','postponed') COLLATE utf8mb4_unicode_ci DEFAULT 'draft',
  `visibility` enum('public','private','community') COLLATE utf8mb4_unicode_ci DEFAULT 'public',
  `featured` tinyint(1) DEFAULT 0,
  `views` int(11) DEFAULT 0,
  `unique_views` int(11) DEFAULT 0,
  `shares_count` int(11) DEFAULT 0,
  `created_at` timestamp NOT NULL DEFAULT current_timestamp(),
  `updated_at` timestamp NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp(),
  `deleted_at` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `slug` (`slug`),
  KEY `user_id` (`user_id`),
  KEY `community_id` (`community_id`),
  KEY `business_id` (`business_id`),
  KEY `idx_dates` (`start_date`,`end_date`),
  KEY `idx_location` (`state`,`city`),
  KEY `idx_status` (`status`),
  KEY `idx_type` (`event_type`),
  FULLTEXT KEY `ft_search` (`title`, `description`, `location`),
  CONSTRAINT `events_ibfk_1` FOREIGN KEY (`user_id`) REFERENCES `users` (`id`) ON DELETE CASCADE,
  CONSTRAINT `events_ibfk_2` FOREIGN KEY (`community_id`) REFERENCES `communities` (`id`) ON DELETE SET NULL,
  CONSTRAINT `events_ibfk_3` FOREIGN KEY (`business_id`) REFERENCES `business_listings` (`id`) ON DELETE SET NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
Size: ~2,000+ records
Growth: ~150 new events/month
Partitioning: By month (archival strategy)

3.5 Jobs Table
sql
CREATE TABLE `jobs` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `user_id` int(11) NOT NULL,
  `business_id` int(11) DEFAULT NULL,
  `title` varchar(200) COLLATE utf8mb4_unicode_ci NOT NULL,
  `slug` varchar(200) COLLATE utf8mb4_unicode_ci NOT NULL,
  `description` text COLLATE utf8mb4_unicode_ci NOT NULL,
  `requirements` text COLLATE utf8mb4_unicode_ci,
  `responsibilities` text COLLATE utf8mb4_unicode_ci,
  `company_name` varchar(200) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `company_logo` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `category` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `subcategory` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `job_type` enum('full-time','part-time','contract','internship','remote','hybrid') COLLATE utf8mb4_unicode_ci DEFAULT 'full-time',
  `experience_level` enum('entry','mid','senior','lead','executive') COLLATE utf8mb4_unicode_ci DEFAULT 'entry',
  `location` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `city` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `state` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `remote_option` tinyint(1) DEFAULT 0,
  `salary_min` decimal(10,2) DEFAULT NULL,
  `salary_max` decimal(10,2) DEFAULT NULL,
  `salary_period` enum('hour','day','week','month','year') COLLATE utf8mb4_unicode_ci DEFAULT 'month',
  `is_negotiable` tinyint(1) DEFAULT 0,
  `currency` varchar(10) COLLATE utf8mb4_unicode_ci DEFAULT 'NGN',
  `benefits` text COLLATE utf8mb4_unicode_ci COMMENT 'JSON array',
  `application_method` enum('email','url','internal') COLLATE utf8mb4_unicode_ci DEFAULT 'internal',
  `application_email` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `application_url` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `deadline` date DEFAULT NULL,
  `is_featured` tinyint(1) DEFAULT 0,
  `featured_until` date DEFAULT NULL,
  `status` enum('active','filled','closed','expired','draft') COLLATE utf8mb4_unicode_ci DEFAULT 'active',
  `views` int(11) DEFAULT 0,
  `applications_count` int(11) DEFAULT 0,
  `saves_count` int(11) DEFAULT 0,
  `shares_count` int(11) DEFAULT 0,
  `created_at` timestamp NOT NULL DEFAULT current_timestamp(),
  `updated_at` timestamp NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp(),
  `deleted_at` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `slug` (`slug`),
  KEY `user_id` (`user_id`),
  KEY `business_id` (`business_id`),
  KEY `idx_category` (`category`),
  KEY `idx_location` (`state`,`city`),
  KEY `idx_type` (`job_type`),
  KEY `idx_status` (`status`),
  KEY `idx_deadline` (`deadline`),
  FULLTEXT KEY `ft_search` (`title`, `description`, `company_name`),
  CONSTRAINT `jobs_ibfk_1` FOREIGN KEY (`user_id`) REFERENCES `users` (`id`) ON DELETE CASCADE,
  CONSTRAINT `jobs_ibfk_2` FOREIGN KEY (`business_id`) REFERENCES `business_listings` (`id`) ON DELETE SET NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
Size: ~1,200+ records
Growth: ~100 new jobs/month
Partitioning: By status (active/expired)

3.6 Communities Table
sql
CREATE TABLE `communities` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(100) COLLATE utf8mb4_unicode_ci NOT NULL,
  `slug` varchar(100) COLLATE utf8mb4_unicode_ci NOT NULL,
  `description` text COLLATE utf8mb4_unicode_ci,
  `rules` text COLLATE utf8mb4_unicode_ci,
  `category` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `tags` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL COMMENT 'Comma separated',
  `avatar` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT 'default-community.png',
  `banner` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT 'default-banner.png',
  `cover_image` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `created_by` int(11) NOT NULL,
  `is_private` tinyint(1) DEFAULT 0,
  `is_hidden` tinyint(1) DEFAULT 0,
  `requires_approval` tinyint(1) DEFAULT 1,
  `members_count` int(11) DEFAULT 0,
  `posts_count` int(11) DEFAULT 0,
  `events_count` int(11) DEFAULT 0,
  `pending_requests` int(11) DEFAULT 0,
  `location` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `city` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `state` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `website` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `social_links` text COLLATE utf8mb4_unicode_ci COMMENT 'JSON',
  `status` enum('active','archived','banned') COLLATE utf8mb4_unicode_ci DEFAULT 'active',
  `created_at` timestamp NOT NULL DEFAULT current_timestamp(),
  `updated_at` timestamp NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp(),
  `deleted_at` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `slug` (`slug`),
  KEY `created_by` (`created_by`),
  KEY `idx_category` (`category`),
  KEY `idx_privacy` (`is_private`),
  KEY `idx_members` (`members_count`),
  FULLTEXT KEY `ft_search` (`name`, `description`, `category`),
  CONSTRAINT `communities_ibfk_1` FOREIGN KEY (`created_by`) REFERENCES `users` (`id`) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
Size: ~500+ records
Growth: ~30 new communities/month
Partitioning: None

3.7 Transactions & Payments Table
sql
CREATE TABLE `transactions` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `reference` varchar(100) COLLATE utf8mb4_unicode_ci NOT NULL,
  `user_id` int(11) NOT NULL,
  `order_id` int(11) DEFAULT NULL,
  `event_booking_id` int(11) DEFAULT NULL,
  `wallet_id` int(11) DEFAULT NULL,
  `amount` decimal(10,2) NOT NULL,
  `fee` decimal(10,2) DEFAULT 0.00,
  `total` decimal(10,2) NOT NULL,
  `currency` varchar(10) COLLATE utf8mb4_unicode_ci DEFAULT 'NGN',
  `payment_method` varchar(50) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `channel` varchar(50) COLLATE utf8mb4_unicode_ci DEFAULT NULL COMMENT 'card, bank, ussd, transfer, wallet',
  `provider` varchar(50) COLLATE utf8mb4_unicode_ci DEFAULT NULL COMMENT 'paystack, flutterwave, wallet',
  `provider_reference` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `status` enum('pending','success','failed','abandoned','refunded') COLLATE utf8mb4_unicode_ci DEFAULT 'pending',
  `metadata` text COLLATE utf8mb4_unicode_ci COMMENT 'JSON: customer, products, etc',
  `paid_at` datetime DEFAULT NULL,
  `refunded_at` datetime DEFAULT NULL,
  `refund_reason` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `ip_address` varchar(45) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `user_agent` text COLLATE utf8mb4_unicode_ci,
  `created_at` timestamp NOT NULL DEFAULT current_timestamp(),
  `updated_at` timestamp NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp(),
  PRIMARY KEY (`id`),
  UNIQUE KEY `reference` (`reference`),
  UNIQUE KEY `provider_reference` (`provider_reference`),
  KEY `user_id` (`user_id`),
  KEY `order_id` (`order_id`),
  KEY `event_booking_id` (`event_booking_id`),
  KEY `idx_status` (`status`),
  KEY `idx_dates` (`paid_at`),
  KEY `idx_created` (`created_at`),
  CONSTRAINT `transactions_ibfk_1` FOREIGN KEY (`user_id`) REFERENCES `users` (`id`) ON DELETE CASCADE,
  CONSTRAINT `transactions_ibfk_2` FOREIGN KEY (`order_id`) REFERENCES `orders` (`id`) ON DELETE SET NULL,
  CONSTRAINT `transactions_ibfk_3` FOREIGN KEY (`event_booking_id`) REFERENCES `event_bookings` (`id`) ON DELETE SET NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
Size: ~150,000+ records
Growth: ~10,000 new transactions/month
Partitioning: By month (monthly partitions)

3.8 Messages Table
sql
CREATE TABLE `messages` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `conversation_id` int(11) NOT NULL,
  `sender_id` int(11) NOT NULL,
  `receiver_id` int(11) NOT NULL,
  `message` text COLLATE utf8mb4_unicode_ci NOT NULL,
  `type` enum('text','image','file','voice','location','product') COLLATE utf8mb4_unicode_ci DEFAULT 'text',
  `attachment` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `attachment_name` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `attachment_size` int(11) DEFAULT NULL,
  `metadata` text COLLATE utf8mb4_unicode_ci COMMENT 'JSON',
  `is_read` tinyint(1) DEFAULT 0,
  `read_at` datetime DEFAULT NULL,
  `is_delivered` tinyint(1) DEFAULT 0,
  `delivered_at` datetime DEFAULT NULL,
  `is_edited` tinyint(1) DEFAULT 0,
  `edited_at` datetime DEFAULT NULL,
  `is_deleted` tinyint(1) DEFAULT 0,
  `deleted_at` datetime DEFAULT NULL,
  `created_at` timestamp NOT NULL DEFAULT current_timestamp(),
  PRIMARY KEY (`id`),
  KEY `conversation_id` (`conversation_id`),
  KEY `sender_id` (`sender_id`),
  KEY `receiver_id` (`receiver_id`),
  KEY `idx_read` (`is_read`),
  KEY `idx_created` (`created_at`),
  CONSTRAINT `messages_ibfk_1` FOREIGN KEY (`conversation_id`) REFERENCES `conversations` (`id`) ON DELETE CASCADE,
  CONSTRAINT `messages_ibfk_2` FOREIGN KEY (`sender_id`) REFERENCES `users` (`id`) ON DELETE CASCADE,
  CONSTRAINT `messages_ibfk_3` FOREIGN KEY (`receiver_id`) REFERENCES `users` (`id`) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
Size: ~500,000+ records
Growth: ~50,000 new messages/month
Partitioning: By conversation_id (hash sharding)

3.9 Notifications Table
sql
CREATE TABLE `notifications` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `user_id` int(11) NOT NULL,
  `type` varchar(50) COLLATE utf8mb4_unicode_ci NOT NULL COMMENT 'like, comment, follow, message, order, event, job, system',
  `notifiable_type` varchar(50) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `notifiable_id` int(11) DEFAULT NULL,
  `actor_id` int(11) DEFAULT NULL,
  `message` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `title` varchar(100) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `image` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `url` varchar(255) COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `data` text COLLATE utf8mb4_unicode_ci COMMENT 'JSON payload',
  `channels` varchar(50) COLLATE utf8mb4_unicode_ci DEFAULT 'in-app' COMMENT 'in-app, email, sms, push',
  `is_read` tinyint(1) DEFAULT 0,
  `read_at` datetime DEFAULT NULL,
  `is_clicked` tinyint(1) DEFAULT 0,
  `clicked_at` datetime DEFAULT NULL,
  `is_archived` tinyint(1) DEFAULT 0,
  `archived_at` datetime DEFAULT NULL,
  `priority` enum('low','normal','high','urgent') COLLATE utf8mb4_unicode_ci DEFAULT 'normal',
  `created_at` timestamp NOT NULL DEFAULT current_timestamp(),
  PRIMARY KEY (`id`),
  KEY `user_id` (`user_id`),
  KEY `idx_read` (`is_read`),
  KEY `idx_type` (`type`),
  KEY `idx_created` (`created_at`),
  CONSTRAINT `notifications_ibfk_1` FOREIGN KEY (`user_id`) REFERENCES `users` (`id`) ON DELETE CASCADE,
  CONSTRAINT `notifications_ibfk_2` FOREIGN KEY (`actor_id`) REFERENCES `users` (`id`) ON DELETE SET NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
Size: ~2,000,000+ records
Growth: ~200,000 new notifications/month
Partitioning: By month (automatic archival after 90 days)

4. Supporting Tables
4.1 Nigerian Location Data
sql
CREATE TABLE `nigeria_states` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(50) NOT NULL,
  `capital` varchar(50) DEFAULT NULL,
  `slogan` varchar(100) DEFAULT NULL,
  `land_area` int(11) DEFAULT NULL,
  `population` int(11) DEFAULT NULL,
  `created_at` timestamp NOT NULL DEFAULT current_timestamp(),
  PRIMARY KEY (`id`),
  UNIQUE KEY `name` (`name`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

CREATE TABLE `nigeria_lgas` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `state_id` int(11) NOT NULL,
  `name` varchar(100) NOT NULL,
  `population` int(11) DEFAULT NULL,
  `created_at` timestamp NOT NULL DEFAULT current_timestamp(),
  PRIMARY KEY (`id`),
  KEY `state_id` (`state_id`),
  CONSTRAINT `lgas_ibfk_1` FOREIGN KEY (`state_id`) REFERENCES `nigeria_states` (`id`) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

CREATE TABLE `nigeria_cities` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `state_id` int(11) NOT NULL,
  `lga_id` int(11) DEFAULT NULL,
  `name` varchar(100) NOT NULL,
  `latitude` decimal(10,8) DEFAULT NULL,
  `longitude` decimal(11,8) DEFAULT NULL,
  `created_at` timestamp NOT NULL DEFAULT current_timestamp(),
  PRIMARY KEY (`id`),
  KEY `state_id` (`state_id`),
  KEY `lga_id` (`lga_id`),
  CONSTRAINT `cities_ibfk_1` FOREIGN KEY (`state_id`) REFERENCES `nigeria_states` (`id`) ON DELETE CASCADE,
  CONSTRAINT `cities_ibfk_2` FOREIGN KEY (`lga_id`) REFERENCES `nigeria_lgas` (`id`) ON DELETE SET NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
Data: 36 states, 774 LGAs, 500+ major cities
Pre-populated: Static data loaded at installation

4.2 Categories & Taxonomy
sql
CREATE TABLE `categories` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `parent_id` int(11) DEFAULT NULL,
  `type` enum('marketplace','business','event','job','community') NOT NULL,
  `name` varchar(100) NOT NULL,
  `slug` varchar(100) NOT NULL,
  `icon` varchar(50) DEFAULT NULL,
  `description` text,
  `image` varchar(255) DEFAULT NULL,
  `order` int(11) DEFAULT 0,
  `is_active` tinyint(1) DEFAULT 1,
  `created_at` timestamp NOT NULL DEFAULT current_timestamp(),
  PRIMARY KEY (`id`),
  UNIQUE KEY `slug` (`slug`),
  KEY `parent_id` (`parent_id`),
  KEY `type` (`type`),
  CONSTRAINT `categories_ibfk_1` FOREIGN KEY (`parent_id`) REFERENCES `categories` (`id`) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
4.3 Hashtags & Trending
sql
CREATE TABLE `hashtags` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `tag` varchar(100) NOT NULL,
  `slug` varchar(100) NOT NULL,
  `posts_count` int(11) DEFAULT 0,
  `followers_count` int(11) DEFAULT 0,
  `last_trending` datetime DEFAULT NULL,
  `created_at` timestamp NOT NULL DEFAULT current_timestamp(),
  PRIMARY KEY (`id`),
  UNIQUE KEY `tag` (`tag`),
  UNIQUE KEY `slug` (`slug`),
  KEY `idx_count` (`posts_count`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

CREATE TABLE `hashtag_followers` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `user_id` int(11) NOT NULL,
  `hashtag_id` int(11) NOT NULL,
  `created_at` timestamp NOT NULL DEFAULT current_timestamp(),
  PRIMARY KEY (`id`),
  UNIQUE KEY `user_hashtag` (`user_id`,`hashtag_id`),
  KEY `hashtag_id` (`hashtag_id`),
  CONSTRAINT `hashtag_followers_ibfk_1` FOREIGN KEY (`user_id`) REFERENCES `users` (`id`) ON DELETE CASCADE,
  CONSTRAINT `hashtag_followers_ibfk_2` FOREIGN KEY (`hashtag_id`) REFERENCES `hashtags` (`id`) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
5. Indexing Strategy
5.1 Primary Indexes
All id columns: PRIMARY KEY

All slug columns: UNIQUE INDEX

All foreign keys: INDEX

5.2 Composite Indexes (Performance Critical)
sql
-- Business listings: location + category + status
CREATE INDEX idx_business_discovery ON business_listings(state, city, category, status, rating_avg);

-- Marketplace items: location + category + price + status
CREATE INDEX idx_marketplace_discovery ON marketplace_items(state, city, category, price, status, created_at);

-- Events: date + location + status
CREATE INDEX idx_events_discovery ON events(start_date, state, city, status, is_free);

-- Jobs: location + type + category + status
CREATE INDEX idx_jobs_discovery ON jobs(state, city, job_type, category, status, deadline);

-- Orders: buyer + seller + status
CREATE INDEX idx_orders_composite ON orders(buyer_id, seller_id, status, created_at);
5.3 Full-Text Search Indexes
sql
-- Business search
ALTER TABLE business_listings ADD FULLTEXT INDEX ft_business_search (business_name, description, category, city);

-- Marketplace search
ALTER TABLE marketplace_items ADD FULLTEXT INDEX ft_marketplace_search (title, description, category, brand);

-- Events search
ALTER TABLE events ADD FULLTEXT INDEX ft_events_search (title, description, location);

-- Jobs search
ALTER TABLE jobs ADD FULLTEXT INDEX ft_jobs_search (title, description, company_name, requirements);

-- Communities search
ALTER TABLE communities ADD FULLTEXT INDEX ft_communities_search (name, description, category);
6. Partitioning Strategy
6.1 By State (Business Listings)
sql
-- 36 states + FCT = 37 partitions
ALTER TABLE business_listings
PARTITION BY LIST COLUMNS(state) (
    PARTITION p_abia VALUES IN ('Abia'),
    PARTITION p_abuja VALUES IN ('Abuja', 'FCT'),
    PARTITION p_adamawa VALUES IN ('Adamawa'),
    -- ... 34 more states
    PARTITION p_unknown VALUES IN (NULL, 'Unknown', 'Other')
);
6.2 By Month (Transactions)
sql
-- Monthly partitions, auto-created by event
ALTER TABLE transactions
PARTITION BY RANGE (UNIX_TIMESTAMP(created_at)) (
    PARTITION p202601 VALUES LESS THAN (UNIX_TIMESTAMP('2026-02-01')),
    PARTITION p202602 VALUES LESS THAN (UNIX_TIMESTAMP('2026-03-01')),
    PARTITION p202603 VALUES LESS THAN (UNIX_TIMESTAMP('2026-04-01')),
    PARTITION p202604 VALUES LESS THAN (UNIX_TIMESTAMP('2026-05-01')),
    PARTITION p202605 VALUES LESS THAN (UNIX_TIMESTAMP('2026-06-01')),
    PARTITION p202606 VALUES LESS THAN (UNIX_TIMESTAMP('2026-07-01')),
    PARTITION p_future VALUES LESS THAN MAXVALUE
);
7. Query Optimization Examples
7.1 Before Optimization (Slow)
sql
-- Slow: 3.2 seconds, 150ms after optimization
SELECT * FROM marketplace_items 
WHERE category = 'Electronics' 
  AND city = 'Lagos' 
  AND price BETWEEN 10000 AND 50000 
  AND status = 'available'
ORDER BY created_at DESC 
LIMIT 20;
Problem: No composite index, full table scan

7.2 After Optimization (Fast)
sql
-- Add composite index
CREATE INDEX idx_marketplace_discovery ON marketplace_items
(category, city, price, status, created_at);

-- Optimized query: 120ms
SELECT id, title, price, images, city, created_at 
FROM marketplace_items 
WHERE category = 'Electronics' 
  AND city = 'Lagos' 
  AND price BETWEEN 10000 AND 50000 
  AND status = 'available'
ORDER BY created_at DESC 
LIMIT 20;
Improvement: 96% faster (3.2s → 0.12s)

8. Data Archival Strategy
8.1 Archival Policy
Table	Retention	Archival Method
Notifications	90 days	Move to archive table, delete from main
Messages	1 year	Keep in main, partition by month
Logs	30 days	Delete permanently
Transactions	7 years	Keep in main, partition by year
Deleted items	30 days	Permanent delete after grace period
8.2 Archival Job
sql
-- Daily cron job
DELETE FROM notifications 
WHERE created_at < DATE_SUB(NOW(), INTERVAL 90 DAY) 
AND is_read = 1;

-- Move to archive table
INSERT INTO notifications_archive SELECT * FROM notifications 
WHERE created_at < DATE_SUB(NOW(), INTERVAL 90 DAY) 
AND is_read = 1;
9. Database Performance Metrics
Metric	Current Value	Target	Status
Avg Query Time	48ms	<50ms	✅
Slow Queries (>1s)	0.2%	<0.5%	✅
Cache Hit Ratio	87%	>85%	✅
Index Usage	94%	>90%	✅
Connection Count	45 avg	<100	✅
CPU Usage	35%	<70%	✅
Disk IOPS	800	<3000	✅
Replication Lag	<1s	<5s	✅
10. Backup & Recovery
10.1 Backup Schedule
bash
# Daily full backup at 2am
0 2 * * * mysqldump -u backup -p naijabased > /backups/daily/naijabased_$(date +\%Y\%m\%d).sql

# Weekly compressed backup (Sunday)
0 3 * * 0 mysqldump -u backup -p naijabased | gzip > /backups/weekly/naijabased_$(date +\%Y\%m\%d).sql.gz

# Binary logs continuous (RDS automated)
# Point-in-time recovery available
10.2 Backup Retention
Hourly: 24 hours (RDS automated)

Daily: 30 days

Weekly: 12 weeks

Monthly: 12 months

Yearly: 7 years (compliance)

11. Database Migration Strategy
php
// Migration example
class AddBusinessVerificationColumns {
    public function up() {
        Schema::table('business_listings', function($table) {
            $table->enum('verification_level', ['basic', 'verified', 'premium'])
                  ->default('basic')
                  ->after('is_verified');
            $table->datetime('verified_at')->nullable()->after('verification_level');
            $table->integer('verified_by')->nullable()->after('verified_at');
            $table->index('verification_level');
        });
    }
    
    public function down() {
        Schema::table('business_listings', function($table) {
            $table->dropColumn(['verification_level', 'verified_at', 'verified_by']);
        });
    }
}
12. Database Design Lessons Learned
✅ What Worked Well
Prepared statements from day one - No SQL injection vulnerabilities

Consistent naming convention - snake_case, plural table names

Soft deletes - Recoverable data, audit trail

JSON columns for flexible data - Services, social links, metadata

Composite indexes on frequent queries - 90% query time reduction

🔄 What I'd Do Differently
UUID primary keys - Better for sharding, but int is simpler

Partition earlier - Waited too long on large tables

More aggressive archival - Notification table grew too fast

Database migrations - Should have automated from start

💡 Key Takeaways
Index judiciously - Too many indexes hurt write performance

Monitor slow query log - Catch problems before users do

Test with production data volume - Dev data never shows real bottlenecks

Plan for growth - Design for 10x from day one

This document reflects the current database architecture as of February 2026. Schema evolves continuously based on feature requirements and performance optimization.

