---
title: "Something about curl -- Connecting IPA Server using json as an example"
date: "2013-04-10"
categories: 
  - "linux-admin"
---

With Arduino as a server.

What we want is to keep a cookie and build a HEADER [http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html "HTTP/1.1") \[P1\]

curl -e
curl -H referer:whereicamefrom.com
curl -d @file.txt
curl -d "somecmd"
curl -cookie
curl -D

\[P2\] **Communicating with ipa server** [https://git.fedorahosted.org/cgit/freeipa.git/tree/API.txt](https://git.fedorahosted.org/cgit/freeipa.git/tree/API.txt "freeipa api")

Get ca.crt

curl -k https://$YOURHOST/ipa/config/ca.crt >> /tmp/ipa.ca.cert

Get sessionid

sessid=$(curl -v 
-H referer:https://ipa.test.net/ipa/ui/index.html 
-H "Content-Type:application/x-www-form-urlencoded" 
-H "Accept:\*/\*" 
--negotiate -u : 
--cacert ./ca.crt 
-d "user=admin" -d "password=12345678" 
-D cookie.txt 
-X POST 
-k      
https://ipa.test.net/ipa/session/login\_password 
2>&1 | grep -o "ipa\_session=\[a-zA-Z0-9\]\*")

Post a json file with cmd in it

curl -v 
-H referer:https://ipa.test.net/ipa/ui/index.html 
-H "Content-Type:application/json" 
-H "Accept:applicaton/json" 
-negotiate -u : 
--cacert ./ca.crt 
--cookie $sessid 
-d @ipa.json 
-X POST 
-k      
https://ipa.test.net/ipa/session/json

Here's a json file

{
"method":"user\_find",
"params":\[
        \[""\],
        {"uid":"admin"}
        \],
"id":0
}

{
"method":"user\_add",
"params":\[
        \[\],
        {
         "uid":"test1",
         "cn":"cn",
         "givenname":"test1",
         "sn":"test1"
        }
        \],
"id":0
}
