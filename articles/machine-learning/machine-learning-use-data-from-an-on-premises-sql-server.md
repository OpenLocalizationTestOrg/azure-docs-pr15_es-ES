<properties
pageTitle="Usar datos de una base de datos de SQL Server local en aprendizaje de equipo | Azure"
description="Utilizar datos de una base de datos de SQL Server local para realizar análisis avanzado con Azure el aprendizaje."
services="machine-learning"
documentationCenter=""
authors="garyericson"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="machine-learning"
ms.workload="data-services"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="09/16/2016"
ms.author="garye;krishnan"/>

# <a name="perform-advanced-analytics-with-azure-machine-learning-using-data-from-an-on-premises-sql-server-database"></a>Realizar análisis avanzado con el aprendizaje Azure con los datos de una base de datos de SQL Server local


A menudo las empresas que funcionan con datos locales le gustaría aprovechar las ventajas de la escala y agilidad de la nube para su equipo cargas de trabajo de aprendizaje. Pero no quiere interrumpir sus flujos de trabajo y procesos de negocio actuales moviendo sus datos locales a la nube. Aprendizaje de máquina Azure admite ahora lea los datos desde una base de datos de SQL Server local y, a continuación, formación y puntuación un modelo de datos. Ya no debe copiar manualmente y sincronizar los datos entre la nube y el servidor local. En su lugar, ahora puede leer el módulo de **Importar datos** en Azure máquina aprendizaje Studio directamente desde la base de datos de SQL Server local para su formación y puntuación trabajos. 

Este artículo proporciona una descripción general de cómo entrada local datos de SQL server en Azure el aprendizaje. Se supone que está familiarizado con los conceptos de aprendizaje del equipo de Azure como áreas de trabajo, módulos, conjuntos de datos, experimentación, *etcetera*...

