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

## 3.3. Instalación de Ubuntu Server 16.04

### 3.3.1. Instalacion del SO en VMware

Procedemos a la instalación de Ubuntu Server 16.04 en una máquina virtual con VMware Workstation 14 y para ello seguiremos los siguientes pasos:

1. Descargamos el SO desde la URL facilitada anteriormente.
2. Los pasos para la instalación están al final de este post

### 3.3.2. Pasos Post-Instalación

Tras finalizar la instalación del sistema operativo y logarnos necesitamos realiza algunos ajustes simples al sistema.

+ Activar el usuario root: por defecto en los sistemas Ubuntu en usuario root viene deshabilitado por seguridad para activarlo ejecutaremos los siguientes comandos.

	```console
	sudo passwd root
	```
	
	Esto nos solicitara la contraseña del usuario que hemos creado y posteriormente la contraseña que deseamos de root.

+ Nos logamos con root:

	```console
	su - root
	```

+ Activar Acceso SSH a la maquina

	```console
	apt-get install openssh-server -y
	service sshd start
	```

	Con esto hemos instalado el servidor ssh para conectarnos a la maquina con mobaXterm o Putty, pero nos falta habilitar en la configuración para que se pueda acceder con root, cambiaremos la configuración del fichero /etc/ssh/sshd_config donde sustituiremos la línea `PermitRootLogin prohibit-password` por `PermitRootLogin yes`

	```console
	vi /etc/ssh/sshd_config
	```

	Tras realizar estos pasos ya no podremos conectar mediante SSH a la máquina, utilizando MobaXterm o Putty

+ Instalación de la herramienta git en Ubuntu

	Con esta herramienta podréis descargar todo lo necesarios para instalar el servidor KMS, para ello ejecutaremos los siguientes comandos.

	```console
	apt-get install git -y
	```

### 3.3.3 Pasos de instalación del servidor KMS

Tras los pasos de post instalación ya podemos proceder a instalar el servidor KMS, Para ello seguiremos los siguientes pasos siempre con el usuario root en Ubuntu:

+ Descargar el código de servidor KMS desde el servidor de GitHub

	```console
	cd /opt/
	git clone https://github.com/rafaeljimenez85/kmsServer.git
	```

+ Realizamos una copia del ejecutable `vlmcsd-x64-glibc` y posteriormente lo renombramos a `vlmcsd`

	```console
	cd /opt/binaries/Linux/intel/glibc/
	cp vlmcsd-x64-glibc vlmcsd-x64-glibc.original
	mv vlmcsd-x64-glibc vlmcsd
	```

+ Copiamos el ejecutable de KMS al directorio `/usr/local/sbin`

	```console
	cp /opt/binaries/Linux/intel/glibc/vlmcsd /usr/local/sbin/
	```

+ Damos permiso de ejecución al ejecutable `vlmcsd`

	```console
	cd /usr/local/sbin/
	chmod +x vlmcsd
	```

+ Verificamos que el correcto funcionamiento del ejecutable verificando la versión del mismo.

	```console
	./vlmcsd -V
	``` 

	El resultado tiene que ser el siguiente

	```txt
	root@kms:/usr/local/sbin# ./vlmcsd -V
	vlmcsd 1111, built 2017-06-17 00:53:13 UTC 64-bit
	Compiler: x86_64-linux-gcc 4.9.1
	Intended platform: Intel x86_64 Linux glibc little-endian
	Common flags:
	vlmcsd flags:

	```

+ Creamos el fichero `/lib/systemd/system/vlmcsd.service` que es el fichero de configuración para el arranque automático del demonio en modo servicio en caso de paradas, arranques y reinicios

	```console
	vi /lib/systemd/system/vlmcsd.service
	```

	El fichero debe contener lo siguiente:

		```txt
		[Unit]
		Description=Microsoft KMS emulator
		After=network.target auditd.service

		[Service]
		ExecStart=/usr/local/sbin/vlmcsd -D
		ExecReload=/bin/kill -HUP $MAINPID
		KillMode=process
		Restart=on-failure

		[Install]
		WantedBy=multi-user.target
		Alias=vlmcsd.service
		```

+ Recargar los datos de los ficheros de configuración de systemd

	```console 
	systemctl daemon-reload
	```

