---
title: "Hello World!"
date: 2022-11-09T01:10:40+01:00
draft: false
tags:
    - Hello World!
comments: false
toc: false
featured: true
---

# Hello World!
Welcome to our new IT site! Here we are going to post our projects. What is better beginning 
than releasing a website. We have three servers, one is reserved to important things, other
is where we locate all our projects and there is another one which is the f****** jungle.
And, guess what? For this moment, we are going to be allocated there, in lab server. Maybe, if everything
is OK, we migrate to a normal server. All commands named in this blog is refered to Linux SO´s.

How is it supposed I am going to do that? Well, keep reading.

# First, we need a domain
It´s important to mention our server structure. Eurielec has it´s own DNS server, which depends of UPM network. It dump the domains
to the UPM server which publishes it. Eurielec has only one server connected to UPM´s network, stark.
Stark is connected over port 80. All other servers are connected by other ports to him. Well, how to add a new domain?

## Register the domain
Open a terminal with `Ctrl + Alt + t`  and write:
```
ssh ha100da@ha100da´s IP -p ha100da´s access port
```
Now, we are connected via ssh to ha100da´s server. Mind beeing root.

---

```
sudo docker ps
```
See there´s a bind dns server´s docker. If you want to check there´s no domain called as we want.
In our case, we are creating this page, so the domain is `it.eurielec.etsit.upm.es`. If you
want to check it, try this command `curl domain`. For example, `curl it.eurielec.etsit.upm.es`,
the output will be something similar to this: `curl: (6) Could not resolve host: ittutorial.eurielec.etsit.upm.es`.

---

Go to `/data/bind_dns_server/data/bind/etc/eurielec.db` and write in service´s section your
domain. Update the serial (line 17) with the `date +%s` output´s
```
docker exec -it bind_dns_server named-checkzone eurielec.etsit.upm.es /etc/bind/eurielec.db
```
With this command we are synchronizing the time.
Now restart the docker to upload the domain to the server and allow it to dump to UPM´s server.

---

Well, now we need to create the page. Let´s suppose we have created the page. Save it to `/data` Go to poliedro and 
create a docker-compose (with extension .yml). For now, I am working to create new posts about docker.
```
version: "3.7"
services:
  it-website:
    image: klakegg/hugo:0.101.0
    container_name: "it-website"
    command: server --appendPort=false --baseURL https://$DOMAIN
    volumes:
      - $IT_WEBSITE_DATA:/src
      - /etc/localtime:/etc/localtime:ro
    ports:
      - "80:1313"
    environment:
      - HUGO_ENV=production
```
As you can see, there are some variales. They are defined in a hidden file called .env. There, you can define the domain and the place where you have allocated the page. We have saved it into /data due to good practicing but feel free to save where you want. It´s always grateful keep a clean server. Be responsible!

Build a container from the docker-compose. Probably, you will need to stop the nginx´s container till it updates to get the traffic to the respectives ports. And thats all, you should have your page launched! Hope this have been grateful! If you have any suggestions, you can contact me by telegram.
