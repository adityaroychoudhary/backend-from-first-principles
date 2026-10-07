# Backend from First Principles

## 1. High-Level Understanding

1. How backend systems work behind the scenes
2. How a request flows from the browser to a remote server (e.g. on AWS): DNS, firewalls, the internet, load balancers/reverse proxies, and then the application server
3. How the server handles the request and what the response looks like

**Idea:** how the client and server communicate, and the response the server sends back.

## 2. HTTP Protocol

1. The role HTTP plays in web communication
2. How a connection is established (TCP, TLS, then HTTP)
3. What raw HTTP messages look like
4. Headers: their role and the types
   - Request headers
   - Response headers
   - Representation headers
   - Security headers
   - General headers
5. HTTP methods: GET, POST, PUT, PATCH, DELETE (plus HEAD, OPTIONS), when to use each, and the semantics behind them (e.g. safe and idempotent)
6. The core request/response flow
7. Simple requests vs. preflight requests (CORS): the preflight flow from browser to server and back
8. Structure of HTTP responses and status codes: when to return which, and the most common ones
9. HTTP caching: types and techniques (`Cache-Control`/`max-age`, `ETag`, validation)
10. Differences between HTTP/1.0, HTTP/1.1, HTTP/2 and HTTP/3
11. Content negotiation between client and server using headers
12. Persistent connections
13. Compression types: gzip, deflate, Brotli
14. Security: TLS (the successor to SSL) and HTTPS

# Routing

1. How routing maps a URL (and HTTP method) to server-side logic
2. Connection between routing and HTTP methods
3. Components of a route
   - Path parameters
   - Query parameters
4. Types of routes
   - Static routes
   - Dynamic routes
   - Nested routes
   - Hierarchical routes
   - Regex-based routes
   - Catch-all / wildcard routes
5. API versioning using HTTP and the different approaches (URI path, query parameter, custom header, content negotiation via media type)
6. How to deprecate a route and industry best practices (`Deprecation` and `Sunset` headers, a migration window, then `410 Gone`)
7. Benefits of route grouping: how it helps with permissions, versioning and shared middleware
8. How to secure routes (authentication, validation, rate limiting) and how to optimise route matching performance (route ordering and specificity, trie/radix-tree matchers)

# Serialization and Deserialization

1. What they are: serialization converts in-memory data structures into a format that can be transmitted or stored, and deserialization rebuilds those structures from the received data
2. Why it is needed and how it enables interoperability between different systems and languages
3. Formats used
   - Text-based formats (JSON, XML)
   - Binary formats (Protocol Buffers)
4. Structure of JSON and its data types (string, number, boolean, null, object, array)
5. How nested objects are serialized in JSON
6. Deserializing into native data structures: dictionaries in Python, structs in Go, objects in JavaScript, and how each language implements it
7. Common errors and how to handle them: null values, time-zone issues (use ISO 8601/UTC), and custom serialization before sending data as JSON
8. Security concerns: injection attacks, insecure deserialization of untrusted data, validating incoming data before processing, and JSON Schema validation
9. Performance: reducing payload size with compression and by removing unnecessary fields
10. Text vs. binary formats (JSON vs. Protocol Buffers): binary is faster and smaller, text is more readable and easier to debug, and when to choose which

# Authentication and Authorization

1. Why we need them: authentication verifies who you are, authorization decides what you are allowed to do
2. Types of authentication
   - Stateful (server stores session state)
   - Stateless (state travels with the request, e.g. in a token)
3. Basic authentication, bearer token authentication, sessions, JWT, cookies
4. Deep dive into OAuth 2.0 and OpenID Connect (OIDC)
5. How API keys work
6. How MFA works
7. Hashing, salting, and the cryptographic techniques behind authentication (password hashing with bcrypt/argon2, signing, encryption)
8. Access control models: RBAC, ABAC, ReBAC
9. Security best practices
   - Securing cookies (`HttpOnly`, `Secure`, `SameSite`)
   - Preventing CSRF, XSS and MITM attacks
   - Audit logging (recording authentication and authorization events), monitoring failed login attempts, privilege escalation and access to sensitive resources
   - Keeping authentication error messages generic so they don't leak information to attackers
10. Handling edge cases and keeping responses consistent across failure modes such as rate limiting and account lockout
11. Avoiding timing attacks (attackers can infer credentials from differences in response time; use constant-time comparisons)

# Validation and Transformation

