---
title: "Experimental post for debugging styles"
date: 2020-11-20T17:19:07+03:00
tags: [experimental]
description: 'This is a description for experimental post   '
---

It is a long established fact that a reader will be distracted by the readable content of a page when looking at its layout. The point of using Lorem Ipsum is that it has a more-or-less normal distribution of letters, as opposed to using 'Content here, content here', making it look like readable English. Many desktop publishing packages and web page editors now use Lorem Ipsum as their default model text, and a search for 'lorem ipsum' will uncover many web sites still in their infancy. Various versions have evolved over the years, sometimes by accident, sometimes on purpose (injected humour and the like).


<!--more--> 


# Problem
I've been trying to setup it

# Solution
Use rev list


{{< highlight java "linenos=table,hl_lines=8 15-17,linenostart=199" >}}
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@SpringBootApplication
@EnableEurekaServer
public class EurekaServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(EurekaServerApplication.class, args);
    }
}
{{< / highlight >}}

and

```c
int main() {

}
```