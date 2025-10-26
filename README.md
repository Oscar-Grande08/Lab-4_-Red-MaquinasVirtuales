# Lab. Red y Máquinas Virtuales

**Autores:** Oscar Grande — Didier Posse  
**Fecha:** Octubre 2025

---

## Resumen (qué contiene y objetivo)
Este repositorio resume paso a paso el laboratorio **“Red y Máquinas Virtuales”**: primero la práctica con un **switch Cisco Catalyst 2960** (conexión física, configuración básica, VLANs, verificación y transferencia de archivos) y luego la **creación e instalación de máquinas virtuales** en KVM/QEMU (Ubuntu, CentOS, Alpine, Scientific Linux). El objetivo es dejar un procedimiento reproducible y claro para repetir la práctica en un entorno de laboratorio. :contentReference[oaicite:1]{index=1}

---

## Resumen paso a paso (versión condensada y accionable)

### 1) Preparación
- Materiales: switch Cisco 2960, cable consola (RJ45-to-USB/DB9), PC host (Linux/Windows), cables Ethernet, máquina para KVM/QEMU y archivos `.iso` de las distros.
- Objetivo inmediato: configurar el switch, asignar IP administrativa, conectar 3 dispositivos (PC, otro PC, Raspberry) y comprobar conectividad. :contentReference[oaicite:2]{index=2}

### 2) Conexión serial al switch (establecer consola)
1. Localiza el puerto **Console** (RJ45) en el switch.  
2. Conecta el cable consola al switch y al puerto USB/COM del PC.  
3. En Linux instala y configura `minicom` o `screen` para la conexión serial:
   - Ejemplo de pasos:
     (comandos mostrados como guía — ejecutarlos donde corresponda)
     
     4 espacios indentados indican bloque de comandos:
     
     sudo apt update  
     sudo apt install minicom -y  
     sudo minicom -s  
     (configurar puerto `/dev/ttyUSB0`, baud 9600, 8N1)  
4. Verifica que la consola responde (mensaje del switch, prompts). :contentReference[oaicite:3]{index=3}

### 3) Configuración básica del switch (paso a paso)
1. Entra al modo privilegiado y al modo de configuración global:
   
   enable  
   configure terminal

2. Ajustes iniciales (ejemplo reproducible):
   - Asignar nombre:
     
     hostname Portolito
   - Desactivar resolución DNS (evita delays): 
     
     no ip domain-lookup
   - Configurar rango de interfaces y ponerlas en modo access y activarlas:
     
     interface range fa0/1 - 3  
         switchport mode access  
         no shutdown  
     exit
   - Configurar IP administrativa en la VLAN 1:
     
     interface vlan 1  
         ip address 192.168.1.1 255.255.255.0  
         no shutdown  
     exit
   - Guardar configuración:
     
     copy running-config startup-config

3. Verificaciones útiles:
   - `show ip interface brief` — resumen de interfaces e IPs.  
   - `show mac address-table` — ver MACs aprendidas.  
   - `show version` — versión IOS y datos del equipo.  
   - Hacer `ping` desde el switch a los hosts para confirmar conectividad. :contentReference[oaicite:4]{index=4}

### 4) Asignar IPs a los equipos (hosts)
- **Windows (GUI):** Centro de redes → Cambiar configuración del adaptador → IPv4 → Usar la siguiente dirección IP → completar (ej.: 192.168.1.10 / 255.255.255.0 / gateway 192.168.1.1).  
- **Linux (temporales por terminal):**
  
  4 espacios indentados:  
  sudo ip addr add 192.168.1.11/24 dev eth0  
  sudo ip link set eth0 up

- Comprueba con `ping 192.168.1.1` (direccion del switch). :contentReference[oaicite:5]{index=5}

### 5) Diagnóstico y reconocimiento de red
- Usar `nmap` desde un host para mapear dispositivos y puertos:
  
  4 espacios indentados:  
  sudo apt install nmap -y  
  nmap -sP 192.168.1.0/24  
  nmap -sV 192.168.1.10

- Revisar que todos los nodos respondan al ping y que los puertos esperados (SSH, etc.) estén abiertos. :contentReference[oaicite:6]{index=6}