+ Habilitar el nuevo servicio vlmcsd 
	```console
	systemctl enable vlmcsd.service
	```

+ Arrancamos el nuevo servicio vlmcsd

	```console
	systemctl start vlmcsd.service
	```


### 3.3.4 Comandos de status, parada y arranque

Estos comandos son simplemente para la posterior gestión en caso de ser necesarios

+ Arranque

	```console
	systemctl start vlmcsd.service
	```

+ Parada

	```console
	systemctl stop vlmcsd.service
	```

+ Estado

	```console
	systemctl status vlmcsd.service
	```

+ Deshabilitar el servicio

	```console
	systemctl disable  vlmcsd.service
	```
## 4. Validar licencias de windows contra el servidor KMS

Tras la instalación de servidor KMS, lo único que nos queda es probar su funcionamiento a la hora de validar licencias de windows. Para ello instalaremos una máquina virtual de windows 10 Pro sin licencia, al finalizar esta instalación solos nos queda ejecutar 2 comando que explicaremos a continuación.

+ **slmgr /ipk <setup key> :** Este comando le indica a Windows las licencias por volumen (que proporciona Microsoft) que debe usar para el servidor KMS.(para más detalle mira el apartado 5.)
+ **slmgr -skms [KMS IP] :** Con este comando le indicamos que windows el nuevo servidor KMS para validar la licencias
+ **slmgr -ato :** Con este comando realizamos el proceso de validación de la licencia contra el servidor KMS

A continuación, os indico los valores necesarios para validar una licencia windows 10 Pro

+ Abrimos un CMD y ejecutamos los siguiente:
	```console
	slmgr /ipk NRG8B-VKK3Q-CXVCJ-9G2XF-6Q84J
	slmgr -skms 192.168.119.190
	slmgr -ato
	```

Una vez realizado esto, Ya tenemos nuestro windows Activado...

## 5. Licencias por volumen para Servidores KMS
Estas licencias las proporciona Microsoft en su página: https://docs.microsoft.com/en-us/windows-server/get-started/kmsclientkeys

### Windows Server 2016

| Operating system edition  | KMS Client Setup Key  |
|:-:|:-:|
|Windows Server 2016 Datacenter   |CB7KF-BWN84-R7R2Y-793K2-8XDDG   |
|Windows Server 2016 Standard   |WC2BQ-8NRM3-FDDYY-2BFGV-KHKQY   |
|Windows Server 2016 Essentials   |JCKRF-N37P4-C2D82-9YXRT-4M63B   |

### Windows 10, version 1709

|Operating system edition   |KMS Client Setup Key   |
|:-:|:-:|
|Windows 10 Professional Workstation   |NRG8B-VKK3Q-CXVCJ-9G2XF-6Q84J   |
|Windows 10 Professional Workstation N   |9FNHH-K3HBT-3W4TD-6383H-6XYWF   |

### Windows 10

| Operating system edition  |KMS Client Setup Key   |   |   |   |
|:-:|:-:|
|Windows 10 Professional   |W269N-WFGWX-YVC9B-4J6C9-T83GX   |
|Windows 10 Professional N   |MH37W-N47XK-V7XM9-C7227-GCQG9   |
|Windows 10 Enterprise   |NPPR9-FWDCX-D2C8J-H872K-2YT43   |
|Windows 10 Enterprise N   |DPH2V-TTNVB-4X9Q3-TJR4H-KHJW4   |
|Windows 10 Education   |NW6C2-QMPVW-D7KKK-3GKT6-VCFB2   |
|Windows 10 Education N   |2WH4N-8QGBV-H22JP-CT43Q-MDWWJ   |
|Windows 10 Enterprise 2015 LTSB   |WNMTR-4C88C-JK8YV-HQ7T2-76DF9   |
|Windows 10 Enterprise 2015 LTSB N   |2F77B-TNFGY-69QQF-B8YKP-D69TJ   |
|Windows 10 Enterprise 2016 LTSB   |DCPHK-NFMTC-H88MJ-PFHPY-QJ4BJ   |
|Windows 10 Enterprise 2016 LTSB N   |QFFDN-GRT3P-VKWWX-X7T3R-8B639   |

