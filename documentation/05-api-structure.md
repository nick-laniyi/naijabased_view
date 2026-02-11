🔌 NaijaBased - API Structure & Documentation
1. API Philosophy
Design Principles:

RESTful: Resource-based endpoints, HTTP methods, status codes

Consistent: Predictable URL patterns, uniform responses

Versioned: Future-proof with /v1/, /v2/ prefix

Secure: Authentication required, rate limiting, HTTPS only

Documented: Self-descriptive, examples provided

2. Base URL
text
Production: https://api.naijabased.com/v1
Staging:   https://staging-api.naijabased.com/v1
Local:     http://localhost:8080/api/v1
3. Authentication
3.1 API Key Authentication
http
GET /v1/user/profile HTTP/1.1
Host: api.naijabased.com
Authorization: Bearer {api_key}
Content-Type: application/json
3.2 Session Authentication (Web)
http
POST /v1/auth/login HTTP/1.1
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "********"
}
Response:

json
{
  "status": "success",
  "data": {
    "user_id": 12345,
    "name": "John Doe",
    "email": "user@example.com",
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expires_in": 86400
  }
}
3.3 OAuth2 (Partner Applications)
Authorization Code Flow: For third-party apps

Client Credentials: For server-to-server

Refresh Tokens: 30-day validity

4. Response Format
4.1 Success Response
json
{
  "status": "success",
  "code": 200,
  "message": "Request processed successfully",
  "data": {
    // Resource data
  },
  "meta": {
    "timestamp": "2026-02-11T10:30:00Z",
    "request_id": "req_abc123def456"
  }
}
4.2 Paginated Response
json
{
  "status": "success",
  "data": [
    // Array of resources
  ],
  "meta": {
    "current_page": 1,
    "per_page": 20,
    "total": 1250,
    "last_page": 63,
    "next_page_url": "/v1/marketplace?page=2",
    "prev_page_url": null
  }
}
4.3 Error Response
json
{
  "status": "error",
  "code": 400,
  "error": "validation_failed",
  "message": "The given data was invalid",
  "errors": {
    "email": ["The email field is required", "The email must be valid"],
    "password": ["The password must be at least 8 characters"]
  },
  "request_id": "req_abc123def456"
}
4.4 HTTP Status Codes
Code	Description	When to Use
200	OK	Success
201	Created	Resource created
400	Bad Request	Validation error
401	Unauthorized	Missing/invalid auth
403	Forbidden	Insufficient permissions
404	Not Found	Resource doesn't exist
422	Unprocessable Entity	Business logic error
429	Too Many Requests	Rate limit exceeded
500	Server Error	Something went wrong
5. Rate Limiting
http
HTTP/1.1 200 OK
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 58
X-RateLimit-Reset: 1644588000
Endpoint Group	Limit	Window
Public endpoints	60 requests	1 minute
Authenticated	120 requests	1 minute
Payment endpoints	30 requests	1 minute
Search	60 requests	1 minute
Messaging	120 requests	1 minute
6. API Endpoints
6.1 Authentication & Users
POST /v1/auth/register
Register a new user account.

Request:

json
{
  "username": "johndoe",
  "email": "john@example.com",
  "password": "SecurePass123",
  "full_name": "John Doe",
  "phone": "08031234567"
}
Response:

json
{
  "status": "success",
  "data": {
    "user_id": 12345,
    "username": "johndoe",
    "email": "john@example.com",
    "requires_verification": true
  }
}
POST /v1/auth/login
Authenticate user and get access token.

Request:

json
{
  "login": "johndoe@example.com",
  "password": "SecurePass123",
  "remember": true
}
Response:

json
{
  "status": "success",
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expires_in": 86400,
    "user": {
      "id": 12345,
      "username": "johndoe",
      "email": "john@example.com",
      "avatar": "https://cdn.naijabased.com/avatars/12345.jpg",
      "is_verified": true
    }
  }
}
POST /v1/auth/logout
Invalidate current session.

Response:

json
{
  "status": "success",
  "message": "Logged out successfully"
}
POST /v1/auth/password/reset
Request password reset email.

Request:

json
{
  "email": "john@example.com"
}
Response:

json
{
  "status": "success",
  "message": "Password reset link sent to your email"
}
GET /v1/user/profile
Get authenticated user profile.

Response:

