# Video 3 — What is a Backend?
## 🎥 Video
https://www.youtube.com/watch?v=6Ss4dJD9Kzg&list=PLui3EUkuMTPgZcV0QhQrOcwMPcBCcd_Q1&index=3

---

# 1. What is a Backend?

Before understanding backend development, we first need to understand what a **backend actually is**.

## Traditional Definition

In a traditional sense, a backend is a **computer or server that listens for incoming requests**.

Those requests can use different communication protocols, such as:

- HTTP
- HTTPS
- WebSocket
- gRPC

The backend listens for these requests on specific **network ports**.

For example:

- Port `80` → commonly used for HTTP
- Port `443` → commonly used for HTTPS

A client, frontend application, or another server can connect to the backend and send a request.

The backend then processes that request and sends a response.

## Basic Backend Flow
``` text
Client / Browser
        │
        │ Request
        ▼
Backend Server
        │
        │ Processes the request
        ▼
Response
        │
        ▼
Client / Browser
```

## Why is it Called a Server?

It is called a server because it provides or "serves" something to another computer, usually called a client.

A backend server can serve different types of content.

For example:

Static files

A server can send:

HTML files
CSS files
JavaScript files
Images
Fonts
Data

A backend can send data, commonly in formats such as:
{
  "name": "Sarthak",
  "role": "User"
}


This is commonly sent as JSON.

Processed results

A backend can also perform some processing and send the result back.

For example:
```text
Client asks:
"Calculate my total order price"
        │
        ▼
Backend calculates:
Product price
+ Tax
+ Delivery charge
        │
        ▼
Backend sends:
Final total
```

So, at a basic level, a backend can:

Receive requests
Receive data
Process the request
Perform business logic
Fetch or store data
Communicate with other services
Send a response

Simple Definition

A backend is a server-side system that receives requests, processes them, works with data and other services, and sends responses back to clients.

However, this definition alone does not give us the complete picture.

The more important question is:

Where is this backend physically running, and how does a request from a user's browser actually reach it?

To understand that, we need to follow the complete journey of a request.

---

# 2. How Does a Request Reach the Backend?

Now that we know what a backend is, the next question is:

> **When someone makes a request from their browser, how does that request actually travel through the internet and reach our backend server?**

To understand this, let's use an example where our application is deployed on an **AWS EC2 instance**.

The high-level flow is:

```text
User's Browser
      ↓
DNS
      ↓
EC2 Public IP
      ↓
AWS Security Group / Firewall
      ↓
EC2 Virtual Machine
      ↓
Nginx (Reverse Proxy)
      ↓
Node.js Server
```

Let's understand each step.

---

## Step 1: The User Types a Domain Name

Suppose someone opens their browser and types:

```text
mywebsite.com
```

At this point, the browser needs to know:

> **Where is `mywebsite.com` actually located?**

Computers communicate over networks using **IP addresses**, not human-friendly domain names.

For example:

```text
mywebsite.com
        ↓
54.123.45.67
```

So the browser needs to find the IP address associated with the domain name.

This is where **DNS** comes in.

---

## Step 2: DNS Converts the Domain Name into an IP Address

DNS stands for **Domain Name System**.

A simple way to think about DNS is as an internet-wide system that helps map:

```text
Domain Name
     ↓
IP Address
```

For example:

```text
mywebsite.com
        ↓
DNS
        ↓
54.123.45.67
```

The browser asks:

> "What IP address should I connect to for `mywebsite.com`?"

DNS returns the appropriate IP address.

After receiving the IP address, the browser now knows which machine it should send the request to.

---

## Important Question: Is the IP Returned by DNS the Same as the EC2 Public IP?

### In this example, yes.

Suppose AWS gives our EC2 instance this public IP:

```text
54.123.45.67
```

We can configure our DNS using an **A record**:

```text
mywebsite.com → 54.123.45.67
```

So the complete process is:

```text
User types:
mywebsite.com
        ↓
Browser asks DNS:
"What is the IP address?"
        ↓
DNS returns:
54.123.45.67
        ↓
Browser sends the request to:
54.123.45.67
```

That IP address is the **public IP associated with our EC2 virtual machine**.

---

## DNS Records Mentioned in the Video

DNS has different types of records.

Two basic ones mentioned in the video are:

### A Record

An A record points a domain or subdomain to an IP address.

Example:

```text
backend.mywebsite.com
            ↓
      A Record
            ↓
      54.123.45.67
```

### CNAME Record

A CNAME record points one domain or subdomain to another domain or subdomain.

Example:

```text
api.mywebsite.com
        ↓
   CNAME Record
        ↓
backend.mywebsite.com
```

For this video, the important concept is simply:

> **DNS helps route a domain name to the correct IP address.**

---

# Step 3: The Request Reaches the EC2 Public IP

Once the browser receives the IP address, it sends the request through the internet to that address.

For example:

```text
User's Browser
      ↓
Request to 54.123.45.67
      ↓
AWS EC2 Instance
```

