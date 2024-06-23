# HTTP-Request-Smuggling

HTTP Request Smuggling, also known as HTTP Desync Attacks, is an attack technique for interfering with the way a website processes sequences of HTTP requests that are received from one or more users. It allows the attacker to "smuggle" a request to a web server without the devices between the attacker and the web server being aware of it. 

## Types of Request Smuggling

### 1. CL.TE (Content-Length and Transfer-Encoding)

This attack occurs when one server uses the `Content-Length` header to determine the end of an HTTP request, while another server uses `Transfer-Encoding: chunked`.

**Example:**
```http
POST / HTTP/1.1
Host: example.com
Content-Length: 13
Transfer-Encoding: chunked

0


```
SMUGGLED

Here, the first server might consider the request complete due to Content-Length, while the second server processes the "SMUGGLED" data as a new request.


### 2. TE.CL (Transfer-Encoding and Content-Length)

This is the reverse of the CL.TE attack. The first server uses `Transfer-Encoding: chunked` to determine the end of the request, and the second server uses `Content-Length`.

**Example:**
```http
POST / HTTP/1.1
Host: example.com
Transfer-Encoding: chunked
Content-Length: 3

5
GET / HTTP/1.1
Host: example.com

0
```

### 3. CL.CL (Multiple Content-Length headers)

This attack exploits differences in the interpretation of multiple `Content-Length` headers between servers.

**Example:**
```http	
POST / HTTP/1.1
Host: example.com
Content-Length: 13
Content-Length: 6

G
POST /malicious HTTP/1.1
Host: example.com
```

### 4. TE.TE (Multiple Transfer-Encoding headers)

This attack uses multiple `Transfer-Encoding: chunked` headers to exploit inconsistencies between how different servers handle chunked encoding.

**Example:**
```http
POST / HTTP/1.1
Host: example.com
Transfer-Encoding: chunked
Transfer-Encoding: chunked

0
```

## Code Link
[https://github.com/defparam/smuggler.git](https://github.com/defparam/smuggler.git)

## Preventive Measures

Implement these strategies to prevent HTTP Request Smuggling attacks:

1. **Strict HTTP Parsing**: Ensure that the Web Application Firewall (WAF) is configured to strictly adhere to the HTTP/1.1 specification, particularly with respect to the handling of Content-Length and Transfer-Encoding headers. The WAF should reject any request that contains conflicting or malformed headers.

2. **Anomaly Detection**: Configure the WAF to detect anomalies in HTTP request headers, such as multiple Content-Length headers, mixed Content-Length and Transfer-Encoding headers, and improper chunked encoding. Any deviations from normal header patterns should be flagged and blocked.

3. **Custom Rules**: Create custom rules to specifically detect and block patterns associated with HTTP Request Smuggling techniques.

| Rule                                              | Description                                                                                   |
|----------------------------------------------------|-----------------------------------------------------------------------------------------------|
| Block Requests with Both Content-Length and Transfer-Encoding Headers | Ensure requests do not include both `Content-Length` and `Transfer-Encoding` headers simultaneously. |
| Block Requests with Multiple Content-Length Headers | Detect and reject requests containing multiple `Content-Length` headers to prevent interpretation discrepancies. |
| Block Requests with Multiple Transfer-Encoding Headers | Identify and reject requests with multiple `Transfer-Encoding` headers, exploiting inconsistencies in chunked encoding handling. |

These rules are essential for enforcing strict header handling policies to mitigate HTTP request smuggling attacks effectively.

4. **Rate Limiting and Traffic Throttling**: Implement rate limiting to control the number of requests a client can send in a short period. This can help mitigate the impact of smuggling attempts that rely on sending multiple crafted requests.

5. **Blocking Unsupported Methods**: Block HTTP methods that are not used by your application, as these can sometimes be exploited in smuggling attacks.

**Example**
- Headers without spaces before "chunked"
- Headers with extra spaces
- Headers starting with unexpected characters
- Headers specifying "chunk" instead of "chunked" (normalized as chunked encoding)
- Headers with multiple preceding spaces before "chunked"
- Headers with quoted values (single or double quotations)
- Headers with CRLF (Carriage Return Line Feed) characters before "chunked"
- Values containing invalid characters like ".1"

Implementing these blocks ensures only valid HTTP methods and headers are processed, reducing the risk of vulnerabilities.

## Usage

Clone the repository:
```bash
git clone https://github.com/defparam/smuggler.git
cd smuggler
```

Explore the code and documentation to understand and defend against HTTP Request Smuggling attacks effectively.