json
{
  "status": "success",
  "data": {
    "id": 12345,
    "username": "johndoe",
    "full_name": "John Doe",
    "email": "john@example.com",
    "phone": "08031234567",
    "avatar": "https://cdn.naijabased.com/avatars/12345.jpg",
    "cover": "https://cdn.naijabased.com/covers/12345.jpg",
    "bio": "Tech entrepreneur from Lagos",
    "location": "Lagos, Nigeria",
    "state": "Lagos",
    "city": "Ikeja",
    "website": "https://johndoe.com",
    "social_links": {
      "twitter": "@johndoe",
      "instagram": "@johndoe",
      "linkedin": "johndoe"
    },
    "stats": {
      "followers": 1250,
      "following": 430,
      "posts": 89,
      "reviews": 45,
      "rating": 4.8
    },
    "badges": [
      "email_verified",
      "phone_verified",
      "top_rated"
    ],
    "created_at": "2024-01-15T10:30:00Z"
  }
}
PUT /v1/user/profile
Update user profile.

Request:

json
{
  "full_name": "John A. Doe",
  "bio": "Updated bio text",
  "location": "Lagos",
  "website": "https://johndoe.dev"
}
Response:

json
{
  "status": "success",
  "data": {
    "updated_fields": ["full_name", "bio", "location", "website"]
  }
}
6.2 Marketplace
GET /v1/marketplace
List marketplace items with filters.

Query Parameters:

Parameter	Type	Description
category	string	Filter by category
city	string	Filter by city
state	string	Filter by state
min_price	number	Minimum price (NGN)
max_price	number	Maximum price (NGN)
condition	string	new/used/refurbished
seller_id	integer	Filter by seller
search	string	Full-text search
sort	string	price_asc, price_desc, newest, oldest, relevance
page	integer	Page number
limit	integer	Items per page (default 20, max 100)
Response:

json
{
  "status": "success",
  "data": [
    {
      "id": 54321,
      "title": "iPhone 12 Pro Max",
      "slug": "iphone-12-pro-max-256gb",
      "price": 450000,
      "negotiable": true,
      "condition": "used",
      "category": "Electronics > Phones",
      "brand": "Apple",
      "images": [
        "https://cdn.naijabased.com/marketplace/54321/1.jpg",
        "https://cdn.naijabased.com/marketplace/54321/2.jpg"
      ],
      "location": "Ikeja, Lagos",
      "seller": {
        "id": 12345,
        "username": "johndoe",
        "avatar": "https://cdn.naijabased.com/avatars/12345.jpg",
        "rating": 4.9
      },
      "views": 1250,
      "saves": 89,
      "created_at": "2026-02-10T14:23:00Z"
    }
  ],
  "meta": {
    "current_page": 1,
    "per_page": 20,
    "total": 1250,
    "filters_applied": {
      "category": "Electronics",
      "city": "Lagos",
      "min_price": 10000,
      "max_price": 500000
    }
  }
}
GET /v1/marketplace/{id}
Get single marketplace item.

Response:

json
{
  "status": "success",
  "data": {
    "id": 54321,
    "title": "iPhone 12 Pro Max",
    "slug": "iphone-12-pro-max-256gb",
    "description": "Slightly used iPhone 12 Pro Max. 256GB storage, Pacific Blue. Battery health 92%. Comes with original charger and box.",
    "price": 450000,
    "negotiable": true,
    "original_price": 650000,
    "condition": "used",
    "category": "Electronics > Phones",
    "subcategory": "Smartphones",
    "brand": "Apple",
    "model": "iPhone 12 Pro Max",
    "year": 2021,
    "color": "Pacific Blue",
    "specifications": {
      "storage": "256GB",
      "ram": "6GB",
      "screen": "6.7 inches",
      "battery": "3687mAh"
    },
    "location": "Ikeja, Lagos",
    "city": "Ikeja",
    "state": "Lagos",
    "delivery_options": ["pickup", "shipping"],
    "shipping_cost": 2500,
    "warranty": "3 months store warranty",
    "images": [
      "https://cdn.naijabased.com/marketplace/54321/1.jpg",
      "https://cdn.naijabased.com/marketplace/54321/2.jpg",
      "https://cdn.naijabased.com/marketplace/54321/3.jpg"
    ],
    "seller": {
      "id": 12345,
      "username": "johndoe",
      "full_name": "John Doe",
      "avatar": "https://cdn.naijabased.com/avatars/12345.jpg",
      "phone": "08031234567",
      "whatsapp": "08031234567",
      "is_verified": true,
      "member_since": "2024-01-15",
      "response_rate": 98,
      "response_time": "< 1 hour",
      "total_sales": 156,
      "rating": 4.9,
      "reviews_count": 89
    },
    "stats": {
      "views": 1250,
      "unique_views": 890,
      "saves": 89,
      "shares": 34,
      "questions": 12
    },
    "is_saved": true,
    "created_at": "2026-02-10T14:23:00Z",
    "updated_at": "2026-02-11T09:15:00Z"
  }
}
POST /v1/marketplace
Create new marketplace listing.

