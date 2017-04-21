<properties
   pageTitle="Implementar un nuevo servicio Web"
   description="El flujo de trabajo de la implementación de un BRAZO en función de servicio web"
   services="machine-learning"
   documentationCenter=""
   authors="vDonGlover"
   manager="raymondl"
   editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="v-donglo"/>

# <a name="deploy-a-new-web-service"></a>Implementar un nuevo servicio web

Ahora de aprendizaje de Microsoft Azure máquina proporciona servicios web que se basan en [El Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md) permitiendo implementar el servicio web para varias regiones y opciones del plan de facturación de nuevo.

El flujo de trabajo general para implementar un servicio web mediante servicios Web de Microsoft Azure máquina aprendizaje es:

* Crear un ensayo predictivo
* implementarlo
* configurar su nombre
* plan de facturación
* Pruébelo
* consume.

La ilustración siguiente muestra el flujo de trabajo.

![Flujo de trabajo de implementación de servicio de Web][1]
 
## <a name="deploy-web-service-from-studio"></a>Implementar el servicio web desde Studio 

Implementar un ensayo como un nuevo servicio web. Inicie sesión en el estudio de aprendizaje de equipo y crear un nuevo servicio web predictiva. 

**Nota**: si ya ha implementado apropiados como un servicio web clásico no se puede implementar como un nuevo servicio web.
 
Haga clic en **Ejecutar** en la parte inferior del lienzo de ensayo y, a continuación, haga clic en **Desplegar servicio Web** e **implementar Web [New]**. Se abrirá la página de implementación del Administrador de servicio Web de aprendizaje de equipo.

## <a name="machine-learning-web-service-manager-deploy-experiment-page"></a>Administrador de servicios Web de máquina aprendizaje implementar página de ensayo
En la página implementar ensayo, escriba un nombre para el servicio web.
Seleccione un plan de precio. Si tiene un plan de precio existente que puede seleccionar, en caso contrario, debe crear un nuevo plan de precio del servicio. 

1.  En el **Plan de precios** de lista desplegable, seleccione un plan existente o seleccione la opción **Seleccionar nuevo plan** .
2.  En **Nombre del Plan**, escriba un nombre que identifique el plan en la factura.
3.  Seleccione uno de los **Niveles de Plan mensual**. Tenga en cuenta que el valor predeterminado de niveles de plan para los planes de la región predeterminados y el servicio web se implementa en esa región.

Haga clic en **implementar** y la página de tutorial rápido para abrir el servicio de web.

## <a name="quickstart-page"></a>Página tutorial rápido
La página del tutorial de servicio web da acceso e instrucciones en las tareas más comunes que se realizará después de crear un nuevo servicio web. Desde aquí puede acceder fácilmente tanto la página de **prueba** y **Consume** .

## <a name="testing-your-web-service"></a>Probar el servicio web

Desde la página tutorial rápido, haga clic en servicio web de prueba en tareas comunes.   

Para probar el servicio web como una respuesta de solicitud de servicio (RR):

* En la barra de menús, haga clic en **prueba** .
* Haga clic en **Respuesta de la solicitud**.
* Escriba los valores apropiados para las columnas de entrada de su ensayo.
* Haga clic en **Respuesta de solicitud**de la prueba.

Resultados se mostrarán en el lado derecho de la página.

Para probar un servicio web de servicio de ejecución de proceso por lotes (BES), que usa un archivo CSV:

* En la barra de menús, haga clic en **prueba** .
* Haga clic en **lote**.
* En la entrada, haga clic en Examinar y navegue hasta el archivo de datos de ejemplo.
* Haga clic en **prueba**.

Se muestra el estado de la prueba en **Probar procesos**.

## <a name="consuming-your-web-service"></a>Su servicio Web

Cuando se implementa como un servicio web, el aprendizaje Azure experimentación proporciona una API de REST que puede utilizar una amplia variedad de dispositivos y plataformas. Esto es porque la simple API de REST acepta y responde con JSON con formato de mensajes. El portal de aprendizaje de máquina Azure proporciona código que puede usarse para llamar al servicio web en R, C# y Python.
 
En la página Consuming puede encontrar:

* La clave de API y URI para consumir el servicio web en las aplicaciones.
* Plantillas de aplicaciones web y Excel para comenzar iniciar el proceso de consumo.
* Código de ejemplo en C#, python y R para empezar.

Para obtener más información sobre cómo utilizar los servicios web, vea [cómo utilizar un servicio web de Azure aprendizaje del equipo que se ha implementado desde un equipo de ensayo de aprendizaje](machine-learning-consume-web-services.md).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo utilizar los servicios web, consulte:

[Cómo utilizar un servicio web de aprendizaje del equipo de Azure que se ha implementado desde un equipo de ensayo de aprendizaje](machine-learning-consume-web-services.md)

[Servicios Web de Azure máquina aprendizaje: Implementación y consumo](machine-learning-deploy-consume-web-service-guide.md)

<!--Image references-->
[1]: ./media/machine-learning-webservice-deploy-a-web-service/armdeploymentworkflow.png


<!--links-->
