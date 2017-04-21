<properties
   pageTitle="Cruiser y la integración de la API de facturación de Microsoft Azure en la nube | Microsoft Azure"
   description="Proporciona una perspectiva única de facturación de Microsoft Azure asociado Cruiser de la nube, en sus experiencias integrar las API de facturación de Azure en sus productos.  Esto es especialmente útil para los clientes de Azure y la nube Cruiser interesen mediante/intentando nube Cruiser Pack de Microsoft Azure."
   services=""
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""
   tags="billing"
   />

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="09/08/2016"
   ms.author="mobandyo;sirishap;bryanla"/>

# <a name="cloud-cruiser-and-microsoft-azure-billing-api-integration"></a>Cruiser y la integración de la API de facturación de Microsoft Azure en la nube

Este artículo describe cómo puede utilizarse la información recopilada desde las API de facturación de nuevo Microsoft Azure en la nube Cruiser para análisis y simulación de costo de flujo de trabajo.

## <a name="azure-ratecard-api"></a>API de Azure RateCard
La API de RateCard proporciona información de la tasa de Azure. Después de autenticar con las credenciales adecuadas, puede consultar la API para recopilar metadatos acerca de los servicios disponibles en Azure, junto con los tipos asociados con su identificador de la oferta.

La siguiente es una respuesta de ejemplo de la API que muestra los precios para la A0 instancia (Windows):

    {
        "MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",
        "MeterName": "Compute Hours",
        "MeterCategory": "Virtual Machines",
        "MeterSubCategory": "A0 VM (Windows)",
        "Unit": "Hours",
        "MeterRates":
        {
            "0": 0.029
        },
        "EffectiveDate": "2014-08-01T00:00:00Z",
        "IncludedQuantity": 0.0,
        "MeterStatus": "Active"
    },

### <a name="cloud-cruisers-interface-to-azure-ratecard-api"></a>Interfaz de Cruiser a Azure RateCard API en la nube
Nube Cruiser puede aprovechar la información de la API RateCard de diferentes formas. En este artículo, le mostrará cómo puede utilizarse para realizar IaaS simulación y análisis de costos de carga de trabajo.

Para demostrar este caso de uso, imagine una carga de trabajo de varias instancias que se ejecutan en Microsoft Azure Pack (WAP). El objetivo es simular esta misma carga de trabajo en Azure y estimar los costos de manera tal migración. Para crear esta simulación, hay dos tareas principales que debe realizar:

1. **Importar y proceso recopilada la información del servicio de la API RateCard.** También se realiza esta tarea en los libros, donde el extracto de la API RateCard se transforma y publicado en un nuevo plan de tasa. Este nuevo plan de tasa se utilizará en las simulaciones para estimar los precios de Azure.

2. **Normalizar servicios WAP y servicios Azure IaaS.** De forma predeterminada, los servicios WAP se basan en los recursos individuales (CPU, tamaño de la memoria, tamaño de disco, etc.) mientras Azure servicios se basan en tamaño de la instancia (A0, A1, A2, etcetera). Esta primera tarea puede ser realiza ETL motor de nube Cruiser, había denominado libros, donde se pueden integrar estos recursos en tamaños de instancia, parecidos a servicios de instancia de Azure.

### <a name="import-data-from-the-ratecard-api"></a>Importar datos de la API RateCard

Nube Cruiser libros proporcionan una forma automatizada para recopilar y procesar la información de la API RateCard.  Libros ETL (carga de transformación extraer) le permiten configurar la colección, la transformación y la publicación de datos en la base de datos de nube Cruiser.

Cada libro puede tener una o varias colecciones, lo que permite relacionar la información de orígenes diferentes de complemento o aumentar los datos de uso. Las capturas de pantalla de dos siguientes muestran cómo crear una nueva *colección* en un libro existente y la información de importación a la *colección* de la API RateCard:

![Ilustración 1: crear una nueva colección][1]

