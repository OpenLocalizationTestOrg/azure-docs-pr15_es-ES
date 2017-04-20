<properties
    pageTitle="Proteger una aplicación de servicio de aplicaciones de Azure"
    description="Obtenga información sobre cómo proteger una aplicación web, una aplicación móvil back-end o API de aplicación de servicio de la aplicación de Azure."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="01/12/2016"
    ms.author="cephalin"/>


#<a name="secure-an-app-in-azure-app-service"></a>Proteger una aplicación de servicio de aplicaciones de Azure

Este artículo le ayudará a introducción sobre cómo proteger su aplicación web, una aplicación móvil back-end o API de aplicación de servicio de aplicaciones de Azure. 

Seguridad en el servicio de aplicación de Azure tiene dos niveles: 

- **Seguridad de infraestructura y plataforma** - confía en Azure para que los servicios que necesita para ejecutar realmente cosas de forma segura en la nube.
- **Seguridad de la aplicación** : debe diseñar la aplicación de forma segura. Esto incluye cómo integrar con Azure Active Directory, cómo administrar los certificados y cómo garantizar que puede hablar de forma segura a diversos servicios de. 

#### <a name="infrastructure-and-platform-security"></a>Seguridad de infraestructura y plataforma
Debido a que el servicio de aplicación mantiene las máquinas virtuales de Azure, almacenamiento, conexiones de red, marcos de web, administración y características de integración y mucho más, es seguro y consolidado conocidas y atraviesa la exhaustiva cumplimiento y comprueba de forma continua para asegurarse de que:

- Las aplicaciones de servicio de aplicaciones están aisladas de ambos Internet y de recursos de los demás clientes Azure.
- Comunicación de información confidencial (por ejemplo, las cadenas de conexión) entre la aplicación de servicio de aplicaciones y otros recursos de Azure (por ejemplo, base de datos de SQL) en un grupo de recursos permanece en Azure y no entre los límites de la red. Siempre se cifra información confidencial.
- Todas las comunicaciones entre la aplicación de servicio de aplicaciones y recursos externos, como la administración de PowerShell, interfaz de línea de comandos, SDK de Azure, las API de REST y conexiones híbrido, correctamente están cifrados.
- amenaza de 24 horas administración protege los recursos de servicio de aplicaciones de malware, distribuido denegación de servicio (DDoS), en-el-intermediario (MITM) y otras amenazas. 

Para obtener más información sobre la seguridad de infraestructura y plataforma de Azure, consulte [Centro de confianza de Azure](/support/trust-center/security/).

#### <a name="application-security"></a>Seguridad de la aplicación

Aunque es responsable de proteger la infraestructura de plataforma y que la aplicación se ejecuta en Azure, es su responsabilidad para proteger su propia aplicación. En otras palabras, debe desarrollar, implementar y administrar su contenido y el código de la aplicación de forma segura. Sin esto, el código de aplicación o el contenido puede quedar vulnerable a amenazas como:

- Inserción de SQL
- Secuestro de sesión
- Reutilización de scripts sitio
- Nivel de aplicación MITM
- Nivel de aplicación DDoS

