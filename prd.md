# Product Requirements Document (PRD)

## Production-Scale URL Shortener Platform

**Product name:** Linkify -- URL Shortener Platform\
**Version:** 1.0\
**Status:** Draft\
**Product type:** SaaS web application\
**Target platforms:** Web application and REST API

------------------------------------------------------------------------

## 1. Product Overview

Linkify is a production-scale URL shortener that allows users to convert
long URLs into short, shareable links. Users can create, manage,
customize, track, and analyze shortened URLs through a dashboard.

The platform supports anonymous users, authenticated users, developers,
teams, and organizations. It is designed to scale from an initial MVP to
a high-volume redirect service.

### Example

-   Original:
    `https://example.com/products/electronics/mobile/iphone-17-pro-max`
-   Short: `https://lnkfy.in/aB92xK`

When someone visits the shortened URL, the platform redirects them to
the original destination and records an analytics event asynchronously.

## 2. Problem Statement

Long URLs are difficult to share, remember, and manage. Users and
businesses also need convenient ways to:

-   Track link clicks and visitor activity.
-   Customize shortened URLs.
-   Monitor link performance.
-   Set expiration dates and manage access.
-   Manage multiple links from one dashboard.
-   Detect malicious or suspicious destinations.
-   Integrate URL shortening into external applications.

**Proposed solution:** A centralized platform providing URL shortening,
link management, analytics, security, and developer APIs.

## 3. Product Goals and Objectives

  Goal             Description
  ---------------- ------------------------------------------------
  Fast redirects   Minimize redirect latency
  Scalability      Support millions of links
  Reliability      Target 99.95% availability
  Security         Prevent phishing and malicious redirects
  Analytics        Provide historical and near-real-time insights
  Usability        Provide a simple, responsive dashboard
  Extensibility    Provide REST APIs and webhooks

### Non-goals for the initial release

-   Native Android and iOS applications.
-   AI-generated marketing campaigns.
-   Full marketing automation.
-   Website hosting.
-   Blockchain-based URL management.

## 4. Target Users

  -----------------------------------------------------------------------
  User type                           Primary requirements
  ----------------------------------- -----------------------------------
  Guest users                         Shorten URLs without registration,
                                      subject to abuse limits

  Registered users                    Manage and track links

  Marketers                           Campaign tracking and analytics

  Developers                          API access and integrations

  Businesses                          Teams, permissions, and custom
                                      domains

  Administrators                      Platform monitoring and abuse
                                      management
  -----------------------------------------------------------------------

## 5. Functional Requirements

### 5.1 Authentication and User Management

Users should be able to:

-   Register using email and password.
-   Log in and log out.
-   Verify their email address.
-   Reset forgotten passwords.
-   Sign in using Google OAuth.
-   Update profile information.
-   Delete their account.
-   Manage active sessions.

**Security requirements:**

-   Hash passwords using Argon2id or bcrypt.
-   Use short-lived access tokens and secure refresh-token rotation.
-   Apply rate limits to authentication endpoints.
-   Enforce role-based access control (RBAC).
-   Store secrets outside source control.

**Roles:**

1.  Guest
2.  Free user
3.  Premium user
4.  Team administrator
5.  Platform administrator

### 5.2 URL Shortening

**Priority: P0 -- Core functionality**

Users can enter a long URL and generate a short URL.

Features:

-   Automatically generate unique short codes.
-   Support custom aliases.
-   Validate destination URLs.
-   Handle duplicate destination URLs according to product policy.
-   Copy shortened URLs.
-   Generate QR codes.
-   Enable or disable shortened URLs.
-   Set link expiration.
-   Support optional password-protected links.
-   Optionally show a destination preview page.

**Business rules:**

-   Short codes must be globally unique.
-   Expired links must not redirect.
-   Disabled links must return an appropriate error page.
-   Custom aliases must be checked for availability.
-   Reserved aliases such as `admin`, `login`, and `api` cannot be used.
-   Users can only manage links they own or have permission to access.
-   Destination URLs must use allowed schemes, initially HTTP and HTTPS.