Request:

json
{
  "title": "iPhone 12 Pro Max",
  "description": "Slightly used iPhone 12 Pro Max...",
  "price": 450000,
  "negotiable": true,
  "condition": "used",
  "category": "Electronics > Phones",
  "brand": "Apple",
  "model": "iPhone 12 Pro Max",
  "color": "Pacific Blue",
  "location": "Ikeja, Lagos",
  "city": "Ikeja",
  "state": "Lagos",
  "delivery_options": ["pickup", "shipping"],
  "shipping_cost": 2500,
  "images": [
    "data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEAYABgAAD...",
    "data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEAYABgAAD..."
  ],
  "warranty": "3 months"
}
Response:

json
{
  "status": "success",
  "data": {
    "id": 54321,
    "slug": "iphone-12-pro-max-256gb",
    "message": "Listing created successfully"
  }
}
PUT /v1/marketplace/{id}
Update existing listing.

Request:

json
{
  "price": 430000,
  "negotiable": true,
  "description": "Updated description with more details"
}
Response:

json
{
  "status": "success",
  "data": {
    "id": 54321,
    "updated_fields": ["price", "description"]
  }
}
DELETE /v1/marketplace/{id}
Delete listing.

Response:

json
{
  "status": "success",
  "message": "Listing deleted successfully"
}
6.3 Businesses
GET /v1/businesses
List verified businesses.

Query Parameters:

Parameter	Type	Description
category	string	Filter by category
city	string	Filter by city
state	string	Filter by state
verified	boolean	Show only verified (default: true)
featured	boolean	Show only featured
search	string	Full-text search
page	integer	Page number
limit	integer	Items per page
Response:

json
{
  "status": "success",
  "data": [
    {
      "id": 789,
      "business_name": "Mama Put Restaurant",
      "slug": "mama-put-restaurant-ikeja",
      "category": "Food & Beverage",
      "logo": "https://cdn.naijabased.com/business/789/logo.jpg",
      "cover_image": "https://cdn.naijabased.com/business/789/cover.jpg",
      "city": "Ikeja",
      "state": "Lagos",
      "is_verified": true,
      "verification_level": "premium",
      "rating": 4.7,
      "reviews_count": 342,
      "featured": true
    }
  ]
}
GET /v1/businesses/{id}
Get complete business profile.

Response:

json
{
  "status": "success",
  "data": {
    "id": 789,
    "business_name": "Mama Put Restaurant",
    "slug": "mama-put-restaurant-ikeja",
    "description": "Authentic Nigerian cuisine since 2010...",
    "category": "Food & Beverage",
    "subcategory": "Restaurant",
    "address": "15 Allen Avenue",
    "city": "Ikeja",
    "state": "Lagos",
    "lga": "Ikeja",
    "landmark": "Near Allen Roundabout",
    "latitude": 6.601838,
    "longitude": 3.351486,
    "phone": "08012345678",
    "whatsapp": "08012345678",
    "email": "contact@mamaput.com",
    "website": "https://mamaput.com",
    "social_media": {
      "instagram": "@mamaputlagos",
      "facebook": "mamaputrestaurant",
      "twitter": "@mamaput"
    },
    "business_hours": {
      "monday": "08:00-22:00",
      "tuesday": "08:00-22:00",
      "wednesday": "08:00-22:00",
      "thursday": "08:00-22:00",
      "friday": "08:00-23:00",
      "saturday": "10:00-23:00",
      "sunday": "12:00-20:00"
    },
    "services": [
      "Dine-in",
      "Takeaway",
      "Delivery",
      "Catering"
    ],
    "price_range": "₦1,000 - ₦5,000",
    "established_year": 2010,
    "employees": "11-50",
    "is_verified": true,
    "verification_level": "premium",
    "verified_at": "2024-03-15T10:30:00Z",
    "is_featured": true,
    "featured_until": "2026-03-15",
    "gallery": [
      "https://cdn.naijabased.com/business/789/gallery/1.jpg",
      "https://cdn.naijabased.com/business/789/gallery/2.jpg",
      "https://cdn.naijabased.com/business/789/gallery/3.jpg"
    ],
    "stats": {
      "views": 15420,
      "unique_views": 8970,
      "reviews_count": 342,
      "rating_avg": 4.7,
      "rating_breakdown": {
        "5": 210,
        "4": 89,
        "3": 32,
        "2": 8,
        "1": 3
      },
      "saves": 567,
      "shares": 234
    },
    "owner": {
      "id": 5678,
      "name": "Adebayo Ogunlesi",
      "avatar": "https://cdn.naijabased.com/avatars/5678.jpg",
      "joined": "2024-01-20"
    },
    "created_at": "2024-01-20T14:23:00Z"
  }
}
POST /v1/businesses/verify/initiate
Initiate business verification (KYC).