### Windows Server 2012 R2 and Windows 8.1

|Operating system edition   |KMS Client Setup Key   |
|:-:|:-:|
|Windows 8.1 Professional   |GCRJD-8NW9H-F2CDX-CCM8D-9D6T9   |
|Windows 8.1 Professional N   |HMCNV-VVBFX-7HMBH-CTY9B-B4FXY   |
|Windows 8.1 Enterprise   |MHF9N-XY6XB-WVXMC-BTDCT-MKKG7   |
|Windows 8.1 Enterprise N   |TT4HM-HN7YT-62K67-RGRQJ-JFFXW   |
|Windows Server 2012 R2 Server Standard   |D2N9P-3P6X9-2R39C-7RTCD-MDVJX   |
|Windows Server 2012 R2 Datacenter   |W3GGN-FT8W3-Y4M27-J84CP-Q3VJ9   |
|Windows Server 2012 R2 Essentials   |KNC87-3J2TX-XB4WP-VCPJV-M4FWM   |

### Windows Server 2012 and Windows 8

|Operating system edition   |KMS Client Setup Key   |
|:-:|:-:|
|Windows 8 Professional   |NG4HW-VH26C-733KW-K6F98-J8CK4   |
|Windows 8 Professional N   |XCVCF-2NXM9-723PB-MHCB7-2RYQQ   |
|Windows 8 Enterprise   |32JNW-9KQ84-P47T8-D8GGY-CWCK7   |
|Windows 8 Enterprise N   |JMNMF-RHW7P-DMY6X-RF3DR-X2BQT   |
|Windows Server 2012   |BN3D2-R7TKB-3YPBD-8DRP2-27GG4   |
|Windows Server 2012 N   |8N2M2-HWPGY-7PGT9-HGDD8-GVGGY   |
|Windows Server 2012 Single Language   |2WN2H-YGCQR-KFX6K-CD6TF-84YXQ   |
|Windows Server 2012 Country Specific   |4K36P-JN4VD-GDC6V-KDT89-DYFKP   |
|Windows Server 2012 Server Standard   |XC9B7-NBPP2-83J2H-RHMBY-92BT4   |
|Windows Server 2012 MultiPoint Standard   |HM7DN-YVMH3-46JC3-XYTG7-CYQJJ   |
|Windows Server 2012 MultiPoint Premium   |XNH6W-2V9GX-RGJ4K-Y8X6F-QGJ2G   |
|Windows Server 2012 Datacenter   |48HP8-DN98B-MYWDG-T2DCC-8W83P   |

### Windows Vista and Windows Server 2008

|Operating system edition   |KMS Client Setup Key   |
|:-:|:-:|
|Windows Vista Business   |YFKBB-PQJJV-G996G-VWGXY-2V3X8   |
|Windows Vista Business N   |HMBQG-8H2RH-C77VX-27R82-VMQBT   |
|Windows Vista Enterprise   |VKK3X-68KWM-X2YGT-QR4M6-4BWMV   |
|Windows Vista Enterprise N   |VTC42-BM838-43QHV-84HX6-XJXKV   |
|Windows Web Server 2008   |WYR28-R7TFJ-3X2YQ-YCY4H-M249D   |
|Windows Server 2008 Standard   |TM24T-X9RMF-VWXK6-X8JC9-BFGM2   |
|Windows Server 2008 Standard without Hyper-V   |W7VD6-7JFBR-RX26B-YKQ3Y-6FFFJ   |
|Windows Server 2008 Enterprise   |YQGMW-MPWTJ-34KDK-48M3W-X4Q6V   |
|Windows Server 2008 Enterprise without Hyper-V   |39BXF-X8Q23-P2WWT-38T2F-G3FPG   |
|Windows Server 2008 HPC   |RCTX3-KWVHP-BR6TB-RB6DM-6X7HP   |
|Windows Server 2008 Datacenter   |7M67G-PC374-GR742-YH8V4-TCBY3   |
|Windows Server 2008 Datacenter without Hyper-V   |22XQ2-VRXRG-P8D42-K34TD-G3QQC   |
|Windows Server 2008 for Itanium-Based Systems   |4DWFP-JF3DJ-B7DTH-78FJB-PDRHK   |
