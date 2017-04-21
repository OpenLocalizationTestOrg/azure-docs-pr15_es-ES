<properties 
    pageTitle="CENC con múltiples DRM y Control de acceso: un diseño de referencia e implementación de Azure y Azure Media Services | Microsoft Azure" 
    description="Obtenga más información sobre cómo Microsoft® Smooth Streaming cliente trasladar Kit de licencias." 
    services="media-services" 
    documentationCenter="" 
    authors="willzhan"  
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"  
    ms.author="willzhan;kilroyh;yanmf;juliako"/>

#<a name="cenc-with-multi-drm-and-access-control-a-reference-design-and-implementation-on-azure-and-azure-media-services"></a>CENC con múltiples DRM y Control de acceso: un diseño de referencia e implementación de Azure y Azure Media Services

##<a name="key-words"></a>Palabras clave
 
Entrega de Azure Active Directory, Azure Media Services, Azure Media Player, cifrado dinámico, licencia, PlayReady, Widevine, FairPlay, Encryption(CENC) comunes, DRM entre varios, Axinom, guión, EME, MSE, JSON Web Token (JWT), notificaciones, exploradores modernos, conversión de clave, clave simétrica, clave asimétricos, OpenID conectarse, X509 certificado. 

##<a name="in-this-article"></a>En este artículo

En este artículo se tratan los siguientes temas:

- [Introducción](media-services-cenc-with-multidrm-access-control.md#introduction)
    - [Información general de este artículo](media-services-cenc-with-multidrm-access-control.md#overview-of-this-article)
- [Un diseño de referencia](media-services-cenc-with-multidrm-access-control.md#a-reference-design)
- [Diseño de la asignación a la tecnología de implementación](media-services-cenc-with-multidrm-access-control.md#mapping-design-to-technology-for-implementation)
- [Implementación](media-services-cenc-with-multidrm-access-control.md#implementation)
    - [Procedimientos de implementación](media-services-cenc-with-multidrm-access-control.md#implementation-procedures)
    - [Algunas trampas en implementación](media-services-cenc-with-multidrm-access-control.md#some-gotchas-in-implementation)
- [Temas adicionales para la implementación](media-services-cenc-with-multidrm-access-control.md#additional-topics-for-implementation)
    - [HTTP o HTTPS](media-services-cenc-with-multidrm-access-control.md#http-or-https)
    - [Azure Active Directory sustitución de la clave de firma](media-services-cenc-with-multidrm-access-control.md#azure-active-directory-signing-key-rollover)
    - [¿Dónde está el Token de acceso?](media-services-cenc-with-multidrm-access-control.md#where-is-the-access-token)
    - [¿Qué hay de Live Streaming?](media-services-cenc-with-multidrm-access-control.md#what-about-live-streaming)
    - [¿Qué información acerca de los servidores de licencia fuera de Azure Media Services?](media-services-cenc-with-multidrm-access-control.md#what-about-license-servers-outside-of-azure-media-services)
    - [¿Qué pasa si deseo usar a un STS personalizado?](media-services-cenc-with-multidrm-access-control.md#what-if-i-want-to-use-a-custom-sts)
- [El sistema completado y la prueba.](media-services-cenc-with-multidrm-access-control.md#the-completed-system-and-test)
    - [Inicio de sesión de usuario](media-services-cenc-with-multidrm-access-control.md#user-login)
    - [Con las extensiones de medios cifrado para PlayReady](media-services-cenc-with-multidrm-access-control.md#using-encrypted-media-extensipons-for-playready)
    - [Usar EME para Widevine](media-services-cenc-with-multidrm-access-control.md#using-eme-for-widevine)
    - [Usuarios no tiene derecho](media-services-cenc-with-multidrm-access-control.md#not-entitled-users)
    - [Ejecutando el servicio de Token seguro personalizado](media-services-cenc-with-multidrm-access-control.md#running-custom-secure-token-service)
- [Resumen](media-services-cenc-with-multidrm-access-control.md#summary)

##<a name="introduction"></a>Introducción

Es bien conocido que es una tarea compleja para diseñar y crear un subsistema DRM para un OTT o online transmisión solución. Y es una práctica común para proveedores de vídeo en operadores y en línea delegar este elemento para proveedores de servicios DRM especializados. El objetivo de este documento es presentar un diseño de referencia e implementación de subsistema DRM de llevar a cabo en OTT o solución streaming en línea.

Los lectores de destinados de este documento son ingenieros que trabajan en el subsistema DRM de OTT o soluciones en línea transmisión/múltiples-screen o cualquier lectores interesados en el subsistema de DRM. Se supone que los lectores están familiarizados con al menos una de las tecnologías DRM en el mercado, como PlayReady, Widevine, FairPlay o acceso de Adobe.

Por DRM, también incluimos CENC (cifrado comunes) con múltiples DRM. Una línea de tendencia principal en transmisión en línea y sector OTT es usar CENC con múltiples-native-DRM en varias plataformas de cliente, que es un cambio desde la tendencia anterior del uso de una sola DRM y el SDK de cliente para varias plataformas de cliente. Cuando usa CENC con múltiples native DRM, PlayReady y Widevine se cifran por la especificación de [Cifrado comunes (ISO/IEC 23001 7 CENC)](http://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) .

Las ventajas de CENC con múltiples DRM son los siguientes:

1. Reduce el cifrado de costo como un proceso de cifrado solo se utiliza destinado a plataformas diferentes con sus DRMs nativas;
1. Reduce el costo de administrar activos de cifrado, dado que se necesita una única copia de los activos cifrados;
1. Elimina la licencia costo desde el cliente DRM nativo es normalmente gratuito en su plataforma nativo de cliente DRM.

Microsoft ha creado un promotor activo de guión y CENC junto con algunos reproductores principales de la industria. Soporte técnico de guión y CENC ha proporcionado Microsoft Azure Media Services. Para anuncios recientes, consulte los blogs de Mingfei: [Servicios de entrega de anuncio de Google Widevine licencia en Azure Media Services](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)y [Azure Media Services agrega embalaje Widevine de Google para proporcionar múltiples DRM secuencia](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).  

### <a name="overview-of-this-article"></a>Información general de este artículo

El objetivo de este artículo incluye lo siguiente:

1. Proporciona un diseño de referencia de subsistema DRM con CENC multi-DRM;
1. Proporciona una implementación de referencia en la plataforma de Microsoft Azure y Azure Media Services;
1. Describe algunos temas de diseño e implementación.

En el artículo "multi-DRM" trata lo siguiente:

1. Microsoft PlayReady
1. Google Widevine
1. FairPlay de Apple (aún no admite Azure Media Services)

La siguiente tabla resume la aplicación nativa y plataforma nativa y exploradores compatibles con cada DRM.

**Plataforma de cliente**|**Soporte técnico DRM nativo**|**Explorador/aplicación**|**Formatos de streaming**
----|------|----|----
**TV inteligente, el operador STB, OTT STB**|PlayReady principalmente, o Widevine, y otras|Linux, Opera, WebKit, otros|Varios formatos
**Dispositivos de Windows 10 (equipos con Windows, tabletas de Windows, Windows Phone, Xbox)**|PlayReady|Borde, IE11/EME de MS<br/><br/><br/>UWP|GUIÓN (no se admite para HLS, PlayReady)<br/><br/>GUIÓN, Smooth Streaming (no se admite para HLS, PlayReady) 
**Dispositivos Android (teléfono, tableta, TV)**|Widevine|Chrome/EME|GUIÓN
**iOS (iPhone, iPad), los clientes de OS X y Apple TV**|FairPlay|Safari 8 + / EME|HLS
**Complemento: Adobe Primetime**|Access Primetime|Complemento del explorador|HDS, HLS

Teniendo en cuenta el estado actual de implementación para cada DRM, un servicio normalmente desea implementar DRMs 2 o 3 para asegurarse de que todos los tipos de extremos de direcciones de la mejor manera.

Hay un equilibrio entre la complejidad de la lógica de servicio y la complejidad del cliente para alcanzar un determinado nivel de la experiencia del usuario en los distintos clientes.

Para hacer que la selección, tenga en cuenta estos hechos:

- PlayReady nativa se implementa en cada dispositivo de Windows, en algunos dispositivos Android y están disponibles en el SDK de software en prácticamente cualquier plataforma
- Widevine nativa se implementa en todos los dispositivos Android, en Chrome y en algunos otros dispositivos
- FairPlay está disponible únicamente en iOS y clientes de Mac OS o a través de iTunes.

Por lo que un DRM multi típico sería:

- Opción 1: PlayReady y Widevine
- Opción 2: PlayReady, Widevine y FairPlay


## <a name="a-reference-design"></a>Un diseño de referencia

En esta sección, presentamos un diseño de referencia que es independiente del tecnologías usadas para implementarlo.

Un subsistema DRM puede contener los siguientes componentes:

1. Administración de claves
1. Embalaje DRM
1. Entrega de licencia DRM
1. Comprobación de derecho
1. Autenticación/autorización
1. Reproductor
1. Origen/CDN

El diagrama siguiente muestra la interacción entre los componentes de un subsistema DRM alto nivel.

![Subsistema DRM con CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)
 
Hay tres básicas "capas" en el diseño:

1. Capa de oficina (en negro) que no se exponen externamente;
1. Capa de "DMZ" (azul) que contiene todos los extremos opuestas público;
1. Público capa de Internet (azul claro) que contenga CDN y reproductores con el tráfico a través de Internet público.
 
Debe haber una herramienta de administración de contenido para controlar la protección DRM, independientemente cifrado estático o dinámico. Las entradas para el cifrado de DRM deben incluir:

1. Contenido de vídeo de MBR;
1. Clave de contenido;
1. Direcciones URL de adquisición de licencia.

Durante la reproducción, el flujo de alto nivel es:

1. Usuario se autentica;
1. Se crea el token de autorización para el usuario;
1. Contenido protegido con DRM (manifiesto) se descarga en el Reproductor.
1. Reproductor envía la solicitud de adquisición de licencias para servidores de licencia junto con la clave ID y la autorización de token.

Antes de pasar al siguiente tema, unas palabras sobre el diseño de administración de claves.

**ContentKey – a activo**|**Escenario**
------|---------------------------
1-1|El caso más sencillo. Proporciona el control mejor. Pero, en general, el resultado el mayor costo de entrega de licencia. En una licencia de mínima solicitud se requiere para cada activo protegido.
1 a varios|Puede utilizar la misma clave de contenido para varios activos. Por ejemplo, todos los activos en un grupo lógico como un género o un subconjunto de género (o génica de película), puede usar una clave de contenido único.
Varios-1|Varias teclas contenidas son necesarias para cada activo. <br/><br/>Por ejemplo, si necesita aplicar protección CENC dinámica con múltiples DRM para MPEG-guión y cifrado AES de 128 dinámico para HLS, necesita dos claves de contenido separadas, cada una con su propio ContentKeyType. (Para la clave de contenido que se utiliza para la protección de CENC dinámico, debe ser ContentKeyType.CommonEncryption usado, mientras que para la clave de contenido utilizada para el cifrado AES de 128 dinámico, debe utilizar ContentKeyType.EnvelopeEncryption.)<br/><br/>Otro ejemplo de protección de CENC del contenido de guión, en teoría, una clave de contenido puede usarse para proteger la secuencia de vídeo y otro contenido para proteger la secuencia de audio. 
Muchos-a - muchos|Combinación de los dos escenarios anteriores: un conjunto de claves de contenido se usan para cada uno de los varios activos en el mismo activo "grupo".


Otro factor importante para tener en cuenta es el uso de licencias persistentes y no persistentes.

¿Por qué son importantes estas consideraciones? 

Tienen impacto directo a costo de entrega de licencia si usa nube pública para el envío de licencia. Vamos a tener en cuenta los siguientes dos casos de diseño diferente para ilustrar:



1. Suscripción mensual: usar licencia persistente y la asignación de activos de clave de contenido de 1 a muchos. Por ejemplo para todas las películas niños, usamos una única clave de contenido para el cifrado. En este caso: 

    Total de licencias # solicitados para todos los niños películas o dispositivo = 1

1. Suscripción mensual: usar licencia no persistente y asignación 1 a 1 entre activos y clave de contenido. En este caso:

    Total de licencias # solicitados para todos los niños películas o dispositivo = [películas de # observadas] x [sesiones #]

Como puede ver fácilmente los dos diseños diferentes resultado tramas de solicitud de licencia muy diferente, por tanto, si el servicio de entrega de licencia se proporciona por una nube pública como Azure Media Services de costo de entrega de licencia.

## <a name="mapping-design-to-technology-for-implementation"></a>Diseño de la asignación a la tecnología de implementación

A continuación, se asigna nuestro diseño genérico para tecnologías de la plataforma de Microsoft Azure y Azure Media Services especificando la tecnología que utilizará para cada bloque de creación.

La siguiente tabla muestra la asignación:

**Bloque de creación**|**Tecnología**
------|-------
**Reproductor**|[Reproductor multimedia de Azure](https://azure.microsoft.com/services/media-services/media-player/)
**Proveedor de identidades (IDP)**|Azure Active Directory
**Servicio de Token seguro (STS)**|Azure Active Directory
**Flujo de trabajo de protección de DRM**|Protección dinámica de servicios multimedia de Azure
**Entrega de licencia DRM**|1. azure Media Services entrega licencia (PlayReady, Widevine, FairPlay), o <br/>2. servidor de licencias de Axinom <br/>3. servidor de licencias PlayReady personalizado
**Origen**|Extremo transmisiones de servicios multimedia de Azure
**Administración de claves**|No es necesario para la implementación de referencia
**Administración de contenido**|Una aplicación de consola de C#

En otras palabras, proveedor de identidades (IDP) y servicio de Token seguro (STS) estarán Azure AD. Para Reproductor, usaremos [API de Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). Azure Media Services y Azure Media Player admiten guión y CENC con múltiples DRM.

El diagrama siguiente muestra la estructura y el flujo de la asignación anterior de tecnología general.

![CENC en AMS](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Para configurar el cifrado de CENC dinámico, la herramienta de administración de contenido usan las entradas siguientes:

1. Abrir contenido;
1. Clave de contenido de la clave de generación y administración;
1. Direcciones URL de adquisición de licencia;
1. Una lista de información de Azure AD.

El resultado de la herramienta de administración de contenido será:

1. ContentKeyAuthorizationPolicy que contiene la especificación de cómo entrega licencia comprueba un token JWT y especificaciones de licencia DRM;
1. AssetDeliveryPolicy que contiene las especificaciones de transmisión de formato, la protección DRM y direcciones URL de adquisición de licencia.

En tiempo de ejecución, el flujo es como a continuación:

1. Tras la autenticación de usuario, se generará un token JWT;
1. Una de las notificaciones de token JWT es reclamación "grupos" que contiene el identificador de objeto de grupo de "EntitledUserGroup". Esta notificación se usará para pasar "verificación del derecho".
1. Información de cliente de descargas de Reproductor de un CENC contenido protegido y "ve" lo siguiente:
    1. Id., clave 
    1. el contenido está protegido, de CENC
    1. Direcciones URL de adquisición de licencia.

1. El Reproductor realiza una solicitud de adquisición de licencia basada en el explorador o DRM compatible. Id. de clave en la convocatoria de adquisición de licencia, y también se enviará el token JWT. Servicio de entrega de licencia comprobar el token JWT y las notificaciones de contenido antes de emitir la licencia necesaria.

##<a name="implementation"></a>Implementación

###<a name="implementation-procedures"></a>Procedimientos de implementación

La implementación incluye los pasos siguientes:

1. Preparar los activos de prueba: codificar/paquete un vídeo de prueba de velocidad de bits múltiple fragmentar MP4 en Azure Media Services. Este activo no está protegido con DRM. Protección DRM realizarla protección dinámica más adelante.
1. Crear clave ID y contenido clave (opcionalmente de inicialización clave). Para nuestro propósito, no es necesario el sistema de administración de claves ya que estamos trabajando con un solo conjunto de clave ID y clave de contenido para un par de activos de prueba;
1. Use AMS API para configurar servicios de entrega de licencia DRM múltiples para los activos de prueba. Si está utilizando servidores de licencia personalizada por proveedores de su compañía en lugar de los servicios de licencias en Azure Media Services o de su organización, puede omitir este paso y especificar direcciones URL de adquisición de licencia en el paso para configurar la entrega de licencia. API de AMS es necesaria para especificar que algo detallado configuraciones, como la restricción de directiva de autorización, las plantillas de respuesta de licencia de servicios de licencias DRM diferentes, etcetera. En este momento, el portal de Azure no aún proporciona la interfaz de usuario necesario para esta configuración. Puede buscar información de nivel de API y código en el documento de Julia Kornich de ejemplo: [usar PlayReady o cifrado dinámico comunes de Widevine](media-services-protect-with-drm.md). 
1. Use AMS API para configurar la directiva de entrega de activos para el activo de prueba. Puede buscar información de nivel de API y código en el documento de Julia Kornich de ejemplo: [usar PlayReady o cifrado dinámico comunes de Widevine](media-services-protect-with-drm.md).
1. Crear y configurar a un inquilino de Azure Active Directory en Azure;
1. Crear algunas cuentas de usuario y grupos en el inquilino de Azure Active Directory: debe crear al menos "EntitledUser" agrupar y agregar un usuario a este grupo. Usuarios de este grupo pasan derecho de la adquisición de licencias y los usuarios de este grupo no se pasa la comprobación de autenticación y no podrá adquirir ninguna licencia. Ser un miembro de este grupo de "EntitledUser" es una notificación de "grupos" requeridos en el token JWT emitido por Azure AD. Este requisito reclamación se debe especificar en la configuración de paso de servicios de entrega de licencia DRM entre varios.
1. Crear una aplicación de ASP.NET MVC que va a alojar el Reproductor de vídeo. Esta aplicación ASP.NET estarán protegida con autenticación de usuario en el inquilino de Azure Active Directory. NOMPROPIO reclamaciones se incluirán en los identificadores de acceso que se obtienen después de la autenticación de usuario. Se recomienda OpenID conectar API para este paso. Debe instalar los paquetes de NuGet siguientes:
    - Paquete de instalación Microsoft.Azure.ActiveDirectory.GraphClient
    - Paquete de instalación Microsoft.Owin.Security.OpenIdConnect
    - Paquete de instalación Microsoft.Owin.Security.Cookies
    - Paquete de instalación Microsoft.Owin.Host.SystemWeb
    - Paquete de instalación Microsoft.IdentityModel.Clients.ActiveDirectory
1. Crear un reproductor con la [API de Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/). [API de ProtectionInfo del Reproductor de Azure](http://amp.azure.net/libs/amp/latest/docs/) le permite especificar qué tecnología DRM para usar en la plataforma DRM diferente.
1. Matriz de pruebas:

**DRM**|**Explorador**|**Resultado para el usuario tiene derecho**|**Resultado para el usuario no tiene derecho**
---|---|-----|---------
**PlayReady**|Borde de MS o IE11 en Windows 10|Realice correctamente|Un error
**Widevine**|Chrome en Windows 10|Realice correctamente|Un error
**FairPlay** |POR DETERMINAR||

George Trifonov del equipo de Azure Media Services ha escrito un blog que proporciona los pasos detallados en configuración de Azure Active Directory para una aplicación de Reproductor de ASP.NET MVC: [integrar Azure Media Services OWIN MVC según la aplicación con Azure Active Directory y restringir el contenido entrega clave basada en notificaciones JWT](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

George también ha escrito un blog sobre la [autenticación de token JWT en Azure Media Services y cifrado dinámico](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/). Y aquí está su [muestra en la integración de Azure AD con entrega clave de Azure Media Services](https://github.com/AzureMediaServicesSamples/Key-delivery-with-AAD-integration/).

Para obtener información acerca de Azure Active Directory:

- Puede encontrar información para desarrolladores de [Azure Active Directory del manual del programador](../active-directory/active-directory-developers-guide.md).
- Puede encontrar información del administrador en [Administrar su directorio de Azure AD](../active-directory/active-directory-administer.md).

### <a name="some-gotchas-in-implementation"></a>Algunas trampas en implementación

Hay algunos "trampas" en la implementación. Afortunadamente la siguiente lista de "trampas" puede ayudar a solucionar problemas en caso de se produzcan problemas.

1. **Emisor** Dirección URL debe finalizar con **"/"**.  

    **Audiencia** debe ser el identificador de cliente de la aplicación de Reproductor y también deberá agregar **"/"** al final de la dirección URL del emisor.

        <add key="ida:audience" value="[Application Client ID GUID]" />
        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" /> 

    En [JWT descodificador](http://jwt.calebb.net/), debería ver **y** y **iss** como debajo del token JWT:
    
    ![dificultad 1ª](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)


2. Agregar permisos a la aplicación en AAD (en la ficha configurar de la aplicación). Esto es necesario para cada aplicación (versiones locales e implementadas).

    ![2 º dificultad](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)


3. Use el emisor correcto en la configuración de protección de CENC dinámica:

        <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
    
    Siguiente no funcionará:
    
        <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />
    
    El GUID es el identificador del inquilino AAD. Puede encontrar el GUID en el menú emergente extremos en el portal de Azure.

4. Notificaciones de pertenencia conceder privilegios. Asegúrese de que en el archivo de la aplicación de AAD manifiesto, tenemos siguiente

    "groupMembershipClaims": "Todos" (el valor predeterminado es nulo)

5. Configuración TokenType en adecuada al crear los requisitos de la restricción.

        objTokenRestrictionTemplate.TokenType = TokenType.JWT;

    Desde la adición de soporte técnico de JWT (AAD) además de SWT (ACS), el valor predeterminado TokenType en es TokenType.JWT. Si usa SWT/ACS, debe establecer a TokenType.SWT.

## <a name="additional-topics-for-implementation"></a>Temas adicionales para la implementación
A continuación se le de disco algunos temas adicionales en el diseño y la implementación uss.

###<a name="http-or-https"></a>¿HTTP o HTTPS?

La aplicación de reproducción de ASP.NET MVC creamos debe admitir lo siguiente:

1. Autenticación de usuario a través de Azure AD que debe estar en HTTPS;
1. Intercambio de token de JWT entre cliente y Azure AD que debe estar en HTTPS;
1. Adquisición de licencia DRM por el cliente que es necesario estar en HTTPS si Azure Media Services proporciona entrega de licencia. Por supuesto, conjunto de productos de PlayReady exige HTTPS para el envío de licencia. Si su servidor de licencias PlayReady está fuera de Azure Media Services, se podrían usar HTTP o HTTPS.

Por lo tanto, la aplicación de reproducción ASP.NET usará HTTPS como práctica recomendada. Esto significa que el Reproductor multimedia de Azure estará en una página en HTTPS. Sin embargo, para la transmisión se prefiere HTTP, por lo tanto, debemos tener en cuenta el problema de contenido mixto.

1. Explorador no permite el contenido mixto. Pero permiten complementos como complemento de Silverlight y OSMF para suave y guión. Contenido mixto es un problema de seguridad, esto es debido a la amenaza de la capacidad de insertar JS malintencionado que puede provocar que los datos del cliente en riesgo.  Exploradores bloquearlo de forma predeterminada y hasta ahora es la única manera de solucionar el problema en el servidor (origen), para permitir que todos los dominios (con independencia https o http). Esto probablemente no es una buena idea bien.
1. Debemos evitar contenido mixto: ambos usan HTTP o ambos utilizan HTTPS. Cuando se reproduce contenido mixto, silverlightSS tecnológica requiere desactivando una advertencia de contenido mixta. flashSS técnico trata el contenido mixto sin advertencia contenido mixto.
1. Si se creó el extremo de transmisión antes de agosto de 2014, no admite HTTPS. En este caso, cree y utilice un nuevo punto final streaming para HTTPS.

En la implementación de referencia para el contenido protegido con DRM, la aplicación y la transmisión se encuentran en HTTTPS. Para abrir contenido, el Reproductor no necesita autenticación o licencia, por lo que tendrá la libertad para utilizar HTTP o HTTPS.

### <a name="azure-active-directory-signing-key-rollover"></a>Azure Active Directory sustitución de la clave de firma

Este es un punto importante para tener en cuenta de su implementación. Si no se considera en la implementación, el sistema completado finalmente dejan de funcionar completamente dentro de seis semanas como máximo.

Azure AD usa estándar para establecer la confianza entre sí y las aplicaciones con Azure AD. Más concretamente, Azure AD utiliza una clave de firma que consta de un par de claves público y privado. Cuando Azure AD crea un token de seguridad que contiene información sobre el usuario, este token está firmado por Azure AD con su clave privada antes de enviarlo a la aplicación. Para comprobar que el token es válida y realmente se originaron en Azure AD, la aplicación debe validar firma del token mediante la clave pública expuesta por Azure AD contenido en el documento de metadatos de federación del inquilino. Esta clave pública y la clave de firma del que se deriva, son el mismo que se usa para todos los inquilinos en Azure AD.

Encontrará información detallada en sustitución de Azure AD la clave en el documento: [Información importante sobre la conversión de clave de firma en Azure AD](../active-directory/active-directory-signing-key-rollover.md).

Entre el [par de clave pública y privada](https://login.windows.net/common/discovery/keys/), 

- La clave privada se utiliza por Azure Active Directory para generar un token JWT;
- La clave pública se usa en una aplicación como servicios de entrega de licencia DRM en AMS para verificar el token JWT;
 
Para el propósito de seguridad, Azure Active Directory gira este certificado periódicamente (cada semanas 6). En caso de problemas de seguridad, la sustitución de la clave se produce cada vez. Por lo tanto, los servicios de entrega de licencia en AMS necesitan actualizar la clave pública utilizada como Azure AD gira el par de claves, en caso contrario, se producirá un error de autenticación token en AMS y no se emite ninguna licencia. 

Esto se logra estableciendo TokenRestrictionTemplate.OpenIdConnectDiscoveryDocument al configurar servicios de entrega de licencia DRM.

El flujo de token de JWT es como a continuación:

1.  Azure AD va a emitir el token JWT con la clave privada actual para un usuario autenticado;
2.  Cuando un reproductor ve un CENC con contenido protegido con multi-DRM, primero buscará el token JWT emitido por Azure AD.
3.  El Reproductor envía la solicitud de adquisición de licencia con el token JWT a servicios de entrega de licencia en AMS;
4.  Los servicios de entrega de licencia en AMS utilizará la clave pública actual o válida de Azure AD para comprobar el token JWT, antes de emitir licencias.

Servicios de entrega de licencia DRM se pueden comprobar siempre la clave pública actual o válida de Azure AD. La clave pública realizada por Azure AD será la clave utilizada para comprobar un símbolo JWT emitido por Azure AD.

¿Qué ocurre si la sustitución de la clave pasa después de AAD genera un token JWT pero antes de la JWT token se envía por jugadores a los servicios de entrega de licencia DRM en AMS para su verificación? 

Porque una clave puede se acumulen en cualquier momento, siempre hay más de una clave pública válida disponibles en el documento de metadatos de federación. Entrega de licencia de Azure Media Services puede utilizar cualquiera de las teclas especificadas en el documento, ya que una tecla puede se acumulen pronto, otro puede ser su reemplazo y así sucesivamente.

### <a name="where-is-the-access-token"></a>¿Dónde está el Token de acceso?

Si mira cómo una aplicación web llama a una aplicación de la API en la [Identidad de la aplicación con concesión de credenciales de cliente de OAuth 2.0](active-directory-authentication-scenarios.md#web-application-to-web-api), el flujo de autenticación es como a continuación:

1.  Un usuario ha iniciado sesión en Azure AD en la aplicación web (consulte el [Explorador Web para la aplicación Web](active-directory-authentication-scenarios.md#web-browser-to-web-application).
2.  El extremo de autorización de Azure AD redirige al agente de usuario a la aplicación cliente con un código de autorización. El agente de usuario devuelve el código de autorización para redirigir el URI de la aplicación de cliente.
3.  La aplicación web necesita adquirir un token de acceso para que se puede autenticar a la web API y recuperar el recurso que desee. Realiza una solicitud al extremo de token de Azure AD, proporcionar la credencial, ID de cliente y aplicación web de la API de URI de ID. Presenta el código de autorización para demostrar que ha aceptado el usuario.
4.  Azure AD autentica la aplicación y devuelve un token de acceso JWT que se usa para llamar a la API de web.
5.  A través de HTTPS, la aplicación web usa el token de acceso JWT devuelto para agregar la cadena JWT con una designación de "Portador" en el encabezado de autorización de la solicitud a la API de web. La API de web, a continuación, valide el token JWT y si la validación es correcta, devuelve el recurso que desee.

En este flujo de "identidad de la aplicación" web API confía en que la aplicación web autenticado el usuario. Por este motivo, este modelo se denomina subsistema de confianza. El [diagrama en esta página](http://msdn.microsoft.com/library/azure/dn645542.aspx/) describe cómo el código de autorización conceder funciona de flujo.

Adquisición de licencia con restricción token, nos estamos siguiendo el mismo modelo de subsistema de confianza. Y el servicio de entrega de licencia en Azure Media Services API recurso, "back-end" una aplicación web necesita tener acceso a Internet. Así que ¿dónde está el token de acceso?

De hecho, nos estamos obtención de token de acceso de Azure AD. Después de la autenticación de usuario correcta, se devuelve el código de autorización. El código de autorización, a continuación, emplea, junto con la clave de ID y la aplicación de cliente, al token de acceso de exchange. Y el token de acceso para tener acceso a una aplicación de "puntero" que señala o que representa el servicio de entrega de licencia de Azure Media Services.

Se necesita registrar y configurar la aplicación "puntero" en Azure AD siguiendo los pasos siguientes:

1.  En el inquilino de Azure AD

    - Agregar una aplicación (recursos) con la dirección URL de inicio de sesión: 

    https://[resource_name].azurewebsites.NET/ y 

    - dirección URL de ID de aplicación: 
    
    https://[aad_tenant_name].onmicrosoft.com/[resource_name]; 
2.  Agregar una nueva clave para la aplicación de recursos;
3.  Actualizar el archivo de manifiestos de aplicación para que la propiedad groupMembershipClaims tiene el siguiente valor: "groupMembershipClaims": "Todos"  
4.  En la aplicación de Azure AD apuntando a la aplicación web de Reproductor, en la sección "permisos a otras aplicaciones", agregue la aplicación de recursos que se ha agregado en el paso 1 anterior. En "permisos de delegado" Active la marca de verificación "Acceso [resource_name]". Esto le da el permiso de la aplicación web para crear tokens de access para obtener acceso a la aplicación de recursos. Debe hacer esto para versión local e implementada de la aplicación web si desarrolla con la aplicación web de Visual Studio y Azure.
    
Por lo tanto, el token JWT emitido por Azure AD es el token de acceso para tener acceso a este recurso "puntero".

### <a name="what-about-live-streaming"></a>¿Qué hay de Live Streaming?

En anteriores, nuestro debate ha sido centrarse en activos a petición. ¿Qué hay de live transmisión?

La buena noticia es que puede usar exactamente el mismo diseño y la implementación para proteger la transmisión live en Azure Media Services, tratando activo asociado con un programa como "activo VOD".

Más concretamente, es bien conocido que para realizar directo transmisión en Azure Media Services, debe crear un canal y, a continuación, un programa en el canal. Para crear el programa, debe crear un activo que contendrá el archivo directo para el programa. Para proporcionar CENC protección múltiple DRM del contenido activo, todo lo que tiene que hacer, es aplicar la misma configuración/procesamiento activo como si fuera una "activos VOD" antes de iniciar el programa.

### <a name="what-about-license-servers-outside-of-azure-media-services"></a>¿Qué información acerca de los servidores de licencia fuera de Azure Media Services?

A menudo, los clientes que han invertido en granja de servidores de licencia en sus propios datos del centro u hospedado por proveedores de servicios DRM. Afortunadamente, protección de contenido de Azure Media Services le permite trabajar en modo híbrido: contenido hospedado y protegido dinámicamente en Azure Media Services, mientras se entregarán licencias DRM servidores fuera de Azure Media Services. En este caso, existen las consideraciones de los cambios siguientes:

1. El servicio de Token seguro debe emitir tokens que son aceptables y pueden comprobar la granja de servidores de licencia. Por ejemplo, los servidores de licencia Widevine proporciona Axinom requiere un token JWT específico que contiene "mensaje derecho". Por lo tanto, debe tener un STS a emitir como token JWT. Los autores han completado dicha implementación y puede encontrar los detalles en el documento siguiente en el [Centro de documentación de Azure](https://azure.microsoft.com/documentation/): [Usar Axinom para proporcionar licencias Widevine a Azure Media Services](media-services-axinom-integration.md). 
1. Ya no necesita configurar el servicio de entrega de licencia (ContentKeyAuthorizationPolicy) en Azure Media Services. Lo que debe hacer es proporcionar direcciones URL de adquisición de la licencia (para PlayReady, Widevine y FairPlay) al configurar AssetDeliveryPolicy en configurar CENC con múltiples DRM.
 
### <a name="what-if-i-want-to-use-a-custom-sts"></a>¿Qué pasa si deseo usar a un STS personalizado?

Puede haber varias razones por las que un cliente puede optar por usar a un STS personalizado (servicio Token seguro) para proporcionar tokens JWT. Algunos de ellos son:

1.  El proveedor de identidades (IDP) usado por el cliente no admite a STS. En este caso un STS personalizado puede ser una opción.
2.  El cliente pueda necesitar un control más flexible o mayor en la integración de STS con sistema de facturación de los suscriptores de cliente. Por ejemplo, puede ofrecer un operador MVPD varios paquetes de suscriptor OTT como premium, básico, deportes, etcetera. El operador es aconsejable para que coincida con las notificaciones en un token con paquete del suscriptor para que solo contenido en el paquete adecuado estará disponible. En este caso, un STS personalizado proporciona la flexibilidad necesaria y el control.

Dos cambios deben realizarse cuando se usa a un STS personalizado:

1.  Al configurar el servicio de entrega de licencia para un activo, debe especificar la clave de seguridad utilizada para la verificación por el STS personalizado (más detalles a continuación) en lugar de la clave actual de Azure Active Directory.
2.  Cuando se genera un token JTW, se especifica una clave de seguridad en lugar de la clave privada de la X509 actual certificado de Azure Active Directory.

Existen dos tipos de claves de seguridad:

1.  Clave simétrica: la misma clave se usa para generar y comprobar un símbolo JWT;
2.  Clave asimétrico: un par de claves públicas y privadas en un certificado se usa con una clave privada para cifrar o generar un token JWT y la clave pública para comprobar el símbolo de X509.

####<a name="tech-note"></a>Nota técnica

Si usa .NET Framework y C# como la plataforma de desarrollo, la X509 certificado usado para la clave de seguridad asimétricos debe tener al menos 2048 longitud de la clave. Esto es necesario de la clase System.IdentityModel.Tokens.X509AsymmetricSecurityKey en .NET Framework. En caso contrario, se producirá la siguiente excepción:

IDX10630: 'System.IdentityModel.Tokens.X509AsymmetricSecurityKey' para firmar no puede ser menor que '2048' bits. 

## <a name="the-completed-system-and-test"></a>El sistema completado y la prueba.

Guiaremos a través de algunos escenarios en el sistema de llevar a cabo completado para que los lectores puedan tener un basic "imagen" del comportamiento antes de obtener una cuenta de inicio de sesión.

La aplicación web de reproducción y su inicio de sesión se encuentran [aquí](https://openidconnectweb.azurewebsites.net/).

Si lo que necesita es "no integrada" escenario: recursos de vídeo hospedados en Azure Media Services que no están bien protegidas o DRM protegido pero sin autenticación token (emitir una licencia a quien solicite), puede probar sin inicio de sesión (cambiando a HTTP si la transmisión de video es a través de HTTP).

Si lo que necesita es integrada escenario: recursos de vídeo está bajo protección DRM dinámica en Azure Media Services, con la autenticación token y token JWT generando Azure AD, necesita iniciar sesión.

### <a name="user-login"></a>Inicio de sesión de usuario

Para probar el sistema DRM-to-end integrado, debe tener una "cuenta" creados o agregados. 

¿Qué cuenta?

Aunque Azure originalmente permite acceso a los usuarios de cuenta de Microsoft ahora permite el acceso a los usuarios de ambos sistemas. Esto se hizo teniendo todos la confianza de Azure propiedades Azure AD para la autenticación, tener Azure AD autenticar usuarios organizativos y creando una relación de federación donde Azure AD confía en el sistema de identidad de Microsoft cuenta consumidor autenticar a los usuarios de consumidores. Como resultado, Azure AD es pueden autenticar cuentas de Microsoft "Invitado", así como cuentas de Azure AD "nativas".

Dado que Azure AD confía en el dominio de la cuenta de Microsoft (MSA), puede agregar las cuentas de cualquiera de los siguientes dominios a Azure AD personalizado de inquilinos y usar la cuenta de inicio de sesión:

**Nombre de dominio**|**Dominio**
-----------|----------
**Dominio de inquilinos personalizado Azure AD**|somename.onmicrosoft.com
**Dominio corporativo**|Microsoft.com
**Dominio de la cuenta de Microsoft (MSA)**|Outlook.com, live.com, hotmail.com

Puede comunicarse con cualquiera de los autores que tiene una cuenta creados o agregados por usted. 

A continuación se muestran las capturas de pantalla de las páginas de inicio de sesión diferente utilizadas las cuentas de dominio diferente.

**Azure personalizado cuenta de dominio de AD inquilino**: en este caso, verá la página de inicio de sesión personalizado de personalizado dominio del inquilino de Azure AD.

![Cuenta de dominio personalizado Azure AD inquilino](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Cuenta de dominio de Microsoft con tarjeta inteligente**: en este caso verá la página de inicio de sesión personalizada corporativa de Microsoft TI con autenticación de dos factores.

![Cuenta de dominio personalizado Azure AD inquilino](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Cuenta de Microsoft (MSA)**: en este caso, verá la página de inicio de sesión de Microsoft Account para los consumidores.

![Cuenta de dominio personalizado Azure AD inquilinos](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="using-encrypted-media-extensions-for-playready"></a>Con las extensiones de medios cifrado para PlayReady

En un explorador moderno con cifrado multimedia extensiones (EME) para la compatibilidad de PlayReady, como 11 de Internet Explorer en Windows 8.1 y el explorador Microsoft Edge en Windows 10, PlayReady será DRM para EME subyacente.

![Usar EME para PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

El área de Reproductor oscuro es puesto que la protección de PlayReady impide que uno de realizar la captura de pantalla de vídeo protegida. 

En la siguiente pantalla muestra la compatibilidad MSE/EME y complementos del Reproductor.

![Usar EME para PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME en Microsoft Edge y 11 de Internet Explorer en Windows 10 permite invocar de [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) en dispositivos Windows 10 que lo admitan. PlayReady SL3000 desbloquea el flujo de contenido premium mejorado (4K, encabezado, etc.) y los modelos de entrega de contenido nuevo (ventana anticipado contenido mejorado).

Centrarse en los dispositivos de Windows: PlayReady es DRM solo en el hardware disponible en dispositivos de Windows (PlayReady SL3000). Un servicio de transmisión puede usar PlayReady a través de EME o una aplicación UWP y ofrecer una mejor calidad de vídeo con PlayReady SL3000 que otro DRM. Normalmente, el contenido de 2K fluirá a través de Chrome o Firefox y contenido de 4K a través de Microsoft borde/IE11 o una aplicación UWP en el mismo dispositivo (según la configuración del servicio e implementación).


#### <a name="using-eme-for-widevine"></a>Usar EME para Widevine

En un explorador moderno con soporte EME/Widevine, como Chrome 41 + en Windows 10, Windows 8.1, Mac OSX Yosemite y Chrome en 4.4.4 Android, Google Widevine es DRM detrás EME.

![Usar EME para Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Observe que Widevine no evita que uno de realizar la captura de pantalla de vídeo protegida.

![Usar EME para Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="not-entitled-users"></a>Usuarios no tiene derecho

Si un usuario no es un miembro del grupo "Usuarios titulada", el usuario no podrá pasar "verificación del derecho" y el servicio de licencias de DRM multi rechaza emitir la licencia solicitada tal como se muestra a continuación. Es una descripción detallada "adquirir licencias error", que es como diseñado.

![Usuarios sin derecho](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="running-custom-secure-token-service"></a>Ejecutando el servicio de Token seguro personalizado

En el caso de que se ejecuta personalizado Token servicio seguro (STS), el token JWT será emitido mediante el STS personalizado con teclas simétrica o asimétricos. 

Caso de uso de clave simétrica (con Chrome):

![Ejecutar a STS personalizado](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

El caso de uso de clave asimétrico a través de un X509 de certificados (con el explorador moderno de Microsoft).

![Ejecutar a STS personalizado](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

En ambos de los casos anteriores, autenticación de usuario sea la misma: a través de Azure AD. La única diferencia es que el STS personalizado en lugar de Azure AD emite tokens JWT. Por supuesto, al configurar protección CENC dinámica, la restricción del servicio de entrega de licencia especifica el tipo de token de JWT clave simétrica o asimétrico.

## <a name="summary"></a>Resumen

En este documento, hemos tratado CENC con múltiples native DRM y control de acceso a través de autenticación token: su diseño y su implementación con Azure, Azure Media Services y el Reproductor multimedia de Azure.

- Se presenta un diseño de referencia que contiene todos los componentes necesarios en un subsistema DRM/CENC;
- Una implementación de referencia en Azure, Azure Media Services y el Reproductor multimedia de Azure.
- También se describen algunos temas directamente implicados en el diseño e implementación.


##<a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

###<a name="acknowledgments"></a>Confirmaciones 

Guillermo Zhang, Mingfei Yan, Roland Le franco, Kilroy Portillo, Julia Kornich
