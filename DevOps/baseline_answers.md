# Baseline Tech Questions - Answers

## Questions Every Tech Professional Should Know

---

### **Q1: What is the OSI model and can you explain at least 4 layers?**

**Answer:**

The OSI (Open Systems Interconnection) model is a conceptual framework that standardizes the functions of a communication system into seven distinct layers. Each layer serves a specific purpose and communicates with the layers directly above and below it.

**The 7 Layers (from top to bottom):**

1. **Layer 7 - Application Layer**
   - Closest to the end-user
   - Provides network services to applications
   - Protocols: HTTP, HTTPS, FTP, SMTP, DNS
   - Example: Web browsers, email clients

2. **Layer 6 - Presentation Layer**
   - Data translation, encryption, and compression
   - Converts data formats between application and network
   - Handles character encoding, data compression
   - Example: SSL/TLS encryption, JPEG, MPEG

3. **Layer 5 - Session Layer**
   - Manages sessions between applications
   - Establishes, maintains, and terminates connections
   - Handles authentication and reconnection
   - Example: NetBIOS, RPC

4. **Layer 4 - Transport Layer**
   - End-to-end communication and data transfer
   - Protocols: TCP (reliable) and UDP (fast, unreliable)
   - Port numbers and segmentation
   - Example: TCP port 80 for HTTP, UDP port 53 for DNS

5. **Layer 3 - Network Layer**
   - Routing and logical addressing
   - IP addressing and packet forwarding
   - Protocols: IP, ICMP, routing protocols
   - Example: Routers operate at this layer

6. **Layer 2 - Data Link Layer**
   - Physical addressing (MAC addresses)
   - Frame formatting and error detection
   - Protocols: Ethernet, Wi-Fi (802.11)
   - Example: Switches operate at this layer

7. **Layer 1 - Physical Layer**
   - Physical transmission of raw bits
   - Hardware specifications (cables, voltages, frequencies)
   - Example: Ethernet cables, fiber optics, radio frequencies

**Mnemonic to remember:** "All People Seem To Need Data Processing" (Application, Presentation, Session, Transport, Network, Data Link, Physical)

---

### **Q2: Explain the difference between TCP and UDP.**

**Answer:**

TCP (Transmission Control Protocol) and UDP (User Datagram Protocol) are both transport layer protocols, but they have significant differences:

**TCP (Transmission Control Protocol):**
- **Connection-oriented:** Establishes a connection before data transfer (3-way handshake)
- **Reliable:** Guarantees delivery of data in the correct order
- **Error checking:** Has built-in error detection and correction
- **Flow control:** Manages data transmission rate
- **Slower:** Due to overhead of reliability mechanisms
- **Use cases:** Web browsing (HTTP/HTTPS), email (SMTP), file transfer (FTP), SSH
- **Analogy:** Like a phone call - connection must be established first

**UDP (User Datagram Protocol):**
- **Connectionless:** No connection establishment, just sends data
- **Unreliable:** No guarantee of delivery or order
- **No error correction:** Minimal error checking
- **No flow control:** Sends data as fast as possible
- **Faster:** Less overhead, lower latency
- **Use cases:** Video streaming, online gaming, DNS queries, VoIP
- **Analogy:** Like sending a postcard - you send it and hope it arrives

**Comparison Table:**

| Feature | TCP | UDP |
|---------|-----|-----|
| Connection | Connection-oriented | Connectionless |
| Reliability | Reliable | Unreliable |
| Speed | Slower | Faster |
| Ordering | Ordered | No ordering |
| Error Checking | Extensive | Minimal |
| Use Case | When accuracy matters | When speed matters |

---

### **Q3: What is DNS and how does it work?**

**Answer:**

**DNS (Domain Name System)** is like the internet's phone book. It translates human-friendly domain names (like www.google.com) into IP addresses (like 172.217.164.46) that computers use to identify each other on the network.

**How DNS Works:**

1. **User enters a URL:** You type "www.example.com" in your browser

2. **DNS Resolver Query:** Your computer asks a DNS resolver (usually provided by your ISP)

