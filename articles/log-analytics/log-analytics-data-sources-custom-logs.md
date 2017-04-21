<properties 
   pageTitle="Registros personalizados en el análisis de registro | Microsoft Azure"
   description="Análisis de registro puede recopilar eventos de archivos de texto en equipos de Windows y Linux.  En este artículo se describe cómo definir un nuevo registro personalizado y detalles de los registros que se crean en el repositorio OMS."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="custom-logs-in-log-analytics"></a>Registros personalizados en el análisis de registro

El origen de datos de registros personalizados en el análisis de registro le permite recopilar eventos de archivos de texto en equipos de Windows y Linux. Muchas aplicaciones registran información en archivos de texto en lugar de los servicios de registro estándar como el registro de eventos de Windows o el registro del sistema.  Una vez recopilada, puede analizar cada registro en el registro en campos individuales con la característica de [Campos personalizados](log-analytics-custom-fields.md) de análisis de registro.

![Colección de registro personalizado](media/log-analytics-data-sources-custom-logs/overview.png)

Los archivos de registro que se recopilen deben coincidir con los criterios siguientes.

- El registro debe tener una entrada por línea o usar una marca de tiempo que coincidan con uno de los siguientes formatos al comienzo de cada entrada.

    DD-MM-AAAA HH <br>
  D/M/AAAA HH: MM: SS A.M./P.M. <br>
  El mes DD, aaaa hh
    
- El archivo de registro no debe permitir actualizaciones circulares donde el archivo se sobrescribe con nuevas entradas. 

## <a name="defining-a-custom-log"></a>Definir un registro personalizado

Use el siguiente procedimiento para definir un archivo de registro personalizado.  Desplácese hasta el final de este artículo para obtener un tutorial de una muestra de la adición de un registro personalizado.

### <a name="step-1-open-the-custom-log-wizard"></a>Paso 1. Abrir al Asistente de registro personalizado

El Asistente de registro personalizado se ejecuta en el portal OMS y le permite definir un nuevo registro personalizado para recopilar.

1.  En el portal OMS, vaya a **configuración**.
2.  Haga clic en **datos** y, a continuación, **registros personalizados**.
3.  De forma predeterminada, todos los cambios de configuración se insertan automáticamente a todos los agentes.  Para agentes Linux, se envía un archivo de configuración para el selector de datos de Fluentd.  Si desea modificar este archivo manualmente en cada agente Linux, a continuación, desactive la casilla *Aplicar debajo de configuración de mis equipos Linux*.
4.  Haga clic en **Add +** para abrir al Asistente de registro personalizado.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Paso 2. Cargar y analizar un ejemplo de registro

Para empezar, cargar una muestra de registro personalizado.  El Asistente analizar y mostrar las entradas de este archivo para validar.  Análisis de registro utilizará el delimitador que especifique para identificar cada registro.

**Nueva línea** es el delimitador predeterminado y se usa para los archivos de registro que tengan una entrada por línea.  Si la línea empieza con una fecha y hora en uno de los formatos disponibles, puede especificar un delimitador de **marca de tiempo** que admite las entradas que abarcan más de una línea. 

Si se usa un delimitador de marca de hora, la propiedad TimeGenerated de cada registro almacenado en OMS se rellenará con la fecha y hora especificada para dicho elemento en el archivo de registro.  Si se usa un delimitador de línea nueva, TimeGenerated se rellena con la fecha y hora en que el análisis de registro había recopilada la entrada. 

>[AZURE.NOTE]Análisis de registro actualmente trata la fecha y hora recopilada un registro mediante un delimitador de marca de hora como UTC.  Pronto se cambiarán para usar la zona horaria en el agente. 
 
1.  Haga clic en **Examinar** y busque un archivo de ejemplo.  Tenga en cuenta que esto puede botón puede estar etiquetada **Elegir archivo** en algunos exploradores.
2.  Haga clic en **siguiente**. 
3.  El Asistente de registro personalizado se cargue el archivo y los registros que identifica la lista.
4.  Cambiar el delimitador que se utiliza para identificar un registro nuevo y seleccione el delimitador que mejor identifique los registros de su archivo de registro.
5.  Haga clic en **siguiente**.

### <a name="step-3-add-log-collection-paths"></a>Paso 3. Agregar rutas de colección de registro

