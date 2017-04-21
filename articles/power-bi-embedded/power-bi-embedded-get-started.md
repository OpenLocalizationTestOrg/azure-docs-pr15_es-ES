<properties
   pageTitle="Introducción a Microsoft Power BI incrustado"
   description="Power BI incrustado, agregue informes interactivos de Power BI en la aplicación de inteligencia empresarial"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="get-started-with-microsoft-power-bi-embedded"></a>Introducción a Microsoft Power BI incrustados

**Power BI incrustado** es un servicio de Azure que permite a los desarrolladores de aplicaciones agregar los informes de Power BI interactivos en sus propias aplicaciones. **Power BI incrustado** funciona con las aplicaciones existentes sin necesidad de rediseñar o cambiar los usuarios de forma Inicio de sesión.

Recursos para **Microsoft Power BI incrustado** se suministran a través de las [API de BRAZO de Azure](https://msdn.microsoft.com/library/mt712306.aspx). En este caso, el recurso que se aprovisiona es una **Colección de área de trabajo de Power BI**.

![](media\power-bi-embedded-get-started\introduction.png)

## <a name="create-a-workspace-collection"></a>Crear una colección de área de trabajo
Una **Colección de área de trabajo** es el recurso de Azure de nivel superior y un contenedor para el contenido que se insertará en la aplicación. Puede crearse una **Colección de área de trabajo** de dos maneras:

   -    Manualmente con el Portal de Azure
   -    Mediante programación utilizando la API de Manager(ARM) de recursos de Azure

Veamos los pasos para crear una **Colección de área de trabajo** con el Portal de Azure.

   1.   Abra e inicie sesión en el **Portal de Azure**: [http://portal.azure.com](http://portal.azure.com).

   2.   Haga clic en **+ nuevo** en el panel superior.

       ![](media\power-bi-embedded-get-started\create-workspace-1.png)

   3.   **Datos + análisis** haga clic en **Power BI incrustado**.
   4.   En el **Módulo de creación**, escriba la información requerida. Para **los precios**, consulte [precios Power BI incrustado](http://go.microsoft.com/fwlink/?LinkID=760527).

       ![](media\power-bi-embedded-get-started\create-workspace-2.png)

   5. Haga clic en **crear**.

La **Colección de área de trabajo** tardará unos momentos aprovisionar. Cuando finalice, se abrirá en el **Módulo de la colección de área de trabajo**.

   ![](media\power-bi-embedded-get-started\create-workspace-3.png)

El **Módulo de creación** contiene la información que necesita llamar a las API que creen áreas de trabajo y distribución contenido en ellos.

<a name="view-access-keys"/>
## <a name="view-power-bi-api-access-keys"></a>Teclas de acceso a la API de vista Power BI

Una de las partes más importantes de la información necesaria para llamar a las API de REST de Power BI son las **Teclas de acceso**. Se utilizan para generar los **símbolos de la aplicación** que se utilizan para autenticar las solicitudes de API. Para ver las **Teclas de acceso**, haga clic en **Las teclas de acceso** en el **Módulo de configuración**. Para obtener más información sobre los **tokens de aplicación**, vea [Authenticating y autorizar con Power BI incrustado](power-bi-embedded-app-token-flow.md).

   ![](media\power-bi-embedded-get-started\access-keys.png)

Deberá ' aviso de que tiene dos claves.

   ![](media\power-bi-embedded-get-started\access-keys-2.png)

Copiar estas teclas y almacenar de forma segura en la aplicación. Es muy importante tratar estas teclas como lo haría con una contraseña, ya que le proporcionan acceso a todo el contenido de la **Colección de área de trabajo**.

Mientras se muestran dos claves, es necesaria ninguna solo clave en un momento determinado. La segunda clave sirve para periódicamente puede generar claves sin interrumpir el acceso al servicio.

Ahora que ya tiene una instancia de Power BI para su aplicación y **Las teclas de acceso**, puede importar un informe a su propia aplicación. Antes de aprender a importar un informe, la siguiente sección describe crear conjuntos de datos de Power BI e informes para incrustar en una aplicación.

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app"></a>Crear conjuntos de datos de Power BI e informes para incrustar en una aplicación

Ahora que ha creado una instancia de Power BI para su aplicación y tienen **Teclas de acceso**, debe crear los conjuntos de datos de Power BI e informes que desea insertar. Conjuntos de datos e informes pueden crearse mediante el uso de **Power BI Desktop**. Puede descargar [Power BI Desktop para liberar](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/). O bien, para comenzar rápidamente, puede descargar el [minorista análisis muestra PBIX] (http://go.microsoft.com/fwlink/?LinkID=780547). Para obtener más información sobre cómo usar **Power BI Desktop**, vea [Introducción a Power BI Desktop](https://powerbi.microsoft.com/en-us/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop).

Con **Power BI Desktop**, debe conectarse al origen de datos importando una copia de los datos en **Power BI Desktop** o conectarse directamente al origen de datos usando **DirectQuery**.

Estas son las diferencias entre usar **Importar** y **DirectQuery**.

|Importar | DirectQuery
|---|---
|Tablas, columnas *y los datos* se importan o se copian en **Power BI Desktop**. Mientras trabaja con visualizaciones, **Power BI Desktop** consulta una copia de los datos. Para ver los cambios que ocurrieron con los datos subyacentes, debe actualizar o importar un completo actual conjunto de datos nuevo.|Importar solo *tablas y columnas* o copia en **Power BI Desktop**. Mientras trabaja con visualizaciones, **Power BI Desktop** consulta el origen de datos subyacente, lo que significa que está viendo siempre datos actuales.

Para obtener más información sobre cómo conectarse a un origen de datos, vea [conectarse a un origen de datos](power-bi-embedded-connect-datasource.md).

Después de guardar el trabajo en **Power BI Desktop**, se crea un archivo PBIX. Este archivo contiene el informe. Además, si importa los datos del PBIX contiene el conjunto de datos completa, o si usa **DirectQuery**, la PBIX contiene un esquema de conjunto de datos. Implementar mediante programación el PBIX en el área de trabajo con la [API de importación de Power BI](https://msdn.microsoft.com/library/mt711504.aspx).

> [AZURE.NOTE] **Power BI incrustado** tiene API adicionales para cambiar el servidor y la base de datos que señala el conjunto de datos y establecer una credencial de cuenta de servicio que usará el conjunto de datos para conectarse a la base de datos. Consulte [SetAllConnections de publicación](https://msdn.microsoft.com/library/mt711505.aspx) y [origen de datos de puerta de enlace de revisión](https://msdn.microsoft.com/library/mt711498.aspx).

## <a name="next-steps"></a>Pasos siguientes
En los pasos anteriores, se crean una colección de área de trabajo y el primer informe y conjunto de datos. Ahora es el momento para obtener información sobre cómo escribir código para **Power BI incrustado**. Para ayudarle a empezar, creamos una aplicación web de ejemplo: [Introducción a los datos de ejemplo](power-bi-embedded-get-started-sample.md). En el ejemplo se muestra cómo para:

  - Contenido de aprovisionamiento
      - Crear un área de trabajo
      - Importar un archivo PBIX
      - Actualice las cadenas de conexión y establecer credenciales para los conjuntos de datos.

  - Insertar un informe de forma segura

## <a name="see-also"></a>Vea también
- [Introducción a muestra](power-bi-embedded-get-started-sample.md)
- [Autenticar y autorizar con Power BI incrustado](power-bi-embedded-app-token-flow.md)
- [Escritorio de Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