### 6) Transferencia de archivos entre hosts (SSH + SCP)
1. Instalar y activar SSH en el host receptor:
   
   4 espacios indentados:  
   sudo apt update  
   sudo apt install openssh-server -y  
   sudo systemctl enable --now ssh

2. Enviar archivo desde origen:
   
   4 espacios indentados:  
   scp switch.txt usuario@192.168.1.11:~/switch.txt

3. Verificar recepción en el equipo destino (`ls -l ~/switch.txt`). Este procedimiento fue probado en el laboratorio (envío entre “oscar” y “didier-posse”). :contentReference[oaicite:7]{index=7}

### 7) Máquinas virtuales en KVM/QEMU — flujo general
Procedimiento reproducible para crear e instalar una VM con QEMU/virt-install (sintetizado):

1. Preparar disco:
   
   4 espacios indentados:  
   qemu-img create -f qcow2 ubuntu.qcow2 20G

2. Instalar con `virt-install` (ejemplo Ubuntu):
   
   4 espacios indentados:  
   sudo virt-install \  
     --name ubuntu-lab \  
     --ram 4096 \  
     --vcpus 2 \  
     --disk path=/path/to/ubuntu.qcow2,format=qcow2 \  
     --cdrom /path/to/ubuntu.iso \  
     --os-type linux \  
     --network network=default \  
     --graphics vnc

3. Pasos dentro del instalador de cada distro:
   - **Ubuntu:** elegir idioma, particionado (usar disco asignado), crear usuario, instalar paquetes y reboot. :contentReference[oaicite:8]{index=8}  
   - **CentOS / Scientific Linux:** proceso similar a Ubuntu (instalador gráfico/anaconda), particionar, crear usuario/root, reboot. :contentReference[oaicite:9]{index=9}  
   - **Alpine Linux:** instalación por terminal; usar `setup-alpine` para configurar interfaz (`eth0`), DHCP o IP estática, usuario, zona horaria y mirror; particionar con las opciones de QEMU y `reboot` final. Muy útil para imágenes mínimas. :contentReference[oaicite:10]{index=10}

> Nota: el documento original muestra capturas (GRUB, pantallas de instalación y mensajes de bienvenida) y comentarios específicos del flujo de cada instalación. :contentReference[oaicite:11]{index=11}

---

## Lista de comandos clave (rápido)
- Switch: enable, configure terminal, hostname, no ip domain-lookup, interface range fa0/1-3, switchport mode access, no shutdown, interface vlan 1, ip address 192.168.1.1 255.255.255.0, copy running-config startup-config. :contentReference[oaicite:12]{index=12}  
- Host Linux: sudo apt install minicom / nmap / openssh-server, sudo ip addr add …, scp file user@host:.  
- KVM/QEMU: qemu-img, virt-install (ejemplos arriba). :contentReference[oaicite:13]{index=13}

---

## Checklist mínimo para reproducir la práctica
- [ ] Cable consola y puerto funcional.  
- [ ] `minicom` o herramienta serial configurada y testeada.  
- [ ] Switch con acceso privilegiado para cambiar configuración.  
- [ ] Rango de IPs planificado (ej.: 192.168.1.0/24).  
- [ ] ISOs descargadas y disponibles para QEMU.  
- [ ] Acceso a `virt-install` o interfaz QEMU para crear VMs.  
- [ ] `nmap` y `ssh` instalados para diagnóstico y transferencia. :contentReference[oaicite:14]{index=14}

---

## Conclusión corta
El documento documenta una práctica completa: desde la **conexión física** y **configuración inicial** de un switch Cisco 2960 hasta la **creación e instalación** de máquinas virtuales en KVM/QEMU. El README anterior (este) deja un procedimiento claro, reproducible y con los comandos mínimos necesarios para repetir la práctica en otro laboratorio. Para ver las capturas, explicaciones largas y ejemplos concretos, revisa el documento original. :contentReference[oaicite:15]{index=15}

---

## Autores y evidencia
Oscar Grande — Didier Posse.  
El PDF original incluye capturas y ejemplos de cada paso (consola, comandos, pings, nmap, envíos por scp e instalaciones de distros). :contentReference[oaicite:16]{index=16}

---