3. **Root Server:** If the resolver doesn't have the answer cached, it queries a root DNS server

4. **TLD Server:** Root server directs to the Top-Level Domain (TLD) server (e.g., .com, .org)

5. **Authoritative Server:** TLD server directs to the authoritative nameserver for the specific domain

6. **IP Address Returned:** The authoritative server returns the IP address

7. **Connection Made:** Your browser connects to the web server at that IP address

**DNS Record Types:**

- **A Record:** Maps domain to IPv4 address
- **AAAA Record:** Maps domain to IPv6 address
- **CNAME Record:** Creates an alias for another domain
- **MX Record:** Specifies mail servers for the domain
- **NS Record:** Specifies authoritative nameservers
- **TXT Record:** Holds text information (often for verification)
- **PTR Record:** Reverse DNS lookup (IP to domain)

**DNS Caching:**
- Results are cached at multiple levels (browser, OS, resolver)
- TTL (Time To Live) determines how long to cache
- Reduces lookup time and DNS server load

**Example DNS Query Flow:**
```
User → DNS Resolver → Root Server → .com TLD Server → 
example.com Nameserver → Returns IP → User connects to website
```

---

### **Q4: What are the different HTTP status codes? Explain 2xx, 3xx, 4xx, 5xx categories.**

**Answer:**

HTTP status codes are three-digit responses from a server indicating the result of a client's request.

**1xx - Informational Responses** (Rarely seen)
- **100 Continue:** Server received request headers, client should send body
- **101 Switching Protocols:** Server is switching protocols as requested

**2xx - Success**
- **200 OK:** Request succeeded, standard response
- **201 Created:** Request succeeded and a new resource was created
- **202 Accepted:** Request accepted but not yet processed
- **204 No Content:** Request succeeded but no content to return
- **206 Partial Content:** Partial GET request (for resumable downloads)

**3xx - Redirection**
- **301 Moved Permanently:** Resource permanently moved to new URL
- **302 Found:** Temporary redirect
- **303 See Other:** Redirect to another URL using GET
- **304 Not Modified:** Cached version is still valid
- **307 Temporary Redirect:** Same as 302 but method must not change
- **308 Permanent Redirect:** Same as 301 but method must not change

**4xx - Client Errors**
- **400 Bad Request:** Malformed request syntax
- **401 Unauthorized:** Authentication required
- **403 Forbidden:** Server refuses to authorize
- **404 Not Found:** Resource doesn't exist
- **405 Method Not Allowed:** HTTP method not supported for this resource
- **408 Request Timeout:** Server timed out waiting for request
- **409 Conflict:** Request conflicts with current state
- **429 Too Many Requests:** Rate limiting triggered

**5xx - Server Errors**
- **500 Internal Server Error:** Generic server error
- **501 Not Implemented:** Server doesn't support the functionality
- **502 Bad Gateway:** Invalid response from upstream server
- **503 Service Unavailable:** Server temporarily unavailable
- **504 Gateway Timeout:** Upstream server didn't respond in time
- **505 HTTP Version Not Supported:** HTTP version not supported

**Quick Reference:**
- **2xx:** "Success - everything worked!"
- **3xx:** "Go somewhere else"
- **4xx:** "You (client) messed up"
- **5xx:** "We (server) messed up"

---

### **Q5: What is the difference between a process and a thread?**

**Answer:**

**Process:**
- An independent program in execution
- Has its own memory space (isolated)
- Contains at least one thread
- Heavy-weight (more resources required)
- Creation is expensive (time and memory)
- Inter-process communication is complex (IPC, pipes, sockets)
- If one process crashes, others are unaffected
- **Example:** Running Chrome, Firefox, and VS Code simultaneously - each is a separate process

**Thread:**
- A lightweight unit of execution within a process
- Shares memory space with other threads in the same process
- Part of a process
- Light-weight (fewer resources)
- Creation is cheaper and faster
- Communication is easier (shared memory)
- If one thread crashes, it can crash the entire process
- **Example:** Chrome browser has multiple tabs - each tab often runs in a separate thread