### 5.3 Link Management Dashboard

The dashboard should provide:

-   Total links created.
-   Total clicks.
-   Estimated unique visitors.
-   Active and expired links.
-   Recent link activity.
-   Search and filter options.
-   Sorting by creation date and clicks.
-   Link settings and editing.
-   Delete or archive actions.
-   Bulk actions.

### 5.4 Analytics and Tracking

**Priority: P0 -- Core functionality**

Each redirect should produce an analytics event without making the
redirect wait for analytics processing.

Track, where lawful and appropriate:

  Metric             Description
  ------------------ -----------------------------------
  Total clicks       All redirect requests
  Unique visitors    Estimated distinct visitors
  Country            Approximate visitor country
  Device             Mobile, desktop, or tablet
  Browser            Browser family
  Operating system   Operating system family
  Referrer           Referring website, when available
  Timestamp          Event date and time

Analytics features:

-   View clicks by date.
-   Compare link performance.
-   View top-performing links.
-   Analyze geographic distribution.
-   Filter by device and browser.
-   Export reports as CSV.

**Privacy requirements:**

-   Minimize collection of personal data.
-   Avoid storing full IP addresses unless there is a justified,
    disclosed need.
-   Define retention and deletion policies.
-   Respect applicable privacy laws and user consent requirements.
-   Explain that unique visitor counts are estimates and may be affected
    by bots, shared devices, and privacy protections.

### 5.5 QR Code Generation

-   Generate QR codes for shortened URLs.
-   Download as PNG or SVG.
-   Support configurable sizes.
-   Allow basic color customization.
-   Ensure generated QR codes resolve to the shortened URL.

### 5.6 Link Expiration and Scheduling

Users can:

-   Set an expiration date and time.
-   Schedule link activation.
-   Automatically disable expired links.
-   View expiration status.
-   Extend expiration dates, subject to permissions.

### 5.7 Custom Domains

Premium and business users can:

-   Connect their own domains.
-   Verify domain ownership using DNS records.
-   Generate branded shortened links.
-   Manage multiple domains.
-   Configure and renew SSL/TLS certificates.

Example: `https://go.company.com/product`

### 5.8 Developer API

Provide a versioned REST API.

  Method   Endpoint                        Purpose
  -------- ------------------------------- --------------------------------
  POST     `/api/v1/links`                 Create a short URL
  GET      `/api/v1/links`                 List the current user's links
  GET      `/api/v1/links/:id`             Get link details
  PATCH    `/api/v1/links/:id`             Update a link
  DELETE   `/api/v1/links/:id`             Delete or archive a link
  GET      `/api/v1/links/:id/analytics`   Get link analytics
  GET      `/api/v1/links/:id/qr`          Generate or retrieve a QR code

Example request:

``` http
POST /api/v1/links
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

``` json
{
  "originalUrl": "https://example.com/products",
  "customAlias": "my-product",
  "expiresAt": "2027-01-01T00:00:00Z"
}
```

Example response:

``` json
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
```

The API must also define validation errors, authentication failures,
authorization failures, rate-limit responses, pagination, and consistent
error formats.

## 6. Non-functional Requirements

  Requirement        Initial target
  ------------------ ----------------------------------------------------
  Availability       99.95% monthly target
  Redirect latency   p95 under 100 ms in the agreed test environment
  API latency        p95 under 250 ms for standard operations
  Scalability        Horizontal scaling
  Data durability    No acknowledged link loss
  Security           OWASP-aligned controls
  Recovery           Documented backup and disaster recovery objectives
  Observability      Metrics, logs, and distributed traces
  Mobile support     Responsive user interface

These are engineering targets, not measured guarantees. Validate them
through load testing and production monitoring. Define the measurement
region, traffic mix, and dependency assumptions before treating them as
service-level objectives.

## 7. System Architecture

### 7.1 High-level architecture

``` text
                         CLIENTS
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
              +-------------+-------------+
              |                           |
              v                           v
        Authentication                Link Service
              |                           |
              v                    +------+------+
          PostgreSQL                |             |
                                    v             v
                                  Redis       PostgreSQL
                                    ^
                                    |
