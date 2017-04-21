<properties
   pageTitle="Proteger un clúster de servicio tela | Microsoft Azure"
   description="Describe los escenarios de seguridad de un clúster de tela de servicio y las distintas tecnologías utilizadas para implementar los escenarios."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/19/2016"
   ms.author="chackdan"/>

# <a name="service-fabric-cluster-security-scenarios"></a>Escenarios de seguridad de clúster de tela de servicio

Un clúster de servicio tela es un recurso que es el propietario. Siempre se deben proteger clústeres para impedir que los usuarios no autorizados a conectarse a su clúster, especialmente cuando tenga las cargas de trabajo de producción ejecutando en él. Aunque es posible crear un clúster no seguro, hacerlo así que le permitirá que cualquier usuario anónimo para conectarse a ella si expone extremos de administración a Internet. 

Este artículo proporciona una descripción general de los escenarios de seguridad para clústeres se ejecutan en Azure o independiente y las diversas tecnologías utilizadas para implementar los escenarios. Los escenarios de seguridad de clúster son:

- Seguridad de nodo a otro
- Seguridad de nodo de cliente
- Control de acceso basado en roles (RBAC)

## <a name="node-to-node-security"></a>Seguridad de nodo a otro
Protege la comunicación entre las máquinas virtuales o equipos del clúster. Así se garantiza que sólo los equipos que están autorizados a unirse al clúster pueden participar en aplicaciones y servicios en el clúster de hospedaje.

![Diagrama de comunicación de nodo a otro][Node-to-Node]