**Key Differences Table:**

| Aspect | Process | Thread |
|--------|---------|--------|
| Memory | Separate memory space | Shared memory space |
| Communication | Complex (IPC) | Simple (shared memory) |
| Creation Time | Slower | Faster |
| Resource Usage | Heavy | Light |
| Independence | Independent | Dependent on process |
| Crash Impact | Isolated | Can affect whole process |
| Context Switching | Expensive | Cheaper |

**Analogy:**
- **Process:** Like a house - completely separate from other houses
- **Thread:** Like rooms in a house - share the same house (resources)

**Real-world Example:**
```
Google Chrome Process
├── Main Thread (UI)
├── Renderer Thread (Tab 1)
├── Renderer Thread (Tab 2)
├── Network Thread
└── GPU Thread
```

**When to use:**
- **Multiple Processes:** When you need isolation and independence
- **Multiple Threads:** When you need parallel execution with shared data

---

### **Q6: Explain what an API is and give an example of REST API.**

**Answer:**

**API (Application Programming Interface):**

An API is a set of rules and protocols that allows different software applications to communicate with each other. It defines the methods and data structures that developers can use to interact with a service or library.

**Simple Analogy:**
Think of a restaurant:
- **Menu:** The API (shows what you can order)
- **Waiter:** The API endpoint (takes your order)
- **Kitchen:** The backend system (prepares your order)
- **Your meal:** The response (what you get back)

**REST API (Representational State Transfer):**

REST is an architectural style for designing networked applications. It uses HTTP methods and is stateless.

**REST API Principles:**
1. **Client-Server Architecture:** Separation of concerns
2. **Stateless:** Each request contains all necessary information
3. **Cacheable:** Responses can be cached
4. **Uniform Interface:** Consistent way to interact
5. **Layered System:** Client doesn't know if connected directly to server

**HTTP Methods in REST:**
- **GET:** Retrieve data (Read)
- **POST:** Create new data (Create)
- **PUT:** Update existing data (Update/Replace)
- **PATCH:** Partial update (Modify)
- **DELETE:** Remove data (Delete)

**Example REST API - User Management System:**

**Base URL:** `https://api.example.com/v1`

**1. Get all users:**
```
GET /users
Response: 200 OK
[
  {"id": 1, "name": "John Doe", "email": "john@example.com"},
  {"id": 2, "name": "Jane Smith", "email": "jane@example.com"}
]
```

**2. Get specific user:**
```
GET /users/1
Response: 200 OK
{"id": 1, "name": "John Doe", "email": "john@example.com"}
```

**3. Create new user:**
```
POST /users
Body: {"name": "Bob Wilson", "email": "bob@example.com"}
Response: 201 Created
{"id": 3, "name": "Bob Wilson", "email": "bob@example.com"}
```

**4. Update user:**
```
PUT /users/1
Body: {"name": "John Updated", "email": "john.new@example.com"}
Response: 200 OK
{"id": 1, "name": "John Updated", "email": "john.new@example.com"}
```

**5. Delete user:**
```
DELETE /users/1
Response: 204 No Content
```

**Real-world REST API Examples:**
- **Twitter API:** Get tweets, post tweets, follow users
- **GitHub API:** Access repositories, create issues, manage pull requests
- **Google Maps API:** Get location data, directions, places
- **Payment APIs (Stripe, PayPal):** Process payments, manage subscriptions

**REST API Response Format (JSON):**
```json
{
  "status": "success",
  "data": {
    "user": {
      "id": 123,
      "name": "John Doe"
    }
  },
  "message": "User retrieved successfully"
}
```

---

### **Q7: What is Git and why is it used?**

**Answer:**

**Git** is a distributed version control system (VCS) used to track changes in source code during software development.

**Why Git is Used:**

1. **Version Control:**
   - Track every change made to files
   - See who made changes and when
   - Revert to previous versions if needed

2. **Collaboration:**
   - Multiple developers can work simultaneously
   - Merge changes from different contributors
   - Manage conflicts when changes overlap

