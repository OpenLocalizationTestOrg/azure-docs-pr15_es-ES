<properties
    pageTitle="Administrar y supervisar los conectores y aplicaciones de la API en la aplicación de servicio | Microsoft Azure"
    description="Ver el rendimiento de los conectores y aplicaciones de la API en las aplicaciones de lógica; arquitectura de microservices"
    services="app-service\logic"
    documentationCenter=".net,nodejs,java"
    authors="MandiOhlinger"
    manager="anneta"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="mandia"/>

# <a name="manage-and-monitor-your-built-in-api-apps-and-connectors"></a>Administrar y supervisar la API de aplicaciones y los conectores integrados

>[AZURE.NOTE] Esta versión de este artículo se aplica a la versión del esquema de 2014 12-01-vista previa de aplicaciones de lógica.

Crea un App API integradas. ¿Y ahora qué?

En Azure, cada App API es otro sitio web alojado en Azure. Como resultado, puede ver fácilmente cuántas peticiones realizados y ver la cantidad de datos que se utiliza el conector. También puede su App API de copia de seguridad, crear alertas, habilitar la seguridad de estaño y agregar usuarios y funciones.

En este tema se describe algunas de las diferentes opciones para administrar su App API.

Para ver estas características integradas, abra el App API en el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). Si el App API está en su startboard, selecciónela para abrir las propiedades. Puede también seleccionar **Examinar**, seleccione **API de aplicaciones**y, a continuación, seleccione su App API:

![][browse]

## <a name="see-the-properties-you-entered"></a>Ver las propiedades que escribió

Cuando abre el App API, hay varias tareas y características disponibles:

![][settings]

Puedes:

- **Configuración** muestra información específica en el App API, incluidos los detalles de la suscripción y enumera los usuarios que tienen acceso a la aplicación de la API. También puede aumentar o disminuir el número de instancias de su App API con la característica escala; entre otras características.
- Use los botones de **Iniciar** y **Detener** para controlar el App API.
- Cuando se realizan las actualizaciones de productos en los archivos subyacentes usados con la API de App, puede hacer clic en **Actualizar** para obtener las últimas versiones. Por ejemplo, si hay una solución o una actualización de seguridad publicadas por Microsoft, automáticamente haciendo clic en **Actualizar** actualiza su App API para incluir esta corrección.
- Seleccione **El Plan de cambio** para actualizar o degradar basándose en el uso de datos de la API de App. También puede usar esta característica para ver el uso de datos.
- Al crear un conector que tiene tablas, como el conector SQL, puede escribir un nombre de tabla para conectarse a. Un esquema basado en la tabla es automáticamente creen y estén disponibles al hacer clic en **Descargar esquemas**. A continuación, puede usar este esquema descargado para crear una transformación o una asignación.

## <a name="change-your-connector-or-api-configuration-values-you-entered"></a>Cambiar el conector o valores de configuración de API que haya escrito

Después de configurar o crear el conector integrado, puede cambiar los valores que haya escrito. Por ejemplo, si ha configurado el conector de SQL y desea cambiar el nombre de tabla o SQL Server, puede hacerlo en el módulo API App para el conector.

Los pasos son:

1. Abra el conector o API App. Cuando lo haga, se abrirá el módulo API App.
2. En **Essentials**, haga clic en el hipervínculo en la propiedad de Host. El hipervínculo se denomina algo como *slackconnector* o *microsoftsqlconnector123*:

    ![][apiapphost]

3. En el módulo API App Host, seleccione **configuración**. En el módulo de configuración, seleccione **Configuración de la aplicación**. Los valores de configuración se muestran en la **Configuración de la aplicación**:

    ![][hostsettings]

4. Haga clic en la configuración que desea cambiar, escriba el nuevo valor y **Guardar** los cambios.


## <a name="install-the-hybrid-connection-manager---optional"></a>Instalar al administrador de conexión híbrido - opcional

![][hcsetup]

El Administrador de conexión híbrido le ofrece la capacidad de conectar con un sistema local, como SQL Server o SAP. Esta conectividad híbrido usa Bus de servicio de Azure para conectarse y controlar la seguridad entre los recursos de Azure y los recursos locales.

Consulte [con el Administrador de conexión híbrido en Azure de aplicación de servicio](app-service-logic-hybrid-connection-manager.md).

> [AZURE.NOTE] Administrador de conexiones de híbrido se requiere únicamente si se está conectando a un recurso local detrás del firewall. Si no se conecta a un sistema local, el Administrador de conexión híbrida no puede mostrarse en el módulo de conector.

## <a name="monitor-the-performance"></a>Supervisar el rendimiento
Indicadores de rendimiento son características integradas e incluyen con cada App API que cree. Estas métricas son específicas de la API de aplicación hospedada en Azure. Métrica de ejemplo:

![][monitoring]

Puedes:

