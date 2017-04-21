<properties
    pageTitle="Guía de seguridad de almacenamiento de Azure | Microsoft Azure"
    description="Detalles de los diversos métodos de seguridad del almacenamiento de Azure, incluyendo, pero no se limita a RBAC, cifrado de servicio de almacenamiento, el cifrado de cliente, SMB 3.0 y cifrado de disco de Azure."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="robinsh"/>

#<a name="azure-storage-security-guide"></a>Guía de seguridad de almacenamiento de Azure

##<a name="overview"></a>Información general

Almacenamiento de Azure proporciona un conjunto completo de características de seguridad que juntas permiten a los desarrolladores generar aplicaciones seguras. La cuenta de almacenamiento, sí se puede proteger mediante Control de acceso basado en roles y Azure Active Directory. Pueden proteger los datos al transmitirlos entre una aplicación y Azure mediante [El cifrado de cliente](storage-client-side-encryption.md), HTTPS o SMB 3.0. Pueden establecer el datos para cifrar automáticamente al escribir en el almacenamiento de Azure con [Cifrado de servicio de almacenamiento (SSE)](storage-service-encryption.md). Discos de sistema operativo y datos usados por máquinas virtuales se pueden establecer para ser cifrada con [Cifrado de disco de Azure](../security/azure-security-disk-encryption.md). Se pueden conceder acceso delegado a los objetos de datos en el almacenamiento de Azure usar [Firmas de acceso compartido](storage-dotnet-shared-access-signature-part-1.md).

En este artículo se proporciona una descripción general de cada una de estas características de seguridad que se pueden usar con el almacenamiento de Azure. Aparecen vínculos a artículos que ofrecen detalles de cada característica lo que puede hacer con facilidad nuevas investigaciones en cada tema.

Estos son los temas que tratar en este artículo:

-   [Seguridad de plano de administración](#management-plane-security) : seguridad de su cuenta de almacenamiento

    Plano de administración consta de los recursos que se utilizan para administrar su cuenta de almacenamiento. En esta sección, hablaremos acerca del modelo de implementación de administrador de recursos de Azure y cómo usar el Control de acceso basado en roles (RBAC) para controlar el acceso a sus cuentas de almacenamiento. También hablaremos acerca de cómo administrar las claves de la cuenta de almacenamiento y cómo volver a generar ellos.

-   [Plano de datos de seguridad](#data-plane-security) : proteger el acceso a los datos

    En esta sección, veremos permitir el acceso a los objetos de datos reales en su cuenta de almacenamiento, como BLOB, archivos, colas y tablas, uso compartido firmas de Access y almacena las directivas de acceso. Trataremos SA de nivel de servicio y asociaciones de seguridad de nivel de la cuenta. También veremos cómo limitar el acceso a una dirección IP específica (o intervalo de direcciones IP), cómo limitar el protocolo utilizado para HTTPS y cómo revocar una firma de acceso compartido sin tener que esperar para que expire.

-   [Cifrado en tránsito](#encryption-in-transit)

    En esta sección se explica cómo proteger los datos al transferir dentro o fuera de almacenamiento de Azure. Hablaremos recomienda el uso de HTTPS y el cifrado usado por SMB 3.0 Azure recursos compartidos de archivos. También se echamos un vistazo a cifrado de cliente, lo que le permite cifrar los datos antes de que se transfieran en almacenamiento en una aplicación de cliente y descifrar los datos después de que se transfieran sin espacio de almacenamiento.

-   [Cifrado al resto](#encryption-at-rest)

    Hablaremos sobre cifrado de servicio de almacenamiento (SSE) y cómo puede habilitar para una cuenta de almacenamiento de su blobs de bloque, blobs de página y se cifran automáticamente cuando escribe en Azure almacenamiento de blobs de datos anexados. También veremos cómo puede usar el cifrado de disco de Azure y explorar las diferencias básicas y las cajas de cifrado de disco frente a SSE frente a cifrado de cliente. Veremos brevemente cumplimiento de FIPS para equipos de gobierno de Estados Unidos.

-   Usar [El análisis de almacenamiento](#storage-analytics) para auditar el acceso de almacenamiento de Azure

    En esta sección se explica cómo buscar información en los registros de análisis de almacenamiento de una solicitud. Se le Eche un vistazo a análisis real de almacenamiento de datos del registro y vea cómo distinguir si se realiza una solicitud con la clave de cuenta de almacenamiento, con una firma de acceso compartido o anónima y si realizó correctamente o no.

-   [Habilitar a clientes basadas en navegador, con CORS](#Cross-Origin-Resource-Sharing-CORS)

    Esta sección trata sobre cómo permitir entre origen de recursos compartidos (CORS). Hablaremos acceso entre dominios y cómo manejar con las capacidades CORS integradas en el almacenamiento de Azure.

##<a name="management-plane-security"></a>Seguridad de plano de administración

Plano de administración consta de las operaciones que afectan a la cuenta de almacenamiento propiamente dicho. Por ejemplo, puede crear o eliminar una cuenta de almacenamiento, obtener una lista de cuentas de almacenamiento en una suscripción, recuperar las claves de la cuenta de almacenamiento o volver a crear las claves de la cuenta de almacenamiento.

Cuando se crea una nueva cuenta de almacenamiento, seleccione un modelo de implementación de clásico o el Administrador de recursos. El modelo clásico de creación de recursos en Azure sólo permite el acceso de todo o nada a la suscripción y a su vez, la cuenta de almacenamiento.

Esta guía se centra en el modelo de administrador de recursos que es la forma recomendada para crear cuentas de almacenamiento. Con las cuentas de almacenamiento de administrador de recursos, en lugar de proporcionar acceso a toda la suscripción, puede controlar el acceso en un nivel más finito para el plano de administración con Control de acceso basado en roles (RBAC).

###<a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Cómo proteger su cuenta de almacenamiento con Control de acceso basado en roles (RBAC)

Hablemos de novedades de RBAC, y cómo puede usar. Cada suscripción Azure tiene Azure Active Directory. Usuarios, grupos y aplicaciones de directorio pueden tener concedidas acceso para administrar los recursos en la suscripción de Azure que usan el modelo de implementación de administrador de recursos. Esto se conoce como Control de acceso basado en roles (RBAC). Para administrar este acceso, puede usar el [portal de Azure](https://portal.azure.com/), las [Herramientas de Azure CLI](../xplat-cli-install.md), [PowerShell](../powershell-install-configure.md)o las [API de REST de Azure almacenamiento recursos proveedor](https://msdn.microsoft.com/library/azure/mt163683.aspx).

Con el modelo de administrador de recursos, ponga la cuenta de almacenamiento en un recurso grupo y controlar el acceso al plano de la administración de esa cuenta de almacenamiento específico con Azure Active Directory. Por ejemplo, puede dar a usuarios específicos la capacidad de acceso a las claves de cuenta de almacenamiento, mientras que otros usuarios pueden ver información sobre la cuenta de almacenamiento, pero no pueden acceder a las teclas de la cuenta de almacenamiento.

####<a name="granting-access"></a>Conceder acceso

Acceso al asignar el rol RBAC correspondiente a los usuarios, grupos y aplicaciones en el ámbito de la derecha. Para conceder acceso a toda la suscripción, asignar un rol en el nivel de suscripción. Puede conceder acceso a todos los recursos en un grupo de recursos al conceder permisos al grupo de recursos propiamente dicho. También puede asignar roles específicos a recursos específicos, como cuentas de almacenamiento.

Estos son los puntos principales que debe saber sobre el uso de RBAC para tener acceso a las operaciones de administración de una cuenta de almacenamiento de Azure:

-   Cuando se asigna acceso, básicamente asignar un rol a la cuenta que desea tener acceso. Puede controlar el acceso a las operaciones que se utilizan para administrar esa cuenta de almacenamiento, pero no a los objetos de datos de la cuenta. Por ejemplo, puede conceder permiso para recuperar las propiedades de la cuenta de almacenamiento (como redundancia), pero no a un contenedor o datos dentro de un contenedor de almacenamiento de blobs.

-   Para alguien que tiene permiso para acceder a los objetos de datos de la cuenta de almacenamiento, puede dar permiso de lectura de las claves de la cuenta de almacenamiento y que el usuario, a continuación, puede usar las claves para tener acceso a BLOB, colas, tablas y archivos.

-   Roles pueden asignarse a una cuenta de usuario, un grupo de usuarios, o a una aplicación específica.

-   Cada rol tiene una lista de acciones y las acciones no. Por ejemplo, el rol de colaborador de máquina Virtual tiene una acción de "listKeys" que permite que las teclas de la cuenta de almacenamiento para que puedan leer. El colaborador tiene "No acciones" como actualizar el acceso para los usuarios de Active Directory.

-   Funciones de almacenamiento incluyen (pero no se limitan a) las siguientes acciones:

    -   Propietario: pueden administrar todo, incluido el acceso.

    -   Colaborador: pueden hacer nada el propietario puede excepto asignar acceso. Un usuario con este rol puede ver y volver a crear las claves de la cuenta de almacenamiento. Con las teclas de cuenta de almacenamiento, que pueden obtener acceso a los objetos de datos.

    -   Lector: pueden ver información sobre la cuenta de almacenamiento, excepto la información confidencial. Por ejemplo, si se asigna una función con permisos de lectura en la cuenta de almacenamiento a alguien, pueden ver las propiedades de la cuenta de almacenamiento, pero no podrán realizar cambios en las propiedades ni ver las teclas de la cuenta de almacenamiento.

    -   Colaborador de la cuenta de almacenamiento, puede administrar la cuenta de almacenamiento: pueden leer los grupos de recursos y recursos, la suscripción y crear y administrar las implementaciones de grupo de recursos de suscripción. También puede acceder a las claves de cuenta de almacenamiento, que a su vez significa que pueden obtener acceso a plano de datos.

    -   Administrador de acceso de usuario: pueden administrar acceso de usuario a la cuenta de almacenamiento. Por ejemplo, puede conceder acceso de lectura a un usuario específico.

    -   Máquina virtual colaborador: pueden administrar máquinas virtuales pero no la cuenta de almacenamiento al que están conectados. Esta función puede mostrar las teclas de cuenta de almacenamiento, lo que significa que el usuario a quien asignar este rol puede actualizar el plano de datos.

        Fin de un usuario crear una máquina virtual, tienen que pueda crear el archivo de disco duro virtual correspondiente en una cuenta de almacenamiento. Para ello, deben poder recuperar la clave de cuenta de almacenamiento y pasar a la API de creación de la máquina virtual. Por lo tanto, debe tener este permiso, por lo que pueden indicar las teclas de la cuenta de almacenamiento.

- La capacidad de definir funciones personalizadas es una característica que le permite crear un conjunto de acciones de una lista de acciones disponibles que se pueden realizar en los recursos de Azure.

- El usuario debe estar configurado en Azure Active Directory antes de poder asignar un rol a ellos.

- Puede crear un informe de que conceder o revocar ¿qué tipo de acceso a o desde quién y en qué ámbito con PowerShell o CLI Azure.

####<a name="resources"></a>Recursos

-   [Control de acceso basado en roles de Azure Active Directory](../active-directory/role-based-access-control-configure.md)

    En este artículo se explica el Control de acceso basado en roles de Azure Active Directory y cómo funciona.

-   [RBAC: Integrado en Roles](../active-directory/role-based-access-built-in-roles.md)

    Este artículo describe todas las funciones integradas disponibles en RBAC.

-   [Descripción de administrador de recursos e implementación clásica](../resource-manager-deployment-model.md)

    Este artículo explica la implementación del Administrador de recursos y los modelos de implementación clásica y las ventajas de usar los grupos de administrador de recursos y recursos

-   [Proceso de Azure, red y proveedores de almacenamiento en el Administrador de recursos Azure](../virtual-machines/virtual-machines-windows-compare-deployment-models.md)

    En este artículo se explica cómo funcionan los Azure calcular, redes y proveedores de almacenamiento en el modelo de administrador de recursos.

-   [Administración de Control de acceso basado en roles con la API de REST](../active-directory/role-based-access-control-manage-access-rest.md)

    Este artículo le muestra cómo usar la API de REST para administrar RBAC.

-   [Referencia de la API de REST de proveedor de almacenamiento de Azure recursos](https://msdn.microsoft.com/library/azure/mt163683.aspx)

    Esta es la referencia de la API que puede usar para administrar su cuenta de almacenamiento mediante programación.

-   [Guía del desarrollador auth con la API del Administrador de recursos de Azure](http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/)

    Este artículo le muestra cómo autenticar mediante las API de administrador de recursos.

-   [Control de acceso basado en roles de Microsoft Azure de Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Esto es un vínculo a un vídeo en canal 9 de la conferencia de Ignite MS 2015. En esta sesión, hablan sobre acceso a capacidades de administración y generación de informes en Azure y explorar las prácticas recomendadas de proteger el acceso a Azure suscripciones con Azure Active Directory.

###<a name="managing-your-storage-account-keys"></a>Administrar las claves de la cuenta de almacenamiento

Teclas de la cuenta de almacenamiento son cadenas de 512 bits creadas por Azure que, junto con el nombre de la cuenta de almacenamiento, se puede usar para tener acceso a los objetos de datos almacenados en la cuenta de almacenamiento, por ejemplo, blobs, entidades dentro de una tabla, la cola de mensajes y archivos en un recurso compartido de archivos de Azure. Controlar el acceso al almacenamiento cuenta teclas controles al plano de datos para la cuenta de almacenamiento.

Cada cuenta de almacenamiento tiene dos claves se denominan "Clave 1" y "2" en el [portal de Azure](http://portal.azure.com/) y clave en los cmdlets de PowerShell. Estos se pueden regenerar manualmente uno de varios métodos, incluidos, pero no se limita a usar el [portal de Azure](https://portal.azure.com/), PowerShell, CLI Azure o mediante programación utilizando la biblioteca de cliente de almacenamiento de .NET o la API de REST de servicios de almacenamiento de Azure.

Existen varios motivos para volver a generar las claves de la cuenta de almacenamiento.

-   Es posible que regenerar de forma regular por motivos de seguridad.

-   ¿Volver a generar las claves de la cuenta de almacenamiento si alguien administrado ataque una aplicación y recuperar la clave que fue codificado o guardado en un archivo de configuración, otorgar acceso completo a su cuenta de almacenamiento.

-   Otro caso de regeneración de clave es si su equipo usa una aplicación de explorador de almacenamiento que conserva la clave de cuenta de almacenamiento y sale de uno de los miembros del equipo. La aplicación debería seguir trabajando, dar acceso a su cuenta de almacenamiento después de que encuentra ausente. Esto es realmente la razón principal que crean firmas de acceso compartido de nivel de cuenta: puede usar un nivel de la cuenta de SA en lugar de almacenamiento de las teclas de acceso en un archivo de configuración.

####<a name="key-regeneration-plan"></a>Plan de regeneración de clave

No desea regenerar la clave que utiliza sin la planeación. Si lo hace, podría cortados todo el acceso a esta cuenta de almacenamiento, que puede provocar interrupciones principales. Se trata de por qué hay dos claves. Debe volver a generar una clave a la vez.

Antes de generar las claves, asegúrese de que tiene una lista de todas las aplicaciones que dependen de la cuenta de almacenamiento, así como cualquier otro servicio que esté usando en Azure. Por ejemplo, si está utilizando Azure Media Services que dependen de su cuenta de almacenamiento, debe volver a sincronizar las teclas de acceso con el servicio de medios después de regenerar la clave. Si está utilizando las aplicaciones como el Explorador de almacenamiento, debe proporcionar las teclas de nuevas para esas aplicaciones también. Tenga en cuenta que si tiene máquinas virtuales cuyos archivos de disco duro virtual se almacenan en la cuenta de almacenamiento, no se verán afectadas por regenerar las claves de la cuenta de almacenamiento.

Puede volver a generar las claves en el portal de Azure. Una vez que se vuelven a generar claves pueden tardar hasta 10 minutos para que se sincronice entre los servicios de almacenamiento.

Cuando esté listo, le presentamos el proceso general que detalla cómo debe cambiar la clave. En este caso, la suposición es que utiliza actualmente clave 1 y va a cambiar todo para usar la clave 2 en su lugar.

1.  Regenerar la clave 2 para asegurarse de que es seguro. Puede hacerlo en el portal de Azure.

2.  En todas las aplicaciones que se almacena la clave de almacenamiento, cambie la clave de almacenamiento para usar el nuevo valor de la clave 2. Probar y publicar la aplicación.

3.  Después de todo de las aplicaciones y servicios están por encima y ejecutando correctamente, regenerar la clave 1. Así se garantiza que alguien a quien no expresamente tienen la clave nueva ya no tendrán acceso a la cuenta de almacenamiento.

Si actualmente usa clave 2, puede usar el mismo proceso, pero invertir los nombres de clave.

Puede migrar con un par de días, cambiar cada aplicación para usar la nueva clave y su publicación. Una vez haya terminado todos ellos, debe volver atrás y regenerar la clave antigua de modo que ya no funciona.

Otra opción es poner la clave de cuenta de almacenamiento en un [Depósito de clave de Azure](https://azure.microsoft.com/services/key-vault/) como secreto y tener las aplicaciones recuperar la clave desde allí. A continuación, al regenerar la clave y actualizar el depósito de clave de Azure, las aplicaciones no tendrá que se pueden redistribuir porque seleccionará la nueva clave de la caja fuerte clave de Azure automáticamente. Tenga en cuenta que puede hacer que la aplicación lee la clave cada vez que lo necesite, o puede almacenarla en memoria caché y si se produce un error cuando se utiliza, recuperar la clave de nuevo de la caja fuerte clave de Azure.

Usar depósito de clave de Azure, también agrega otro nivel de seguridad de las claves de almacenamiento. Si usa este método, nunca tendrá codificado de clave de almacenamiento en un archivo de configuración que elimina este tipo de alguien obtener acceso a las claves sin permisos específicos.

Otra ventaja de usar Azure clave depósito es que también puede controlar el acceso a las claves de Azure Active Directory. Esto significa que puede conceder acceso a los pocos aplicaciones que necesita para recuperar las claves de la cámara de clave de Azure y sabe que otras aplicaciones no pueda tener acceso a las teclas sin concederles permisos específicamente.

Nota: se recomienda usar solo una de las teclas en todas las aplicaciones a la vez. Si usa clave 1 en algunos lugares y 2 de clave en otros, no podrá girar las claves sin alguna aplicación perder el acceso.

####<a name="resources"></a>Recursos

-   [Acerca de las cuentas de almacenamiento de Azure](storage-create-storage-account.md#regenerate-storage-access-keys)

    Este artículo proporciona una descripción general de las cuentas de almacenamiento y explica cómo ver, copiar y volver a generar las teclas de acceso de almacenamiento.

-   [Referencia de la API de REST de proveedor de almacenamiento de Azure recursos](https://msdn.microsoft.com/library/mt163683.aspx)

    Este artículo contiene vínculos a artículos específicos sobre recuperar las claves de la cuenta de almacenamiento y regenerar las claves de la cuenta de almacenamiento para una cuenta de Azure utilizando la API de REST. Nota: Este es para cuentas de almacenamiento de administrador de recursos.

-   [Operaciones en cuentas de almacenamiento](https://msdn.microsoft.com/library/ee460790.aspx)

    En este artículo en el administrador del servicio de almacenamiento REST API Reference contiene vínculos a artículos específicos en recuperar y regenerar las claves de la cuenta de almacenamiento con la API de REST. Nota: Este es para las cuentas de almacenamiento clásico.

-   [Diga despedida para la administración de claves: administrar el acceso a datos de almacenamiento de Azure con Azure AD](http://www.dushyantgill.com/blog/2015/04/26/say-goodbye-to-key-management-manage-access-to-azure-storage-data-using-azure-ad/)

    Este artículo le muestra cómo usar Active Directory para controlar el acceso a las claves de Azure almacenamiento en depósito de clave de Azure. También se muestra cómo usar un trabajo de automatización de Azure para volver a crear las claves cada hora.

##<a name="data-plane-security"></a>Seguridad de plano de datos

Seguridad de plano de datos se refiere a los métodos utilizados para proteger los objetos de datos almacenados en el almacenamiento de Azure: BLOB, colas, tablas y archivos. Hemos visto métodos para cifrar los datos y la seguridad durante el tránsito de los datos, pero ¿qué tiene que hacer acerca de cómo permitir el acceso a los objetos?

Básicamente, hay dos métodos para controlar el acceso a los propios objetos de datos. El primero es controlando el acceso a las claves de la cuenta de almacenamiento y el segundo es usar las firmas de acceso compartido para conceder acceso a objetos de datos específicos para un período de tiempo determinado.

Una excepción nota es que puede permitir el acceso del público a su BLOB estableciendo el nivel de acceso para el contenedor que almacena los BLOB en consecuencia. Si configura el acceso para un contenedor de Blob o contenedor, permitirá público acceso de lectura para la blobs de ese contenedor. Esto significa que cualquier usuario con una dirección URL que hace referencia a un objeto binario en ese contenedor puede abrir en un explorador sin con una firma de acceso compartido o con las teclas de la cuenta de almacenamiento.

###<a name="storage-account-keys"></a>Teclas de la cuenta de almacenamiento

Teclas de la cuenta de almacenamiento son cadenas de 512 bits creadas por Azure que puede usarse junto con el nombre de la cuenta de almacenamiento, para tener acceso a los objetos de datos almacenados en la cuenta de almacenamiento.

Por ejemplo, puede leer BLOB, escribir en colas, crear tablas y modificar archivos. Muchas de estas acciones se pueden realizar a través del portal de Azure, o mediante una de muchas aplicaciones del explorador de almacenamiento. También puede escribir el código para usar la API de REST o una de las bibliotecas de cliente de almacenamiento para realizar estas operaciones.

Como se describe en la sección en la [Administración de plano de seguridad](#management-plane-security), se puede conceder acceso a las claves de almacenamiento para una cuenta de almacenamiento estándar que le da acceso completo a la suscripción de Azure. Acceso a las claves de almacenamiento para una cuenta de almacenamiento que utiliza el modelo de administrador de recursos de Azure se puede controlar mediante el Control de acceso basado en roles (RBAC).

###<a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Cómo delegar el acceso a objetos de su cuenta mediante firmas de acceso compartido y almacena las directivas de acceso

Una firma de acceso compartido es una cadena que contiene un token de seguridad que se puede vincular a un URI que le permite delegar el acceso a objetos de almacenamiento y especificar restricciones, como los permisos y el intervalo de fecha y hora de acceso.

Puede conceder acceso a BLOB, contenedores, la cola de mensajes, archivos y tablas. Con las tablas, realmente puede conceder permiso de acceso a un rango de entidades en la tabla especificando los intervalos de clave partición y fila a la que desea que el usuario tenga acceso. Por ejemplo, si tiene datos almacenados con una clave de partición de estado geográfica, puede proporcionar a alguien acceso a solo los datos para California.

En otro ejemplo, puede dar un token SA que le permite escribir entradas a una cola de una aplicación web y dar un empleado de su aplicación de rol un token SA recibe los mensajes de la cola y procesar ellos. O bien, puede dar a un cliente un token SA que pueden usar para cargar imágenes en un contenedor de almacenamiento de blobs y conceder permisos de aplicación web para leer las imágenes. En ambos casos, hay una separación de preocupaciones: cada aplicación puede especificarse solo el acceso que necesitan para realizar sus tareas. Esto es posible mediante el uso compartido firmas de Access.

####<a name="why-you-want-to-use-shared-access-signatures"></a>¿Por qué desea usar firmas de acceso compartido

¿Por qué se quiere usar una SA en lugar de simplemente al proporcionar la clave de cuenta de almacenamiento, que es mucho más fácil? Al proporcionar la clave de cuenta de almacenamiento es similar a las teclas de su Unido de almacenamiento de uso compartido. Concede acceso completo. Alguien podría usar las claves y cargar su biblioteca de música toda su cuenta de almacenamiento. También puede reemplazar los archivos con versiones infectado o apropiarse indebidamente de los datos. Dar acceso ilimitado a su cuenta de almacenamiento es algo que no debe tomarse ligeramente.

Con las firmas de acceso compartido, puede dar a un cliente sólo los permisos necesarios para un período de tiempo limitado. Por ejemplo, si alguien está cargando un blob a su cuenta, puede concederles acceso de escritura suficiente tiempo cargar el blob (según el tamaño del objeto, por supuesto). ¿Y si cambia de opinión, puede revocar el acceso.

Además, puede especificar que las solicitudes realizadas mediante un SA están limitadas a una determinada dirección IP o intervalo de direcciones IP externo de Azure. También puede requerir que se realizan las peticiones con un protocolo específico (HTTPS o HTTP/HTTPS). Esto significa que si desea que admita el tráfico HTTPS, puede establecer el protocolo requerido HTTPS solo y tráfico HTTP se bloqueará.

####<a name="definition-of-a-shared-access-signature"></a>Definición de una firma de acceso compartido

Una firma de acceso compartido es un conjunto de parámetros de consulta que se anexa a la dirección URL que apunta a la el recurso

que proporciona información sobre el acceso permitido y la longitud de tiempo que se permite el acceso. Aquí tenemos un ejemplo; Este URI proporciona acceso de lectura a un blob de cinco minutos. Nota que SA los parámetros de la consulta debe ser la dirección URL codificada, como % 3A de dos puntos (:) o % 20 para un espacio.

    http://mystorage.blob.core.windows.net/mycontainer/myblob.txt (URL to the blob)
    ?sv=2015-04-05 (storage service version)
    &st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
    &se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
    &sr=b (resource is a blob)
    &sp=r (read access)
    &sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
    &spr=https (only allow HTTPS requests)
    &sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)

####<a name="how-the-shared-access-signature-is-authenticated-by-the-azure-storage-service"></a>¿Cómo se autentica la firma de acceso compartido por el servicio de almacenamiento de Azure

Cuando el servicio de almacenamiento recibe la solicitud, los parámetros de consulta de entrada y crea una firma con el mismo método como el programa de llamada. A continuación, compara las firmas de dos. Si están de acuerdo, el servicio de almacenamiento puede comprobar la versión de servicio de almacenamiento para asegurarse de que es válida, compruebe que la fecha y hora actuales son dentro de la ventana especificada, asegúrese de que el acceso solicitado corresponde a la solicitud realizada, etcetera.

Por ejemplo, con nuestra dirección URL anterior, si la dirección URL se apuntando a un archivo en lugar de un blob esta solicitud sería un error porque especifica que la firma de acceso compartido para un blob. Si el resto de comandos que se llama actualizar un blob, se producirá un error porque la firma de acceso compartido especifica que se permite el acceso de sólo lectura.

####<a name="types-of-shared-access-signatures"></a>Tipos de firmas de acceso compartido

-   Asociaciones de seguridad de un nivel de servicio pueden utilizarse para tener acceso a recursos específicos en una cuenta de almacenamiento. Algunos ejemplos de este están recuperando una lista de BLOB en un contenedor, descargar un blob, actualizar una entidad en una tabla, agregar mensajes a una cola o cargar un archivo a un archivo compartido.

-   Asociaciones de seguridad de un nivel de cuenta pueden utilizarse para tener acceso a cualquier cosa que se puede usar una SA de nivel de servicio para. Además, puede dar a las opciones para los recursos que no se permiten con asociaciones de seguridad de nivel de servicio, como la capacidad para crear contenedores, tablas, colas y recursos compartidos de archivos. También puede especificar el acceso a servicios de varios a la vez. Por ejemplo, puede proporcionar a alguien acceso a BLOB y archivos en su cuenta de almacenamiento.

####<a name="creating-an-sas-uri"></a>Crear un URI de SA

1.  Puede crear un URI ad hoc a petición, todos los parámetros de consulta de definición de cada vez.

    Esto es realmente flexible, pero si tiene un conjunto lógico de parámetros similares cada vez, usando una directiva de acceso almacenado es mejor.

2.  Puede crear una directiva de acceso almacenado para un contenedor completo, el recurso compartido de archivos, la tabla o la cola. A continuación, puede utilizar esto como base para los URI de SA crear. Pueden revocar fácilmente los permisos según almacenado directivas de acceso. Puede tener un máximo de 5 directivas definidas en cada contenedor, la cola, la tabla o el recurso compartido de archivos.

    Por ejemplo, si fuese tiene muchas personas leer el BLOB en un contenedor específico, puede crear una directiva de acceso almacenado que dice "conceder acceso de lectura" y cualquier otra configuración que será el mismo cada vez. A continuación, puede crear un URI SAS usando la configuración de la directiva de acceso almacenado y que especifica la fecha y hora de expiración. La ventaja de esto es que no tiene que especificar todos los parámetros de consulta cada vez.

####<a name="revocation"></a>Revocación

Supongamos que se ha comprometido su sa o desea cambiar debido a los requisitos de cumplimiento de reglamentaciones o seguridad de la empresa. ¿Cómo revocar el acceso a un recurso mediante esa SA? Depende de cómo haya creado el URI SAS.

Si está utilizando de ad hoc URI, tiene tres opciones. Puede emitir tokens SA con directivas de expiración corto y simplemente espere las asociaciones de seguridad para que expire. Puede cambiar el nombre o elimine el recurso (suponiendo que el token ha establecido el ámbito a un único objeto). Puede cambiar las teclas de la cuenta de almacenamiento. Esta última opción puede tener un gran impacto, dependiendo de cuántos services están utilizando esa cuenta de almacenamiento y probablemente no es algo que desee hacer sin la planeación.

Si está utilizando una SA derivado de una directiva de acceso almacenados, puede quitar el acceso al revocar la directiva de acceso almacenados: solo puede cambiar por lo que ya ha caducado, o puede quitarlo. Esto surte efecto inmediatamente e invalida cada SA creado con la directiva de acceso que almacenan. Actualizar o quitar la directiva de acceso almacenado pueden personas impacto en el acceso a ese contenedor específico, el recurso compartido de archivos, tabla o cola a través de SA, pero si se escriben los clientes para que se solicitan una nuevas SA cuando antiguo será válida, esto funciona correctamente.

Dado que usando una SA derivado de una directiva de acceso almacenados permite revocar dicha SA inmediatamente, es recomendable usar siempre almacena las directivas de acceso cuando sea posible.

####<a name="resources"></a>Recursos

Para obtener información detallada sobre el uso compartido firmas de Access y almacena las directivas de acceso, junto con ejemplos, consulte los siguientes artículos:

-   Estos son los artículos de referencia.

    -   [Servicio SA](https://msdn.microsoft.com/library/dn140256.aspx)

        Este artículo proporciona ejemplos de uso de un nivel de servicio de SA con BLOB, la cola de mensajes, intervalos de tabla y archivos.

    -   [Creación de un servicio SA](https://msdn.microsoft.com/library/dn140255.aspx)

    -   [Creación de una cuenta SA](https://msdn.microsoft.com/library/mt584140.aspx)

-   Estos son los tutoriales para el uso de la biblioteca de cliente .NET crear firmas de acceso compartido y almacena las directivas de acceso.

    -   [Usar firmas de acceso compartido (SA)](storage-dotnet-shared-access-signature-part-1.md)

    -   [Compartido firmas de Access, parte 2: Crear y usar un SA con el servicio de blobs](storage-dotnet-shared-access-signature-part-2.md)

        En este artículo se incluye una explicación del modelo SA, ejemplos de firmas de acceso compartido, y recomendaciones para la recomendación utilizan de SA. También trata de revocación de concedido el permiso.

-   Limitar el acceso por dirección IP (ACL de IP)

    -   [¿Qué es un punto final de la lista de Control de acceso (ACL)?](../virtual-network/virtual-networks-acl.md)

    -   [Creación de un servicio SA](https://msdn.microsoft.com/library/azure/dn140255.aspx)

        Este es el artículo de referencia para asociaciones de seguridad de nivel de servicio; incluye un ejemplo de ACLing de IP.

    -   [Creación de una cuenta SA](https://msdn.microsoft.com/library/azure/mt584140.aspx)

        Este es el artículo de referencia para el nivel de la cuenta SA; incluye un ejemplo de ACLing de IP.

-   Autenticación

    -    [Autenticación de los servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)

-   Compartido Introducción tutoriales de firmas de Access

    -   [Introducción a tutoriales de SA](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

##<a name="encryption-in-transit"></a>Cifrado en tránsito

###<a name="transport-level-encryption--using-https"></a>Cifrado de nivel de transporte – mediante HTTPS

Otro paso que debe realizar para garantizar la seguridad de los datos de almacenamiento de Azure es cifrar los datos entre el cliente y el almacenamiento de Azure. La primera recomendación es utilizar siempre el protocolo [HTTPS](https://en.wikipedia.org/wiki/HTTPS) , que garantiza una comunicación segura a través de Internet pública.

Siempre debe usar HTTPS al llamar a las API de REST u obtener acceso a objetos de almacenamiento. Además, **Firmas de acceso compartido**, que puede usarse para delegar el acceso a objetos de almacenamiento de Azure, incluir una opción para especificar que se puede utilizar sólo el protocolo HTTPS al usar firmas de acceso compartido, asegurarse de que nadie enviar vínculos con tokens SA utiliza el protocolo adecuado.

####<a name="resources"></a>Recursos

-   [Habilitar HTTPS para una aplicación de servicio de aplicaciones de Azure](../app-service-web/web-sites-configure-ssl-certificate.md)

    Este artículo le muestra cómo habilitar HTTPS para una aplicación Web de Azure.

###<a name="using-encryption-during-transit-with-azure-file-shares"></a>Mediante el cifrado durante el tránsito con recursos compartidos de archivos de Azure

Almacenamiento de archivos de Azure admite HTTPS cuando se usa la API de REST, pero más usados como un recurso compartido de archivos SMB está conectado a una máquina virtual. 2.1 SMB no admite el cifrado, por lo que solo se permiten conexiones dentro de la misma región en Azure. Sin embargo, SMB 3.0 admite el cifrado y pueden utilizarse con Windows Server 2012 R2, Windows 8, Windows 8.1 y Windows 10, permitiendo entre región accedan e incluso acceso en el escritorio.

Tenga en cuenta que mientras Azure recursos compartidos de archivos se pueden utilizar con Unix, el cliente SMB Linux aún no admite el cifrado, para que solo se permite el acceso dentro de un área de Azure. Compatibilidad con cifrado para Linux se encuentra en la Guía de desarrolladores de Linux responsables de la funcionalidad SMB. Cuando se agregan cifrado, tendrá la misma capacidad para obtener acceso a un recurso compartido de archivos de Azure en Linux como lo haría para Windows.

####<a name="resources"></a>Recursos

-   [Cómo usar el almacenamiento de archivos de Azure con Linux](storage-how-to-use-files-linux.md)

    Este artículo le muestra cómo cargar o descargar archivos y montaje de un recurso compartido de archivos de Azure en un sistema Linux.

-   [Introducción a almacenamiento de archivos de Azure en Windows](storage-dotnet-how-to-use-files.md)

    Este artículo proporciona una descripción general de los recursos compartidos de archivos de Azure y cómo montaje y usarlos mediante PowerShell y .NET.

-   [Almacenamiento de archivos dentro de Azure](https://azure.microsoft.com/blog/inside-azure-file-storage/)

    En este artículo se anuncia la disponibilidad general de almacenamiento de archivos de Azure y proporciona detalles técnicos sobre el cifrado de SMB 3.0.

###<a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Mediante el cifrado de cliente para proteger los datos que se envían a almacenamiento

Otra opción que le ayuda a garantizar que los datos están protegidos mientras se transfiere entre una aplicación de cliente y el almacenamiento es el cifrado de cliente. Los datos se cifran antes de que se transfieren en el almacenamiento de Azure. Al recuperar los datos de Azure almacenamiento, los datos se descifran cuando se recibe en el cliente. Aunque los datos se cifran que atraviesa el cable, le recomendamos que también usa HTTPS, como comprobaciones de integridad de datos integradas que ayudan a mitigar los errores de red que afectan a la integridad de los datos.

Cifrado de cliente también es un método para cifrar los datos almacenados, como los datos se almacenan en un formato cifrado. Hablaremos esto con más detalle en la sección de [cifrado al resto](#encryption-at-rest).

##<a name="encryption-at-rest"></a>Cifrado al resto

Existen tres características Azure que proporcionan cifrado en el resto. Cifrado de disco Azure se usa para cifrar los discos de sistema operativo y datos de IaaS máquinas virtuales de Windows. Los otros dos: cifrado de cliente y SSE: son ambos usado para cifrar datos en el almacenamiento de Azure. Veamos cada una de ellas y, a continuación, realice una comparación y ver cuándo se puede utilizar cada uno.

Aunque puede usar el cifrado de cliente para cifrar los datos al transmitirlos (que también se almacena en su formulario cifrado en almacenamiento), es posible que prefiera simplemente usar HTTPS durante la transferencia, y tiene alguna forma de los datos que se cifran automáticamente cuando se almacenan. Hay dos formas de hacerlo: cifrado de disco de Azure y SSE. Una se utiliza para cifrar directamente los datos en discos de sistema operativo y datos usados por máquinas virtuales y la otra se usa para cifrar datos escritos en el almacenamiento de blobs de Windows Azure.

###<a name="storage-service-encryption-sse"></a>Cifrado de servicio de almacenamiento (SSE)

SSE permite solicitar que el servicio de almacenamiento cifrar automáticamente los datos al escribir en el almacenamiento de Azure. Al leer los datos de almacenamiento de Azure, se descifra por el servicio de almacenamiento antes de que se devuelven. Esto le permite proteger los datos sin tener que modificar el código o agregar código a todas las aplicaciones.

Esta es una configuración que se aplica a la cuenta de almacenamiento. Puede habilitar y deshabilitar esta característica cambiando el valor de la configuración. Para ello, puede usar el portal de Azure, PowerShell, CLI Azure, la API de REST de proveedor de recursos de almacenamiento o la biblioteca de cliente de almacenamiento de .NET. De forma predeterminada, SSE está desactivada.

En este momento, Microsoft administra las claves utilizadas para el cifrado. Hemos generar las claves originalmente y administrar el almacenamiento seguro de las teclas, así como el giro regular definida por política interna de Microsoft. En el futuro, se obtiene la capacidad de administrar sus propios claves de cifrado y proporcione una ruta de migración de claves administrado por Microsoft a claves administrado por el cliente.

Esta característica está disponible para las cuentas estándar y Premium almacenamiento creadas con el modelo de implementación de administrador de recursos. SSE se aplica solo a bloquear blobs, blobs de página y anexar BLOB. Otros tipos de datos, incluidos tablas, colas y archivos, no estará cifrados.

Sólo se cifran los datos cuando se habilita SSE y los datos se escriben con el almacenamiento de blobs de Windows. Habilitar o deshabilitar SSE no afecta a los datos existentes. En otras palabras, cuando se habilita este cifrado, no volver atrás y cifrar los datos que ya existe; ni se lo descifrar los datos que ya existen cuando se deshabilita SSE.

Si desea usar esta característica con una cuenta de almacenamiento estándar, puede crear una nueva cuenta de almacenamiento de administrador de recursos y usar AzCopy para copiar los datos a la nueva cuenta. 

###<a name="client-side-encryption"></a>Cifrado de cliente

Cifrado de cliente se mencionó al explicar el cifrado de los datos al transmitirlos. Esta característica le permite cifrar mediante programación los datos en una aplicación de cliente antes de enviar a través de la conexión se escriban en el almacenamiento de Azure y descifrar mediante programación los datos después de recuperar del almacenamiento de Azure.

Proporciona cifrado en tránsito, sino también proporciona la característica de cifrado al resto. Tenga en cuenta que aunque los datos se cifran en tránsito, es recomendable usar HTTPS para aprovechar las ventajas de las comprobaciones de integridad de datos integrados que ayudan a mitigar los errores de red que afectan a la integridad de los datos.

Un ejemplo de dónde puede usar esta opción es si tiene una aplicación web que almacena BLOB y recupera BLOB y desea que la aplicación y los datos sean lo más seguro posible. En ese caso, debería usar cifrado de cliente. El tráfico entre el cliente y el servicio de blobs de Windows Azure contiene el recurso cifrado y nadie puede interpretar los datos al transmitirlos y reconstituir a su BLOB privado.

Cifrado de cliente integrado en Java y las bibliotecas de cliente de almacenamiento de .NET, que a su vez utilizan las API de depósito de clave de Azure, lo que posibilita bastante para implementar. El proceso de cifrado y descifrado los datos usa la técnica de sobres y almacena los metadatos que usa el cifrado en cada objeto de almacenamiento. Por ejemplo, para blobs, lo almacena en los metadatos de blobs, mientras que para colas, lo agrega a cada mensaje de cola.

Para el cifrado, puede generar y administrar sus propios claves de cifrado. También puede usar las claves generadas por la biblioteca de cliente de almacenamiento de Azure, o puede hacer que la cámara de clave de Azure generar las claves. Puede almacenar las claves de cifrado en el almacenamiento de claves local, o puede almacenar en un depósito de clave de Azure. Depósito de clave Azure le permite conceder acceso a la información confidencial en depósito de clave de Azure a usuarios específicos con Azure Active Directory. Esto significa que no solo cualquiera puede leer el depósito de clave de Azure y recuperar las teclas que se usa para el cifrado de cliente.

####<a name="resources"></a>Recursos

-   [Cifrar y descifrar BLOB en Microsoft Azure almacenamiento mediante depósito de clave de Azure](storage-encrypt-decrypt-blobs-key-vault.md)

    Este artículo le muestra cómo usar el cifrado de cliente con Azure clave depósito, incluido cómo crear la KEK y guardarlo en el depósito con PowerShell.

-   [Cifrado de cliente y Azure depósito clave para el almacenamiento de Microsoft Azure](storage-client-side-encryption.md)

    Este artículo ofrece una explicación del cifrado del cliente y proporciona ejemplos de uso de la biblioteca de cliente de almacenamiento para cifrar y descifrar recursos de los cuatro servicios de almacenamiento. También habla de Azure clave depósito.

###<a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Usar cifrado de disco de Azure para cifrar discos usados por las máquinas virtuales

Cifrado de disco Azure es una nueva característica que está en vista previa. Esta característica le permite cifrar la discos de sistemas operativos y los datos usados por una máquina Virtual de IaaS. Para Windows, las unidades se cifran mediante la tecnología de cifrado de BitLocker estándar. Para Linux, los discos se cifran mediante la tecnología de cifrado DM. Esto se integra con Azure depósito de clave que le permite controlar y administrar las claves de cifrado de disco.

La solución de cifrado de disco de Azure es compatible con los siguientes escenarios de cifrado de tres clientes:

-   Habilitar el cifrado en nuevas máquinas virtuales de IaaS creado a partir de archivos de disco duro virtual de cifrado del cliente y claves de cifrado proporcionada por el cliente, que se almacenan en depósito de clave de Azure.

-   Habilitar el cifrado en nuevas máquinas virtuales de IaaS creado a partir de Azure Marketplace.

-   Habilitar el cifrado en máquinas virtuales de IaaS existentes ya en ejecución en Azure.

>[AZURE.NOTE] Máquinas virtuales de Linux ya en ejecución en Azure o de nuevas máquinas virtuales de Linux creado a partir de las imágenes de Azure Marketplace, cifrado del disco de sistema operativo no es compatible actualmente. Se admite el cifrado de volumen del sistema operativo para máquinas virtuales de Linux solo para máquinas virtuales que se han cifrado local y cargan en Azure. Esta restricción se aplica solo en el disco de sistema operativo; se admite el cifrado de volúmenes de datos para una VM Linux.

La solución es compatible con las siguientes acciones para máquinas virtuales de IaaS versión de vista previa público cuando habilitadas en Microsoft Azure:

-   Integración con Azure depósito clave

-   Estándar [A, D y G serie IaaS VM](https://azure.microsoft.com/pricing/details/virtual-machines/)

-   Habilitar el cifrado en máquinas virtuales de IaaS creados con el [Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md) modelo

-   Público Azure todas las [regiones](https://azure.microsoft.com/regions/)

Esta característica garantiza que todos los datos de los discos de la máquina virtual está cifrado al resto de almacenamiento de Azure.

####<a name="resources"></a>Recursos

-   [Cifrado de disco Azure para Windows y máquinas virtuales de Linux IaaS](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

    En este artículo se describe la versión preliminar de Azure disco cifrado y proporciona un vínculo para descargar las notas.

###<a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Comparación de cifrado de disco Azure, SSE y cifrado de cliente

####<a name="iaas-vms-and-their-vhd-files"></a>Máquinas virtuales de IaaS y sus archivos de disco duro virtual

Discos usados por máquinas virtuales de IaaS, recomendamos usar cifrado de disco de Azure. Puede activar SSE para cifrar los archivos de disco duro virtual que se usan para realizar copias de los discos de almacenamiento de Azure, pero sólo cifra datos recién escritos. Esto significa que si crea una máquina virtual y, a continuación, habilitar SSE en la cuenta de almacenamiento que contiene el archivo de disco duro virtual, se pueden cifrar únicamente los cambios, no el archivo original del disco duro virtual.

Si crea una máquina virtual con una imagen de Azure Marketplace, Azure realiza una [copia superficial](https://en.wikipedia.org/wiki/Object_copying) de la imagen a su cuenta de almacenamiento en el almacenamiento de Azure y no están cifrado incluso si tiene SSE habilitado. Cuando crea la máquina virtual y se inicia la actualización de la imagen, SSE empezará a cifrar los datos. Por este motivo, es mejor usar cifrado de disco de Azure en máquinas virtuales creadas a partir de las imágenes de Azure Marketplace si desea totalmente cifrada.

Si se coloca una máquina virtual previamente cifrada en Azure locales, podrá cargar las claves de cifrado en depósito de clave de Azure y seguir utilizando el cifrado para dicha máquina virtual que estaba utilizando local. Cifrado de disco Azure está habilitado para controlar este escenario.

Si tiene disco duro sin cifrar virtual locales, puede cargar en la Galería como una imagen personalizada y proporciona una máquina virtual de él. Si hace esto con las plantillas de administrador de recursos, puede pedirle que activar el cifrado de disco de Azure cuando se inicia la máquina virtual.

Cuando agrega un disco de datos y montaje en la máquina virtual, puede activar el cifrado de disco de Azure en el disco datos. Cifra primero ese disco de datos local y, a continuación, la capa de administración de servicio va a hacer una escritura diferida contra almacenamiento para que el contenido de almacenamiento está cifrado.

####<a name="client-side-encryption"></a>Cifrado de cliente####

Cifrado de cliente es el método más seguro de cifrado porque cifra antes de tránsito y cifra los datos en el resto de los datos. Sin embargo, es necesario agregar código a sus aplicaciones de almacenamiento, que puede que no desee hacer. En estos casos, puede usar HTTPs para sus datos en tránsito y SSE para cifrar los datos en el resto.

Con el cifrado de cliente, puede cifrar entidades de tabla, la cola de mensajes y BLOB. Con SSE, sólo puede cifrar BLOB. Si necesita cifrar los datos de tabla y cola, debe utilizar el cifrado de cliente.

Cifrado de cliente es administrado por completo por la aplicación. Es el enfoque más seguro, pero requieren que realizar cambios mediante programación en la aplicación y colocar los procesos de administración de claves en su lugar. Se usa cuando desee la seguridad adicional durante el tránsito, y desea que los datos almacenados cifrado.

Cifrado de cliente es más carga en el cliente y deberá tener esto en cuenta los planes de escalabilidad, especialmente si está cifrado y transferir una gran cantidad de datos.

####<a name="storage-service-encryption-sse"></a>Cifrado de servicio de almacenamiento (SSE)

SSE administrado por almacenamiento de Azure. Utilizar SSE no proporciona para la seguridad de los datos al transmitirlos, pero cifra los datos mientras se escribe en el almacenamiento de Azure. No hay ningún impacto en el rendimiento al usar esta característica.

Solo puede cifrar bloque BLOB, anexar BLOB y página BLOB con SSE. Si necesita cifrar datos de tabla o cola, considere la posibilidad de uso del cifrado del cliente.

Si tiene un archivo o una biblioteca de archivos de disco duro virtual que usar como base para la creación de nuevas máquinas virtuales, puede crear una nueva cuenta de almacenamiento, habilitar SSE y, a continuación, cargue los archivos de disco duro virtual a esa cuenta. Los archivos de disco duro virtual estará cifrados por el almacenamiento de Azure.

Si tiene habilitados para los discos en una máquina virtual y SSE habilitadas para la cuenta de almacenamiento que contiene los archivos del disco duro virtual de Azure disco cifrado, funcionará correctamente; se traducirán en los datos recién escrito se cifran dos veces.

##<a name="storage-analytics"></a>Análisis de almacenamiento

###<a name="using-storage-analytics-to-monitor-authorization-type"></a>Usar el análisis de almacenamiento para controlar el tipo de autorización

Para cada cuenta de almacenamiento, puede habilitar análisis de almacenamiento de Azure realizar el registro y almacenar datos de métricas. Esta es una excelente herramienta para usar cuando desee comprobar las métricas de rendimiento de una cuenta de almacenamiento, o necesita solucionar problemas de una cuenta de almacenamiento porque está teniendo problemas de rendimiento.

Otro dato que puede ver en los registros de análisis de almacenamiento es el método de autenticación usado por una persona cuando tengan acceso a almacenamiento. Por ejemplo, con el almacenamiento de blobs de Windows, puede ver si utilizan una firma de acceso compartido o las teclas de la cuenta de almacenamiento, o si el blob acceso fue público.

Esto puede ser muy útil si estrechamente son proteger el acceso al almacenamiento. Por ejemplo, en el almacenamiento de blobs de todos los contenedores sea privado e implementar el uso de un servicio de SA a través de las aplicaciones. A continuación, puede comprobar los registros con frecuencia para ver si se tiene acceso a su BLOB mediante las teclas de cuenta de almacenamiento, lo que pueden indicar una infracción de seguridad, o si los BLOB son públicos pero no debería ser.

####<a name="what-do-the-logs-look-like"></a>¿Qué los registros de aspecto?

Después de habilitar la métrica de la cuenta de almacenamiento y el registro a través del portal de Azure, datos de análisis empezará a acumular con rapidez. El registro y métricas para cada servicio es independiente; el registro solo se escribe cuando hay actividad en esa cuenta de almacenamiento, mientras que las mediciones se registrarán cada minuto, cada hora o cada día, dependiendo de la configuración.

Los registros se almacenan en BLOB bloque en un contenedor denominado $logs en la cuenta de almacenamiento. Este contenedor se crea automáticamente cuando se habilita el análisis de almacenamiento. Una vez creado este contenedor, no se puede eliminar, aunque sí puede eliminar su contenido.

En el contenedor $logs, hay una carpeta para cada servicio y hay subcarpetas de año/mes/día/hora. En hora, simplemente se numeran los registros. Este es el aspecto que tendrá la estructura de directorio:

![Vista de archivos de registro](./media/storage-security-guide/image1.png)

Se registra cada solicitud de almacenamiento de Azure. Aquí es una instantánea de un archivo de registro, que muestra los primeros algunos campos.

![Instantánea de un archivo de registro](./media/storage-security-guide/image2.png)

Puede ver que puede usar los registros para realizar un seguimiento de cualquier tipo de llamadas a una cuenta de almacenamiento.

####<a name="what-are-all-of-those-fields-for"></a>¿Cuáles son todos los campos de?

Hay un artículo que aparece en los recursos siguientes que proporciona la lista de los muchos campos en los registros y qué se utilizan. Aquí encontrará la lista de campos en orden:

![Instantánea de campos en un archivo de registro](./media/storage-security-guide/image3.png)

Estamos interesados en las entradas para GetBlob y cómo se autentican, así que necesitamos busque entradas con el tipo de operación "Get-Blob" y a continuación, compruebe el estado de la solicitud<sup>(columna 4)</sup> y el tipo de autorización (8<sup>ésima</sup> columna).

Por ejemplo, en las primeras filas en la lista anterior, el estado de la solicitud es "Correcto" y el tipo de autorización "autenticación". Esto significa que la solicitud se valida utilizando la clave de cuenta de almacenamiento.

####<a name="how-are-my-blobs-being-authenticated"></a>¿Cómo se vayan a autenticar mi BLOB?

Tenemos tres casos que le interesa.

1.  El blob es público y se tiene acceso con una dirección URL sin una firma de acceso compartido. En este caso, el estado de la solicitud es "AnonymousSuccess" y el tipo de autorización es "anónimo".

    1.0; 2015-11-17T02:01:29.0488963Z; GetBlob; **AnonymousSuccess**; 200; 124 37; **anónimo**; mystorage...

2.  El blob es privado y se usó con una firma de acceso compartido. En este caso, el estado de la solicitud es "SASSuccess" y el tipo de autorización es "sa".

    1.0; 2015-11-16T18:30:05.6556115Z; GetBlob; **SASSuccess**200 416; 64; **sa**; mystorage...

3.  El blob es privado y se ha usado la clave de almacenamiento para tener acceso a él. En este caso, el estado de la solicitud es "**correcto**" y el tipo de autorización es "**autenticado**".

    1.0; 2015-11-16T18:32:24.3174537Z; GetBlob; **Éxito**206 59; 22; **se han autenticado**; mystorage...

Puede usar el analizador de mensaje de Microsoft para ver y analizar estos registros. Incluye funciones de búsqueda y filtro. Por ejemplo, desea buscar instancias de GetBlob para ver si el uso es lo que espera, es decir, para asegurarse de que alguien no obtiene acceso a su cuenta de almacenamiento inapropiadamente.

####<a name="resources"></a>Recursos

-   [Análisis de almacenamiento](storage-analytics.md)

    En este artículo es una descripción general de análisis de almacenamiento y cómo habilitarlas.

-   [Formato de registro de análisis de almacenamiento](https://msdn.microsoft.com/library/azure/hh343259.aspx)

    Este artículo muestra el formato de registro de análisis de almacenamiento y detalles de los campos disponibles en él, incluidos el tipo de autenticación, lo que indica el tipo de autenticación que se utiliza para la solicitud.

-   [Monitor de una cuenta de almacenamiento en el portal de Azure](storage-monitor-storage-account.md)

    Este artículo le muestra cómo configurar el registro para una cuenta de almacenamiento y la supervisión de métricas.

-   [Solución de problemas de llevar a cabo con métrica de almacenamiento de Azure y registro, AzCopy y el analizador de mensaje](storage-e2e-troubleshooting.md)

    Este artículo trata sobre solución de problemas con el análisis de almacenamiento y muestra cómo usar el analizador de mensaje de Microsoft.

-   [Guía de funcionamiento de Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)

    En este artículo es la referencia para el analizador de mensaje de Microsoft e incluye vínculos a un tutorial, el inicio rápido y la función de resumen.

##<a name="cross-origin-resource-sharing-cors"></a>Recursos de origen entre uso compartido (CORS)

###<a name="cross-domain-access-of-resources"></a>Acceso de los dominios de recursos

Cuando un explorador web que se ejecute en un dominio, realiza una solicitud HTTP para un recurso de un dominio diferente, se denomina una solicitud HTTP entre origen. Por ejemplo, una página HTML servida desde contoso.com realiza una solicitud de un archivo jpeg hospedado en fabrikam.blob.core.windows.net. Por motivos de seguridad, exploradores restringen las solicitudes HTTP entre origen iniciadas desde dentro de secuencias de comandos, como JavaScript. Esto significa que cuando código JavaScript en una página web en contoso.com solicita que jpeg en fabrikam.blob.core.windows.net, el explorador no permite la solicitud.

¿Qué tiene esto con el almacenamiento de Azure? Bien, si va a almacenar activos estáticos como archivos de datos JSON o XML en el almacenamiento de blobs de Windows mediante una cuenta de almacenamiento denominado Fabrikam, el dominio de los activos será fabrikam.blob.core.windows.net y la aplicación web de contoso.com no podrá acceder a ellos mediante JavaScript porque los dominios son diferentes. Esto también es true si está intentando llamar a uno de los servicios de almacenamiento de Azure, como el almacenamiento de tablas: que devolver datos JSON procese el cliente de JavaScript.

####<a name="possible-solutions"></a>Posibles soluciones

Una forma de resolver este problema es asignar un dominio personalizado como "storage.contoso.com" a fabrikam.blob.core.windows.net. El problema es que solo puede asignar ese dominio personalizado a la cuenta de uno almacenamiento. ¿Qué ocurre si los activos se almacenan en varias cuentas de almacenamiento?

Otra forma de resolver este problema es que la aplicación web actúe como un servidor proxy para las llamadas de almacenamiento. Esto significa que si va a cargar un archivo en el almacenamiento de blobs de Windows, la aplicación web desea escribir de forma local y se cópielo al almacenamiento de blobs o desea leer todo en la memoria y, a continuación, vuelva a escribirla en el almacenamiento de blobs. Como alternativa, puede escribir una aplicación web dedicado (por ejemplo, una API Web) que carga los archivos localmente y escribe en el almacenamiento de blobs. En ambos casos, tiene en cuenta que funcionan al determinar la escalabilidad las necesidades.

####<a name="how-can-cors-help"></a>¿Cómo puede ayudar CORS?

Almacenamiento de Azure le permite habilitar CORS: compartir recursos de origen cruzada. Para cada cuenta de almacenamiento, puede especificar los dominios que pueden tener acceso a los recursos en esa cuenta de almacenamiento. Por ejemplo, en nuestro caso descrito anteriormente, podemos habilitar CORS en la cuenta de almacenamiento de fabrikam.blob.core.windows.net y configúrelo para permitir el acceso a contoso.com. A continuación, el contoso.com de aplicación web puede acceder directamente a los recursos en fabrikam.blob.core.windows.net.

Hay que destacar es que CORS permite el acceso, pero no proporciona autenticación, que es necesaria para todos los distintos públicos de access de recursos de almacenamiento. Esto significa que solo puede acceder a BLOB si están públicos o incluir una firma de acceso compartido que le da el permiso adecuado. Tablas, colas y archivos no tienen público acceso y requieran una SA.

CORS está deshabilitado de forma predeterminada, en todos los servicios. Puede habilitar CORS mediante la API de REST o la biblioteca de cliente de almacenamiento para llamar a uno de los métodos para configurar las directivas de servicio. Cuando lo haga, incluir una regla CORS, que se encuentra en XML. Aquí es un ejemplo de una regla CORS que se ha establecido mediante la operación de establecer propiedades del servicio para el servicio de blobs de una cuenta de almacenamiento. Puede realizar esta operación con las API de REST o la biblioteca de cliente de almacenamiento para el almacenamiento de Azure.

    <Cors>    
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
            <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
            <MaxAgeInSeconds>200</MaxAgeInSeconds>
        </CorsRule>
    <Cors>

Esto es lo que significa cada fila:

-   **AllowedOrigins** Esto indica a los dominios que no coincidentes pueden solicitar y recibir datos del servicio de almacenamiento. Esto indica que contoso.com y fabrikam.com pueden solicitar datos de almacenamiento de blobs de una cuenta de almacenamiento específico. También puede hacer esto para un carácter comodín (\*) para permitir que todos los dominios de solicitudes de acceso.

-   **AllowedMethods** Esta es la lista de métodos (verbos de solicitud HTTP) que pueden utilizarse para realizar la solicitud. En este ejemplo, se permiten solo obtener y colocar. Puede configurar a un carácter comodín (\*) para permitir que todos los métodos para usarse.

-   **AllowedHeaders** Se trata de los encabezados de solicitud que puede especificar el dominio de origen al realizar la solicitud. En este ejemplo, todos los encabezados de metadatos, comenzando con x-ms-metadatos, x ms meta de destino y x ms meta abc se admiten. El carácter comodín (\*) indica que se permite cualquier encabezado que empiece por el prefijo especificado.

-   **ExposedHeaders** Esto indica qué encabezados de respuestas que se deben exponer el explorador para el emisor de la solicitud. En este ejemplo, cualquier encabezado comenzando con "x-ms - meta-" estará expuesto.

-   **MaxAgeInSeconds** Esta es la cantidad máxima de tiempo que un explorador caché la solicitud de opciones de comprobación. (Para obtener más información sobre la solicitud de comprobaciones, consulte el siguiente artículo primera).

####<a name="resources"></a>Recursos

Para obtener más información sobre CORS y cómo habilitarlo, consulte estos recursos.

-   [Recursos compartidos soporte (CORS) para los servicios de almacenamiento de Azure en Azure.com de origen de cruz](storage-cors-support.md)

    Este artículo proporciona una descripción general de CORS y cómo configurar las reglas para los servicios de almacenamiento diferente.

-   [Recursos de origen entre uso compartido de soporte técnico (CORS) para los servicios de almacenamiento de Azure en MSDN](https://msdn.microsoft.com/library/azure/dn535601.aspx)

    Esta es la documentación de referencia para la compatibilidad con CORS para los servicios de almacenamiento de Azure. Contiene vínculos a artículos que aplicar a cada servicio de almacenamiento y se muestra un ejemplo y se explica cada elemento en el archivo CORS.

-   [Microsoft Azure almacenamiento: Introducción CORS](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

    Se trata de un vínculo al artículo blog inicial anuncio CORS y que muestra cómo se usa.

##<a name="frequently-asked-questions-about-azure-storage-security"></a>Preguntas más frecuentes sobre seguridad de almacenamiento de Azure

1.  **¿Cómo puedo comprobar la integridad de los objetos binarios que estoy transferir dentro o fuera de Azure almacenamiento si no puedo utilizar el protocolo HTTPS?**

    Si por cualquier motivo que necesita usar HTTP en lugar de HTTPS y está trabajando con BLOB bloque, puede usar la comprobación de MD5 para ayudar a comprobar la integridad de los objetos binarios que se transfieren. Esto le ayudará a con protección de errores de capa de transporte o de red, pero no necesariamente con ataques de intermediarios.

    Si puede utilizar HTTPS, que proporciona la seguridad de transporte, a continuación, con la comprobación de MD5 es innecesarios y redundantes.
    
    Para obtener más información, compruebe la [Información general de MD5 de blobs de Windows Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx).

2.  **¿Qué información sobre el cumplimiento de FIPS para el gobierno?**

    Los Estados Unidos Federal información procesamiento estándar (FIPS) define los algoritmos de cifrado aprobados para su uso con los sistemas de equipo Gobierno Federal de Estados Unidos para la protección de datos confidenciales. Habilitar FIPS modo en un servidor de Windows o el escritorio indica el sistema operativo que se deben usar solo FIPS validada algoritmos de cifrado. Si una aplicación utiliza algoritmos no son compatibles, romperá las aplicaciones. Versiones con.NET Framework 4.5.2 o posterior, la aplicación cambia automáticamente los algoritmos de cifrado para usar algoritmos compatibles con FIPS cuando el equipo está en modo FIPS.

    Microsoft deja cada cliente para decidir si desea habilitar el modo FIPS. Creemos que no hay ninguna razón para los clientes que no están sujetos a normas de la administración pública para habilitar el modo FIPS predeterminada.

    **Recursos**

-   [¿Por qué nos estamos no recomendar "Modo FIPS" ya](http://blogs.technet.com/b/secguide/archive/2014/04/07/why-we-re-not-recommending-fips-mode-anymore.aspx)

    En este artículo de blog proporciona un resumen de FIPS y se explica por qué no permiten modo FIPS de forma predeterminada.

-   [FIPS 140 validación](https://technet.microsoft.com/library/cc750357.aspx)

    En este artículo se proporciona información sobre cómo cumplan el estándar FIPS para el Gobierno Federal de Estados Unidos módulos de cifrado y productos de Microsoft.

-   ["Codificación de sistema: usar FIPS algoritmos compatibles para el cifrado, hash y firma" efectos de la configuración de seguridad en Windows XP y en versiones posteriores de Windows](https://support.microsoft.com/kb/811833)

    En este artículo se habla sobre el uso del modo FIPS en equipos con Windows anteriores.
