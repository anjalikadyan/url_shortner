Product Requirements Document (PRD)
Production-Scale URL Shortener Platform
Production-ready vision
Full-stack project
System design

Product Name: Linkify – URL Shortener Platform

Version: 1.0

Document Status: Draft

Product Type: SaaS Web Application

Target Platform: Web Application + REST API

Primary Goal: Build a highly scalable, secure, reliable URL-shortening platform capable of handling millions of shortened URLs and high-volume redirects.

1. Product Overview
Linkify is a production-scale URL shortener that allows users to convert long URLs into short, shareable links.

Users can create, manage, customize, track, and analyze shortened URLs through a dashboard.

The platform should support both anonymous users and authenticated users, with advanced features for premium users and organizations.
Example
ORIGINAL URL

https://example.com/products/electronics/mobile/iphone-17-pro-max


SHORTENED URL
lnkfy.in/aB92xK

Active



When someone visits the shortened URL, the platform redirects them to the original destination and records analytics events.
2. Problem Statement
Long URLs are difficult to share, remember, and manage.

Existing URL-sharing workflows also lack convenient ways to:
- Track link clicks and visitor activity.
- Customize shortened URLs.
- Monitor link performance.
- Set expiration dates.
- Manage multiple links from one dashboard.
- Detect malicious or suspicious destinations.
- Integrate URL shortening into external applications.
Proposed solution: A centralized platform providing URL shortening, link management, analytics, security, and developer APIs.
3. Product Goals and Objectives
Goal	Description
Fast redirects	Minimize redirect latency
Scalability	Support millions of links
Reliability	Target 99.95% availability
Security	Prevent phishing and malicious URLs
Analytics	Provide real-time and historical insights
Usability	Simple and responsive dashboard
Extensibility	Provide REST APIs and webhooks

Non-goals for initial release
- Native Android and iOS applications.
- AI-generated marketing campaigns.
- Full marketing automation platform.
- URL hosting or website hosting.
- Blockchain-based URL management.
4. Target Users
User type	Requirements
Guest users	Shorten URLs without registration
Registered users	Manage and track links
Marketers	Campaign tracking and analytics
Developers	API access and integrations
Businesses	Teams, permissions, and custom domains
Administrators	Platform monitoring and abuse management

5. Functional Requirements
5.1 Authentication and User Management
Users should be able to:
- Register using email and password.
- Login and logout.
- Verify email address.
- Reset forgotten passwords.
- Login using Google OAuth.
- Update profile information.
- Delete their account.
- Manage active sessions.
Security requirements:
- Password hashing using Argon2id or bcrypt.
- JWT access tokens with short expiration.
- Refresh-token rotation.
- Rate limiting on authentication endpoints.
- Role-based access control (RBAC).
Roles:
1. Guest
2. Free User
3. Premium User
4. Team Admin
5. Platform Admin
5.2 URL Shortening
P0 – Core functionality
Users can enter a long URL and generate a short URL.

Features:
- Automatically generate unique short codes.
- Custom aliases.
- URL validation.
- Duplicate URL handling.
- Copy shortened URL.
- QR code generation.
- Enable/disable shortened URLs.
- Set link expiration.
- Optional password-protected links.
- Optional redirect preview page.
Example:
Original:
https://www.amazon.in/dp/example-product

Short:
https://lnkfy.in/aB92xK

Custom:
https://lnkfy.in/my-product


Business rules:
- Short codes must be globally unique.
- Expired links should not redirect.
- Disabled links should return an appropriate error page.
- Custom aliases must be checked for availability.
- Reserved aliases such as admin, login, and api cannot be used.
- Users can only manage links they own or have permission to access.
5.3 Link Management Dashboard







dribbble.com


The dashboard should provide:
- Total links created.
- Total clicks.
- Unique visitors (estimated).
- Active links.
- Expired links.
- Recent link activity.
- Search and filter options.
- Sort by creation date and clicks.
- Edit link settings.
- Delete or archive links.
- Bulk actions.
5.4 Analytics and Tracking
P0 – Core functionality
Every redirect should generate an analytics event.

Track:
Metric	Description
Total clicks	All redirect requests
Unique visitors	Estimated distinct visitors
Country	Visitor's approximate country
Device	Mobile, desktop, tablet
Browser	Chrome, Firefox, Safari, etc.
Operating system	Windows, Linux, Android, etc.
Referrer	Website or source of traffic
Timestamp	Date and time of click

Analytics dashboard:







preline.co