An **EC2 instance is a virtual machine running inside AWS infrastructure**.

You can think of it as a computer that you rent and run in an AWS data center.

That virtual machine can run:

- Node.js
- Nginx
- Frontend servers
- Backend servers
- Other applications and services

### Whose machine is the EC2 instance?

It is infrastructure that **we control**, but it is physically hosted inside AWS infrastructure.

For example:

```text
Other Person's Device
        │
        │ Request over the Internet
        ▼
Your AWS Infrastructure
        │
        ▼
Your EC2 Virtual Machine
```

The person visiting your website does **not** connect directly to your personal laptop when the application is deployed.

Instead, they connect to the publicly reachable server where you deployed your application.

---

# Public IP vs Private IP

An EC2 instance can have different types of IP addresses.

```text
EC2 Instance
│
├── Public IP
│       │
│       └── Used for communication over the Internet
│
└── Private IP
        │
        └── Used for communication inside a private network
```

For our website example:

```text
User's Browser
       ↓
Internet
       ↓
EC2 Public IP
```

So when DNS returns an IP address that allows users on the internet to reach our EC2 instance, it is referring to the **publicly reachable IP**.

---

# Step 4: The Request Reaches the AWS Firewall

The request has now reached our AWS infrastructure.

However, that does not automatically mean it can enter the EC2 instance.

Before allowing traffic through, AWS can check firewall rules using a **Security Group**.

A Security Group controls what network traffic is allowed.

For example:

```text
Incoming Request
       ↓
AWS Security Group
       ↓
"Is this type of traffic allowed?"
       │
       ├── Yes → Allow
       │
       └── No  → Block
```

---

## What Does the Security Group Check?

One important thing is the **port** being used.

Common examples are:

```text
HTTP  → Port 80
HTTPS → Port 443
SSH   → Port 22
```

Suppose someone visits:

```text
https://mywebsite.com
```

The request is typically using HTTPS.

The relevant port is commonly:

```text
443
```

The Security Group checks whether traffic to that port is allowed.

```text
HTTPS Request
      ↓
Port 443
      ↓
Security Group checks:
"Is port 443 allowed?"
      │
      ├── Yes → Request continues
      │
      └── No  → Request is blocked
```

### Important Correction to My Earlier Understanding

The firewall is **not checking whether the virtual machine itself has an "allowed port" in some abstract sense**.

More precisely:

> **The Security Group contains rules that determine which incoming traffic is allowed to reach the EC2 instance.**

For example:

```text
Internet
    ↓
HTTPS request on port 443
    ↓
Security Group rule allows port 443
    ↓
Request can reach EC2
```

If port `443` is not allowed:

```text
Internet
    ↓
HTTPS request
    ↓
Security Group
    ↓
BLOCKED ❌
```

---

# Step 5: The Request Enters the EC2 Instance

Once the request passes through the Security Group, it reaches our EC2 virtual machine.

Inside that EC2 instance, we can have multiple services running.

For example:

```text
EC2 Virtual Machine
│
├── Nginx
│
├── Frontend Server
│   └── localhost:3000
│
└── Backend Server
    └── localhost:3001
```

Now another question appears:

> **The request has reached the machine, but how does it know which application should receive it?**

This is where **Nginx** comes in.

---

# Step 6: Nginx Receives the Request

Nginx can act as a **reverse proxy**.

A reverse proxy is a server that sits in front of other servers and forwards requests to the appropriate internal server.

A simplified flow is:

```text
Internet
    ↓
Nginx
    ↓
Chooses the appropriate internal server
    ↓
Frontend or Backend
```

For example:

```text
User Request
      ↓
Nginx
      │
      ├── Frontend request → localhost:3000
      │
      └── Backend request  → localhost:3001
```

Nginx provides a centralized place to manage routing and server configuration.

Instead of exposing every internal application directly to the internet, we can expose Nginx and let it forward requests internally.

---

## HTTP to HTTPS

In the video, Nginx is also configured to listen on HTTP and redirect traffic to HTTPS.

Conceptually:

```text
HTTP Request
Port 80
    ↓
Nginx
    ↓
Redirect
    ↓
HTTPS Request
Port 443
```

HTTPS uses SSL/TLS certificates to provide encrypted communication.

The video mentions using Certbot to help manage SSL certificates.

---

# Step 7: Nginx Forwards the Request to the Node.js Server

Suppose our backend is running on:

```text
localhost:3001
```

Nginx can receive a request from the internet and forward it internally:

```text
User's Browser
       ↓
Internet
       ↓
EC2 Public IP
       ↓
Nginx
       ↓
localhost:3001
       ↓
Node.js Backend
```

This is the final hop before the request reaches our backend application.

---

# What Does `localhost:3001` Actually Mean?

This is an important concept.

`localhost` means:

> **The current computer or machine that is making the connection.**

In this case, Nginx and the Node.js backend are both running on the EC2 instance.