Request:

json
{
  "business_id": 789,
  "document_type": "cac_registration",
  "document_file": "data:application/pdf;base64,JVBERi0xLjQKJcOkw7zD...",
  "business_address": "15 Allen Avenue, Ikeja, Lagos",
  "utility_bill": "data:image/jpeg;base64,/9j/4AAQSkZJRg..."
}
Response:

json
{
  "status": "success",
  "data": {
    "verification_id": "vfy_abc123",
    "status": "pending",
    "estimated_time": "24-48 hours"
  }
}
6.4 Events
GET /v1/events
List upcoming events.

Query Parameters:

Parameter	Type	Description
category	string	Event type
city	string	Filter by city
state	string	Filter by state
start_date	date	Events after date
end_date	date	Events before date
free	boolean	Free events only
featured	boolean	Featured events
search	string	Search by title/location
page	integer	Page number
Response:

json
{
  "status": "success",
  "data": [
    {
      "id": 456,
      "title": "Lagos Tech Summit 2026",
      "slug": "lagos-tech-summit-2026",
      "event_type": "Conference",
      "format": "hybrid",
      "start_date": "2026-05-15T09:00:00Z",
      "end_date": "2026-05-17T18:00:00Z",
      "location": "Eko Convention Centre",
      "city": "Lagos",
      "state": "Lagos",
      "cover_image": "https://cdn.naijabased.com/events/456/cover.jpg",
      "is_free": false,
      "min_price": 15000,
      "max_price": 50000,
      "currency": "NGN",
      "attendees_count": 850,
      "capacity": 2000,
      "organizer": {
        "id": 123,
        "name": "TechNaija",
        "avatar": "https://cdn.naijabased.com/avatars/123.jpg"
      },
      "created_at": "2026-01-10T10:00:00Z"
    }
  ]
}
GET /v1/events/{id}
Get detailed event information.

Response:

