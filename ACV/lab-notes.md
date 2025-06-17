# 🛡️ Access Control Labs Walkthrough

This repository contains solutions and notes for the **Access Control** labs from PortSwigger’s Web Security Academy. Each lab simulates a real-world vulnerability related to broken or misconfigured access controls.

---

## 🔐 Access Control Vulnerabilities – Summary

**Access Control** determines whether a user is authorized to perform an action or access a resource. It's enforced after authentication and session management.

### ⚠️ What is Privilege Escalation?

Privilege escalation occurs when a user gains access to functions or data beyond their intended level — often by exploiting flawed access controls.

### 🔄 Types of Access Control

- **Vertical Access Control**  
  Restricts access based on user roles.  
  _Example: Admins can delete users; regular users cannot._

- **Horizontal Access Control**  
  Restricts access to resources owned by users.  
  _Example: Users can only view their own bank transactions._

- **Context-Dependent Access Control**  
  Depends on the application state or user workflow.  
  _Example: Users cannot edit a cart after payment is completed._

### 🔒 Why It Matters

Broken access controls are common and critical vulnerabilities. They allow attackers to:

- Bypass restrictions
- View/modify other users’ data
- Gain admin-level access

---

## Lab1: Unprotected Admin Functionality 🟢 _Apprentice_

## 🎯 Goal

Access the unprotected admin panel and delete the user `carlos`.

## 🛠️ How We Achieved It

1. Appended `/robots.txt` to the lab URL to discover hidden paths.
2. Found a `Disallow: /administrator-panel` entry revealing the admin panel location.
3. Navigated directly to `/administrator-panel` in the browser.
4. Clicked the option to delete user `carlos` — no authentication or protection was enforced.

---

## Lab2: Unprotected Admin Functionality with Unpredictable URL 🟢 _Apprentice_

## 🎯 Goal

Find the hidden admin panel URL and use it to delete the user `carlos`.

## 🛠️ How We Achieved It

1. Viewed the page source (or used Burp Suite) on the lab’s home page.
2. Found a JavaScript snippet that revealed the full path to the admin panel.
3. Navigated to the disclosed admin URL.
4. Used the exposed panel to delete user `carlos`.

---

## Lab3: User Role Controlled by Request Parameter 🟢 _Apprentice_

## 🎯 Goal

Access the admin panel by forging a cookie and delete the user `carlos`.

## 🛠️ How We Achieved It

1. Logged in as `wiener:peter`.
2. Intercepted the login response using Burp Suite.
3. Modified the `Admin=false` cookie in the response to `Admin=true`.
4. Accessed `/admin` successfully and deleted user `carlos`.

---

## Lab4: User Role Can Be Modified in User Profile 🟢 _Apprentice_

## 🎯 Goal

Modify your own user role to admin (roleid 2) and delete the user `carlos`.

## 🛠️ How We Achieved It

1. Logged in as `wiener:peter` and navigated to the account settings.
2. Captured the email update request and sent it to Burp Repeater.
3. Injected `"roleid": 2` into the JSON body of the request.
4. Verified the role was updated.
5. Accessed `/admin` and deleted user `carlos`.

---

## Lab5: URL-based Access Control Can Be Circumvented 🔵 _Practitioner_

## 🎯 Goal

Bypass front-end access control to access the admin panel and delete user `carlos`.

## 🛠️ How We Achieved It

1. Attempted to access `/admin` and saw a blocked response from a front-end system.
2. Sent the request to Burp Repeater and changed the URL path to `/`.
3. Added the header `X-Original-URL: /admin` to route the request to the admin panel.
4. Then modified the header to `X-Original-URL: /admin/delete?username=carlos`.
5. Successfully triggered the delete action and solved the lab.

---

## Lab6: Method-based Access Control Can Be Circumvented 🔵 _Practitioner_

## 🎯 Goal

Promote the user `wiener` to an administrator by bypassing flawed method-based access control.

## 🛠️ How We Achieved It

1. Logged in as `administrator:admin` to access the admin panel and capture the **promote user** request in Burp Repeater.
2. Logged in as `wiener:peter` in a separate browser and copied the session cookie.
3. Replaced the session cookie in the Repeater request with `wiener`'s cookie and confirmed that the server returned `Unauthorized`.
4. Changed the HTTP method from `POST` to `GET` in the Repeater request and moved the `username=wiener` parameter to the query string.
5. Resent the request — the server accepted it and promoted `wiener` to admin.

---

## Lab7: User ID Controlled by Request Parameter 🟢 _Apprentice_

## 🎯 Goal

Access another user's account by manipulating the request parameter and retrieve the API key for `carlos`.

## 🧠 Vulnerability Type

- 🔓 Insecure Direct Object Reference (IDOR)
- 🔁 Horizontal Privilege Escalation

## 📚 Key Concept

This lab demonstrates **horizontal privilege escalation** via **IDOR**. The application uses a user-controllable parameter (`id`) to fetch account data. Without proper authorization checks, an attacker can modify this value to access data belonging to other users.

