<properties
    pageTitle="Cómo migrar aplicaciones lógica esquema versión 2015-08-01-vista previa | Servicio de aplicaciones de Microsoft Azure"
    description="Puede migrar fácilmente sus aplicaciones de lógica a la última versión de esquema. Siga estos pasos."
    services="logic-apps"
    documentationCenter=""
    authors="MSFTMAN"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="deonhe"/>

# <a name="how-to-migrate-logic-apps-to-schema-version-2015-08-01-preview"></a>Cómo migrar aplicaciones lógica esquema versión 2015-08-01-vista previa

Para mover sus aplicaciones de lógica existente al nuevo esquema, haga lo siguiente:  
1. Abra la aplicación de la lógica en el portal de Azure  
2. Haga clic en Actualizar esquema:

 ![Icono de la API][step1]   
La página Actualizar esquema muestra y proporciona un vínculo a un documento que proporcionan información detallada sobre las mejoras en el nuevo esquema: ![API de icono][step2]

>[AZURE.NOTE] Cuando se selecciona el **Esquema de actualización**, estamos automáticamente ejecutar los pasos de migración y prever la salida de código. Puede utilizar esto para actualizar la definición, sin embargo, asegúrese de que seguir buenas prácticas de codificación como los descritos en la sección **prácticas recomendadas** .

## <a name="best-practices-when-migrating-your-logic-apps-to-the-latest-schema-version"></a>Procedimientos recomendados para migrar sus aplicaciones de lógica a la última versión de esquema:  

- Copiar la secuencia de comandos migrado a una nueva aplicación de lógica: no se sobrescriben a los antiguos uno hasta que se ha completado las pruebas y confirmado la aplicación migrada funciona según lo esperado.
- Pruebe su lógica aplicación **antes de** poner en producción
- Una vez completada la migración, empezar a actualizar las aplicaciones de lógica para usar la [API administradas](./apis-list.md) cuando sea posible. Por ejemplo, puede comenzar a usar Dropbox v2, cada vez que usa DropBox v1.


## <a name="whats-next"></a>¿Qué es la siguiente
-  [Obtenga información sobre cómo migrar manualmente las aplicaciones de lógica](../app-service-logic/app-service-logic-schema-2015-08-01.md)


<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png