![Ilustración 2: importar datos de la nueva colección][2]

Después de importar los datos en el libro, es posible crear varios pasos y procesos de transformación, modificar y los datos del modelo. En este ejemplo, ya que solo estamos interesados en infraestructura como-servicio (IaaS) podemos usar los pasos de la transformación para quitar filas innecesarias o registros, relacionados con los servicios que no sean IaaS.

La siguiente captura de pantalla muestra los pasos de transformación utilizados para procesar los datos recopilados desde RateCard API:

![Figura 3 - pasos para la transformación para procesar datos recopilados de la API RateCard][3]

### <a name="defining-new-services-and-rate-plans"></a>Planes de definición de tasa y nuevos servicios

Existen diferentes maneras de definir los servicios de nube Cruiser. Una de las opciones es importar los servicios de los datos de uso. Este método se usa generalmente cuando trabaje con nubes públicas, donde los servicios ya están definidos por el proveedor.

Un Plan de tasa es un conjunto de tasas o los precios que se pueden aplicar a diferentes servicios, en función de las fechas de vigencia o grupo de clientes, entre otras opciones. Planes de tasa también pueden usarse en la nube Cruiser para crear simulación o escenarios "Y si", para comprender cómo los cambios en los servicios pueden afectar el costo total de una carga de trabajo.

En este ejemplo, se usará la información del servicio de la API RateCard para definir nuevos servicios en la nube Cruiser. En la misma manera, podemos utilizar para crear un nuevo Plan de tasa en nube Cruiser las tasas asociadas a los servicios.

Al final del proceso de transformación, es posible crear un nuevo paso y publicar los datos de la API RateCard como tasas y nuevos servicios.

![Figura 4 - publicar los datos de la API RateCard como nuevos servicios y tasas][4]

### <a name="verify-azure-services-and-rates"></a>Comprobar las tasas y servicios de Azure

Después de publicar los servicios y las tasas, puede comprobar la lista de servicios importados en la pestaña de *Servicios* de nube Cruiser:

![Figura 5 - comprobar los nuevos servicios][5]

En la pestaña *Tasa de plan* , puede comprobar el nuevo plan de tasa denominado "AzureSimulation" con las tasas importado de la API RateCard.

![Figura 6 - comprobar el nuevo Plan de tasa y tasas de asociados][6]

### <a name="normalize-wap-and-azure-services"></a>Normalizar WAP y servicios de Azure

De forma predeterminada, WAP proporciona información de uso basada en el uso de cálculo, memoria y recursos de red. Puede definir los servicios basados en nube Cruiser, directamente en la asignación o el uso de uso medido de estos recursos. Por ejemplo, puede establecer una tasa básica para cada hora de la CPU o cargar los GB de memoria asignada a una instancia.

En este ejemplo, para comparar los costos entre WAP y Azure, es necesario agregar el uso de recursos en WAP en paquetes, que pueden asignarse a los servicios de Azure. Esta transformación se puede implementar fácilmente en los libros:

![Figura 7 - transformar datos WAP para normalizar servicios][7]

El último paso en el libro es publicar los datos en la base de datos de nube Cruiser. Durante este paso, los datos de uso ahora incorporará servicios (que se asignan a los servicios de Azure) y vinculados a tasas predeterminadas para crear los cargos.

Una vez finalizado el libro, puede automatizar el procesamiento de los datos, agregar una tarea en el programador y especificar la frecuencia y la hora para el libro para que se ejecute.

![Figura 8 - programación de libro][8]

### <a name="create-reports-for-workload-cost-simulation-analysis"></a>Crear informes de análisis de costos de simulación de carga de trabajo

Después de que el uso que se recopila y cargos se cargan en la base de datos de nube Cruiser, podemos utilizar el módulo de nube Cruiser perspectivas para crear la carga de trabajo costo simulación que se desea.