> [AZURE.NOTE] Esta característica no está disponible para áreas de trabajo gratuitas. Para obtener más información sobre los precios de aprendizaje y niveles, consulte [Precios de aprendizaje de máquina de Azure](https://azure.microsoft.com/pricing/details/machine-learning/).

<!-- --> 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## <a name="install-the-microsoft-data-management-gateway"></a>Instalar Microsoft Data Management Gateway

Para obtener acceso a una base de datos de SQL Server local en Azure el aprendizaje debe descargar e instalar Microsoft Data Management Gateway. Cuando configure la conexión de puerta de enlace en máquina aprendizaje Studio tendrá la oportunidad de descargar e instalar la puerta de enlace con el cuadro de diálogo de **descarga y la puerta de enlace de datos de registro** que se describe más adelante.

También puede instalar Data Management Gateway antes de tiempo descargando y ejecutando el paquete de instalación MSI desde el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Elija la última versión, seleccionar 32 bits o 64 bits según corresponda en su equipo. El archivo MSI también puede utilizarse para actualizar una puerta de enlace de la administración de datos existentes a la versión más reciente, con todas las opciones que se conservan.

La puerta de enlace tiene los siguientes requisitos previos:

- Las versiones de sistema operativo Windows compatibles son Windows 7, Windows 8 o 8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012 y Windows Server 2012 R2.
- La configuración recomendada para el equipo de puerta de enlace es 2 GHz mínimo, 4 núcleos, 8 GB de RAM y disco de 80 GB.
- Si el equipo host hibernación, la puerta de enlace no podrá responder a solicitudes de datos. Por lo tanto, configure un plan de energía correspondiente en el equipo antes de instalar la puerta de enlace. La instalación de puerta de enlace muestra un mensaje si el equipo está configurado para hibernación.
- Porque la actividad de copia se produce en una frecuencia determinada, el uso de recursos (CPU, memoria) en el equipo también sigue el mismo patrón con pico y tiempos de inactividad. Uso de recursos también depende en gran medida la cantidad de datos que se mueve. Cuando varios trabajos de copia en curso observará Subir durante las horas de uso de recursos. Aunque técnicamente basta la configuración mínima enumerada anteriormente, desea tiene una configuración con más recursos que la configuración mínima dependiendo de su carga específica para movimiento de datos.

Debe tener en cuenta lo siguiente al configurar y usar una puerta de enlace de administración de datos:

- Puede instalar solo una instancia de Data Management Gateway en un único equipo.
- Puede usar una sola puerta de enlace para varios orígenes de datos local.
- Puede conectar varias puertas de enlace en equipos diferentes al mismo origen de datos local.
- Configurar una puerta de enlace para solo un área de trabajo en un momento. Las puertas de enlace no se puede compartir a través de áreas de trabajo en este momento.
- Puede configurar varias puertas de enlace para un área de trabajo único. Por ejemplo, desea utilizar una puerta de enlace que se conecta a los orígenes de datos de prueba durante el desarrollo y una puerta de enlace de producción cuando esté listo para hacer operativa.
- La puerta de enlace no es necesario estar en el mismo equipo que el origen de datos, pero permanecer más cerca del origen de datos reduce el tiempo de la puerta de enlace para conectarse al origen de datos. Le recomendamos que instale la puerta de enlace en un equipo diferente de la que hospeda el origen de datos local para que la puerta de enlace y origen de datos no competir por los recursos.
- Si ya tiene una puerta de enlace instalado en su equipo servir escenarios de Power BI o generador de datos de Azure, instalar una puerta de enlace independiente para el aprendizaje Azure en otro equipo. 

    > [AZURE.NOTE] No puede ejecutar Data Management Gateway y puerta de enlace de Power BI en el mismo equipo.

- Debe usar Data Management Gateway para el aprendizaje Azure incluso si está utilizando Azure ExpressRoute para otros datos. Debe tratar el origen de datos como origen de datos local (es decir, detrás de un firewall) incluso cuando utiliza ExpressRoute y usa Data Management Gateway para establecer la conexión entre el aprendizaje y el origen de datos. 

Puede encontrar información detallada sobre los requisitos previos de instalación, los pasos de instalación y sugerencias para solucionar problemas en el artículo de [mover datos entre orígenes locales y la nube con Data Management Gateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway), comenzando por la sección, [Consideraciones sobre el uso de Data Management Gateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-idtoc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Datos de entrada de la base de datos de SQL Server local en aprendizaje de máquina de Azure


En este tutorial, se configurar una puerta de enlace de administración de datos en un área de trabajo de aprendizaje del equipo de Azure, configúrelo y, a continuación, leer datos desde una base de datos de SQL Server local.

> [AZURE.TIP] Antes de empezar, deshabilitar el bloqueador de ventanas emergentes de su explorador para `studio.azureml.net`. Si está usando el Explorador de Google Chrome, descargue e instale uno de los varios complementos disponibles en el almacén de Web de Google Chrome [Extensión de aplicación una vez haga clic en](https://chrome.google.com/webstore/search/clickonce?_category=extensions).

### <a name="step-1-create-a-gateway"></a>Paso 1: Crear una puerta de enlace

El primer paso es crear y configurar la puerta de enlace para tener acceso a la base de datos SQL en local.

1. Inicie sesión en [Azure máquina aprendizaje Studio](https://studio.azureml.net/Home/) y seleccione el área de trabajo que desea trabajar en.

2. Haga clic en el módulo de **configuración** de la izquierda y, a continuación, haga clic en la pestaña **Puertas de enlace de datos** en la parte superior.

3. Haga clic en **Nueva puerta de enlace de datos** en la parte inferior de la pantalla.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)

4. En el cuadro de diálogo **nueva puerta de enlace de datos** , escriba el **Nombre de la puerta de enlace** y, opcionalmente, agregue una **Descripción**. Haga clic en la flecha situada en la esquina inferior derecha para ir al siguiente paso de la configuración.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)

5. En el diálogo puerta de enlace de datos de descarga y registrar, copie la clave de registro de la puerta de enlace en el Portapapeles.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)

6. <span id="note-1" class="anchor"></span>Si aún no ha descargado e instalado Microsoft Data Management Gateway, a continuación, haga clic en **Descargar la puerta de enlace de administración de datos**. Esto le lleva a Microsoft Download Center donde puede seleccionar la versión de la puerta de enlace que necesita, descárguelo e instalarlo. Puede encontrar información detallada sobre los requisitos previos de instalación, los pasos de instalación y sugerencias para solucionar problemas en las secciones del principio del artículo [mover datos entre orígenes locales y la nube con Data Management Gateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md).

7. Después de instala la puerta de enlace, el Administrador de configuración de Data Management Gateway se abrirá y aparecerá el cuadro de diálogo **registrar la puerta de enlace** . Pegue la **Clave de registro de la puerta de enlace** que ha copiado en el Portapapeles y haga clic en **registrarse**.

8. Si ya tiene una puerta de enlace instalado, ejecute el Administrador de configuración de Data Management Gateway, haga clic en **cambiar la clave**, pegue la  **Clave de registro de la puerta de enlace** que ha copiado en el Portapapeles y haga clic en **Aceptar**.

9. Una vez completada la instalación, se muestra el cuadro de diálogo **registrar la puerta de enlace** para el Administrador de configuración de puerta de enlace de Microsoft Data Management. Pegue la clave de registro de la puerta de enlace que ha copiado en el Portapapeles anterior y haga clic en **registrarse**.

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)

10. La configuración de puerta de enlace está completa cuando se establecen los valores siguientes en la ficha **Inicio** en el Administrador de configuración de puerta de enlace de Microsoft Data Management:

    - **Nombre de puerta de enlace** y el **nombre de instancia** se establecen en el nombre de la puerta de enlace.

    - **El registro** se establece **registrado**.

    - **Estado** se establece en **iniciado**.

    - La barra de estado en la parte inferior muestra **conectado al servicio de nube de puerta de enlace de administración de datos** junto con una marca de verificación verde.

     ![](media/machine-learning-use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

     Azure Studio de aprendizaje de equipo también se actualiza cuando se realiza correctamente el registro.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\gateway-registered.png)

11. En el cuadro de diálogo **descargar y registrar la puerta de enlace de datos** , haga clic en la marca de verificación para completar la configuración. La página de **configuración** muestra el estado de la puerta de enlace como "En línea". En el panel de la derecha encontrará estado y otra información útil.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\gateway-status.png)