Users should be able to:
- View clicks by date.
- Compare link performance.
- View top-performing links.
- Analyze geographic distribution.
- Filter by device and browser.
- Export reports as CSV.
Important: Analytics collection must not slow down the redirect operation. Redirect processing and analytics processing should be decoupled using a message queue or event stream.
5.5 QR Code Generation
- Generate QR codes for shortened URLs.
- Download as PNG or SVG.
- Support different sizes.
- Allow basic color customization.
- Ensure QR codes point to the shortened URL.
5.6 Link Expiration and Scheduling
Users can:
- Set expiration date and time.
- Schedule link activation.
- Automatically disable expired URLs.
- View expiration status.
- Extend expiration dates.
5.7 Custom Domains
Premium and business users can:
- Connect their own domains.
- Verify domain ownership using DNS records.
- Generate branded shortened links.
- Manage multiple domains.
- Configure SSL certificates.
Example:
go.company.com/product


5.8 Developer API
Provide a REST API for developers.

Example endpoints:
Method	Endpoint	Purpose
POST	/api/v1/links	Create short URL
GET	/api/v1/links	Get user's links
GET	/api/v1/links/:id	Get link details
PATCH	/api/v1/links/:id	Update link
DELETE	/api/v1/links/:id	Delete link
GET	/api/v1/links/:id/analytics	Get analytics
GET	/api/v1/links/:id/qr	Generate QR code

Example API request:
POST /api/v1/links
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json


{
  "originalUrl": "https://example.com/products",
  "customAlias": "my-product",
  "expiresAt": "2027-01-01T00:00:00Z"
}


Example response:
{
  "success": true,
  "data": {
    "id": "link_12345",
    "shortCode": "my-product",
    "shortUrl": "https://lnkfy.in/my-product",
    "originalUrl": "https://example.com/products",
    "status": "active"
  }
}


6. Non-functional Requirements
These requirements distinguish a production-scale system from a basic CRUD project.
Requirement	Target
Availability	99.95% monthly
Redirect latency	p95 under 100 ms
API latency	p95 under 250 ms
Scalability	Horizontally scalable
Data durability	No acknowledged link loss
Security	OWASP-aligned
Recovery	Defined backup and disaster recovery
Observability	Metrics, logs, traces
Mobile support	Responsive UI

These are initial engineering targets, not measured performance guarantees. They should be validated through load testing.
7. System Architecture







medium.com


Recommended architecture
Use a modular monolith initially, with clearly separated modules. Extract high-throughput services such as redirects and analytics into independent services when necessary.
                 CLIENT
                   |
                   v
             CDN / WAF
                   |
                   v
             Load Balancer
                   |
                   v
              API Gateway
                   |
          +--------+---------+
          |                  |
          v                  v
    Auth Service       Link Service
          |                  |
          |             +----+-----+
          |             |          |
          v             v          v
       Database      Redis      Database
                         |
                         |
USER CLICKS SHORT URL    |
          |              |
          v              |
      CDN / Edge         |
          |              |
          v              |
      Redirect Service <-+
          |
          +-------> Event Queue
                         |
                         v
                  Analytics Worker
                         |
                         v
                   Analytics DB
                         |
                         v
                  Dashboard API


Core components
Component	Responsibility
CDN	Edge caching and static assets
WAF	Filter malicious traffic
Load balancer	Distribute requests
API gateway	Routing and rate limiting
Auth module	Authentication and authorization
Link service	Create and manage links
Redirect service	Resolve short codes
Redis	Cache link mappings
PostgreSQL	Persistent link and user data
Kafka	Asynchronous event processing
Analytics worker	Process click events
ClickHouse	Analytics queries
Object storage	Reports and exports

8. Technology Stack
Considering a MERN/full-stack development background, this is a practical stack.









Frontend – React / Next.js

React, TypeScript, Tailwind CSS, Recharts












Backend – Node.js

Node.js, TypeScript, Express.js or NestJS












Primary database – PostgreSQL

Users, links, domains, billing and permissions












Cache – Redis

Short-code lookup, rate limits, hot links












Message broker – Kafka

Click event streaming and asynchronous processing












Containerization – Docker

Docker, Docker Compose












Cloud – AWS

EC2/ECS, RDS, ElastiCache, S3, CloudFront












CI/CD and monitoring

GitHub Actions, Prometheus, Grafana, OpenTelemetry



