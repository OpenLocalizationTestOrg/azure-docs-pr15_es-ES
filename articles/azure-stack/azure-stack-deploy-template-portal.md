<properties
    pageTitle="Implementar plantillas con el portal de la pila de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo usar el portal de pila de Azure para implementar plantillas."
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="helaw"/>

# <a name="deploy-templates-using-the-azure-stack-portal"></a>Implementar plantillas con el portal de pila de Azure

Usar el portal para implementar plantillas de administrador de recursos de Azure en la prueba de concepto de pila de Azure.

Plantillas de administrador de recursos implementación y aprovisionar todos los recursos de la aplicación en una única operación coordinada.

1.  Inicie sesión en el portal, haga clic en **nuevo**, haga clic en **personalizado**y, a continuación, haga clic en **implementación de la plantilla**.

2.  Haga clic en **Editar plantilla**, a continuación, pegue el código de plantilla JSON en el módulo y, a continuación, haga clic en **Guardar**.

3.  Haga clic en **Editar parámetros**, escriba los valores de los parámetros que se enumeran y, a continuación, haga clic en **Aceptar**.

4.  Haga clic en la **suscripción**, seleccione la suscripción que desea usar y, a continuación, haga clic en **Aceptar**.

5.  Haga clic en el **grupo de recursos**, elija un grupo de recursos existente o cree uno nuevo y, a continuación, haga clic en **Aceptar**.

6.  Haga clic en **crear**. Un nuevo mosaico en el panel realiza un seguimiento del progreso de la implementación de la plantilla.

## <a name="next-steps"></a>Pasos siguientes

[Implementar plantillas con PowerShell](azure-stack-deploy-template-powershell.md)
