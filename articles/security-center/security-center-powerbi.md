<properties
   pageTitle="Obtener recomendaciones de datos del centro de seguridad de Azure con Power BI | Microsoft Azure"
   description="El paquete de contenido de BI de energía de centro de seguridad de Azure hace que sea fácil encontrar las alertas de seguridad, recomendaciones, ataque recursos y tendencias, en función de un conjunto de datos que se ha creado la creación de informes."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="yurid"/>

# <a name="get-insights-from-azure-security-center-data-with-power-bi"></a>Obtener recomendaciones de datos del centro de seguridad de Azure con Power BI
El [Panel de Power BI](http://aka.ms/azure-security-center-power-bi) para el centro de seguridad de Azure le permite visualizar, analizar y filtrar las recomendaciones y las alertas de seguridad desde cualquier lugar, incluida su dispositivo móvil. Use el panel de Power BI para revelar las tendencias y patrones: las alertas de seguridad de la vista por recurso o la dirección IP de origen y los riesgos de seguridad sin destinatario por recurso o la edad de ataque. 

Puede también mezclar recomendaciones de centro de seguridad y las alertas de seguridad con otros datos de formas interesante, por ejemplo utilizando los datos de [Los registros de auditoría de Azure](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) y [Auditoría de base de datos de SQL Azure](https://powerbi.microsoft.com/blog/monitor-your-azure-sql-database-auditing-activity-with-power-bi/). Ambas ofrecen paneles de Power BI, y también puede exportar datos a Excel para los informes de fácil en el estado de seguridad de los recursos de la nube.

##<a name="using-azure-security-center-dashboard-to-access-power-bi"></a>Usar el panel Centro de seguridad de Azure para tener acceso a Power BI
También puede usar el panel Centro de seguridad de Azure para acceder a informes de Power BI. Siga los pasos para llevar a cabo esta tarea: 

1. En el panel **Centro de seguridad de Azure** , haga clic en el botón **exploración en Power BI** .

    ![Conectarse al centro de seguridad de Azure con Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new10.png) 

2. El módulo de **exploración en Power BI** se abre en el lado derecho, tal como se muestra en la pantalla siguiente:

    ![Conectarse al centro de seguridad de Azure con Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new2.png)

3. Si va a crear paneles de Power BI por primera vez, puede elegir una de las siguientes opciones en el módulo de **exploración en Power BI** : 

    - **Panel de información de seguridad**: elija esta opción si desea crear un panel que incluye el estado de seguridad, subprocesos y las detecciones. Esta opción es más común de rol DevOps que es responsable de analizar su estado de protección y detecta alertas a través de suscripciones.
    - **Panel de administración de directivas**: seleccione esta opción si desea explorar la directiva de administración y la aplicación.  Esta opción es más común para Central de TI que se centra más en gobernanza. Pueden utilizar este panel para obtener visibilidad y perspectivas en cumplimiento de la directiva de seguridad en toda la organización.
    - Si ya dispone de un panel de Power BI, haga clic en **Ir a su panel de Power BI actual**.

4. En este ejemplo, haga clic en la opción **panel de información de seguridad** . Si es la primera vez, se crea un panel de Power BI para el centro de seguridad se le pedirá que instale el módulo de contenido. Haga clic en el botón **obtener** en la ventana de **paquetes de contenido para Power BI** tal como se muestra en la pantalla siguiente:

    ![Panel de información de seguridad del centro de seguridad de Azure](./media/security-center-powerbi/security-center-powerbi-fig1-new3.png)

5. Aparece la ventana de **conectarse a la información sobre seguridad de centro de seguridad de Azure** . Asegúrese de que el método de **autenticación** **oAuth2** tal como se muestra a continuación y haga clic en el botón de **Inicio de sesión** .
    
    ![Autenticación](./media/security-center-powerbi/security-center-powerbi-fig1-new4.png)

6. Se le pedirá autenticarse de nuevo con sus credenciales de Azure. Después de autenticar su panel se creará. Una vez creado el panel de ver un informe con la estructura similar tal como se muestra en la pantalla siguiente:

    ![Panel de Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new5.png)


> [AZURE.NOTE] Una actualización del informe está programada que tenga lugar diariamente. En caso de que se produce un error en esta actualización, lea [Posibles problemas de actualización con el centro de seguridad de Azure Power BI](https://blogs.msdn.microsoft.com/azuresecurity/2016/04/07/azure-security-center-power-bi-refresh-fails/), para obtener más información sobre cómo solucionar problemas.

Aquí puede ver el número de las alertas de seguridad y recomendaciones, así como el número de máquinas virtuales, bases de datos SQL Azure y recursos de red que supervisa el centro de seguridad de Azure.

Vínculo al centro de seguridad de Azure le redirige el portal de Azure. Los gráficos que sea visualizar la información sobre las recomendaciones de seguridad y alertas, incluidos:

- Estado de seguridad de recursos
- Recomendaciones pendientes
- Recomendaciones de VM
- Alertas a lo largo del tiempo
- Han de recursos
- Direcciones IP han

Detrás de cada gráfico, hay información adicional. Seleccione un icono para ver más información. Por ejemplo, el icono de **Estado de seguridad de recursos** muestra detalles adicionales sobre pendientes recomendaciones recursos tal como se muestra en la pantalla siguiente:

![Recomendaciones](./media/security-center-powerbi/security-center-powerbi-fig1-new6.png)

Si hace clic en cualquier línea de este gráfico, los demás va a sombrear y le centrarse únicamente en seleccionado. Para volver al panel, haga clic en **Centro de seguridad de Azure** en la opción de **paneles** en el panel izquierdo de esta página.

> [AZURE.NOTE] Si desea personalizar sus informes agregando campos adicionales o cambiar los efectos visuales existentes, puede modificar el informe. Para obtener más información, lea [interactuar con un informe en la vista de edición de Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-interact-with-a-report-in-editing-view/) .

Los mosaicos de **alertas con el tiempo, ataque recursos** y **Direcciones IP de intruso** tendrá el resultado similar al hacer clic en cada uno de ellos del mismo. Esto ocurre porque el informe agrega información relacionada con las tres variables y llamadas **recursos bajo ataque** tal como se muestra en la pantalla siguiente:

![Recursos de ataque](./media/security-center-powerbi/security-center-powerbi-fig1-new7.png)

En este momento también puede guardar una copia de este informe, imprimir o publicar en la web con las opciones disponibles en el menú **archivo** .

![Menú archivo](./media/security-center-powerbi/security-center-powerbi-fig8.png)

## <a name="exploring-your-azure-security-center-data-with-power-bi-services"></a>Explorar los datos del centro de seguridad de Azure con los servicios de Power BI

Conectarse a los [Servicios de paquete de contenido de Power BI](https://msit.powerbi.com/groups/me/getdata/services) en Power BI y ejecute los siguientes pasos:

1. En la ventana de **Paquete de contenido para Power BI** verá dos opciones tal como se muestra a continuación.

    ![Paquete de contenido para Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new.png)

    >[AZURE.NOTE] Si ya ha ejecutado la primera parte de este artículo se muestra solo una opción, que es la administración de directivas de centro de seguridad de Azure.

2. En este ejemplo, haga clic en **obtener** el mosaico de **Administración de directivas de centro de seguridad de Azure** .

3. En la ventana de **conectarse a la administración de directivas de centro de seguridad de Azure** , asegúrese de seleccionar **oAuth2** en la lista desplegable **Método de autenticación** de tal como se muestra a continuación y haga clic en el botón de **Inicio de sesión** .

    ![Ventana de directiva de administración](./media/security-center-powerbi/security-center-powerbi-fig1-new8.png)

4. Se le redirigirá a una página de autenticación donde debe escribir las credenciales que se va a usar para conectarse al centro de seguridad de Azure. Una vez completado el proceso de autenticación, Power BI comenzará la importación de datos para generar los informes. Durante este período es posible que vea el siguiente mensaje en la esquina derecha del explorador:

    ![Conectarse al centro de seguridad de Azure con Power BI](./media/security-center-powerbi/security-center-powerbi-fig4.png)

    >[AZURE.NOTE] Cuando se crea por primera vez el panel puede tardar más de lo normal, principalmente para escenarios donde tiene varias suscripciones. 

5. Una vez finalizado el proceso, cargará el panel Centro de seguridad de Azure Power BI con el informe de **Administración de directivas de** similar a la que se muestra a continuación:

    ![Panel de administración de la directiva](./media/security-center-powerbi/security-center-powerbi-fig1-new9.png)

## <a name="see-also"></a>Vea también
En este documento, ha aprendido cómo usar Power BI en el centro de seguridad de Azure. Para obtener más información sobre el centro de seguridad de Azure, consulte lo siguiente:

- [Guía de operaciones y planificación de centro de seguridad de Azure](security-center-planning-and-operations-guide.md) : Obtenga información sobre cómo planear la adopción de centro de seguridad de Azure.
- [Configurar directivas de seguridad en el centro de seguridad de Azure](security-center-policies.md) : Obtenga información sobre cómo configurar las opciones de seguridad en el centro de seguridad de Azure
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md) : Obtenga información sobre cómo administrar y responder a las alertas de seguridad
- [Preguntas más frecuentes sobre centro de seguridad de Azure](security-center-faq.md) : preguntas más frecuentes sobre el uso del servicio de búsqueda
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : buscar entradas de blog sobre seguridad de Azure y cumplimiento