Alternative: MongoDB can be used for the initial MVP, but PostgreSQL is a strong default for transactional link ownership, uniqueness constraints, and relational data.
9. Database Design
Users
CREATE TABLE users (
    id UUID PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash TEXT,
    role VARCHAR(30) DEFAULT 'free',
    created_at TIMESTAMP DEFAULT NOW()
);


Links
CREATE TABLE links (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    short_code VARCHAR(20) UNIQUE NOT NULL,
    original_url TEXT NOT NULL,
    custom_alias BOOLEAN DEFAULT FALSE,
    status VARCHAR(20) DEFAULT 'active',
    expires_at TIMESTAMP,
    created_at TIMESTAMP DEFAULT NOW()
);


Click Events
CREATE TABLE click_events (
    id BIGSERIAL PRIMARY KEY,
    link_id UUID NOT NULL,
    country VARCHAR(100),
    device VARCHAR(50),
    browser VARCHAR(100),
    referrer TEXT,
    clicked_at TIMESTAMP DEFAULT NOW()
);


For production, click events should be stored in a dedicated analytics system such as ClickHouse rather than keeping an indefinitely growing click-events table in the transactional PostgreSQL database.
Additional tables
- api_keys
- domains
- teams
- team_members
- subscriptions
- link_passwords
- link_tags
- audit_logs
10. Short Code Generation Strategy
This is one of the most important technical decisions.

A short code should be unique and compact.
Option 1: Random Base62
Use characters:
0123456789
abcdefghijklmnopqrstuvwxyz
ABCDEFGHIJKLMNOPQRSTUVWXYZ


Example:
aB92xK


For a six-character code, the possible combinations are:
\[
62^6 = 56,800,235,584
\]

Generate a random code and enforce a unique database constraint. Retry on collisions.
Option 2: ID + Base62
1. Generate a unique numeric ID.
2. Convert it to Base62.
3. Use the resulting string as the short code.
For example:
Database ID: 125678
Base62: w7E


Recommendation: Start with random Base62 codes for unpredictability, or use distributed ID generation plus Base62 for high-volume allocation. Never rely on probability alone for uniqueness; enforce it in persistent storage.
11. Redirect Flow
Most performance-critical feature
When a user visits:
https://lnkfy.in/aB92xK


The following process happens:
1. Receive the request.
2. Validate the short code.
3. Check Redis cache.
4. If cache hit, retrieve destination.
5. If cache miss, query PostgreSQL.
6. Validate link status and expiration.
7. Return HTTP redirect.
8. Publish click event asynchronously.
Example:
app.get("/:code", async (req, res) => {
  const { code } = req.params;

  let link = await redis.get(`link:${code}`);

  if (!link) {
    link = await Link.findOne({
      shortCode: code,
      status: "active"
    });

    if (!link) {
      return res.status(404).send("Link not found");
    }

    await redis.set(
      `link:${code}`,
      JSON.stringify(link),
      "EX",
      3600
    );
  } else {
    link = JSON.parse(link);
  }

  if (link.expiresAt && new Date(link.expiresAt) <= new Date()) {
    return res.status(410).send("Link expired");
  }

  // Publish analytics event asynchronously.
  // Never wait for analytics before redirecting.

  return res.redirect(302, link.originalUrl);
});


Illustrative MVP code only. Production implementation needs proper Redis parsing, database field mapping, cache invalidation, event publishing, error handling, and protections against stale cached links.

Important production decisions:
- Use 302 or 307 for temporary redirects when link destinations can change.
- Avoid permanently caching 301 redirects for editable links.
- Do not let Redis outages take down the entire service; fall back to the database.
- Use an outbox or durable event publishing strategy to avoid losing analytics events.
- Avoid logging sensitive visitor information unnecessarily.
12. Security Requirements
Security is essential because URL shorteners can be abused for phishing and malicious redirects.
Threat	Protection
Phishing links	URL reputation checks
Spam creation	Rate limiting
Brute-force guessing	Request throttling
XSS	Input/output sanitization
SQL injection	Parameterized queries
Account takeover	Secure authentication
SSRF	Safe URL scanning architecture
Abuse	Reporting and moderation
API attacks	API keys and quotas
Cache poisoning	Trusted cache writes and validation