json
{
  "status": "success",
  "data": {
    "id": 456,
    "title": "Lagos Tech Summit 2026",
    "slug": "lagos-tech-summit-2026",
    "description": "Africa's largest technology gathering...",
    "short_description": "3 days of innovation, networking, and learning",
    "event_type": "Conference",
    "format": "hybrid",
    "start_date": "2026-05-15T09:00:00Z",
    "end_date": "2026-05-17T18:00:00Z",
    "registration_deadline": "2026-05-14T23:59:00Z",
    "location": "Eko Convention Centre",
    "venue": "Hall 1 & 2",
    "address": "Victoria Island",
    "city": "Lagos",
    "state": "Lagos",
    "latitude": 6.4281,
    "longitude": 3.4216,
    "meeting_link": "https://zoom.us/j/123456789",
    "cover_image": "https://cdn.naijabased.com/events/456/cover.jpg",
    "gallery": [...],
    "capacity": 2000,
    "attendees_count": 850,
    "waitlist_count": 45,
    "is_free": false,
    "has_tickets": true,
    "ticket_tiers": [
      {
        "id": 1,
        "name": "Early Bird",
        "price": 15000,
        "quantity": 500,
        "sold": 500,
        "available": 0,
        "benefits": ["Full access", "Workshop pass", "Lunch included"]
      },
      {
        "id": 2,
        "name": "Regular",
        "price": 25000,
        "quantity": 1000,
        "sold": 350,
        "available": 650,
        "benefits": ["Full access", "Workshop pass", "Lunch included", "Swag bag"]
      },
      {
        "id": 3,
        "name": "VIP",
        "price": 50000,
        "quantity": 500,
        "sold": 0,
        "available": 500,
        "benefits": ["Full access", "VIP lounge", "Meet speakers", "Premium swag", "Networking dinner"]
      }
    ],
    "organizer": {
      "id": 123,
      "name": "TechNaija",
      "description": "Nigeria's largest tech community",
      "avatar": "https://cdn.naijabased.com/avatars/123.jpg",
      "email": "events@technaija.com",
      "phone": "08012345678"
    },
    "speakers": [
      {
        "name": "Dr. Ngozi Okonjo-Iweala",
        "title": "Director General, WTO",
        "avatar": "https://cdn.naijabased.com/speakers/1.jpg",
        "topic": "The Future of African Innovation"
      }
    ],
    "sponsors": [...],
    "schedule": [...],
    "faqs": [...],
    "stats": {
      "views": 3450,
      "saves": 567,
      "shares": 234
    },
    "is_saved": true,
    "is_registered": false,
    "created_at": "2026-01-10T10:00:00Z"
  }
}
POST /v1/events/{id}/book
Book tickets for event.

Request:

json
{
  "tickets": [
    {
      "tier_id": 2,
      "quantity": 2
    }
  ],
  "promo_code": "TECH10",
  "attendee_details": [
    {
      "name": "John Doe",
      "email": "john@example.com",
      "phone": "08031234567"
    },
    {
      "name": "Jane Doe",
      "email": "jane@example.com",
      "phone": "08031234568"
    }
  ]
}
Response:

json
{
  "status": "success",
  "data": {
    "booking_id": "bk_789xyz",
    "total_amount": 45000,
    "payment_url": "https://paystack.com/pay/xyz789",
    "expires_at": "2026-02-11T15:30:00Z"
  }
}
6.5 Jobs
GET /v1/jobs
List job postings.

Query Parameters:

Parameter	Type	Description
category	string	Job category
job_type	string	full-time/part-time/contract/internship/remote
experience	string	entry/mid/senior/lead
city	string	Location city
state	string	Location state
remote	boolean	Remote only
salary_min	number	Minimum salary
salary_max	number	Maximum salary
search	string	Search by title/company
page	integer	Page number
Response:

json
{
  "status": "success",
  "data": [
    {
      "id": 7890,
      "title": "Senior PHP Developer",
      "slug": "senior-php-developer-lagos",
      "company_name": "TechNaija",
      "company_logo": "https://cdn.naijabased.com/companies/123/logo.png",
      "job_type": "full-time",
      "work_mode": "hybrid",
      "location": "Lagos",
      "city": "Lagos",
      "state": "Lagos",
      "salary_min": 500000,
      "salary_max": 800000,
      "salary_period": "month",
      "currency": "NGN",
      "deadline": "2026-04-30",
      "is_featured": true,
      "created_at": "2026-02-01T08:00:00Z"
    }
  ]
}
GET /v1/jobs/{id}
Get detailed job posting.

Response:

json
{
  "status": "success",
  "data": {
    "id": 7890,
    "title": "Senior PHP Developer",
    "slug": "senior-php-developer-lagos",
    "description": "We are looking for an experienced PHP developer...",
    "requirements": "• 5+ years PHP experience\n• Laravel framework\n• MySQL optimization\n• REST API design\n• Team leadership",
    "responsibilities": "• Lead development team\n• Architecture decisions\n• Code reviews\n• Mentoring juniors",
    "company_name": "TechNaija",
    "company_description": "Leading tech company in Nigeria...",
    "company_logo": "https://cdn.naijabased.com/companies/123/logo.png",
    "company_website": "https://technaija.com",
    "company_size": "51-200",
    "company_founded": 2018,
    "category": "Technology",
    "subcategory": "Software Development",
    "job_type": "full-time",
    "work_mode": "hybrid",
    "experience_level": "senior",
    "location": "Lagos",
    "city": "Lagos",
    "state": "Lagos",
    "remote_option": true,
    "salary_min": 500000,
    "salary_max": 800000,
    "salary_period": "month",
    "is_negotiable": true,
    "currency": "NGN",
    "benefits": [
      "Health insurance",
      "Pension scheme",
      "Annual bonus",
      "Remote days",
      "Training budget"
    ],
    "application_method": "internal",
    "deadline": "2026-04-30",
    "skills_required": [
      "PHP",
      "Laravel",
      "MySQL",
      "Redis",
      "REST APIs",
      "Git"
    ],
    "stats": {
      "views": 1250,
      "applications": 45,
      "saves": 89
    },
    "is_featured": true,
    "featured_until": "2026-03-30",
    "posted_by": {
      "id": 123,
      "name": "Sarah Johnson",
      "title": "HR Manager",
      "avatar": "https://cdn.naijabased.com/avatars/123.jpg"
    },
    "created_at": "2026-02-01T08:00:00Z"
  }
}
POST /v1/jobs/{id}/apply
Apply for job.

