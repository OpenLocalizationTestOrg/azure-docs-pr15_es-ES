<properties 
   pageTitle="Agregar almacenamiento de Azure mediante servicios conectados en Visual Studio | Microsoft Azure"
   description="Agregar almacenamiento de Azure a su aplicación utilizando el cuadro de diálogo Servicios de conectados agregar de Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="adding-azure-storage-by-using-visual-studio-connected-services"></a>Adición de almacenamiento de Azure mediante servicios conectados de Visual Studio

## <a name="overview"></a>Información general

Con Visual Studio de 2015, puede conectar cualquier servicio de nube de C#, servicio móvil de back-end de .NET, sitio Web ASP.NET o servicio, servicio de 5 ASP.NET o servicio de Azure WebJob con Azure almacenamiento mediante el cuadro de diálogo **Agregar servicios conectados** . La funcionalidad de servicio conectado agrega todas las referencias requeridas y código de conexión y modifica los archivos de configuración de forma adecuada. El cuadro de diálogo también le lleva a la documentación que indica que hay los siguientes pasos iniciar el almacenamiento de blobs, colas y tablas.

## <a name="supported-project-types"></a>Tipos de proyectos compatibles

Puede usar el cuadro de diálogo servicios conectados para conectarse al almacenamiento de Azure en los siguientes tipos de proyecto.

- Proyectos Web de ASP.NET

- ASP.NET 5 proyectos

- Rol de Web de servicio de nube de .NET y proyectos de la función de trabajo

- Proyectos de servicios móviles.

- Proyectos de Azure WebJob


## <a name="connect-to-azure-storage-using-the-connected-services-dialog"></a>Conectarse al almacenamiento de Azure usando el cuadro de diálogo servicios conectados

1. Asegúrese de que dispone de una cuenta de Azure. Si no tiene una cuenta de Azure, puede registrarse para una [prueba gratuita](http://go.microsoft.com/fwlink/?LinkId=518146). Una vez que tenga una cuenta de Azure, puede crear cuentas de almacenamiento, crear servicios móviles y configurar Azure Active Directory.

1. Abra el proyecto de Visual Studio, abra el menú contextual para el nodo **referencias** en el Explorador de soluciones y, a continuación, elija **Agregar servicios conectados**.

    ![Agregar un servicio conectado](./media/vs-azure-tools-connected-services-storage/IC796702.png)

1. En el cuadro de diálogo **Agregar servicio conectado** , elija **El almacenamiento de Azure**y, a continuación, elija el botón **Configurar** . Es posible que se le pregunte si para iniciar sesión en Azure si aún no lo ha hecho.

    ![Cuadro de diálogo servicio conectado - almacenamiento agregar](./media/vs-azure-tools-connected-services-storage/IC796703.png)

1. En el cuadro de diálogo de **Almacenamiento de Azure** , seleccione una cuenta de almacenamiento existente y seleccione **Agregar**.

    Si necesita crear una nueva cuenta de almacenamiento, vaya al paso siguiente. En caso contrario, vaya al paso 6.

    ![Cuadro de diálogo de almacenamiento de Azure](./media/vs-azure-tools-connected-services-storage/IC796704.png)

1. Para crear una nueva cuenta de almacenamiento: 

    1. Elija el botón **crear una nueva cuenta de almacenamiento** en la parte inferior del cuadro de diálogo de almacenamiento de Azure.

    1. Rellene el cuadro de diálogo **Crear cuenta de almacenamiento** y, a continuación, elija el botón **crear** .
    
        ![Cuadro de diálogo de almacenamiento de Azure](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)

        Cuando esté en el cuadro de diálogo de **Almacenamiento de Azure** , el almacenamiento nuevo aparece en la lista.

    1. Seleccione el almacenamiento nuevo en la lista y seleccione **Agregar**.

1. El servicio de almacenamiento conectado aparece bajo el nodo de referencias de servicio de su proyecto WebJob.

    ![Almacenamiento de Azure en el proyecto de trabajos de web](./media/vs-azure-tools-connected-services-storage/IC796705.png)

1. Revise la página de introducción que aparece y averiguar cómo se ha modificado el proyecto. Una página de introducción aparece en el explorador cuando se agrega un servicio conectado. Puede revisar los pasos siguientes sugeridos y ejemplos de código, o cambie a la página ¿Qué ha ocurrido para ver qué referencias se han agregado a su proyecto y cómo se modificaron los archivos de código y la configuración.

## <a name="how-your-project-is-modified"></a>¿Cómo se modifica el proyecto

Cuando haya terminado, el cuadro de diálogo, Visual Studio agrega referencias y modifica determinados archivos de configuración. Los cambios específicos dependen del tipo de proyecto. 

 - Para los proyectos ASP.NET, vea [¿Qué ha ocurrido: proyectos ASP.NET](http://go.microsoft.com/fwlink/p/?LinkId=513126). 
 - Para proyectos de 5 ASP.NET, vea [¿Qué ha ocurrido: proyectos de 5 ASP.NET](http://go.microsoft.com/fwlink/p/?LinkId=513124). 
 - Para nube proyectos de servicio (funciones web y funciones de trabajo), vea [¿Qué ha ocurrido: servicio de nube de proyectos](http://go.microsoft.com/fwlink/p/?LinkId=516965). 
 - Para proyectos de WebJob, vea [¿Qué ha ocurrido - WebJob proyectos](./storage/vs-storage-webjobs-what-happened.md).

## <a name="next-steps"></a>Pasos siguientes

1. ¡Con los ejemplos de código Introducción como guía, cree el tipo de almacenamiento que desee y empiece a escribir el código para obtener acceso a su cuenta de almacenamiento!

1. Formular preguntas y obtener ayuda
     - [El foro MSDN: Almacenamiento de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)

     - [Blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/)

     - [Almacenamiento en azure.microsoft.com](https://azure.microsoft.com/services/storage/)

     - [Documentación de almacenamiento en azure.microsoft.com](https://azure.microsoft.com/documentation/services/storage/)