Additional requirements:
- Only allow HTTP and HTTPS destinations.
- Block unsafe URL schemes such as javascript: and data:.
- Implement abuse reporting.
- Maintain audit logs.
- Use HTTPS everywhere.
- Restrict administrative endpoints.
- Add bot detection and suspicious traffic monitoring.
13. Scalability Strategy
The platform should be designed to scale horizontally.
Phase 1 – Initial launch
- One backend application.
- PostgreSQL.
- Redis.
- Docker.
- One cloud deployment.
Phase 2 – Growing traffic
- Multiple backend instances.
- Load balancing.
- Redis replication.
- CDN integration.
- Separate analytics workers.
- Background processing.
Phase 3 – Large-scale production
- Independent redirect service.
- Kafka event streaming.
- ClickHouse analytics.
- Multi-region deployment.
- Database read replicas.
- Automated failover.
- Partitioning and retention policies.
Example traffic assumptions
These are illustrative planning assumptions, not actual user forecasts.
Metric	Initial target	Growth target
Registered users	10,000	1 million
Total links	100,000	50 million
Redirect requests	100/sec	10,000/sec
Analytics events	100/sec	10,000/sec

At high traffic, redirect infrastructure and analytics infrastructure should scale independently.
14. UI/UX Requirements
Main pages









1. Landing Page
- URL input field
- Shorten button
- Product introduction
- Features and pricing












2. Authentication
- Login
- Registration
- Forgot password
- Google login












3. Dashboard
- Link management
- Search and filtering
- Performance overview












4. Analytics
- Click charts
- Country analytics
- Device breakdown












5. Settings
- Profile
- API keys
- Custom domains
- Security



15. Monetization Strategy
Use a freemium model.
Feature	Free	Pro	Business
Short links	Limited	Higher limits	Custom limits
Custom aliases	Yes	Yes	Yes
Analytics	Basic	Advanced	Advanced
Custom domains	No	Yes	Yes
QR codes	Basic	Customizable	Customizable
API access	Limited	Higher quotas	Higher quotas
Team management	No	Limited	Yes
Support	Community	Priority	Dedicated

Actual quotas and prices should be decided after estimating infrastructure costs and customer demand.
16. Testing Requirements
Unit testing
- Short code generation.
- URL validation.
- Authentication.
- Link expiration.
- Permission checks.
Integration testing
- PostgreSQL operations.
- Redis cache operations.
- API authentication.
- Redirect functionality.
- Event queue publishing.
Load testing
Use k6 or Locust.

Test scenarios:
- 100 concurrent users.
- 1,000 concurrent users.
- 10,000 requests per second.
- Cache hit and cache miss scenarios.
- Sudden traffic spikes.
- Redis failure.
- Database failure.
- Queue backlog.
Acceptance criteria
- Shortened URLs redirect to the correct destinations.
- No duplicate short codes are created.
- Unauthorized users cannot modify other users' links.
- Expired and disabled links do not redirect.
- Analytics events are processed asynchronously.
- System recovers gracefully from component failures.
- Redirect latency meets the agreed performance target under the specified load.
17. Development Roadmap
Suggested implementation roadmap for a full-stack developer.
Phase	Duration	Deliverables
Phase 1	Week 1	Requirements, architecture, UI design
Phase 2	Week 2	Authentication and database
Phase 3	Week 3	URL shortening and redirects
Phase 4	Week 4	Redis and caching
Phase 5	Week 5	Dashboard and link management
Phase 6	Week 6	Analytics and QR codes
Phase 7	Week 7	Security, testing, Docker
Phase 8	Week 8	AWS deployment and monitoring

Estimated schedule for an individual developer working consistently. Advanced multi-region and enterprise features can come later.
18. MVP vs Production Features
Prioritize implementation instead of building everything simultaneously.
Feature	MVP	Production
Authentication	Required	Advanced security
URL shortening	Required	Distributed generation
Redirect service	Required	Edge optimization
Redis caching	Basic	Cluster and replication
Analytics	Basic clicks	ClickHouse
QR code	Basic	Custom branding
Custom domains	Later	Full support
REST API	Basic	API keys and quotas
Kafka	Later	Event streaming
Docker	Required	Orchestration
Monitoring	Basic	Full observability
Multi-region	No	Advanced

19. Success Metrics (KPIs)
Measure product success through:
- Monthly active users.
- Number of links created.
- Total redirects.
- Redirect success rate.
- Average and p95 redirect latency.
- Percentage of cached lookups.
- Analytics processing delay.
- Abuse reports and confirmed malicious links.
- User retention.
- Free-to-paid conversion rate.

20. Final Recommended Implementation
For a portfolio project that demonstrates real production engineering, I recommend building these components first:
Linkify – Core Architecture
Recommended stack
Frontend

React + TypeScript + Tailwind


Backend

Node.js + Express


Database

PostgreSQL


Cache

Redis


Messaging

Kafka


Deployment

Docker + AWS




