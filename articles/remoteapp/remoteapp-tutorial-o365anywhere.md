<properties
   pageTitle="Obtener la misma experiencia de Office 365 en cualquier dispositivo con RemoteApp de Azure | Microsoft Azure"
   description="Aprenda a compartir cualquier aplicación de Office 365 con sus usuarios mediante RemoteApp de Azure."
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="guscatal;elizapo"/>


# <a name="get-the-same-office-365-experience-on-any-device-with-azure-remoteapp"></a>Obtener la misma experiencia de Office 365 en cualquier dispositivo con RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Este artículo se trata sobre cómo implementar Office 365 en cualquier dispositivo de la empresa. Los usuarios pueden obtener las mismas capacidades y la experiencia de interfaz de usuario Android, Apple y Windows.

Esto se realizar mediante RemoteApp de Azure, Office 365 de hospedaje en máquinas virtuales de capaz de escala en Azure que los usuarios puedan conectarse a. Este conjunto de máquinas virtuales llamamos a una colección de"nube".

## <a name="create-a-cloud-collection"></a>Crear una colección de nube

En primer lugar después de haber creado una cuenta de Azure, vaya a **RemoteApp** haciendo clic en el vínculo en el lado izquierdo.
![Mostrar RemoteApp Azure en el Portal de Azure](./media/remoteapp-tutorial-o365anywhere/1-menu.png)

Continúe haciendo clic en **nuevo** en la parte inferior y "crear rápida" una colección. Proporcione un nombre, la región, la suscripción, el plan y la imagen "Proffesional de Office 2013" que proporcionamos.
![Cuadro de diálogo Crear](./media/remoteapp-tutorial-o365anywhere/2-quickcreate.png)

Cuando haya terminado el formulario que se debe iniciar el proceso de creación de la colección. Esto puede tardar una hora o lo.

![En espera](./media/remoteapp-tutorial-o365anywhere/3-waiting.png)

Una vez finalizado el proceso, tendrá un aspecto parecido al siguiente. Si se haga clic en **publicación** podemos ver que la mayoría de las aplicaciones de Office se han publicado nos ya.
![Colección creada](./media/remoteapp-tutorial-o365anywhere/4-done.png)

![Aplicaciones publicadas](./media/remoteapp-tutorial-o365anywhere/5-publish.png)

En este momento también puede agregar más usuarios que tienen acceso a esta colección haciendo clic en **Acceso de usuario**.
![Configurar el acceso de usuario](./media/remoteapp-tutorial-o365anywhere/6-user.png)

Ahora vamos a probar a conectarse a Office 365!

## <a name="connect-to-office-365"></a>Conectarse a Office 365

Se le vaya sobre a [https://www.remoteapp.windowsazure.com/](https://www.remoteapp.windowsazure.com/), desplácese hacia abajo y haga clic en **descargar los clientes** para instalar al cliente de RemoteApp de Azure en el dispositivo que está usando. Las capturas de pantalla siguientes son para Windows.

Una vez que inicie la aplicación, se le pedirá que inicie sesión con su cuenta de Microsoft (anteriormente denominado un "Live ID"), utilice el mismo como su cuenta de Azure por ahora. Cuando haya iniciado sesión en debe ver una notificación sobre las invitaciones de nuevas, haga clic ahí y debería ver una lista de uno a continuación. Aceptar la invitación que coincida con el correo electrónico de propietario de cuenta de Azure.

![Nueva invitación](./media/remoteapp-tutorial-o365anywhere/7-araclient.png)

Su apariencia cuando hay invitaciones nuevas.

![Aceptar una aplicación](./media/remoteapp-tutorial-o365anywhere/8-invitation.png)

Una vez que aceptar la invitación debería ver todas las aplicaciones de Office en el cliente de RemoteApp de Azure.

![Lista de aplicaciones](./media/remoteapp-tutorial-o365anywhere/9-work.png)

Al hacer clic en cualquiera de estas que debe iniciar la aplicación de la máquina virtual de Azure y deben ser establecen! Disfrute.

![iniciar](./media/remoteapp-tutorial-o365anywhere/10-arastart.png)

![PowerPoint](./media/remoteapp-tutorial-o365anywhere/11-pp.png)