## 🛠️ How We Achieved It

1. Logged in as `wiener:peter` and visited the account page.
2. Observed the `id` parameter in the URL or request (e.g., `/my-account?id=wiener`).
3. Sent the request to Burp Repeater and changed the `id` to `carlos`.
4. Retrieved the API key for `carlos` and submitted it to solve the lab.

---

## Lab8: User ID Controlled by Request Parameter (with Unpredictable User IDs) 🟢 _Apprentice_

## 🎯 Goal

Access another user's account using a GUID-based ID(Globally Unique Identifier) and retrieve the API key for `carlos`.

## 🧠 Vulnerability Type

- 🔓 Insecure Direct Object Reference (IDOR)
- 🔁 Horizontal Privilege Escalation

## 📚 Key Concept

This lab demonstrates **horizontal privilege escalation** where user identifiers are **unpredictable** GUIDs instead of numeric IDs. Even with non-guessable IDs, an attacker can still exploit the application if those identifiers are exposed elsewhere (e.g., blog posts, reviews).

## 🛠️ How We Achieved It

1. Browsed the website and found a blog post written by `carlos`.
2. Clicked on `carlos`'s name and copied the GUID from the URL (e.g., `/user/98f6ae1c-...`).
3. Logged in as `wiener:peter` using provided credentials.
4. Accessed the account page request and modified the `id` parameter to `carlos`'s GUID.
5. Retrieved the API key from his account and submitted it to solve the lab.

---

## Lab9: User ID Controlled by Request Parameter with Data Leakage in Redirect 🟢 _Apprentice_

## 🎯 Goal

Access the user `carlos`'s account indirectly and retrieve his API key despite the application redirecting unauthorized access.

## 🧠 Vulnerability Type

- 🔓 Insecure Direct Object Reference (IDOR)
- 🔁 Horizontal Privilege Escalation
- 🕵️‍♂️ Information Disclosure via Redirect Response

## 📚 Key Concept

This lab demonstrates that **even when an app properly redirects unauthorized users**, it may still leak **sensitive data** (like API keys) inside the **HTTP response body** before the redirect is followed. Such leakage can be exploited by inspecting raw responses.

## 🛠️ How We Achieved It

1. Logged in using credentials `wiener:peter`.
2. Navigated to the account page and sent the request to **Burp Repeater**.
3. Modified the `id` parameter to `carlos`.
4. Although the server responded with a redirect to the homepage, the **response body still contained carlos’s API key**.
5. Extracted the key and submitted it to solve the lab.

---

## Lab10: User ID Controlled by Request Parameter with Password Disclosure 🟢 _Apprentice_

## 🎯 Goal

Retrieve the **administrator’s password** and use it to **delete the user `carlos`**.

## 🧠 Vulnerability Type

- 🔓 Insecure Direct Object Reference (IDOR)
- 🔁 Horizontal to Vertical Privilege Escalation
- 🔐 Sensitive Information Disclosure

## 📚 Key Concept

This lab shows how a **horizontal privilege escalation** (accessing another user’s account) can lead to **vertical privilege escalation** by exposing privileged information (e.g., passwords). If the targeted user is an admin, this allows full access to protected functions.

## 🛠️ How We Achieved It

1. Logged in as `wiener:peter` and navigated to the account page.
2. Modified the `id` parameter in the URL to `administrator`.
3. Intercepted and inspected the response using **Burp Suite**.
4. Found the administrator’s password **prefilled** in a masked input field in the HTML.
5. Logged in as `administrator` using the recovered password.
6. Accessed the admin panel and deleted user `carlos`.

---

## Lab11: Insecure Direct Object References 🟢 _Apprentice_

## 🎯 Goal

Find the **password for user `carlos`** by exploiting an insecure direct object reference and **log into their account**.

## 🧠 Vulnerability Type

- 🔓 Insecure Direct Object Reference (IDOR)
- 📁 File-based Access Control Bypass

## 📚 Key Concept

IDOR occurs when an application exposes internal object references (like filenames, user IDs, etc.) and fails to enforce proper access control. In this case, chat transcripts are directly accessible via predictable filenames, allowing unauthorized access to other users' private data.

## 🛠️ How We Achieved It

1. Navigated to the **Live chat** tab and sent a test message.
2. Clicked **View transcript**, which opened a `.txt` file containing the chat log.
3. Observed the URL pattern (e.g., `/files/10.txt`) and inferred the filename format was based on incremental numbers.
4. Changed the filename to `1.txt` and viewed its content.
5. Found `carlos`'s password within the transcript.
6. Returned to the main login page and logged in using the stolen credentials to solve the lab.

---

## Lab12: Multi-step Process with No Access Control on One Step 🔵 _Practitioner_

## 🎯 Goal

Exploit the flawed multi-step admin workflow to **promote yourself (wiener)** to an administrator.

