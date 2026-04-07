# 🍪 Complete Notes on Cookies (Frontend + Backend + Security)

---

# 🧠 What is a Cookie?

A **cookie** is a small piece of data:

* Sent by the **backend (server)**
* Stored by the **browser**
* Automatically sent back to the server with every request

👉 Used to maintain **state** (like login session)

---

# 🚨 Why Cookies Are Needed

HTTP is **stateless**:

* Server forgets everything after each request

👉 Cookies solve this:

* Identify user across multiple requests
* Maintain login sessions

---

# 🔁 Complete Flow (End-to-End)

---

## 🧩 Step 1: Frontend → Backend (Login Request)

```http
POST /login
Content-Type: application/json

{
  "email": "user@gmail.com",
  "password": "1234"
}
```

---

## 🧩 Step 2: Backend → Frontend (Set Cookie)

```http
HTTP/1.1 200 OK
Set-Cookie: sessionId=abc123; HttpOnly; Secure; SameSite=Lax
```

### Backend Responsibility:

* Create session
* Generate session ID
* Send it via `Set-Cookie`

---

## 🧠 Step 3: Browser Stores Cookie

Stored internally:

```
Domain: example.com
Key: sessionId
Value: abc123
```

👉 No frontend code needed

---

## 🧩 Step 4: Frontend → Backend (Next Request)

```http
GET /profile
```

---

## 🧠 Step 5: Browser Automatically Adds Cookie

```http
Cookie: sessionId=abc123
```

👉 Automatically handled by browser

---

## 🧩 Step 6: Backend Uses Cookie

```text
sessionId = abc123
→ Fetch user
→ Return response
```

---

# 🧠 Who Does What?

---

## 🖥️ Frontend (React / UI)

### Does:

* Send HTTP requests
* Enable credentials (if cross-origin)

### Does NOT:

* Store cookies ❌
* Attach cookies ❌

---

## 🌐 Browser (Most Important)

### Responsibilities:

* Store cookies
* Attach cookies automatically
* Enforce security rules

---

## 🛠️ Backend (Server)

### Does:

* Send cookies (`Set-Cookie`)
* Read cookies (`Cookie`)
* Use cookies for authentication

---

# 🔥 How Cookies Are Sent (Frontend Code)

---

## ✅ Fetch Example

```js
fetch("http://localhost:8080/profile", {
  method: "GET",
  credentials: "include"
});
```

👉 Required for cross-origin requests

---

# 🔥 Backend Code (Spring Boot)

---

## ✅ Set Cookie

```java
@PostMapping("/login")
public ResponseEntity<String> login(HttpServletResponse response) {

    Cookie cookie = new Cookie("sessionId", "abc123");
    cookie.setHttpOnly(true);
    cookie.setSecure(true);
    cookie.setPath("/");
    cookie.setMaxAge(3600);

    response.addCookie(cookie);

    return ResponseEntity.ok("Login successful");
}
```

---

## ✅ Read Cookie

```java
@GetMapping("/profile")
public String getProfile(@CookieValue("sessionId") String sessionId) {
    return "Session: " + sessionId;
}
```

---

## ✅ Alternative (Manual)

```java
@GetMapping("/profile")
public String getProfile(HttpServletRequest request) {
    for (Cookie cookie : request.getCookies()) {
        if (cookie.getName().equals("sessionId")) {
            return cookie.getValue();
        }
    }
    return "No session";
}
```

---

# 🌐 CORS Configuration (Important)

```java
@Configuration
public class CorsConfig {

    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return registry -> registry.addMapping("/**")
                .allowedOrigins("http://localhost:3000")
                .allowedMethods("*")
                .allowCredentials(true);
    }
}
```

---

# 🔍 How Browser Decides Which Cookies to Send

Browser checks:

---

## 1️⃣ Domain

* `example.com` → sent to `example.com` ✅
* `example.com` → not sent to `google.com` ❌

---

## 2️⃣ Path

```
Path=/settings
```

* `/settings/profile` → ✅
* `/dashboard` → ❌

---

## 3️⃣ Secure

```
Secure
```

* HTTPS → ✅
* HTTP → ❌

---

## 4️⃣ SameSite

| Type   | Behavior                        |
| ------ | ------------------------------- |
| Strict | Only same-site                  |
| Lax    | Partial cross-site              |
| None   | Fully cross-site (needs Secure) |

---

# 🔐 Cookie Attributes (Important)

---

## 1️⃣ HttpOnly

```
HttpOnly
```

* JS cannot read cookie
* Protects from XSS

---

## 2️⃣ Secure

```
Secure
```

* Only sent over HTTPS

---

## 3️⃣ SameSite

```
SameSite=Lax
```

* Prevents CSRF attacks

---

## 4️⃣ Max-Age

```
Max-Age=3600
```

* Cookie expiry time

---

# ⚠️ Important Rules

---

## 🔥 1. Cookies are Automatic

```js
// ❌ NOT allowed
headers: {
  Cookie: "sessionId=abc"
}
```

👉 Browser handles cookies

---

## 🔥 2. Cookies are Domain-Specific

* Only sent to matching domain
* Cannot be read by other websites

---

## 🔥 3. Browser Controls Everything

* Storage
* Sending
* Security

---

# 🔐 Security Concepts

---

## 1️⃣ CSRF

* Cookies auto-send → vulnerable
* Solution:

  * CSRF token
  * SameSite cookies

---

## 2️⃣ XSS

* JS can steal cookies (if not HttpOnly)
* Solution:

  * HttpOnly

---

# ⚡ Cookies vs LocalStorage

| Feature   | Cookies  | LocalStorage |
| --------- | -------- | ------------ |
| Auto sent | ✅ Yes    | ❌ No         |
| JS access | Optional | ✅ Yes        |
| Security  | High     | Lower        |

---

# 🧠 Deep Understanding

> Cookies are the **bridge between frontend and backend state**

* Backend → creates identity
* Browser → stores identity
* Backend → verifies identity

---

# ⚡ Real Analogy

* Backend = Office 🏢
* Cookie = ID card 🪪
* Browser = Person

👉 Every request:

* Browser shows ID card automatically
* Backend identifies user

---

# 🎯 Final Summary

> Cookies are created by the backend, stored by the browser, automatically sent with requests, and used by the backend to maintain user sessions and identity.

---

# 💡 Interview One-Liner

> “Cookies are browser-managed data set by the backend, automatically sent with requests, and used to maintain session state in web applications.”

---

# 🚀 Key Takeaways

* Frontend → sends request + enables credentials
* Browser → stores & sends cookies automatically
* Backend → sets & reads cookies
* Cookies are domain-based and secure by design
* Used for session management and authentication

---