1. Syntactic validation: checking that a value is well-formed, such as a valid email address, phone number, or date format
2. Semantic validation: checking that a value makes sense, such as a birth date not being in the future or an age being between 1 and 120
3. Type validation: checking that input matches the expected data types
4. Best practices for validation
5. Client-side vs. server-side validation
6. Why server-side validation is essential even when client-side validation exists: the client gives quick feedback, but the server is the gateway to your actual code and business logic
7. Failing fast: returning early to avoid unnecessary processing
8. Keeping validation consistent between front end and backend
9. Transformation: typecasting and converting formats (such as dates) within the validation pipeline
10. Normalization: adding a country code to a phone number, lowercasing email addresses, trimming whitespace
11. Sanitization: cleaning user input for security, such as stripping or escaping dangerous characters (pair it with parameterized queries for SQL injection and output encoding for XSS)
12. Complex validation logic: cross-field rules, such as checking that password and confirm password match
13. Conditional validation
14. Chained validation
15. Error handling: meaningful messages the user can act on, aggregating all validation errors into one response for client-side display, and keeping security-sensitive messages generic (e.g. "invalid credentials" instead of "invalid password")
16. Gracefully handling failed transformations
17. Performance trade-offs of validation and how to optimise it (returning early, avoiding redundant validations)

# Middlewares

1. What middleware is, when to use it, common use cases of middleware, and the role of middleware in a request cycle
2. Pre-request middleware vs. post-response middleware
3. Flow of middleware: chaining, where middleware is executed in sequence, passing control to the next middleware until the request reaches its final handler
4. How to order middleware appropriately: log the request, check if the user is authenticated, perform validation, handle the route, and handle errors
5. The next function in middleware and exiting early
6. How middleware can interrupt the request pipeline, such as when handling 404 errors
7. Common middleware: security middleware that adds security headers such as X-Content-Type-Options, Strict-Transport-Security, and Content-Security-Policy; CORS middleware that adds appropriate CORS headers to requests and responses
8. Middleware to prevent CSRF attacks
9. Middleware for rate limiting
10. Authentication middleware for reusing route-protection logic across applications
11. Logging and monitoring middleware for request logging and structured logging for observability and easier debugging in production
12. Error-handling middleware that catches and formats application-level errors for consistent API responses
13. Compression and performance-related middleware
14. Data-parsing middleware: parsing incoming request bodies such as JSON, file uploads, and URL-encoded forms
15. Performance and scalability aspect of middlewares, best practices

# Request Context

Request context is the metadata passed through application middlewares, controllers and services. It is request-scoped state: it is only valid for the lifetime of that one request.

1. Lifecycle of a request
2. Maintaining state for the duration of a request
3. Sharing data across layers of the application without coupling them
4. How context provides temporary, request-scoped state
5. Components of request context metadata
   - Request ID / trace ID
   - Authenticated user
   - Timestamps and deadlines
   - Cancellation signal
6. Tracking and logging with unique request IDs and trace IDs
7. Request-specific data injected during the lifecycle of a request (e.g. the authenticated user set by an auth middleware)
8. Use cases
9. Connection between middlewares and request context
10. Timeouts and cancellation
    - Request timeouts
    - Custom timeouts
    - Cancellation signals
11. Best practices
    - Keep it lightweight to avoid memory overhead
    - Clean up data to prevent memory leaks
    - Avoid tight coupling and over-reliance on context for passing data

# Handlers and Controllers

1. MVC pattern (in an API, the "view" is the serialized response)
2. What handlers, controllers and services are, and the responsibility of each (the terms handler and controller are often used interchangeably depending on the framework)
3. Centralizing error handling (e.g. in a single error-handling middleware), keeping success and error response formats consistent, and how to implement both in controllers

# CRUD Deep Dives

1. How CRUD operations map to HTTP methods
   - Create: POST
   - Read: GET
   - Update: PUT / PATCH
   - Delete: DELETE
2. Common APIs associated with each CRUD operation
3. Implementing pagination (offset-based and cursor-based)
4. Implementing a search API
5. Sorting and filtering
6. Best practices
   - Strict validation
   - Consistent response formatting
   - Limiting payload and page sizes
   - Redacting sensitive fields
   - Error handling
   - Authorization

# RESTful Architecture

1. Best practices for implementing REST APIs, including the core REST constraints (client-server, stateless, cacheable, uniform interface, layered system)
2. The principle of designing APIs around resources
3. Types of versioning: URI, header, query parameter and media type
4. Designing APIs with the OpenAPI Specification
5. Content negotiation
6. Handling exceptions and returning meaningful error messages
7. Supporting client-side caching with ETags
8. Optimizing large requests and responses (pagination, compression, partial responses)

# Databases

1. Relational (SQL) and non-relational (NoSQL) databases: the differences and when to use which
2. Theoretical concepts: ACID and the CAP theorem
3. Basic querying and joins
4. Database best practices: schema design (including normalization) and indexing
5. Optimization methods
   - Query optimization
   - Caching
   - Connection pooling
6. Data integrity: constraints, validations, transactions (including isolation levels) and concurrency
7. How ORMs work, whether to use them, and the trade-offs
8. Database migrations