3. **Branching:**
   - Create separate branches for features
   - Experiment without affecting main code
   - Merge back when ready

4. **Distributed:**
   - Every developer has full history
   - Can work offline
   - No single point of failure

5. **Backup:**
   - Code is stored in multiple locations
   - Remote repositories (GitHub, GitLab, Bitbucket)

**Key Git Concepts:**

**Repository (Repo):**
- Storage location for your project
- Contains all files and version history

**Commit:**
- Snapshot of your changes
- Includes message describing what changed

**Branch:**
- Separate line of development
- Default branch: main/master

**Common Git Commands:**

```bash
# Initialize a new repository
git init

# Clone an existing repository
git clone https://git.example.com/user/repo.git

# Check status of files
git status

# Add files to staging area
git add filename.txt
git add .  # Add all files

# Commit changes
git commit -m "Add new feature"

# View commit history
git log

# Create and switch to new branch
git checkout -b feature-branch

# Switch to existing branch
git checkout main

# Merge branch into current branch
git merge feature-branch

# Push changes to remote repository
git push origin main

# Pull changes from remote repository
git pull origin main

# View remote repositories
git remote -v
```

**Git Workflow Example:**

```
1. Clone repository → git clone <url>
2. Create feature branch → git checkout -b new-feature
3. Make changes → edit files
4. Stage changes → git add .
5. Commit changes → git commit -m "Add feature"
6. Push to remote → git push origin new-feature
7. Create Pull Request → on GitHub/GitLab
8. Review and merge → team reviews and merges
```

**Benefits:**
- **History tracking:** Complete audit trail
- **Parallel development:** Multiple features simultaneously
- **Code review:** Pull requests enable review before merge
- **Rollback capability:** Undo mistakes easily
- **Industry standard:** Used by millions of developers

---

### **Q8: What is the difference between SQL and NoSQL databases? Give examples.**

**Answer:**

**SQL (Relational Databases):**

**Characteristics:**
- Structured data with predefined schema
- Data stored in tables with rows and columns
- Uses SQL (Structured Query Language)
- ACID properties (Atomicity, Consistency, Isolation, Durability)
- Vertical scaling (scale up - more powerful server)
- Relationships between tables (foreign keys)

**When to Use SQL:**
- Complex queries and transactions
- Data integrity is critical
- Structured data with clear relationships
- Need for ACID compliance
- Examples: Banking, E-commerce, ERP systems

**Popular SQL Databases:**
- **MySQL:** Open-source, widely used for web applications
- **PostgreSQL:** Advanced features, supports JSON
- **Oracle:** Enterprise-level, feature-rich
- **Microsoft SQL Server:** Windows-integrated
- **SQLite:** Lightweight, embedded database

**Example SQL Structure:**
```sql
-- Users table
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100)
);

-- Orders table with foreign key
CREATE TABLE orders (
    id INT PRIMARY KEY,
    user_id INT,
    amount DECIMAL(10,2),
    FOREIGN KEY (user_id) REFERENCES users(id)
);

-- Query with JOIN
SELECT users.name, orders.amount
FROM users
JOIN orders ON users.id = orders.user_id;
```

---

**NoSQL (Non-Relational Databases):**

**Characteristics:**
- Flexible schema or schema-less
- Various data models (document, key-value, graph, column-family)
- Horizontal scaling (scale out - add more servers)
- Eventually consistent (BASE properties)
- Optimized for specific use cases

**Types of NoSQL Databases:**

1. **Document Stores:**
   - Store data as documents (usually JSON)
   - Examples: MongoDB, CouchDB
   - Use case: Content management, user profiles

2. **Key-Value Stores:**
   - Simple key-value pairs
   - Examples: Redis, DynamoDB
   - Use case: Caching, session management

3. **Column-Family Stores:**
   - Data stored in columns instead of rows
   - Examples: Cassandra, HBase
   - Use case: Time-series data, analytics

