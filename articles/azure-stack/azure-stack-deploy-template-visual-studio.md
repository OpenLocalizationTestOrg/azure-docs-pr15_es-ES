<properties
    pageTitle="Implementar plantillas de Visual Studio en pila de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo implementar plantillas de Visual Studio en pila de Azure."
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

# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Implementar plantillas de pila de Azure con Visual Studio

Use Visual Studio para implementar plantillas de administrador de recursos de Azure en la prueba de concepto de pila de Azure.

Plantillas de administrador de recursos implementación y aprovisionar todos los recursos de la aplicación en una única operación coordinada.

1.  Abra Visual Studio 2015 Update 1.

2.  Haga clic en **archivo**, haga clic en **nuevo**y, en el cuadro de diálogo **Nuevo proyecto** , haga clic en **Grupo de recursos de Azure**.

3.  Escriba un **nombre** para el nuevo proyecto y, a continuación, haga clic en **Aceptar**.

4.  En el cuadro de diálogo **Seleccionar plantilla de Azure** , haga clic en la **Máquina Virtual de Windows**y, a continuación, haga clic en **Aceptar**.

  En el nuevo proyecto, puede ver una lista de las plantillas disponibles, expanda el nodo de **plantillas** en el panel **Explorador de soluciones** .

5.  En el panel **Explorador de soluciones** , haga clic en el nombre del proyecto, haga clic en **implementar**y haga clic en **Nueva implementación**.

6.  En el cuadro de diálogo **implementar al grupo de recursos** , en la lista desplegable **suscripción** , seleccione la suscripción de Microsoft Azure pila.

7.  En la lista del **Grupo de recursos** , elija un grupo de recursos existente o cree uno nuevo.

8.  En la lista **ubicación de grupo de recursos** , elija una ubicación y, a continuación, haga clic en **implementar**.

9.  En el cuadro de diálogo **Editar parámetros** , escriba valores para los parámetros (que varían según la plantilla) y, a continuación, haga clic en **Guardar**.

## <a name="next-steps"></a>Pasos siguientes

[Implementar plantillas de la línea de comandos](azure-stack-deploy-template-command-line.md)
