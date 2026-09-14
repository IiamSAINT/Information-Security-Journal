Reflected XSS with Event Handlers and href Attributes Blocked

Lab: PortSwigger Web Security Academy
Difficulty: Expert
Type: Reflected XSS

Vulnerability

The search functionality reflected user-controlled input, but the application applied filtering that blocked a number of HTML tags, event handlers, and href attributes.

Some HTML tags and attributes were still allowed.

Reconnaissance

I first confirmed that the search parameter was reflected in the response.

I then tested different HTML tags and attributes to determine what was being filtered and what remained available.

Using Burp Suite Intruder, I fuzzed different HTML tags against the application's filter.

This revealed that the following tags were whitelisted:

<svg>
<animate>

At this point, I had to investigate how SVG and the <animate> element could be used to manipulate attributes without directly specifying them.

Technique

The <animate> element can dynamically modify an attribute using:

attributeName
values

This provided a way to indirectly set the href attribute without writing:

href="javascript:alert(1)"

The href attribute itself was blocked by the filter.

Payload
<svg>
    <a>
        <animate attributeName="href" values="javascript:alert(1)"/>
        <text x="12" y="50">
            Click me
        </text>
    </a>
</svg>

The lab also required the final payload to contain the click keyword.

Result

The payload bypassed the filtering restrictions and created a clickable SVG element.

Clicking the injected "Click me" text triggered:

alert(1)

The lab was successfully solved.

Root Cause

The application relied on a restrictive whitelist to prevent XSS, but the filtering did not account for all ways in which allowed HTML/SVG elements could be combined to produce executable behavior.

The vulnerability was therefore not simply caused by allowing <svg> or <animate>, but by the filter failing to understand the security implications of how these elements interact.

Key Takeaway

This lab reinforced that XSS filtering is difficult to implement reliably.

When common payloads and obvious event handlers are blocked, understanding the underlying HTML/SVG specifications can reveal alternative ways to reach the same dangerous behavior.