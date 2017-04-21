<properties
   pageTitle="Infraestructura de actualización de sombrero rojo (RHUI) | Microsoft Azure"
   description="Obtenga más información acerca de infraestructura de actualización de sombrero rojo (RHUI) para instancias de Red sombrero Enterprise Linux a petición en Microsoft Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="BorisB2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="borisb"/>

# <a name="red-hat-update-infrastructure-rhui-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Infraestructura de actualización de sombrero rojo (RHUI) para máquinas virtuales de on demand rojos sombrero Enterprise Linux en Azure

Máquinas virtuales creadas a partir de las imágenes a petición de Red sombrero Enterprise Linux (RHEL) en Azure Marketplace están registradas para tener acceso a la infraestructura de actualización de sombrero (RHUI) rojo implementado en Azure.  Las instancias RHEL a petición tienen acceso a un repositorio de yum regionales y puede recibir actualizaciones incrementales.

La lista de repositorio yum, que es administrada por RHUI, está configurada en su instancia RHEL durante el aprovisionamiento. No es necesario realizar ninguna configuración adicional - ejecutar `yum update` después de la instancia RHEL está lista para obtener las actualizaciones más recientes.

> [AZURE.NOTE] Infraestructura de Azure RHUI se ha actualizado recientemente (septiembre de 2016) y requiere cambios en la configuración de las instancias RHEL existentes para el acceso sin interrupciones a la RHUI de Azure. Consulte la sección de actualización de infraestructura de Azure RHUI para obtener más detalles.