Request:

json
{
  "full_name": "John Doe",
  "email": "john@example.com",
  "phone": "08031234567",
  "cover_letter": "I am writing to express my strong interest...",
  "resume": "data:application/pdf;base64,JVBERi0xLjQKJcOkw7zD...",
  "portfolio_url": "https://johndoe.dev",
  "linkedin_url": "https://linkedin.com/in/johndoe",
  "answers": [
    {
      "question": "Why do you want to work with us?",
      "answer": "I admire your company's mission..."
    },
    {
      "question": "What is your expected salary?",
      "answer": "₦600,000 - ₦750,000 monthly"
    }
  ]
}
Response:

json
{
  "status": "success",
  "data": {
    "application_id": "app_456abc",
    "message": "Application submitted successfully",
    "next_steps": "You will be contacted within 5 business days"
  }
}
6.6 Communities
GET /v1/communities
List communities.

Query Parameters:

Parameter	Type	Description
category	string	Community category
city	string	Filter by city
state	string	Filter by state
private	boolean	Include private?
search	string	Search by name/description
page	integer	Page number
Response:

json
{
  "status": "success",
  "data": [
    {
      "id": 123,
      "name": "Lagos Developers",
      "slug": "lagos-developers",
      "description": "Community for software developers in Lagos",
      "category": "Technology",
      "avatar": "https://cdn.naijabased.com/communities/123/avatar.jpg",
      "banner": "https://cdn.naijabased.com/communities/123/banner.jpg",
      "members_count": 3450,
      "posts_count": 1250,
      "is_private": false,
      "is_member": true,
      "created_at": "2024-01-15T10:30:00Z"
    }
  ]
}
POST /v1/communities
Create new community.

Request:

json
{
  "name": "Lagos Foodies",
  "description": "Discover the best restaurants and food spots in Lagos",
  "category": "Food & Lifestyle",
  "is_private": false,
  "requires_approval": true,
  "location": "Lagos",
  "city": "Lagos",
  "state": "Lagos",
  "avatar": "data:image/jpeg;base64,/9j/4AAQSkZJRg..."
}
Response:

json
{
  "status": "success",
  "data": {
    "id": 124,
    "slug": "lagos-foodies",
    "message": "Community created successfully"
  }
}
POST /v1/communities/{id}/join
Join community.

Response:

json
{
  "status": "success",
  "data": {
    "membership_id": "mem_789xyz",
    "status": "approved",
    "message": "You have joined Lagos Foodies"
  }
}
6.7 Feed & Social
GET /v1/feed
Get personalized feed.

Query Parameters:

Parameter	Type	Description
page	integer	Page number
limit	integer	Items per page (default 20)
Response:

