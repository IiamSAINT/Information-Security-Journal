Reflected XSS

Lab: PortSwigger Web Security Academy
Difficulty: Apprentice
Type: Reflected XSS

Vulnerability

The application reflected user-controlled input directly into the HTTP response without proper output encoding.

Payload
<Script> alert(1) </script>
Result

The payload was reflected back into the page and executed as JavaScript, triggering an alert box.

Root Cause

User input was treated as trusted HTML instead of untrusted data.

Client Input
     ↓
Application
     ↓
Input reflected directly
     ↓
Browser
     ↓
JavaScript execution
Mitigation
Context-aware output encoding
Validate/sanitize input where appropriate
Avoid inserting untrusted input into executable HTML/JavaScript contexts
Key Takeaway

Reflected XSS occurs when attacker-controlled input is returned in a response and the browser interprets it as executable content.