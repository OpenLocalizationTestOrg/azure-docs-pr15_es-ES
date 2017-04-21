<properties
   pageTitle="Administrar las aplicaciones en Visual Studio | Microsoft Azure"
   description="Use Visual Studio para crear, desarrollar, empaquetar, implementar y depurar servicios y las aplicaciones de servicio tela."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="seanmck;mikhegn"/>

# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Use Visual Studio para simplificar la escritura y administrar las aplicaciones de servicio tela

Puede administrar las aplicaciones de Azure servicio tela y servicios a través de Visual Studio. Una vez que haya [configurado su entorno de desarrollo](service-fabric-get-started.md), puede usar Visual Studio para crear aplicaciones de servicio tela, agregar servicios o empaquetar, registrar e implementar aplicaciones en el clúster de desarrollo local.

## <a name="deploy-your-service-fabric-application"></a>Implementar la aplicación de servicio tela

De forma predeterminada, implementar una aplicación combina los pasos siguientes en una operación simple:

1. Crear el paquete de aplicación
2. Cargar el paquete de aplicación en el almacén de imágenes
3. Registrar el tipo de aplicación
4. Quitar todas las instancias de aplicación en ejecución
5. Crear una nueva instancia de aplicación

En Visual Studio, presione **F5** también implementar la aplicación y adjuntar al depurador a todas las instancias de aplicación. Puede usar **Ctrl + F5** para implementar una aplicación sin depurar o puede publicar en un clúster local o remoto usando el perfil de publicación. Para obtener más información, consulte [publicar una aplicación a un clúster remoto mediante Visual Studio](service-fabric-publish-app-remote-cluster.md).

### <a name="application-debug-mode"></a>Modo de depuración de aplicación

De forma predeterminada, Visual Studio quita las instancias existentes del tipo de aplicación al dejar de depuración o (si se implementa la aplicación sin asociar el depurador), al volver a instalar la aplicación. En ese caso, se quitan todos los datos de la aplicación. Al depurar localmente, puede que desee mantener los datos que ya ha creado al probar una nueva versión de la aplicación, que desea mantener la aplicación en ejecución o que desee sesiones de depuración subsiguientes para actualizar la aplicación. Tela de servicio de Visual Studio Tools proporciona una propiedad denominada **Modo de depuración de la aplicación**, los controles que si la **F5** debe desinstalar la aplicación, mantener la aplicación en ejecución después de una sesión de depuración finaliza o permitir que actualizado en las siguientes sesiones de depuración, en lugar de elimina y vuelve a instalar la aplicación.

#### <a name="to-set-the-application-debug-mode-property"></a>Para establecer la propiedad de modo de depuración de aplicación

1. En el menú de acceso directo del proyecto de la aplicación, elija **Propiedades** (o presione la tecla **F4** ).
2. En la ventana de **Propiedades** , establezca la propiedad **Modo de depuración de la aplicación** .

    ![Establezca la propiedad de modo de depuración de aplicación][debugmodeproperty]

Estas son las opciones de **Modo de depuración de aplicaciones** disponibles.

1. **Actualización automática**: la aplicación continúa ejecutándose cuando finaliza la sesión de depuración. La siguiente **F5** lo tratará la implementación como una actualización con modo automático no supervisado para actualizar rápidamente la aplicación a una versión más reciente con una cadena de fecha agregada. El proceso de actualización conserva los datos que ha introducido en una sesión de depuración anterior.

2. **Mantener la aplicación**: la aplicación sigue funcionando en el clúster cuando finaliza la sesión de depuración. En la siguiente **F5** se quitarán la aplicación y la aplicación recién creada se implementará en el clúster.

3. **Quitar la aplicación** , la aplicación que se eliminen cuando finaliza la sesión de depuración.

Para **Actualizar automáticamente** los datos se conservan aplicando las capacidades de actualización de aplicaciones de servicio, pero se ajusta para optimizar el rendimiento en lugar de seguridad. Para obtener más información acerca de cómo actualizar aplicaciones y cómo puede realizar una actualización en un entorno real, vea [actualizar aplicaciones de servicio tela](service-fabric-application-upgrade.md).

![Ejemplo de la nueva versión de la aplicación con la fecha agregada][preservedata]

>[AZURE.NOTE] Esta propiedad no existe antes de la versión 1.1 de las herramientas de tela de servicio para Visual Studio. Antes de 1.1, utilice la propiedad **Conservar datos empiezan** a alcanzar el mismo comportamiento. La opción "Mantener aplicación" se introdujo en la versión 1.2 de las herramientas de tela de servicio para Visual Studio.

## <a name="add-a-service-to-your-service-fabric-application"></a>Agregar un servicio a la aplicación de servicio tela

Puede agregar nuevos servicios a su aplicación para ampliar su funcionalidad.  Para asegurarse de que el servicio está incluido en el paquete de aplicación, agregue el servicio mediante el elemento de menú **Nuevo servicio a tela...** .

![Agregar un nuevo servicio de tela a la aplicación][newservice]

Seleccione un tipo de proyecto de tela de servicio para agregar a la aplicación y especifique un nombre para el servicio.  Vea [Elegir un marco de trabajo para un servicio](service-fabric-choose-framework.md) para ayudarle a decidir qué tipo de servicio para usar.

![Seleccione un tipo de proyecto de servicio de tela para agregar a la aplicación][addserviceproject]

El nuevo servicio se agregará a la solución y el paquete de aplicación existente. Las referencias de servicio y una instancia de servicio de forma predeterminada se agregará al manifiesto de aplicación. El servicio se crea y se inicia la próxima vez que se implementa la aplicación.

![El nuevo servicio se agregará a su manifiesto de aplicación][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Empaquetar la aplicación de servicio tela

Para implementar la aplicación y sus servicios en un clúster, debe crear un paquete de aplicación.  El paquete organiza el manifiesto de aplicación, servicio manifiestos y otros archivos necesarios en un diseño específico.  Visual Studio configura y administra el paquete en la carpeta del proyecto de la aplicación, en el directorio 'paquete'.  Hacer clic en el **paquete** en el menú contextual de **aplicación** crea o actualiza el paquete de aplicación.  Desea hacerlo si implementa la aplicación mediante secuencias de comandos de PowerShell personalizados.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Quitar aplicaciones y tipos de aplicación con el Explorador de nube

Puede realizar operaciones de administración de clúster básico de Visual Studio mediante el Explorador de la nube, que puede iniciar en el menú **Ver** . Por ejemplo, puede eliminar aplicaciones y deshacer el aprovisionamiento de tipos de aplicaciones en clústeres locales o remotos.

![Quitar una aplicación](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

>[AZURE.TIP] Para una mejor funcionalidad de administración de clúster, consulte [visualizar el clúster con el Explorador de tela de servicio](service-fabric-visualizing-your-cluster.md).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes

- [Modelo de aplicación de servicio tela](service-fabric-application-model.md)
- [Implementación de aplicaciones de servicio tela](service-fabric-deploy-remove-applications.md)
- [Administrar los parámetros de la aplicación para múltiples entornos](service-fabric-manage-multiple-environment-app-configuration.md)
- [Depuración de la aplicación de servicio tela](service-fabric-debugging-your-application.md)
- [Visualizar el clúster mediante el Explorador de tela de servicio](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[preservedata]:./media/service-fabric-manage-application-in-visual-studio/preservedata.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