Para mostrar este escenario, que se creó el siguiente informe:

![Comparación de costos][9]

El gráfico superior muestra una comparación de costes por servicios, comparar el precio de la ejecución de la carga de trabajo para cada servicio específico entre WAP (azul oscuro) y Azure (azul claro).

El gráfico inferior muestra los mismos datos pero clasificados por departamento. Muestra los costos para cada departamento ejecuten su carga de trabajo en WAP y Azure, junto con la diferencia entre ellos en la barra de ahorro (verde).

## <a name="azure-usage-api"></a>API de uso de Azure


### <a name="introduction"></a>Introducción

Microsoft presentó recientemente la API de uso de Azure, permite a los suscriptores extraer datos de uso para obtener recomendaciones sobre su consumo mediante programación. Esto es una gran noticia para los clientes de nube Cruiser que puede aprovechar las ventajas del conjunto de datos más rica disponible a través de esta API.

Nube Cruiser puede aprovechar la integración con la API de uso de varias formas. El nivel de detalle (cada hora información de uso) y la información de metadatos de recursos disponible a través de la API proporciona el conjunto de datos es necesario para admitir modelos Showback o cargo al usuario flexibles. 

En este tutorial, presentamos un ejemplo de cómo puede aprovecharse Cruiser de nube de la información de uso de la API. Más concretamente, se cree un grupo de recursos en Azure, asociar las etiquetas de la estructura de la cuenta y describa el proceso de extracción y procesamiento de la información de la etiqueta en la nube Cruiser.
 
El objetivo final es que pueda crear informes como la siguiente y a continuación, podrá analizar costo y consumo basada en la estructura de cuenta consta de las etiquetas.

![Figura 10 - informe con interrupciones con etiquetas][10]

### <a name="microsoft-azure-tags"></a>Etiquetas de Microsoft Azure

Los datos disponibles a través de la API de uso de Azure incluyen información de consumo pero metadatos de recursos, incluidas las etiquetas asociadas. Etiquetas proporcionan una forma sencilla para organizar los recursos, pero para que sea efectivo, debe asegurarse de que:

- Las etiquetas se aplican correctamente a los recursos en el momento de aprovisionamiento
- Las etiquetas se usan correctamente en el proceso de Showback o cargo al usuario para vincular el uso de la estructura de cuenta de la organización.

Estos dos requisitos pueden ser difíciles, especialmente cuando hay un proceso manual en el suministro o cargando lado. Etiquetas de mal, incorrectas o incluso que faltan son comunes quejas de los clientes al usar etiquetas y estos errores puede dificultar la vida en el lado de cargando muy.

Con la nueva API de uso de Azure, Cruiser de nube puede extraer la información de etiquetas temáticas de recursos y, a través de una herramienta ETL sofisticada denominada libros, solucionar estos errores comunes de las etiquetas temáticas. A través de transformación mediante expresiones regulares y recopilación de datos, Cruiser de nube puede identificar recursos incorrectamente etiquetados y aplicar las etiquetas correctas, asegurarse de que la asociación correcta de los recursos con el consumidor.

En el lado de la cargando, nube Cruiser automatiza el proceso de Showback o cargo al usuario y puede aprovechar la información de etiquetas para unir el uso de la consumidor adecuado (departamento, división, Project, etcetera). Esta automatización proporciona una gran mejora y puede asegurarse de que un proceso de cargando coherente y auditoría.
 

### <a name="creating-a-resource-group-with-tags-on-microsoft-azure"></a>Crear un grupo de recursos con etiquetas en Microsoft Azure
El primer paso en este tutorial es crear un grupo de recursos en el portal de Azure, a continuación, crear nuevas etiquetas para asociar a los recursos. En este ejemplo, crearemos las etiquetas siguientes: departamento, entorno, propietario, Project.

La siguiente captura de pantalla muestra un ejemplo de grupo de recursos con las etiquetas asociadas.