## <a name="rhui-azure-infrastructure-update"></a>Actualización de la infraestructura de Azure de RHUI
A partir de septiembre de 2016, Azure tiene un nuevo conjunto de servidores de infraestructura de actualización de sombrero de rojo (RHUI). Estos servidores se implementan con [El administrador del tráfico de Azure]( https://azure.microsoft.com/services/traffic-manager/) para que un único extremo (rhui 1.micrsoft.com) puede ser usado por cualquier máquina virtual independientemente de la región. También se utiliza un certificado SSL que está encadenado a una entidad de certificación conocido (raíz de Baltimore). Hacer que esta actualización automática sería peligroso algunos de los clientes que tengan ACL o las tablas de enrutamiento personalizadas para los servidores de actualización RHUI, por lo que esta actualización es "participar en." Pasos manuales para integrado para estos nuevos servidores están disponibles en esta página y una secuencia de comandos completa integrado de forma automática (tras la comprobación de los pasos individuales). Las imágenes de RHEL PAYG nuevas de Azure Marketplace (versiones fechas de 2016 de septiembre o posteriores) automáticamente se apunten a los servidores de Azure RHUI nuevos y no se requiere ninguna acción adicional.

### <a name="the-new-azure-rhui-infrastructure-onboarding-timeline"></a>La escala de tiempo de integrado de infraestructura de Azure RHUI nueva

| Fecha | Nota |
| --- | --- |
|22 de septiembre de 2016 | Servidores RHUI e instrucciones de instalación disponibles para su uso. Máquinas virtuales implementadas con el nuevo (de 2016 de septiembre fechada) imágenes del catálogo de soluciones de RHEL PAYG utilizará automáticamente los nuevos servidores RHUI, pero las máquinas virtuales existentes son "participar en la"
|1 de noviembre de 2016 | Imágenes RHEL PAYG VM heredadas (que utilizan los servidores de Azure RHUI antiguos) ya figurará en la Galería de Azure Marketplace
|16 de enero de 2017 | Los servidores de Azure RHUI antiguos se desactivará. Actualizar todas sus máquinas virtuales RHEL de PAYG afectados por esta vez para mantener el acceso a RHUI de Azure

### <a name="the-ips-for-the-new-rhui-content-delivery-servers-are"></a>Son las direcciones IP para los servidores de entrega de contenido RHUI nuevas

```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213

# Azure US Government
13.72.186.193
```

### <a name="manual-update-procedure-to-use-the-new-azure-rhui-servers"></a>Procedimiento de actualización manual para usar los nuevos servidores RHUI de Azure

Descargar (a través de doblez) la firma de clave pública

```
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
```

Compruebe la clave descargada

```
gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
```

Comprobar el resultado, comprobar `keyid` y `user ID packet`:

```
Version: GnuPG v1.4.7 (GNU/Linux)
:public key packet:
        version 4, algo 1, created 1446074508, expires 0
        pkey[0]: [2048 bits]
        pkey[1]: [17 bits]
        keyid: EB3E94ADBE1229CF
:user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
:signature packet: algo 1, keyid EB3E94ADBE1229CF
        version 4, created 1446074508, md5len 0, sigclass 0x13
        digest algo 2, begin of digest 1a 9b
        hashed subpkt 2 len 4 (sig created 2015-10-28)
        hashed subpkt 27 len 1 (key flags: 03)
        hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
        hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
        hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
        hashed subpkt 30 len 1 (features: 01)
        hashed subpkt 23 len 1 (key server preferences: 80)
        subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
        data: [2047 bits]
```

Instalar la clave pública

```
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
```

Descargar, comprobar e instalar el cliente de RPM

Descarga: Para RHEL 6

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.0-2.noarch.rpm 
```

Para RHEL 7

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.0-2.noarch.rpm  
```

Comprobar:

```
rpm -Kv azureclient.rpm
```

Compruebe en salida dicha firma del paquete es correcta

```
azureclient.rpm:
    Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
    Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
    V3 RSA/SHA256 Signature, key ID be1229cf: OK
    MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
```

Instalar el RPM

```
sudo rpm -U azureclient.rpm
```

Al finalizar, compruebe que tiene acceso el formulario de Azure RHUI la máquina virtual

### <a name="all-in-one-script-for-automating-the-above-task"></a>Secuencia de comandos en uno para automatizar la tarea anterior
Utilice la siguiente secuencia de comandos según sea necesario para automatizar la tarea de actualizar máquinas virtuales afectadas a los nuevos servidores RHUI de Azure.

```
# Download key
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 

# Validate key
if ! gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release | grep -q "keyid: EB3E94ADBE1229CF"; then
    echo "Keyfile azure.asc NOT valid. Exiting."
    exit 1
fi

# Install Key
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release

# Download RPM package
if grep -q "release 7" /etc/redhat-release; then
    ver=7
elif  grep -q "release 6" /etc/redhat-release; then
    ver=6
else
    echo "Version not supported, exiting"
    exit 1
fi

url=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel$ver/rhui-azure-rhel$ver-2.0-2.noarch.rpm
curl -o azureclient.rpm "$url"

# Verify package
if ! rpm -Kv azureclient.rpm | grep -q "key ID be1229cf: OK"; then
    echo "RPM failed validation ($url)"
    exit 1
fi

# Install package
sudo rpm -U azureclient.rpm
```

## <a name="rhui-overview"></a>Información general RHUI
[Infraestructura de actualización de sombrero rojo](https://access.redhat.com/products/red-hat-update-infrastructure) ofrece una solución altamente scalable para administrar el contenido de repositorio yum instancias de nube de Red sombrero Enterprise Linux hospedado por proveedores de nube certificados sombrero rojo. Según el proyecto pasta precede, RHUI permite a los proveedores de nube localmente reflejar hospedado rojos sombrero repositorio de contenido, crear repositorios personalizados con su propio contenido y hacer que los repositorios disponibles para un grupo grande de usuarios finales a través de un sistema de equilibrio de carga de entrega de contenido.

## <a name="regions-where-rhui-is-available"></a>Regiones donde RHUI no está disponible
RHUI está disponible en todas las regiones donde imágenes RHEL a petición están disponibles. Actualmente incluye públicas todas las regiones que aparece en la página de [panel de estado de Azure](https://azure.microsoft.com/status/) y las regiones de Azure gobierno de Estados Unidos. Acceso RHUI para máquinas virtuales se aprovisione de imágenes RHEL a petición está incluido en su precio. Disponibilidad de nube de configuración regional o nacionales adicionales se actualizarán medida que ampliamos disponibilidad RHEL a petición en el futuro.

> [AZURE.NOTE] Acceso a RHUI hospedado Azure se limita a las máquinas virtuales dentro de [los intervalos de IP de centro de datos de Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="get-updates-from-another-update-repository"></a>Obtener actualizaciones desde otro repositorio de actualización

Si necesita obtener actualizaciones desde un repositorio de actualización diferente (en lugar de RHUI hospedado de Azure) debe anular el registro de las instancias de RHUI y registrarse con la infraestructura de actualización que desee (como rojos sombrero satélite o rojos sombrero cliente Portal CDN). Necesitará suscripciones rojos sombrero adecuadas para estos servicios y el registro para el [Acceso a la nube de sombrero de color rojo en Azure](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure).

Para eliminar del registro RHUI y volver a registrar a su infraestructura de actualización, siga los pasos siguientes.

1.  Editar /etc/yum.repos.d/rh-cloud.repo y cambiar todas `enabled=1` a `enabled=0`. Por ejemplo:

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.  Editar /etc/yum/pluginconf.d/rhnplugin.conf y cambiar `enabled=0` a `enabled=1`.
3.  A continuación, registrar con la infraestructura deseada, como rojos sombrero cliente Portal. Siga a Guía de solución de rojo sombrero sobre [cómo registrar y suscribirse a un sistema en el Portal de cliente de sombrero rojo](https://access.redhat.com/solutions/253273).

> [AZURE.NOTE] Acceso a la RHUI hospedado Azure se incluye en el precio de imagen RHEL pago por uso (PAYG). Anular el registro de una VM de RHEL PAYG desde el RHUI hospedado Azure no convertir la máquina virtual en tipo de Traer-su-propietario-licencia (BYOL) VM y, por tanto, puede ser provocar cargos dobles si se registra la misma VM con otro origen de actualizaciones. 
> 
> Si siempre debe usar una infraestructura de actualización distinto de RHUI hospedado Azure considere la posibilidad de crear e implementar sus propias imágenes (tipo de BYOL), como se describe en el artículo [crear y basado en cargar rojos sombrero máquina virtual para Azure](virtual-machines-linux-redhat-create-upload-vhd.md) .

## <a name="next-steps"></a>Pasos siguientes
Para crear una máquina virtual Linux de Enterprise sombrero de rojo de la imagen de pago por uso de Azure Marketplace y aproveche hospedado Azure RHUI vaya a [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). Podrá usar `yum update` en su instancia RHEL sin ninguna configuración adicional.