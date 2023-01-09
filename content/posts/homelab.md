+++
title = "Homelab"
date = "2023-01-09T21:49:51+01:00"
author = ""
authorTwitter = "" #do not include @
cover = ""
tags = ["homelab"]
keywords = ["", ""]
description = "Descripcion de mi entorno de pruebas"
showFullContent = false
readingTime = false
hideComments = false
color = "" #color from the theme settings
+++

A continuación detallo cual es mi entorno de pruebas actual. Estoy probando diferentes cosas y configuraciones, así que esto puede cambiar bastante. Intentaré mantener este hilo actualizado. 

Desde la conexión de Internet:

- Router/Wifi: Unifi Dream Machine

	Para usarlo como router neutro tuve que obtener las credenciales de la operadora. En aquel momento no las facilitaban, por lo que configurando un switch con un port mirroring fue fácil. Mi red interna está segmentada en tres categorías:

	* 192.168.1.0/24: Red por defecto
	* 192.168.2.0/24: Red IOT para dispositivos.
	* 192.168.3.0/24: Red homelab externa.
	* 192.168.4.0/24: Red homelab interna (dentro del proxmox uso pfsense en esa red).

- Server: Proxmox PVE1

	Características:
	* CPU: AMD Ryzen 5 5600G (6 cores / 12 hilos)
	* Memoria: 32 Gigas.
	* Wifi: conectada a la wifi por defecto.
	* Lan integrada: conectada al router a través de un PLC.
	* Tarjeta Ethernet extra con otras dos bocas de red: uso una de las conexiones contra un switch, de tal forma que todo lo que conecte ahí está en la red homelab interna.
	* Almacenamiento: 2 NVME de 500 Gigas y 2 discos de 8 Teras en configuración RAIDZ-1
	* Versión de Proxmox: 7.3-4, con kernel Linux 6.1.0-1-pve.

Puesto que el server PVE1 tiene direccionamiento de la red 1.0/24 como de la 3.0/24 puedo acceder al Proxmox incluso en caso de que la VM_PFSense que controla la red interna fallase (siempre podría aun así conectarme físicamente al equipo, ya que lo tengo cerca).

Máquinas virtuales que tengo ahora mismo desplegadas:
  - PFSense: siempre activa, haciendo las veces de servidore de OpenVPN, firewall, DHCP Interno...
  - Pihole: en un futuro lo complementaré con un DNS basado en Bind9. 
  - Grafana: uso Influx como datasource, y recopilo métricas tanto de Proxmox como del pool de ZFS o del PFSense.
  - Cluster de Kubernetes con K3s para hacer pruebas.
  - Maquina Windows 10 con PCI Passthrough de la tarjeta gráfica.
  - Máquina Ubuntu 20.04 con el mismo PCI de la tarjeta (no puedo usarlas a la vez).
  - Otras máquinas para pruebas varias: haproxy, Apache,...


Cambios que querría hacer:
- Montar una máquina con TrueNas y pasar la controladora SATA de manera completa: el problema es que mi placa la controladora usa el mismo grupo iommu que otro montón de dispositivos, por lo que de momento no lo he conseguido:

{{< code language="bash" title="IOMMU Groups" id="1" expand="Show" collapse="Hide" isCollapsed="true" >}}
root@pve1:~# ./iommu.sh
Group 0:        [1022:1632]     00:01.0  Host bridge                              Renoir PCIe Dummy Host Bridge
Group 1:        [1022:1633] [R] 00:01.1  PCI bridge                               Renoir PCIe GPP Bridge
Group 2:        [1022:1632]     00:02.0  Host bridge                              Renoir PCIe Dummy Host Bridge
Group 3:        [1022:1634] [R] 00:02.1  PCI bridge                               Renoir PCIe GPP Bridge
Group 4:        [1022:1634] [R] 00:02.2  PCI bridge                               Renoir PCIe GPP Bridge
Group 5:        [1022:1632]     00:08.0  Host bridge                              Renoir PCIe Dummy Host Bridge
Group 6:        [1022:1635] [R] 00:08.1  PCI bridge                               Renoir Internal PCIe GPP Bridge to Bus
Group 7:        [1022:790b]     00:14.0  SMBus                                    FCH SMBus Controller
                [1022:790e]     00:14.3  ISA bridge                               FCH LPC Bridge
