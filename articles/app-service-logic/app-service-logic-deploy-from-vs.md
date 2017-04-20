<properties 
    pageTitle="Crear aplicaciones de lógica en Visual Studio | Microsoft Azure" 
    description="Crear un proyecto de Visual Studio para crear e implementar la aplicación de la lógica." 
    authors="jeffhollan" 
    manager="erikre" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jehollan"/> 
    
# <a name="build-and-deploy-logic-apps-in-visual-studio"></a>Compile e implemente la lógica de aplicaciones en Visual Studio

Aunque el [Portal de Azure](https://portal.azure.com/) ofrece una excelente forma de diseñar y administrar las aplicaciones de lógica, también puede diseñar e implementar la aplicación de la lógica de Visual Studio en su lugar.  Lógica de aplicaciones viene con un conjunto de herramientas enriquecido Visual Studio que le permite crear una aplicación de lógica mediante el diseñador, configure las plantillas de implementación y automatización e implementar en cualquier entorno.  

## <a name="installation-steps"></a>Pasos de instalación

A continuación son los pasos para instalar y configurar Visual Studio tools para aplicaciones de lógica.

### <a name="prerequisites"></a>Requisitos previos

- [Visual Studio de 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- [Último SDK de Azure](https://azure.microsoft.com/downloads/) (2.9.1 o posterior)
- [Azure PowerShell] (https://github.com/Azure/azure-powershell#installation)
- Acceso a la web al usar el diseñador incrustado

### <a name="install-visual-studio-tools-for-logic-apps"></a>Instalar Visual Studio tools para aplicaciones de lógica

Una vez que haya los requisitos previos instalados, 

1. Abra 2015 de Visual Studio en el menú **Herramientas** y seleccione **extensiones y actualizaciones**
1. Seleccione la categoría **en línea** para buscar en línea
1. Buscar **Aplicaciones de lógica** mostrar las **Herramientas de aplicaciones de lógica de Azure para Visual Studio**
1. Haga clic en el botón **Descargar** para descargar e instalar la extensión
1. Reinicie Visual Studio después de la instalación

> [AZURE.NOTE] También puede descargar la extensión directamente desde [este vínculo](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9)

Una vez instalado, podrá usar el proyecto del grupo de recursos de Azure con el Diseñador de lógica de aplicación.

## <a name="create-a-project"></a>Crear un proyecto

1. Vaya al menú **archivo** y seleccione **nuevo** >  **proyecto** (o bien, puede ir a **Agregar** y, a continuación, seleccione **nuevo proyecto** para agregar a una solución existente):  ![menú archivo](./media/app-service-logic-deploy-from-vs/filemenu.png)

1. En el cuadro de diálogo Buscar de la **nube**y, a continuación, seleccione el **Grupo de recursos de Azure**. Escriba un **nombre** y, a continuación, haga clic en **Aceptar**.
    ![Agregar nuevo proyecto](./media/app-service-logic-deploy-from-vs/addnewproject.png)

1. Seleccione la plantilla de **aplicación de la lógica** . Para empezar con esta opción creará una plantilla de implementación de la aplicación de lógica en blanco.
    ![Seleccionar plantilla de Azure](./media/app-service-logic-deploy-from-vs/selectazuretemplate.png)

1. Una vez que haya seleccionado la **plantilla**, haga clic en **Aceptar**.

    Ahora el proyecto de aplicación de la lógica se agrega a la solución. Debería ver el archivo de implementación en el Explorador de soluciones:  

    ![Implementación](./media/app-service-logic-deploy-from-vs/deployment.png)

## <a name="using-the-logic-app-designer"></a>Mediante el Diseñador de la aplicación de lógica

Una vez que tenga un proyecto del grupo de recursos de Azure que contiene una aplicación de lógica, puede abrir el Diseñador de Visual Studio para ayudarle a crear el flujo de trabajo.  El diseñador requiere una conexión a internet para consultar los conectores de datos y las propiedades disponibles (por ejemplo, si usa el conector de Dynamics CRM Online, el diseñador consultará la instancia CRM para obtener una lista de propiedades predeterminadas y personalizados disponibles).

1. Haga clic en el `<template>.json` de archivo y seleccione **Abrir con el Diseñador de lógica de aplicación** (o `Ctrl+L`)
1. Elija la suscripción, el grupo de recursos y la ubicación para la plantilla de implementación
    - Es importante que tenga en cuenta que diseñar una aplicación de lógica creará los recursos de **Conexión de API** para consultar propiedades durante el diseño.  El grupo de recursos seleccionado será el grupo de recursos utilizado para crear las conexiones en tiempo de diseño.  Puede ver o modificar las conexiones de la API, vaya al Portal de Azure y para **Las conexiones de la API**de exploración.
    ![Selector de suscripción](./media/app-service-logic-deploy-from-vs/designer_picker.png)
1. Debe representar el diseñador basado en la definición de la `<template>.json` archivo.
1. Ahora puede crear y diseñar la aplicación de lógica y cambios se actualizarán en la plantilla de implementación.
    ![Diseñador de Visual Studio](./media/app-service-logic-deploy-from-vs/designer_in_vs.png)

También verá `Microsoft.Web/connections` recursos que se agrega a su archivo de recursos para todas las conexiones necesarias para la aplicación de lógica a función.  Estas propiedades de conexión se pueden establecer cuando se implementa y administra después de implementar en las **Conexiones de la API** en el Portal de Azure.

### <a name="switching-to-the-json-code-view"></a>Cambiar a la vista código JSON

Puede seleccionar la pestaña de la **Vista código** en la parte inferior del diseñador para cambiar a la representación JSON de la aplicación de la lógica.  Para volver a todo el recurso JSON, haga clic en el `<template>.json` de archivo y seleccione **Abrir**.

### <a name="saving-the-logic-app"></a>Guardar la aplicación de lógica

Puede guardar la aplicación lógica en cualquier momento mediante el botón **Guardar** o `Ctrl+S`.  Si hay algún error con la aplicación de la lógica en el momento de que guardar, mostrará en la ventana de **resultados** de Visual Studio.

## <a name="deploying-your-logic-app"></a>Implementación de la aplicación de lógica

Por último, una vez haya configurado la aplicación, puede implementar directamente desde Visual Studio en un par de pasos. 

1. Haga clic en el proyecto en el Explorador de soluciones y vaya a **implementar** > **Implementación nueva...** 
     ![Implementación nueva](./media/app-service-logic-deploy-from-vs/newdeployment.png)

2. Se le pide que inicie sesión en sus suscripciones Azure. 

3. Ahora debe elegir los detalles del grupo de recursos que desee para implementar la aplicación lógica. 
    ![Implementar al grupo de recursos](./media/app-service-logic-deploy-from-vs/deploytoresourcegroup.png)

     > [AZURE.NOTE]    Asegúrese de seleccionar los archivos de plantilla y parámetros derecha del grupo de recursos (por ejemplo si se implementa en un entorno de producción desea elegir el archivo de parámetros de producción). 
4. Seleccione el botón de implementar
 
    
6. El estado de la implementación aparece en la ventana de **resultados** (debe elegir **El aprovisionamiento de Azure**. 
    ![Salida](./media/app-service-logic-deploy-from-vs/output.png)

En el futuro, puede revisar la aplicación de la lógica de control de código fuente y utilizar Visual Studio para implementar nuevas versiones. 

> [AZURE.NOTE] Si modifica la definición en el Portal de Azure directamente, la próxima vez que se implementa desde Visual Studio dichos cambios se sobrescribirán.

## <a name="next-steps"></a>Pasos siguientes

- Para empezar con la lógica de aplicaciones, siga el tutorial de [crear una aplicación de lógica](app-service-logic-create-a-logic-app.md) .  
- [Ver ejemplos y escenarios comunes](app-service-logic-examples-and-scenarios.md)
- [Puede automatizar procesos empresariales con aplicaciones de lógica](http://channel9.msdn.com/Events/Build/2016/T694) 
- [Obtenga información sobre cómo integrar sus sistemas con aplicaciones de lógica](http://channel9.msdn.com/Events/Build/2016/P462)