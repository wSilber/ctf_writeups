# The shark

### Text: There is pcapng file. Can you reveal the authentication credentials? 

&nbsp;

Score: 5

Link: https://github.com/SecurityValley/PublicCTFChallenges/tree/master/network/the_shark

---
 ## Writeup

In this challenge you are given a pcap file that contains a series of HTTP requests. Looking at the requests themselves two of them result in a *401 Unauthorized* response while the last one results in a *200 OK* response. Looking at the last *GET* request, the request itself contains an *Authorization* header with a basic token. Basic authorization tokens usually follow the same format of **username:password** and then encoded with base64. The flag is encoded as the password in the authorization header. All that is needed to be done is decode the base64.

Full Request Frame
 ```
Frame 14: 373 bytes on wire (2984 bits), 373 bytes captured (2984 bits) on interface lo, id 0
Ethernet II, Src: 00:00:00_00:00:00 (00:00:00:00:00:00), Dst: 00:00:00_00:00:00 (00:00:00:00:00:00)
Internet Protocol Version 6, Src: ::1, Dst: ::1
Transmission Control Protocol, Src Port: 53022, Dst Port: 7777, Seq: 503, Ack: 172, Len: 287
Hypertext Transfer Protocol
    GET /api/v1/auth HTTP/1.1\r\n
    Authorization: Basic c2VjdmFsOlNlY1ZhbHs4NDVJYzR1N2hfaTVfNVVQM1JfNWhJN30=\r\n
    User-Agent: PostmanRuntime/7.26.8\r\n
    Accept: */*\r\n
    Postman-Token: 4a8745d5-69b5-4024-97c6-e4058a6bc3bd\r\n
    Host: localhost:7777\r\n
    Accept-Encoding: gzip, deflate, br\r\n
    Connection: keep-alive\r\n
    \r\n
    [Full request URI: http://localhost:7777/api/v1/auth]
    [HTTP request 3/3]
    [Prev request in frame: 9]
    [Response in frame: 15]
 ```

## Flag: SecVal{845Ic4u7h_i5_5UP3R_5hI7}