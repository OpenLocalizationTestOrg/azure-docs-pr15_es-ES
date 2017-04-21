<properties
   pageTitle="Cómo crear una incidencia de soporte técnico para el almacén de datos SQL | Microsoft Azure"
   description="Cómo crear una incidencia de soporte técnico en el almacén de datos de SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/01/2016"
   ms.author="sonyama;barbkess"/>

# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Cómo crear una incidencia de soporte técnico para el almacén de datos SQL
 
Si tiene problemas con el almacén de datos SQL, cree vales de soporte técnico para que le ayudará a nuestro equipo de ingeniería.

## <a name="create-a-support-ticket"></a>Crear una incidencia de soporte técnico

1. Abra el [portal de Azure][].

2. En la pantalla de inicio, haga clic en el icono de **ayuda + soporte técnico** .

    ![Ayuda + soporte técnico](./media/sql-data-warehouse-get-started-create-support-ticket/help-support.png)

3. En el menú Ayuda + módulo de compatibilidad, haga clic en **Crear solicitud de soporte técnico**.

    ![Nueva solicitud de soporte técnico](./media/sql-data-warehouse-get-started-create-support-ticket/create-support-request.png)
    
    <a name="request-quota-change"></a> 

4. Seleccione el **tipo de solicitud**.

    ![Tipo de solicitud](./media/sql-data-warehouse-get-started-create-support-ticket/request-type.png)
    
    >[AZURE.NOTE]  De forma predeterminada, cada servidor de SQL server (por ejemplo, myserver.database.windows.net) tiene una **Cuota DTU** de 45.000. Esta cuota es simplemente un límite de seguridad. Puede aumentar su cuota creando una incidencia de soporte técnico y seleccionar *cuota* como el tipo de solicitud. Para calcular la DTU necesita, multiplique el 7.5 total [que DWU][] es necesario. Por ejemplo, que le gustaría alojar dos DW6000s en un servidor SQL server, debe solicitar una cuota DTU de 90.000.  Puede ver su consumo DTU actual desde el módulo SQL server en el portal. Bases de datos pausados y anular pausados cuentan en la cuota DTU. 

5. Seleccione la **suscripción** que hospeda la base de datos con el problema que está informando.

    ![Suscripción](./media/sql-data-warehouse-get-started-create-support-ticket/subscription.png)

6. Seleccione el **almacén de datos SQL** como el recurso.

    ![Recursos](./media/sql-data-warehouse-get-started-create-support-ticket/resource.png)

7. Seleccione el [plan de asistencia de Azure][].

    - Soporte de **facturación, administración de cuota y suscripción** está disponible en todos los niveles de soporte técnico.
    - Soporte técnico de **reparación** se proporciona a través de [desarrollador][], [estándar][], [Professional directa][] o [Premier][] support. Salto de solucionar problemas son problemas experimentados por los clientes con el uso de Azure donde hay un esperadas razonable que Microsoft ha causado el problema.
    - **Programador tutores** y **Servicios de consultoría** están disponibles en los niveles de soporte técnico [Premier][] y [Professional directa][] . 
    
    Si tiene un Premier plan de asistencia, también puede informar de almacén de datos SQL relacionados con problemas en el [portal en línea de Microsoft Premier][].  Para obtener más información sobre los distintos planes de soporte técnico, incluidos el ámbito, los tiempos de respuesta, precios, etcetera, vea [plan de soporte técnico de Azure][plan de asistencia de Azure] .  Para las preguntas más frecuentes acerca de Azure compatibilidad, consulte [preguntas más frecuentes de soporte técnico de Azure][].  

    ![Plan de soporte técnico](./media/sql-data-warehouse-get-started-create-support-ticket/support-plan.png)

8. Seleccione el **tipo de problema** y la **categoría**.

    ![Categoría de tipo de problema](./media/sql-data-warehouse-get-started-create-support-ticket/problem-type-category.png)

9. Describa el problema y elija el nivel de impacto en el negocio.

    ![Descripción del problema](./media/sql-data-warehouse-get-started-create-support-ticket/problem-description.png)

10. Su **información de contacto** para esta incidencia de soporte técnico se rellenará automáticamente. Actualizar esta opción si es necesario.

    ![Información de contacto](./media/sql-data-warehouse-get-started-create-support-ticket/contact-info.png)

11. Haga clic en **crear** para enviar la solicitud de soporte técnico.


## <a name="monitor-a-support-ticket"></a>Supervisar una incidencia de soporte técnico

Después de que ha enviado la solicitud de soporte técnico, el equipo de soporte de Azure se comunicará con usted. Para comprobar el estado de la solicitud y detalles, haga clic en **Administrar solicitudes de soporte** en el panel.

![Comprobar estado](./media/sql-data-warehouse-get-started-create-support-ticket/check-status.png)

## <a name="other-resources"></a>Otros recursos

Además, puede conectarse con la Comunidad de almacén de datos SQL en [Desbordamiento de pila][] o en el [foro de MSDN de almacén de datos de SQL Azure][].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

<!--MSDN references--> 

<!--Other web references--> 
[Portal de Azure]: https://portal.azure.com/
[Plan de soporte técnico de Azure]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Programador]: https://azure.microsoft.com/support/plans/developer/  
[Estándar]: https://azure.microsoft.com/support/plans/standard/  
[Directos profesional]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Preguntas más frecuentes de soporte técnico de Azure]: https://azure.microsoft.com/support/faq/
[Portal en línea de Microsoft Premier]: https://premier.microsoft.com/
[Desbordamiento de pila]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Foro de MSDN de almacén de datos de SQL Azure]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

