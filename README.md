# Lab Red y Máquinas Virtuales

**Autores:** Oscar Grande — Didier Posse
**Fecha:** Octubre 2025

Este laboratorio documenta la configuración y gestión práctica de una red local utilizando un **Switch Cisco Catalyst 2960**, la validación de la conectividad entre diversos dispositivos (PC, Raspberry Pi), y el proceso detallado de instalación de múltiples sistemas operativos (Linux) como **Máquinas Virtuales (VM)** utilizando el hipervisor **KVM/QEMU**.

---

## 1. Configuración de Red y Switch Cisco 2960

La primera parte del laboratorio se centra en la gestión y configuración de un switch de capa 2 para establecer una red administrada y probar la comunicación entre el PC del laboratorio, un segundo PC y una Raspberry Pi.

### Modos y Gestión Básica del Switch

Se abordan los **Modos de Operación** del switch (User EXEC, Privileged EXEC, Global Configuration) y los **Comandos Esenciales** para su gestión:

| Categoría | Comandos Clave | Propósito |
| :--- | :--- | :--- |
| **Acceso/Seguridad** | `enable`, `configure terminal`, `enable secret`, `service password-encryption` | Entrar en modo privilegiado y cifrar contraseñas de configuración. |
| **Configuración** | `hostname [nombre]`, `interface vlan 1`, `ip address [ip] [máscara]` | Asignar nombre e IP de administración a la VLAN 1. |
| **Guardar/Reiniciar** | `copy running-config startup-config`, `reload`, `erase startup-config` | Guardar cambios permanentemente en la NVRAM o reiniciar el dispositivo. |
| **Verificación** | `show running-config`, `show ip interfaces brief`, `show mac address-table` | Mostrar la configuración activa y el estado de las interfaces. |

### Proceso de Conexión y Configuración

1.  **Conexión Serial:** Uso de un cable de Consola (RJ45 a USB) para la configuración inicial. En sistemas Linux, se utilizan herramientas como **`dmesg`** y **`lsusb`** para detectar el puerto COM/Serial (ej. `/dev/ttyUSB0`) y **`minicom`** o **`picocom`** para establecer la comunicación a una tasa de baudios de **9600**.
2.  **Configuración de Puertos:** Se configura un rango de interfaces Fast Ethernet (ej. `fa0/1-3`) como puertos de acceso (`switchport mode access`) y se activan (`no shutdown`).
3.  **Validación de Red:** Se asigna una IP privada de administración (ej. `192.168.1.1 255.255.255.0`) a la **VLAN 1** del switch. Luego, se asigna una IP dentro del mismo patrón a cada dispositivo final (PC/Raspberry Pi).
4.  **Diagnóstico y Transferencia:**
    * **Conectividad:** Se valida la comunicación de extremo a extremo utilizando **`ping`**.
    * **Escaneo:** Se usa **`nmap`** para visualizar los dispositivos, puertos y servicios activos en la red.
    * **Transferencia Segura:** Se instala y habilita el servidor **SSH** (`openssh-server`) en los dispositivos para permitir la transferencia segura de archivos mediante el comando `scp`.

### Conceptos Clave de Red

* **Puerta de Enlace (Gateway):** Dispositivo que conecta la red local con otras redes (ej. Internet).
* **Máscara de Subred:** Utilizada para dividir una red en subredes más pequeñas y definir qué parte de una dirección IP pertenece a la red y cuál al host. (Ej. 255.255.255.0).
* **VLAN (Red de Área Local Virtual):** Permite segmentar una red lógica dentro de un mismo switch físico para mejorar el flujo y la seguridad del tráfico.
* **CIDR (Classless Inter-Domain Routing):** Notación moderna para escribir direcciones IP y su máscara, resumiendo la máscara con un número de bits (Ej. `192.168.1.0/24`).

---

## 2. Instalación de Máquinas Virtuales en KVM/QEMU

La segunda parte del laboratorio documenta el proceso detallado para instalar varios sistemas operativos Linux como Máquinas Virtuales (VMs) utilizando la plataforma de virtualización **KVM/QEMU**.

### Proceso General de Instalación

El proceso común implica:

1.  Descarga de la imagen ISO oficial.
2.  Configuración de la VM en QEMU/KVM: asignación de recursos de hardware (disco, RAM, núcleos de CPU).
3.  Selección del idioma/teclado.
4.  Partición del disco asignado.
5.  Creación de usuarios y contraseñas.
6.  Reinicio (reboot) para finalizar la instalación.

### Distribuciones Instaladas

| Distribución | Método de Instalación | Descripción del Proceso |
| :--- | :--- | :--- |
| **Ubuntu** | Gráfico (GUI) | Instalación estándar vía interfaz gráfica. La VM utiliza el acceso a Internet del sistema operativo anfitrión (Host). |
| **CentOS Stream** | Gráfico (GUI) | Proceso de instalación guiado similar a Ubuntu, centrado en la configuración regional y la creación de cuentas de usuario. |
| **Scientific Linux** | Gráfico (GUI) | Proceso análogo a CentOS, incluyendo la configuración de particiones y la definición de usuarios. |
| **Alpine Linux** | Terminal (CLI) | Instalación única y minimalista a través de comandos en la terminal (`setup-alpine`). Requiere configurar manualmente la red, el hostname y la contraseña de root. |
