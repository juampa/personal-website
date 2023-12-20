+++
title = "Plc Wifi Lan"
date = "2023-12-20T17:35:52+01:00"
author = ""
authorTwitter = "" #do not include @
cover = ""
tags = ["", ""]
keywords = ["", ""]
description = "Pasar de un lento PLC a una conexión por cable"
showFullContent = false
readingTime = false
hideComments = false
color = "" #color from the theme settings
+++

Aprovechando las vacaciones de Navidad, me he decidido por fin a tirar cable de red desde el despacho hasta el salón, donde se encuentra el router. He aprovechado y he puesto cable Cat7, por si en el futuro paso a 2.5G (me obligaría a cambiar el UDM, el switch y varias cosas, pero es algo que tengo en mente). Tras pasar el cable (gracias a mi padre y a sus trucos con el jabón) y crimpar, os comparto los resultados de las pruebas de velocidad usando iperf3 desde mi equipo del despacho hasta el router. Anteriormente he usado tanto Wifi como un PLC para el Homelab (Proxmox no se lleva muy bien con las interfaces wifi, por lo que preferí usar el PLC). 

### PLC

![PLC](/plc.png)

### WIFI (5 GHz) 

![WIFI](/wifi.png)

### Cable LAN 

![LAN](/lan.png)


La mejora es evidente, pero sobre una gráfica se aprecia todavía mejor:

![GRAFICA](/grafica30.png)

Esperaba tener problemas, bien con el cable o con el crimpado, así que estoy muy satisfecho con el resultado. Una vez hecho esto voy a replantear toda la red interna y la red del Homelab. Lo primero que quiero hacer es poner ipv6, lo contaré en la próxima entrada. Hasta la próxima!



