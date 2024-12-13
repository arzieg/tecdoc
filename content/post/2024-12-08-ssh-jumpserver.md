---
title: ssh jump
subtitle:  ssh jump zwischen mehreren Servern
date: 2024-12-08
tags: ["ssh", "ssh-jump"]
---


Immer dann, wenn es keine direkte Verbindung zwischen Client und Server gibt, aber eine Verbindung über ein oder mehrere Jump-Server eine Verbinung aufgebaut werden kann, kann mit der -J Option im ssh-Befehl der Server erreicht werden. Mit der Option -J werden 1 bis n Jumphosts angegeben, über die dann eine Verbindung zum Zielserver aufgebaut werden. Je Jumphost kann man username, Jumphost und Port des ssh-Daemon angeben. 

Allgemein: 

```
ssh -J username@jumphost1:port1,username@jumphost2:port2 -A username@zielhost:port
```

Der Port kann weggelassen werden, wenn der Default Port 22 angesprochen wird. 
Username kann weggelassen werden, wenn man sich mit seinem login User über die Jumphosts einloggen kann. 

Noch angenehmer wird das ganze, wenn mit einem ssh-agent gearbeitet wird, bei dem der private Key auf dem Client hinterlegt ist. Sollte man sich öfter auf dem Remote-Server einloggen wollen, bietet es sich an, ein Alias in ~/.ssh/config zu definieren. Bspw.:

```
Host myserver
  -J username@jumphost1:port1,username@jumphost2:port2 -A username@zielhost:port
  ForwardAgent yes
```

Der Server ist dann unter ssh myserver über die Jumpserver erreichbar.

![Einfacher Jump](../images/ssh-jumpserver1.png)



