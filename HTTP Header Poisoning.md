#### Hieroglyph Payload Causing 520 Errors

1. **Specificity of the Payload**:
    
    - The attack is not based on overwhelming the server with high traffic but on sending a specifically crafted payload that the server cannot handle, resulting in a 520 error.
2. **Nature of the Error**:
    
    - The 520 error indicates that the server is unable to process the request correctly due to the payload, leading to an unexpected response.
3. **Impact**:
    
    - If the 520 error can be triggered consistently with a simple request, it indicates a flaw in how the server handles certain inputs, which can be considered a logical vulnerability rather than resource exhaustion.

###

### Reporting the Vulnerability

**Title**: Potential Denial of Service (DoS) via Host Header Injection Causing 520 Errors

**Summary**: A vulnerability in the handling of the `Host` header allows an attacker to trigger a 520 error by sending specially crafted requests. This can be exploited to cause a Denial of Service (DoS) condition by making the server unable to process certain inputs.

**Description**: The application is susceptible to a DoS attack through host header injection. By sending requests with specific payloads in the `Host` or `X-Forwarded-Host` headers, the server returns a 520 error. This behavior can be exploited by an attacker to send a targeted request, leading to service disruption.

**Steps to Reproduce**:

1. Open a terminal or command prompt.
2. Use `curl` to send the following request:
    ``
    
    `curl -X GET "https://biocorellc.com/services-capabilities/analytics-ai-machine-learning" \ -H "Host: biocorellc.com" \ -H "X-Forwarded-Host: 𓅂='',𓂀=!𓅂+𓅂,𓁄=!𓂀+𓅂,𓊎=𓅂+{},𓆣=𓂀[𓅂++],𓊝=𓂀[𓇎=𓅂],𓏢=++𓇎+𓅂,𓆗=𓊎[𓇎+𓏢],𓂀[𓆗+=𓊎[𓅂]+(𓂀.𓁄+𓊎)[𓅂]+𓁄[𓏢]+𓆣+𓊝+𓂀[𓇎]+𓆗+𓆣+𓊎[𓅂]+𓊝][𓆗](𓁄[𓅂]+𓁄[𓇎]+𓂀[𓏢]+𓊝+𓆣+'𓅂 𓏢 𓂀 𓁄 𓆣 𓊝 𓇎')"`
    
    or use Burp Suite and the repeater function as shown down bellow:


![[image1 1.png]]

    
3. Observe that the server returns a 520 error.
![[image.png]]



**Impact**: An attacker can exploit this vulnerability to cause a Denial of Service (DoS) by sending a targeted request with a malicious `Host` header. This can make the server unable to process certain inputs, leading to service disruption.

**Mitigation**:

- **Input Validation**: Ensure all headers are properly validated and sanitized.
- **Error Handling**: Properly handle unexpected input to avoid triggering 520 errors.

 Does This Count as DoS Based on Resource Exhaustion?

- **Resource Exhaustion**: Typically involves high traffic volumes designed to deplete server resources (CPU, memory, bandwidth).
- **Hieroglyph Payload**: This attack does not involve high traffic volumes but exploits a specific flaw in the server's handling of certain inputs, causing it to fail.

### Conclusion: Is It In Scope?

Given the context:

- The discovered attack involves a specific payload causing a 520 error.
- The payload exploits a vulnerability in how the server processes inputs, not by overwhelming it with traffic but by causing it to return an unexpected response.

This type of attack is more akin to a logical flaw or input validation issue rather than a traditional resource exhaustion DoS attack. As such, it should be considered in scope if the bug bounty program includes vulnerabilities related to improper input handling, unexpected server responses, or logical errors that could lead to denial of service.

