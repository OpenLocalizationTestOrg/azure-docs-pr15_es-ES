<properties
    pageTitle="Configurar DHCPv6 para máquinas virtuales de Linux | Microsoft Azure"
    description="Cómo configurar DHCPv6 para máquinas virtuales de Linux."
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    keywords="IPv6, equilibrador de carga de azure, pila dual, ip pública, ipv6 nativa, mobile, iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="configuring-dhcpv6-for-linux-vms"></a>Configurar DHCPv6 para máquinas virtuales de Linux

Algunas de las imágenes de máquina virtual Linux en Azure Marketplace no tienen DHCPv6 configurado de manera predeterminada. Para admitir IPv6, debe estar configurado en DHCPv6 dentro de la distribución de Linux OS que está utilizando. Diferentes distribuciones de Linux tienen distintas formas de configurar DHCPv6 porque usan diferentes paquetes.

>[AZURE.NOTE] Se han configurado previamente con DHCPv6 recientes SUSE Linux y CoreOS imágenes en Azure Marketplace. Cambios adicionales no son necesarios para usar estas imágenes.

Este documento describe cómo habilitar DHCPv6 para que la máquina virtual de Linux Obtiene una dirección IPv6.

>[AZURE.WARNING] Incorrectamente editar archivos de configuración de red que se pueden perder el acceso a la red a su máquina virtual. Se recomienda probar sus cambios de configuración en sistemas que no sean de producción. Las instrucciones de este artículo se han probado en las últimas versiones de las imágenes de Linux de Azure Marketplace. Consulte la documentación de su versión específica de Linux para obtener instrucciones detalladas.

## <a name="ubuntu"></a>Ubuntu

1. Editar el archivo `/etc/dhcp/dhclient6.conf` y agregue la línea siguiente:

        timeout 10;

2. Editar la configuración de red para la interfaz eth0 con la siguiente configuración:

    * En **Ubuntu 12.04 y 14.04**, edite el archivo`/etc/network/interfaces.d/eth0.cfg`
    * En **Ubuntu 16.04**, edite el archivo`/etc/network/interfaces.d/50-cloud-init.cfg`

    ```bash
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. Renovar dirección IPv6:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. Editar el archivo `/etc/dhcp/dhclient6.conf` y agregue la línea siguiente:

        timeout 10;

2. Editar el archivo `/etc/network/interfaces` y agregue la siguiente configuración:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Renovar dirección IPv6:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel--centos--oracle-linux"></a>RHEL / CentOS / Linux de Oracle

1. Editar el archivo `/etc/sysconfig/network` y agregue el parámetro siguiente:

        NETWORKING_IPV6=yes

2. Editar el archivo `/etc/sysconfig/network-scripts/ifcfg-eth0` y agregar los dos parámetros siguientes:

        IPV6INIT=yes
        DHCPV6C=yes

3. Renovar dirección IPv6:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11--opensuse-13"></a>SLES 11 & openSUSE 13

Se han configurado previamente con DHCPv6 recientes SLES y openSUSE imágenes en Azure. Cambios adicionales no son necesarios para usar estas imágenes. Si tiene una máquina virtual basada en una imagen SUSE personalizada o versiones anterior, siga estos pasos:

1. Instalar el `dhcp-client` empaquetar, si es necesario:

    ```bash
    # sudo zypper install dhcp-client
    ```

2. Editar el archivo `/etc/sysconfig/network/ifcfg-eth0` y agregue el parámetro siguiente:

        DHCLIENT6_MODE='managed'

3. Renovar la dirección IPv6:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 y openSUSE salto

Se han configurado previamente con DHCPv6 recientes SLES y openSUSE imágenes en Azure. Cambios adicionales no son necesarios para usar estas imágenes. Si tiene una máquina virtual basada en una imagen SUSE personalizada o versiones anterior, siga estos pasos:

1. Editar el archivo `/etc/sysconfig/network/ifcfg-eth0` y reemplazar este parámetro

        #BOOTPROTO='dhcp4'

    con el siguiente valor:

        BOOTPROTO='dhcp'

2. Agregar el siguiente parámetro a `/etc/sysconfig/network/ifcfg-eth0`:

        DHCLIENT6_MODE='managed'

3. Renovar la dirección IPv6:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Se han configurado previamente con DHCPv6 imágenes CoreOS recientes en Azure. Cambios adicionales no son necesarios para usar estas imágenes. Si tiene una máquina virtual basada en una imagen de CoreOS anterior o personalizada, siga estos pasos:

1. Editar el archivo`/etc/systemd/network/10_dhcp.network`

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Renovar la dirección IPv6:

    ```bash
    # sudo systemctl restart systemd-networkd
    ```