Therefore, from the EC2 instance's perspective:

```text
localhost:3001
```

means:

```text
"The server running on port 3001 on this same EC2 machine."
```

So:

```text
EC2 Virtual Machine
│
├── Nginx
│
└── Node.js Server running on port 3001
```

Nginx does not need to send the request back through the internet.

It can forward it internally:

```text
Nginx
   ↓
localhost:3001
   ↓
Node.js Backend
```

---

# The Complete Backend Request Flow

We can now combine everything.

```text
OTHER PERSON'S DEVICE
│
│ User types:
│ mywebsite.com
│
▼
Browser
│
│ "What IP belongs to mywebsite.com?"
▼
DNS
│
│ Returns the IP address
▼
YOUR EC2 PUBLIC IP
│
▼
AWS SECURITY GROUP / FIREWALL
│
│ Is the incoming traffic allowed?
│
├── No → Blocked ❌
│
└── Yes
      │
      ▼
YOUR EC2 VIRTUAL MACHINE
      │
      ▼
NGINX
      │
      │ Forwards the request
      ▼
localhost:3001
      │
      ▼
YOUR NODE.JS BACKEND
```

At this point, the request has finally reached the backend application.

The backend can now process the request, work with databases or other services, and send a response back.

---


# 3. Why Do We Need Backends?

Now that we understand how a request physically reaches a backend server, the next question is:

> **Why do we need a backend in the first place? Why can't everything just happen in the frontend?**

Let's understand this using the example from the video.

---

## Example: Liking an Instagram Post

Imagine you are scrolling through your Instagram feed and see a post from your friend.

You click the **Like** button.

Later, your friend receives a notification saying that you liked their post.

From the user's point of view, the flow looks simple:

```text
You click Like
      ↓
Your friend gets a notification
```

But many things need to happen between these two events.

A simplified flow looks like this:

```text
You click the Like button
        ↓
Frontend sends a request to the backend
        ↓
Backend identifies who you are
        ↓
Backend saves the like
        ↓
Backend identifies the owner of the post
        ↓
Backend triggers a notification
        ↓
Your friend receives the notification
```

So the actual flow is more like:

```text
YOUR DEVICE
│
│ Click "Like"
▼
Frontend running in your browser/app
│
│ Request: "User liked this post"
▼
BACKEND SERVER
│
├── Identifies the user
│
├── Identifies the post
│
├── Saves the like in the database
│
└── Triggers a notification
        │
        ▼
Friend receives notification
```

---

## What Does the Backend Need to Know?

Suppose you click the Like button.

The backend needs to process information such as:

```text
Who clicked Like?
        ↓
Which post was liked?
        ↓
Who owns that post?
        ↓
Should the like be saved?
        ↓
Should a notification be sent?
```

The backend therefore needs access to the application's shared data and state.

For example:

```text
Users
Posts
Likes
Comments
Followers
Notifications
```

This information needs to be managed centrally.

---

# The Key Idea: Data

The main responsibility of a backend can be simplified into one major concept:

> **Data**

A backend is often responsible for:

1. Receiving data
2. Fetching data
3. Processing data
4. Persisting data
5. Updating data
6. Sending data to clients
7. Triggering actions based on data

For example:

```text
Frontend
    ↓
Sends:
"User wants to like Post 123"
    ↓
Backend
    ↓
Processes the action
    ↓
Database
    ↓
Stores:
User X liked Post 123
```

The word **persisting** means saving data so that it continues to exist even after the current request or program execution is finished.

For example, if you like a post:

```text
Click Like
   ↓
Backend receives action
   ↓
Database stores the like
   ↓
You close the app
   ↓
The like still exists
```

That is persistence.

---

# Why Does This Information Need to Be Centralized?

Every user has their own device and their own frontend running locally.

For example:

```text
User A's Phone
User B's Laptop
User C's Desktop
User D's Tablet
```

Each user only has access to their own application session and device.

But an application such as Instagram needs a central system that can understand the relationship between all users.

For example:

```text
User A
   │
   ├── Follows User B
   │
   ├── Likes User C's post
   │
   └── Sends a message to User D
```

Some centralized system needs to maintain this shared information.

That is one of the major responsibilities of the backend and database infrastructure.

```text
Many Users
    │
    │ Requests
    ▼
Centralized Backend
    │
    ▼
Shared Application Data
```

---

# Frontend vs Backend in the Like Example

Let's separate their responsibilities.

## Frontend

The frontend handles the interaction:

```text
User sees the post
        ↓
User clicks Like
        ↓
Frontend responds to the interaction
        ↓
Frontend sends a request
```

For example:

```text
Click Like
    ↓
Frontend JavaScript runs
    ↓
Sends request to backend
```

## Backend

The backend handles the server-side processing:

```text
Request arrives
      ↓
Who is this user?
      ↓
Which post are they liking?
      ↓
Validate the request
      ↓
Save the like
      ↓
Find the post owner
      ↓
Trigger notification
      ↓
Send response
```