json
{
  "status": "success",
  "data": [
    {
      "id": 98765,
      "type": "post",
      "content": {
        "id": 543,
        "user": {
          "id": 12345,
          "username": "johndoe",
          "full_name": "John Doe",
          "avatar": "https://cdn.naijabased.com/avatars/12345.jpg",
          "verified": true
        },
        "content": "Just launched my new product on NaijaBased! Check it out 🚀",
        "media": [
          "https://cdn.naijabased.com/posts/543/media1.jpg"
        ],
        "likes": 45,
        "comments": 12,
        "shares": 5,
        "hashtags": ["naijabased", "newproduct", "lagos"],
        "created_at": "2026-02-11T09:23:00Z",
        "is_liked": true,
        "is_saved": false
      }
    },
    {
      "id": 98764,
      "type": "marketplace",
      "content": {
        "id": 54321,
        "title": "iPhone 12 Pro Max",
        "price": 450000,
        "images": ["https://cdn.naijabased.com/marketplace/54321/1.jpg"],
        "seller": {
          "id": 12345,
          "username": "johndoe",
          "avatar": "https://cdn.naijabased.com/avatars/12345.jpg"
        },
        "created_at": "2026-02-10T14:23:00Z"
      }
    }
  ],
  "meta": {
    "current_page": 1,
    "per_page": 20,
    "total": 1250,
    "has_more": true
  }
}
POST /v1/posts
Create new post.

Request:

json
{
  "content": "Excited to announce our upcoming tech meetup! #lagostech",
  "media": [
    "data:image/jpeg;base64,/9j/4AAQSkZJRg..."
  ],
  "privacy": "public",
  "location": "Lagos",
  "hashtags": ["lagostech", "meetup"]
}
Response:

json
{
  "status": "success",
  "data": {
    "id": 544,
    "message": "Post created successfully"
  }
}
POST /v1/posts/{id}/like
Like/unlike post.

Response:

json
{
  "status": "success",
  "data": {
    "liked": true,
    "likes_count": 46
  }
}
POST /v1/posts/{id}/comment
Add comment to post.

Request:

json
{
  "content": "Great news! Will be there 🙌",
  "parent_id": null
}
Response:

json
{
  "status": "success",
  "data": {
    "id": 7890,
    "content": "Great news! Will be there 🙌",
    "user": {
      "id": 12345,
      "username": "johndoe",
      "avatar": "https://cdn.naijabased.com/avatars/12345.jpg"
    },
    "created_at": "2026-02-11T10:15:00Z"
  }
}
6.8 Messaging
GET /v1/conversations
List user conversations.

Response:

json
{
  "status": "success",
  "data": [
    {
      "id": 5678,
      "type": "direct",
      "participant": {
        "id": 6789,
        "username": "janedoe",
        "full_name": "Jane Doe",
        "avatar": "https://cdn.naijabased.com/avatars/6789.jpg",
        "is_online": true,
        "last_seen": "2026-02-11T10:30:00Z"
      },
      "last_message": {
        "id": 123456,
        "content": "Is this still available?",
        "sender_id": 6789,
        "created_at": "2026-02-11T10:15:00Z",
        "is_read": false
      },
      "unread_count": 2,
      "updated_at": "2026-02-11T10:15:00Z"
    }
  ]
}
GET /v1/conversations/{id}/messages
Get conversation messages.

Query Parameters:

Parameter	Type	Description
before	timestamp	Messages before timestamp
limit	integer	Messages per page (default 50)
Response:

json
{
  "status": "success",
  "data": {
    "conversation_id": 5678,
    "messages": [
      {
        "id": 123456,
        "sender_id": 6789,
        "sender_name": "Jane Doe",
        "sender_avatar": "https://cdn.naijabased.com/avatars/6789.jpg",
        "content": "Is this still available?",
        "type": "text",
        "created_at": "2026-02-11T10:15:00Z",
        "is_read": false,
        "is_delivered": true
      },
      {
        "id": 123455,
        "sender_id": 12345,
        "sender_name": "John Doe",
        "sender_avatar": "https://cdn.naijabased.com/avatars/12345.jpg",
        "content": "Yes, it's still available",
        "type": "text",
        "created_at": "2026-02-11T10:16:00Z",
        "is_read": true,
        "is_delivered": true
      }
    ],
    "has_more": false
  }
}
POST /v1/conversations/{id}/messages
Send message.

Request:

json
{
  "content": "I'd like to buy it",
  "type": "text"
}
Response:

json
{
  "status": "success",
  "data": {
    "id": 123457,
    "content": "I'd like to buy it",
    "sender_id": 12345,
    "created_at": "2026-02-11T10:35:00Z"
  }
}
POST /v1/conversations/start
Start new conversation.

Request:

json
{
  "recipient_id": 6789,
  "initial_message": "Hi, I'm interested in your listing",
  "context_type": "marketplace",
  "context_id": 54321
}
Response:

json
{
  "status": "success",
  "data": {
    "conversation_id": 5679,
    "message": "Conversation started"
  }
}
6.9 Payments
POST /v1/payments/initialize
Initialize payment.

