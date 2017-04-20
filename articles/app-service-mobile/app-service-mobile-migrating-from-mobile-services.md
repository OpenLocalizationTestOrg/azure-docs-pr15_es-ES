<properties
    pageTitle="Migrar de servicios móviles a una aplicación de servicio de la aplicación móvil"
    description="Obtenga información sobre cómo migrar fácilmente la aplicación de servicios móviles para una aplicación móvil de servicio de aplicación"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="adrianha"/>

# <a name="article-top"></a>Migrar el servicio móvil de Azure existente al servicio de la aplicación de Azure

Con la [disponibilidad general de la aplicación de servicio de Azure], sitios de servicios móviles de Azure pueden migrarse fácilmente en el lugar para aprovechar todas las características del servicio de aplicación de Azure.  Este documento explica lo que pasará al migrar su sitio de servicios móviles de Azure al servicio de la aplicación de Azure.

## <a name="what-does-migration-do"></a>¿Qué hace migración a su sitio

Migración de su servicio móvil de Azure activa el servicio móvil a una aplicación de [Servicio de la aplicación de Azure] sin que ello afecte el código.  Permanezcan igual su notificación Hubs, conexión de datos SQL, configuración de autenticación, las tareas programadas y nombre de dominio.  Los clientes móviles con el servicio móvil de Azure sigan funcionando normalmente.  Migración reinicia el servicio una vez que se transfiere al servicio de la aplicación de Azure.

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>¿Por qué debería migrar su sitio

Microsoft recomienda migrar su servicio móvil de Azure para aprovechar las ventajas de las características del servicio de aplicación Azure, incluidos:

  *  Nuevas características de host, incluidos [WebJobs] y los [nombres de dominio personalizado].
  *  Conectividad con los recursos locales con [VNet] además de [Conexiones híbrido].
  *  Supervisar y solucionar problemas con Relic nuevo o [Perspectivas de aplicación].
  *  Herramientas de DevOps integrados, incluidos [provisional ranuras], volver y pruebas de producción.
  *  [Ajustar]equilibrio de carga y [la supervisión de rendimiento].

Para obtener más información sobre las ventajas del servicio de aplicación de Azure, vea el tema de [Servicio móvil de servicios y aplicaciones] .

## <a name="before-you-begin"></a>Antes de empezar

Antes de empezar cualquier trabajo principal en su sitio, debe [realizar copias de seguridad el servicio móvil de] secuencias de comandos y base de datos SQL.

## <a name="migrating-site"></a>Migración de sus sitios

El proceso de migración migra todos los sitios dentro de una única región de Azure.

Para migrar su sitio:

  1.  Inicie sesión en el [Portal de clásico de Azure].
  2.  Seleccione un servicio de móvil en la región que desea migrar.
  3.  Haga clic en el botón **migrar a la aplicación de servicio** .

    ![El botón migrar][0]

  4.  Lea el migrar al cuadro de diálogo Servicios de aplicación.
  5.  Escriba el nombre de su servicio móvil en el cuadro proporcionado.  Por ejemplo, si el nombre de dominio es contoso.azure-Mobile.NET, escriba _contoso_ en el cuadro proporcionado.
  6.  Haga clic en el botón de graduación.

Supervisar el estado de la migración en el monitor de actividad. Su sitio se muestra como la *migración* en el Portal de clásico de Azure.

  ![Monitor de actividad de migración][1]

Cada migración puede tardar entre 3 y 15 minutos por cada servicio móvil que se migran.  El sitio sigue estando disponible durante la migración.
El sitio se reinicia al final del proceso de migración.  El sitio está disponible durante el proceso de reinicio, que puede durar unos segundos.

## <a name="finalizing-migration"></a>Finalización de la migración

Plan para probar su sitio de un cliente móvil al final del proceso de migración.  Asegúrese de que puede realizar todas las acciones comunes de cliente sin realizar cambios en el cliente móvil.  

### <a name="update-app-service-tier"></a>Seleccione un servicio de aplicación apropiado precios nivel