So we can think of it like this:

```text
FRONTEND
Handles user interaction
        ↓
Sends request
        ↓
BACKEND
Handles application logic and shared data
        ↓
DATABASE
Stores persistent data
```

---

# Important Question: Does the Client Send the Request Through the Frontend?

## Yes, in a typical web application.

Suppose the user clicks a button:

```text
User
  ↓
Clicks "Get Users"
  ↓
Your frontend JavaScript runs
  ↓
fetch("/api/users")
  ↓
Browser sends HTTP request
  ↓
Backend
```

For example, your frontend may contain code like:

```javascript
fetch("/api/users")
```

The important detail is:

> **The frontend JavaScript initiates the request, but the browser actually executes that code and performs the network request.**

So the technically accurate flow is:

```text
User Action
     ↓
Frontend JavaScript
     ↓
Browser executes the JavaScript
     ↓
Browser sends HTTP request
     ↓
Backend
```

---

# Who Is the Client?

In a web application, the **client** is usually the user's browser.

The frontend is the application code running inside that client.

So:

```text
OTHER PERSON'S DEVICE
│
└── Browser = Client
        │
        └── Your Frontend Code
                │
                └── Runs inside the browser
```

This is an important distinction.

The frontend is not usually a separate machine sitting between the user's browser and your backend.

Instead:

> **The frontend code is downloaded into the user's browser and runs there.**

---

# Example: A User Requests Data

Suppose the user clicks a button to see a list of users.

The flow is:

```text
User
  ↓
Clicks "Get Users"
  ↓
YOUR frontend code
runs in THEIR browser
  ↓
Frontend JavaScript executes:
fetch("/api/users")
  ↓
Browser sends request
  ↓
YOUR backend
  ↓
Backend fetches data
  ↓
Backend sends JSON response
  ↓
THEIR browser receives the data
  ↓
YOUR frontend code displays it
```

This gives us an important distinction:

```text
Your Code ≠ Where Your Code Runs
```

You may have written both the frontend and backend.

However:

```text
YOUR FRONTEND CODE
        ↓
Downloaded and executed on
THE USER'S DEVICE
```

while:

```text
YOUR BACKEND CODE
        ↓
Stays and executes on
YOUR SERVER
```

---

# A Simple Mental Model

```text
                YOUR CODE
                   │
        ┌──────────┴──────────┐
        │                     │
        ▼                     ▼
   FRONTEND CODE         BACKEND CODE
        │                     │
        ▼                     ▼
Runs on the user's        Runs on your
browser/device            server
```

Both may be written by the same developer.

The important difference is **where they execute**.

---

# The Main Question of the Next Section

At this point, a very logical question appears:

> **If the frontend can already send requests over the internet to another machine, why can't we put the backend logic directly inside the frontend?**

For example, why not do this?

```text
User's Browser
        │
        │ Directly handles everything
        ├── Database operations
        ├── Business logic
        ├── External API calls
        ├── File operations
        └── Heavy computation
```

instead of:

```text
User's Browser
        ↓
Frontend
        ↓
Backend
        ↓
Database / Other Services
```

This is the exact hypothetical question explored in the next part of the video.

The next section will explain:

> **Why can't we simply remove the backend and run backend logic directly in the user's browser?**

---

# 4. How Do Frontends Work?

To understand why we cannot simply run all backend logic in the frontend, we first need to understand **how a frontend actually works behind the scenes**.

The key difference between frontend and backend is:

> **Frontend code is sent to the user and executed on the user's device. Backend code stays on the server and is executed on the server.**

Let's trace what happens when someone opens a website.

---

## Step 1: The User Requests the Website

Suppose a user types:

```text
mywebsite.com
```

The browser follows a similar network flow that we saw earlier:

```text
User's Browser
       ↓
DNS
       ↓
Gets the IP address
       ↓
AWS EC2 Public IP
       ↓
Security Group / Firewall
       ↓
EC2 Instance
       ↓
Nginx
       ↓
Frontend Server
```

In the example from the video, the frontend server is running on:

```text
localhost:3000
```

while the backend server is running on:

```text
localhost:3001
```

So Nginx can route requests to different internal servers.

For example:

```text
Request for frontend domain
        ↓
Nginx
        ↓
localhost:3000
        ↓
Frontend Server
```

and:

```text
Request for backend domain
        ↓
Nginx
        ↓
localhost:3001
        ↓
Backend Server
```

---

# Step 2: The Frontend Server Sends the Frontend Code

Once the request reaches the frontend server, the server sends the resources required by the browser.

These can include:

- HTML
- CSS
- JavaScript
- Images
- Fonts

A simplified flow is:

```text
YOUR FRONTEND SERVER
        │
        │ Sends frontend resources
        ▼
OTHER PERSON'S BROWSER
        │
        ├── HTML
        ├── CSS
        ├── JavaScript
        ├── Images
        └── Fonts
```

