<properties 
    pageTitle="Tareas de administración comunes de servicio de nube (clásico) | Microsoft Azure" 
    description="Obtenga información sobre cómo administrar servicios en la nube en el portal de clásico de Azure." 
    services="cloud-services" 
    documentationCenter="" 
    authors="Thraka" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/10/2016"
    ms.author="adegeo"/>





# <a name="how-to-manage-cloud-services"></a>Cómo administrar servicios en la nube

> [AZURE.SELECTOR]
- [Portal de Azure](cloud-services-how-to-manage-portal.md)
- [Portal de clásico de Azure](cloud-services-how-to-manage.md)

En el área de **Servicios de nube** de Azure portal clásico, puede actualizar una función de servicio o una implementación, promover una implementación preconfigurada de producción, vincular recursos en el servicio de nube para que puedan ver las dependencias de recursos y escalar los recursos de manera conjunta y eliminar un servicio de nube o una implementación.


## <a name="how-to-update-a-cloud-service-role-or-deployment"></a>Cómo: actualizar un rol de servicio de nube o una implementación

Si necesita actualizar el código de la aplicación del servicio de nube, use **Actualizar** en el panel, página de **Servicios de nube** o **instancias de** página. Puede actualizar una única función o todas las funciones. Deberá cargar un nuevo paquete de servicio y el archivo de configuración de servicio.