Clústeres se ejecutan en clústeres Azure o independiente que ejecute Windows pueden usar el [Certificado de seguridad](https://msdn.microsoft.com/library/ff649801.aspx) o de [Seguridad de Windows](https://msdn.microsoft.com/library/ff649396.aspx) para equipos con Windows Server.
### <a name="node-to-node-certificate-security"></a>Nodo a otro certificado de seguridad
Servicio tela usa certificados de servidor X.509 que especifique como parte de las configuraciones de tipo de nodo cuando se crea un clúster. Al final de este artículo, se proporciona una descripción general de estos certificados son y cómo puede adquirir o crearlos.

Certificado de seguridad está configurado al crear el clúster a través del portal de Azure, plantillas de administrador de recursos de Azure o una plantilla JSON independiente. Puede especificar un certificado principal y un certificado secundario opcional que se usa para imágenes de sustitución de certificado. Los certificados primario y secundarios que especifique deben ser diferentes de los certificados de cliente de solo lectura que especifique para la [seguridad de nodo de cliente](#client-to-node-security)y el cliente de administración.

Para Azure lea [configurar un clúster mediante una plantilla de Azure Administrador de recursos](service-fabric-cluster-creation-via-arm.md) para aprender a configurar el certificado de seguridad en un clúster.

Para independiente Windows Server lea [seguro un clúster independiente en Windows con certificados X.509](service-fabric-windows-cluster-x509-security.md)

### <a name="node-to-node-windows-security"></a>Seguridad de windows de nodo a otro
Para independiente Windows Server lea [seguro un clúster independiente en Windows con la seguridad de Windows](service-fabric-windows-cluster-windows-security.md)

## <a name="client-to-node-security"></a>Seguridad de nodo de cliente
Autentica a los clientes y protege la comunicación entre un cliente y nodos individuales en el clúster. Este tipo de seguridad autentica y protege las comunicaciones de cliente, que garantiza que sólo los usuarios autorizados puedan tener acceso al clúster y las aplicaciones implementadas en el clúster. Los clientes se identifican a través de sus credenciales de seguridad de Windows o sus credenciales de seguridad del certificado.

![Diagrama de comunicación de nodo de cliente][Client-to-Node]

[Certificado de seguridad](https://msdn.microsoft.com/library/ff649801.aspx) o de [Seguridad de Windows](https://msdn.microsoft.com/library/ff649396.aspx), pueden usar clústeres se ejecutan en clústeres Azure o independiente que se ejecutan en Windows.

### <a name="client-to-node-certificate-security"></a>Certificado de cliente para nodo de seguridad
 Certificado de cliente para nodo de seguridad se configura al crear el clúster ya sea a través del portal de Azure, plantillas de administrador de recursos o una plantilla JSON de independiente especificando un certificado de cliente de administración o un certificado de cliente del usuario.  El Administrador cliente y usuario certificados de cliente que especifique deben ser diferentes de los certificados primario y secundarios que especifique para la [seguridad de nodo a otro](#node-to-node-security).

Clientes que se conectan al clúster mediante el certificado de administrador tienen acceso completo a las capacidades de administración.  Clientes que se conectan al clúster mediante el certificado de cliente de usuario de sólo lectura tienen acceso de sólo lectura a las capacidades de administración. En otras palabras, estos certificados se usan para el control de acceso de bases de roles (RBAC) se describe más adelante en este artículo.

Para Azure lea [configurar un clúster mediante una plantilla de Azure Administrador de recursos](service-fabric-cluster-creation-via-arm.md) para aprender a configurar el certificado de seguridad en un clúster.

Para independiente Windows Server lea [seguro un clúster independiente en Windows con certificados X.509](service-fabric-windows-cluster-x509-security.md)

### <a name="client-to-node-azure-active-directory-aad-security-on-azure"></a>Seguridad de Azure Active Directory (AAD) de nodo de cliente en Azure
Clústeres se ejecutan en Azure también pueden proteger el acceso a los extremos de administración con Azure Active Directory (AAD). Para obtener información sobre cómo crear los artefactos AAD es necesarios, cómo llenar ellos durante la creación de clúster y cómo conectarse a esos clústeres posteriormente, vea [configurar un clúster por mediante una plantilla de administrador de recursos de Azure](service-fabric-cluster-creation-via-arm.md) .

## <a name="security-recommendations"></a>Recomendaciones de seguridad
Clústeres de Azure, se recomienda que utilice seguridad AAD para autenticar clientes y certificados de seguridad de nodo a otro.

Para independiente Windows Server clústeres, se recomienda que use la seguridad de Windows con grupo administran cuentas (GMA) si tiene Windows Server 2012 R2 y Active Directory. En caso contrario, aún puede usar la seguridad de Windows con cuentas de Windows.

## <a name="role-based-access-control-rbac"></a>Control de acceso basado en roles (RBAC)
Control de acceso permite que el Administrador de clúster limitar el acceso a determinadas operaciones de clúster para los diferentes grupos de usuarios, hacer que el clúster sea más segura. Dos tipos de control de acceso diferentes son compatibles para conectarse a un clúster de clientes: rol de administrador y la función de usuario.

Los administradores tienen acceso completo a las capacidades de administración (incluidas las capacidades de lectura y escritura). Los usuarios, de forma predeterminada, tienen sólo acceso de lectura a las capacidades de administración (por ejemplo, capacidades de consulta) y la capacidad para resolver las aplicaciones y servicios.

Especifique los roles de administrador y usuario cliente en el momento de la creación de un clúster mediante identidades independientes (certificados, etc. AAD) para cada uno. Para obtener más información sobre la configuración de control de acceso predeterminada y cómo cambiar la configuración predeterminada, vea [control de acceso para los clientes de tela de servicio basado en roles](service-fabric-cluster-security-roles.md).


## <a name="x509-certificates-and-service-fabric"></a>Certificados X.509 y tela de servicio
Certificados digitales X.509 se usan generalmente para autenticar clientes y servidores y para cifrar y firmar digitalmente los mensajes. Para obtener más detalles sobre estos certificados, vaya a [trabajar con certificados](http://msdn.microsoft.com/library/ms731899.aspx).

Algunas cosas importantes a tener en cuenta:

- Certificados que se utilizan en clústeres de cargas de trabajo de producción deben creados mediante un servicio de certificados de Windows Server correctamente configurado o que se obtienen de una [Entidad de certificación (CA)](https://en.wikipedia.org/wiki/Certificate_authority)de aprobado.
- Nunca use cualquier temporal o certificados de prueba de producción creados con herramientas como MakeCert.exe.
- Puede usar un certificado autofirmado, pero deben hacerlo sólo para clústeres de prueba y no en producción.

### <a name="server-x509-certificates"></a>Certificados de X.509 de servidor

Certificados de servidor tienen la tarea principal de un servidor (nodo) a los clientes de autenticación o autenticar un servidor (nodo) a un servidor (nodo). Una de las comprobaciones iniciales cuando un nodo o cliente autentica un nodo es comprobar el valor del nombre común en el campo Asunto. Este nombre común o uno de los nombres de los certificados asunto alternativo debe estar presente en la lista de nombres comunes permitidos.

El siguiente artículo describe cómo generar certificados con nombres alternativos de asunto (SAN): [cómo agregar un nombre alternativo de sujeto a un certificado LDAP seguro](http://support.microsoft.com/kb/931351).

El campo asunto puede contener varios valores, cada uno con el prefijo con una inicialización para indicar el tipo de valor. Normalmente, la inicialización es "CN" nombre común; Por ejemplo, "CN = www.contoso.com". También es posible que esté en blanco el campo de asunto. Si se rellena el campo nombre de asunto alternativo opcional, debe contener el nombre del certificado común y una entrada por nombre alternativo de asunto. Estos se introducen como valores de nombre de DNS.

El valor del campo está pensado propósitos del certificado debe incluir un valor apropiado, como "Autenticación Server" o "cliente".

### <a name="client-x509-certificates"></a>Certificados de cliente X.509

Los certificados de cliente no se suele ser emitidos por una entidad de certificación de terceros. En su lugar, el almacén Personal de la ubicación del usuario actual contiene normalmente los certificados de cliente procedentes de una entidad de certificación raíz, con fines de "Autenticación de cliente". El cliente puede utilizar este certificado cuando se requiere autenticación mutua.

>[AZURE.NOTE] Todas las operaciones de administración en un clúster de servicio tela requieren certificados de servidor. Los certificados de cliente no se pueden usar para la administración.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->


## <a name="next-steps"></a>Pasos siguientes

En este artículo se proporciona información conceptual acerca de la seguridad de clúster. A continuación, [crear un clúster en Azure usando una plantilla de administrador de recursos](service-fabric-cluster-creation-via-arm.md) o a través del [portal de Azure](service-fabric-cluster-creation-via-portal.md).

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png