4. **Graph Databases:**
   - Store relationships between entities
   - Examples: Neo4j, ArangoDB
   - Use case: Social networks, recommendation engines

**When to Use NoSQL:**
- Massive amounts of data
- Rapid development with changing requirements
- Horizontal scaling needed
- Unstructured or semi-structured data
- High-speed read/write operations

**Example NoSQL Document (MongoDB):**
```json
{
  "_id": "123",
  "name": "John Doe",
  "email": "john@example.com",
  "orders": [
    {
      "order_id": "001",
      "amount": 99.99,
      "date": "2026-01-15"
    },
    {
      "order_id": "002",
      "amount": 149.99,
      "date": "2026-01-20"
    }
  ],
  "address": {
    "street": "123 Main St",
    "city": "New York"
  }
}
```

**Comparison Table:**

| Feature | SQL | NoSQL |
|---------|-----|-------|
| Schema | Fixed schema | Flexible/Dynamic schema |
| Scaling | Vertical (scale up) | Horizontal (scale out) |
| Data Model | Tables with rows/columns | Various (document, key-value, etc.) |
| Transactions | ACID compliant | Eventually consistent (BASE) |
| Query Language | SQL | Database-specific |
| Best For | Complex queries, relationships | Large-scale, high-speed, flexibility |
| Examples | MySQL, PostgreSQL | MongoDB, Redis, Cassandra |

**Real-World Examples:**

**SQL Use Cases:**
- Banking system (transactions, accounts)
- E-commerce (orders, inventory)
- HR management system

**NoSQL Use Cases:**
- Facebook (social graph) - Graph database
- Twitter (tweets, feeds) - Document store
- Amazon cart (session data) - Key-value store
- Netflix (recommendations) - Multiple NoSQL types

**Hybrid Approach:**
Many modern applications use both:
- SQL for transactional data
- NoSQL for caching, analytics, or specific features

---

### **Q9: What is cloud computing? Name the three main service models.**

**Answer:**

**Cloud Computing:**

Cloud computing is the delivery of computing services (servers, storage, databases, networking, software, analytics) over the internet ("the cloud") on a pay-as-you-go basis.

**Key Characteristics:**
1. **On-demand self-service:** Provision resources without human interaction
2. **Broad network access:** Available over network from any device
3. **Resource pooling:** Multi-tenant model, resources shared
4. **Rapid elasticity:** Scale up/down quickly
5. **Measured service:** Pay only for what you use

**Benefits:**
- **Cost savings:** No upfront hardware investment
- **Scalability:** Easily scale resources up or down
- **Reliability:** Built-in redundancy and backups
- **Speed:** Deploy quickly, faster time to market
- **Global reach:** Deploy in multiple regions worldwide
- **Automatic updates:** Provider manages maintenance

---

**Three Main Service Models:**

**1. IaaS - Infrastructure as a Service**

**What it is:**
- Provides virtualized computing resources over the internet
- Rent servers, storage, networking
- Most control and flexibility

**What you manage:**
- Operating system
- Applications
- Data
- Runtime
- Middleware

**What provider manages:**
- Virtualization
- Servers
- Storage
- Networking

**Examples:**
- **Amazon EC2 (AWS):** Virtual servers
- **Google Compute Engine (GCP):** VM instances
- **Azure Virtual Machines:** Windows/Linux VMs
- **DigitalOcean Droplets:** Simple cloud servers

**Use Cases:**
- Website hosting
- Development and testing environments
- Data storage and backup
- High-performance computing

**Analogy:** Renting an empty apartment - you bring your own furniture and decorations

---

**2. PaaS - Platform as a Service**

**What it is:**
- Provides platform for developing, running, and managing applications
- No need to manage underlying infrastructure
- Focus on code, not servers

**What you manage:**
- Applications
- Data

**What provider manages:**
- Operating system
- Runtime
- Middleware
- Virtualization
- Servers
- Storage
- Networking

**Examples:**
- **Heroku:** Easy app deployment
- **Google App Engine:** Scalable web applications
- **AWS Elastic Beanstalk:** Application deployment
- **Azure App Service:** Web apps and APIs
- **Cloud Foundry:** Enterprise PaaS