This is the first major concept to understand:

> **The frontend code belongs to you, but it is sent to and executed on the user's device.**

---

# What Does "Frontend Runs on the Client" Mean?

Suppose you create a frontend application.

You write code like:

```javascript
button.addEventListener("click", () => {
    fetch("/api/users");
});
```

This code is part of **your frontend application**.

However, when another person visits your website:

```text
Your Server
      ↓
Sends your frontend JavaScript
      ↓
Their Browser downloads it
      ↓
Their Browser executes it
```

So:

```text
WHO WROTE THE CODE?
You / The application developer

WHERE DOES IT EXECUTE?
The user's browser/device
```

This distinction is extremely important.

---

# Step 3: The Browser Processes the HTML

The browser first receives the main HTML document.

For example:

```text
Frontend Server
        ↓
HTML
        ↓
Browser
```

The browser reads the HTML and discovers other resources that may be needed.

For example:

```html
<link rel="stylesheet" href="style.css">
<script src="app.js"></script>
<img src="image.png">
```

The browser can then make additional requests to fetch these resources.

So the process looks something like:

```text
Browser receives HTML
        ↓
Browser reads HTML
        ↓
Finds CSS
        ↓
Fetches CSS

Finds JavaScript
        ↓
Fetches JavaScript

Finds Images
        ↓
Fetches Images

Finds Fonts
        ↓
Fetches Fonts
```

---

# Step 4: CSS Helps the Browser Display the Page

Once the browser receives the CSS, it can apply styling to the webpage.

For example:

```text
HTML
+
CSS
+
Fonts
        ↓
Browser renders the visual page
```

This determines things such as:

- Colors
- Layout
- Fonts
- Button styles
- Background
- Spacing

Without CSS, the page may still contain the same information, but it will not have the intended design.

---

# Step 5: JavaScript Makes the Page Interactive

The browser also downloads JavaScript files.

The browser then **executes that JavaScript**.

This allows the frontend to become interactive.

For example:

```text
JavaScript downloaded
        ↓
Browser executes JavaScript
        ↓
Event listeners are attached
        ↓
Buttons can respond to clicks
```

Suppose your page has a button:

```text
[ Get Users ]
```

Your frontend JavaScript may attach an event listener:

```javascript
button.addEventListener("click", getUsers);
```

Now:

```text
User clicks button
        ↓
Browser detects the click
        ↓
JavaScript event listener runs
        ↓
getUsers() executes
```

This is what makes the frontend interactive.

---

# A Very Important Concept: The Browser Is the Frontend Runtime

When we say frontend JavaScript runs on the client, the browser is acting as the **runtime environment**.

A runtime is the environment in which code actually executes.

For frontend JavaScript:

```text
JavaScript Code
       ↓
Browser Runtime
       ↓
Runs on User's Device
```

For a traditional backend:

```text
Backend Code
       ↓
Node.js Runtime
       ↓
Runs on Your Server
```

This is the key comparison:

```text
FRONTEND                         BACKEND

Code is sent to client           Code stays on server
        ↓                               ↓
Browser executes it              Server runtime executes it
        ↓                               ↓
Runs on user's device            Runs on your infrastructure
```

---

# The Exact Question I Had While Learning

At this point, I had an important question:

> **If the frontend is already running on the user's browser and can make requests to the backend, can the frontend directly communicate with another machine instead?**

The answer is:

## Yes.

A browser can communicate with another machine over a network.

For example:

```text
Browser
   ↓
HTTP/HTTPS Request
   ↓
Another Server
```

This is exactly what happens when frontend JavaScript runs:

```javascript
fetch("https://api.mywebsite.com/users");
```

The flow is:

```text
Frontend JavaScript
running in the user's browser
        ↓
fetch()
        ↓
Browser makes an HTTP request
        ↓
Another server on the Internet
```

So the fact that a server or database is on a **different physical machine** is not itself the reason we need a backend.

That was an important realization.

---

# Can the Frontend Use DNS and Connect to an IP Address?

Yes.

When the browser needs to connect to a domain:

```text
api.mywebsite.com
```

it can perform the necessary network resolution process.

Conceptually:

```text
Domain Name
      ↓
DNS
      ↓
IP Address
      ↓
Browser connects to that server
```

So a browser can absolutely communicate with a publicly reachable server on another machine.

For example:

```text
User's Browser
       ↓
DNS resolves:
api.mywebsite.com
       ↓
Gets IP address
       ↓
Browser sends HTTP/HTTPS request
       ↓
Remote Server
```

Therefore, this is possible:

```text
User's Browser
        ↓
Internet
        ↓
Another Machine / Server
```

The real question is not:

> "Can a frontend communicate with another machine?"

It definitely can.

The real question is:

> **What is the browser allowed to communicate with, what protocols can it use, and what level of access should we give every user's browser?**

That is where the need for a backend becomes much clearer.

---

# Frontend and Backend: The Same Developer, Different Execution Locations

