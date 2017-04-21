<properties
   pageTitle="Crear un proyecto de Azure con Visual Studio | Microsoft Azure"
   description="Crear un proyecto de Azure con Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="creating-an-azure-project-with-visual-studio"></a>Crear un proyecto de Azure con Visual Studio

Las herramientas de Azure para Visual Studio proporcionan una plantilla que le permite crear un servicio de nube para Azure. Las herramientas también ayudan a configurar, depurar e implementar el servicio de nube en Azure.

Una solución de servicio de nube Azure contiene los siguientes tipos de proyectos:

- **Proyecto de Azure**

    El proyecto Azure tiene asociaciones de los proyectos de la función de la solución. También incluye la definición de servicio y los archivos de configuración de servicio. El archivo de definición de servicio define la configuración de tiempo de ejecución de su aplicación incluidos qué funciones son necesarias, extremos y tamaño de la máquina virtual. El archivo de configuración de servicio configura cuántas instancias de una función se ejecutan y los valores de la configuración definida para una función. Para obtener más información acerca de estas opciones, vea [Cómo: configurar los Roles de un servicio de nube de Azure con Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

- **Proyecto de la función de Web**

    Una función de trabajador realiza procesamiento en segundo plano. Una función de trabajo puedan comunicarse con los servicios de almacenamiento y con otros servicios basados en Internet. Una función de trabajo puede tener cualquier número de extremos HTTP, HTTPS o TCP.

    - **Rol Web de ASP.NET**para crear una aplicación ASP.NET con un web front-end
    - **Función de Web MVC5 de ASP.NET**
    - **Función de Web MVC4 de ASP.NET**
    - **Función de Web MVC3 de ASP.NET**
    - **Función de WCF servicio Web**para la creación de un servicio WCF
    - **Función de Silverlight negocio aplicación Web** (requiere Visual Studio 2012)

- **Función de trabajador de la caché**

    Una función que proporciona una caché dedicada a la aplicación.

- **Función de trabajador con cola de Bus de servicio**

    Cola de bus de servicio que proporciona la funcionalidad de cola de mensajes para comunicarse con el proceso de trabajo. Para obtener más información, vea [cómo usar colas de Bus de servicio](http://go.microsoft.com/fwlink/?LinkId=260560).

## <a name="to-create-an-azure-cloud-service-project-in-visual-studio"></a>Para crear un proyecto de servicio de nube de Azure en Visual Studio

1. Inicie Microsoft Visual Studio como administrador.

1. En la barra de menús, elija **archivo**, **nuevo** **proyecto**.

1. En el panel **Tipos de proyecto** , elija **nube** del proyecto de Visual C# o Visual Basic nodos de plantilla.

1. En el panel **plantillas** , elija **Servicio de nube de Azure**.

1. Especifique qué versión de .NET Framework que desea usar para desarrollar el proyecto.

1. Escriba un nombre y una ubicación para el proyecto y un nombre para la solución. Elija el botón **Aceptar** .

1. En el cuadro de diálogo **Nuevo proyecto de Azure** , elija las funciones que desea agregar y seleccione el botón de flecha derecha para agregarlas a su solución. Puede agregar tantas funciones como sea necesario.

1. Para cambiar el nombre de una función que haya agregado a su proyecto, mantenga el mouse sobre el rol en el cuadro de diálogo **Nuevo proyecto de Azure** y elija el icono de **cambiar el nombre** a la derecha de la función. También puede cambiar un rol dentro de su solución después de que se ha agregado.