![Figura 11 - grupo de recursos con etiquetas asociadas en el portal de Azure][11]

El siguiente paso es extraer la información de la API de uso en nube Cruiser. La API de uso actualmente proporciona las respuestas en formato JSON. Aquí es un ejemplo de los datos recuperados:


    {
      "id": "/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXX/providers/Microsoft.Commerce/UsageAggregates/Daily_BRSDT_20150623_0000",
      "name": "Daily_BRSDT_20150623_0000",
      "type": "Microsoft.Commerce/UsageAggregate",
      "properties":
      {
        "subscriptionId": "bb678b04-0e48-4b44-XXXX-XXXXXXXXX",
        "usageStartTime": "2015-06-22T00:00:00+00:00",
        "usageEndTime": "2015-06-23T00:00:00+00:00",
        "meterName": "Compute Hours",
        "meterRegion": "",
        "meterCategory": "Virtual Machines",
        "meterSubCategory": "Standard_D1 VM (Non-Windows)",
        "unit": "Hours",
        "instanceData": "{\"Microsoft.Resources\":{\"resourceUri\":\"/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXXX/resourceGroups/DEMOUSAGEAPI/providers/Microsoft.Compute/virtualMachines/MyDockerVM\",\"location\":\"eastus\",\"tags\":{\"Department\":\"Sales\",\"Project\":\"Demo Usage API\",\"Environment\":\"Test\",\"Owner\":\"RSE\"},\"additionalInfo\":{\"ImageType\":\"Canonical\",\"ServiceType\":\"Standard_D1\"}}}",
        "meterId": "e60caf26-9ba0-413d-a422-6141f58081d6",
        "infoFields": {},
        "quantity": 8

      },
    },


### <a name="import-data-from-the-usage-api-into-cloud-cruiser"></a>Importar datos de la API de uso en nube Cruiser

Nube Cruiser libros proporcionan una forma automatizada para recopilar y procesar la información de la API de uso. Un libro ETL (carga de transformación extraer) le permite configurar la colección, la transformación y la publicación de datos en la base de datos de nube Cruiser.

Cada libro puede tener una o varias colecciones. Esto le permite relacionar la información de orígenes diferentes de complemento o aumentar los datos de uso. En este ejemplo, se creará una nueva hoja en el libro de Azure plantilla (_UsageAPI)_ y establecer una nueva _colección_ para importar la información de la API de uso.

![Figura 3 - datos de uso de la API importados en la hoja de UsageAPI][12]

Observe que este libro ya tiene otras hojas para importar los servicios de Azure (_ImportServices_) y la información de la API de facturación (_PublishData_) consumo de proceso.

A continuación se utilizará la API de uso para rellenar la hoja de _UsageAPI_ y relacionar la información con los datos de consumo de la API de facturación en la hoja de _PublishData_ .

### <a name="processing-the-tag-information-from-the-usage-api"></a>Procesamiento de la información de etiqueta de la API de uso

Después de importar los datos en el libro, crearemos pasos para la transformación en la hoja de _UsageAPI_ para procesar la información de la API. Primer paso es utilizar un procesador "JSON divide" para extraer las etiquetas de un solo campo y luego crear campos de cada una de ellas (departamento, Project, propietario y entorno).

![Figura 4 - crear nuevos campos para la información de etiqueta][13]

Observe que el servicio de "redes de" Falta la información de etiqueta (cuadro amarillo), pero podemos comprobar que forma parte del mismo grupo de recursos consultando el campo _ResourceGroupName_ . Puesto que tenemos las etiquetas para el resto de los recursos de este grupo de recursos, podemos usamos esta información para aplicar las etiquetas que faltan a este recurso más adelante en el proceso.

El siguiente paso es crear una tabla de búsqueda asociar la información de las etiquetas a la _ResourceGroupName_. Esta tabla de búsqueda se utilizará en el paso siguiente para mejorar los datos de consumo con información de etiquetas.

