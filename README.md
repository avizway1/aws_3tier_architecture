## Three-Tier Web Architecture: Step-by-Step Process

# Tier Architecture explanation

- **Web Server**: This is the front part of a web application that displays the website to users. It handles how the site looks and interacts with users, like showing product pages or allowing people to sign up.

- **Application Server**: This middle part manages the business logic and processes user actions. For example, it checks the inventory to see if a product is in stock or updates a user's account details.

- **Database Server**: This is the back-end part that stores all the data for the application. It uses databases like MySQL or PostgreSQL to keep track of information.

Looking for more videos on AWS and DevOps follow me on youtube here [![YouTube Channel](https://img.shields.io/badge/YouTube-Channel-red?logo=youtube&style=flat-square)](https://www.youtube.com/@avizway)



![Three-Tier Architecture](https://avinash.s3.amazonaws.com/3tier.png)


### Overview
In this architecture, we have three main layers:
1. **Web Tier**: Handles client requests and serves the front-end website.
2. **Application Tier**: Processes API requests and handles the business logic.
3. **Database Tier**: Manages data storage and retrieval.

### Components explanation

#### 1. External Load Balancer (Public-Facing Application Load Balancer)
- **Role**: This acts as the entry point for all client traffic.
- **Functionality**:
  - Distributes incoming client requests to the web tier EC2 instances.
  - Ensures even distribution of traffic for better performance and reliability.
  - Performs health checks to ensure only healthy instances receive traffic.

#### 2. Web Tier
- **Role**: Serves the front-end of the application and redirects API calls.
- **Components**:
  - **Nginx Webservers**: Running on EC2 instances.
  - **React.js Website**: The front-end application served by Nginx.
- **Functionality**:
  - **Serving the Website**: Nginx serves the static files for the React.js application to the clients.
  - **Redirecting API Calls**: Nginx is configured to route API requests to the internal-facing load balancer of the application tier.

#### 3. Internal Load Balancer (Application Tier Load Balancer)
- **Role**: Manages traffic between the web tier and the application tier.
- **Functionality**:
  - Receives API requests from the web tier.
  - Distributes these requests to the appropriate EC2 instances in the application tier.
  - Ensures high availability and load balancing within the application tier.

#### 4. Application Tier
- **Role**: Handles the application logic and processes API requests.
- **Components**:
  - **Node.js Application**: Running on EC2 instances.
- **Functionality**:
  - **Processing Requests**: The Node.js application receives API requests, performs necessary computations or data manipulations.
  - **Database Interaction**: Interacts with the Aurora MySQL database to fetch or update data.
  - **Returning Responses**: Sends the processed data back to the web tier via the internal load balancer.

#### 5. Database Tier (Aurora MySQL Multi-AZ Database)
- **Role**: Provides reliable and scalable data storage.
- **Functionality**:
  - **Data Storage**: Stores all the application data in a structured format.
  - **Multi-AZ Setup**: Ensures high availability and fault tolerance by replicating data across multiple availability zones.
  - **Data Retrieval and Manipulation**: Handles queries and transactions from the application tier to manage the data.

### Additional Components

#### Load Balancing
- **Purpose**: Distributes incoming traffic evenly across multiple instances to prevent any single instance from becoming a bottleneck.
- **Implementation**:
  - **Web Tier**: The external load balancer distributes traffic to web servers.
  - **Application Tier**: The internal load balancer distributes API requests to application servers.

#### Health Checks
- **Purpose**: Continuously monitors the health of instances to ensure only healthy instances receive traffic.
- **Implementation**:
  - **Web Tier**: Health checks by the external load balancer to ensure web servers are responsive.
  - **Application Tier**: Health checks by the internal load balancer to ensure application servers are operational.

#### Auto Scaling Groups
- **Purpose**: Automatically adjusts the number of running instances based on traffic load to maintain performance and cost efficiency.
- **Implementation**:
  - **Web Tier**: Auto-scaling based on metrics like CPU usage or request count to add or remove web server instances.
  - **Application Tier**: Auto-scaling based on similar metrics to adjust the number of application server instances.

#### AWS Certificate Manager (ACM)
- **Purpose**: Manages SSL/TLS certificates to secure data in transit between clients and your application, ensuring encrypted communication.
- **Implementation**:
  - **Certificate Provisioning**: ACM provides and manages SSL/TLS certificates for your domain `learnaws.co.in`.
  - **Certificate Deployment**: The ACM certificates are associated with the public-facing Application Load Balancer (ALB) to enable HTTPS traffic.
  - **Automatic Renewal**: ACM automatically renews certificates before they expire, ensuring uninterrupted secure connections.

#### Amazon Route 53
- **Purpose**: Manages DNS records and directs user traffic to the appropriate AWS resources, optimizing for performance and reliability.
- **Implementation**:
  - **DNS Management**: Route 53 handles DNS queries for the domain `learnaws.co.in`, translating it into IP addresses for your Application Load Balancer.
  - **Traffic Routing**: Route 53 directs client requests to the public-facing Application Load Balancer based on DNS records.
  - **Health Checks and Failover**: Optionally, Route 53 performs health checks on your endpoints and can automatically reroute traffic to healthy resources if needed.


### Summary
This architecture ensures high availability, scalability, and reliability by distributing the load, monitoring instance health, and scaling resources dynamically. The web tier serves the front-end and routes API calls, the application tier handles business logic and interacts with the database, and the database tier provides robust data storage and retrieval.