- Seleccione **errores y solicitudes** para agregar indicadores de rendimiento diferentes incluidos los códigos de error HTTP comúnmente conocidos, como 200, 400 o códigos de estado HTTP 500. También puede ver los tiempos de respuesta, ver cuántas peticiones se realizan en el App API y ver la cantidad de datos entra y sale la cantidad de datos. En función de las mediciones de rendimiento, puede crear alertas de correo electrónico si una métrica supera un umbral de su elección.
- En **uso**, puede ver cuánto **CPU** se usa en el App API, revise la **Cuota de uso** de actual en MB y ver el uso de la cantidad máxima de datos en función de su nivel de costo. **Estimado dedica** puede ayudarle a determinar los costos de la ejecución de su App API posibles.
- Seleccione **procesos** para abrir el Explorador de proceso. Aquí muestran las instancias de web y sus propiedades, incluido el uso de memoria y el recuento de subproceso.

Estas herramientas puede determinar si el Plan de servicio de aplicación deben escalado o reducido, en función de sus necesidades empresariales. Estas características están integradas en el portal con no se necesitan herramientas adicionales.

## <a name="control-the-security"></a>Controlar la seguridad

Aplicaciones de la API usar seguridad basada en roles. Estas funciones se aplican a toda la experiencia Azure, incluidas las API de aplicaciones y otros recursos de Azure. Las funciones incluyen:

Función | Descripción
--- | ---
Propietario | Tener acceso completo a la experiencia de administración y puede conceder acceso a otros usuarios o grupos.
Colaborador | Tener acceso completo a la experiencia de administración. No puede conceder acceso a otros usuarios o grupos.
Lector | Puede ver todos los recursos excepto información confidencial.
Administrador de acceso de usuario | Puede ver todos los recursos, crear y administrar roles y crear y administrar vales de soporte técnico.

Consulte [control de acceso basado en roles en el portal de Microsoft Azure](../active-directory/role-based-access-control-configure.md).

Puede agregar usuarios y asignar roles específicos a su App API fácilmente. El portal muestra los usuarios que tienen acceso y su rol asignado:

![][access]  

- Seleccione **usuarios** para agregar un usuario, asignar un rol y quitar un usuario.
- Seleccione **las funciones** para ver todos los usuarios de una función específica, agregar un usuario a un rol y quitar un usuario de una función.


## <a name="more-good-stuff"></a>Más cuestiones buena
- Seleccione la **definición de la API** para abrir el archivo de Swagger creada automáticamente para la aplicación de la API específica.
- Seleccione **dependencias** para ver los archivos necesarios para el App API. Por ejemplo, si usa el conector SAP, instalar algunos archivos adicionales en el Administrador de conexión de local híbrido. Estas dependencias se muestran en el módulo de aplicación de la API.

>[AZURE.IMPORTANT] Cuando abre las propiedades de la aplicación API y busque en **Essentials**, hay vínculos de **Host** y la **puerta de enlace** que abra aspas nuevas:
>
> ![][host]
>
>Estas propiedades son específicas para el sitio Web que hospeda su App API. Cuando se utiliza un conector o API App integrados, la mayoría de estas propiedades no se aplica realmente y le recomendamos que no actualizan estas propiedades. Si ha creado su propio App API en Visual Studio y había implementado a su suscripción de Azure, puede usar los módulos Host y la puerta de enlace. <br/><br/>


>[AZURE.NOTE] Para empezar con aplicaciones lógica antes de suscribirse para una cuenta de Azure, vaya a [Probar la aplicación de lógica](https://tryappservice.azure.com/?appservice=logic). Puede crear una aplicación de la lógica de corta duración starter. Sin tarjetas de crédito obligatorios y sin compromisos.

## <a name="read-more"></a>Obtener más información

[Supervisar las aplicaciones de lógica](app-service-logic-monitor-your-logic-apps.md)<br/>
[Conectores y lista de aplicaciones de la API en la aplicación de servicio](app-service-logic-connectors-list.md)<br/>
[Control de acceso basado en roles en el portal de Microsoft Azure](../active-directory/role-based-access-control-configure.md)<br/>
[Uso del Administrador de conexión de híbrido en Azure de aplicación de servicio](app-service-logic-hybrid-connection-manager.md)


<!--Image references-->
[browse]: ./media/app-service-logic-monitor-your-connectors/browse.png
[settings]: ./media/app-service-logic-monitor-your-connectors/settings.png
[hcsetup]: ./media/app-service-logic-monitor-your-connectors/hcsetup.png
[monitoring]: ./media/app-service-logic-monitor-your-connectors/monitoring.png
[access]: ./media/app-service-logic-monitor-your-connectors/access.png
[host]: ./media/app-service-logic-monitor-your-connectors/host.png
[hostsettings]: ./media/app-service-logic-monitor-your-connectors/hostsettings.png
[apiapphost]: ./media/app-service-logic-monitor-your-connectors/apiapphost.png