Both frontend and backend may be written by the same developer.

For example:

```text
YOU write:

Frontend Code
+
Backend Code
```

But after deployment:

```text
                YOUR APPLICATION CODE
                         │
            ┌────────────┴────────────┐
            │                         │
            ▼                         ▼
      FRONTEND CODE              BACKEND CODE
            │                         │
            │ Sent to every           │ Stays on your
            │ user's browser          │ server
            ▼                         ▼
      Runs on THEIR device       Runs on YOUR server
```

This is probably the most important mental model from this part of the video.

> **Frontend code belongs to your application, but it executes on the user's device. Backend code also belongs to your application, but it executes on your server.**

---

# Example: User Requests Some Data

Suppose the user clicks:

```text
[ Get Users ]
```

The complete flow is:

```text
OTHER PERSON
       ↓
Clicks "Get Users"
       ↓
YOUR FRONTEND JAVASCRIPT
running in THEIR browser
       ↓
fetch("/api/users")
       ↓
THEIR BROWSER sends request
       ↓
YOUR BACKEND
running on YOUR server
       ↓
Backend fetches/processes data
       ↓
YOUR BACKEND sends response
       ↓
THEIR BROWSER receives data
       ↓
YOUR FRONTEND JAVASCRIPT
processes/displays the data
```

Notice the important separation:

```text
The browser does NOT receive your backend code.

The browser receives the response/data from your backend.
```

For example:

```text
Browser asks:
GET /users

Backend does:
- Runs server-side logic
- Connects to database
- Processes data

Backend returns:
JSON data

Browser receives:
Only the response/data
```

The user normally does not download your backend source code simply by visiting the website.

---

# This Leads to the Main Question

Now we can finally understand the question the video asks:

> **Why can't we just run the backend logic inside the frontend and remove the backend entirely?**

The hypothetical architecture would look like:

```text
User's Browser
        │
        ├── Frontend UI
        │
        ├── Business Logic
        │
        ├── Database Operations
        │
        ├── External Service Communication
        │
        ├── File Operations
        │
        └── Heavy Computation
```

Instead of the traditional architecture:

```text
User's Browser
        │
        │ Frontend
        ▼
Backend Server
        │
        ├── Business Logic
        ├── Database Access
        ├── External Services
        ├── File Operations
        └── Heavy Computation
```

This is the **hypothetical question** being asked.

It is not saying that this is how applications normally work.

It is asking:

> **"Why do we need a separate backend at all? Why not put its responsibilities into the frontend?"**

The next section answers exactly that.

---

# 5. Why Can't We Run Backend Logic in the Frontend?

At this point, an important question comes up:

> **If frontend JavaScript can send requests to other machines over the internet, why can't it directly handle databases, external services, business logic, and everything else that a backend does?**

Technically, the frontend **can perform some of these tasks**.

However, a browser is not designed to act like a full backend server.

The main reasons are:

1. Security and browser sandboxing
2. Restrictions on communicating with external APIs
3. Database connections and connection pooling
4. Computing power

---

# 5.1 Security and Browser Sandboxing

The first major reason is **security**.

Frontend code runs inside a browser.

A browser does not allow JavaScript to freely access everything on the user's computer.

The browser acts as a restricted environment, often called a **sandbox**.

You can think of it like this:

```text
User's Computer
│
├── Operating System
│
├── File System
│
├── Other Applications
│
└── Browser Sandbox
        │
        └── Frontend JavaScript runs here
```

Frontend JavaScript is intentionally isolated from many parts of the user's computer.

It usually has access to limited browser resources such as:

- The DOM
- Browser APIs
- Local Storage
- Cookies
- Some browser-provided APIs
- External APIs, subject to security rules

It does **not** normally get unrestricted access to:

- The user's entire file system
- Operating system processes
- Server environment variables
- Arbitrary network connections
- Native database drivers

---

## Why Is the Browser So Restrictive?

Think about what happens when you visit a website.

The website sends code to your browser:

```text
Remote Server
      ↓
Sends JavaScript
      ↓
Your Browser downloads it
      ↓
Your Browser executes it
```

If browsers gave every website unrestricted access to your computer, a malicious website could potentially do something like:

```text
You visit a website
        ↓
Website JavaScript runs
        ↓
Reads your personal files
        ↓
Reads sensitive information
        ↓
Sends it to an attacker
```

That would be extremely dangerous.

Therefore, browsers deliberately isolate the code they download and execute.

The core idea is:

> **You are running code that came from someone else's server, so the browser must restrict what that code can access.**

---

# Why Does This Affect Backend Logic?

Backend applications often need access to resources that browsers intentionally restrict.

For example, a backend may need to:

```text
Write logs to a file
        ↓
Read environment variables
        ↓
Access server resources
        ↓
Use native libraries
        ↓
Maintain long-running processes
```

A Node.js backend running on your server can do things such as:

```text
Node.js Server
│
├── Access environment variables
├── Access server files
├── Write log files
├── Run server processes
└── Connect to databases
```

But frontend JavaScript running inside a user's browser is much more restricted.

Therefore:

```text
Frontend Runtime
        ≠
Backend Runtime
```

They are both capable of running JavaScript, but they run in very different environments with very different permissions.

---

# 5.2 External APIs and CORS

The second major reason is restrictions on communicating with external APIs.

Suppose your frontend is running on:

```text
https://mywebsite.com
```

and it tries to make a request to:

```text
https://some-other-api.com
```

The browser may apply a security policy called **CORS**.

CORS stands for:

```text
Cross-Origin Resource Sharing
```

The browser uses CORS rules to control whether JavaScript from one origin is allowed to access resources from another origin.

Conceptually:

```text
Frontend running on:

mywebsite.com
        │
        │ Request
        ▼
some-other-api.com
        │
        ▼
Does the API allow this origin?
        │
        ├── Yes → Browser allows access
        │
        └── No  → Browser blocks access to the response
```

So frontend JavaScript cannot simply assume that it can communicate with every external API.

The external server must allow the appropriate cross-origin access through its HTTP response headers.

---

## Why Is This a Problem for Backend Logic?

A backend server may need to communicate with many different services.

For example:

```text
Backend
│
├── Payment Service
├── Email Service
├── Notification Service
├── Database
├── Authentication Service
└── Other Internal Services
```

A backend is generally much more suitable for server-to-server communication.

If we moved everything into the browser, browser security policies could restrict certain cross-origin interactions.

This is another reason why the backend is useful as a separate server-side environment.

---

# 5.3 Can the Frontend Directly Connect to a Database?

This was one of my biggest questions while learning this:

> **If the database is on another machine, and the browser can already send requests to another machine, why can't the frontend directly connect to the database?**

At first, the idea seems possible:

```text
User's Browser
       ↓
Internet
       ↓
Database on Another Machine
```

And yes, **being on another physical machine is not the main problem**.

The bigger problem is what would happen if every user's browser directly accessed the database.

---

## Problem 1: Database Credentials Would Be Exposed

Suppose your frontend needs to connect directly to your database.

It would need some connection information, such as:

```text
Database Host
Username
Password
Connection Details
```

Frontend code is sent to the user's browser.

This means the user can inspect the frontend code.

For example:

```text
Your Server
      ↓
Sends JavaScript to User
      ↓
User's Browser receives it
      ↓
User can inspect the JavaScript
```

Therefore, putting sensitive database credentials in frontend code would be extremely dangerous.

Conceptually:

```text
❌ BAD

Frontend Code
    │
    ├── Database URL
    ├── Database Username
    └── Database Password

        ↓

Sent to every user
```

Instead:

```text
✅ GOOD

Frontend
    ↓
Backend
    ↓
Database
```

The database credentials stay on the backend server.

The user never needs direct access to them.

---

## Problem 2: Browsers Are Not Designed as Database Clients

Backend runtimes have database drivers specifically designed to communicate with databases.

For example:

```text
Node.js Backend
        ↓
Database Driver
        ↓
PostgreSQL / MongoDB
```

These drivers can work with database-specific protocols, sockets, binary data, and long-lived connections.

A browser is primarily designed to work with web protocols and browser APIs.

Therefore, a browser is not generally designed to act as a direct database client in the same way as a backend runtime.

---

## Problem 3: Connection Pooling

This is one of the important points mentioned in the video.

Backend servers may receive thousands of requests.

If the backend created and destroyed a completely new database connection for every request, the database could become overwhelmed.

Instead, backend systems often maintain a **connection pool**.

A connection pool is essentially a managed collection of reusable database connections.

For example:

```text
Backend
   │
   ▼
Connection Pool

Connection 1
Connection 2
Connection 3
Connection 4
Connection 5
   │
   ▼
Database
```

When a request arrives:

```text
Request
   ↓
Backend takes an available connection
from the connection pool
   ↓
Runs database operation
   ↓
Returns the connection to the pool
```

This is more efficient than repeatedly doing:

```text
Request 1
   ↓
Create Database Connection
   ↓
Query
   ↓
Destroy Connection

Request 2
   ↓
Create Database Connection
   ↓
Query
   ↓
Destroy Connection
```

---

## What Would Happen if Every User Connected Directly?

Imagine:

```text
100,000 Users
      │
      │ Each browser creates its own
      │ database connection
      ▼
DATABASE
```

The database could be overwhelmed by a huge number of direct client connections.

A backend provides a centralized layer:

```text
100,000 Users
       │
       │ HTTP Requests
       ▼
Backend Servers
       │
       │ Managed Connection Pools
       ▼
Database
```

This architecture allows the backend to efficiently manage database access.

---

# 5.4 Computing Power

The fourth major reason is **computing power**.

Frontend applications can run on many different devices:

```text
High-end Desktop
        │
Laptop
        │
Smartphone
        │
Low-end Smartphone
        │
Tablet
        │
Older Computer
```

Every user has different hardware.

One user may have:

```text
32 GB RAM
High-performance CPU
```

while another user may have:

```text
2 GB RAM
Low-performance processor
```

If we put heavy backend processing on the frontend, the performance of our application would depend heavily on the user's device.

For example:

```text
Heavy Computation
        │
        ▼
Runs on User's Device
        │
        ├── Powerful Device → May work fine
        │
        └── Weak Device → Lag / Slow / Crash
```

---

## Why Is a Backend Better for Heavy Processing?

A backend runs on infrastructure that we control.

For example:

```text
AWS EC2 Server
│
├── CPU
├── RAM
└── Storage
```

If our application needs more resources, we can upgrade or scale the infrastructure.

Conceptually:

```text
More Users
    ↓
Need More Processing Power
    ↓
Increase Server Resources
    ↓
More CPU / RAM
```

This provides more consistent processing capability than relying on thousands or millions of different user devices.

---

# The Four Main Reasons Summarized

```text
Why not put all backend logic in the frontend?

1. SECURITY
   Browser code is sandboxed and restricted.

2. EXTERNAL API RESTRICTIONS
   Browser security policies such as CORS can restrict
   cross-origin communication.

3. DATABASES
   Browsers are not designed to manage direct database
   access, credentials, and connection pooling like backend servers.

4. COMPUTING POWER
   User devices have different capabilities, while backend
   infrastructure can be centrally controlled and scaled.
```

---

# The Most Important Security Difference

This is the core idea:

```text
FRONTEND CODE
        ↓
Must be sent to the user
        ↓
The user can inspect it
```

Therefore, we should never place sensitive information in frontend code.

Examples of sensitive information:

```text
❌ Database passwords
❌ Private API keys
❌ Secret tokens
❌ Server environment secrets
```

These should remain on the server:

```text
Frontend
   ↓
Request
   ↓
Backend
   ↓
Uses secrets securely
   ↓
Returns only the necessary response
   ↓
Frontend
```

The frontend gets the **result**, not the secret.

---

# Final Mental Model: Why Frontend and Backend Are Separate

```text
                    YOUR APPLICATION

        ┌─────────────────────────────────┐
        │                                 │
        ▼                                 ▼

   FRONTEND                           BACKEND
   Runs on client                     Runs on server

   Handles UI                         Handles server logic
   Handles interaction                Handles business logic
   Sends requests                     Accesses databases
   Displays data                      Manages sensitive data
   Runs on user's device              Runs on controlled infrastructure

        │                                 │
        └───────────────┬─────────────────┘
                        │
                        ▼
                     DATABASE
```

---

# One-Sentence Summary

> **The frontend is responsible for interacting with the user, while the backend provides a secure, centralized, scalable environment for processing requests, managing data, accessing databases, communicating with services, and keeping sensitive logic and credentials away from the user's device.**

---

# Video 3 — Final Request Flow

We can now combine everything we learned into one complete picture.

```text
1. USER TYPES:

   mywebsite.com
          │
          ▼

2. DNS

   Finds the IP address for the domain
          │
          ▼

3. YOUR AWS EC2 PUBLIC IP
          │
          ▼

4. AWS SECURITY GROUP / FIREWALL

   Checks whether the incoming traffic is allowed
          │
          ▼

5. EC2 VIRTUAL MACHINE
          │
          ▼

6. NGINX

   Acts as a reverse proxy and forwards the request
   to the appropriate internal server
          │
          ├──────────────────┐
          │                  │
          ▼                  ▼

   localhost:3000      localhost:3001
   FRONTEND SERVER     BACKEND SERVER
          │                  │
          ▼                  ▼

   Sends HTML/CSS/JS    Processes API requests
          │                  │
          ▼                  ▼

   USER'S BROWSER       DATABASE / SERVICES
          │                  │
          └────────┬─────────┘
                   │
                   ▼

             RESPONSE TO USER
```

---

# Final Key Takeaways

- A backend is a server-side system that receives and processes requests.
- A backend can serve data, process business logic, and communicate with databases and other services.
- A domain name is mapped to an IP address using DNS.
- In this example, DNS points the domain to the public IP of our EC2 instance.
- The AWS Security Group controls which incoming network traffic is allowed.
- Nginx acts as a reverse proxy and forwards requests to internal servers.
- The frontend server can send HTML, CSS, JavaScript, images, and other resources to the user's browser.
- Frontend code runs on the user's device inside the browser.
- Backend code runs on infrastructure controlled by the application.
- The frontend can make network requests to other machines, but that does not make it a replacement for a backend.
- Browsers are sandboxed for security.
- Browser restrictions such as CORS can limit some cross-origin communication.
- Database credentials and sensitive logic should not be exposed to users.
- Backend servers can efficiently manage database connections using connection pools.
- Centralized backend infrastructure provides more consistent and scalable computing power.

---
