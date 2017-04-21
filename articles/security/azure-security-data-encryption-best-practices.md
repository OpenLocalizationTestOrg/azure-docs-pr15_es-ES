<properties
   pageTitle="Prácticas recomendadas de seguridad de los datos y cifrado | Microsoft Azure"
   description="En este artículo se proporciona un conjunto de prácticas recomendadas de seguridad de datos y capacidades de Azure usa cifrado integradas."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="yuridio"/>

#<a name="azure-data-security-and-encryption-best-practices"></a>Mejores prácticas de seguridad de los datos de Azure y cifrado

Una de las claves de protección de datos en la nube está teniendo en cuenta los posibles estados en el que pueden producirse los datos y los controles que están disponibles para ese estado. Con el fin de datos de Azure seguridad cifrado mejores prácticas y las recomendaciones será alrededor de los Estados de los datos siguientes:

- En el resto: Incluye información de todos los objetos de almacenamiento, los contenedores y los tipos que existen estática en medios físicos, ser magnético o disco óptico.

- Tránsito: Cuando datos se transfiere entre componentes, ubicaciones o programas, como por la red a través de un bus de servicio (de local en la nube y viceversa, incluidas las conexiones híbrido como ExpressRoute), o durante el proceso de entrada y salida, se considera que se está en movimiento.

En este artículo se describen una colección de prácticas recomendadas de seguridad y el cifrado de datos de Azure. Estas recomendaciones derivan de nuestra experiencia con la seguridad de los datos de Azure y cifrado y las experiencias de los clientes como usted.

Para cada práctica recomendada, explicaremos:

- ¿Qué es el método recomendado
- ¿Por qué desea habilitar esa práctica recomendada
- ¿Qué podría ser el resultado si no puede habilitar la práctica recomendada
- Posibles alternativas a la práctica recomendada
- ¿Cómo puede aprender a habilitar la práctica recomendada

En este artículo de seguridad de datos de Azure y prácticas recomendadas de cifrado se basa en una opinión consenso y las capacidades de la plataforma Windows Azure y conjuntos de características que ya existen en el momento en que se escribió este artículo. Opiniones y tecnologías cambian con el tiempo y en este artículo se actualizarán de forma periódica para reflejar los cambios.

Datos de Azure cifrado y seguridad recomendaciones analizados en este artículo se incluyen:

- Exigir la autenticación multifactor
- Control de acceso (RBAC) basado en roles de uso
- Cifrar máquinas virtuales de Windows Azure
- Usar modelos de seguridad de hardware
- Administrar con estaciones seguros
- Habilitar el cifrado de datos SQL
- Proteger los datos al transmitirlos
- Exigir el cifrado de los datos de nivel de archivo


## <a name="enforce-multi-factor-authentication"></a>Exigir la autenticación multifactor

Para autenticar al usuario, es el primer paso para el acceso a datos y control de Microsoft Azure. [Autenticación multifactor de Azure (AMF)](../multi-factor-authentication/multi-factor-authentication.md) es un método para comprobar la identidad del usuario con cualquier otro método que simplemente un nombre de usuario y contraseña. Este autenticación método ayuda a proteger el acceso a datos y aplicaciones cumpliendo demanda de usuario de un proceso de inicio de sesión simple.

Habilitando Azure AMF para sus usuarios, que agrega una segunda capa de seguridad para inicios de sesión de usuario y las transacciones. En este caso, una transacción puedan tener acceso a un documento que se encuentra en un servidor de archivos o en SharePoint Online. Azure AMF también ayuda a TI para reducir la probabilidad de que una credencial comprometida tendrá acceso a los datos de la organización.

Por ejemplo: si se aplica Azure AMF para sus usuarios y configurarlo para usar una llamada de teléfono o un mensaje de texto como verificación, si se ve comprometido credenciales del usuario, el intruso no podrá tener acceso a cualquier recurso, ya que no tendrá acceso al teléfono del usuario. Las organizaciones que no agregue esta capa adicional de protección de identidad son más susceptibles de ataque robo de credenciales, lo que puede producir compromiso de datos.

Una alternativa para las organizaciones que desea conservar la autenticación control local es usar el [Servidor de autenticación multifactor de Azure](../multi-factor-authentication/multi-factor-authentication-get-started-server.md), también denominado AMF local. Con este método aún podrá exigir la autenticación multifactor, manteniendo la AMF server local.

