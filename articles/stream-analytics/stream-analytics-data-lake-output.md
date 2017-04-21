<properties
    pageTitle="Salida de almacén de lago de datos de análisis de secuencia | Microsoft Azure"
    description="Configuración de autenticación y la autorización de un almacén de lago de datos de Azure en una tarea de análisis de secuencia"
    keywords=""
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>

# <a name="stream-analytics-data-lake-store-output"></a>Salida de almacén de lago de datos de análisis de secuencia

Tareas de análisis de secuencia admiten varios métodos de salida, uno es un [Almacén de lago de datos de Azure](https://azure.microsoft.com/services/data-lake-store/). Almacén de lago de datos de Azure es un repositorio de gran escala en toda la empresa para cargas de trabajo de análisis de datos grande. Almacén de datos de lago le permite almacenar datos de cualquier tamaño, tipo y recopilación de velocidad para análisis operativa y exploración.

## <a name="authorize-a-data-lake-store-account"></a>Autorizar una cuenta de almacén de datos lago

1.  Cuando se selecciona el almacén de datos de lago como un resultado en el portal de administración de Azure, se le pedirá para autorizar el uso de la tienda de lago datos existentes o solicitar acceso a la vista previa de la tienda de lago datos a través del Portal de clásico de Azure.

    ![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-authorization.png)  

2.  Si ya tiene acceso al almacén de lago de datos, haga clic en "Autorizar ahora" y una hora breve una página aparecerá que indica "Redirigir a autorización..". La página se cerrará automáticamente y se presentará con la página que le permitirá configurar la salida de almacén de datos lago.

Si no ha firmado de vista previa de la tienda de lago de datos, puede seguir el vínculo "Registrarse ahora" para iniciar la solicitud o siga las [instrucciones de introducción](../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="configure-the-data-lake-store-output-properties"></a>Configurar las propiedades de salida de almacén de datos lago

Una vez que la cuenta de almacén de datos lago autenticada, puede configurar las propiedades de la salida de almacén de datos lago. La tabla siguiente es la lista de nombres de propiedad y sus descripciones para configurar la salida de almacén de datos lago.

<table>
<tbody>
<tr>
<td><B>NOMBRE DE PROPIEDAD</B></td>
<td><B>DESCRIPCIÓN</B></td>
</tr>
<tr>
<td>Alias de salida</td>
<td>Se trata de un nombre descriptivo que se usan en las consultas para dirigir el resultado de la consulta a este almacén de lago de datos.</td>
</tr>
<tr>
<td>Cuenta de almacén de datos lago</td>
<td>El nombre de la cuenta de almacenamiento que desea enviar el resultado. Aparecerá una lista desplegable de cuentas de la tienda de lago de datos a la que el usuario que ha iniciado sesión en el portal tiene acceso a.</td>
</tr>
<tr>
<td>Trama de prefijo del ruta [<I>opcional</I>]</td>
<td>La ruta de acceso para escribir los archivos dentro de la cuenta de la tienda de lago de datos especificada. <BR>{date}, {time}<BR>Ejemplo 1: carpeta 1/registros / {date} / {time}<BR>Ejemplo 2: carpeta 1/registros / {date}</td>
</tr>
<tr>
<td>Formato de fecha [<I>opcional</I>]</td>
<td>Si el token de fecha se usa en la ruta de acceso de prefijo, puede seleccionar el formato de fecha en la que se organizan los archivos. Ejemplo: Aaaa/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [<I>opcional</I>]</td>
<td>Si el token de tiempo se usa en la ruta de acceso de prefijo, especifique el formato de hora en que se organizan los archivos. Actualmente, el único valor admitido es HH.</td>
</tr>
<tr>
<td>Formato de serialización de evento</td>
<td>Formato de serialización de datos de salida. JSON, CSV y Avro son compatibles.</td>
</tr>
<tr>
<td>Codificación</td>
<td>Si el formato CSV o JSON, se debe especificar una codificación. UTF-8 es el formato de codificación compatible solo en este momento.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Sólo aplicable para serialización CSV. Análisis de secuencia es compatible con un número de delimitadores comunes para serializar datos CSV. Valores admitidos son coma, punto y coma, espacio, ficha y barra vertical.</td>
</tr>
<tr>
<td>Formato</td>
<td>Sólo aplicable para serialización JSON. Línea separado indica que se da formato al resultado haciendo que cada objeto JSON separado por una nueva línea. Matriz especifica que el resultado tendrá formato como una matriz de objetos JSON.</td>
</tr>
</tbody>
</table>

## <a name="renew-data-lake-store-authorization"></a>Renovar autorización de almacén de datos lago

Actualmente, hay un límite de donde el símbolo de autenticación debe actualizarse manualmente cada 90 días para todos los trabajos con los resultados del almacén de datos lago. También debe volver a autenticar su cuenta de almacén de datos lago si ha cambiado su contraseña desde que se creó o se autenticado por última vez el trabajo. Un síntoma de este problema es ningún resultado de trabajo y un error en los registros de operación que indica la necesidad de autorización de nuevo.

Para resolver este problema, detenga el trabajo en ejecución y vaya a la salida de almacén de datos lago. Haga clic en el vínculo "Renovación autorización" y una hora breve una página aparecerá que indica "Redirigir a autorización..". La página se cerrará automáticamente y si se realiza correctamente, se indicará "Autorización se ha renovado correctamente". , A continuación, necesita hacer clic en "Guardar" en la parte inferior de la página y puede continuar, reinicie su trabajo desde la última vez detenido para evitar la pérdida de datos.

![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-renew-authorization.png)
