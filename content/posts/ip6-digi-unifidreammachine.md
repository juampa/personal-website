+++
title = "Configurar UDM para soportar ipv6 en Digi"
date = "2023-12-21T10:34:32+01:00"
author = ""
authorTwitter = "" #do not include @
cover = ""
tags = ["", ""]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
color = "" #color from the theme settings
+++

Detallo por aquí la configuración para conseguir tener ip6 con el operador Digi y el router neutro Unifi Dream Machine (Network version 8.0.24). Asumo que ya has conseguido las credenciales para conectar el router directamente a la ONT. 

Dentro de la configuración de Internet, seleccionaremos el modo Manual en "Advanced":

- VLAN ID: Enabled (20 en Digi)
- Mac Address clone: Enabled (usar la MAC del router de Digi, figura en la parte de atrás).
- Ipv4 configuration: PPPoE (por teléfono dan las credenciales, sino se puede usar wireshark para obtenerlas).
- Ipv6 configuration: DHCPv6
- Prefix delegation size: 56
- DNS Server: auto

Si todo va bien, el router ya será capaz de obtener una dirección ipv6 desde Digi. Para comprobarlo deberemos verificar que en la interfaz ppp0 aparezca la dirección ip4 y la ipv6:

![PPP0](/ppp0.png)

A continuación ya podemos configurar la LAN para que también pueda usar direcciones ipv6

Networks -> Default (o la red que queráis) -> ipv6:

- Interface type: Prefix delegation
- Prefix delegation interface: Primary (Wan1, en mi caso).
- Prefix delegation ID: Auto.
- Advanced -> Manual
- Client Address Assignment: SLAAC.
- DNS Server: Auto.
- Router Advertisment: checked.
- RA Priority: High.

Si todo va bien vuestros equipos deberían obtener direcciones DHCP automáticamente:

![IPv6](/ip6_client.png)


Esas direcciones "temporales" se usan para conexiones salientes, para mejorar la privacidad. Pueden ser un problema para acceder a recursos internos de la red si utilizas firewalls o ACLs. Es algo que tengo que seguir investigando. 

Una buena prueba para comprobar que todo haya ido bien es acceder a [test-ipv6](http://test-ipv6.com) y ver si obtenemos la máxima puntuación:

![Test](/testipv6_60.png)



