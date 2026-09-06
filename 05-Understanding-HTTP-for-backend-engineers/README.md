# 🌐 Understanding HTTP for Backend Engineers

<p align="center">
  <b>HTTP from the ground up — requests, responses, headers, methods, CORS, caching, compression, streaming, and HTTPS.</b>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Backend-HTTP-blue?style=for-the-badge" alt="Backend HTTP">
  <img src="https://img.shields.io/badge/Topic-Web%20Networking-green?style=for-the-badge" alt="Web Networking">
  <img src="https://img.shields.io/badge/Level-Beginner%20→%20Intermediate-orange?style=for-the-badge" alt="Level">
</p>

---

## 🎥 Video

> 📺 **Understanding HTTP for Backend Engineers**

**Video:** [Watch on YouTube](https://youtu.be/a3C1DMswClQ?si=j6AM3TR_sCOQwlQR)

---

## 📚 Table of Contents

- [1. What is HTTP?](#1--what-is-http)
- [2. HTTP is Stateless](#2--http-is-stateless)
- [3. Client–Server Model](#3--clientserver-model)
- [4. HTTP and TCP](#4--http-and-tcp)
- [5. OSI Model](#5--osi-model)
- [6. Evolution of HTTP](#6--evolution-of-http)
- [7. HTTP Request](#7--http-request)
- [8. HTTP Response](#8--http-response)
- [9. HTTP Headers](#9--http-headers)
- [10. HTTP Methods](#10--http-methods)
- [11. Idempotency](#11--idempotency)
- [12. CORS](#12--cors)
- [13. HTTP Status Codes](#13--http-status-codes)
- [14. HTTP Caching](#14--http-caching)
- [15. Content Negotiation](#15--content-negotiation)
- [16. HTTP Compression](#16--http-compression)
- [17. Persistent Connections](#17--persistent-connections)
- [18. Multipart Form Data](#18--multipart-form-data)
- [19. Streaming](#19--streaming)
- [20. SSL, TLS & HTTPS](#20--ssl-tls--https)
- [21. Complete Mental Model](#21--complete-mental-model)
- [22. Quick Revision](#22--quick-revision)

---

# 1. 🌐 What is HTTP?

**HTTP (HyperText Transfer Protocol)** is an **application-layer protocol** used for communication between a client and a server.

The basic idea is simple:

```text
┌──────────────┐                         ┌──────────────┐
│    CLIENT    │ ───── HTTP Request ───► │    SERVER    │
│              │ ◄──── HTTP Response ─── │              │
└──────────────┘                         └──────────────┘
```

HTTP is the foundation of communication used by:

- 🌍 Websites
- 🔌 APIs
- 📱 Web applications
- 🖥️ Backend services
- 🤝 Communication between services

### Traditional Definition

A backend can traditionally be thought of as a **computer or server that listens for incoming requests**.

Those requests can use protocols such as:

- HTTP
- HTTPS
- WebSocket
- gRPC

---

# 2. 🧠 HTTP is Stateless

HTTP is a **stateless protocol**.

This means that each HTTP request is treated independently. HTTP itself does not automatically remember previous requests from the same client.

### Example

```text
Request 1
Client ─────────► Server
       "Who am I?"

Request 2
Client ─────────► Server
       "Give me my email."
```

The server does not inherently know that Request 2 belongs to the same interaction.

Applications can maintain state using mechanisms such as:

- 🍪 Cookies
- 🔐 Sessions
- 🎫 Tokens
- 🪪 Authentication headers

> 💡 **Important:** Stateless does **not** mean that web applications cannot have state.  
> It means that **HTTP itself does not inherently maintain state between requests**.

---

# 3. 🖥️ Client–Server Model

HTTP commonly follows the **client–server model**.

### Client

The client initiates the request.

Examples:

- Web browser
- Mobile application
- Frontend application
- Another backend service
- Command-line tools

### Server

The server receives the request, processes it, and sends a response.

A backend may perform:

```text
Request
   │
   ├──► Authentication
   ├──► Validation
   ├──► Business Logic
   ├──► Database Query
   ├──► Other Services
   │
   ▼
Response
```

---

# 4. 🔗 HTTP and TCP

HTTP operates at the **Application Layer**.

Traditionally:

```text
┌──────────────────────────┐
│ Application Layer        │
│ HTTP                     │
├──────────────────────────┤
│ Transport Layer          │
│ TCP                      │
├──────────────────────────┤
│ Internet Layer           │
│ IP                       │
├──────────────────────────┤
│ Data Link / Physical     │
└──────────────────────────┘
```

### Responsibilities

**HTTP** defines how web communication is structured.

**TCP** provides reliable, ordered delivery of data.

> 🧩 Think of HTTP as the **language/rules of communication**, while TCP provides a **reliable transport mechanism** underneath it.

---

# 5. 🧱 OSI Model

The OSI model divides networking into seven layers.

| Layer | Name | Examples |
|:---:|---|---|
| 7 | Application | HTTP, DNS |
| 6 | Presentation | Data representation |
| 5 | Session | Session management |
| 4 | Transport | TCP, UDP |
| 3 | Network | IP |
| 2 | Data Link | Ethernet |
| 1 | Physical | Cables, radio signals |

📌 **HTTP operates at Layer 7 — the Application Layer.**

---

# 6. 🚀 Evolution of HTTP

HTTP has evolved to improve performance, efficiency, and scalability.

## HTTP/1.0

HTTP/1.0 generally used a separate TCP connection for each request.

```text
Request 1 ──► Connection 1
Request 2 ──► Connection 2
Request 3 ──► Connection 3
```

This creates additional connection overhead.

---

## HTTP/1.1

HTTP/1.1 introduced **persistent connections** as the normal behavior.

```text
One Connection
      │
      ├── Request 1
      ├── Request 2
      ├── Request 3
      └── Request 4
```

Important improvements included:

- Persistent connections
- `Host` header
- Chunked transfer encoding
- Improved caching
- More standardized HTTP behavior

---

## HTTP/2

HTTP/2 improved performance while keeping the same fundamental HTTP semantics.

Major improvements:

- 🚦 Multiplexing
- 📦 Binary framing
- 🗜️ Header compression
- 🔀 Multiple streams over one connection

```text
                 One Connection
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
       Stream 1     Stream 2     Stream 3
       Request      Request      Request
```

---

## HTTP/3

HTTP/3 uses **QUIC** instead of TCP.

QUIC runs over **UDP**.

```text
HTTP/3
   │
   ▼
 QUIC
   │
   ▼
 UDP
   │
   ▼
 IP
```

---

# 7. 📤 HTTP Request

An HTTP request is sent by the client to the server.

A request generally contains:

1. **Request line**
2. **Headers**
3. **Blank line**
4. **Optional body**

### Example

```http
GET /users/123 HTTP/1.1
Host: example.com
Accept: application/json
Authorization: Bearer <token>

```

### Request Line

```text
GET /users/123 HTTP/1.1
│       │          │
│       │          └── HTTP Version
│       └───────────── Resource Path
└───────────────────── HTTP Method
```

---

# 8. 📥 HTTP Response

The server sends an HTTP response back to the client.

A response generally contains:

1. **Status line**
2. **Headers**
3. **Blank line**
4. **Optional body**

### Example

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "name": "Sarthak"
}
```

The status line contains:

```text
HTTP VERSION
STATUS CODE
REASON PHRASE
```

---

# 9. 🏷️ HTTP Headers

Headers provide **additional metadata** about the request or response.

Example:

```http
Content-Type: application/json
Authorization: Bearer <token>
Accept: application/json
Cache-Control: max-age=60
```

Headers can describe:

- Content type
- Authentication
- Caching
- Compression
- Accepted formats
- Client information
- Server information

### Common Headers

| Header | Purpose |
|---|---|
| `Content-Type` | Describes the body format |
| `Authorization` | Carries authentication credentials |
| `Accept` | Specifies preferred response format |
| `Cache-Control` | Controls caching |
| `ETag` | Identifies a resource version |
| `Last-Modified` | Indicates when a resource changed |
| `Accept-Language` | Specifies preferred language |
| `Accept-Encoding` | Specifies supported encodings |

---

# 10. 🛠️ HTTP Methods

HTTP methods describe **what the client wants to do**.

| Method | Typical Purpose |
|---|---|
| `GET` | Retrieve data |
| `POST` | Create a resource / perform an operation |
| `PUT` | Replace or update a resource |
| `PATCH` | Partially update a resource |
| `DELETE` | Delete a resource |
| `HEAD` | Get headers without the body |
| `OPTIONS` | Discover supported communication options |

### GET

```http
GET /users/123
```

> "Give me user 123."

### POST

```http
POST /users
```

```json
{
  "name": "Sarthak",
  "email": "example@email.com"
}
```

### PUT

```http
PUT /users/123
```

Usually used to replace/update a resource.

### PATCH

```http
PATCH /users/123
```

Used for a partial update.

### DELETE

```http
DELETE /users/123
```

Used to delete a resource.

### HEAD

Similar to `GET`, but the response does not contain the response body.

### OPTIONS

Used to discover available communication options and is especially important for **CORS preflight requests**.

---

# 11. 🔁 Idempotency

An operation is **idempotent** when making the same request multiple times has the **same intended effect** as making it once.

For example:

```http
PUT /users/123
```

If the same update is sent repeatedly, it does not continually create new versions of the intended resource state.

### Commonly Idempotent Methods

- `GET`
- `HEAD`
- `PUT`
- `DELETE`
- `OPTIONS`

`POST` is generally **not idempotent**.

For example:

```http
POST /orders
```

could create a new order every time it is sent.

> ⚠️ **Remember:** Idempotent does not necessarily mean that the response is identical every time. It refers to the **intended effect on the resource/state**.

---

# 12. 🌍 CORS

**CORS = Cross-Origin Resource Sharing**

Browsers use the **Same-Origin Policy** as a security mechanism.

Two URLs have different origins if their:

- Scheme
- Host
- Port

differ.

### Example

```text
http://localhost:5173
http://localhost:3001
```

These are different origins because their **ports differ**.

---

## Same-Origin Policy

The browser does not allow frontend JavaScript to freely access resources from another origin.

```text
Frontend
localhost:5173
     │
     │ Cross-Origin Request
     ▼
API
localhost:3001
```

The server can explicitly allow the frontend:

```http
Access-Control-Allow-Origin: http://localhost:5173
```

---

## Simple CORS Request

Some cross-origin requests are considered **simple requests**.

Example:

```http
GET /users
Origin: http://localhost:5173
```

The server can respond with:

```http
Access-Control-Allow-Origin: http://localhost:5173
```

---

## Preflight Request ✈️

Some cross-origin requests require the browser to check permission **before sending the actual request**.

The browser sends an `OPTIONS` request.

### Browser → Server

```http
OPTIONS /users
Origin: http://localhost:5173
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: Authorization, Content-Type
```

### Server → Browser

```http
HTTP/1.1 204 No Content
Access-Control-Allow-Origin: http://localhost:5173
Access-Control-Allow-Methods: PUT
Access-Control-Allow-Headers: Authorization, Content-Type
Access-Control-Max-Age: 3600
```

If the request is allowed, the browser sends the actual request:

```http
PUT /users/123
```

### Why Preflight?

The browser is effectively asking:

> **"Is this origin allowed to make this type of request with these headers?"**

before sending the actual request.

---

# 13. 🚦 HTTP Status Codes

Status codes tell the client **what happened with its request**.

| Range | Category | Meaning |
|:---:|---|---|
| `1xx` | ℹ️ Informational | Request processing information |
| `2xx` | ✅ Success | Request succeeded |
| `3xx` | 🔀 Redirection | Further action required |
| `4xx` | ❌ Client Error | Problem with the request/client |
| `5xx` | 💥 Server Error | Problem on the server side |

---

## `1xx` — Informational

### `100 Continue`

The server has received the initial part of the request and the client may continue.

### `101 Switching Protocols`

The server agrees to switch protocols.

---

## `2xx` — Success

### `200 OK`

The request succeeded.

### `201 Created`

A resource was successfully created.

### `204 No Content`

The request succeeded, but there is no response body.

---

## `3xx` — Redirection

### `301 Moved Permanently`

The resource has permanently moved.

### `302 Found`

The resource is temporarily available at another location.

### `304 Not Modified`

The cached version of the resource can still be used.

---

## `4xx` — Client Errors

### `400 Bad Request`

The request is invalid or malformed.

### `401 Unauthorized`

Authentication is required or has failed.

### `403 Forbidden`

The server understands the request but refuses to authorize it.

### `404 Not Found`

The requested resource could not be found.

### `405 Method Not Allowed`

The HTTP method is not supported for that resource.

### `409 Conflict`

The request conflicts with the current state of the resource.

### `429 Too Many Requests`

The client has sent too many requests in a given period.

Often associated with **rate limiting**.

---

## `5xx` — Server Errors

### `500 Internal Server Error`

A generic server-side error.

### `501 Not Implemented`

The server does not support the required functionality.

### `502 Bad Gateway`

A gateway/proxy received an invalid response from an upstream server.

### `503 Service Unavailable`

The server is currently unable to handle the request.

Possible causes:

- Overload
- Maintenance
- Temporary unavailability

### `504 Gateway Timeout`

A gateway/proxy did not receive a timely response from an upstream server.

---

# 14. ⚡ HTTP Caching

Caching allows a client to **reuse a previously downloaded response** instead of requesting the same resource every time.

### Benefits

- ⚡ Faster responses
- 📉 Less network traffic
- 🖥️ Lower server load

```text
Without Cache

Client ─────► Server
Client ◄───── Server

Client ─────► Server
Client ◄───── Server


With Cache

Client ─────► Server
Client ◄───── Server
       │
       ▼
     CACHE
       │
       └──► Reuse locally
```

---

## Cache-Control

The server can specify how long a response can be considered fresh.

```http
Cache-Control: max-age=10
```

---

## ETag

An **ETag** identifies a particular version of a resource.

```http
ETag: "abc123"
```

Later, the client can send:

```http
If-None-Match: "abc123"
```

If the resource has not changed:

```http
304 Not Modified
```

The client uses its cached copy.

---

## Last-Modified

The server can also provide the last modification time.

```http
Last-Modified: Wed, 02 Sep 2026 10:00:00 GMT
```

The client can later send:

```http
If-Modified-Since: Wed, 02 Sep 2026 10:00:00 GMT
```

If nothing changed:

```http
304 Not Modified
```

---

## 🔄 Conditional Request Flow

```text
FIRST REQUEST

Client ───────────────► Server
       ◄───────────────
       200 OK
       Resource
       ETag: "abc123"


LATER REQUEST

Client ───────────────► Server
       If-None-Match: "abc123"

       ◄───────────────
       304 Not Modified

Client uses cached resource.
```

If the resource changed:

```text
Client ───────────────► Server
       If-None-Match: "old"

       ◄───────────────
       200 OK
       New Resource
       New ETag
```

---

# 15. 🤝 Content Negotiation

Content negotiation allows the client to tell the server **what type of representation it prefers**.

### `Accept`

Specifies preferred media types.

```http
Accept: application/json
```

### `Accept-Language`

Specifies preferred language.

```http
Accept-Language: en-US
```

### `Accept-Encoding`

Specifies supported content encodings.

```http
Accept-Encoding: gzip
```

The server uses these headers when choosing an appropriate response representation.

---

# 16. 🗜️ HTTP Compression

Large HTTP responses can be compressed before being sent across the network.

### Client

```http
Accept-Encoding: gzip
```

### Server

```http
Content-Encoding: gzip
```

The client then decompresses the response.

```text
Original Response
████████████████████████████████

        ↓ Compression

Compressed Response
██████████
```

### Why compress?

Compression can:

- Reduce payload size
- Reduce bandwidth usage
- Improve transfer speed

---

# 17. 🔌 Persistent Connections

Opening a new TCP connection repeatedly creates overhead.

HTTP/1.1 supports **persistent connections**, allowing multiple requests to reuse the same connection.

### Without Reuse

```text
Request 1 → Connection 1
Request 2 → Connection 2
Request 3 → Connection 3
```

### With Persistent Connection

```text
             ONE CONNECTION
                    │
        ┌───────────┼───────────┐
        ▼           ▼           ▼
     Request 1   Request 2   Request 3
```

A connection can be explicitly closed with:

```http
Connection: close
```

---

# 18. 📦 Multipart Form Data

File uploads commonly use:

```http
Content-Type: multipart/form-data; boundary=----XYZ
```

The body is divided into multiple parts using a **boundary**.

### Example

```text
------XYZ
Content-Disposition: form-data; name="username"

Sarthak

------XYZ
Content-Disposition: form-data; name="file"; filename="photo.png"

<file data>

------XYZ--
```

This allows a request to contain:

- 📝 Text fields
- 🖼️ Images
- 📄 Documents
- 📦 Other files

---

# 19. 📡 Streaming

HTTP can be used to send data **progressively** instead of waiting for the entire response to be generated.

```text
Server
  │
  ├──── Chunk 1 ────► Client
  ├──── Chunk 2 ────► Client
  ├──── Chunk 3 ────► Client
  ├──── Chunk 4 ────► Client
  └──── ...          Client
```

A streaming response can use:

```http
Content-Type: text/event-stream
Connection: keep-alive
```

The connection remains open while the server continues sending data.

This is useful when data is generated continuously or incrementally.

---

# 20. 🔐 SSL, TLS & HTTPS

## SSL

**SSL (Secure Sockets Layer)** is the older security technology.

Modern systems use **TLS (Transport Layer Security)**.

---

## TLS

TLS protects communication between the client and server.

It provides:

- 🔒 Encryption
- 🛡️ Integrity
- ✅ Server authentication

---

## HTTPS

HTTPS can be understood as:

```text
HTTP
  +
TLS
  =
HTTPS
```

### HTTPS over TCP

```text
HTTP
  ↓
TLS
  ↓
TCP
  ↓
IP
```

### HTTP/3

```text
HTTP/3
   ↓
 QUIC
   ↓
 UDP
   ↓
 IP
```

---

## TLS Certificates

Certificates help the client authenticate the server.

```text
Client
   │
   │ Connect to HTTPS server
   ▼
Server
   │
   │ Certificate
   ▼
Client verifies certificate
   │
   ▼
Secure TLS connection
```

After the secure connection is established, HTTP data can be transmitted through the encrypted connection.

---

# 21. 🧩 Complete Mental Model

When you open a website or call an API, think about the process like this:

```text
                         CLIENT
                           │
                           │ HTTP Request
                           ▼
                    ┌──────────────┐
                    │   BACKEND    │
                    │    SERVER    │
                    └──────┬───────┘
                           │
                ┌──────────┼──────────┐
                ▼          ▼          ▼
            Database     Cache    Other APIs
                │          │          │
                └──────────┼──────────┘
                           │
                           ▼
                    HTTP Response
                           │
                           ▼
                         CLIENT
```

### Request

```text
┌─────────────────────────┐
│ Method                  │
│ Path                    │
│ Headers                 │
│ Body (optional)         │
└─────────────────────────┘
```

### Response

```text
┌─────────────────────────┐
│ Status Code             │
│ Headers                 │
│ Body (optional)         │
└─────────────────────────┘
```

---

# 22. 🧠 Quick Revision

| Concept | Remember |
|---|---|
| **HTTP** | Application-layer communication protocol |
| **Stateless** | Each request is independent from HTTP's perspective |
| **Request** | Method + Path + Headers + optional Body |
| **Response** | Status Code + Headers + optional Body |
| **GET** | Retrieve |
| **POST** | Create / perform operation |
| **PUT** | Replace/update |
| **PATCH** | Partial update |
| **DELETE** | Delete |
| **OPTIONS** | Communication options / CORS preflight |
| **CORS** | Controls browser cross-origin access |
| **Preflight** | `OPTIONS` request before certain cross-origin requests |
| **2xx** | Success |
| **3xx** | Redirection |
| **4xx** | Client error |
| **5xx** | Server error |
| **ETag** | Identifies a resource version |
| **304** | Resource has not changed |
| **Compression** | Reduces transferred data |
| **Persistent Connection** | Reuses a connection for multiple requests |
| **Multipart** | Useful for file uploads |
| **Streaming** | Sends data progressively |
| **HTTPS** | HTTP protected by TLS |
| **HTTP/3** | HTTP over QUIC |

---

# 🎯 Final Takeaways

> ### If you remember only these things:

1. **HTTP is an application-layer protocol.**
2. **The client sends a request; the server sends a response.**
3. **HTTP is stateless by itself.**
4. **Requests contain methods, paths, headers, and optionally a body.**
5. **Responses contain status codes, headers, and optionally a body.**
6. **CORS is a browser security mechanism for cross-origin requests.**
7. **Preflight requests use `OPTIONS`.**
8. **`2xx` = success, `4xx` = client-side/request problem, `5xx` = server-side problem.**
9. **Caching avoids unnecessary downloads.**
10. **ETag + conditional requests can produce `304 Not Modified`.**
11. **Compression reduces network payload size.**
12. **Persistent connections reduce connection overhead.**
13. **Multipart requests are commonly used for file uploads.**
14. **Streaming allows data to arrive progressively.**
15. **HTTPS = HTTP secured using TLS.**
16. **HTTP/3 uses QUIC over UDP.**

---

<p align="center">
  <b>🌐 HTTP is one of the most important foundations of backend development.</b>
</p>