Tiene más flexibilidad para definir precios después de migrar al servicio de aplicación de Azure.

  1.  Inicie sesión en el [portal de Azure].
  2.  Seleccione **todos los recursos** o los **Servicios de aplicación** , a continuación, haga clic en el nombre de su servicio móvil migrados.
  3.  Se abre el módulo de la configuración predeterminada.
  4.  En el menú Configuración, haga clic en **Plan de servicio de aplicación** .
  5.  Haga clic en el mosaico de **Nivel de precios** .
  6.  Haga clic en el icono correspondiente a sus necesidades, haga clic en **Seleccionar**.  Puede que tenga que haga clic en **Ver todos** para ver los precios disponibles niveles.

Como punto de partida, recomendamos los siguientes niveles:

| Nivel de precios de servicio móvil | Nivel de precios de servicio de aplicaciones |
| :-------------------------- | :----------------------- |
| Liberar                        | F1 libre                  |
| Básico                       | B1 Basic                 |
| Estándar                    | S1 estándar              |

Existe una considerable flexibilidad para elegir el derecho de precios de nivel de la aplicación.  Para obtener más detalles sobre los precios de su nuevo servicio de aplicación, consulte [Precios de servicio de aplicación] .

> [AZURE.TIP]El nivel de aplicación servicio estándar contiene acceso a muchas características que desea usar, incluidos [ranuras de ensayo], copias de seguridad automáticas y escala automática.  Vea las nuevas capacidades mientras está allí.

### <a name="review-migration-scheduler-jobs"></a>Revisar los trabajos de programador migrados

Trabajos del programador no estará visibles hasta unos 30 minutos después de la migración.  Tareas programadas seguirán ejecutando en segundo plano.
Para ver los trabajos programados después de que están visibles de nuevo:

  1.  Inicie sesión en el [portal de Azure].
  2.  Seleccione **Examinar >**, escriba **programación** en el cuadro _filtro_ , y seleccione **Las colecciones de programador**.

Hay un número limitado de programador libre trabajos disponibles después de la migración.  Revise los [Planes de programador de Azure]y su uso.

### <a name="configure-cors"></a>Configurar CORS si es necesario

Compartir recursos entre origen es una técnica para permitir que un sitio Web obtener acceso a una API de Web en un dominio diferente.  Si está utilizando servicios de Azure Mobile con un sitio Web asociado, debe configurar CORS como parte de la migración.  Si tiene acceso a los servicios de Azure Mobile exclusivamente desde dispositivos móviles, CORS no es necesario configurar excepto en algunos casos.

La configuración de CORS migrada está disponible como la configuración de la aplicación **MS_CrossDomainWhitelist** .  Para migrar su sitio para la función CORS de servicio de aplicación:

  1.  Inicie sesión en el [portal de Azure].
  2.  Seleccione **todos los recursos** o los **Servicios de aplicación** , a continuación, haga clic en el nombre de su servicio móvil migrados.
  3.  Se abre el módulo de la configuración predeterminada.
  4.  Haga clic en **CORS** en el menú de la API.
  5.  Escriba los orígenes permitidos en el cuadro proporcionado, presionando ENTRAR después de cada uno.
  6.  Una vez que la lista de orígenes permitido es correcta, haga clic en el botón Guardar.