Para obtener más información sobre AMF Azure, lea el artículo [Introducción a la autenticación multifactor de Azure en la nube](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="use-role-based-access-control-rbac"></a>Control de acceso (RBAC) basado en roles de uso
Restringir el acceso basándose en los principios de seguridad de [menor privilegio](https://en.wikipedia.org/wiki/Principle_of_least_privilege) y [necesita saber](https://en.wikipedia.org/wiki/Need_to_know) . Esto es fundamental para las organizaciones que desea aplicar directivas de seguridad para el acceso a los datos. Azure Control de acceso basado en roles (RBAC) puede usarse para asignar permisos a usuarios, grupos y aplicaciones en un determinado ámbito. El ámbito de una asignación de roles puede ser una suscripción, un grupo de recursos o un recurso.

Puede aprovechar [las funciones RBAC integradas](../active-directory/role-based-access-built-in-roles.md) en Azure asignar privilegios a los usuarios. Considere el uso de *Almacenamiento cuenta colaborador* operadores de nube que necesita para administrar las cuentas de almacenamiento y rol de *Colaborador clásico de cuenta de almacenamiento* para administrar las cuentas de almacenamiento clásico. Operadores de nube que necesita para administrar máquinas virtuales y cuenta de almacenamiento, considere la posibilidad de agregarlos al rol de *Colaborador de máquina Virtual* .

Las organizaciones que imponen el control de acceso de datos al aprovechar las capacidades como RBAC pueden dar más privilegios que es necesario para sus usuarios. Esto puede poner en peligro datos haciendo que algunos usuarios tengan acceso a los datos que no tienen en primer lugar.

Puede obtener más información sobre RBAC Azure, lea el artículo [Azure Role-Based Control de acceso](../active-directory/role-based-access-control-configure.md).

## <a name="encrypt-azure-virtual-machines"></a>Cifrar máquinas virtuales de Windows Azure
Para muchas organizaciones, el [cifrado de datos en el resto](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) es un paso obligatorio hacia la privacidad de los datos, cumplimiento y soberanía de datos. Cifrado de disco Azure permite a los administradores de TI cifrar discos de Windows y Linux IaaS Máquina Virtual (VM). Cifrado de disco Azure aprovecha la característica BitLocker estándar de la industria de Windows y la característica de cifrado DM de Linux para proporcionar cifrado de volumen para el sistema operativo y los discos de datos.

Puede aprovechar Azure disco cifrado para ayudar a proteger y proteger sus datos para satisfacer sus requisitos de cumplimiento y seguridad de la organización. Las organizaciones, también deben tener en cuenta usa cifrado para ayudar a mitigar riesgos datos relacionados a no autorizado acceso. También se recomienda que cifre unidades antes de escribir los datos confidenciales en ellos.

Asegúrese de cifrar volúmenes de datos de la VM y el volumen de inicio para proteger los datos almacenados en su cuenta de almacenamiento de Azure. Proteger las claves de cifrado y la información confidencial aprovechando [Depósito de clave de Azure](../key-vault/key-vault-whatis.md).

Para los servidores de Windows local, considere el cifrado siguiente recomendaciones:

- Usar [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) para cifrar datos
- Almacenar información de recuperación en AD DS.
- Si hay cualquier duda que se han comprometido claves de BitLocker, le recomendamos que bien formatear la unidad para quitar todas las instancias de los metadatos de BitLocker de la unidad o que descifrar y cifrar toda la unidad de nuevo.

Las organizaciones que imponen cifrado de datos están más probable que se expone a problemas de integridad de datos, como usuarios malintencionados o malintencionados robo de datos y comprometido cuentas de obtener acceso no autorizado a los datos en Borrar formato. Además de estos riesgos, las empresas que deben cumplir las normas de la industria, debe demostrar que son minucioso y que va a usar los controles de seguridad correcta para mejorar la seguridad de datos.

Puede obtener más información sobre el cifrado de disco de Azure, lea el artículo [Azure disco cifrado para Windows y máquinas virtuales de IaaS Linux](azure-security-disk-encryption.md).

## <a name="use-hardware-security-modules"></a>Utilizar los módulos de seguridad de Hardware

Soluciones de cifrado de la industria usar teclas secretas para cifrar datos. Por lo tanto, es fundamental que estas teclas se almacenan de forma segura. Administración de claves se convierte en una parte integral de protección de datos, ya que se utilizarán para almacenar claves secretas que se usan para cifrar los datos.

Cifrado de disco Azure usa [Depósito de Azure clave](https://azure.microsoft.com/services/key-vault/) para ayudarle a controlar y administrar las claves de cifrado del disco y la información confidencial en su suscripción de depósito clave, mientras le garantiza que todos los datos en los discos de la máquina virtual se cifran almacenados en el almacenamiento de Azure. Debe utilizar depósito de Azure clave para las teclas y el uso de la directiva de auditoría.

Hay muchos riesgos inherentes para no tener controles de seguridad adecuados para proteger las claves secretas que se utilizaron para cifrar los datos. Si los intrusos tiene acceso a las claves secretos, puedan descifrar los datos y tener acceso a información confidencial.

Puede obtener más información sobre las recomendaciones generales para la administración de certificados en Azure, lea el artículo [administración de certificados en Azure: recomendaciones y prohibiciones](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/).

Para obtener más información acerca de la cámara de clave de Azure, lea la [Introducción a Azure clave depósito](../key-vault/key-vault-get-started.md).

## <a name="manage-with-secure-workstations"></a>Administrar con estaciones seguros

Puesto que la mayoría de los ataques destinar el usuario final, el extremo se convierte en uno de los puntos principales de ataque. Si un intruso comprometa el extremo, puede aprovechar las credenciales del usuario para tener acceso a datos de la organización. La mayoría de extremo de ataques son capaces de aprovechar el hecho de que los usuarios finales son administradores en sus equipos locales.

Puede reducir estos riesgos mediante una estación de trabajo de administración segura. Le recomendamos que use un [Estaciones de trabajo con privilegios de acceso (PATA)](https://technet.microsoft.com/library/mt634654.aspx) para reducir la superficie de ataque en estaciones de trabajo. Estas estaciones de trabajo de administración segura pueden ayudar a mitigar algunos de estos ataques para garantizar que los datos sean más seguros. Asegúrese de usar PATA refuerce y bloquear la estación de trabajo. Se trata de un paso importante para proporcionar garantías de alta seguridad para las cuentas confidenciales, tareas y protección de datos.

Falta de protección de extremo puede poner los datos en riesgo, asegúrese de aplicar directivas de seguridad en todos los dispositivos que se utilizan para consumir datos, independientemente de la ubicación de datos (nube o local).

Puede obtener más información sobre privilegios acceder a estación de trabajo, lea el artículo [Proteger el acceso con privilegios](https://technet.microsoft.com/library/mt631194.aspx).

## <a name="enable-sql-data-encryption"></a>Habilitar el cifrado de datos SQL

[Cifrado de datos transparente de base de datos de SQL Azure](https://msdn.microsoft.com/library/dn948096.aspx) (TDE) ayuda a proteger contra la amenaza de actividad malintencionada realizando en tiempo real cifrado y descifrado de la base de datos, las copias de seguridad asociados y los archivos de registro de transacciones en el resto sin necesidad de cambios en la aplicación.  TDE cifra el almacenamiento de toda una base de datos mediante una clave simétrica denominada la clave de cifrado de base de datos.

Aunque el almacenamiento de información se cifra, es muy importante cifrar la base de datos. Se trata de una implementación de la defensa en el enfoque de profundidad para la protección de datos. Si está utilizando la [Base de datos de SQL Azure](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) y desea proteger datos confidenciales como tarjeta de crédito o números de la seguridad social, puede cifrar las bases de datos con cifrado AES de 256 bits de 140-2 validar de FIPS que cumple los requisitos de los muchos estándares (por ejemplo, HIPAA, PCI).

Es importante comprender que no se cifran los archivos relacionados con la [extensión de agrupación de búfer](https://msdn.microsoft.com/library/dn133176.aspx) (BPE) cuando se cifra una base de datos usando TDE. Debe usar herramientas de cifrado de nivel de sistema de archivos como BitLocker o archivos relacionados con el [Sistema de archivos cifrados](https://technet.microsoft.com/library/cc700811.aspx) (EFS) para BPE.

Desde un usuario no autorizado, como un administrador de seguridad o una base de datos puede tener acceso a los datos incluso si la base de datos se cifra con TDE, también debería seguir las siguientes recomendaciones:

- Autenticación de SQL en el nivel de base de datos
- Uso de funciones RBAC Azure autenticación de AD
- Los usuarios y aplicaciones deben utilizar cuentas independientes para autenticar. De esta forma puede limitar los permisos para los usuarios y las aplicaciones y reducir el riesgo de actividad malintencionada
- Implementar seguridad a nivel de la base de datos usando funciones de base de datos fija (como db_datareader o db_datawriter), o bien puede crear funciones personalizadas para su aplicación conceder permisos explícitos a objetos de base de datos seleccionada

Las organizaciones que no utilizan cifrado de nivel de base de datos pueden ser más susceptibles de ataques que pueden afectar los datos ubicados en bases de datos SQL.

Puede obtener más información sobre el cifrado de SQL TDE, lea el artículo de [Cifrado de datos transparente con la base de datos de SQL Azure](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="protect-data-in-transit"></a>Proteger los datos al transmitirlos

Proteger los datos al transmitirlos debe ser una parte esencial de la estrategia de protección de datos. Puesto que los datos se va a mover hacia adelante y hacia atrás desde varias ubicaciones, la recomendación general es usar siempre los protocolos SSL/TLS intercambio de datos entre diferentes ubicaciones. En algunos casos, es aconsejable aislar el canal de comunicación completa entre su local y la nube infraestructura utilizando una red privada virtual (VPN).

Para moverse entre la infraestructura local y Azure de datos, debe tener en cuenta las garantías adecuadas, como HTTPS o VPN.

Para las organizaciones que necesitan para proteger el acceso desde varias estaciones de trabajo encuentra local a Azure, use [Azure VPN de sitio a otro](../vpn-gateway/vpn-gateway-site-to-site-create.md).

Para organizaciones que necesitan acceso seguro desde una estación de trabajo encuentra local a Azure, utilice [punto al sitio VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md).

Vincular datos más grandes conjuntos de se pueden mover sobre una WAN de alta velocidad dedicada como [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Si decide usar ExpressRoute, también puede cifrar los datos en el nivel de aplicación con [SSL/TLS](https://support.microsoft.com/kb/257591) u otros protocolos para agregar la protección.

Si se interactúa con el almacenamiento de Azure a través del Portal de Azure, todas las transacciones se producen a través de HTTPS. [API de REST de almacenamiento](https://msdn.microsoft.com/library/azure/dd179355.aspx) a través de HTTPS también puede usarse para interactuar con [El almacenamiento de Azure](https://azure.microsoft.com/services/storage/) y [Base de datos de SQL Azure](https://azure.microsoft.com/services/sql-database/).

Las organizaciones que no para proteger los datos al transmitirlos son más susceptibles de [ataques de intermediario](https://technet.microsoft.com/library/gg195821.aspx), [espionaje](https://technet.microsoft.com/library/gg195641.aspx) y secuestro de sesión. Estos ataques pueden ser el primer paso para obtener acceso a información confidencial.

Puede obtener más información acerca de la opción de Azure VPN, lea el artículo de [planificación y diseño de puerta de enlace VPN](../vpn-gateway/vpn-gateway-plan-design.md).

## <a name="enforce-file-level-data-encryption"></a>Exigir el cifrado de los datos de nivel de archivo

Otro nivel de protección que puede aumentar el nivel de seguridad de los datos es cifrar el archivo, independientemente de la ubicación del archivo.

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) usa cifrado, la identidad y la autorización de directivas para ayudar a proteger tus archivos y correo electrónico. Azure RMS funciona en varios dispositivos, teléfonos, tabletas y equipos mediante la protección de la organización y fuera de su organización. Esta función es posible porque Azure RMS agrega un nivel de protección que acompaña a los datos, incluso cuando deja los límites de su organización.

Cuando utiliza Azure RMS para proteger los archivos, usa cifrado estándar con compatibilidad total de [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). Cuando aprovechar Azure RMS para proteger los datos, tiene la seguridad de que la protección permanece con el archivo, incluso si lo ha copiado al almacenamiento que no está bajo el control de TI, por ejemplo, un servicio de almacenamiento de la nube. Lo mismo ocurre con los archivos compartidos por correo electrónico, el archivo está protegido como dato adjunto a un mensaje de correo electrónico, con instrucciones cómo abrir el archivo adjunto protegido.

Al planear la adopción de Azure RMS, le recomendamos lo siguiente:

- Instale la [aplicación de uso compartido de RMS](https://technet.microsoft.com/library/dn339006.aspx). Esta aplicación se integra con Office aplicaciones instalando una oficina complemento para que los usuarios puedan proteger fácilmente archivos directamente.
- Configurar aplicaciones y servicios para admitir Azure RMS
- Crear [plantillas personalizadas](https://technet.microsoft.com/library/dn642472.aspx) que reflejen sus requisitos empresariales. Por ejemplo: una plantilla de datos secretos superiores que deben aplicarse en secreto superior relacionados con mensajes de correo electrónico.

Las organizaciones que son débiles sobre protección de [clasificación de datos](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) y el archivo pueden ser más susceptibles a pérdidas de datos. Sin protección de archivos adecuado, las organizaciones no pueden obtener perspectivas empresariales, supervisar para abuso y evitar el acceso malintencionado a archivos.

Puede obtener más información acerca de Azure RMS, lea el artículo [Introducción a Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).