# Business Logic Layer (BLL)

1. The role of the BLL
2. The layers of a request cycle: routing, middleware, validation, handlers, and controllers all belong to the presentation layer
3. The BLL itself: the core business logic
4. The data access layer: handles querying, inserting, updating and deleting, and is used by the BLL behind the scenes
5. Design principles
   - Separation of concerns
   - Single responsibility
   - Open/closed principle
   - Dependency inversion
6. Components of a BLL: services and domain models (which represent core entities like a user or an order)
7. Business tools and business validation logic
8. Service layer design best practices
9. Error handling: how to handle errors properly and propagate them from the service layer to the presentation layer

# Caching

1. The need for caching and how it differs from database persistence (a cache is a fast, temporary copy of data; the database is the source of truth)
2. Types of caching: in-memory caching, browser caching, CDN caching, database caching
3. The need for client-side and server-side caching
4. Caching strategies
   - Cache-aside (lazy loading)
   - Read-through
   - Write-through
   - Write-behind (write-back)
5. Cache eviction policies: LRU, LFU, FIFO, and TTL-based expiry
6. Cache invalidation: why it is needed, and manual, TTL-based and event-based invalidation
7. Levels of caching
   - L1: local, in-process memory cache (small and very fast)
   - L2: network-based distributed cache such as Redis (larger, shared across servers)
   - Hierarchical caching: combining both, with frequently used data in the small fast L1 cache and less frequently used data in the larger L2 cache
8. Caching for web apps: caching static assets, and caching API responses using headers (`Cache-Control`, `ETag`)
9. Caching with databases: query caching
10. Storing the results of expensive queries (e.g. heavy joins) in Redis
11. Cache hit ratio and miss ratio, and how to optimize them

# Transactional Emails

1. What they are and common use cases (account verification, password reset, order confirmations, receipts)
2. Anatomy of a transactional email: subject, preheader, header, main content, call to action (CTA), footer, and how to personalize them with dynamic parameters (templates)
3. Sending in practice: email providers (e.g. SES, SendGrid), sending through a task queue, and deliverability basics (SPF, DKIM, DMARC)

# Task Queuing and Scheduling

1. Common use cases: queuing emails, image processing, API integrations, payment processing, webhooks
2. Offloading heavy computation such as batch processing: respond to the client immediately and run the work as a background job by pushing it to a task queue
3. Scheduling use cases: database backups, recurring notifications and reminders, data synchronization, clearing logs and caches (typically defined with cron expressions)
4. Components of a task queue: producer, broker, consumer (worker), result backend
5. Task dependencies: chains and parent-child relationships
6. Task groups: executing multiple tasks concurrently
7. Error handling and retries in task queues (retries with exponential backoff, dead-letter queues, idempotent tasks)
8. Task prioritization and rate limiting

# Elasticsearch

1. Why we use Elasticsearch and how it works behind the scenes
2. Core techniques: the inverted index, relevance scoring (TF-IDF, with BM25 as the modern default), and shards
3. Use cases: type-ahead (autocomplete) search, log analytics
4. Creating and managing indexes
5. Searching and querying: basic search, full-text search, relevance scoring
6. Optimizing search performance: `text` vs. `keyword` fields
7. Analyzers, boosting and pagination
8. Advanced search patterns: filtering, aggregations, fuzzy search
9. How Kibana works and how to use it to explore Elasticsearch in a user-friendly way
10. Best practices: define field mappings explicitly, choose the number of shards carefully, index data in batches (bulk API), avoid wildcard queries

# Error Handling

1. Types of errors: syntax, logical, runtime
2. Error handling strategies: fail-safe, fail-fast, graceful degradation, and error prevention
3. How global error handlers work
4. The importance of monitoring and logging in error handling
5. Tools such as Sentry or the ELK stack (Elasticsearch, Logstash, Kibana)
6. Error alerts: email-based and Slack-based

# Config Management

1. What config management is, how it adds flexibility, and how it decouples environment-specific settings from application logic
2. Use cases: safely managing sensitive data such as API keys and private certificates
3. Best practices
   - Never commit secrets to version control
   - Keep separate configs per environment
   - Validate config at startup and fail fast if it is missing or invalid
   - Use a secrets manager for sensitive values
4. Kinds of config
   - Static: ports, API endpoints
   - Dynamic: feature flags, rate limits
   - Sensitive: tokens, secrets, database credentials
5. Sources of config: environment variables, JSON, YAML (and `.env` files)
6. Differences between environment variables, command-line flags and static config files

# Logging, Monitoring and Observability