> [AZURE.TIP]Una de las ventajas de usar un servicio de aplicación de Azure es que puede ejecutar su sitio web y el servicio móvil en el mismo sitio.  Para obtener más información, vea la sección [pasos siguientes](#next-steps) .

### <a name="download-publish-profile"></a>Descargar un nuevo perfil de publicación

El perfil de publicación de su sitio Cambia al migrar a la aplicación de servicio de Azure.  Si desea publicar su sitio desde dentro de Visual Studio, es necesario un nuevo perfil de publicación.  Para descargar el nuevo perfil de publicación:

  1.  Inicie sesión en el [portal de Azure].
  2.  Seleccione **todos los recursos** o los **Servicios de aplicación** , a continuación, haga clic en el nombre de su servicio móvil migrados.
  3.  Haga clic en **obtener publicar perfil**.

El archivo PublishSettings se descargará en su equipo.  Normalmente se llama _nombre del sitio_. PublishSettings.  Importar la configuración de publicación a un proyecto existente:

  1.  Abra Visual Studio y el proyecto del servicio móvil de Azure.
  2.  Haga clic en el proyecto en el **Explorador de soluciones** y seleccione **publicar...**
  3.  Haga clic en **Importar**
  4.  Haga clic en **Examinar** y seleccione su descargado publicar el archivo de configuración.  Haga clic en **Aceptar**
  5.  Haga clic en **Validar conexión** para garantizar que el trabajo de configuración de publicación.
  6.  Haga clic en **Publicar** para publicar el sitio.


## <a name="working-with-your-site"></a>Trabajar con su posteriores a la migración de sitio

Empezar a trabajar con su nuevo servicio de aplicación en el [portal de Azure] posteriores a la migración.  Las siguientes son algunas notas en operaciones específicas que usa para realizar en el [Portal de Azure clásica], junto con su equivalente de servicio de aplicación.

### <a name="publishing-your-site"></a>Descargar y publicar su sitio migrado

El sitio está disponible mediante git o ftp y puede volver a publicar con diversos mecanismos diferentes, incluidos WebDeploy, TFS, mercuriano, GitHub y FTP.  Las credenciales de implementación se migran con el resto del sitio.  Si no ha definido sus credenciales de implementación o no los recuerda, puede restablecer:

  1. Inicie sesión en el [portal de Azure].
  2. Seleccione **todos los recursos** o los **Servicios de aplicación** , a continuación, haga clic en el nombre de su servicio móvil migrados.
  3. Se abre el módulo de la configuración predeterminada.
  4. Haga clic en **credenciales de implementación** de la publicación de menú.
  5. Escriba las credenciales de implementación de nuevo en los cuadros correspondientes y luego haga clic en el botón Guardar.

Puede usar estas credenciales para clonar el sitio con git o configurar implementaciones automatizadas de GitHub, TFS o mercuriano.  Para obtener más información, consulte la [documentación de implementación de servicio de aplicaciones de Azure].

### <a name="appsettings"></a>Configuración de la aplicación

Configuración de la mayoría de un servicio móvil migrado está disponibles a través de la configuración de la aplicación.  Puede obtener una lista de la configuración de la aplicación desde el [portal de Azure].
Para ver o cambiar la configuración de aplicación:

  1. Inicie sesión en el [portal de Azure].
  2. Seleccione **todos los recursos** o los **Servicios de aplicación** , a continuación, haga clic en el nombre de su servicio móvil migrados.
  3. Se abre el módulo de la configuración predeterminada.
  4. En el menú GENERAL, haga clic en **configuración de la aplicación** .
  5. Desplácese hasta la sección Configuración de la aplicación y busque la configuración de la aplicación.
  6. Haga clic en el valor de la configuración de la aplicación para editar el valor.  Haga clic en **Guardar** para guardar el valor.

Puede actualizar la configuración de la aplicación de varios al mismo tiempo.

> [AZURE.TIP]Hay dos opciones de aplicación con el mismo valor.  Por ejemplo, es posible que vea _ApplicationKey_ y _MS\_ApplicationKey_.  Actualizar la configuración de la aplicación al mismo tiempo.

### <a name="authentication"></a>Autenticación

Toda la configuración de autenticación está disponible como la configuración de la aplicación en su sitio migrado.  Para actualizar la configuración de autenticación, debe modificar la configuración de aplicación correspondiente.  La siguiente tabla muestra la configuración de la aplicación adecuada para su proveedor de autenticación:

| Proveedor          | Identificador de cliente                 | Secreto de cliente                | Otras opciones de configuración             |
| :---------------- | :------------------------ | :--------------------------- | :------------------------- |
| Cuenta de Microsoft | **MS\_MicrosoftClientID**  | **MS\_MicrosoftClientSecret** | **MS\_MicrosoftPackageSID** |
| Facebook          | **MS\_FacebookAppID**      | **MS\_FacebookAppSecret**     |                            |
| Twitter           | **MS\_TwitterConsumerKey** | **MS\_TwitterConsumerSecret** |                            |
| Google            | **MS\_GoogleClientID**     | **MS\_GoogleClientSecret**    |                            |
| Azure AD          | **MS\_AadClientID**        |                              | **MS\_AadTenants**          |

Nota: **MS\_AadTenants** se almacena como una lista de valores separados por comas de dominios del inquilino (los campos de "Inquilinos permitido" en el portal de servicios móviles).

> [AZURE.WARNING] **No utilice los mecanismos de autenticación en el menú Configuración**
>
> Servicio de aplicación de Azure proporciona un sistema de autenticación y la autorización de "sin código" independiente en el _autenticación / autorización_ menú Configuración y la opción de _Autenticación de Mobile_ (obsoleta) en el menú Configuración.  Estas opciones no son compatibles con un servicio móvil de Azure migrados.  Puede [actualizar su sitio](app-service-mobile-net-upgrading-from-mobile-services.md) para aprovechar las ventajas de la autenticación de servicio de aplicaciones de Azure.

### <a name="easytables"></a>Datos

La ficha _datos_ en servicios móviles se reemplazó por _Fácil tablas_ en el portal de Azure.  Para obtener acceso a tablas fáciles:

  1. Inicie sesión en el [portal de Azure].
  2. Seleccione **todos los recursos** o los **Servicios de aplicación** , a continuación, haga clic en el nombre de su servicio móvil migrados.
  3. Se abre el módulo de la configuración predeterminada.
  4. En el menú de móvil, haga clic en **tablas fáciles** .

Puede agregar una tabla haciendo clic en el botón **Agregar** u obtener acceso a las tablas existentes haciendo clic en un nombre de tabla.  Existen diversas operaciones que se puede hacer desde este módulo, incluidos:

* Cambiar los permisos de tabla
* Editar las secuencias de comandos operativas
* Administrar el esquema de tabla
* Eliminar la tabla
* Borrar el contenido de la tabla
* Eliminar filas específicas de la tabla

### <a name="easyapis"></a>API

La ficha de la _API_ de servicios móviles se reemplazó con _Las API de fácil_ dentro del portal de Azure.  Para obtener acceso a las API de fácil:

  1. Inicie sesión en el [portal de Azure].
  2. Seleccione **todos los recursos** o los **Servicios de aplicación** , a continuación, haga clic en el nombre de su servicio móvil migrados.
  3. Se abre el módulo de la configuración predeterminada.
  4. En el menú de móvil, haga clic en **Las API de fácil** .

La API migradas ya aparecen en el módulo.  También puede agregar una API de este módulo.  Para administrar una API específica, haga clic en la API.
En el nuevo módulo, puede ajustar los permisos y modificar las secuencias de comandos de la API.

### <a name="on-demand-jobs"></a>Trabajos del programador

Todos los trabajos de programador están disponibles a través de la sección de colecciones de trabajo del programador.  Para acceder a los trabajos del programador:

  1. Inicie sesión en el [portal de Azure].
  2. Seleccione **Examinar >**, escriba **programación** en el cuadro _filtro_ , y seleccione **Las colecciones de programador**.
  3. Seleccione el conjunto de trabajo para su sitio.  Se denomina _sitio_-trabajos.
  4. Haga clic en **configuración**.
  5. En administrar, haga clic en **trabajos del programador** .

Se muestran las tareas programadas con la frecuencia especificada antes de la migración.  Tareas a petición están deshabilitadas.  Para ejecutar una tarea a petición:

  1. Seleccione la tarea que desea ejecutar.
  2. Si es necesario, haga clic en **Habilitar** para habilitar el trabajo.
  3. Haga clic en **configuración**y luego en **programación**.
  4. Seleccione una periodicidad de **una vez**, a continuación, haga clic en **Guardar**

Los trabajos a petición están ubicados en `App_Data/config/scripts/scheduler post-migration`.  Se recomienda convertir todas las tareas a petición [WebJobs] o [funciones].  Escribir nueva trabajos del programador como [WebJobs] o [funciones].

### <a name="notification-hubs"></a>Notificación Hubs

Servicios móviles usa Hubs de notificación para las notificaciones de inserción.  La siguiente configuración de la aplicación se utilizan para vincular el Hub de notificación en el servicio móvil después de la migración:

| Configuración de la aplicación                    | Descripción                              |
| :------------------------------------- | :--------------------------------------- |
| **MS\_PushEntityNamespace**             | El Namespace de concentrador de notificación           |
| **MS\_NotificationHubName**             | El nombre del concentrador de notificación                |
| **MS\_NotificationHubConnectionString** | La cadena de conexión de Hub de notificación   |
| **MS\_NombreDeEspacioDeNombres**                   | Un alias para MS_PushEntityNamespace      |

El concentrador de notificación se administra a través del [portal de Azure].  Anote el nombre del concentrador de notificación (que puede encontrar esto mediante la configuración de la aplicación):

  1. Inicie sesión en el [portal de Azure].
  2. Seleccione **Examinar**>, a continuación, seleccione **Hubs de notificación**
  3. Haga clic en el nombre del concentrador de notificación asociado con el servicio móvil.

> [AZURE.NOTE]Si su centro de notificación es un tipo de "Mixto", no está visible.  "Mixto" escriba hubs utilizan Hubs de notificación y heredadas características de Bus de servicio de notificación.  [Convertir los espacios de nombres mixta] antes de continuar.  Una vez finalizada la conversión, su centro de notificación aparece en el [portal de Azure].

Para obtener más información, consulte la documentación de [Hubs de notificación] .

> [AZURE.TIP]Características de administración de Hubs de notificación en el [portal de Azure] aún están en vista previa.  El [Portal de Azure clásica] sigue estando disponible para administrar su Hubs de notificación.

### <a name="legacy-push"></a>Configuración de inserción heredada

Si ha configurado Push en el servicio móvil antes de la introducción de Hubs de notificación, usa _inserción heredada_.  Si está utilizando la inserción y no ve un concentrador de notificación que aparece en la configuración, es probable que esté utilizando la _inserción heredada_.  Esta característica se migra con todas las características.  Sin embargo, se recomienda que actualice a Hubs notificación inmediatamente después de completar la migración.

Mientras tanto, todas las opciones de inserción heredada (con la excepción notable del certificado APN) están disponibles en la configuración de la aplicación.  Actualizar el certificado de APN reemplazando el archivo apropiado en el sistema de archivos.

### <a name="app-settings"></a>Otras opciones de configuración de aplicación

La siguiente configuración de aplicación adicionales se migran desde el servicio móvil y están disponibles en *configuración* > *Configuración de la aplicación*:

| Configuración de la aplicación              | Descripción                             |
| :------------------------------- | :-------------------------------------- |
| **MS\_MobileServiceName**         | El nombre de la aplicación                    |
| **MS\_MobileServiceDomainSuffix** | Prefijo del dominio. es decir Mobile.NET de Azure |
| **MS\_ApplicationKey**            | La tecla de aplicación                    |
| **MS\_claves maestras**                 | La clave principal de aplicación                     |

La tecla de aplicación y la clave principal son idénticos a las claves de aplicación en el servicio móvil de original.  En particular, la tecla de aplicación se envía por los clientes móviles para validar el uso de la API de móvil.

### <a name="cliequivalents"></a>Equivalentes de línea de comandos

Ya puede usar el comando de _azure móvil_ para administrar su sitio de servicios móviles de Azure.  En su lugar, muchas funciones se reemplazaron con el comando _sitio azure_ .  Use la tabla siguiente para buscar equivalentes de comandos comunes:

| _Azure Mobile_ Comando                     | Comando equivalente de _Sitio de Azure_            |
| :----------------------------------------- | :----------------------------------------- |
| ubicaciones móviles                           | lista de ubicación de sitio                         |
| lista de dispositivos móvil                                | lista de sitios                                  |
| _nombre_ de la presentación móvil                         | _nombre_ de la presentación de sitio                           |
| reiniciar móvil _nombre_                      | _nombre_ del reinicio del sitio                        |
| bola móvil _nombre_                     | sitio implementación bola _commitId_ _nombre_ |
| clave móvil establece el _nombre_ del _tipo de_ _valor_       | eliminar _clave de_ _nombre_ del sitio appsetting <br/> sitio appsetting agregar _clave_=_valor de_ _nombre_ |
| _nombre_ de la lista de configuración de dispositivos móviles                  | _nombre_ de la lista de appsetting del sitio                |
| configuración móvil obtener _nombre_ _clave_             | _nombre_ de la _clave_ de presentación del appsetting sitio          |
| _nombre de_ _clave_ de conjunto de configuración móvil             | eliminar _clave de_ _nombre_ del sitio appsetting <br/> sitio appsetting agregar _clave_=_valor de_ _nombre_ |
| _nombre_ de la lista de dominio móvil                  | _nombre_ de la lista de dominio del sitio                    |
| Agregar un dominio móvil de _nombre de_ _dominio_          | Agregar un dominio de sitio _nombre_ de _dominio_            |
| eliminar _nombre_ de dominio móvil                | eliminar _dominio_ _nombre_ de sitio dominio         |
| _nombre_ de la presentación de escala móvil                   | _nombre_ de la presentación de sitio                           |
| cambiar _nombre_ de escala móvil                 | _modo de_ _nombre_ de sitio escala modo <br /> instancias _instancias de_ _nombre_ de sitio escala |
| _nombre_ de la lista de appsetting móvil              | _nombre_ de la lista de appsetting del sitio                |
| móvil appsetting agregar _valor_ de _nombre de_ _clave_ | sitio appsetting agregar _clave_=_valor de_ _nombre_   |
| _nombre_ de eliminar appsetting móvil _clave_      | eliminar _clave de_ _nombre_ del sitio appsetting        |
| móvil appsetting mostrar _nombre_ _clave_        | eliminar _clave de_ _nombre_ del sitio appsetting        |

Actualizar autenticación o la configuración de notificaciones de inserción actualizando la configuración de aplicación correspondiente.
Editar archivos y publicar su sitio mediante ftp o git.

### <a name="diagnostics"></a>Registro y diagnóstico

Registro de diagnóstico está desactivado normalmente en un servicio de aplicación de Azure.  Para habilitar el registro de diagnóstico:

  1. Inicie sesión en el [portal de Azure].
  2. Seleccione **todos los recursos** o los **Servicios de aplicación** , a continuación, haga clic en el nombre de su servicio móvil migrados.
  3. Se abre el módulo de la configuración predeterminada.
  4. Seleccione los **Registros de diagnóstico** en el menú de funciones.
  5. Haga clic **en** los siguientes registros: **Aplicación de registro (sistema de archivos)**, **mensajes de error detallados**y **seguimiento de solicitud de error**
  6. Haga clic en **Sistema de archivos** de registro del servidor Web
  7. Haga clic en **Guardar**

Para ver los registros:

  1. Inicie sesión en el [portal de Azure].
  2. Seleccione **todos los recursos** o los **Servicios de aplicación** , a continuación, haga clic en el nombre de su servicio móvil migrados.
  3. Haga clic en el botón **Herramientas**
  4. Seleccione la **secuencia de registro** en el menú de respetar.

Registros se muestran en la ventana que se generan.  También puede descargar los registros para un análisis posterior con sus credenciales de implementación. Para obtener más información, consulte la documentación del [registro] .

## <a name="known-issues"></a>Problemas conocidos

### <a name="deleting-a-migrated-mobile-app-clone-causes-a-site-outage"></a>Eliminar una aplicación de Mobile migrado, clonar provoca una interrupción en el sitio

Si clonar su servicio móvil migrado con PowerShell de Azure y después eliminar la copia, se quita la entrada DNS para el servicio de producción.  El sitio está ya no sea accesible desde Internet.  

Resolución: Si desea clonar su sitio, hágalo a través del portal.

### <a name="changing-webconfig-does-not-work"></a>Cambio de Web.config no funciona

Si tiene un sitio ASP.NET, cambia a la `Web.config` no se aplican archivo.  El servicio de aplicación de Azure genera un adecuado `Web.config` archivo durante el inicio para admitir el tiempo de ejecución de servicios móviles.  Puede reemplazar ciertas opciones (por ejemplo, los encabezados personalizados) mediante un archivo de transformación XML.  Crear un archivo llamado en `applicationHost.xdt` -debe terminar este archivo en el `D:\home\site` directorio en el servicio de Azure.  Cargar la `applicationHost.xdt` de archivos a través de un script de implementación personalizado o directamente mediante Kudu.  A continuación muestra un documento de ejemplo:

```
<?xml version="1.0" encoding="utf-8"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <system.webServer>
    <httpProtocol>
      <customHeaders>
        <add name="X-Frame-Options" value="DENY" xdt:Transform="Replace" />
        <remove name="X-Powered-By" xdt:Transform="Insert" />
      </customHeaders>
    </httpProtocol>
    <security>
      <requestFiltering removeServerHeader="true" xdt:Transform="SetAttributes(removeServerHeader)" />
    </security>
  </system.webServer>
</configuration>
```

Para obtener más información, consulte la documentación de [XDT transformar muestras] en GitHub.

### <a name="migrated-mobile-services-cannot-be-added-to-traffic-manager"></a>Servicios de Mobile migrados no se pueden agregar al administrador de tráfico

Cuando se crea un perfil de administrador de tráfico, no puede elegir directamente un servicio móvil migrado al perfil.  Usar "extremo externo".  El extremo externo solo pueden agregarse a través de PowerShell.  Para obtener más información, consulte el [tutorial de administrador de tráfico](https://azure.microsoft.com/blog/azure-traffic-manager-external-endpoints-and-weighted-round-robin-via-powershell/).

## <a name="next-steps"></a>Pasos siguientes

Ahora que la aplicación se migra a la aplicación de servicio, hay más funciones que puede usar:

  * Implementación de [ensayo ranuras] permitir cambios en el sitio de la fase y realizar un / B pruebas.
  * [WebJobs] proporcionar un reemplazo para las tareas programada a petición.
  * Puede [implementar continuamente] el sitio mediante la vinculación de su sitio GitHub, TFS o mercuriano.
  * Puede usar [Información de la aplicación] para supervisar su sitio.
  * Servir de un sitio Web y API Mobile desde el mismo código.

### <a name="upgrading-your-site"></a>Actualizar el sitio de servicios móviles SDK de aplicaciones móviles de Azure

  * Para proyectos de servidor basado en Node.js, el nuevo [Mobile aplicaciones Node.js SDK] proporciona varias características nuevas. Por ejemplo, ahora puede depuración y desarrollo local, use cualquier versión Node.js encima 0,10 y personalizar con software Express.js intermedio.

  * Para. Proyectos de servidor de red, los nuevos [paquetes de Mobile aplicaciones SDK NuGet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) tiene más flexibilidad sobre las dependencias de NuGet.  Estos paquetes compatible con la nueva autenticación de servicio de la aplicación y componen con cualquier proyecto ASP.NET. Para obtener más información sobre cómo actualizar, consulte [actualizar el servicio móvil de .NET existentes al servicio de aplicación](app-service-mobile-net-upgrading-from-mobile-services.md).

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[Precios de aplicación de servicio]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[Información de la aplicación]: ../application-insights/app-insights-overview.md
[Escala automática]: ../app-service-web/web-sites-scale.md
[Servicio de aplicaciones de Azure]: ../app-service/app-service-value-prop-what-is.md
[Documentación de implementación de servicio de aplicaciones de Azure]: ../app-service-web/web-sites-deploy.md
[Portal de clásico de Azure]: https://manage.windowsazure.com
[Portal de Azure]: https://portal.azure.com
[Azure Region]: https://azure.microsoft.com/en-us/regions/
[Planes de Azure programador]: ../scheduler/scheduler-plans-billing.md
[implementar continuamente]: ../app-service-web/app-service-continuous-deployment.md
[Convertir los espacios de nombres mixto]: https://azure.microsoft.com/en-us/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: http://curl.haxx.se/
[nombres de dominio personalizado]: ../app-service-web/web-sites-custom-domain-name.md
[Fiddler]: http://www.telerik.com/fiddler
[disponibilidad general de la aplicación de servicio de Azure]: https://azure.microsoft.com/blog/announcing-general-availability-of-app-service-mobile-apps/
[Conexiones de híbrido]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[Registro de información]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Aplicaciones móviles Node.js SDK]: https://github.com/azure/azure-mobile-apps-node
[Servicios frente a aplicación de servicio móvil]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Notificación Hubs]: ../notification-hubs/notification-hubs-push-notification-overview.md
[supervisar el rendimiento]: ../app-service-web/web-sites-monitor.md
[Postman]: http://www.getpostman.com/
[Realizar copias de seguridad de su servicio móvil]: ../mobile-services/mobile-services-disaster-recovery.md
[ranuras provisionales]: ../app-service-web/web-sites-staged-publishing.md
[VNet]: ../app-service-web/web-sites-integrate-with-vnet.md
[WebJobs]: ../app-service-web/websites-webjobs-resources.md
[Ejemplos de transformación XDT]: https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples
[Funciones]: ../azure-functions/functions-overview.md
