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

# Lab1: Unprotected Admin Functionality 🟢 _Apprentice_

## 🎯 Goal

Access the unprotected admin panel and delete the user `carlos`.

## 🛠️ How We Achieved It

1. Appended `/robots.txt` to the lab URL to discover hidden paths.
2. Found a `Disallow: /administrator-panel` entry revealing the admin panel location.
3. Navigated directly to `/administrator-panel` in the browser.
4. Clicked the option to delete user `carlos` — no authentication or protection was enforced.

---

# Lab2: Unprotected Admin Functionality with Unpredictable URL 🟢 _Apprentice_

## 🎯 Goal

Find the hidden admin panel URL and use it to delete the user `carlos`.

## 🛠️ How We Achieved It

1. Viewed the page source (or used Burp Suite) on the lab’s home page.
2. Found a JavaScript snippet that revealed the full path to the admin panel.
3. Navigated to the disclosed admin URL.
4. Used the exposed panel to delete user `carlos`.

---

# Lab3: User Role Controlled by Request Parameter 🟢 _Apprentice_

## 🎯 Goal

Access the admin panel by forging a cookie and delete the user `carlos`.

## 🛠️ How We Achieved It

1. Logged in as `wiener:peter`.
2. Intercepted the login response using Burp Suite.
3. Modified the `Admin=false` cookie in the response to `Admin=true`.
4. Accessed `/admin` successfully and deleted user `carlos`.

---

# Lab4: User Role Can Be Modified in User Profile 🟢 _Apprentice_

## 🎯 Goal

Modify your own user role to admin (roleid 2) and delete the user `carlos`.

## 🛠️ How We Achieved It

1. Logged in as `wiener:peter` and navigated to the account settings.
2. Captured the email update request and sent it to Burp Repeater.
3. Injected `"roleid": 2` into the JSON body of the request.
4. Verified the role was updated.
5. Accessed `/admin` and deleted user `carlos`.

---

# Lab5: URL-based Access Control Can Be Circumvented 🔵 _Practitioner_

## 🎯 Goal

Bypass front-end access control to access the admin panel and delete user `carlos`.

## 🛠️ How We Achieved It

1. Attempted to access `/admin` and saw a blocked response from a front-end system.
2. Sent the request to Burp Repeater and changed the URL path to `/`.
3. Added the header `X-Original-URL: /admin` to route the request to the admin panel.
4. Then modified the header to `X-Original-URL: /admin/delete?username=carlos`.
5. Successfully triggered the delete action and solved the lab.

---

# Lab6: Method-based Access Control Can Be Circumvented 🔵 _Practitioner_

## 🎯 Goal

Promote the user `wiener` to an administrator by bypassing flawed method-based access control.

## 🛠️ How We Achieved It

1. Logged in as `administrator:admin` to access the admin panel and capture the **promote user** request in Burp Repeater.
2. Logged in as `wiener:peter` in a separate browser and copied the session cookie.
3. Replaced the session cookie in the Repeater request with `wiener`'s cookie and confirmed that the server returned `Unauthorized`.
4. Changed the HTTP method from `POST` to `GET` in the Repeater request and moved the `username=wiener` parameter to the query string.
5. Resent the request — the server accepted it and promoted `wiener` to admin.

---