1. Differences between logging, monitoring and observability
2. Types of logs: system, application, access and security logs. Log levels: DEBUG, INFO, WARN, ERROR, FATAL
3. Structured vs. unstructured logging
4. Logging best practices: centralized logging, log rotation and retention, contextual logs
5. Types of monitoring, with examples
   - Infrastructure monitoring: CPU, memory, disk usage
   - Application performance monitoring: latency, error rate, throughput
   - Uptime monitoring: periodic health-check pings
   - Security monitoring: failed logins, suspicious access patterns
6. Tools such as Prometheus and Grafana, and how to manage alerts by defining thresholds
7. The three pillars of observability: logs, metrics and traces
8. Best practices: alert on symptoms rather than causes, avoid alert fatigue, correlate logs and traces with request IDs
9. Security and compliance of log management: never log secrets or personal data, restrict access to logs, retain logs as regulations require

# Graceful Shutdown

1. Why we need graceful shutdown and how it works behind the scenes
2. Use cases: scaling in cloud environments, microservices, long-running jobs
3. Signal handling: SIGTERM, SIGINT, and SIGKILL (which cannot be caught or handled, so a graceful shutdown must finish before it arrives)
4. Steps of a graceful shutdown
   - Capture the signal
   - Stop accepting new requests
   - Finish in-flight requests
   - Close database connections and other resources
   - Exit (with a timeout as a safety net)

# Security

1. Different aspects of security in a backend codebase
2. Avoiding common attacks (e.g. the OWASP Top 10: injection, broken authentication, XSS, CSRF
3. Principles of secure software design: least privilege, defense in depth, fail securely, secure defaults, separation of duties, security by design
4. The importance of input validation and sanitization, CORS, Content Security Policy (CSP) and SameSite cookies
5. The importance of monitoring security events

# Scaling and Performance

1. Performance metrics: response time (latency), throughput, error rate, resource utilization
2. Database optimizations: avoiding N+1 queries, using joins properly
3. Using database indexes to speed up reads on large datasets (at the cost of slower writes)
4. Avoiding memory leaks: closing file handles and connections, cleaning up after long-running processes
5. Minimizing network overhead by reducing payload size
6. Performance testing (load testing) and profiling to find bottlenecks
7. Best practices: write clear, modular, maintainable code first, and optimize based on measurements rather than guesses
8. Offloading non-critical tasks to background processes so request handling stays fast

# Concurrency and Parallelism

1. The difference between concurrency (managing many tasks at once) and parallelism (running tasks at the same instant)
2. How concurrency works for I/O-bound tasks, and how parallelism helps with CPU-bound tasks
3. Concurrency models (threads, the event loop, worker threads) and common problems (race conditions, deadlocks)

# Object Storage and Large Files

1. Common use cases of object storage such as AWS S3
2. Managing large files: chunking and streaming
3. Multipart file uploads
4. Pre-signed URLs for letting clients upload or download directly

# Realtime Backend Systems

1. WebSockets and Server-Sent Events (SSE), and when to use each
2. Pub/sub architecture

# Testing and Code Quality

1. Types of testing: unit, integration, end-to-end, functional, regression, performance (load and stress), user acceptance, security
2. Test-driven development (TDD)
3. Automating tests in CI/CD environments
4. Managing code quality with linting and formatting tools
5. Measures of code quality and test coverage
6. Quality metrics such as cyclomatic complexity and the maintainability index

# 12-Factor App Principles

1. The twelve factors: codebase, dependencies, config, backing services, build/release/run, processes, port binding, concurrency, disposability, dev/prod parity, logs, admin processes

# OpenAPI Standards

1. Swagger and Postman
2. History (Swagger became the OpenAPI Specification under the Linux Foundation)
3. Structure of an OpenAPI document
4. New features in 3.0 and 3.1, and tools such as Swagger UI
5. Best practices
6. The API-first development method

# Webhooks

1. Use cases: sending notifications, third-party integrations
2. Differences between APIs and webhooks
3. With an API, the client polls for updates, whereas a webhook is pushed by the server when an event occurs (server-initiated)
4. Key components: URL (endpoint), event triggers, payload, HTTP method, response handling
5. Best practices: signature verification, using HTTPS, responding quickly and processing the event asynchronously, handling retries
6. Testing webhooks locally with ngrok
7. Real-world use cases: Stripe payment processing, GitHub, Slack, Discord

# DevOps for Backend Engineers

1. Core concepts: continuous integration, continuous delivery, continuous deployment
2. Best practices: infrastructure as code, version control
3. Tools: containers with Docker, orchestration with Kubernetes, CI/CD pipelines
4. Scaling your service: horizontal vs. vertical scaling
5. Deployment strategies: blue-green, rolling, canary

---

Full credits go to [sriniously](https://www.youtube.com/@sriniously) on youtube and this [video](https://youtu.be/0Rwb4Xmlcwc?si=KdN49uVhuMaEARPj)

_**Version:** 1.0_
_**Last updated:** October 2026_