**Use Cases:**
- Web application development
- API development and hosting
- Business analytics
- Database services

**Analogy:** Renting a furnished apartment - furniture provided, you just move in

---

**3. SaaS - Software as a Service**

**What it is:**
- Complete software applications delivered over the internet
- No installation or management needed
- Access via web browser

**What you manage:**
- Your data and access settings

**What provider manages:**
- Everything (entire stack)

**Examples:**
- **Gmail:** Email service
- **Microsoft 365:** Office applications
- **Salesforce:** CRM software
- **Slack:** Team communication
- **Dropbox:** File storage
- **Zoom:** Video conferencing
- **Netflix:** Video streaming

**Use Cases:**
- Email and collaboration
- Customer relationship management (CRM)
- Enterprise resource planning (ERP)
- Human resources management

**Analogy:** Staying at a hotel - everything is provided and managed for you

---

**Comparison Table:**

| Aspect | IaaS | PaaS | SaaS |
|--------|------|------|------|
| Control | High | Medium | Low |
| Flexibility | Most flexible | Moderate | Least flexible |
| Management | You manage more | Balanced | Provider manages all |
| Use Case | Infrastructure needs | App development | Ready-to-use software |
| Technical Knowledge | High | Medium | Low |
| Example | AWS EC2 | Heroku | Gmail |

**Visual Representation:**

```
YOU MANAGE:
IaaS:  [Applications][Data][Runtime][Middleware][OS] | [Virtual][Storage][Network]
PaaS:  [Applications][Data] | [Runtime][Middleware][OS][Virtual][Storage][Network]
SaaS:  | [Applications][Data][Runtime][Middleware][OS][Virtual][Storage][Network]

PROVIDER MANAGES: Everything after the |
```

**Other Service Models:**

- **FaaS (Function as a Service):** Serverless computing (AWS Lambda)
- **DBaaS (Database as a Service):** Managed databases (AWS RDS)
- **DaaS (Desktop as a Service):** Virtual desktops

**Major Cloud Providers:**
1. **Amazon Web Services (AWS)** - Market leader, most services
2. **Microsoft Azure** - Strong enterprise integration
3. **Google Cloud Platform (GCP)** - Data analytics and ML
4. **IBM Cloud** - Enterprise and hybrid solutions
5. **Oracle Cloud** - Database and enterprise apps

---

### **Q10: Explain what load balancing is and why it's important.**

**Answer:**

**Load Balancing:**

Load balancing is the process of distributing network traffic across multiple servers to ensure no single server bears too much load. It acts as a "traffic cop" sitting in front of your servers.

**How It Works:**

```
                    [Load Balancer]
                          |
        +-----------------+-----------------+
        |                 |                 |
    [Server 1]        [Server 2]        [Server 3]
```

When a user request comes in, the load balancer decides which server should handle it based on various algorithms.

---

**Why Load Balancing is Important:**

**1. High Availability:**
- If one server fails, traffic is redirected to healthy servers
- Users experience no downtime
- System remains accessible even during failures

**2. Scalability:**
- Easily add or remove servers based on demand
- Handle traffic spikes (Black Friday, viral events)
- Scale horizontally by adding more servers

**3. Performance:**
- Distribute workload evenly
- Prevent server overload
- Reduce response time
- Better user experience

**4. Redundancy:**
- No single point of failure
- Built-in failover mechanism
- Automatic recovery

**5. Flexibility:**
- Perform maintenance without downtime
- Update servers one at a time
- Deploy new versions gradually (canary deployments)

---

**Load Balancing Algorithms:**

**1. Round Robin:**
- Distributes requests sequentially
- Server 1 → Server 2 → Server 3 → Server 1 (repeat)
- Simple and fair distribution
- Doesn't consider server load

**2. Least Connections:**
- Sends requests to server with fewest active connections
- Good for long-running connections
- Better for varying request durations

**3. Least Response Time:**
- Routes to server with fastest response time
- Considers both connections and response time
- Optimizes for performance