12. En el conmutador de administrador de configuración de Microsoft Data Management Gateway en la pestaña de **certificado** . El certificado especificado en esta pestaña se usa para las credenciales para el almacén de datos local que especifique en el portal de cifrado/descifrado. Este es el certificado predeterminado generado. Microsoft recomienda el cambio a su propio certificado que haga una copia en el sistema de administración de certificados. Haga clic en **cambiar** para usar su propio certificado en su lugar.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\data-gateway-configuration-manager-certificate.png)

13. (opcional) Si desea habilitar el registro detallado para solucionar problemas con la puerta de enlace, en la administración de datos de Microsoft Administrador de configuración de puerta de enlace cambie a la ficha **diagnóstico** y Active la opción de **Habilitar el registro detallado para solucionar problemas** . La información de registro puede encontrarse en el Visor de eventos de Windows en **los registros de servicios de aplicaciones y**  - &gt; nodo de **Data Management Gateway** . También puede usar la ficha **diagnóstico** para probar la conexión a un origen de datos local con la puerta de enlace.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\data-gateway-configuration-manager-verbose-logging.png)

Esto completa la puerta de enlace configurar el proceso de aprendizaje del equipo de Azure.
Ahora ya está listo para usar los datos locales.

Puede crear y configurar varias puertas de enlace en Studio para cada área de trabajo. Por ejemplo, tendrá una puerta de enlace que desea conectarse a los orígenes de datos de prueba durante el desarrollo y una puerta de enlace diferente para los orígenes de datos de producción. Aprendizaje de máquina Azure ofrece la flexibilidad para configurar varias puertas de enlace dependiendo de su entorno corporativo. Actualmente no se puede compartir una puerta de enlace entre las áreas de trabajo y solo una puerta de enlace se puede instalar en un único equipo. Para obtener más información sobre la instalación de la puerta de enlace, vea [Consideraciones sobre el uso de Data Management Gateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway) en el artículo de [mover datos entre orígenes locales y la nube con Data Management Gateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Paso 2: Utilizar la puerta de enlace para leer datos desde un origen de datos local

Después de configurar la puerta de enlace, puede agregar un módulo de **Importar datos** a un ensayo que se dirige a los datos de la base de datos de SQL Server local.

1.  En equipo aprendizaje Studio, seleccione la pestaña **experimentación** , haga clic en **+ nuevo** en la esquina inferior izquierda y seleccione **Ensayo en blanco** (o uno de varios experimentación muestra disponible).

2.  Buscar y arrastre el módulo de **Importar datos** en el lienzo de ensayo.

3.  Debajo del lienzo, haga clic en **Guardar como** . Escriba "Azure máquina aprendizaje local SQL Server Tutorial" para el nombre de ensayo, seleccione el área de trabajo y haga clic en la marca de verificación **Aceptar** .

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\experiment-save-as.png)

4.  Haga clic en el módulo de **Importar datos** para seleccionarlo y luego en el panel de **Propiedades** a la derecha del lienzo, seleccione "Base de datos local de SQL" en la lista desplegable de **origen de datos** .

5.  Seleccione la **puerta de enlace de datos** instalado y registrado. Puede configurar otra puerta de enlace seleccionando "(Agregar nueva puerta de enlace de datos...)".

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\import-data-select-on-premises-data-source.png)

6.  Escriba el **nombre del servidor de base de datos** SQL y el **nombre de la base de datos**, junto con la **consulta de base de datos** SQL que desea ejecutar.

7.  Haga clic en **Escriba valores** en **nombre de usuario y contraseña** y escriba sus credenciales de la base de datos. Puede usar autenticación de Windows integrada o la autenticación de SQL Server dependiendo de la configuración de SQL Server local.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\database-credentials.png)
    
    El mensaje "valores requeridos" cambiará a "conjunto de valores" con una marca de verificación verde. Necesitará indicar las credenciales de una vez a menos que cambie la información de la base de datos o la contraseña. Aprendizaje de máquina Azure usa el certificado que haya proporcionado al instalar la puerta de enlace para cifrar las credenciales en la nube. Azure nunca guardará credenciales local sin cifrado.

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\import-data-properties-entered.png)

8.  Haga clic en **Ejecutar** para ejecutar el ensayo.

Una vez que finalice el ensayo ejecutando puede visualizar los datos importados desde la base de datos haciendo clic en el puerto de salida del módulo de **Importar datos** y seleccionar **visualizar**.

Una vez que haya terminado de desarrollar el ensayo, puede implementar y controle el modelo. Mediante el servicio de ejecución de proceso por lotes, datos de la base de datos de SQL Server local configurado en el módulo de **Importar datos** se lea y se utiliza para la puntuación. Aunque puede utilizar el servicio de respuesta de solicitud de datos locales de puntuación, Microsoft recomienda usar el [complemento de Excel](machine-learning-excel-add-in-for-web-services.md) en su lugar. Actualmente, escribir en un servidor local SQL Server base de datos a través de **Exportar datos** no es compatible en su experimentación o servicios web publicados.

