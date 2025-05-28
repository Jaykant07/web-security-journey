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

---

## 🛡️ Takeaways

- Always ensure SQL queries are **parameterized**
- Avoid direct user input in queries
- Use least-privilege database accounts
- Regularly test applications with tools like **Burp Suite**

---

## 📎 Links

- [PortSwigger SQLi Labs](https://portswigger.net/web-security/sql-injection)
- [Day 3 GitHub Folder](./)

🌟 **Day 3 complete! On to Day 4...**