1. En el [portal de clásica Azure](https://manage.windowsazure.com/), en el panel, página de **Servicios de nube** o **instancias de** página, haga clic en **Actualizar**.

    ![UpdateDeployment](./media/cloud-services-how-to-manage/CloudServices_UpdateDeployment.png)

2. En la **etiqueta de implementación**, escriba un nombre para identificar la implementación (por ejemplo, mycloudservice4). En el panel encontrará la etiqueta de implementación en **Inicio rápido** .

3. En el **paquete**, use **Examinar** para cargar el archivo de paquete de servicio (.cspkg).

4. En **configuración**, use **Examinar** para cargar el archivo de configuración de servicio (.cscfg).

5. Seleccionar **todo** en **función de**si desea actualizar todos los roles en el servicio de nube. Para realizar una actualización única función, seleccione el rol que desea actualizar. Incluso si se selecciona un rol específico para la actualización, las actualizaciones en el archivo de configuración de servicio se aplican a todos los roles.

6. Si la actualización cambia el número de roles o el tamaño de una función, seleccione la casilla de verificación **Permitir actualizar si cambia los tamaños de papel o el número de funciones** para habilitar la actualización continuar. 

    Tenga en cuenta que si cambia el tamaño de una función (es decir, el tamaño de una máquina virtual que aloja una instancia de funciones) o el número de funciones, cada instancia de rol (máquina virtual) se debe renuevan y se perderán los datos locales.

7. Si los roles de servicio tienen solo una instancia de rol, seleccione la **actualización incluso si una o más funciones contienen una casilla de verificación de instancia única** para habilitar la actualización continuar. 

    Azure sólo puede garantizar disponibilidad del servicio 99,95 por ciento durante una actualización de servicio de nube si cada rol tiene al menos dos instancias de función (máquinas virtuales). Que permite una máquina virtual procesar las solicitudes de cliente mientras se actualiza el otro.

8. Haga clic en **Aceptar** (marca) para iniciar la actualización del servicio.



## <a name="how-to-swap-deployments-to-promote-a-staged-deployment-to-production"></a>Cómo: intercambiar implementaciones para promover una implementación preconfigurada de producción

Use **intercambiar** para promover una implementación de un servicio de nube de producción provisional. Cuando decide implementar una nueva versión de un servicio de nube, puede ensayar y probar la nueva versión en su entorno de ensayo de servicio de nube mientras los clientes están utilizando la versión actual de producción. Cuando esté listo para promover la nueva versión de producción, puede usar **intercambiar** para cambiar las direcciones URL que se tratan las dos implementaciones. 

Puede intercambiar implementaciones desde la página de **Servicios de nube** o el panel.

1. En el [portal de clásica Azure](https://manage.windowsazure.com/), haga clic en **Servicios en la nube**.

2. En la lista de servicios en la nube, haga clic en el servicio de nube para seleccionarlo.

3. Haga clic en **intercambiar**.

    Se abre el siguiente mensaje de confirmación.

    ![Intercambio de servicios de nube](./media/cloud-services-how-to-manage/CloudServices_Swap.png)

4. Después de comprobar la información de implementación, haga clic en **Sí** para intercambiar las implementaciones.

    Intercambio de implementación rápidamente ocurre porque lo único que cambia es las direcciones IP virtuales (VIP) para las implementaciones.

    Para guardar los costos de proceso, puede eliminar la implementación en el entorno de ensayo cuando esté seguro de que la implementación de producción nuevo está realizando según lo esperado.

## <a name="how-to-link-a-resource-to-a-cloud-service"></a>Cómo: vincular un recurso a un servicio de nube

Para mostrar las dependencias del servicio de la nube en otros recursos, puede vincular una instancia de base de datos de SQL Azure o una cuenta de almacenamiento del servicio de nube. Puede vincular y desvincular recursos en la página de **Recursos vinculados** y, a continuación, supervisar su uso en el panel de servicio de nube. Si tiene una cuenta de almacenamiento vinculado supervisión activado, puede supervisar solicitudes de Total en el panel de servicio de nube.

Utilice el **vínculo** para vincular una cuenta nueva o existente de SQL instancia o almacenamiento a su servicio de nube. A continuación, puede escalar junto con el rol de servicio de nube que se usa en la página de la **escala de** la base de datos. (Una cuenta de almacenamiento automáticamente escala como aumenta el uso). Para obtener más información, vea [cómo ajustar el tamaño de un servicio de nube y recursos vinculados](cloud-services-how-to-scale.md). 

También puede supervisar, administrar y ajustar el tamaño de la base de datos en el nodo de **bases de datos** de Azure portal clásico. 

"Vinculación" un recurso en este sentido, la aplicación no conectarse al recurso. Si crea una nueva base de datos con el **vínculo**, debe agregar las cadenas de conexión a su código de la aplicación y, a continuación, actualizar el servicio de nube. También deberá agregar cadenas de conexión, si su aplicación usa los recursos en una cuenta de almacenamiento vinculado.

El procedimiento siguiente describe cómo vincular una nueva instancia de base de datos de SQL, implementada en un nuevo servidor de base de datos SQL, a un servicio de nube.

### <a name="to-link-a-sql-database-instance-to-a-cloud-service"></a>Vincular una instancia de base de datos SQL a un servicio de nube

1. En el [portal de clásica Azure](http://manage.windowsazure.com/), haga clic en **Servicios en la nube**. A continuación, haga clic en el nombre del servicio de nube para abrir el panel.

2. Haga clic en **recursos vinculados**.

    Se abrirá la página de **Recursos vinculados** .

    ![LinkedResourcesPage](./media/cloud-services-how-to-manage/CloudServices_LinkedResourcesPage.png)

3. Haga clic en el **vínculo de un recurso** o **vínculo**.

    Se inicia el Asistente para **Recursos de vínculo** .

    ![Vínculo Page1](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkPage1.png)

4. Haga clic en **crear un nuevo recurso** o **vincular un recurso existente**.

5. Elija el tipo de recurso al que vincular. En el [portal de clásica Azure](http://manage.windowsazure.com/), haga clic en **Base de datos de SQL**. (El portal de clásico de Azure de vista previa no admite la vinculación de una cuenta de almacenamiento a un servicio de nube.)

6. Para completar la configuración de la base de datos, siga las instrucciones de la ayuda para el área de **Bases de datos SQL** del portal clásica Azure.

    Puede seguir el progreso de la operación de vinculación en el área de mensaje.

    ![Progreso del vínculo](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkProgress.png)

    Cuando termine de vinculación, puede supervisar el estado del recurso vinculado en el panel de servicio de nube. Para obtener información sobre la escala de una base de datos SQL vinculada, vea [cómo ajustar el tamaño de un servicio de nube y recursos vinculados](cloud-services-how-to-scale.md).

### <a name="to-unlink-a-linked-resource"></a>Desvincular un recurso vinculado

1. En el [portal de clásica Azure](http://manage.windowsazure.com/), haga clic en **Servicios en la nube**. A continuación, haga clic en el nombre del servicio de nube para abrir el panel.

2. Haga clic en **Recursos vinculados**y, a continuación, seleccione el recurso.

3. Haga clic en **Desvincular**. A continuación, haga clic en **Sí** en el mensaje de confirmación.

    Desvincular una base de datos de SQL no tiene ningún efecto en la base de datos o conexiones de la aplicación a la base de datos. Aún puede administrar la base de datos en el área de **Bases de datos SQL** del portal clásico Azure.



## <a name="how-to-delete-deployments-and-a-cloud-service"></a>Cómo: eliminar implementaciones y un servicio de nube

Antes de eliminar un servicio de nube, debe eliminar cada implementación existente.

Para guardar los costos de proceso, puede eliminar la implementación de ensayo después de comprobar que la implementación de producción funciona según lo esperado. Son los costos de proceso facturado para instancias de la función incluso si no se está ejecutando un servicio de nube.

Use el procedimiento siguiente para eliminar una implementación o su servicio de nube. 

1. En el [portal de clásica Azure](http://manage.windowsazure.com/), haga clic en **Servicios en la nube**.

2. Seleccione el servicio de nube y, a continuación, haga clic en **Eliminar**. (Para seleccionar un servicio de nube sin tener que abrir el panel, haga clic en cualquier lugar excepto el nombre de la entrada de servicio de nube.)

    Si tiene una implementación de ensayo o de producción, verá un menú de opciones similares a la siguiente en la parte inferior de la ventana. Antes de poder eliminar el servicio de nube, debe eliminar cualquier implementación existente.

    ![Eliminar menú](./media/cloud-services-how-to-manage/CloudServices_DeleteMenu.png)


3. Para eliminar una implementación, haga clic en **eliminar la implementación de producción** o **eliminar provisional implementación**. A continuación, en el mensaje de confirmación, haga clic en **Sí**. 

4. Si va a eliminar el servicio de nube, repita el paso 3, si es necesario, para eliminar la implementación de otra.

5. Para eliminar el servicio de nube, haga clic en **servicio de nube de eliminar**. A continuación, en el mensaje de confirmación, haga clic en **Sí**.

> [AZURE.NOTE]
> Si supervisión detallado está configurado para su servicio de nube, Azure no elimine los datos de supervisión de su cuenta de almacenamiento cuando se elimina del servicio de nube. Debe eliminar los datos manualmente. Para obtener información acerca de dónde buscar las tablas métricas, consulte "Cómo: acceso detallado supervisar datos externos al portal clásico Azure" en [cómo supervisar servicios en la nube](cloud-services-how-to-monitor.md).

## <a name="next-steps"></a>Pasos siguientes

 * [Configuración general de su servicio de nube](cloud-services-how-to-configure.md).
* Obtenga información sobre cómo [implementar un servicio de nube](cloud-services-how-to-create-deploy.md).
* Configurar un [nombre de dominio personalizado](cloud-services-custom-domain-name.md).
* Configurar [certificados ssl](cloud-services-configure-ssl-certificate.md).