### <a name="adding-the-tag-information-to-the-consumption-data"></a>Agregar información de las etiquetas a los datos de consumo

Ahora podemos ir a la hoja _PublishData_ , que procesa la información de consumo de la API de facturación y agregue los campos extraídos de las etiquetas. Este proceso se realiza consultando la tabla de búsqueda que creó en el paso anterior, utilizando el _ResourceGroupName_ como la clave para las búsquedas.

![Figura 5 - rellenar la estructura de la cuenta con la información de las búsquedas][14]

Observe que los campos de la estructura de cuenta adecuada para el servicio de "redes de" se han aplicado, solucionar el problema con las etiquetas que faltan. También, los campos de la estructura de cuenta para los recursos se rellenan distinto de nuestro destino grupo de recursos "Sí" para distinguirlos de los informes.

Ahora debemos agregar un paso para publicar los datos de uso. Durante este paso, las tasas apropiadas para cada servicio definidas en nuestro Plan de tasa se aplicará a la información de uso, con la carga resultante cargada en la base de datos.

Lo mejor es que solo tiene que ir a través de este proceso una vez. Cuando haya finalizado el libro, necesitará agregar el programador y se ejecutará cada hora o cada día a la hora programada. A continuación, es cuestión de crear nuevos informes o personalizar las existentes, para analizar los datos para obtener recomendaciones significativos desde el uso de la nube.

### <a name="next-steps"></a>Pasos siguientes

+ Para obtener instrucciones detalladas sobre la creación de informes y los libros de nube Cruiser, consulte en línea de nube Cruiser [documentación](http://docs.cloudcruiser.com/) (válido requerido inicio de sesión).  Para obtener más información acerca de la nube Cruiser, póngase en contacto con [info@cloudcruiser.com](mailto:info@cloudcruiser.com).
+ Para obtener información general sobre el uso de recursos de Azure y RateCard APIs, consulte [obtener recomendaciones sobre el consumo de recursos de Microsoft Azure](billing-usage-rate-card-overview.md) .
+ Consulte la [Referencia de la API de REST de Azure facturación](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) para obtener más información sobre ambos API, que forman parte del conjunto de API proporcionadas por el Administrador de recursos de Azure.
+ Si le gustaría meterse en el código de ejemplo, consulte nuestra Microsoft Azure facturación API ejemplos de código en [Los ejemplos de código de Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

### <a name="learn-more"></a>Aprende más
+ Vea el artículo [Información general del Administrador de recursos de Azure](azure-resource-manager/resource-group-overview.md) para obtener más información sobre el Administrador de recursos de Azure.

<!--Image references-->
 
[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "Ilustración 1: crear una nueva colección"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "Ilustración 2: importar datos de la nueva colección"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "Figura 3 - pasos para la transformación para procesar datos recopilados de la API RateCard"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "Figura 4 - publicar los datos de la API RateCard como nuevos servicios y tasas"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "Figura 5 - comprobar los nuevos servicios"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "Figura 6 - comprobar el nuevo Plan de tasa y tasas de asociados"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "Figura 7 - transformar datos WAP para normalizar servicios"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "Figura 8 - programación de libro"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "Figura 9 - informe de ejemplo para el escenario de comparación del costo de carga de trabajo"
[10]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/1_ReportWithTags.png "Figura 10 - informe con interrupciones con etiquetas"
[11]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/2_ResourceGroupsWithTags.png "Figura 11 - grupo de recursos con etiquetas asociadas en el portal de Azure"
[12]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/3_ImportIntoUsageAPISheet.png "Figura 12 - datos de uso de la API importados en la hoja de UsageAPI"
[13]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/4_NewTagField.png "Figura 13 - crear nuevos campos para la información de etiqueta"
[14]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/5_PopulateAccountStructure.png "Figura 14 - rellenar la estructura de la cuenta con la información de las búsquedas"
