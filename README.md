# Instalación de Servidor KMS

## 1. Introducción 
Con este documento se busca dar una idea de que es y como instalar un servidor KMS en un servidor Linux, para de esta manera profundizar el conocimiento sobre su uso y funcionamiento de como el servidor KMS valida licencias de Windows desde la red local, hay que tener en cuenta que este documento es un laboratorio y no debe ser usuario en casos reales ya que la utilización de este servidor no es legal según las políticas de licenciamiento de Microsoft, por este motivo se desaconseja su uso en entornos caseros o corporativos. 

## 2. Base de conocimientos

### 2.1. ¿Qué es un Servidor KMS?
Key Management Service (KMS) es un servicio que permite realizar la activación de nuestros productos Microsoft en la red local sin necesidad de que cada cliente se conecte con Microsoft para ello.

### 2.2. ¿Qué es un KMS Host?
Es un equipo no dedicado que cuenta con una licencia KMS activa y que permite la activación de un número ilimitado de equipos cliente. En el enlace a Microsoft indicado a continuación es posible encontrar estos y más conceptos que permitirán despejar la mayoría de las dudas acerca de KMS http://www.microsoft.com/es-es/licensing/existingcustomers/product-activation-faq.aspx

### 2.3. ¿Cómo funciona KMS?
La activación de KMS requiere de conectividad TCP/IP. Por defecto, los servidores HOST KMS y máquinas clientes, utilizan el servicio de DNS para publicar y encontrar dentro de la red al servicio KMS


## 3. Instalación de Servidor KMS en Ubuntu Server 16.04
Para este laboratorio utilizaremos un servidor Ubuntu 16.04 virtualizado con VMware Workstation 14, a continuación, describiremos los pasos a seguir para realizar este laboratorio desde 0

## 3.1. Prerrequisitos del Laboratorio
Los siguientes son todas las herramientas que utilizaremos en este Laboratorio.

+ Virtualización
	- VMware Workstation 14 (usada): Podéis descargar la versión de prueba desde [aqui](https://my.vmware.com/en/web/vmware/info/slug/desktop_end_user_computing/vmware_workstation_pro/14_0)
	- VirtualBox (Alternativa): Podéis descarga la herramienta desde [aquí](https://www.virtualbox.org/wiki/Downloads)
+ Sistemas Operativos
	- Ubuntu Server 16.04: Podéis descargarlo desde [aquí](https://www.ubuntu.com/download/server)
+ Herramientas Necesarias
	- SSH Tools
		- MobaXterm (usada): Es una de las herramientas más útiles para conexiones remotas a sistemas Linux y podéis descargar gratis la versión Home desde [aquí](https://mobaxterm.mobatek.net/download.html)
		- Putty (Alternativa): Podéis descargarla gratis desde [aqui](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
	- SFTP Tool
		- WinScp (No necesaria con MobaXterm): La mejor herramienta de FTP y SFTP en mi opinión podéis descargarla gratis desde[aquí](https://winscp.net/eng/download.php)
		- FileZilla (Alternativa): Podéis descargarla gratis desde [aquí](https://filezilla-project.org/download.php)
+ Herramientas de descompresión
	- 7zip: Puedes descargarlo gratis desde [aquí](https://www.7-zip.org/download.html)
	- WinRAR: puedes descargarlo desde [aquí](https://www.winrar.es/descargas/winrar)

## 3.2 Instalación de VMware Workstation 14
Procedemos a la instalación de la herramienta de Virtualización VMware Workstation 14, para ello seguiremos los siguientes pasos:

1. Descargamos la herramienta desde la URL antes facilitada.
2. Le damos a siguiente, siguiente, siguiente... o si quieres mira el siguiente video.

<a href="http://www.youtube.com/watch?feature=player_embedded&v=VxTO0xxN6-M" target="_blank"><img src="http://img.youtube.com/vi/VxTO0xxN6-M/0.jpg" alt="IMAGE ALT TEXT HERE" width="240" height="180" border="10" /></a>

3. Reinicia el PC

> **Nota:** Hay que tener en cuanta al menos dos cosas: 
> + VMware y HiperV de windows no pueden funcionar simultáneamente, en el video indica como deshabilitarlo
> + Recuerda que debes habilitar la Virtualización desde la BIOS de ti PC