Incoming short-link request         |
              |                     |
              v                     |
        CDN / Edge (optional)        |
              |                     |
              v                     |
        Redirect Service -----------+
              |
              v
       Durable Event Queue
              |
              v
       Analytics Workers
              |
              v
         ClickHouse
              |
              v
        Dashboard API
```

### 7.2 Architecture approach

Start with a modular monolith and clear module boundaries. Extract
high-throughput components such as redirect handling and analytics
processing into independent services when measured traffic or
operational needs justify it.

### 7.3 Core components

  -----------------------------------------------------------------------
  Component                           Responsibility
  ----------------------------------- -----------------------------------
  CDN                                 Edge delivery and static assets;
                                      optionally cache safe redirect
                                      responses

  WAF                                 Filter malicious traffic

  Load balancer                       Distribute requests

  API gateway                         Routing, authentication
                                      integration, and rate limiting

  Auth module                         Authentication and authorization

  Link service                        Create and manage links

  Redirect service                    Resolve short codes and redirect

  Redis                               Cache link mappings and support
                                      rate limits

  PostgreSQL                          Users, links, domains, teams, and
                                      billing data

  Kafka                               Asynchronous event streaming at
                                      scale

  Analytics workers                   Process click events

  ClickHouse                          Analytics queries

  Object storage                      Reports and exports
  -----------------------------------------------------------------------

## 8. Technology Stack

Recommended stack for a full-stack implementation:

  -----------------------------------------------------------------------
  Layer                   Technology              Purpose
  ----------------------- ----------------------- -----------------------
  Frontend                React or Next.js,       Web application
                          TypeScript              

  UI                      Tailwind CSS            Styling

  Charts                  Recharts                Analytics visualization

  Backend                 Node.js, TypeScript,    REST APIs and services
                          Express or NestJS       

  Primary database        PostgreSQL              Transactional data

  Cache                   Redis                   Short-code lookup and
                                                  rate limiting

  Event streaming         Kafka                   High-volume
                                                  asynchronous events

  Analytics database      ClickHouse              Click analytics

  Containers              Docker, Docker Compose  Local development and
                                                  packaging

  Cloud                   AWS                     Hosting and managed
                                                  infrastructure

  CI/CD                   GitHub Actions          Automated testing and
                                                  deployment

  Monitoring              Prometheus, Grafana,    Metrics and tracing
                          OpenTelemetry           
  -----------------------------------------------------------------------

MongoDB can be used for an MVP, but PostgreSQL is a strong default for
transactional link ownership, uniqueness constraints, and relational
data.

## 9. Database Design

### 9.1 Users table

``` sql
CREATE TABLE users (
    id UUID PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash TEXT,
    role VARCHAR(30) DEFAULT 'free',
    created_at TIMESTAMPTZ DEFAULT NOW()
);
```

### 9.2 Links table

``` sql
CREATE TABLE links (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    short_code VARCHAR(32) UNIQUE NOT NULL,
    original_url TEXT NOT NULL,
    custom_alias BOOLEAN DEFAULT FALSE,
    status VARCHAR(20) DEFAULT 'active',
    expires_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_links_user_id ON links(user_id);
CREATE INDEX idx_links_status ON links(status);
```

### 9.3 Click events table (illustrative only)

``` sql
CREATE TABLE click_events (
    id BIGSERIAL PRIMARY KEY,
    link_id UUID NOT NULL,
    country VARCHAR(100),
    device VARCHAR(50),
    browser VARCHAR(100),
    referrer TEXT,
    clicked_at TIMESTAMPTZ DEFAULT NOW()
);
```

For production, use a dedicated analytics store such as ClickHouse
rather than allowing an indefinitely growing click-events table to
overwhelm the transactional PostgreSQL database.

### 9.4 Additional tables

-   `api_keys`
-   `domains`
-   `teams`
-   `team_members`
-   `subscriptions`
-   `link_passwords`
-   `link_tags`
-   `audit_logs`
-   `refresh_tokens`
-   `abuse_reports`

The final schema should define foreign keys, indexes, uniqueness rules,
deletion behavior, and data-retention policies.

## 10. Short Code Generation Strategy

A short code should be compact and unique.

### 10.1 Base62 alphabet

Use the following characters:

``` text
0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ
```

A six-character code has:

``` text
62^6 = 56,800,235,584
```

possible combinations.

### 10.2 Option A: Random Base62

1.  Generate a cryptographically secure random code.
2.  Attempt to insert it into the database.
3.  Enforce a unique constraint on `short_code`.
4.  If a collision occurs, generate another code and retry.

### 10.3 Option B: Unique ID converted to Base62

1.  Allocate a unique numeric ID.
2.  Convert it to Base62.
3.  Use the result as the short code.

**Recommendation:** Use random Base62 for unpredictable codes, or
distributed ID generation plus Base62 for high-volume allocation. Never
rely on probability alone for uniqueness; enforce uniqueness in
persistent storage. Consider minimum code length, enumeration
resistance, reserved words, and code-space growth.

## 11. Redirect Flow

When a visitor opens:

``` text
https://lnkfy.in/aB92xK
```

The redirect service should:

1.  Receive the request.
2.  Validate the short code format.
3.  Check Redis for the mapping.
4.  On a cache hit, retrieve the destination and relevant status
    metadata.
5.  On a cache miss, query PostgreSQL.
6.  Verify link status, activation time, and expiration.
7.  Return the appropriate redirect response.
8.  Publish a click event asynchronously through a durable
    event-publishing path.

### Illustrative pseudocode

``` javascript
async function redirect(req, res) {
  const { code } = req.params;

  let link = await cache.getLink(code);

  if (!link) {
    link = await database.findLinkByCode(code);

    if (!link) {
      return res.status(404).send("Link not found");
    }

    await cache.setLink(code, link, { ttlSeconds: 3600 });
  }

  if (link.status !== "active") {
    return res.status(410).send("Link unavailable");
  }

  if (link.expiresAt && new Date(link.expiresAt) <= new Date()) {
    return res.status(410).send("Link expired");
  }

  // Publish through a durable asynchronous event pipeline.
  // Do not wait for analytics processing before redirecting.
  eventPublisher.publishClick({
    linkId: link.id,
    clickedAt: new Date().toISOString()
  }).catch(error => {
    logger.error({ error, linkId: link.id }, "Click event publish failed");
  });

  return res.redirect(302, link.originalUrl);
}
```

This is illustrative pseudocode, not production-ready code. A production
implementation must address:

-   Cache invalidation when links are edited, disabled, or deleted.
-   Stale cache entries and expiration enforcement.
-   Redis failure and database fallback.
-   Durable event delivery, retries, and dead-letter handling.
-   Event duplication and idempotent analytics processing.
-   Request validation, logging, and error handling.
-   Password-protected links and scheduled activation.

### Redirect status choice

Use `302` or `307` for temporary redirects when destinations may change.
Avoid permanently caching `301` redirects for editable links because
browsers and intermediaries may retain them.

## 12. Security Requirements

URL shorteners can be abused for phishing, malware distribution, and
spam.

  Threat                 Protection
  ---------------------- ---------------------------------------------------
  Phishing links         URL reputation checks and abuse reporting
  Spam creation          Account and IP-based rate limits
  Brute-force guessing   Request throttling and monitoring
  XSS                    Context-aware output encoding
  SQL injection          Parameterized queries
  Account takeover       Secure authentication and session controls
  SSRF                   Isolated URL scanning with strict egress controls
  Abuse                  Moderation and takedown workflow
  API attacks            API keys, quotas, and rate limits
  Cache poisoning        Trusted cache writes and validation

Additional requirements:

-   Allow only HTTP and HTTPS destination schemes.
-   Reject unsafe schemes such as `javascript:` and `data:`.
-   Validate and normalize URLs consistently.
-   Implement abuse reporting and a takedown process.
-   Maintain administrative audit logs.
-   Use HTTPS everywhere.
-   Restrict administrative endpoints.
-   Apply bot detection and suspicious-traffic monitoring.
-   Protect URL preview and scanning services from SSRF.
-   Never fetch arbitrary destination URLs from the redirect request
    path.

## 13. Scalability Strategy

Design the platform to scale horizontally.

### Phase 1 -- Initial launch

-   One backend application.
-   PostgreSQL.
-   Redis.
-   Docker.
-   One cloud deployment.
-   Basic monitoring and backups.

### Phase 2 -- Growing traffic

-   Multiple backend instances.
-   Load balancing.
-   Redis replication or managed Redis.
-   CDN integration.
-   Separate analytics workers.
-   Background processing.
-   Database read replicas where appropriate.

### Phase 3 -- Large-scale production

-   Independent redirect service.
-   Kafka event streaming.
-   ClickHouse analytics.
-   Multi-region deployment where justified.
-   Database read replicas and partitioning.
-   Automated failover.
-   Retention and archival policies.
-   Capacity planning and disaster recovery exercises.

### Illustrative traffic assumptions

These are planning examples, not actual forecasts or guarantees.

  Metric                Initial target   Growth target
  ------------------- ---------------- ---------------
  Registered users              10,000       1 million
  Total links                  100,000      50 million
  Redirect requests            100/sec      10,000/sec
  Analytics events             100/sec      10,000/sec

At high traffic, redirect infrastructure and analytics infrastructure
should scale independently.

## 14. UI/UX Requirements

### Main pages

1.  **Landing page**
    -   URL input field.
    -   Shorten button.
    -   Product introduction.
    -   Features and pricing.
2.  **Authentication**
    -   Login.
    -   Registration.
    -   Forgot password.
    -   Google login.
3.  **Dashboard**
    -   Link management.
    -   Search and filtering.
    -   Performance overview.
4.  **Analytics**
    -   Click charts.
    -   Country analytics.
    -   Device breakdown.
    -   Date filters and CSV export.
5.  **Settings**
    -   Profile.
    -   API keys.
    -   Custom domains.
    -   Security and sessions.

### UX requirements

-   Responsive desktop and mobile layouts.
-   Accessible forms and keyboard navigation.
-   Clear loading, success, and error states.
-   Confirmation for destructive actions.
-   Clear explanation of link status and expiration.
-   Copy-to-clipboard feedback.
-   Empty states that explain the next action.

## 15. Monetization Strategy

Use a freemium model.

  Feature           Free        Pro             Business
  ----------------- ----------- --------------- ---------------
  Short links       Limited     Higher limits   Custom limits
  Custom aliases    Yes         Yes             Yes
  Analytics         Basic       Advanced        Advanced
  Custom domains    No          Yes             Yes
  QR codes          Basic       Customizable    Customizable
  API access        Limited     Higher quotas   Higher quotas
  Team management   No          Limited         Yes
  Support           Community   Priority        Dedicated

Actual quotas and prices should be decided after estimating
infrastructure costs, abuse risk, and customer demand.

## 16. Testing Requirements

### Unit testing

-   Short code generation.
-   URL validation.
-   Authentication.
-   Link expiration.
-   Permission checks.
-   Cache behavior.

### Integration testing

-   PostgreSQL operations.
-   Redis cache operations.
-   API authentication.
-   Redirect functionality.
-   Event publishing and consumption.
-   Cache invalidation.

### Load testing

Use k6 or Locust.

Test scenarios:

-   100 concurrent users.
-   1,000 concurrent users.
-   A target of 10,000 requests per second, if infrastructure and budget
    support it.
-   Cache-hit and cache-miss scenarios.
-   Sudden traffic spikes.
-   Redis failure.
-   Database failure.
-   Queue backlog and worker recovery.

### Acceptance criteria

-   Shortened URLs redirect to the correct destinations.
-   No duplicate short codes are created.
-   Unauthorized users cannot modify other users' links.
-   Expired, disabled, and not-yet-active links do not redirect.
-   Analytics events are processed asynchronously.
-   Duplicate events do not inflate analytics when idempotency is
    required.
-   The system recovers gracefully from component failures.
-   Redirect latency meets the agreed performance target under the
    defined load test.

## 17. Development Roadmap

Suggested eight-week implementation plan for an individual developer
working consistently.

  Phase       Duration Deliverables
  --------- ---------- ---------------------------------------
  Phase 1       Week 1 Requirements, architecture, UI design
  Phase 2       Week 2 Authentication and database
  Phase 3       Week 3 URL shortening and redirects
  Phase 4       Week 4 Redis and caching
  Phase 5       Week 5 Dashboard and link management
  Phase 6       Week 6 Analytics and QR codes
  Phase 7       Week 7 Security, testing, and Docker
  Phase 8       Week 8 Cloud deployment and monitoring

Advanced multi-region, enterprise, and high-volume streaming features
can follow after the core platform is stable.

## 18. MVP vs. Production Features

  Feature            MVP            Production
  ------------------ -------------- --------------------------------
  Authentication     Required       Advanced security
  URL shortening     Required       Distributed code generation
  Redirect service   Required       Edge optimization
  Redis caching      Basic          Cluster and replication
  Analytics          Basic clicks   ClickHouse and event streaming
  QR code            Basic          Custom branding
  Custom domains     Later          Full support
  REST API           Basic          API keys and quotas
  Kafka              Later          Event streaming
  Docker             Required       Orchestration
  Monitoring         Basic          Full observability
  Multi-region       No             Advanced

## 19. Success Metrics (KPIs)

Measure product success through:

-   Monthly active users.
-   Number of links created.
-   Total redirects.
-   Redirect success rate.
-   Average and p95 redirect latency.
-   Cache hit ratio.
-   Analytics processing delay.
-   Abuse reports and confirmed malicious links.
-   User retention.
-   Free-to-paid conversion rate.

Define each metric precisely. For example, distinguish redirect requests
from successful redirects and raw clicks from estimated unique visitors.

## 20. Recommended Implementation Plan

For a portfolio project demonstrating production engineering, build the
platform in this order:

### Core stack

  Layer        Recommended technology
  ------------ -----------------------------------
  Frontend     React + TypeScript + Tailwind CSS
  Backend      Node.js + Express
  Database     PostgreSQL
  Cache        Redis
  Messaging    Kafka (later phase)
  Analytics    ClickHouse (later phase)
  Deployment   Docker + AWS

### Development checklist

-   [ ] Create project structure and GitHub repository.
-   [ ] Design database schema.
-   [ ] Implement registration and login.
-   [ ] Build URL shortening API.
-   [ ] Implement unique Base62 short codes.
-   [ ] Build redirect service.
-   [ ] Integrate Redis caching.
-   [ ] Build URL management dashboard.
-   [ ] Implement click analytics.
-   [ ] Add QR code generation.
-   [ ] Implement rate limiting and abuse protection.
-   [ ] Dockerize the application.
-   [ ] Write unit and integration tests.
-   [ ] Deploy to a cloud environment.
-   [ ] Set up monitoring and load testing.

### Implementation advice

Do not start by implementing Kafka, Kubernetes, or microservices. First
build a working URL shortener with React, Node.js, PostgreSQL, and
Redis. Then introduce asynchronous event processing, Docker, cloud
deployment, monitoring, and distributed architecture as the requirements
justify them.

This sequence helps validate the product early while building a strong
system-design portfolio project.

------------------------------------------------------------------------

**Document end**
