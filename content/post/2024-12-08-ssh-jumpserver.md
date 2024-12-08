---
title: ssh jump
subtitle:  ssh jump zwischen mehreren Servern
date: 2024-12-08
tags: ["ssh", "ssh-jump"]
---

```mermaid
architecture-beta
    group simplejump(server)[SSH Jump Example]

    service client(server)[Client] in simplejump
    service jump(server)[Jumphost] in simplejump
    service server(server)[Server] in simplejump

    client:R --> L:jump
    jump:R --> L:server 
```