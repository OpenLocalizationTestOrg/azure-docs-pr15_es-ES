<properties
   pageTitle="Guía de inicio rápido para la instalación manual de HANA de SAP en máquinas virtuales de Azure | Microsoft Azure"
   description="Guía de inicio rápido para la instalación manual de HANA de SAP en máquinas virtuales de Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="hermanndms"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/15/2016"
   ms.author="hermannd"/>

# <a name="quickstart-guide-for-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Guía de inicio rápido para la instalación manual de instancia única SAP HANA en máquinas virtuales de Azure

## <a name="introduction"></a>Introducción

Esta guía de inicio rápido le ayudará a configurar un única instancia SAP HANA prototipo o demostración sistema en máquinas virtuales de Azure por una instalación manual de SAP NetWeaver 7.5 y SAP HANA SP12.
La guía se presupone que el lector está familiarizado con los conceptos básicos de Azure IaaS como cómo implementar máquinas virtuales o redes virtuales a través del portal de Azure o Powershell/CLI, incluida la opción Usar las plantillas de json. Además se espera que el lector está familiarizado con HANA de SAP, SAP NetWeaver y cómo instalarlo en local.

Esperamos que el lector está al tanto de la documentación de SAP Azure general mencionada en la sección información general al final de este artículo.

Debido a la restricción a sistemas de producción no esta guía no trata temas como HA, hacer copia de seguridad DR, de alto rendimiento o consideraciones de seguridad especiales.

Se ha terminado la configuración de ejemplo con dos máquinas virtuales para llevar a cabo una instalación de SAP NetWeaver distribuida a través del modelo de administrador de recursos de Azure como Linux de SAP de Azure solo es compatible con en el Administrador de recursos de Azure y no el modelo clásico. Vínculos a información más detallada sobre el Administrador de recursos de Azure pueden encontrarse en la sección información general al final de este artículo.

Estas son las máquinas virtuales de dos pruebas se utiliza para la instalación de ejemplo:

* (escriba DS3) para hospedar la instancia NW 7.5 ASC + PAS Hana-appsrv
* Hana-dbsrv (tipo GS4) al host HANA SP12
* ambas máquinas virtuales pertenecen a una red virtual Azure (azure hana-prueba vnet)
* Sistema operativo en ambos casos fue SLES 12 SP1


Aunque debe tener en cuenta el hecho de que a partir de julio de 2016 se admite totalmente HANA SAP para sistemas de producción OLAP (blanco y negro) en el tipo de máquina virtual de Azure GS5. Para realizar pruebas donde uno no espera oficial de soporte técnico SAP es adecuado usar algo más pequeñas, como por ejemplo, GS4.
¿Qué debe usarse siempre de HANA de SAP en Azure es el almacenamiento de Azure premium para archivos de datos y de registro HANA: consulte "Disco de instalación" sección más abajo. Acerca de más detalles sobre qué SAP productos son compatibles con Azure, consulte la sección información general al final de este artículo.


La guía describe dos maneras de instalar manualmente HANA de SAP en máquinas virtuales de Azure:

* instalar HANA de SAP a través de administrador de aprovisionamiento de Software de SAP (SWPM) como parte de una instalación de NetWeaver distribuida en el paso "instancia de base de datos"
* instalar HANA SAP utilizando la hdblcm de la herramienta de administrador del ciclo de vida de HANA y, a continuación, instalar NetWeaver posteriormente

También es posible usar SWPM e instalar todos los componentes (HANA SAP, servidor de aplicaciones de SAP, instancia de ASC, SAP gráfico) en una única VM. Esta opción no está descrita en este artículo, pero los elementos que se debe considerar están la misma.

Antes de iniciar una instalación de la sección después de la lista de comprobación debajo sobre cómo configurar las máquinas virtuales de prueba de Azure debe leerse para evitar errores básicas varias lo que ocurrirá cuando se usa solo una configuración de Azure VM predeterminada.


## <a name="checklist-sap-hana-installation-via-sap-swpm"></a>Lista de comprobación instalación de SAP HANA a través de SWPM de SAP

Esta es una simple lista de comprobación de la clave de instalación de elementos relacionados con una instalación manual de SAP HANA instancia única de demostración o creación de prototipos pursposes a través de SWPM de SAP realizando una distribuido 7.5 NW de SAP. Los elementos individuales se explica y se muestra en forma de capturas de pantalla con más detalle en este artículo:

* crear una red virtual Azure que se incluye las máquinas virtuales de dos pruebas 
* implementar dos máquinas virtuales de Azure con OS SLES 12 SP1 a través del modelo de administrador de recursos de Azure 
* adjuntar dos discos de almacenamiento de información estándar en el servidor de aplicación VM (por ejemplo, 75 y 500GB)
* adjuntar cuatro discos en el servidor de base de datos de HANA VM - discos 2 almacenamiento estándar como para el servidor de aplicación VM + 2 discos de almacenamiento de premium (por ejemplo, 2x512GB)
* Dependiendo de los requisitos de tamaño y rendimiento adjuntar varios discos y crear volúmenes seccionados ya sea usando lvm o mdadm en el nivel de sistema operativo dentro de la máquina virtual
* crear sistemas de archivos XFS en los discos vinculados / lógicos volúmenes
* montaje de los sistemas de archivo XFS nuevos en el nivel de sistema operativo. Use un sistema de archivos para mantener el software SAP y el otro, por ejemplo, para el directorio sapmnt y quizás copias de seguridad. En la base de datos de SAP HANA montaje de servidor la XFS sistemas de archivos en los discos de almacenamiento de premium como /hana y /usr/sap esto es todo necesario para evitar que el sistema de archivos de raíz que no es demasiado grande en máquinas virtuales de Azure Linux llena
* Escriba las direcciones ip local de la prueba máquinas virtuales en hosts/etcetera /
* Escriba el parámetro nofail en/etc/fstab
* establecer parámetros de kernel según la nota de SAP de HANA-SLES-12 (vea detalles más abajo en la sección de parámetros de kernel)
* Agregar espacio de intercambio
* Si desea - instalar un escritorio gráfico en la prueba máquinas virtuales. En caso contrario, utilice una instalación remota sapinst
* descargar el software SAP desde el catálogo de soluciones de servicio SAP
* Instale la instancia de SAP ASC en el servidor de aplicación VM
* compartir el directorio sapmnt a través de NFS entre la prueba máquinas virtuales (servidor de aplicación VM es el servidor NFS)
* instalar la instancia de base de datos incluidos HANA a través de SWPM en el servidor de base de datos VM
* instalar PAS en el servidor de aplicación VM
* Inicie MC de SAP y, por ejemplo, conectarse a través de la interfaz gráfica de SAP / HANA Studio 



## <a name="checklist-sap-hana-installation-via-hdblcm"></a>Lista de comprobación instalación de SAP HANA a través de hdblcm

Esta es una simple lista de comprobación de la clave de instalación de elementos relacionados con una instalación manual de SAP HANA instancia única de demostración o creación de prototipos pursposes a través de SWPM de SAP realizando una distribuido 7.5 NW de SAP. Los elementos individuales se explica y se muestra en forma de capturas de pantalla con más detalle en este artículo:

* crear una red virtual Azure que se incluye las máquinas virtuales de dos pruebas 
* implementar dos máquinas virtuales de Azure con OS SLES 12 SP1 a través del modelo de administrador de recursos de Azure 
* adjuntar dos discos de almacenamiento de información estándar en el servidor de aplicación VM (por ejemplo, 75 y 500GB)
* adjuntar cuatro discos en el servidor de HANA DB VM - 2 almacenamiento estándar como para el servidor de aplicación VM + 2 discos de almacenamiento de premium (por ejemplo, 2x512GB)
* Dependiendo de los requisitos de tamaño y rendimiento adjuntar varios discos y crear volúmenes seccionados ya sea usando lvm o mdadm en el nivel de sistema operativo dentro de la máquina virtual
* crear sistemas de archivos XFS en los discos vinculados / lógicos volúmenes
* montaje de los sistemas de archivo XFS nuevos en el nivel de sistema operativo. Use un sistema de archivos para mantener el software SAP y el otro, por ejemplo, para el directorio sapmnt y quizás copias de seguridad. En la base de datos de SAP HANA montaje de servidor la XFS sistemas de archivos en los discos de almacenamiento de premium como /hana y /usr/sap esto es todo necesario para evitar que el sistema de archivos de raíz que no es demasiado grande en máquinas virtuales de Azure Linux llena
* Escriba las direcciones ip local de la prueba máquinas virtuales en hosts/etcetera /
* Escriba el parámetro nofail en/etc/fstab
* establecer parámetros de kernel según la nota de SAP de HANA-SLES-12 (vea detalles más abajo en la sección de parámetros de kernel)
* Agregar espacio de intercambio
* Si desea - instalar un escritorio gráfico en la prueba máquinas virtuales. En caso contrario, utilice una instalación remota sapinst
* descargar el software SAP desde el catálogo de soluciones de servicio SAP
* Crear grupo "sapsys" con el Id. de grupo 1001 en la máquina virtual de HANA DB Server
* instalar HANA de SAP en el servidor de base de datos VM con hdblcm
* Instale la instancia de SAP ASC en el servidor de aplicación VM
* compartir el directorio sapmnt a través de NFS entre la prueba máquinas virtuales (servidor de aplicación VM es el servidor NFS)
* instalar la instancia de base de datos incluidos HANA a través de SWPM en el servidor de base de datos VM
* instalar PAS en el servidor de aplicación VM
* Inicie MC de SAP y, por ejemplo, conectarse a través de la interfaz gráfica de SAP / HANA Studio 