**4. IP Hash:**
- Uses client's IP address to determine server
- Same client always goes to same server
- Good for session persistence

**5. Weighted Round Robin:**
- Assigns weight to each server based on capacity
- More powerful servers get more requests
- Example: Server 1 (weight 3), Server 2 (weight 1)

**6. Random:**
- Randomly selects a server
- Simple but effective for evenly capable servers

---

**Types of Load Balancers:**

**1. Hardware Load Balancers:**
- Physical devices
- High performance and reliability
- Expensive
- Examples: F5 BIG-IP, Citrix ADC

**2. Software Load Balancers:**
- Software applications
- More flexible and cost-effective
- Examples: Nginx, HAProxy, Apache mod_proxy

**3. Cloud Load Balancers:**
- Managed services from cloud providers
- Highly scalable
- Pay-as-you-go
- Examples: AWS ELB/ALB, Azure Load Balancer, GCP Load Balancer

---

**Load Balancing Layers:**

**Layer 4 (Transport Layer) - Network Load Balancer:**
- Works at TCP/UDP level
- Faster, less overhead
- Routes based on IP and port
- Cannot inspect packet content
- Use case: High-performance, simple routing

**Layer 7 (Application Layer) - Application Load Balancer:**
- Works at HTTP/HTTPS level
- Can inspect packet content
- Routes based on URLs, headers, cookies
- More intelligent routing decisions
- Use case: Complex routing, microservices

---

**Real-World Examples:**

**1. E-commerce Website:**
```
User Request → Load Balancer
    ├── Web Server 1 (handles checkout)
    ├── Web Server 2 (handles product browsing)
    └── Web Server 3 (handles user authentication)
```

**2. Global Application:**
```
Users in US → US Load Balancer → US Servers
Users in EU → EU Load Balancer → EU Servers
Users in Asia → Asia Load Balancer → Asia Servers
```

**3. Microservices Architecture:**
```
API Gateway → Load Balancer
    ├── User Service (3 instances)
    ├── Order Service (5 instances)
    └── Payment Service (2 instances)
```

---

**Load Balancer Features:**

**Health Checks:**
- Regularly ping servers to check if they're healthy
- Remove unhealthy servers from rotation
- Automatically add them back when healthy

**SSL Termination:**
- Load balancer handles SSL/TLS encryption
- Reduces load on backend servers
- Centralized certificate management

**Session Persistence (Sticky Sessions):**
- Ensures user stays on same server for session
- Important for stateful applications
- Can be done via cookies or IP

**DDoS Protection:**
- Detect and mitigate attacks
- Rate limiting
- Traffic filtering

---

**Popular Load Balancing Solutions:**

**1. Nginx:**
- High-performance web server and load balancer
- Open-source
- Low resource consumption

**2. HAProxy:**
- Reliable, high-performance
- TCP and HTTP load balancing
- Widely used

**3. AWS Elastic Load Balancer (ELB):**
- Application Load Balancer (ALB) - Layer 7
- Network Load Balancer (NLB) - Layer 4
- Classic Load Balancer - Legacy

**4. Traefik:**
- Modern cloud-native load balancer
- Great for Docker and Kubernetes
- Automatic service discovery

---

**Load Balancing Scenarios:**

**Without Load Balancer:**
```
Single Server: 1000 requests/sec capacity
Traffic spike: 5000 requests/sec
Result: Server crashes, website down
```

**With Load Balancer:**
```
5 Servers: Each 1000 requests/sec = 5000 total capacity
Traffic spike: 5000 requests/sec
Result: Load balanced across all servers, website stable
```

**Benefits Summary:**
- ✅ No downtime during server failures
- ✅ Handle traffic spikes
- ✅ Improve response times
- ✅ Enable rolling updates
- ✅ Scale horizontally
- ✅ Better resource utilization
- ✅ Enhanced security

Load balancing is essential for any modern production application that needs to handle significant traffic and maintain high availability.

---

**End of Baseline Tech Questions Answers**
