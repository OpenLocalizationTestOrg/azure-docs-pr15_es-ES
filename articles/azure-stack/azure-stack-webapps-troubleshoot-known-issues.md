<properties
    pageTitle="Web de aplicaciones en la pila de Azure: problemas conocidos y solución de problemas | Microsoft Azure"
    description="Instrucciones detalladas para implementar aplicaciones Web de pila de Azure"
    services="azure-stack"
    documentationCenter=""
    authors="apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>
    
# <a name="web-apps-resource-provider---known-issues-and-troubleshooting"></a>Proveedor de recursos de aplicaciones Web: problemas conocidos y solución de problemas

> [AZURE.NOTE] La siguiente información solo se aplica a implementaciones TP1 de pila de Azure.

Si tiene problemas durante la implementación o trabajar con aplicaciones Web de pila de Azure, consulte las instrucciones a continuación.

## <a name="azure-stack-web-apps-not-available-in-the-portal"></a>Azure pila Web aplicaciones no está disponible en el portal

![Aplicaciones Web de pila Azure crear nueva aplicación Web][1]

Después de completar el [registro de proveedor de aplicaciones de Azure pila Web](azure-stack-webapps-deploy.md#register-the-newly-deployed-azure-stack-web-apps-provider-with-arm) si no se encuentre la Web + módulo móvil a continuación, intente lo siguiente:
* **Cierre de sesión del portal** y **Cierre el explorador** y, a continuación, en un **Nuevo inicio de sesión de ventana de explorador en el portal** y vuelva a intentarlo.

Si aún no puede ver la web y móvil módulo intente lo siguiente:

1.  En el **Equipo de Host de pila de Azure** en el **Administrador de Hyper-V** localizar el **xRPVM** y **conectarse** a la máquina virtual.
2.  Abra un **símbolo del sistema como administrador** y ejecute **IISRESET**
3.  Volver a la **ClientVM** y abrir una **nueva ventana del explorador**, **inicie sesión en el portal** y vuelva a intentarlo.

## <a name="deployment-fails-when-creating-a-web-app-in-a-new-resource-group"></a>Implementación se produce un error al crear una aplicación Web en un nuevo grupo de recursos

En la primera vista previa de aplicaciones Web, **Todas las aplicaciones Web** debe crearse en el mismo grupo de recursos, como el proveedor de recursos de aplicaciones Web (**AppService LOCAL**).  Este es un problema conocido y se resolverá en una vista previa futura.

## <a name="other-issues"></a>Otros problemas

Si encuentra otros publican problemas con aplicaciones Web de pila de Azure en [el foro de la pila de Azure] (https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) donde los miembros de nuestro equipo supervisa publicaciones.


<!--Image references-->
[1]: ./media/azure-stack-webapps-troubleshoot-known-issues/NewWebandMobile.png



<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[WebAppsDeployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
