Cross-Site Scripting (XSS)

Category: Web Security

Introduction

Cross-Site Scripting (XSS) is a web application vulnerability that occurs when an application allows untrusted data to reach a browser in a context where the browser interprets that data as code.

At a basic level, the problem is not simply that an application accepts malicious input. Web applications accept user input all the time. The security issue arises when that input crosses the boundary between data and executable content.

An attacker who is able to control content that is interpreted as JavaScript may be able to execute code in another user's browser under the security context of the vulnerable web application.

Understanding XSS therefore requires understanding how data moves through an application and, eventually, how the browser parses and interprets that data.

How XSS Occurs

A simplified XSS flow looks like this:

Attacker-controlled input
        |
        v
   Web application
        |
        v
Unsafe output / DOM manipulation
        |
        v
      Browser
        |
        v
Code interpreted as executable content

The important part of this process is the point at which attacker-controlled data is inserted into the page.

For example, an application might take a value supplied by a user and insert it directly into an HTML document. If the application does not properly handle that value, the browser may interpret part of the input as HTML or JavaScript rather than treating it purely as data.

This is why XSS is closely related to the way browsers parse HTML, attributes, JavaScript, CSS, and URLs.

Types of XSS
Reflected XSS

Reflected XSS occurs when malicious input is supplied as part of a request and is immediately reflected in the application's response.

A simplified flow is:

Attacker-controlled request
          |
          v
   Vulnerable server
          |
          v
  Malicious input reflected
          |
          v
        Browser

A common example is a search parameter that is inserted into the response without appropriate output encoding.

The attack generally requires the victim to interact with a crafted request.

Stored XSS

Stored XSS occurs when malicious input is persisted by an application and subsequently returned to users.

The input could potentially be stored in places such as comments, profiles, messages, or database records.

Attacker input
      |
      v
Application
      |
      v
   Storage
      |
      v
Victim requests content
      |
      v
Payload returned to browser

Stored XSS can be more significant because the attacker does not necessarily need to target each victim individually. Anyone who loads the affected content may be exposed.

DOM-Based XSS

DOM-based XSS differs from reflected and stored XSS because the vulnerable behavior can occur entirely within client-side JavaScript.

A common pattern is:

Attacker-controlled source
          |
          v
   Client-side JavaScript
          |
          v
     Dangerous sink
          |
          v
       DOM change

For example, client-side code might read attacker-controlled data from a URL and place it into the page using a dangerous DOM operation.

This makes understanding JavaScript sources and sinks important when researching DOM-based XSS.

XSS Is Context-Dependent

One of the most important aspects of XSS is that there is no universal payload that works against every vulnerable application.

The context in which the input is inserted determines how the browser interprets it.

For example, attacker-controlled data could appear inside:

HTML content
An HTML attribute
JavaScript
CSS
A URL
A DOM operation

Each context has different parsing rules and therefore requires a different way of thinking about the vulnerability.

This leads to an important question when testing for XSS:

Where does my input reach, and how does the browser interpret it?

Finding the location of the input is only the beginning. The next step is understanding the parser that will process it.

Sources and Sinks

A useful way to reason about DOM-based XSS is through the concept of sources and sinks.

A source is a location from which attacker-controlled data can originate.

Examples include:

URL parameters
URL fragments
document.referrer
Web storage
PostMessage data

A sink is a location where that data is used in a potentially dangerous way.

Examples include:

innerHTML
document.write()
eval()

The existence of a source or sink by itself does not necessarily mean that an application is vulnerable. The security impact depends on whether attacker-controlled data can travel from a source to a dangerous sink without appropriate handling.

This source-to-sink relationship is particularly useful when analyzing client-side applications.

Why XSS Is Possible

At its core, XSS is a failure to maintain a clear separation between untrusted data and executable content.

The browser ultimately receives a document or executes client-side code. It does not inherently know whether a particular piece of content came from a trusted developer, a legitimate user, or an attacker.

If an application constructs that content unsafely, the browser follows its normal parsing rules.

This means that understanding XSS requires more than memorizing payloads. It requires understanding:

HTTP requests and responses
HTML parsing
JavaScript execution
DOM manipulation
Browser security boundaries
Output encoding
Application data flow
Security Impact

The impact of XSS depends heavily on the application and the context in which the vulnerability exists.

Potential consequences can include:

Performing actions as the victim
Modifying content displayed to the victim
Accessing data available to client-side JavaScript
Phishing through modification of trusted pages
Compromising application functionality available to the victim
In some situations, contributing to account compromise

The actual impact should therefore be evaluated based on what the vulnerable application's JavaScript context and the victim's privileges allow.

Defending Against XSS

The primary defense is to ensure that untrusted data is not interpreted as executable content.

A major principle is context-aware output encoding. Data should be encoded according to the context in which it will be inserted.

Other important defenses include:

Using safe DOM APIs
Avoiding unnecessary use of dangerous sinks
Properly handling user-generated content
Applying appropriate Content Security Policy
Using framework security features correctly
Validating input where appropriate

Input validation can be useful, but it should not be treated as a complete replacement for correct output handling.

What I Learned

The most important thing I took from studying XSS is that it is less about memorizing JavaScript payloads and more about understanding how an application handles data.

When investigating an XSS vulnerability, I should be able to answer:

Where does the input originate?
Where does the input travel?
In what context does it reach the browser?
How does the browser parse that context?
Is the input encoded, sanitized, or otherwise handled safely?
If it reaches a dangerous sink, what can actually be achieved?

This way of thinking should make it easier to move from simply identifying XSS to understanding why the vulnerability exists.

Further Research

The next step is to put these concepts into practice through controlled labs.

Areas I want to investigate include:

Reflected XSS
Stored XSS
DOM-based XSS
XSS contexts
Filter and sanitization bypasses
Content Security Policy
XSS exploitation and impact
Browser parsing behavior