## 🧠 Vulnerability Type

- 🔓 Broken Access Control (Multi-step Logic Flaw)
- 🔁 Privilege Escalation via Incomplete Access Validation

## 📚 Key Concept

Some multi-step processes apply access control only at the earlier steps, assuming users will follow the correct path. If any one step lacks proper checks, an attacker can bypass previous validations and directly interact with the vulnerable step.

## 🛠️ How We Achieved It

1. **Logged in as `administrator:admin`** to explore the admin panel.
2. Navigated to the section where a user like `carlos` can be promoted to admin.
3. Captured the **final confirmation request** (Step 3) in **Burp Repeater**.
4. **Logged out** and **logged in as `wiener:peter`** in a private/incognito session.
5. Extracted the `session` cookie of the `wiener` user.
6. Reused the previously captured admin promotion request:
   - Replaced the `session` cookie with `wiener`'s.
   - Changed the username from `carlos` to `wiener`.
7. Sent the request — access control was not enforced on this final step.
8. Confirmed that `wiener` was promoted to **admin** and solved the lab.

---

## Lab13: Referer-based Access Control 🔵 _Practitioner_

## 🎯 Goal

Exploit weak referer-based validation to **promote yourself (wiener)** to an administrator.

## 🧠 Vulnerability Type

- 🔓 Broken Access Control
- 🔁 Insecure Referer Header Validation
- 🚀 Horizontal to Vertical Privilege Escalation

## 📚 Key Concept

Some applications rely on the `Referer` header to verify if a request originated from an authorized source (e.g., `/admin`). This is insecure because **attackers can manually set or spoof the Referer** using tools like Burp Suite. If access to critical actions (like role changes) depends solely on the presence of a Referer header, it's vulnerable.

## 🛠️ How We Achieved It

1. **Logged in as `administrator:admin`** and visited the admin panel.
2. Located the functionality to **promote a user** (e.g., `carlos`) and captured the request in **Burp Repeater**.
3. Opened a **private/incognito session** and logged in as `wiener:peter`.
4. 4. Navigated directly to `/admin-roles?username=carlos&action=upgrade` and confirmed the request failed.
5. In Burp Repeater:

- **Replaced the session cookie** with `wiener`'s.
- Changed the `username=carlos` to `username=wiener`.
- **Added the header**:
  ```
  Referer: https://<LAB-ID>.web-security-academy.net/admin
  ```

6. Replayed the request — access was granted, and `wiener` was successfully promoted to **admin**.

---

## 🌍 Location-Based Access Control

Some applications enforce access control based on a user's **geographic location**, especially in sectors like banking or streaming services where **legal or business restrictions** apply. These controls, however, are often weak and **can be bypassed** using:

- 🔁 Web proxies
- 🛡️ VPNs
- 🗺️ Spoofed client-side geolocation

### 🔐 Preventing Access Control Vulnerabilities

To effectively secure access to sensitive resources, always follow these principles:

1. ❌ **Don't rely on obfuscation alone** for protecting access.
2. 🚫 **Deny access by default** unless the resource is explicitly public.
3. 🛡️ **Centralize access control enforcement** using a uniform mechanism across the application.
4. 📜 **Require developers to explicitly declare allowed access** to every resource in the codebase.
5. 🧪 **Rigorously test and audit** access controls to verify they work as intended.

---

## 🔐 Access Control Vulnerabilities — Summary

Access control vulnerabilities occur when users can perform actions or access data **outside their intended permissions**. These flaws arise due to **insufficient enforcement** of user roles, session context, or request validation.

### 🧨 Common Types:

- **Horizontal Privilege Escalation** 🔁  
  Gaining access to **other users' resources** of the same privilege level.

- **Vertical Privilege Escalation** ⬆️  
  Performing actions reserved for **higher-privileged roles** (e.g., admin functions).

- **Insecure Direct Object References (IDOR)** 🔍  
  Accessing unauthorized data or functions by **manipulating input parameters** like IDs or filenames.

- **Referer/Location-based Access Control** 🌍  
  Relying on headers or geolocation that can be **spoofed** by attackers.

- **Flawed Multi-Step Processes** 🔄  
  Missing access checks on intermediate or final steps allows attackers to **bypass restrictions**.

### 🔐 Why it Matters:

- Exposes **sensitive data**
- Enables **account takeover**
- Allows **unauthorized actions** like privilege escalation or user deletion

### ✅ Prevention Tips:

- 🧱 Enforce access control **on the server side**, not just the client.
- 🚫 **Deny by default** unless explicitly allowed.
- 🎯 Use a **centralized access control mechanism**.
- 🧪 Perform thorough **testing and code review** for access control logic.

---

Access control flaws are critical and common — mastering them is a key step in becoming a **skilled web security professional**.

---

## 📅 Timeline

- **Start Date:** Jun 17, 2025 **End Date**

🌟 **Access control vulnerabilities and privilege escalation complete! On to next...**
