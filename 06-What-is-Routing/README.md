# 🛣️ Understanding Routing for Backend Engineers

<p align="center">
  <b>Routing from the ground up — methods, routes, handlers, path parameters, query parameters, nested routes, versioning, and catch-all routes.</b>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Backend-Routing-blue?style=for-the-badge" alt="Backend Routing">
  <img src="https://img.shields.io/badge/Topic-REST%20APIs-green?style=for-the-badge" alt="REST APIs">
  <img src="https://img.shields.io/badge/Level-Beginner%20→%20Intermediate-orange?style=for-the-badge" alt="Level">
</p>

---

## 🎥 Video

> 📺 **What is Routing? — Sriniously**

These notes are based on the Sriniously video transcript, with the explanations expanded around the doubts we discussed while studying it.

---

## 📚 Table of Contents

- [1. 🧭 What is Routing?](#1--what-is-routing)
- [2. 🧠 WHAT vs WHERE](#2--what-vs-where)
- [3. 🎯 What is a Handler?](#3--what-is-a-handler)
- [4. 🔑 Method + Route = Route Matching](#4--method--route--route-matching)
- [5. 📌 Static Routes](#5--static-routes)
- [6. 🔄 Dynamic Routes](#6--dynamic-routes)
- [7. 🛣️ Path Parameters](#7--path-parameters)
- [8. ❓ Query Parameters](#8--query-parameters)
- [9. 🤔 Why Not Just Put Filters in the Path?](#9--why-not-just-put-filters-in-the-path)
- [10. 📄 Pagination with Query Parameters](#10--pagination-with-query-parameters)
- [11. 🔗 Nested Routes](#11--nested-routes)
- [12. 🤔 Can Query Parameters Do the Same Thing as Nested Routes?](#12--can-query-parameters-do-the-same-thing-as-nested-routes)
- [13. 🧩 Static + Dynamic + Nested Routes](#13--static--dynamic--nested-routes)
- [14. 🔢 Route Versioning](#14--route-versioning)
- [15. 🗑️ Deprecation](#15--deprecation)
- [16. 🕳️ Catch-All Routes](#16--catch-all-routes)
- [17. 🧠 Complete Mental Model](#17--complete-mental-model)
- [18. 🎯 Interview Revision](#18--interview-revision)
- [19. ⚡ Quick Revision](#19--quick-revision)

---

# 1. 🧭 What is Routing?

**Routing** is the process of taking an incoming request and deciding **which server-side handler should handle it**.

The key idea from the video is:

> **HTTP method = WHAT you want to do**
>
> **Route/path = WHERE you want to do it**

For example:

```http
GET /api/books
```

Here:

```text
GET          → WHAT?
               Fetch data.

 /api/books  → WHERE?
               The books resource.
```

The server uses these pieces to find the appropriate handler.

### Basic flow

```text
┌──────────────┐
│    CLIENT    │
└──────┬───────┘
       │
       │ GET /api/books
       ▼
┌────────────────────┐
│       SERVER       │
│                    │
│  1. Check method   │
│  2. Check route    │
│  3. Find handler   │
└─────────┬──────────┘
          │
          ▼
      ┌─────────┐
      │ Handler │
      └────┬────┘
           │
           ▼
   Business Logic
           │
           ▼
       Database
           │
           ▼
       Response
```

---

# 2. 🧠 WHAT vs WHERE

This is the easiest way to understand the video's introduction.

HTTP methods describe the **intent/action**.

Routes describe the **destination/resource**.

| Part | Question | Example |
|---|---|---|
| HTTP Method | **What do I want to do?** | `GET` |
| Route | **Where/which resource?** | `/api/books` |

### Example

```http
GET /api/users
```

Means roughly:

> "I want to **GET** information from the **users** resource."

Another request:

```http
POST /api/users
```

means:

> "I want to **POST/create** something in the **users** resource."

Notice something important:

```text
GET  /api/users
POST /api/users
```

The route is the same.

The **method changes the intent**.

---

# 3. 🎯 What is a Handler?

This was one of our first major doubts.

When Sriniously says:

> "The server checks the method and the route, then maps it to a handler"

**Handler = the server-side code/function responsible for handling that request.**

Think of a handler as the piece of code that says:

> "Okay, this request is mine. I'll perform the required work."

### Example

Suppose the client sends:

```http
GET /api/users/123
```

The backend may have a route definition conceptually like:

```text
GET /api/users/:id
```

which is connected to:

```text
UserHandler
```

The handler could conceptually do:

```text
1. Read id = 123
2. Query the database
3. Find user 123
4. Return the user's data
```

### Routing vs Handler

This distinction is extremely important:

```text
ROUTING
    ↓
"Which code should handle this request?"

HANDLER
    ↓
"Now that I have the request, what should I actually do?"
```

So routing itself is not necessarily doing the database operation.

It is **selecting the code that will do the operation**.

### Example flow

```text
GET /api/users/123
        │
        ▼
   Route Matching
        │
        │ matches
        ▼
GET /api/users/:id
        │
        ▼
   User Handler
        │
        ▼
Find user 123
        │
        ▼
   JSON Response
```

---

# 4. 🔑 Method + Route = Route Matching

Suppose a server has:

```text
GET  /api/books
POST /api/books
```

The route is identical:

```text
/api/books
```

but the methods differ.

Therefore, conceptually:

```text
GET  + /api/books → GetBooksHandler
POST + /api/books → CreateBookHandler
```

### Why?

Because:

```http
GET /api/books
```

could mean:

> "Give me the books."

while:

```http
POST /api/books
```

could mean:

> "Create a new book."

So the server needs both:

```text
HTTP Method
+
Route
```

to decide which handler should receive the request.

### Mental model

```text
                Incoming Request
                       │
              ┌────────┴────────┐
              ▼                 ▼
          HTTP Method         Route
              │                 │
              └────────┬────────┘
                       ▼
                Route Matching
                       │
                       ▼
                    Handler
```

> 💡 **Interview point:** Routing is not just "matching a URL." In typical HTTP APIs, the HTTP method is also part of determining which handler should execute.

---

# 5. 📌 Static Routes

A **static route** has no variable part.

Example:

```text
/api/books
```

The path stays the same.

```http
GET /api/books
```

and:

```http
POST /api/books
```

both use the static path:

```text
/api/books
```

There is no dynamic value inside it.

### Why "static"?

Because this:

```text
/api/books
```

is constant.

It does not become:

```text
/api/books/123
/api/books/456
/api/books/789
```

---

# 6. 🔄 Dynamic Routes

A **dynamic route** contains a variable part.

Example:

```http
GET /api/users/123
```

The fixed part is:

```text
/api/users/
```

The variable part is:

```text
123
```

The server can define the route conceptually as:

```text
GET /api/users/:id
```

Here:

```text
:id
```

means:

> "Some value will appear here."

Therefore all of these can match:

```text
/api/users/123
/api/users/456
/api/users/789
```

### Why is this useful?

Instead of defining:

```text
/api/users/123
/api/users/456
/api/users/789
...
```

we define one dynamic route:

```text
/api/users/:id
```

and let the incoming request provide the value.

---

# 7. 🛣️ Path Parameters

A dynamic value inside the route path is called a:

- **Path parameter**
- **Route parameter**

Example:

```text
/api/users/123
```

Conceptually:

```text
/api/users/:id
```

where:

```text
id = 123
```

### What does it mean?

Usually:

> **"I want a specific resource identified by this value."**

For example:

```http
GET /api/users/123
```

means:

> "Give me user 123."

This is a very natural use of a path parameter because `123` identifies a specific user.

### Important detail

Even if the value looks numeric:

```text
123
```

route parameters are received at the path level as strings.

So conceptually:

```text
id = "123"
```

If your application needs an integer, it can convert it.

---

# 8. ❓ Query Parameters

A query parameter appears **after `?`**.

Example:

```http
GET /api/search?query=iphone
```

Break it down:

```text
/api/search
     │
     └── Route

?
└── Query parameters begin

query=iphone
│     │
│     └── Value
└────── Key
```

So:

```text
query=iphone
```

is a **query parameter**.

Query parameters are commonly used for:

- 🔎 Searching
- 🔍 Filtering
- ↕️ Sorting
- 📄 Pagination
- ⚙️ Additional request options

The important idea is:

> **The path identifies the resource/route, while query parameters provide additional criteria or options for the request.**

---

## 🧪 Example: Cars

Suppose there are 100 cars.

If we want all cars:

```http
GET /api/cars
```

If we only want Audi cars:

```http
GET /api/cars?brand=audi
```

The server receives:

```text
Route:
    /api/cars

Query parameter:
    brand = audi
```

The handler can interpret that as:

```text
Find cars where brand = Audi
```

and return:

```json
[
  {
    "id": 12,
    "brand": "Audi",
    "model": "A4"
  },
  {
    "id": 37,
    "brand": "Audi",
    "model": "Q5"
  },
  {
    "id": 81,
    "brand": "Audi",
    "model": "A6"
  }
]
```

### ⚠️ Important

`brand=audi` does **not magically filter anything**.

The backend developer has to write the logic that interprets:

```text
brand=audi
```

as:

```text
filter cars where brand == Audi
```

The API defines what its query parameters mean.

---

# 9. 🤔 Why Not Just Put Filters in the Path?

This was one of our biggest doubts.

We asked:

> "If I want Audi cars, why can't I just do `/api/cars/audi`?"

### Answer:

**You technically can.**

You could design:

```http
GET /api/cars/audi
```

as a valid route.

But now `audi` is part of the **path**.

Conceptually:

```text
/api/cars/:brand
```

That treats `audi` as a path parameter.

Compare that with:

```http
GET /api/cars?brand=audi
```

which communicates:

> "I want the cars collection, filtered by brand."

So the issue isn't:

> "One works and the other doesn't."

Both can work.

The issue is **semantic meaning and API design**.

---

## 🧠 Better Rule

Don't memorize:

> ❌ Path = IDs  
> ❌ Query = filters

Instead remember:

> ✅ **Path parameters identify/address resources.**
>
> ✅ **Query parameters provide additional criteria/options for a request.**

### Compare

```http
GET /api/users/123
```

> "Give me **user 123**."

vs.

```http
GET /api/users?minAge=20
```

> "Give me **users satisfying an age condition**."

The first identifies a particular resource.

The second modifies/filter the collection.

---

# 10. 📄 Pagination with Query Parameters

This was another important doubt.

Suppose there are:

```text
100 books
```

and the API returns:

```text
20 books per page
```

Then:

```text
Page 1 → Books 1–20
Page 2 → Books 21–40
Page 3 → Books 41–60
Page 4 → Books 61–80
Page 5 → Books 81–100
```

The client can request:

```http
GET /api/books?page=2
```

### But why does `page=2` mean 21–40?

**It doesn't inherently mean that.**

`page=2` simply means:

> "I want page number 2."

The **server's pagination implementation** determines what items belong to page 2.

If:

```text
limit = 20
```

then:

```text
page 1 → 1–20
page 2 → 21–40
```

If:

```text
limit = 10
```

then:

```text
page 1 → 1–10
page 2 → 11–20
```

### Conceptual calculation

If:

```text
page = 2
limit = 20
```

then the offset is:

```text
offset = (page - 1) × limit
       = (2 - 1) × 20
       = 20
```

So the server starts after the first 20 records and takes the next 20.

### Key takeaway

> **`page=2` does not define the range. The server's pagination logic does.**

---

## 📦 Pagination Response

A server may return:

```json
{
  "data": [
    {"id": 21, "name": "Book 21"},
    {"id": 22, "name": "Book 22"}
  ],
  "currentPage": 2,
  "totalBooks": 100,
  "totalPages": 5
}
```

The client can use this metadata to decide what request to make next.

For example:

```http
GET /api/books?page=3
```

The video uses this as a major example of query parameters: the client requests a particular page using a query parameter while keeping the resource route the same.

---

# 11. 🔗 Nested Routes

A nested route represents a **relationship between resources**.

Example:

```http
GET /api/users/123/posts
```

This means:

> "Give me the posts belonging to user 123."

Here:

```text
/users/123
```

identifies the user.

Then:

```text
/posts
```

specifies the related resource.

---

## 🔽 Going One Level Deeper

The video's important example is:

```http
GET /api/users/123/posts/456
```

Read it from left to right:

```text
/api
  └── users
       └── 123
            └── posts
                 └── 456
```

Semantically:

> **Give me post 456 belonging to user 123.**

The URL itself communicates the relationship:

```text
User 123
    ↓
  Posts
    ↓
Post 456
```

---

## 🧩 Different Levels

### All users

```http
GET /api/users
```

→ Get all users.

### One user

```http
GET /api/users/123
```

→ Get user 123.

### All posts of user 123

```http
GET /api/users/123/posts
```

→ Get all posts belonging to user 123.

### Specific post of user 123

```http
GET /api/users/123/posts/456
```

→ Get post 456 belonging to user 123.

This is the semantic hierarchy the video is demonstrating.

---

# 12. 🤔 Can Query Parameters Do the Same Thing as Nested Routes?

This was probably our most important conceptual doubt.

We asked:

> "Can I use query parameters to do the exact same thing as `/api/users/123/posts/456`?"

### Yes — technically.

For example:

```http
GET /api/posts?postId=456&userId=123
```

The backend could interpret this as:

```text
postId = 456
userId = 123
```

and return:

> Post 456 belonging to user 123.

Therefore these two API designs **could return the same data**:

```text
/api/users/123/posts/456
```

and:

```text
/api/posts?postId=456&userId=123
```

### So why use the nested route?

Because the nested route expresses the relationship directly in the URL structure.

```text
/api/users/123/posts/456
```

reads like:

```text
User 123
   ↓
has Posts
   ↓
specifically Post 456
```

while:

```text
/api/posts?postId=456&userId=123
```

reads more like:

> "Give me posts matching these criteria."

### ⚠️ Important

There is **no technical rule** saying:

> "Nested routes can do it, query parameters cannot."

Both are API design choices.

The nested version is simply more expressive when the relationship itself is important.

---

# 13. 🧩 Static + Dynamic + Nested Routes

Consider:

```text
/api/users/123/posts/456
```

### Static portions

```text
/api/users
/posts
```

### Dynamic/path parameters

```text
123
456
```

Conceptually:

```text
/api/users/:userId/posts/:postId
```

The server can extract:

```text
userId = "123"
postId = "456"
```

### Important

A route can be:

- Static
- Dynamic
- Nested

at the same time.

These aren't mutually exclusive categories.

For example:

```text
/api/users/:userId/posts/:postId
```

is:

```text
✓ Dynamic
✓ Nested
✓ Contains static segments
```

---

# 14. 🔢 Route Versioning

The video then introduces **route/API versioning**.

Example:

```text
/api/v1/products
```

and:

```text
/api/v2/products
```

These represent different versions of the API.

---

## 🧪 Why Version?

Suppose V1 returns:

```json
{
  "data": [
    {
      "id": 1,
      "name": "Laptop",
      "price": 50000
    }
  ]
}
```

Later, requirements change.

V2 returns:

```json
{
  "data": [
    {
      "id": 1,
      "title": "Laptop",
      "price": 50000
    }
  ]
}
```

Notice:

```text
V1 → name
V2 → title
```

The response contract has changed.

If existing clients depend on:

```text
/api/v1/products
```

changing that response directly could break them.

Instead:

```text
/api/v1/products → old contract
/api/v2/products → new contract
```

---

# 15. 🗑️ Deprecation

Versioning gives clients time to migrate.

A typical workflow is:

```text
V1 released
    ↓
V2 released
    ↓
V1 marked deprecated
    ↓
Clients migrate to V2
    ↓
V1 eventually removed
```

The important benefit is that existing clients don't have to migrate instantly.

They get a window during which they can move from:

```text
V1
```

to:

```text
V2
```

### Why this matters

It allows APIs to introduce **breaking changes** in a controlled way.

Instead of:

```text
Change API
    ↓
Everything breaks
```

you can do:

```text
Old API ──────────────┐
                      │
                      ├── Migration period
                      │
New API ──────────────┘
```

---

# 16. 🕳️ Catch-All Routes

What happens if the client requests a route that doesn't exist?

Suppose the server supports:

```text
GET  /api/users
GET  /api/users/:id
GET  /api/books
POST /api/books
```

but the client sends:

```http
GET /api/whatever
```

No route matches.

A **catch-all route** is a final fallback.

Conceptually:

```text
Specific route
      ↓
Specific route
      ↓
Specific route
      ↓
Specific route
      ↓
Catch-All
      ↓
"Route not found"
```

The video describes this conceptually as a route such as:

```text
/*
```

The exact syntax varies by framework, but the idea is the same.

---

## 🎯 Why Use a Catch-All?

Instead of returning an unhelpful/default response, the server can send:

```json
{
  "error": "Route not found"
}
```

or something similar.

This gives the client a clear explanation:

> "The endpoint you requested does not exist."

### Important

The catch-all generally comes **after the specific route definitions**.

Conceptually:

```text
GET /api/users
GET /api/books
POST /api/books
...
/*
```

If a request doesn't match any of the routes above, it reaches the catch-all.

---

# 17. 🧠 Complete Mental Model

Let's put everything together.

Suppose the client sends:

```http
GET /api/users/123/posts?page=2
```

### Step 1 — HTTP Method

```text
GET
```

Question:

> **WHAT do I want?**

Answer:

> Fetch data.

---

### Step 2 — Path

```text
/api/users/123/posts
```

Question:

> **WHERE / WHICH resource?**

Answer:

> Posts belonging to user 123.

---

### Step 3 — Route Matching

The server might have:

```text
GET /api/users/:userId/posts
```

This matches.

---

### Step 4 — Extract Path Parameter

```text
userId = "123"
```

---

### Step 5 — Read Query Parameter

```text
page = "2"
```

The handler can use this to determine which page of posts to return.

---

### Step 6 — Handler

The matched handler can conceptually:

```text
1. Read userId = 123
2. Read page = 2
3. Fetch the appropriate posts
4. Apply pagination
5. Return response
```

---

## 🔥 Complete Flow

```text
                    CLIENT
                       │
                       │
                       │ GET /api/users/123/posts?page=2
                       ▼
                ┌───────────────┐
                │    SERVER     │
                └───────┬───────┘
                        │
                        ▼
                 Check HTTP Method
                        │
                        │ GET
                        ▼
                   Check Path
                        │
                        │ /api/users/123/posts
                        ▼
                 Match Route
                        │
                        │ /api/users/:userId/posts
                        ▼
              Extract Path Parameter
                        │
                        │ userId = 123
                        ▼
              Read Query Parameters
                        │
                        │ page = 2
                        ▼
                    HANDLER
                        │
                        ├── Business Logic
                        │
                        ├── Database Query
                        │
                        └── Pagination
                        │
                        ▼
                    RESPONSE
                        │
                        ▼
                     CLIENT
```

---

# 18. 🎯 Interview Revision

## ❓ What is routing?

> **Routing is the process of matching an incoming request, typically using its HTTP method and URL path, to the appropriate server-side handler.**

---

## ❓ What is a handler?

> **A handler is the server-side function/code responsible for processing a matched request and producing the appropriate response.**

---

## ❓ Why are method and route both important?

Because:

```text
GET  /api/books
POST /api/books
```

have the same path but different intentions.

So the method + path can identify different handlers.

---

## ❓ What is a static route?

A route whose path does not contain variable parameters.

```text
/api/books
```

---

## ❓ What is a dynamic route?

A route containing a variable path segment.

```text
/api/users/:id
```

Actual request:

```text
/api/users/123
```

---

## ❓ What is a path parameter?

A dynamic value embedded in the URL path.

```text
/api/users/123
```

where:

```text
id = 123
```

---

## ❓ What is a query parameter?

A key-value parameter appearing after `?`.

```text
/api/cars?brand=audi
```

Here:

```text
brand = audi
```

is a query parameter.

---

## ❓ Why use query parameters?

Common uses include:

```text
Filtering
Searching
Sorting
Pagination
Additional request options
```

---

## ❓ Can `/api/cars/audi` work?

**Yes.**

It can be a valid API design.

But it treats `audi` as part of the path, whereas:

```text
/api/cars?brand=audi
```

expresses Audi as an additional filtering criterion.

---

## ❓ Why doesn't `/api/users>20` automatically mean users above age 20?

Because URL paths don't automatically interpret `>` as a database condition.

An API might instead define:

```text
/api/users?minAge=20
```

or:

```text
/api/users?age_gt=20
```

The backend defines how those parameters are interpreted.

---

## ❓ Why does `page=2` return items 21–40?

It doesn't inherently.

`page=2` means:

> "Give me page 2."

If the server uses:

```text
limit = 20
```

then page 2 happens to contain:

```text
21–40
```

The server's pagination logic determines the range.

---

## ❓ What is a nested route?

A route whose structure represents a relationship between resources.

```text
/api/users/123/posts/456
```

Meaning:

> Post 456 belonging to user 123.

---

## ❓ Can query parameters replace a nested route?

**Technically yes.**

For example:

```text
/api/posts?postId=456&userId=123
```

could return the same resource.

But:

```text
/api/users/123/posts/456
```

makes the parent-child relationship explicit in the URL structure.

---

## ❓ What is API versioning?

Using different route versions to maintain different API contracts.

```text
/api/v1/products
/api/v2/products
```

This allows breaking changes without immediately breaking clients using the older version.

---

## ❓ What is a catch-all route?

A final fallback route that handles requests which didn't match any defined route.

Conceptually:

```text
/*
```

It can return:

```text
Route not found
```

---

# 19. ⚡ Quick Revision

| Concept | Remember |
|---|---|
| 🛣️ **Routing** | Maps incoming requests to handlers |
| 🎯 **Handler** | Code that actually handles the matched request |
| 🧠 **HTTP Method** | WHAT you want to do |
| 📍 **Route/Path** | WHERE/which resource |
| 📌 **Static Route** | No variable path segment |
| 🔄 **Dynamic Route** | Contains variable path segment |
| 🛣️ **Path Parameter** | Dynamic value in the path |
| ❓ **Query Parameter** | Additional key-value criteria/options |
| 🔎 **Filtering** | Common query-parameter use |
| 🔍 **Searching** | Common query-parameter use |
| ↕️ **Sorting** | Common query-parameter use |
| 📄 **Pagination** | Common query-parameter use |
| 🔗 **Nested Route** | Expresses resource relationships |
| 🔢 **Versioning** | Separate API contracts |
| 🗑️ **Deprecation** | Controlled migration away from old API |
| 🕳️ **Catch-All** | Fallback for unmatched routes |

---

# 🎓 The Most Important Mental Model

If you see:

```http
GET /api/users/123/posts?page=2
```

think:

```text
GET
│
├── WHAT?
│      Fetch data.
│
└── /api/users/123/posts
       │
       ├── WHERE?
       │      Posts
       │
       ├── userId = 123
       │      Path parameter
       │
       └── page = 2
              Query parameter
```

Then:

```text
Method + Path
      ↓
Route Matching
      ↓
Handler
      ↓
Use path/query parameters
      ↓
Business Logic
      ↓
Database
      ↓
Response
```

---

<p align="center">
  <b>🛣️ Routing is essentially about deciding where an HTTP request should go and which server-side code should handle it.</b>
</p>
