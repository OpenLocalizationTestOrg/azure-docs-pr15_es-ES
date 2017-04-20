<properties
    pageTitle="Detección de aplicación consideraciones de seguridad y privacidad de la nube | Microsoft Azure"
    description="Este tema describe las consideraciones de seguridad y privacidad relacionadas con la detección de aplicación de nube."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi"/>

# <a name="cloud-app-discovery-security-and-privacy-considerations"></a>Detección de aplicación consideraciones de seguridad y privacidad de la nube

Microsoft se compromete a proteger su privacidad y seguridad de los datos, cuando se realiza software y servicios que ayudan a administrar la seguridad de su organización. <br>
Se reconoce cuando confiar sus datos con otros, que confianza requiere inversiones de ingeniería de seguridad rigurosa y experiencia para realizar una copia.
Microsoft se adhiere a estricta y directrices de seguridad de prácticas de ciclo de vida de desarrollo de software seguro a un servicio de funcionamiento. <br>
Seguridad y protección de datos son una prioridad máxima de Microsoft.

Este tema explica cómo recopilados, procesados y segura dentro de la aplicación de descubrimiento de Azure Active Directory nube datos




##<a name="overview"></a>Información general

Detección de aplicación de nube es una característica de Azure AD y está alojado en Microsoft Azure. <br>
El agente de extremo de descubrimiento de aplicación de nube se usa para recopilar datos de detección de aplicación de equipos de TI administrado. <br>
Los datos recopilados se envían de forma segura a través de un canal cifrado al servicio de detección de la aplicación de nube de Azure AD. <br>
Los datos de detección de la aplicación de nube para una organización, a continuación, están visibles en el portal de Azure. <br>


<center>![Cómo funciona la detección de la aplicación de nube](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png)</center> <br>


Las secciones siguientes, siga el flujo de información y describa cómo está protegido mientras se desplaza de su organización para el servicio de detección de la aplicación de nube y, finalmente, en el portal de detección de la aplicación de nube.



## <a name="collecting-data-from-your-organization"></a>Recopilar datos de su organización

Para poder usar la característica de detección de aplicación de nube de Azure Active Directory para obtener recomendaciones sobre las aplicaciones utilizadas por los empleados de su organización, debe implementar primero el agente de extremo de descubrimiento de aplicación de nube de Azure AD en los equipos de su organización.

Los administradores de inquilinos de Azure Active Directory (o su delegado) pueden descargar el paquete de instalación desde el portal de Azure. El agente puede ser manualmente instalado o instala en varios equipos de la organización mediante SCCM o la directiva de grupo.

Para obtener instrucciones detalladas sobre las opciones de implementación, vea [Guía de implementación de la directiva de grupo de nube aplicación detección](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx).
<br>

### <a name="data-collected-by-the-agent"></a>Datos recopilados por el agente