## <a name="prepare-azure-vms-for-manual-installation-of-sap-hana"></a>Preparar máquinas virtuales de Azure instalación manual de HANA de SAP

Este capítulo acerca de cómo preparar las máquinas virtuales de Azure instalación manual de SAP HANA consta de cinco secciones que abarcan los siguientes temas:

* Configuración de disco
* Parámetros de kernel
* File Systems
* / etcetera/hosts
* / etcetera/fstab


### <a name="disk-setup"></a>Configuración de disco

El sistema de archivos raíz en una VM Linux en Azure es de tamaño limitado. Por lo tanto, es necesario adjuntar espacio adicional a una máquina virtual para ejecutar SAP. En el caso de un servidor de aplicación SAP que VM utilizada en un entorno de prototipo puros o demostración es adecuado usar discos de Azure almacenamiento estándar. Mientras que para los archivos de datos y de registro de SAP HANA DB - deben usarse discos de almacenamiento de Azure Premium incluso en un entorno de producción no.

Vea algunos detalles acerca de cómo adjuntar discos a una VM Linux [aquí](virtual-machines-linux-add-disk.md)

Cuando se trata de la caché de disco Azure - uno debe utilizar "Ninguno" para los discos que se utilizará para almacenar los registros de transacciones HANA. Para HANA leer los archivos de datos está bien usar almacenamiento en caché. Como HANA es una base de datos en memoria depende el patrón de uso global cuánto la memoria caché de lectura en el nivel de disco Azure mejorará el rendimiento (por ejemplo, iniciar HANA y leer datos desde el disco en la memoria).

Ver detalles sobre el almacenamiento de Azure Premium [aquí](../storage/storage-premium-storage.md)