Una discusión completa de consideraciones de seguridad para aplicaciones basadas en web está fuera del ámbito de este documento. Como punto de partida para obtener más instrucciones sobre cómo proteger su aplicación, consulte el [Proyecto de seguridad de aplicaciones de Web abrir (OWASP)](https://www.owasp.org/index.php/Main_Page), específicamente el [proyecto 10 mejores.](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), que se enumeran los errores de seguridad de aplicaciones de web crítica 10 superior actual, según lo determinado por los miembros OWASP.

## <a name="perform-penetration-testing-on-your-app"></a>Realizar pruebas en su aplicación de penetración

Una de las formas más fáciles de ha empezado a con las pruebas de vulnerabilidad en su aplicación de servicio de la aplicación es utilizar la [integración con la seguridad de estaño](/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) para realizar análisis en la aplicación de vulnerabilidad de un solo clic. Puede ver los resultados en un informe de fácil de entender y obtenga información sobre cómo corregir cada vulnerabilidad con instrucciones paso a paso.

Si prefiere realizar sus propias pruebas de penetración o desea utilizar otro conjunto de escáner o proveedor, debe seguir el [proceso de aprobación de pruebas de penetración de Azure](https://security-forms.azure.com/penetration-testing/terms) y obtener autorización para realizar las pruebas de penetración deseada.

##<a name="https"></a>Comunicación segura con los clientes

Si utiliza la ** \*. azurewebsites.net** nombre de dominio que se creó para la aplicación de servicio de aplicaciones, puede usar inmediatamente HTTPS, como se proporciona un certificado SSL para todos los ** \*. azurewebsites.net** nombres de dominio. Si su sitio usa un [nombre de dominio personalizado](web-sites-custom-domain-name.md), puede cargar un certificado SSL para [Habilitar HTTPS](web-sites-configure-ssl-certificate.md) del dominio personalizado.

Habilitar [HTTPS](https://en.wikipedia.org/wiki/HTTPS) puede ayudarle a protegerse de ataques MITM en la comunicación entre la aplicación y sus usuarios.

## <a name="secure-data-tier"></a>Nivel de datos seguro

Aplicación de servicio altamente se integra con base de datos de SQL, que todas las cadenas de conexión se cifran a través de los paneles y solo se descifran en la máquina virtual de la aplicación se ejecuta en *y* solo cuando se ejecuta la aplicación. Además, la base de datos de SQL Azure incluye muchas características de seguridad para ayudar a proteger los datos de la aplicación de las amenazas, incluidos [cifrado al resto](https://msdn.microsoft.com/library/dn948096.aspx), [Siempre cifrados](https://msdn.microsoft.com/library/mt163865.aspx), [Masking dinámico de datos](../sql-database/sql-database-dynamic-data-masking-get-started.md)y [Detección de amenazas](../sql-database/sql-database-threat-detection-get-started.md). Si tiene datos confidenciales o requisitos de cumplimiento, vea [proteger la base de datos de SQL](../sql-database/sql-database-security.md) para obtener más información sobre cómo proteger los datos.

Si usa un proveedor de base de datos de terceros, como ClearDB, debe consultar con la documentación del proveedor directamente en las mejores prácticas de seguridad.  

##<a name="develop"></a>Implementación y desarrollo seguro

### <a name="publishing-profiles-and-publish-settings"></a>Los perfiles de publicación y configuración de publicación

Desarrollo de aplicaciones, realizar tareas de administración o automatizar tareas con herramientas como **Visual Studio**, **Web Matrix**, **Azure PowerShell** o la **Azure de línea de comandos interfaz Azure**, puede usar un archivo de *configuración de publicación* o un *perfil de publicación*. Ambos tipos de archivo autentican con Azure y deben protegerse para evitar el acceso no autorizado.

* Contiene un archivo de **configuración de publicación**

    * El identificador de suscripción de Azure

    * Certificado de administración que le permite realizar tareas de administración para su suscripción *sin tener que proporcionar un nombre de cuenta o contraseña*.

* Contiene un archivo de **perfil de publicación**

    * Información para la publicación en la aplicación

Si usa una utilidad que utiliza un archivo de configuración de publicación o el archivo de perfil de publicación, importar el archivo que contiene la configuración de publicación o el perfil en la herramienta y, a continuación, **elimine** el archivo. Si debe mantener el archivo para compartir con otras personas trabajan en el proyecto, por ejemplo, almacenar en una ubicación segura como un directorio *cifrado* con permisos restringidos.

Además, debe asegurarse de que se protegen las credenciales importadas. Por ejemplo, **Azure PowerShell** e de **Azure de línea de comandos interfaz Azure** almacenan la información importada en su **directorio de inicio** (*~* en */users/yourusername* en los sistemas de Windows y sistemas de Linux o OS X.) Para obtener seguridad adicional, puede que desee **cifrar** estas ubicaciones con herramientas de cifrado disponibles para su sistema operativo.

### <a name="configuration-settings-and-connection-strings"></a>Opciones de configuración y las cadenas de conexión
Es muy común para almacenar cadenas de conexión, las credenciales de autenticación y otra información confidencial en archivos de configuración. Desgraciadamente, estos archivos pueden ser expuestos en el sitio Web o registre en un repositorio público, exponer esta información. Una búsqueda simple en [GitHub](https://github.com), por ejemplo, puede descubrir innumerables archivos de configuración con información confidencial expuesto en los repositorios públicos.

Lo mejor es mantener esta información en archivos de configuración de la aplicación. Aplicación de servicio le permite almacenar la información de configuración como parte del entorno de tiempo de ejecución como **cadenas de conexión**y la **configuración de la aplicación** . Los valores se exponen a la aplicación en tiempo de ejecución mediante *las variables de entorno* para la mayoría de lenguajes de programación. Para aplicaciones. NET, estos valores se insertan en la configuración de .NET en tiempo de ejecución. Aparte de estas situaciones, estos valores de configuración permanecen cifrados a menos que ve o configurarlos mediante el [Portal de Azure](https://portal.azure.com) o utilidades como PowerShell o CLI Azure. 

Almacenar información de configuración de aplicación de servicio hace posible para el Administrador de la aplicación bloquear información confidencial para las aplicaciones de producción. Los desarrolladores pueden utilizar un conjunto diferente de valores de configuración para el desarrollo de aplicaciones y la configuración puede reemplazar automáticamente con la configuración de la aplicación de servicio. Ni siquiera los desarrolladores deben saber la información confidencial configurado para la aplicación de producción. Para obtener más información sobre cómo configurar cadenas de conexión y la configuración de la aplicación de servicio de aplicaciones, vea [Configurar web apps](web-sites-configure.md).

### <a name="ftps"></a>FTPS

Servicio de aplicaciones de Azure proporciona acceso seguro de FTP al sistema de archivos de la aplicación a través de **FTPS**. Esto le permite obtener acceso seguro al código de la aplicación en la aplicación web, así como los diagnósticos de registros. Se recomienda usar siempre FTPS en lugar de FTP. 

El vínculo FTPS para su aplicación puede encontrarse con los siguientes pasos:

1. Abra el [Portal de Azure](https://portal.azure.com).
2. Seleccione **Examinar todos**.
3. En el módulo **Examinar** , seleccione **Servicios de aplicación**.
4. En el módulo de **Servicios de aplicación** , seleccione la aplicación que desee.
5. En el módulo de la aplicación, seleccione **toda la configuración**.
6. En el módulo de **configuración** , seleccione **Propiedades**.
7. Los vínculos FTP y FTPS se proporcionan en el módulo de la **configuración** . 

Para obtener más información sobre FTPS, vea [Protocolo de transferencia de archivos](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre la seguridad de la plataforma Windows Azure, información en informes de un **incidente de seguridad o abuso**, o para informar a Microsoft que se va a realizar **pruebas de penetración** de su sitio, consulte la sección seguridad del [Centro de confianza de Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

Para obtener más información sobre los archivos **web.config** o **applicationhost.config** en las aplicaciones de servicio de aplicaciones, consulte [Opciones de configuración desbloqueados en aplicaciones web de servicio de la aplicación de Azure](https://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/).

Para obtener información sobre la información de registro para las aplicaciones de servicio de aplicación, que puede ser útil para detectar ataques, vea [Habilitar el registro de diagnóstico](web-sites-enable-diagnostic-log.md).

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación de corta duración starter en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="whats-changed"></a>¿Qué ha cambiado

* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
