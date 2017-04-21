<properties 
   pageTitle="Obtener acceso a las nubes privadas Azure con Visual Studio | Microsoft Azure"
   description="Obtenga información sobre cómo obtener acceso a recursos de nube privada mediante Visual Studio."
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

# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Obtener acceso a las nubes privadas Azure con Visual Studio

##<a name="overview"></a>Información general

De forma predeterminada, Visual Studio es compatible con los extremos del resto de nube pública de Azure. Esto puede ser un problema, sin embargo, si está usando Visual Studio con una nube privada de Azure. Puede usar certificados para configurar Visual Studio para tener acceso a los extremos del resto de nube privada de Azure. Puede obtener estos certificados a través de su Azure publicación el archivo de configuración.

## <a name="to-access-a-private-azure-cloud-in-visual-studio"></a>Para obtener acceso a un Azure privada nube en Visual Studio

1. En el [portal de clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885) para la nube privada, descargue el archivo de configuración de publicación o póngase en contacto con el Administrador de un archivo de configuración de publicación. En la versión pública de Azure, el vínculo para descargar esto es [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/). (El archivo que descarga debe tener una extensión .publishsettings).

1. En el **Explorador de servidores** en Visual Studio, elija el nodo de **Azure** y, en el menú contextual, elija el comando **Administrar suscripciones** .

    ![Administrar el comando de suscripciones](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. En el cuadro de diálogo **Administrar suscripciones a Microsoft Azure** , elija la ficha **certificados** y, a continuación, elija el botón **Importar** .

    ![Importar certificados de Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. En el cuadro de diálogo **Importar Microsoft Azure suscripciones** , vaya a la carpeta donde guardó el archivo de configuración de publicación y elija el archivo y luego seleccione el botón **Importar** . Esto importa los certificados en el archivo de configuración de publicación en Visual Studio. Ahora debería poder interactuar con los recursos de nube privada.

    ![Importar configuración de publicación](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

## <a name="next-steps"></a>Pasos siguientes

[Publicación en un servicio de nube Azure desde Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

[Cómo: descargar e importar publicarán información de suscripción y configuración](https://msdn.microsoft.com/library/dn385850(v=nav.70).aspx)

