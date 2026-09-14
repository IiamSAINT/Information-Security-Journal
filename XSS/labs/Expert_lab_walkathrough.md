# Reflected XSS with Event Handlers and `href` Attributes Blocked

> **Note on Rendering Inline SVG in GitHub Markdown:**  
> GitHub sanitizes raw HTML and inline SVG elements (like `<svg>`, `<animate>`, `<script>`) rendered directly inside standard `.md` files to prevent stored XSS attacks against GitHub users. If you paste an inline `<svg>` snippet directly into Markdown without code block wrappers, GitHub's HTML parser will strip or sanitize it. Therefore, XSS vector payloads in documentation should always be enclosed within **fenced code blocks** (e.g., ````xml ... ```` or ````html ... ````) to display cleanly as code rather than attempting execution.

---

| Parameter | Details |
| :--- | :--- |
| **Lab Platform** | PortSwigger Web Security Academy |
| **Lab Name** | Reflected XSS with Event Handlers and `href` Attributes Blocked |
| **Difficulty** | Expert |
| **Vulnerability Class** | Reflected Cross-Site Scripting (XSS) |
| **Technique** | SVG SMIL Attribute Mutation Injection (`<animate>`) |

---

## 1. Executive Summary

This write-up documents an **Expert-level Reflected XSS** vulnerability. The target application implemented filtering aimed at restricting user-supplied HTML tags, event handlers, and specific attributes such as `href`. 

By fuzzing the allowed tag list and taking advantage of **SMIL (Synchronized Multimedia Integration Language)** within an SVG context, the application's filter was bypassed using the `<animate>` tag to dynamically mutate the parent `<a>` element's `href` attribute post-render.

---

## 2. Reconnaissance & Fuzzing

### Input Reflection
Initial testing confirmed that input supplied to the search parameter reflected directly into the application's HTTP response context.

### Filter Enumeration
Using **Burp Suite Intruder**, a payload set of common HTML tags, event handlers, and attributes was fuzzed against the application to identify whitelist rules:

* **Blocked:** Standard execution tags (`<script>`, `<img>`, `<iframe>`), inline event handlers (`onload`, `onerror`, `onclick`, `onmouseover`), and direct declaration of sensitive attributes like `href="..."`.
* **Allowed:** `<svg>`, `<animate>`, `<a>`, `<text>`

Because standard execution vectors were stripped or rejected by the application filter, exploitation required leveraging allowed SVG sub-components.

---

## 3. Exploitation Technique

### SMIL Animation Attribute Injection
The SVG `<animate>` element allows dynamic modification of parent element attributes using two key properties:
* `attributeName`: Identifies the target attribute to manipulate.
* `values`: Specifies the injection vector or value applied to that attribute.

By embedding `<animate>` inside an `<a>` element, the string `href="..."` never appears in the initial payload string inspected by the server side filter. When parsed by the browser DOM engine, the SVG animation engine assigns the payload to the anchor's `href` attribute.

### Payload Structure

```xml
<svg>
    <a>
        <animate attributeName="href" values="javascript:alert(1)"/>
        <text x="12" y="50">Click me</text>
    </a>
</svg>
```

---

## 4. Execution & Verification

1. The SVG payload was submitted via the search parameter.
2. The application reflected the input, bypassing the WAF/filter inspection step.
3. The browser rendered the SVG container. The internal `<animate>` tag bound the dynamic value `javascript:alert(1)` to the parent `<a>` element's `href`.
4. Clicking the rendered **"Click me"** SVG text element triggered the script payload:
   ```javascript
   alert(1)
   ```
5. The lab condition was successfully solved.

---

## 5. Root Cause & Remediation

### Root Cause
The root cause was reliance on **blacklists and incomplete tag/attribute whitelisting**. The filter inspected explicit attribute assignments in the raw string (e.g., `href=`) without accounting for secondary standard specifications (like SVG SMIL element binding) capable of populating DOM attributes post-parser.

### Remediation Strategies

1. **Context-Aware Output Encoding:** Encode user input prior to reflection in the HTML body context to prevent browser interpretation of tags.
2. **Standard Sanitization Libraries:** Instead of regex-based filtering or custom WAF rules, employ established HTML sanitization solutions such as **DOMPurify**, ensuring animation sub-elements (`<animate>`, `<set>`) and dynamic attribute setters (`attributeName`, `values`) are explicitly sanitized or disallowed.
3. **Content Security Policy (CSP):** Implement a restrictive Content Security Policy to stop unapproved inline scripts:
   ```http
   Content-Security-Policy: default-src 'self'; script-src 'self';
   ```

---

## 6. Key Takeaways

* String-matching filters often fail against complex browser specs.
* When obvious tags and event handlers are blocked, inspecting alternative markup standards (like SVG SMIL) can uncover unexpected execution paths.