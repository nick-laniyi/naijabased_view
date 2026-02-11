NaijaBased - Database ER Diagram

🧑‍🤝‍🧑 Core User Tables
erDiagram
    USERS ||--o{ POSTS : creates
    USERS ||--o{ COMMENTS : writes
    USERS ||--o{ LIKES : gives
    USERS ||--o{ FOLLOWS : follows
    USERS ||--o{ NOTIFICATIONS : receives
    USERS ||--o{ USER_BADGES : earns
    USERS ||--o{ POINTS_TRANSACTIONS : accumulates
    USERS ||--o{ USER_PREFERENCES : configures
    
    USERS {
        int id PK
        string username UK
        string email UK
        string password
        string full_name
        string avatar
        string phone UK
        text bio
        string location
        string state
        string city
        boolean is_verified
        boolean is_admin
        boolean is_business
        int points
        string level
        decimal rating_avg
        int review_count
        datetime last_login
        datetime created_at
        datetime deleted_at
    }
    
    POSTS {
        int id PK
        int user_id FK
        text content
        json media
        int likes_count
        int comments_count
        int shares_count
        enum privacy
        json hashtags
        string location
        datetime created_at
        datetime deleted_at
    }
    
    COMMENTS {
        int id PK
        int post_id FK
        int user_id FK
        int parent_id FK
        text content
        json media
        int likes_count
        datetime created_at
        datetime deleted_at
    }
    
    LIKES {
        int id PK
        int user_id FK
        int post_id FK
        int comment_id FK
        datetime created_at
    }
    
    FOLLOWS {
        int id PK
        int follower_id FK
        int following_id FK
        datetime created_at
    }

🏢 Business & Marketplace Tables
erDiagram
    USERS ||--o{ BUSINESS_LISTINGS : owns
    BUSINESS_LISTINGS ||--o{ MARKETPLACE_ITEMS : sells
    BUSINESS_LISTINGS ||--o{ BUSINESS_REVIEWS : receives
    BUSINESS_LISTINGS ||--o{ BUSINESS_CLAIMS : requests
    BUSINESS_LISTINGS ||--o{ BUSINESS_HOURS : has
    
    BUSINESS_LISTINGS {
        int id PK
        int user_id FK
        string business_name
        string slug UK
        text description
        string category
        string subcategory
        text address
        string city
        string state
        string lga
        string landmark
        decimal latitude
        decimal longitude
        string phone
        string whatsapp
        string email
        string website
        json social_media
        string logo
        string cover_image
        json images
        json business_hours
        json services
        boolean is_verified
        enum verification_level
        boolean is_featured
        enum status
        int views
        decimal rating_avg
        int reviews_count
        datetime created_at
    }
    
    MARKETPLACE_ITEMS {
        int id PK
        int user_id FK
        int business_id FK
        string title
        string slug UK
        text description
        decimal price
        boolean negotiable
        decimal original_price
        string category
        string subcategory
        enum condition
        string brand
        string model
        json specifications
        string city
        string state
        json images
        string featured_image
        enum status
        int views
        int saves_count
        boolean is_featured
        datetime created_at
        datetime deleted_at
    }
    
    BUSINESS_REVIEWS {
        int id PK
        int business_id FK
        int user_id FK
        int order_id FK
        tinyint rating
        text comment
        json images
        int helpful_count
        datetime created_at
    }
    
    BUSINESS_CLAIMS {
        int id PK
        int business_id FK
        int user_id FK
        string verification_code
        enum status
        datetime expires_at
        datetime approved_at
        int approved_by FK
        datetime created_at
    }


📅 Events & Ticketing Tables
erDiagram
    USERS ||--o{ EVENTS : creates
    EVENTS ||--o{ EVENT_BOOKINGS : has
    EVENTS ||--o{ TICKET_TIERS : offers
    EVENT_BOOKINGS ||--o{ TICKETS : generates
    USERS ||--o{ EVENT_BOOKINGS : makes
    
    EVENTS {
        int id PK
        int user_id FK
        int community_id FK
        int business_id FK
        string title
        string slug UK
        text description
        string event_type
        enum format
        datetime start_date
        datetime end_date
        datetime registration_deadline
        string location
        string venue
        string city
        string state
        text address
        decimal latitude
        decimal longitude
        string meeting_link
        string cover_image
        int capacity
        int attendees_count
        boolean is_free
        boolean has_tickets
        json ticket_tiers
        decimal min_price
        decimal max_price
        enum status
        enum visibility
        int views
        datetime created_at
    }
    
    TICKET_TIERS {
        int id PK
        int event_id FK
        string name
        decimal price
        int quantity
        int sold
        int max_per_order
        json benefits
        datetime sales_start
        datetime sales_end
    }
    
    EVENT_BOOKINGS {
        int id PK
        int event_id FK
        int user_id FK
        int tier_id FK
        int quantity
        decimal amount
        string reference UK
        enum status
        string qr_code
        json attendees
        datetime booked_at
        datetime paid_at
        datetime checked_in_at
    }
    
    TICKETS {
        int id PK
        int booking_id FK
        string ticket_code UK
        string attendee_name
        string attendee_email
        string attendee_phone
        boolean checked_in
        datetime checked_in_at
    }

💼 Jobs Tables
erDiagram
    USERS ||--o{ JOBS : posts
    BUSINESS_LISTINGS ||--o{ JOBS : offers
    JOBS ||--o{ JOB_APPLICATIONS : receives
    USERS ||--o{ JOB_APPLICATIONS : submits
    
    JOBS {
        int id PK
        int user_id FK
        int business_id FK
        string title
        string slug UK
        text description
        text requirements
        text responsibilities
        string company_name
        string company_logo
        string category
        string subcategory
        enum job_type
        enum experience_level
        string city
        string state
        boolean remote_option
        decimal salary_min
        decimal salary_max
        enum salary_period
        boolean is_negotiable
        json benefits
        enum application_method
        string application_email
        string application_url
        date deadline
        boolean is_featured
        enum status
        int views
        int applications_count
        datetime created_at
    }
    
    JOB_APPLICATIONS {
        int id PK
        int job_id FK
        int user_id FK
        string full_name
        string email
        string phone
        text cover_letter
        string resume_path
        string portfolio_url
        string linkedin_url
        json answers
        enum status
        text employer_notes
        datetime applied_at
        datetime reviewed_at
    }

👥 Communities Tables
erDiagram
    USERS ||--o{ COMMUNITIES : creates
    COMMUNITIES ||--o{ COMMUNITY_MEMBERS : has
    COMMUNITIES ||--o{ COMMUNITY_POSTS : contains
    USERS ||--o{ COMMUNITY_MEMBERS : joins
    USERS ||--o{ COMMUNITY_POSTS : writes
    
    COMMUNITIES {
        int id PK
        string name
        string slug UK
        text description
        text rules
        string category
        string tags
        string avatar
        string banner
        int created_by FK
        boolean is_private
        boolean is_hidden
        boolean requires_approval
        int members_count
        int posts_count
        int events_count
        string city
        string state
        enum status
        datetime created_at
    }
    
    COMMUNITY_MEMBERS {
        int id PK
        int community_id FK
        int user_id FK
        enum role
        enum status
        datetime joined_at
        datetime approved_at
        int approved_by FK
    }
    
    COMMUNITY_POSTS {
        int id PK
        int community_id FK
        int user_id FK
        text content
        json media
        int likes_count
        int comments_count
        boolean is_pinned
        boolean is_announcement
        datetime created_at
    }



💰 Transactions & Payments Tables
erDiagram
    USERS ||--o{ ORDERS : places
    USERS ||--o{ ORDERS : receives
    MARKETPLACE_ITEMS ||--o{ ORDER_ITEMS : includes
    ORDERS ||--o{ ORDER_ITEMS : contains
    ORDERS ||--o{ TRANSACTIONS : has
    ORDERS ||--o{ ESCROW : protects
    
    ORDERS {
        int id PK
        string order_number UK
        int buyer_id FK
        int seller_id FK
        decimal subtotal
        decimal shipping_cost
        decimal discount
        decimal total
        string currency
        enum status
        text shipping_address
        string tracking_number
        string courier
        datetime placed_at
        datetime paid_at
        datetime shipped_at
        datetime delivered_at
        datetime cancelled_at
    }
    
    ORDER_ITEMS {
        int id PK
        int order_id FK
        int item_id FK
        string item_title
        decimal price
        int quantity
        json item_snapshot
    }
    
    TRANSACTIONS {
        int id PK
        string reference UK
        int user_id FK
        int order_id FK
        int event_booking_id FK
        decimal amount
        decimal fee
        decimal total
        string currency
        string payment_method
        string channel
        string provider
        string provider_reference
        enum status
        json metadata
        datetime paid_at
        datetime created_at
    }
    
    ESCROW {
        int id PK
        int order_id FK
        decimal amount
        int buyer_id FK
        int seller_id FK
        enum status
        string dispute_reason
        datetime held_at
        datetime released_at
        datetime refunded_at
    }


💬 Messaging Tables
erDiagram
    USERS ||--o{ CONVERSATIONS : participates
    CONVERSATIONS ||--o{ MESSAGES : contains
    USERS ||--o{ MESSAGES : sends
    
    CONVERSATIONS {
        int id PK
        string type
        int context_id
        string context_type
        int participant_one FK
        int participant_two FK
        datetime last_message_at
        datetime created_at
    }
    
    MESSAGES {
        int id PK
        int conversation_id FK
        int sender_id FK
        int receiver_id FK
        text content
        enum type
        string attachment
        string attachment_name
        int attachment_size
        json metadata
        boolean is_read
        datetime read_at
        boolean is_delivered
        datetime delivered_at
        boolean is_edited
        datetime edited_at
        boolean is_deleted
        datetime deleted_at
        datetime created_at
    }

🔔 Notifications Tables
erDiagram
    USERS ||--o{ NOTIFICATIONS : receives
    USERS ||--o{ NOTIFICATION_PREFERENCES : configures
    
    NOTIFICATIONS {
        int id PK
        int user_id FK
        string type
        string notifiable_type
        int notifiable_id
        int actor_id FK
        string title
        string message
        string image
        string url
        json data
        string channels
        boolean is_read
        datetime read_at
        boolean is_clicked
        datetime clicked_at
        enum priority
        datetime created_at
    }
    
    NOTIFICATION_PREFERENCES {
        int id PK
        int user_id FK
        json email_preferences
        json sms_preferences
        json push_preferences
        json in_app_preferences
        datetime updated_at
    }


🏷️ Hashtags & Taxonomy Tables
erDiagram
    HASHTAGS ||--o{ POST_HASHTAGS : used_in
    POSTS ||--o{ POST_HASHTAGS : has
    USERS ||--o{ HASHTAG_FOLLOWERS : follows
    HASHTAGS ||--o{ HASHTAG_FOLLOWERS : followed_by
    
    HASHTAGS {
        int id PK
        string tag UK
        string slug UK
        int posts_count
        int followers_count
        datetime last_trending
        datetime created_at
    }
    
    POST_HASHTAGS {
        int id PK
        int post_id FK
        int hashtag_id FK
        datetime created_at
    }
    
    HASHTAG_FOLLOWERS {
        int id PK
        int user_id FK
        int hashtag_id FK
        datetime created_at
    }
    
    CATEGORIES {
        int id PK
        int parent_id FK
        enum type
        string name
        string slug UK
        string icon
        text description
        int order
        boolean is_active
    }


🇳🇬 Nigerian Location Tables
erDiagram
    NIGERIA_STATES ||--o{ NIGERIA_LGAS : contains
    NIGERIA_STATES ||--o{ NIGERIA_CITIES : contains
    NIGERIA_LGAS ||--o{ NIGERIA_CITIES : contains
    NIGERIA_CITIES ||--o{ NIGERIA_LANDMARKS : has
    
    NIGERIA_STATES {
        int id PK
        string name UK
        string capital
        string slogan
        int land_area
        int population
    }
    
    NIGERIA_LGAS {
        int id PK
        int state_id FK
        string name
        int population
    }
    
    NIGERIA_CITIES {
        int id PK
        int state_id FK
        int lga_id FK
        string name
        decimal latitude
        decimal longitude
        boolean is_popular
    }
    
    NIGERIA_LANDMARKS {
        int id PK
        int city_id FK
        string name
        string category
        decimal latitude
        decimal longitude
        boolean verified
        int verification_score
    }

📊 Table Relationships Summary
graph TD
    subgraph "👤 Core"
        USERS[👤 users]
        POSTS[📝 posts]
        COMMENTS[💬 comments]
        LIKES[❤️ likes]
        FOLLOWS[🤝 follows]
    end
    
    subgraph "🏢 Business"
        BUSINESS[🏢 business_listings]
        MARKETPLACE[🛍️ marketplace_items]
        REVIEWS[⭐ business_reviews]
        ORDERS[📦 orders]
    end
    
    subgraph "📅 Events"
        EVENTS[🎪 events]
        TICKETS[🎫 tickets]
        BOOKINGS[📅 bookings]
    end
    
    subgraph "💼 Jobs"
        JOBS[💼 jobs]
        APPLICATIONS[📄 applications]
    end
    
    subgraph "👥 Community"
        COMMUNITIES[👥 communities]
        MEMBERS[🤝 members]
        COMM_POSTS[📢 community_posts]
    end
    
    subgraph "💬 Messaging"
        CONVOS[💬 conversations]
        MESSAGES[✉️ messages]
    end
    
    subgraph "💰 Payments"
        TRANSACTIONS[💰 transactions]
        ESCROW[🔒 escrow]
    end
    
    subgraph "🔔 System"
        NOTIFICATIONS[🔔 notifications]
        HASHTAGS[#️⃣ hashtags]
        CACHE[⚡ cache]
        LOGS[📋 logs]
    end
    
    USERS --> POSTS
    USERS --> BUSINESS
    USERS --> MARKETPLACE
    USERS --> EVENTS
    USERS --> JOBS
    USERS --> COMMUNITIES
    USERS --> CONVOS
    USERS --> NOTIFICATIONS
    
    BUSINESS --> MARKETPLACE
    BUSINESS --> ORDERS
    BUSINESS --> REVIEWS
    
    MARKETPLACE --> ORDERS
    ORDERS --> TRANSACTIONS
    ORDERS --> ESCROW
    
    EVENTS --> BOOKINGS
    BOOKINGS --> TICKETS
    BOOKINGS --> TRANSACTIONS
    
    JOBS --> APPLICATIONS
    
    COMMUNITIES --> MEMBERS
    COMMUNITIES --> COMM_POSTS
    
    CONVOS --> MESSAGES
    
    POSTS --> HASHTAGS
    USERS --> HASHTAGS
    
    style USERS fill:#e1f5fe,stroke:#01579b,stroke-width:2px
    style BUSINESS fill:#fff3e0,stroke:#e65100,stroke-width:2px
    style MARKETPLACE fill:#fff3e0,stroke:#e65100,stroke-width:2px
    style EVENTS fill:#e8f5e9,stroke:#1b5e20,stroke-width:2px
    style JOBS fill:#f3e5f5,stroke:#4a148c,stroke-width:2px
    style COMMUNITIES fill:#ffebee,stroke:#b71c1c,stroke-width:2px
    style CONVOS fill:#fff8e1,stroke:#ff6f00,stroke-width:2px
    style TRANSACTIONS fill:#e0f7fa,stroke:#006064,stroke-width:2px


📈 Database Statistics


Table	Records	Growth/Month	Size
users	50,000+	+5,000	250MB
business_listings	2,500+	+200	150MB
marketplace_items	15,000+	+1,000	500MB
events	2,000+	+150	100MB
jobs	1,200+	+100	80MB
communities	500+	+30	50MB
orders	25,000+	+2,000	300MB
transactions	150,000+	+10,000	1.2GB
messages	500,000+	+50,000	2.5GB
notifications	2,000,000+	+200,000	1.8GB
Total	2.7M+	+268K	~7GB
