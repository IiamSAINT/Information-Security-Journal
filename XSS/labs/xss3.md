Reflected XSS with Some SVG Markup Allowed

Lab: PortSwigger Web Security Academy
Difficulty: Practitioner
Type: Reflected XSS

Vulnerability

The search functionality reflected user-controlled input. Common HTML tags were blocked, but some SVG elements and event handlers were still allowed.

Reconnaissance

Similar to the previous lab, I tested different tags and attributes to identify what was being filtered and what remained whitelisted.

The difference here was that the application allowed the SVG <animateTransform> element together with the onbegin event handler.

Payload

<svg><animateTransform attributeName="transform" onbegin="alert(1)"/></svg>

Result

The injected SVG element was processed by the browser and the onbegin event executed:

alert(1)

The alert was triggered and the lab was solved.

Root Cause

The application's XSS filter blocked common HTML markup but failed to account for executable SVG elements and event handlers.

Key Takeaway

SVG provides additional elements and event handlers that can become XSS vectors when filtering is based on a limited whitelist of HTML tags.

This lab was similar to the previous one, but instead of using <animate> to manipulate an attribute, I used <animateTransform> with the onbegin event to trigger JavaScript execution.