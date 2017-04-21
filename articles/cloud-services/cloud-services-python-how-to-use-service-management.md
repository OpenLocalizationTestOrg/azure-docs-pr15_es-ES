<properties
    pageTitle="Cómo usar la API (Python) - Guía de características de administración de servicio"
    description="Obtenga información sobre cómo realizar tareas comunes de administración del servicio de programación de Python."
    services="cloud-services"
    documentationCenter="python"
    authors="lmazuel"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="lmazuel"/>

# <a name="how-to-use-service-management-from-python"></a>Cómo usar el servicio de administración de Python

> [AZURE.NOTE] API del servicio de administración ha quedado reemplazada con la nueva API de administración de recursos, disponible en una versión preliminar.  Consulte la [documentación de administración de recursos de Azure](http://azure-sdk-for-python.readthedocs.org/) para obtener información detallada sobre el uso de la nueva API de administración de recursos de Python.

Esta guía le muestra cómo realizar tareas comunes de administración del servicio de programación de Python. La clase **ServiceManagementService** en el [SDK de Azure para Python](https://github.com/Azure/azure-sdk-for-python) admite puede obtener acceso a muchas de las funciones relacionadas con la administración de servicio que está disponible en el [portal de clásica Azure] [ management-portal] (como **crear, actualizar y eliminar servicios en la nube, implementaciones, servicios de administración de datos y equipos virtuales**). Esta funcionalidad puede ser útil para crear aplicaciones que necesitan puede obtener acceso a la administración de servicio.

## <a name="WhatIs"> </a>¿Qué es la administración de servicios
La API de administración de servicio proporciona acceso mediante programación a gran parte de la funcionalidad de administración de servicio disponible a través del [portal clásica Azure][management-portal]. El SDK de Azure para Python le permite administrar sus cuentas de almacenamiento y de servicios en la nube.

Para usar la API de administración de servicio, debe [crear una cuenta de Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"> </a>Conceptos
El SDK de Azure para Python se ajusta a la [API de administración de servicio de Azure][svc-mgmt-rest-api], que es una API de REST. Todas las operaciones de API se realizan sobre SSL y autentiquen mutuamente utilizando certificados X.509 v3. El servicio de administración puede tener acceso desde dentro de un servicio que se ejecuta en Azure o directamente a través de Internet desde cualquier aplicación que puede enviar una solicitud HTTPS y reciba una respuesta HTTPS.

## <a name="Installation"> </a>Instalación

Todas las características descritas en este artículo están disponibles en la `azure-servicemanagement-legacy` paquete, que se puede instalar con puntos. Para obtener más detalles sobre la instalación (por ejemplo, si es nuevo en Python), consulte este artículo: [instalar Python y el SDK de Azure](../python-how-to-install.md)

## <a name="Connect"> </a>Cómo: conectarse a la administración de servicio
Para conectar con el extremo de administración del servicio, necesita el identificador de suscripción de Azure y un certificado de administración válido. Puede obtener el identificador de suscripción a través del [portal clásica Azure][management-portal].

> [AZURE.NOTE] Desde el SDK de Azure para Python v0.8.0, es posible utilizar certificados creados con OpenSSL cuando se ejecuta en Windows.  Requiere Python 2.7.4 o posterior. Se recomienda utilizar OpenSSL en lugar de .pfx, desde el soporte técnico para .pfx certificados es probable que se quitarán en el futuro.

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Certificados de administración de Windows, Mac o Linux (OpenSSL)
Puede usar [OpenSSL](http://www.openssl.org/) para crear un certificado de administración.  Necesitará crear dos certificados, uno para el servidor (un `.cer` archivo) y otro para el cliente (un `.pem` archivo). Para crear la `.pem` de archivos, ejecute:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Para crear la `.cer` de certificados, ejecute:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Para obtener más información acerca de los certificados de Azure, vea [Introducción a los certificados para servicios de nube de Azure](./cloud-services-certs-create.md). Para obtener una descripción completa de los parámetros de OpenSSL, consulte la documentación en [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

Después de haber creado estos archivos, necesita cargar la `.cer` archivo a Azure a través de la acción "Carga" de la ficha "Configuración" del [portal clásica Azure][management-portal], y tiene que tome nota de la ubicación donde guardó el `.pem` archivo.

Después de obtener el identificador de la suscripción, crea un certificado y cargado el `.cer` archivo a Azure, puede conectarse al extremo administración Azure pasando el identificador de suscripción y la ruta de acceso a la `.pem` archivo **ServiceManagementService**:

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

En el ejemplo anterior, `sms` es un objeto **ServiceManagementService** . El **ServiceManagementService** es la clase principal usada para administrar servicios de Azure.

### <a name="management-certificates-on-windows-makecert"></a>Certificados de administración de Windows (MakeCert)

Puede crear un certificado autofirmado administración en su equipo mediante `makecert.exe`.  Abra un **símbolo del sistema de Visual Studio** como **Administrador** y use el comando siguiente, reemplazando *AzureCertificate* con el nombre del certificado que desea usar.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

El comando crea el `.cer` de archivos y lo instala en el almacén de certificados **personales** . Para obtener más detalles, vea [Introducción a los certificados para servicios de nube de Azure](./cloud-services-certs-create.md).

Después de haber creado el certificado, necesita cargar la `.cer` archivo a Azure a través de la acción "Carga" de la ficha "Configuración" del [portal clásica Azure][management-portal].

Después de obtener el identificador de la suscripción, crea un certificado y cargado el `.cer` archivo a Azure, puede conectarse al extremo administración Azure pasando el identificador de suscripción y la ubicación del certificado en el almacén de certificados **personales** a **ServiceManagementService** (nuevamente, reemplace *AzureCertificate* con el nombre del certificado):

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

En el ejemplo anterior, `sms` es un objeto **ServiceManagementService** . El **ServiceManagementService** es la clase principal usada para administrar servicios de Azure.

## <a name="ListAvailableLocations"> </a>Cómo: lista de ubicaciones disponibles

Para mostrar las ubicaciones que están disponibles para los servicios de hospedaje, utilice la **lista\_ubicaciones** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Cuando se crea un servicio de nube o el servicio de almacenamiento debe proporcionar una ubicación válida. La **lista\_ubicaciones** método siempre devuelve una lista actualizada de las ubicaciones disponibles actualmente. En este momento las ubicaciones disponibles son:

- Europa occidental
- Europa del Norte
- Sudeste asiático
- Asia oriental
- Central de EE.
- Norte Central de EE.
- Sur Central de EE.
- Estados Unidos occidental
- Estados Unidos oriental
- Japón oriental
- Japón oeste
- Sur de Brasil
- Australia Oriental
- Australia sureste

## <a name="CreateCloudService"> </a>Cómo: crear un servicio de nube

Al crear una aplicación y ejecutar en Azure, la configuración y el código juntos se denominan un Azure [servicio de nube][] (conocido como un *servicio alojado* en versiones anteriores de Azure). La **crear\_hospedado\_service** método le permite crear un nuevo servicio hospedado al proporcionar un nombre de servicio hospedado (que debe ser único en Azure), una etiqueta (automáticamente codificado en base 64), una descripción y una ubicación.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Lista de todos los servicios hospedados para su suscripción con la **lista\_hospedado\_servicios** método:

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Si desea obtener información acerca de un servicio hospedado concreto, puede hacerlo pasando el nombre de servicio hospedado a la **obtener\_hospedado\_service\_propiedades** método:

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Después de haber creado un servicio de nube, puede desplegar el código en el servicio con la **crear\_implementación** método.

## <a name="DeleteCloudService"> </a>Cómo: eliminar un servicio de nube

Puede eliminar un servicio de nube pasando el nombre de servicio para el **Eliminar\_hospedado\_service** método:

    sms.delete_hosted_service('myhostedservice')

Antes de eliminar un servicio, primero deben eliminarse todas las implementaciones del servicio. (Consulte [Cómo: eliminar una implementación](#DeleteDeployment) para obtener más detalles.)

## <a name="DeleteDeployment"> </a>Cómo: eliminar una implementación

Para eliminar una implementación, use la **Eliminar\_implementación** método. En el ejemplo siguiente se muestra cómo eliminar una implementación denominada `v1`.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Cómo: crear un servicio de almacenamiento

Un [servicio de almacenamiento](../storage/storage-create-storage-account.md) le proporciona acceso a Azure [BLOB](../storage/storage-python-how-to-use-blob-storage.md), [tablas](../storage/storage-python-how-to-use-table-storage.md)y [colas](../storage/storage-python-how-to-use-queue-storage.md). Para crear un servicio de almacenamiento, necesitará un nombre para el servicio (entre 3 y 24 caracteres en minúsculas y único en Azure), una descripción, una etiqueta (hasta 100 caracteres, automáticamente codificados en base 64) y una ubicación. En el ejemplo siguiente se muestra cómo crear un servicio de almacenamiento especificando una ubicación.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mystorageaccount'
    label = 'mystorageaccount'
    location = 'West US'
    desc = 'My storage account description.'

    result = sms.create_storage_account(name, desc, label, location=location)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Nota en el ejemplo anterior que el estado de la **crear\_almacenamiento\_cuenta** operación puede obtenerse pasando el resultado devuelto por **crear\_almacenamiento\_cuenta** a la **obtener\_operación\_estado** método.  

Puede ver una lista sus cuentas de almacenamiento y sus propiedades con el **lista\_almacenamiento\_cuentas** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"> </a>Cómo: eliminar un servicio de almacenamiento

Puede eliminar un servicio de almacenamiento, pasando el nombre de servicio de almacenamiento para la **Eliminar\_almacenamiento\_cuenta** método. Eliminar un servicio de almacenamiento elimina todos los datos almacenados en el servicio (blobs, tablas y colas).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"> </a>Cómo: lista de sistemas operativos disponibles

Para mostrar los sistemas operativos que están disponibles para los servicios de hospedaje, utilice la **lista\_operativo\_sistemas** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Como alternativa, puede usar el **lista\_operativo\_sistema\_familias** método, que agrupa los sistemas operativos de la familia:

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"> </a>Cómo: crear una imagen de sistema operativo

Para agregar una imagen de sistema operativo en el repositorio de imagen, utilice el **Agregar\_os\_imagen** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mycentos'
    label = 'mycentos'
    os = 'Linux' # Linux or Windows
    media_link = 'url_to_storage_blob_for_source_image_vhd'

    result = sms.add_os_image(label, media_link, name, os)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Para mostrar las imágenes de sistema operativo que están disponibles, utilice la **lista\_os\_imágenes** método. Incluye todas las imágenes de la plataforma y usuario:

    result = sms.list_os_images()

    for image in result:
        print('Name: ' + image.name)
        print('Label: ' + image.label)
        print('OS: ' + image.os)
        print('Category: ' + image.category)
        print('Description: ' + image.description)
        print('Location: ' + image.location)
        print('Media link: ' + image.media_link)
        print('')

## <a name="DeleteVMImage"> </a>Cómo: eliminar una imagen de sistema operativo

Para eliminar una imagen de usuario, use la **Eliminar\_os\_imagen** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Cómo: crear una máquina virtual

Para crear una máquina virtual, debe crear un [servicio de nube](#CreateCloudService).  A continuación, cree la implementación de máquina virtual mediante la **crear\_virtuales\_máquina\_implementación** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    # Name of an os image as returned by list_os_images
    image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

    # Destination storage account container/blob where the VM disk
    # will be created
    media_link = 'url_to_target_storage_blob_for_vm_hd'

    # Linux VM configuration, you can use WindowsConfigurationSet
    # for a Windows VM instead
    linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

    os_hd = OSVirtualHardDisk(image_name, media_link)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=os_hd,
        role_size='Small')

## <a name="DeleteVM"> </a>Cómo: eliminar una máquina virtual

Para eliminar una máquina virtual, primero eliminar la implementación mediante la **Eliminar\_implementación** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

El servicio de nube puede eliminarse mediante la **Eliminar\_hospedado\_service** método:

    sms.delete_hosted_service(service_name='myvm')

##<a name="how-to-create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Cómo: Crear una máquina Virtual de una imagen capturada Máquina Virtual

Para capturar una imagen de máquina virtual, llame primero a la **capturar\_vm\_imagen** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    # replace the below three parameters with actual values
    hosted_service_name = 'hs1'
    deployment_name = 'dep1'
    vm_name = 'vm1'

    image_name = vm_name + 'image'
    image = CaptureRoleAsVMImage    ('Specialized',
        image_name,
        image_name + 'label',
        image_name + 'description',
        'english',
        'mygroup')

    result = sms.capture_vm_image(
            hosted_service_name,
            deployment_name,
            vm_name,
            image
        )

A continuación, para asegurarse de que ha capturado correctamente la imagen, utilice la **lista\_vm\_imágenes** api y asegúrese de que la imagen se muestra en los resultados:

    images = sms.list_vm_images()

Para crear finalmente la máquina virtual mediante la imagen capturada, use la **crear\_virtuales\_máquina\_implementación** método anterior, pero esta vez pasar en la vm_image_name

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=None,
        role_size='Small',
        vm_image_name = image_name)

Para obtener más información sobre cómo capturar una máquina Virtual Linux, consulte [cómo capturar una máquina Virtual Linux.](../virtual-machines/virtual-machines-linux-classic-capture-image.md)

Para obtener más información sobre cómo capturar una máquina Virtual de Windows, vea [cómo capturar una máquina Virtual Windows.](../virtual-machines/virtual-machines-windows-classic-capture-image.md)

## <a name="What's Next"> </a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de administración del servicio, puede obtener acceso a la [documentación de referencia de la API completa para el SDK de Python Azure](http://azure-sdk-for-python.readthedocs.org/) y realizar tareas complejas fácilmente para administrar su aplicación de python.

Para obtener más información, consulte el [Centro para desarrolladores de Python](/develop/python/).

[What is Service Management]: #WhatIs
[Concepts]: #Concepts
[How to: Connect to service management]: #Connect
[How to: List available locations]: #ListAvailableLocations
[How to: Create a cloud service]: #CreateCloudService
[How to: Delete a cloud service]: #DeleteCloudService
[How to: Create a deployment]: #CreateDeployment
[How to: Update a deployment]: #UpdateDeployment
[How to: Move deployments between staging and production]: #MoveDeployments
[How to: Delete a deployment]: #DeleteDeployment
[How to: Create a storage service]: #CreateStorageService
[How to: Delete a storage service]: #DeleteStorageService
[How to: List available operating systems]: #ListOperatingSystems
[How to: Create an operating system image]: #CreateVMImage
[How to: Delete an operating system image]: #DeleteVMImage
[How to: Create a virtual machine]: #CreateVM
[How to: Delete a virtual machine]: #DeleteVM
[Next Steps]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[servicio de nube]:/services/cloud-services/