Group 8:        [1022:166a]     00:18.0  Host bridge                              Device 166a
                [1022:166b]     00:18.1  Host bridge                              Device 166b
                [1022:166c]     00:18.2  Host bridge                              Device 166c
                [1022:166d]     00:18.3  Host bridge                              Device 166d
                [1022:166e]     00:18.4  Host bridge                              Device 166e
                [1022:166f]     00:18.5  Host bridge                              Device 166f
                [1022:1670]     00:18.6  Host bridge                              Device 1670
                [1022:1671]     00:18.7  Host bridge                              Device 1671
Group 9:        [10de:1d01] [R] 01:00.0  VGA compatible controller                GP108 [GeForce GT 1030]
                [10de:0fb8]     01:00.1  Audio device                             GP108 High Definition Audio Controller
Group 10:       [1022:43ee] [R] 02:00.0  USB controller                           Device 43ee
USB:            [0b05:1939]              Bus 001 Device 004                       ASUSTek Computer, Inc. AURA LED Controller
USB:            [8087:0029]              Bus 001 Device 003                       Intel Corp. AX200 Bluetooth
USB:            [1d6b:0002]              Bus 001 Device 001                       Linux Foundation 2.0 root hub
USB:            [1d6b:0003]              Bus 002 Device 001                       Linux Foundation 3.0 root hub
                [1022:43eb]     02:00.1  SATA controller                          Device 43eb
                [1022:43e9]     02:00.2  PCI bridge                               Device 43e9
                [1022:43ea] [R] 03:00.0  PCI bridge                               Device 43ea
                [1022:43ea]     03:01.0  PCI bridge                               Device 43ea
                [1022:43ea]     03:02.0  PCI bridge                               Device 43ea
                [1022:43ea]     03:03.0  PCI bridge                               Device 43ea
                [1022:43ea]     03:04.0  PCI bridge                               Device 43ea
                [1022:43ea]     03:08.0  PCI bridge                               Device 43ea
                [1022:43ea]     03:09.0  PCI bridge                               Device 43ea
                [8086:10c9] [R] 07:00.0  Ethernet controller                      82576 Gigabit Network Connection
                [8086:10c9] [R] 07:00.1  Ethernet controller                      82576 Gigabit Network Connection
                [144d:a809] [R] 08:00.0  Non-Volatile memory controller           Device a809
                [8086:2723] [R] 09:00.0  Network controller                       Wi-Fi 6 AX200
                [10ec:8168] [R] 0a:00.0  Ethernet controller                      RTL8111/8168/8411 PCI Express Gigabit Ethernet Controller
Group 11:       [15b7:5011] [R] 0b:00.0  Non-Volatile memory controller           WD Black SN850
Group 12:       [1002:1638] [R] 0c:00.0  VGA compatible controller                Cezanne
Group 13:       [1002:1637] [R] 0c:00.1  Audio device                             Device 1637
Group 14:       [1022:15df]     0c:00.2  Encryption controller                    Family 17h (Models 10h-1fh) Platform Security Processor
Group 15:       [1022:1639] [R] 0c:00.3  USB controller                           Renoir USB 3.1
USB:            [1d6b:0002]              Bus 003 Device 001                       Linux Foundation 2.0 root hub
USB:            [1d6b:0003]              Bus 004 Device 001                       Linux Foundation 3.0 root hub
Group 16:       [1022:1639] [R] 0c:00.4  USB controller                           Renoir USB 3.1
USB:            [1d6b:0002]              Bus 005 Device 001                       Linux Foundation 2.0 root hub
USB:            [1d6b:0003]              Bus 006 Device 001                       Linux Foundation 3.0 root hub
Group 17:       [1022:15e3]     0c:00.6  Audio device                             Family 17h (Models 10h-1fh) HD Audio Controller


{{< /code >}}


