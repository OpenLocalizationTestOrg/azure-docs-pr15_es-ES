<properties
   pageTitle="Uso de DNS dinámico para registrar nombres de host"
   description="Esta página ofrece detalles sobre cómo configurar el DNS dinámico para registrar los nombres de host en sus propios servidores DNS."
   services="dns"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="" />
<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="sewhee" />

# <a name="using-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Uso de DNS dinámico para registrar nombres de host en su propio servidor DNS

[Azure proporciona resolución de nombres](virtual-networks-name-resolution-for-vms-and-role-instances.md) para máquinas virtuales (VM) y las instancias de la función. Sin embargo, cuando la resolución de nombres debe ir más allá de los proporcionados por Azure, puede proporcionar sus propios servidores DNS. Esto le da la capacidad de personalizar la solución de DNS para adaptarla a sus necesidades. Por ejemplo, tendrá acceso a los recursos locales mediante el controlador de dominio de Active Directory.

Cuando se hospedan los servidores DNS personalizados como máquinas virtuales de Azure puede reenviar consultas de nombre de host para el mismo vnet a Azure para resolver los nombres de host. Si no desea usar esta ruta, puede registrar los nombres de host de máquina virtual en su servidor DNS con DNS dinámico.  Azure no tiene la capacidad (por ejemplo, credenciales) directamente crear registros de los servidores DNS, por lo que a menudo se necesitan medidas alternativas. Estas son algunas situaciones comunes con alternativas.

## <a name="windows-clients"></a>Clientes de Windows

Los clientes no unidos a un dominio de Windows intentan actualizaciones de DNS dinámico (DDNS) no seguras cuando inicia, o cuando cambia su dirección IP. El nombre DNS es el nombre de host más el sufijo DNS principal. Azure deja en blanco el sufijo DNS principal, pero puede establecer en la máquina virtual, a través de la [interfaz de usuario](https://technet.microsoft.com/library/cc794784.aspx) o [mediante la automatización](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix).

Clientes de Windows dominio registran sus direcciones IP con el controlador de dominio mediante DNS dinámica segura. El proceso de unión de dominio establece el sufijo DNS principal en el cliente y crea y mantiene la relación de confianza.

## <a name="linux-clients"></a>Clientes Linux

Clientes Linux, en general, no se registran con el servidor DNS en el inicio, se supone que lo hace haberlo. Los servidores DHCP de Azure no tiene la posibilidad de o credenciales para registrar registros de servidor DNS.  Puede usar una herramienta denominada *nsupdate*, que se incluye en el paquete de enlace, para enviar las actualizaciones DNS dinámicas. Dado que el protocolo DNS dinámico es normalizado, puede usar *nsupdate* incluso cuando no está usando el enlace en el servidor DNS.

Puede usar los enlaces proporcionados por el cliente DHCP para crear y mantener la entrada de nombre de host del servidor DNS. Durante el ciclo DHCP, el cliente ejecuta las secuencias de comandos en */etc/dhcp/dhclient-exit-hooks.d/*. Esto puede usarse para registrar la nueva dirección IP mediante *nsupdate*. Por ejemplo:

        #!/bin/sh
        requireddomain=mydomain.local

        # only execute on the primary nic
        if [ "$interface" != "eth0" ]
        then
            return
        fi

        # when we have a new IP, perform nsupdate
        if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
           [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
        then
            host=`hostname`
            nsupdatecmds=/var/tmp/nsupdatecmds
              echo "update delete $host.$requireddomain a" > $nsupdatecmds
              echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
              echo "send" >> $nsupdatecmds

              nsupdate $nsupdatecmds
        fi

        #done
        exit 0;

También puede usar el comando *nsupdate* para realizar las actualizaciones de DNS dinámicas seguras. Por ejemplo, cuando usa un servidor DNS enlazar, es un par de clave pública y privada [generadas](http://linux.yyz.us/nsupdate/).  El servidor DNS está [configurado](http://linux.yyz.us/dns/ddns-server.html) con la parte de la clave pública para que pueda comprobar la firma en la solicitud. Debe usar la opción *-k* para proporcionar que la clave de par a *nsupdate* en orden para el DNS dinámico actualiza solicitud estén firmados.

Cuando usa un servidor DNS de Windows, puede usar la autenticación Kerberos con el parámetro *-g* en *nsupdate* (no disponible en la versión de Windows de *nsupdate*). Para hacerlo, utilice *kinit* para cargar las credenciales (por ejemplo de un [archivo de tabla de claves](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)). A continuación, *nsupdate -g* recoger las credenciales de la memoria caché.

Si es necesario, puede agregar un sufijo de búsqueda DNS a sus máquinas virtuales. El sufijo DNS especificado en el archivo */etc/resolv.conf* . La mayoría distros Linux automáticamente para administrar el contenido de este archivo, por lo tanto, no se puede editar. Sin embargo, puede reemplazar el sufijo con el comando de *Reemplazar* del cliente DHCP. Para ello, en */etc/dhcp/dhclient.conf*, agregue:

        supersede domain-name <required-dns-suffix>;