[Aquí](https://github.com/Azure/azure-quickstart-templates) encontrará plantillas de ejemplo json para crear máquinas virtuales.
La "101-vm-simple-linux" muestra el aspecto de una plantilla básica como incluidos en la sección de almacenamiento que agrega un disco de datos de 100GB.

[En este artículo](virtual-machines-linux-sap-on-suse-quickstart.md) se incluye información sobre cómo buscar una imagen SUSE a través de Powershell o CLI y la importancia que desea adjuntar un disco a través de UUID.


Dependiendo del tamaño de los requisitos del sistema y el rendimiento podría ser necesario adjuntar varios discos en lugar de uno y versiones posteriores en crear un conjunto en esos discos en el nivel de sistema operativo de bandas. Estos son los dos aspectos, ¿por qué uno crearía un conjunto en varios discos de Azure de bandas:

* aumentar el rendimiento
* necesita un sistema de archivos solo > 1TB como el límite de tamaño de disco Azure actual es 1TB (a partir de julio de 2016)


Aquí encontrará más información acerca de las dos herramientas principales para configurar bandas:

[Artículo sobre cómo usar mdadm para configurar raid de software de Linux en una máquina virtual de Azure](virtual-machines-linux-configure-raid.md)

[Artículo acerca de cómo configurar el Administrador de volumen lógico en máquinas virtuales Linux Azure](virtual-machines-linux-configure-lvm.md)



![](./media/virtual-machines-linux-sap-hana-get-started/image003.jpg)

En la prueba de dos discos de Azure almacenamiento estándar entorno se han adjuntado al servidor de aplicaciones SAP VM. Uno se usó para almacenar todo el software SAP para la instalación (por ejemplo, NetWeaver 7.5, el gráfico de SAP, SAP HANA... ) y el otro a tiene suficiente espacio para cualquier cosa que podrían ser necesario (por ejemplo, hacer copia de seguridad, datos de prueba), así como el directorio sapmnt (por ejemplo, los perfiles SAP) para compartirse entre máquinas virtuales de todos los que pertenecen a la misma horizontal SAP.

![](./media/virtual-machines-linux-sap-hana-get-started/image004.jpg)

Contra el servidor de aplicación discos de VM cuatro se han adjuntado al servidor SAP HANA VM. Como antes de que se utilizaron dos discos para mantener el software SAP (uno puede compartir el disco de software SAP a través de NFS) y tiene suficiente espacio, por ejemplo, para la copia de seguridad. Los dos discos adicionales estaban discos de almacenamiento de Azure Premium para mantener los archivos de registro y datos de HANA de SAP, así como el directorio de /usr/sap.


### <a name="kernel-parameters"></a>Parámetros de kernel


SAP HANA requiere configuración específica de kernel de Linux que no forman parte de las imágenes de la Galería de Azure estándar y tiene que establecer manualmente. Hay una nota específica de SAP que se describe la configuración. 


Nota SAP SAP HANA DB: OS configuración recomendada para SLES 12 / SLES para 12 de aplicaciones de SAP: [SAP nota 2205917](https://launchpad.support.sap.com/#/notes/2205917)

Caché de página reagrding tema adicionales relacionados con la ejecución HANA de SAP en SLES se puede encontrar [aquí](https://www.suse.com/documentation/sles_for_sap/singlehtml/sles_for_sap_guide/sles_for_sap_guide.html#sec.s4s.configure.page-cache) en capítulo 6.1 núcleo: límite de caché de página

También hay una nota de SAP sobre el límite de caché de la página [1557506 de nota de SAP](https://service.sap.com/sap/support/notes/1557506)

SLES 12 tiene una nueva herramienta que reemplaza la utilidad sapconf antiguo. Es "adm ajustado" y hay un perfil de SAP HANA especial. Uno puede encontrar más detalles acerca de esta herramienta los dos vínculos siguientes.

Puede encontrar documentación SLES sobre adm ajustado de perfil sap hana [aquí](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html)

Documentación de SLES sobre adm ajustado perfil sap-hana - sistemas de ajuste de capítulo 6.2 para cargas de trabajo de SAP con adm ajustado - se encuentra [aquí](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf)


![](./media/virtual-machines-linux-sap-hana-get-started/image005.jpg)

Aquí se puede ver cómo "adm ajustado" cambiado la transparent_hugepage, así como los valores de numa_balancing según la configuración de SAP HANA necesario.


Para hacer que la configuración de kernel SAP HANA permanente uno tiene usar grub2 en SLES 12. Puede encontrar más información sobre grub2 [aquí](https://www.suse.com/documentation/sled-12/book_sle_admin/data/sec_grub2_file_structure.html)


![](./media/virtual-machines-linux-sap-hana-get-started/image006.jpg)

Esta captura de pantalla muestra cómo la configuración de kernel se ha modificado en el archivo de configuración y, a continuación, se "compilado" a través de grub2 mkconfig


![](./media/virtual-machines-linux-sap-hana-get-started/image007.jpg)

Otra opción sería cambiar la configuración a través de Yast y el parámetro de kernel de cargador de inicio.


### <a name="filesystems"></a>File Systems 

![](./media/virtual-machines-linux-sap-hana-get-started/image008.jpg)

Aquí se pueden ver los dos sistemas de archivos que se crearon en el servidor de aplicación SAP VM encima de los dos discos de almacenamiento de Azure conexión estándar. Ambos File Systems son del tipo XFS y colocado en /sapdata y /sapsoftware.

No es obligatorio para hacer esto. Existen diferentes opciones cómo estructura el espacio en disco.
El aspecto más importante es evitar que el sistema de archivos raíz se queda sin espacio. 


![](./media/virtual-machines-linux-sap-hana-get-started/image009.jpg)

Acerca de la máquina virtual de SAP HANA DB es importante saber que durante una instalación de base de datos a través de sapinst (swpm) y solo usando la opción de instalación "típica" simple se instalará cosas en /hana y /usr/sap de forma predeterminada. La configuración predeterminada de copia de seguridad de registro de SAP HANA es, por ejemplo, en /usr/sap.
Como antes de que es clave para evitar el sistema de archivos raíz se queda sin espacio. Por lo tanto, uno debe asegurarse de que hay suficiente espacio libre en /hana y /usr/sap antes de instalar HANA de SAP a través de swpm.

[En este artículo](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm) de SAP describa el diseño de un sistema de archivos estándar de HANA de SAP 


![](./media/virtual-machines-linux-sap-hana-get-started/image010.jpg)

Al instalar NetWeaver de SAP en una imagen de la Galería de SLES 12 Azure estándar será un mensaje que no hay ningún espacio de intercambio. Para quitar este mensaje uno podría, por ejemplo, agregar manualmente un archivo de intercambio como se describe en este documento mediante dd, mkswap y swapon. Buscar "Agregar un intercambiar archivos manualmente" en [este artículo](https://www.suse.com/documentation/sled-12/book_sle_deployment/data/sec_yast2_i_y2_part_expert.html)

Otra opción es configurar el espacio de intercambio mediante el agente de VM Linux. Puede encontrar más información [aquí](virtual-machines-linux-agent-user-guide.md)


### <a name="etchosts"></a>/ etcetera/hosts

![](./media/virtual-machines-linux-sap-hana-get-started/image011.jpg)

Otro aspecto importante antes de comenzar a instalar SAP es incluir nombres de host y direcciones IP de las máquinas virtuales de SAP en el archivo/etc/hosts. Uno debe implementar todas las máquinas virtuales de SAP dentro de una red virtual Azure y luego use las direcciones IP internas.

### <a name="etcfstab"></a>/ etcetera/fstab

![](./media/virtual-machines-linux-sap-hana-get-started/image000c.jpg)

Durante la fase de pruebas resultó para ser una buena idea agregar el parámetro nofail a fstab. Si algo va mal con los discos de la máquina virtual ¿todavía surgen y se bloquea en el proceso de inicio. Pero uno debe tener en cuenta que en este caso el espacio en disco adicional podría no estar disponible y procesos podrían rellenar hacia arriba el sistema de archivos de raíz. En caso de que /hana sería falta HANA SAP no iniciar aunque en absoluto.


## <a name="install-graphical-gnome-desktop-on-sles-12"></a>Instalar gráfica escritorio Gnome en SLES 12

Este capítulo consta de dos secitions que abarcan los siguientes temas:

* Instalación de escritorio Gnome y xrdp en SLES 12
* Ejecutar basada en Java SAP MC con Firefox en SLES 12

También se podrían usar alternativas como Xterminal, VNC pero a partir de 2016 Sep este artículo describen solo xrdp.

### <a name="installation-of-gnome-desktop-and-xrdp-on-sles-12"></a>Instalación de escritorio Gnome y xrdp en SLES 12

Especialmente para aquellos que tienen el fondo de Microsoft Windows y le gustaría usar un escritorio gráfico directamente dentro de las máquinas virtuales Linux de SAP para ejecutar Firefox, Sapinst, el gráfico de SAP, SAP MC o HANA Studio y es posible conectarse a la máquina virtual a través de RDP desde un equipo de Microsoft Windows, hay una forma sencilla de conseguirlo. Aunque no puede ser adecuado, por ejemplo, para un servidor de base de datos de producción es correcto para un entorno de prototipo puros o demostración. Estos son los pasos para instalar la versión de escritorio de Gnome en una máquina virtual de Azure SLES 12:

instalar la versión de escritorio de gnome mediante el comando siguiente (por ejemplo, en una ventana masilla):

   zypper de trama de -t gnome basic

a continuación, instalar xrdp para permitir la conexión a la máquina virtual a través de RDP:

   zypper en xrdp

Editar /etc/sysconfig/windowmanager y establezca el Administrador de windows predeterminado en Gnome:

   DEFAULT_WM = "gnome"

Ejecute chkconfig para asegurarse de que xrdp se inicia automáticamente después de reiniciar el equipo: 

  chkconfig-xrdp de nivel 3 en

en caso de que debe haber un problema con la conexión RDP intente volver a iniciar (quizá fuera de una ventana masilla):

  reiniciar /etc/xrdp/xrdp.sh

en caso de que el reinicio xrdp como mencionados no funcionan comprobar si hay un archivo .pid y quitarlo:

  Compruebe /var/run y busque xrdp.pid   
  quitar e inténtelo de nuevo el reinicio



### <a name="sap-mc"></a>MC DE SAP


Para iniciar gráfica basada en Java SAP MC fuera de Firefox cuando se ejecuta en una máquina virtual de Azure SLES 12 después de instalar al Gnome escritorio como se describe en la sección anterior uno producirá un error porque falta el complemento del explorador de Java.

La dirección URL para iniciar la MC SAP es <server>: 5 < instance_number > 13

Puede encontrar más información [aquí](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)


![](./media/virtual-machines-linux-sap-hana-get-started/image013.jpg)

En la captura de pantalla anterior se puede ver cómo el mensaje de error puede tener un aspecto similar al falta el complemento del explorador de Java. 

![](./media/virtual-machines-linux-sap-hana-get-started/image014.jpg)

Una opción para resolver el problema es instalar el complemento que falta a través de Yast.

![](./media/virtual-machines-linux-sap-hana-get-started/image015.jpg)

Repetir la dirección URL de SAP MC aparecerá un poco tiempo de diálogo la primera pregunta si desea activar el complemento.


Un problema adicional que podría aparecer es un mensaje de error sobre un archivo que falta: javafx.properties esto es muy probable que relacionados con la instalación de Java 1,8 que es necesario para 7.4 de gráfico de SAP

La versión de IBM Java vista a través de Yast no incluye este archivo. La solución es una descarga de Java de Oracle.
Puede encontrar un artículo que trata sobre este problema concreto [aquí](https://scn.sap.com/thread/3908306)



## <a name="manual-sap-hana-installation-via-swpm-as-part-of-a-netweaver-75-installation"></a>Instalación manual de HANA de SAP a través de SWPM como parte de una instalación de NetWeaver 7.5


La siguiente lista de capturas de pantalla muestra los pasos clave de instalación de SAP NetWeaver 7.5 y SAP HANA SP12 a través de SWPM (sapinst). Como parte de un 7.5 NW instalación SWPM tiene las capacidades a instalar también la base de datos HANA como una sola instancia.


![](./media/virtual-machines-linux-sap-hana-get-started/image012.jpg)

Para la prueba de ejemplo se instaló un solo servidor de aplicación ABAP entorno. La opción "Sistema distribuido" se utilizó para instalar la instancia de ASC y la instancia del servidor de aplicación principal en una máquina virtual de Azure y SAP HANA como el sistema de base de datos en una máquina virtual de Azure otra.


![](./media/virtual-machines-linux-sap-hana-get-started/image016.jpg)

Una vez que la instancia de ASC está instalada en el servidor de aplicación VM y se establece en "verde" en SAP MC tiene el directorio sapmnt que incluye, por ejemplo, el directorio de perfil SAP que deben compartirse con el servidor de base de datos de SAP HANA VM.
El paso de instalación DB necesita obtener acceso a esta información. La mejor manera es utilizar NFS, que se pueden configurar mediante Yast.


![](./media/virtual-machines-linux-sap-hana-get-started/image017b.jpg)

En la aplicación de servidor VM del directorio sapmnt debe compartirse a través de NFS mediante el uso del opciones "rw" así como "no_root_squash". El valor predeterminado es "ro" y "root_squash", lo que puede causar problemas al instalar la instancia de base de datos.


![](./media/virtual-machines-linux-sap-hana-get-started/image018b.jpg)

En el servidor de base de datos de SAP HANA VM la sapmnt compartir desde el servidor de aplicación VM debe estar configurada a través de "Cliente NFS" (por ejemplo, con la Ayuda de Yast)


![](./media/virtual-machines-linux-sap-hana-get-started/image019.jpg)

A continuación, uno tiene que iniciar sesión en el servidor de base de datos de SAP HANA VM y empiece a SWPM para realizar el paso siguiente de una instalación NW 7.5 distribuida - "Instancia de base de datos".


![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

Relacionados con la instalación de SAP HANA no hay en realidad demasiado introduzca después de seleccionar una instalación "típica". Además, la ruta de acceso a los medios de installatiom uno tiene que introducir un SID DB, el nombre de host, el número de instancia y la contraseña de administrador del sistema de base de datos.

 
![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

Siguiente paso es escribir la contraseña para el esquema DBACOCKPIT.



![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

A continuación, viene la pregunta de la contraseña de esquema SAPABAP1.


![](./media/virtual-machines-linux-sap-hana-get-started/image023.jpg)

Al final debe ser solo verde las marcas de verificación delante de cada fase del proceso de instalación de base de datos y el cuadro de mensaje poco que emerge debe indicar "ejecución de... Instancia de base de datos ha completado".

 
![](./media/virtual-machines-linux-sap-hana-get-started/image024.jpg)

Después de la instalación correcta MC SAP también deben mostrar la instancia de base de datos como "verde" y la lista completa de procesos de SAP HANA (por ejemplo, hdbindexserver, hdbcompileserver)


![](./media/virtual-machines-linux-sap-hana-get-started/image025.jpg)

Esta captura de pantalla muestra partes de la estructura del archivo en /hana/shared que SWPM se crea durante la instalación de HANA. Se ha producido ninguna opción para especificar una ruta de acceso diferente. Por lo tanto, es tan importante para montaje espacio adicional en /hana antes de la instalación de SAP HANA a través de SWPM para evitar que el sistema de archivos raíz se ejecuta sin espacio libre.


![](./media/virtual-machines-linux-sap-hana-get-started/image026.jpg)

Aquí se puede ver lo mismo, como se describe antes del directorio /usr/sap.


![](./media/virtual-machines-linux-sap-hana-get-started/image027b.jpg)

El último paso de la instalación de ABAP distribuido es la "instancia de servidor de aplicaciones de primaria"


![](./media/virtual-machines-linux-sap-hana-get-started/image028b.jpg)

Una vez que tiene instalado el PAS, así como la interfaz gráfica de SAP uno puede comprobar a través de la transacción "dbacockpit" todo parece hacia la derecha con la instalación de HANA de SAP.

 
![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

Como último paso uno podría instalar SAP HANA Studio en el servidor de aplicación SAP VM y conectarse a la instancia de HANA de SAP que se ejecuta en el servidor de base de datos VM.




## <a name="manual-sap-hana-installation-via-hana-life-cycle-manager-tool-hdblcm"></a>Instalación manual de SAP HANA mediante hdblcm de herramienta de administrador de ciclo de vida HANA


Además de instalar HANA SAP como parte de una instalación distribuida mediante SWPM también es más realista para instalar primero independiente HANA utilizando hdblcm y luego instale, por ejemplo, SAP NetWeaver 7.5 posteriormente. La lista de capturas de pantalla que muestra cómo funciona esto.

Hay tres fuentes de información acerca de la herramienta de hdblcm HANA:

[Elegir la HDBLCM HANA SAP correcta para su tarea](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)

[Herramientas de administración de ciclo de vida HANA de SAP](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)

[SAP HANA Server Guía de instalación y actualización](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)



![](./media/virtual-machines-linux-sap-hana-get-started/image030.jpg)

Para evitar problemas más adelante con un valor de Id. de grupo predeterminado para la \<HANA SID\>usuario adm (creado por la herramienta hdblcm) uno debe definir un nuevo grupo denominado "sapsys" con el Id. de grupo 1001 antes de instalar HANA SAP con hdblcm.




![](./media/virtual-machines-linux-sap-hana-get-started/image031.jpg)

Hora de hdblcm la primera habrá un menú de inicio simple donde uno tiene que seleccionar el elemento de inicio 1 "instalar nuevo sistema"



![](./media/virtual-machines-linux-sap-hana-get-started/image032.jpg)

En esta captura de pantalla se pueden ver todas las opciones de clave que se hayan especificado antes. Importante: directorios que se denomina para volúmenes de datos y de registro HANA, así como la ruta de instalación (/ hana/compartido en este ejemplo) y/usr/sap no debe ser parte del sistema de archivos de raíz pero pertenecen a los discos de datos de Azure que se han adjuntado a la máquina virtual tal como se describe en la sección Configuración de máquina virtual de Azure. Esto evitará el riesgo de que el sistema de archivos raíz podría quedarse sin espacio.
También se puede ver que el usuario de administración HANA tiene el identificador de usuario 1005 y forma parte del grupo sapsys (id 1001) que se ha definido antes de la instalación.



![](./media/virtual-machines-linux-sap-hana-get-started/image033.jpg)

Uno puede comprobar la HANA \<HANA SID\>detalles del usuario adm (azdadm en este ejemplo) en/etcetera/contraseña



![](./media/virtual-machines-linux-sap-hana-get-started/image034.jpg)

Después de instalar HANA SAP con hdblcm puede verse en SAP HANA Studio. El esquema de SAPABAP1 que incluye, por ejemplo, todas las tablas de SAP NetWeaver aún no está disponible.



![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

Después de instalar SAP HANA uno puede instalar SAP NetWeaver encima de ellas. En este ejemplo que se realizó utilizando una "instalación distribuida" a través de SWPM como se describe en la sección correspondiente anterior.
Cuando se instala la instancia de base de datos a través de SWPM uno solo tiene que introducir los mismos datos que antes con hdblcm (por ejemplo, nombre de host, HANA SID, número de instancia). SWPM, a continuación, se utiliza la instalación de HANA existente y agregar esquemas adicionales.



![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

Esta es la imagen del paso de instalación SWPM donde uno tiene que introducir datos importantes sobre el esquema DBACOCKPIT.


![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

A continuación, SWPM espera introducir datos sobre el esquema de SAPABAP1.


![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

Una vez finalizada la instalación de instancia de base de datos SWPM se puede ver el esquema de SAPABAP1 en HANA Studio.



![](./media/virtual-machines-linux-sap-hana-get-started/image039b.jpg)

Y por último después de la instalación del servidor de aplicaciones SAP y SAP gráfico uno debería poder comprobar la instancia de HANA DB con transacción "dbacockpit".




## <a name="general-information-related-to-sap-azure-certifications-running-sap-hana-on-azure-and-sap-software-download"></a>Información general relacionada con los certificados de Azure de SAP, ejecuta SAP HANA en descarga de software de Azure y SAP

* documento de Azure SAP general sobre la ejecución de SAP en Azure con el sistema operativo Windows en modo clásico: [Uso de SAP en máquinas virtuales de Windows en Azure](virtual-machines-windows-classic-sap-get-started.md)

* información sobre las plantillas SAP existentes para el uso de los clientes: [Azure tutorial de plantillas para SAP](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/)

* documento de Azure SAP general sobre la ejecución de SAP en Azure con Linux OS en el modelo de administrador de recursos de Azure: [Uso de SAP en máquinas virtuales de Linux (VM)](virtual-machines-linux-sap-get-started.md)

* certificadas directorio de hardware de HANA de SAP que se enumeran los tipos de Azure VM son compatibles para producción: [Directorio de Hardware de certificados SAP HANA®](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)

* información sobre los tamaños de máquina virtual especialmente para cargas de trabajo de Linux: [tamaños para máquinas virtuales de Azure](virtual-machines-linux-sizes.md)

* Nota SAP que enumere todos compatibles con productos SAP en Azure y tipos de Azure VM admitidos para SAP: [SAP nota 1928533](https://launchpad.support.sap.com/#/notes/1928533/E)

* Nota SAP sobre SAP "supervisión"mejorado con máquinas virtuales de Linux en Azure: [2191498 nota de SAP](https://launchpad.support.sap.com/#/notes/2191498/E)

* HANA de SAP que se ofrece en Azure "Grandes instancias". Es importante entender que no trata sobre cómo ejecutar HANA de SAP en máquinas virtuales de Azure, pero en un híbrido entorno donde se ejecutan los servidores de aplicación SAP en máquinas virtuales de Azure pero HANA SAP se ejecuta en servidores completos: [2316233 nota de SAP](https://launchpad.support.sap.com/#/notes/2316233/E)

* Nota SAP con información sobre SAPOSCOL en Linux: [1102124 nota de SAP](https://launchpad.support.sap.com/#/notes/1102124/E)

* Clave supervisión métricas de SAP en Microsoft Azure: [SAP nota 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)

* Información sobre el Administrador de recursos de Azure: [información general del Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md)

* Información sobre cómo implementar máquinas virtuales de Linux a través de plantillas: [implementar y administrar máquinas virtuales mediante plantillas de administrador de recursos de Azure y Azure CLI](virtual-machines-linux-cli-deploy-templates.md)

* Comparación de implementación modelos entre el Administrador de recursos de Azure y clásica: [Administrador de recursos de Azure frente a la implementación clásica: comprender los modelos de implementación y el estado de los recursos](../resource-manager-deployment-model.md)

* Descargar NetWeaver 7.5 para Linux/HANA desde el catálogo de soluciones de servicio SAP:![](./media/virtual-machines-linux-sap-hana-get-started/image001.jpg)

* Descargar HANA SP12 plataforma Edition desde el catálogo de soluciones de servicio SAP:![](./media/virtual-machines-linux-sap-hana-get-started/image002.jpg)