Debe definir uno o varios trazados en el agente donde pueda localizar el registro personalizado.  Puede proporcionar una ruta de acceso específica y un nombre para el archivo de registro, o puede especificar una ruta con un carácter comodín para el nombre.  Esto es compatible con las aplicaciones que cree un nuevo archivo cada día o cuando un archivo llega a un determinado tamaño.  También puede proporcionar varias rutas de acceso para un archivo de registro.

Por ejemplo, una aplicación puede crear un archivo de registro cada día con la fecha incluida en el nombre como en log20100316.txt. Puede ser un patrón para un registro de dichos *Registro\*.txt* que se aplican a cualquier archivo de registro después de la aplicación de nombres del esquema.

La siguiente tabla proporciona ejemplos de patrones válidos para especificar los diferentes archivos de registro. 

| Descripción | Ruta de acceso |
|:--|:--|
| Todos los archivos en *C:\Logs* con extensión .txt en agente de Windows | C:\Logs\\\*.txt |
| Todos los archivos en *C:\Logs* con un nombre que comienzan con registro y extensión en agente de Windows | C:\Logs\log\*.txt |
| Todos los archivos de Linux agente */var/log/audit* con extensión .txt | /var/log/Audit/*.txt |
| Todos los archivos en */var/log/audit* con un nombre que comienzan con registro y extensión en agente de Linux | /var/log/Audit/log\*.txt |
  

1.  Seleccione Windows o Linux para especificar qué formato de ruta de acceso va a agregar.
2.  Escriba la ruta de acceso y haga clic en el **+** botón.
3.  Repita el proceso para las rutas adicionales.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Paso 4. Proporcione un nombre y una descripción para el registro

El nombre que especifique se utilizará para el tipo de registro como se describió anteriormente.  Siempre finalizará con _CL para distinguir como un registro personalizado.

1.  Escriba un nombre para el registro.  La ** \_CL** sufijo se proporciona automáticamente.
2.  Agregar una **Descripción**de opcional.
3.  Haga clic en **siguiente** para guardar la definición de registro personalizado.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Paso 5. Validar que se recopilan los registros personalizados
Pueden tardar hasta una hora inicial de los datos de un nuevo registro personalizado que aparezca en el análisis de registro.  Se iniciará la recopilación de entradas de los registros que se encuentra en la ruta de acceso especificado desde el punto de que haya definido el registro personalizado.  No conservará las entradas que ha cargado durante la creación de registro personalizado, pero recopilará entradas ya existentes en los archivos de registro que busca.

Una vez que el análisis de registro comienza de recopilación de registro personalizado, sus registros estarán disponibles con una búsqueda de registro.  Utilice el nombre que le asignó el registro personalizado como el **tipo** de la consulta.

>[AZURE.NOTE] Si la propiedad RawData falta de la búsqueda, debe cerrar y volver a abrir el explorador.

### <a name="step-6-parse-the-custom-log-entries"></a>Paso 6. Analizar las entradas de registro personalizado

La entrada del registro completo se almacenarán en una única propiedad denominada **RawData**.  Es muy probable que se desea separar los distintos elementos de información de cada entrada en propiedades individuales almacenados en el registro.  Para ello, utilizar la característica de [Campos personalizados](log-analytics-custom-fields.md) de análisis de registro.

No se proporcionan a continuación encontrará instrucciones detalladas para analizar la entrada de registro personalizado.  Consulte la documentación de [Campos personalizados](log-analytics-custom-fields.md) para esta información.

## <a name="disabling-a-custom-log"></a>Deshabilitar un registro personalizado

No puede eliminar una definición de registro personalizado una vez que se ha creado, pero puede desactivarlo quitando todos sus rutas de acceso de la colección.

1.  En el portal OMS, vaya a **configuración**.
2.  Haga clic en **datos** y, a continuación, **registros personalizados**.
3.  Junto a la definición de registro personalizado para deshabilitar, haga clic en **Detalles** .
4.  Quitar cada una de las rutas de acceso de la colección de la definición de registro personalizado.


## <a name="data-collection"></a>Recopilación de datos

Análisis de registro recopilará nuevas entradas de cada registro personalizado aproximadamente cada 5 minutos.  El agente grabará su posición en cada archivo de registro que recopila de.  Si el agente se desconecta durante un período de tiempo, a continuación, el análisis de registro recopilará entradas desde donde se quedó, incluso si las entradas que se crearon mientras el agente estaba sin conexión.

Todo el contenido de la entrada del registro se escribe en una sola propiedad denominada **RawData**.  Se puede analizar en varias propiedades que pueden analizar y búsquedas mediante la definición de [Campos personalizados](log-analytics-custom-fields.md) después de haber creado el registro personalizado.


