<properties
   pageTitle="Configurar un proyecto de servicio de nube Azure con Visual Studio | Microsoft Azure"
   description="Obtenga información sobre cómo configurar un proyecto de servicio de nube de Azure en Visual Studio, dependiendo de los requisitos para el proyecto."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="configure-an-azure-cloud-service-project-with-visual-studio"></a>Configurar un proyecto de servicio de nube Azure con Visual Studio

Puede configurar un proyecto de servicio de nube de Azure, dependiendo de los requisitos para el proyecto. Puede establecer propiedades del proyecto para las siguientes categorías:

- **Publicar un servicio de nube en Azure**

  Puede establecer una propiedad para asegurarse de que no se elimina accidentalmente un servicio de nube existente implementado en Azure.

- **Ejecutar o depurar un servicio de nube en el equipo local**

  Puede seleccionar una configuración de servicio para usar e indicar si desea iniciar el emulador de almacenamiento de Azure.

- **Validar un paquete de servicio de nube cuando se crea**

  Puede decidir tratar las advertencias como errores para que asegurarse de que va a implementar el paquete de servicio de nube sin problemas. Esto reduce el tiempo de espera si implementar y, a continuación, descubrir que se ha producido un error.

La siguiente ilustración muestra cómo seleccionar una configuración que usará al ejecutar o depurar el servicio de nube localmente. Puede establecer cualquiera de las propiedades del proyecto que requieren desde esta ventana, tal como se muestra en la ilustración.

![Configurar un proyecto de Microsoft Azure](./media/vs-azure-tools-configuring-an-azure-project/IC713462.png)

## <a name="to-configure-an-azure-cloud-service-project"></a>Para configurar un proyecto de servicio de nube de Azure

1. Para configurar un proyecto de servicio de nube desde el **Explorador de soluciones**, abra el menú contextual para el proyecto de servicio de nube y, a continuación, elija **Propiedades**.

  Una página con el nombre del proyecto de servicio de nube aparece en el editor de Visual Studio.

1. Elija la pestaña de **desarrollo** .

1. Para asegurarse de que no elimina accidentalmente una implementación existente en Azure, en el símbolo del sistema antes de eliminar una lista de distribución existente, elija **Verdadero**.

1. Para seleccionar la configuración del servicio que desea utilizar al ejecutar o depurar su servicio de nube de forma local, en la lista de **Configuración del servicio de** elegir la configuración del servicio.

  >[AZURE.NOTE] Si desea crear una configuración de servicio usar, vea Cómo: administrar configuraciones de servicio y perfiles. Si desea modificar una configuración de servicio para una función, consulte [cómo configurar los roles de un servicio de nube Azure con Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

1. Para iniciar el almacenamiento de Azure emulador al ejecutar o depurar su servicio de nube de forma local, en el **emulador de almacenamiento de Azure inicio**, elija **Verdadero**.

1. Para asegurarse de que no se puede publicar si hay errores de validación de paquete en **tratar advertencias como errores**, elija **Verdadero**.

1. Para asegurarse de que su rol web utiliza el mismo puerto cada vez que se inicia localmente en IIS Express en **puertos de project web de uso**, elija **Verdadero**. Para usar un puerto específico de un proyecto web determinado, abra el menú contextual para el proyecto web, elija la pestaña **Propiedades** , elija la pestaña **Web** y cambie el número de puerto en la configuración de la **Dirección Url de Project** en la sección **IIS Express** . Por ejemplo, escriba `http://localhost:14020` como la dirección URL del proyecto.

1. Para guardar los cambios realizados en las propiedades del proyecto de servicio de nube, elija el botón **Guardar** en la barra de herramientas.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo configurar proyectos de servicio de nube de Azure en Visual Studio, vea [proyecto de configurar su Azure con varias configuraciones de servicio](vs-azure-tools-multiple-services-project-configurations.md).