La información que se indica en la siguiente lista se recopila el agente cuando se realiza una conexión a una aplicación Web. La información se recopila sólo para las aplicaciones que el administrador ha configurado para detección. <br>
Puede editar la lista de aplicaciones de nube que supervisa el agente mediante el módulo de detección de la aplicación de nube de Microsoft [Azure portal](https://portal.azure.com/), en **configuración de**->**Recopilación de datos**->**lista de colecciones de aplicación**. Para obtener más detalles, vea [Introducción iniciado con nube aplicación descubrimiento](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
<br>
**Categoría de información**: la información de usuario <br>
**Descripción**: <br>
El nombre de usuario de Windows del proceso que realizó una solicitud para la aplicación Web de destino (por ejemplo: DOMINIO\nombre de usuario), así como el identificador de seguridad de Windows (SID) del usuario.


**Categoría de información**: información del proceso <br>
**Descripción**: <br>
El nombre del proceso que realizó la solicitud a la aplicación Web de destino (por ejemplo: "iexplore.exe")

**Categoría de información**: información del equipo <br>
**Descripción**: <br>
El nombre de NetBIOS del equipo donde está instalado el agente.

**Categoría de información**: la información de tráfico de aplicación <br>
**Descripción**: <br>

La siguiente información de conexión:

- El origen (equipo local) y direcciones IP de destino y números de puerto

- La dirección IP pública de la organización a través del cual se transmite la solicitud.

- La hora de la solicitud

- El volumen de tráfico enviados y recibidos

- La versión IP (4 o 6)

- Para las conexiones de TLS: el nombre de host de destino de la extensión de nombre de servidor indicación o el certificado de servidor.

La siguiente información de HTTP:

- Método (GET, POST, etcetera).

- Protocolo (HTTP/1.1, etcetera).

- Cadena de agente de usuario

- Nombre de host

- Destino URI (cadena de consulta excluidos)

- Información de tipo de contenido

- Información de la dirección URL de referencia (excepto la cadena de consulta)



> [AZURE.NOTE] La información de HTTP anterior se recopila para todas las conexiones no cifrado.
Para las conexiones TLS, esta información solo se genera cuando el valor de 'Inspección profunda' está activado en el portal. El valor es 'ON' de forma predeterminada.
Para obtener más detalles, vea el siguiente y [Obtener iniciado con nube aplicación detección](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)


Además de los datos que el agente recopila información sobre la actividad de red, también recopilará información anónima sobre el software y configuración de hardware, informes de errores y obtener información acerca de cómo se utiliza el agente.

<br><br>
### <a name="how-the-agent-works"></a>Cómo funciona el agente

La instalación del agente incluye dos componentes:

- Un componente de modo de usuario

- Un componente de controlador de modo kernel (controlador de plataforma de filtrado de Windows)



Cuando se instala el agente por primera vez almacena un certificado de confianza específica del equipo en el equipo que después se usa para establecer una conexión segura con el servicio de detección de la aplicación de nube. <br>
El agente recupera periódicamente la configuración de directivas del servicio de detección de la aplicación de nube sobre esta conexión segura. <br>
La directiva incluye información sobre qué aplicaciones de nube para supervisar y si la actualización automática debe estar habilitada, entre otras cosas.

A medida que el tráfico Web se envía y recibe en el equipo de Internet Explorer y Chrome, el agente de descubrimiento de aplicación de nube analiza el tráfico y extrae los metadatos relevantes (consulte la sección **datos recopilados por el agente** anterior). <br>
Cada minuto, el agente de carga los metadatos recopilados en el servicio de detección de la aplicación de nube a través de un canal cifrado.

El componente de controlador intercepta el tráfico de cifrado y se inserta en la secuencia de cifrado. Más detalles en la sección **datos de Intercepting de conexiones cifradas (inspección profunda)** .


### <a name="respecting-user-privacy"></a>Respetar la privacidad del usuario

Nuestro objetivo es proporcionar a los administradores las herramientas para establecer el equilibrio entre óptico detallada en privacidad de usuario y el uso de aplicaciones según corresponda para su organización. Para ello, le proporcionamos los siguientes botones en la página Configuración del Portal:

- **Recopilación de datos**: los administradores pueden elegir especificar qué aplicaciones o categorías de aplicación que desean obtener datos de detección.

- **Inspección profunda**: los administradores pueden elegido especificar si el agente recopila tráfico HTTP para conexiones de SSL/TLS (también conocido como **' inspección profunda '**). Obtener más información sobre esto en la siguiente sección.

- **Opciones de consentimiento**: los administradores pueden usar el portal de detección de la aplicación de nube para elegir si desea notificar a los usuarios de la colección de datos por el agente de y, a continuación, si desea solicitar al usuario consentimiento antes de iniciar el agente de recopilación de datos de usuario.

El agente de extremo de descubrimiento de aplicación de nube solo recopila la información que se describe en la sección **datos recopilados por el agente** anterior.


### <a name="intercepting-data-from-encrypted-connections-deep-inspection"></a>Interceptar los datos de las conexiones cifradas (inspección profunda)
Como se mencionó anteriormente, los administradores pueden configurar el agente para supervisar los datos de las conexiones cifradas ('inspección profunda'). TLS ([Seguridad de la capa de transporte](https://msdn.microsoft.com/library/windows/desktop/aa380516%28v=vs.85%29.aspx)) es uno de los protocolos más comunes en uso en Internet hoy. Mediante el cifrado de comunicación con TLS, un cliente puede establecer un canal de comunicación segura y privada con un servidor web. TLS proporciona protección esencial para pasar las credenciales de autenticación y evitar la revelación de información confidencial.

Mientras el canal cifrado seguro de llevar a cabo proporcionado por TLS permite importantes de seguridad y protección de la privacidad, el protocolo con qué frecuencia se produce con fines malintencionados o malintencionadas. Mucho por lo tanto, de hecho, esa TLS es a menudo denomina "Protocolo de derivación de firewall universal". La raíz del problema es que la mayoría de los firewalls no se puede inspeccionar comunicación TLS porque los datos de nivel de aplicación está cifrados con SSL. Este conocimiento, los atacantes aprovechan con frecuencia TLS para ofrecer cargas malintencionados a un usuario seguro de que incluso los firewalls de nivel de aplicación más inteligentes completamente ciegos TLS y simplemente deben transmitir comunicación TLS entre hosts. Con frecuencia, los usuarios finales aprovechar TLS para omitir los controles de acceso aplicados sus firewalls corporativos y servidores proxy, usar para conectarse a los servidores proxy del públicos y para los protocolos no TLS a través del firewall que podrían ser bloqueadas por directivas de túnel.

Inspección profunda permite el agente de descubrimiento de aplicación de nube para que actúe como una confianza en-el-intermediario. Cuando se realiza una solicitud de cliente para tener acceso a un recurso HTTPS protegido, el controlador de agente de extremo intercepta la conexión y establece que una nueva conexión al servidor de destino que recupera su certificado SSL en nombre del cliente. El agente, comprueba que el certificado puede ser de confianza (por comprobar que no se revoca y la realización de otras comprobaciones de certificado) y, si estos paso, a continuación, el agente de extremo copia la información del certificado de servidor y crea su propio certificado de servidor, conocida como un certificado de interceptación--con esa información. El certificado de interceptación está firmada marcha por el agente de extremo con un certificado raíz, que está instalado en el almacén de certificados de confianza de Windows. Este certificado autofirmado raíz está marcado no se puede exportar y se había ACL a los administradores. Se pretende nunca dejar el equipo en el que se creó. Cuando la aplicación cliente del usuario final recibe el certificado de interceptación, le confiar porque puede validar correctamente la cadena de certificados hasta el certificado raíz. Este proceso es principalmente transparente desde el punto de vista del usuario final con algunas advertencias tal como se describe a continuación.

Al habilitar la inspección profunda, el agente de extremo de descubrimiento de aplicación de nube puede descifrar e inspeccionar comunicaciones TLS cifradas, lo que el servicio reducir el ruido y proporcionar recomendaciones sobre el uso de las aplicaciones de nube cifrado.

#### <a name="a-word-of-caution"></a>Una palabra de precaución
Antes de activar inspección profunda, se recomienda encarecidamente que transmitir sus intenciones a sus departamentos de recursos humanos y legal y obtener su consentimiento. Inspección de comunicaciones cifrado privada del usuario final puede ser un tema confidencial, por motivos obvios. Antes de una producción puesta en servicio de inspección profunda, asegúrese de que la seguridad de la empresa y se han actualizado las directivas de uso aceptable para indicar que se puede inspeccionar la comunicación cifrada. También pueden ser necesarios si configurar detección de aplicación de nube para supervisar su notificación de usuario y la exención de sitios considerada confidencial (por ejemplo, bancarios y médico). Como se mencionó anteriormente, los administradores pueden utilizar el portal de detección de la aplicación de nube para elegir si desea notificar a los usuarios de la colección de datos por el agente y requerir consentimiento del usuario antes de que inicia el agente de recopilación de datos de usuario.

### <a name="known-issues-and-drawbacks"></a>Problemas conocidos y desventajas
Hay algunos casos donde interceptación TLS puede afectar a la experiencia del usuario final:

- Los certificados de validación (va) extendida representarán la barra de direcciones del explorador web verde para que actúe como una indicación visual que visite un sitio web de confianza. Inspección de TLS no puede duplicar EV en el certificado que emite el cliente para que sitios web que utilicen certificados EV funcionarán normalmente, pero la barra de direcciones no muestre verde.  

- Anclaje de clave pública (también denominado certificado anclada) están diseñados para ayudar a proteger a los usuarios de ataques de intermediario y no fiables entidades emisoras de certificados. Cuando el certificado raíz para un sitio anclado no coincide con uno de la CA buena conocida, el explorador rechaza la conexión con un error. Puesto que interceptación TLS es, de hecho, una de-la-intermediario, se producirá un error en estas conexiones.

- Si los usuarios, haga clic en el icono de candado en el Explorador de barra de direcciones de explorador que inspeccionar la información del sitio, no verá una cadena que termina en la entidad emisora de certificados utiliza para firmar el certificado de sitio Web, pero en su lugar de una cadena de certificados que terminan por las ventanas de confianza almacén de certificados.

Para reducir las apariciones de estos problemas, nos realizar un seguimiento de servicios en la nube y las aplicaciones cliente conocidas utilizar validación extendida o anclada clave pública e indicarle al agente de punto final para evitar interceptar conexiones afectadas. Sin embargo, incluso en estos casos, seguirá recibiendo informes sobre el uso de estas aplicaciones de la nube y el volumen de datos que se transfieren pero que no están profundas inspeccionen, no hay detalles acerca de cómo se utilizan las aplicaciones estarán disponibles.


## <a name="sending-data-to-cloud-app-discovery"></a>Enviar datos a la detección de la aplicación de nube

Una vez que se ha recopilado metadatos por el agente, se almacena en el equipo durante un minuto o hasta que los datos almacenados en caché alcanza un tamaño de 5MB. A continuación, se comprimen y enviado a través de una conexión segura con el servicio de detección de la aplicación de nube.

Si el agente no puede comunicarse con el servicio de detección de la aplicación de nube por cualquier motivo, los metadatos recopilados se almacenan en una caché local de archivos que solo se puede acceder por usuarios con privilegios en el equipo (por ejemplo, el grupo Administradores). <br>
El agente intenta volver a enviar los metadatos en caché hasta que se recibió correctamente el servicio de detección de la aplicación de nube automáticamente.



## <a name="receiving-the-data-at-the-service-end"></a>Recibe los datos en el extremo de servicio

Los agentes autentican la detección de la aplicación de nube servicio con el certificado de autenticación de máquina cliente específico hace referencia a encima y reenvía datos a través de un canal cifrado. <br>
Canalización de análisis del servicio de detección de la aplicación de nube procesos de metadatos para cada cliente por separado lógicamente dividirla en todas las etapas del proceso de análisis.
Los metadatos analizado unidades los distintos informes en el portal.

Se almacenan los metadatos sin procesar y los metadatos analizado de 180 días. Además, pueden elegir los clientes capturar los metadatos analizados en una cuenta de almacenamiento de blobs de Windows Azure de su elección.
Esto es útil para análisis sin conexión de metadatos, así como la retención más de los datos.

## <a name="accessing-the-data-using-the-azure-portal"></a>Acceso a los datos con el portal de Azure

Con el fin de mantener los metadatos recopilado segura, de forma predeterminada sólo los administradores globales del inquilino tienen acceso a la característica de detección de la aplicación de nube en el portal de Azure. <br>
Sin embargo, los administradores pueden elegir este acceso delegado a otros usuarios o grupos.



> [AZURE.NOTE] Para obtener más detalles, vea [Introducción iniciado con nube aplicación descubrimiento](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)

<br>
Todos los usuarios acceso a los datos en el portal, debe tener una licencia con una licencia de Azure AD Premium.



##<a name="additional-resources"></a>Recursos adicionales


* [¿Cómo puedo descubrir aplicaciones no sancionada nube que se utilizan dentro de mi organización](active-directory-cloudappdiscovery-whatis.md)
* [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