## <a name="custom-log-record-properties"></a>Propiedades del registro de registro personalizado

Registros de registro personalizado tienen un tipo con el nombre de registro que proporciona y las propiedades de la tabla siguiente.

| (Propiedad) | Descripción |
|:--|:--|
| TimeGenerated | Fecha y hora el registro recopilados por el análisis de registro.  Si el registro usa un delimitador de hora basada es el tiempo que se recopilan desde la entrada. |
| SourceSystem  | Tipo de agente que se ha recopilado el registro. <br> Conectar OpsManager: agente de Windows, ambos directa o SCOM <br> Linux: todos los agentes de Linux  |
| RawData             | Texto de la entrada recopilada. |
| ManagementGroupName | Nombre del grupo de administración de agentes SCOM.  Para otros agentes, esto es AOI -\<identificador de área de trabajo\> |


## <a name="log-searches-with-custom-log-records"></a>Búsquedas de registros con los registros de registro personalizado

Registros de registros personalizados se almacenan en el repositorio OMS como registros de cualquier otro origen de datos.  Tienen un tipo que coincida con el nombre que desea proporcionar al definir el registro para poder usar la propiedad de tipo en la búsqueda para recuperar registros recopilados un registro específico.

La tabla siguiente proporciona diferentes ejemplos de búsquedas de registro que recuperar registros de registros personalizados.

| Consulta | Descripción |
|:--|:--|
| Tipo = MyApp_CL | Todos los eventos desde una sesión MyApp_CL con nombre. |
| Tipo = MyApp_CL Severity_CF = error | Todos los eventos de un registro personalizado denominado MyApp_CL con un valor de *error* en un campo personalizado denominan *Severity_CF*. |




## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Tutorial de ejemplo de la adición de un registro personalizado

La siguiente sección le guía a través de un ejemplo de cómo crear un registro personalizado.  El registro de ejemplo que se recopilan con una entrada en cada línea comenzando con una fecha y hora y, a continuación, una coma delimitado por campos de código, el estado y el mensaje.  A continuación se muestran varias entradas de ejemplo.

    2016-03-10 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
    2016-03-10 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
    2016-03-10 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
    2016-03-10 01:38:22 302,Error,Application could not connect to database
    2016-03-10 01:31:34 303,Error,Application lost connection to database

### <a name="upload-and-parse-a-sample-log"></a>Cargar y analizar un ejemplo de registro

Proporcionamos uno de los archivos de registro y puede ver los eventos que se desee obtener.  En este caso nueva línea es un delimitador suficiente.  Si una entrada en el registro puede abarcar varias líneas sin embargo, tendría un delimitador de marca de tiempo para usarse.

![Cargar y analizar un ejemplo de registro](media/log-analytics-data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Agregar rutas de colección de registro

Los archivos de registro se encuentran en *C:\MyApp\Logs*.  Se creará un nuevo archivo cada día con un nombre que incluye la fecha de la trama *appYYYYMMDD.log*.  Una trama suficiente para este registro sería *C:\MyApp\Logs\\\*.log*.

![Ruta de la colección de registro](media/log-analytics-data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Proporcione un nombre y una descripción para el registro

Usamos un nombre de *MyApp_CL* y escriba una **Descripción**.

![Nombre del registro](media/log-analytics-data-sources-custom-logs/log-name.png)


### <a name="validate-that-the-custom-logs-are-being-collected"></a>Validar que se recopilan los registros personalizados

Usamos una consulta de *tipo = MyApp_CL* para devolver todos los registros desde el registro recopilado.

![Consulta de registro sin campos personalizados](media/log-analytics-data-sources-custom-logs/query-01.png)

### <a name="parse-the-custom-log-entries"></a>Analizar las entradas de registro personalizado

Usamos campos personalizados para definir la *EventTime*, *código*, *estado*y campos del *mensaje* y nos podemos ver la diferencia en los registros devueltos por la consulta.

![Consulta de registro con campos personalizados](media/log-analytics-data-sources-custom-logs/query-02.png)

## <a name="next-steps"></a>Pasos siguientes

- Usar [Campos personalizados](log-analytics-custom-fields.md) para analizar las entradas en el registro personalizado en campos individuales.
- Obtenga información acerca de las [búsquedas de registro](log-analytics-log-searches.md) analizar los datos recopilados desde orígenes de datos y soluciones. 