Request:

json
{
  "amount": 45000,
  "email": "customer@example.com",
  "currency": "NGN",
  "reference": "order_789xyz",
  "callback_url": "https://naijabased.com/payment/callback",
  "metadata": {
    "order_id": 12345,
    "customer_name": "John Doe",
    "items": [
      {
        "name": "iPhone 12 Pro Max",
        "quantity": 1,
        "price": 45000
      }
    ]
  }
}
Response:

json
{
  "status": "success",
  "data": {
    "authorization_url": "https://paystack.com/pay/xyz789",
    "access_code": "xyz789",
    "reference": "order_789xyz"
  }
}
GET /v1/payments/verify/{reference}
Verify payment status.

Response:

json
{
  "status": "success",
  "data": {
    "reference": "order_789xyz",
    "amount": 45000,
    "status": "success",
    "paid_at": "2026-02-11T10:40:00Z",
    "channel": "card",
    "card_type": "visa",
    "last4": "1234",
    "customer": {
      "email": "customer@example.com",
      "phone": null
    }
  }
}
6.10 Search
GET /v1/search
Global search across all content.

Query Parameters:

Parameter	Type	Description
q	string	Search query (required)
type	string	marketplace/businesses/events/jobs/communities/users/all
location	string	City or state
page	integer	Page number
limit	integer	Results per page
Response:

json
{
  "status": "success",
  "data": {
    "query": "iphone",
    "suggestions": ["iphone 13", "iphone charger", "iphone case"],
    "results": {
      "marketplace": {
        "total": 45,
        "items": [...]
      },
      "businesses": {
        "total": 12,
        "items": [...]
      },
      "total_results": 57
    }
  }
}
6.11 Notifications
GET /v1/notifications
Get user notifications.

Query Parameters:

Parameter	Type	Description
page	integer	Page number
limit	integer	Items per page (default 20)
unread_only	boolean	Show only unread
Response:

json
{
  "status": "success",
  "data": {
    "unread_count": 3,
    "notifications": [
      {
        "id": 98765,
        "type": "like",
        "title": "New like",
        "message": "Jane Doe liked your post",
        "actor": {
          "id": 6789,
          "username": "janedoe",
          "avatar": "https://cdn.naijabased.com/avatars/6789.jpg"
        },
        "url": "/post/543",
        "image": null,
        "is_read": false,
        "created_at": "2026-02-11T09:15:00Z"
      }
    ]
  }
}
POST /v1/notifications/mark-read
Mark notifications as read.

Request:

json
{
  "notification_ids": [98765, 98764],
  "all": false
}
Response:

json
{
  "status": "success",
  "message": "Notifications marked as read"
}
7. Webhooks
7.1 Paystack Webhook
http
POST /webhooks/paystack
Payload:

json
{
  "event": "charge.success",
  "data": {
    "reference": "order_789xyz",
    "amount": 45000,
    "status": "success",
    "paid_at": "2026-02-11T10:40:00Z"
  }
}
Response:

json
{
  "status": "success"
}
7.2 Termii Webhook (SMS Delivery)
http
POST /webhooks/termii
Payload:

json
{
  "message_id": "msg_123abc",
  "status": "delivered",
  "to": "08031234567",
  "sent_at": "2026-02-11T10:30:00Z"
}
8. SDK & Client Libraries
Language	Repository	Status
PHP	naijabased/php-sdk	✅ Available
JavaScript	naijabased/js-sdk	✅ Available
Python	naijabased/python-sdk	🚧 In Progress
Flutter	naijabased/flutter-sdk	🚧 In Progress
9. API Changelog
v1.2.0 (2026-02-01)
Added /search endpoint with unified search

Added location filters to marketplace endpoints

Added negotiable field to marketplace items

v1.1.0 (2026-01-15)
Added pagination metadata

Added rate limit headers

Added business verification endpoints

v1.0.0 (2024-12-01)
Initial release

Core endpoints: auth, users, marketplace, businesses, events, jobs, communities, messaging, payments

10. Support & Contact
API Support:

Email: api@naijabased.com

Documentation: https://developers.naijabased.com

Status: https://status.naijabased.com

GitHub: https://github.com/nick-laniyi/naijabased_view

This API documentation is continuously updated. Last updated: February 2026

