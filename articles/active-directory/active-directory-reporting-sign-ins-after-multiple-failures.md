<properties
    pageTitle="Complementos de inicio de sesión después de varios errores"
    description="Crear un informe que indica los usuarios que han iniciado sesión en después de varios errores consecutivos error de inicio de sesión en intentos."
    services="active-directory"
    documentationCenter=""
    authors="SSalahAhmed"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/04/2016"
    ms.author="saah;kenhoff"/>

# <a name="sign-ins-after-multiple-failures"></a>Complementos de inicio de sesión después de varios errores
Este informe indica los usuarios que han iniciado sesión en después de varios errores consecutivos error de inicio de sesión en intentos. Las posibles causas se incluyen:

- Usuario hayan olvidado su contraseña</li><li>Usuario es la víctima de la contraseña correcta adivinar bruta forzar ataque

Resultados de este informe mostrará el número de intentos incorrectos consecutivos sesión antes de la sesión correctamente y una marca de tiempo asociada a la sesión primero correcta.

**Configuración de informe**: puede configurar el número mínimo de inicio de sesión error consecutivo de intentos que deben producirse antes de que se pueden mostrar en el informe. Cuando realiza cambios en esta configuración es importante que tenga en cuenta que estos cambios no se aplicarán a cualquier existente error de inicio de sesión ins que actualmente aparecen en el informe existente. Sin embargo, se aplicará a todos los complementos de inicio de sesión futuros. Solo los puede realizar cambios en este informe por administradores con licencia.


![Complementos de inicio de sesión después de varios errores](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)
