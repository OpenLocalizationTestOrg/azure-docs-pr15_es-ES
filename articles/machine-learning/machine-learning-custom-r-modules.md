<properties 
    pageTitle="Creación personalizada R módulos de aprendizaje de máquina Azure | Microsoft Azure" 
    description="Inicio rápido para la creación de módulos R personalizados en Azure el aprendizaje." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev"  
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="tbd" 
    ms.date="08/19/2016" 
    ms.author="bradsev;ankarloff" />


# <a name="author-custom-r-modules-in-azure-machine-learning"></a>Módulos R personalizados de autor en aprendizaje de máquina de Azure

Este tema describe cómo crear e implementar un módulo de R personalizado en Azure el aprendizaje. Se explica qué son los módulos R personalizados y los archivos que utiliza para definirlos. Se muestra cómo crear los archivos que definen un módulo y cómo registrar el módulo para su implementación en un área de trabajo de aprendizaje del equipo. A continuación, se describen los elementos y atributos que se usan en la definición del módulo personalizado con más detalle. También se explica cómo usar la funcionalidad auxiliar, archivos y varias salidas. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## <a name="what-is-a-custom-r-module"></a>¿Qué es un módulo R personalizado?

Un **módulo personalizado** es un módulo definidas por el usuario que puede cargar en el área de trabajo y ejecutar como parte de un ensayo de aprendizaje del equipo de Azure. Un **módulo R personalizado** es un módulo personalizado que se ejecuta una función de R definidas por el usuario. **R** es un lenguaje de programación de la informática y los gráficos que se utilizan ampliamente en estadísticos y datos científicos para implementar algoritmos estadísticos. Actualmente, R es el único lenguaje admitido en módulos personalizados, pero el soporte técnico para idiomas adicionales está programada para los próximos lanzamientos.

Módulos personalizados tienen el **estado de primera clase** en aprendizaje de máquina de Azure en el sentido de que se pueden usar como cualquier otro módulo. Se pueden ejecutar con otros módulos, incluidos en experimentación publicado o en visualizaciones. Tiene control sobre el algoritmo de implementada por el módulo, la entrada y puertos de salida para usarse, los parámetros de modelado y otros diversos comportamientos de tiempo de ejecución. También se puede publicar un ensayo que contiene los módulos personalizados a la Galería de inteligencia Cortana para compartir fácilmente.


## <a name="files-in-a-custom-r-module"></a>Archivos en un módulo de R personalizado

Un módulo R personalizado definido por un archivo .zip que contiene, como mínimo, dos archivos:

* Un **archivo de origen** que implementa la función R expuesta por el módulo
* Un **archivo de definición XML** que describe la interfaz de módulo personalizado

Archivos auxiliares adicionales también se incluirán en el archivo .zip que proporciona la funcionalidad que se puede acceder desde el módulo personalizado. Esta opción se describe en la parte de **argumentos** de la sección de referencia de **elementos en el archivo de definición de XML** siguiendo el ejemplo de tutorial rápido.


## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Ejemplo de tutorial rápido: definir, empaquetar y registrar un módulo R personalizado

Este ejemplo muestra cómo crear los archivos necesarios para que un módulo R personalizado, las empaqueta en un archivo zip y, a continuación, registrar el módulo en el área de trabajo de aprendizaje. Los archivos de ejemplo de paquete del zip ejemplo pueden descargarse desde [CustomAddRows.zip Descargar archivo](http://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>El archivo de origen
Considere el ejemplo de un módulo **Personalizado agregar filas** que modifica la implementación estándar del módulo de **Agregar filas** que se usa para concatenar filas (observaciones) de dos conjuntos de datos (tramas de datos). El módulo de **Agregar filas** estándar anexa las filas de la segunda conjunto de datos de entrada hasta el final de la primera entrada de conjunto de datos utilizando la `rbind` algoritmo. Personalizada `CustomAddRows` función acepta dos conjuntos de datos de forma similar, pero también acepta un parámetro booleano intercambiar como una entrada adicional. Si el parámetro de intercambio se establece en **FALSE**, devuelve el mismo conjunto de datos que la implementación estándar. Pero, si el parámetro de intercambio es **Verdadero**, la función anexa filas del primer conjunto de datos de entrada hasta el final del segundo conjunto de datos en su lugar. El archivo CustomAddRows.R que contiene la implementación de la R `CustomAddRows` función expuesta por el módulo **Personalizado agregar filas** tiene el siguiente código de R.

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) 
    {
        if (swap)
        {
            return (rbind(dataset2, dataset1));
        }
        else
        {
            return (rbind(dataset1, dataset2));
        } 
    } 

### <a name="the-xml-definition-file"></a>El archivo de definición de XML
Exponer esto `CustomAddRows` debe crearse la función como un módulo de aprendizaje del equipo de Azure, un archivo de definición de XML para especificar cómo debe apariencia y comportamiento el módulo **Personalizado agregar filas** . 

    <!-- Defined a module using an R Script -->
    <Module name="Custom Add Rows">
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another. Dataset 2 is concatenated to Dataset 1 when Swap is FALSE, and vice versa when Swap is TRUE.</Description>
    
    <!-- Specify the base language, script file and R function to use for this module. -->      
        <Language name="R" 
         sourceFile="CustomAddRows.R" 
         entryPoint="CustomAddRows" />  
        
    <!-- Define module input and output ports -->
    <!-- Note: The values of the id attributes in the Input and Arg elements must match the parameter names in the R Function CustomAddRows defined in CustomAddRows.R. -->
        <Ports>
            <Input id="dataset1" name="Dataset 1" type="DataTable">
                <Description>First input dataset</Description>
            </Input>
            <Input id="dataset2" name="Dataset 2" type="DataTable">
                <Description>Second input dataset</Description>
            </Input>
            <Output id="dataset" name="Dataset" type="DataTable">
                <Description>The combined dataset</Description>
            </Output>
        </Ports>
        
    <!-- Define module parameters -->
        <Arguments>
            <Arg id="swap" name="Swap" type="bool" >
                <Description>Swap input datasets.</Description>
            </Arg>
        </Arguments>
    </Module>

 
Es muy importante que tenga en cuenta que el valor de los atributos de **identificador** de los elementos de **entrada** y el **argumento** en el archivo XML debe coincidir exactamente con los nombres de parámetro de la función del código en el archivo CustomAddRows.R R: (*dataset1*, *dataset2*e *intercambie* en el ejemplo). Asimismo, el valor del atributo de **punto de entrada** del elemento de **lenguaje** debe coincidir con el nombre de la función en la secuencia de comandos de R exactamente: (*CustomAddRows* en el ejemplo). 

Por el contrario, el atributo **id** del elemento de **salida** no se corresponde con las variables de la secuencia de comandos de R. Cuando se requiere más de un resultado, simplemente devuelve una lista de la función R con resultados que se coloca *en el mismo orden* y los elementos de **resultados** se declaren en el archivo XML.

### <a name="package-and-register-the-module"></a>Empaquetar y registrar el módulo
Guardar estos dos archivos como *CustomAddRows.R* y *CustomAddRows.xml* y, a continuación, zip los dos archivos juntos en un archivo *CustomAddRows.zip* .

Para registrar en el área de trabajo de aprendizaje, vaya al área de trabajo en equipo aprendizaje Studio, haga clic en el botón **+ nuevo** en la parte inferior y elija **módulo -> de paquete ZIP** para cargar el nuevo módulo **Personalizado agregar filas** .

![Cargar Zip](./media/machine-learning-custom-r-modules/upload-from-zip-package.png)

El módulo **Personalizado agregar filas** está preparado para tener acceso a su experimentación aprendizaje del equipo.


## <a name="elements-in-the-xml-definition-file"></a>Elementos en el archivo de definición de XML

### <a name="module-elements"></a>Elementos de módulo
El elemento **módulo** se utiliza para definir un módulo personalizado en el archivo XML. Pueden definirse varios módulos en un archivo XML con varios elementos de **módulo** . Cada módulo en el área de trabajo debe tener un nombre único. Registrar un módulo personalizado con el mismo nombre que un módulo personalizado existente y reemplaza el módulo existente con uno nuevo. Sin embargo, módulos personalizados se pueden registrado con el mismo nombre que un módulo de aprendizaje de Azure máquina existente. Si es así, que aparecen en la categoría **personalizada** de la paleta de módulo.

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


En el elemento **módulo** , puede especificar dos elementos opcionales adicionales:

- un elemento de **propietario** que está incrustado en el módulo  
- un elemento de **Descripción** que contiene el texto que se muestra en la Ayuda rápida para el módulo y cuando desplace el puntero sobre el módulo en la interfaz de usuario de aprendizaje del equipo.


Reglas para los límites de caracteres en los elementos de módulo:

* El valor del atributo **name** en el elemento **módulo** no debe superar 64 caracteres de longitud. 
* El contenido del elemento de **Descripción** no debe exceder de 128 caracteres de longitud.
* El contenido del elemento de **propietario** no debe exceder 32 caracteres de longitud.


Resultados de un módulo pueden ser deterministas o nondeterministic.* * de forma predeterminada, todos los módulos se consideran determinista. Es decir, dado un conjunto de parámetros de entrada y los datos que no cambian, debe devolver el módulo de la misma eacRAND de resultados o una hora de functionh que se ejecuta. Dado que este comportamiento, Azure máquina aprendizaje Studio sólo vuelve a ejecutar módulos marcados como determinista si un parámetro o ha cambiado los datos de entrada. Devolver los resultados de la caché proporciona también mucho más rápida ejecución de experimentación.

Hay funciones que son no deterministas como aleatorio o una función que devuelve la fecha actual o la hora. Si el módulo usa una función determinista, puede especificar que el módulo no es determinante estableciendo el atributo opcional **isDeterministic** en **FALSE**. Esto garantiza que el módulo se ejecute de nuevo cada vez que se ejecuta el ensayo, incluso si no han cambiado el módulo de entrada y los parámetros. 

### <a name="language-definition"></a>Definición de idioma
El elemento de **idioma** en el archivo de definición de XML se utiliza para especificar el idioma de módulo personalizado. Actualmente, R es el único lenguaje admitido. El valor del atributo de **origen** debe ser el nombre del archivo R que contiene la función llamar cuando se ejecuta el módulo. Este archivo debe ser parte del paquete zip. El valor del atributo de **punto de entrada** es el nombre de la función que se llama y debe coincidir con una función válida definida con en el archivo de origen.

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### <a name="ports"></a>Puertos
Los puertos de entrada y salidos para un módulo personalizado se especifican en los elementos secundarios de la sección de **puertos** del archivo de definición de XML. El orden de los elementos siguientes determina la experiencia de diseño (UX) por los usuarios. El primer secundario **entrada** o **salida** que aparece en el elemento de **puertos** del archivo XML se convierte en el puerto de entrada de más a la izquierda en el aprendizaje de equipo UX.
Cada entrada y salida puerto puede tener un elemento secundario de **Descripción** opcional que especifica el texto que aparece cuando coloca el cursor del mouse sobre el puerto en la interfaz de usuario de aprendizaje del equipo.

**Las reglas de puertos**:

* Número máximo de **puertos de entrada y salidos** es 8 para cada uno.

### <a name="input-elements"></a>Elementos de entrada
Puertos de entrada permiten pasar datos a la función R y el área de trabajo. Los **tipos de datos** que son compatibles para puertos de entrada son los siguientes: 

**Tabla de datos:** Este tipo se pasa a la función R como un data.frame. De hecho, cualquier tipo (por ejemplo, archivos CSV o archivos ARFF) que son compatibles con el aprendizaje y que son compatibles con la **tabla de datos** se convierte en un data.frame automáticamente. 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
        </Input>

El atributo de **identificador** asociado a cada puerto de entrada de **tabla de datos** debe tener un valor único y este valor debe coincidir con el parámetro con nombre en la función R correspondiente.
Puertos opcionales de **tabla de datos** que no se pasan como entrada en una prueba tienen el valor **NULL** pasa a la función R y puertos opcional zip se omiten si no está conectada la entrada. El atributo **isOptional** es opcional para los tipos de la **tabla de datos** y **Zip** y es *false* de forma predeterminada.
       
**Zip:** Módulos personalizados pueden aceptar un archivo zip como entrada. Esta entrada se desempaqueta en el directorio de trabajo R de la función

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files to be extracted to the R working directory.</Description>
        </Input>

Para módulos de R personalizados, el identificador de un puerto Zip no tiene para que coincida con los parámetros de la función R. Esto es porque el archivo zip se extraerá automáticamente en el directorio de trabajo R.

**Reglas de entrada:**

* El valor del atributo **id** del elemento de **entrada** debe ser un nombre válido de variable de R.
* El valor del atributo **id** del elemento de **entrada** no debe ser más de 64 caracteres.
* El valor del atributo de **nombre** del elemento de **entrada** no debe ser más de 64 caracteres.
* El contenido del elemento de **Descripción** no puede tener más de 128 caracteres
* El valor del atributo de **tipo** de elemento de **entrada** debe ser *Zip* o la *tabla de datos*.
* El valor del atributo **isOptional** del elemento de **entrada** no es necesario (y es *false* de forma predeterminada cuando no se especifica); pero, si se especifica, debe ser *Verdadero* o *Falso*.

### <a name="output-elements"></a>Elementos de salida

**Puertos de salida estándar:** Puertos de salida se asignan a los valores devueltos de la función de R, a continuación, puede ser usada por módulos posteriores. *Tabla de datos* es el tipo de puerto de salida estándar solo admitido actualmente. (Soporte técnico para *los estudiantes* y *las transformaciones* es próximamente). Un resultado de la *tabla de datos* se define como:

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

Para salidas en módulos R personalizados, para que se corresponda con nada en la secuencia de comandos de R no tiene el valor del atributo **id** , pero deben ser único. Para un resultado único módulo, el valor devuelto de la función R debe ser un *data.frame*. Para mostrar más de un objeto de un tipo de datos compatibles, los puertos de salida apropiado deben especificarse en el archivo de definición de XML y los objetos deben devolverse como una lista. Los objetos de salida se asignan a puertos de izquierda a derecha, que refleja el orden en que se colocan los objetos en la lista devuelta de salida.

Por ejemplo, si desea modificar el módulo **Personalizado agregar filas** para los dos conjuntos de datos originales de salida, *dataset1* *dataset2*, además de la nueva combinar y conjunto de datos, *conjunto de datos*, (en un orden, de izquierda a derecha, como: *conjunto de datos*, *dataset1*, *dataset2*), a continuación, defina los puertos de salida en el archivo CustomAddRows.xml como sigue:

    <Ports> 
        <Output id="dataset" name="Dataset Out" type="DataTable"> 
            <Description>New Dataset</Description> 
        </Output> 
        <Output id="dataset1_out" name="Dataset 1 Out" type="DataTable"> 
            <Description>First Dataset</Description> 
        </Output> 
        <Output id="dataset2_out" name="Dataset 2 Out" type="DataTable"> 
            <Description>Second Dataset</Description> 
        </Output> 
        <Input id="dataset1" name="Dataset 1" type="DataTable"> 
            <Description>First Input Table</Description>
        </Input> 
        <Input id="dataset2" name="Dataset 2" type="DataTable"> 
            <Description>Second Input Table</Description> 
        </Input> 
    </Ports> 


Y volver a la lista de objetos en una lista en el orden correcto en 'CustomAddRows.R':

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
        if (swap) { dataset <- rbind(dataset2, dataset1)) } 
        else { dataset <- rbind(dataset1, dataset2)) 
        } 
    return (list(dataset, dataset1, dataset2)) 
    } 
    
**Salida de visualización:** También puede especificar un puerto de salida del tipo de *visualización*, que muestra los resultados del dispositivo de gráficos R y salida de la consola. Este puerto no forma parte de los resultados de la función R y no interfiera con el orden de los tipos de puerto de salida. Para agregar un puerto de visualización a los módulos personalizados, agregue un elemento de **resultado** con un valor de *visualización* para el atributo de **tipo** :

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>
    
**Reglas de salida:**

* El valor del atributo **id** del elemento de **salida** debe ser un nombre válido de variable de R.
* El valor del atributo **id** del elemento de **resultado** no debe ser más de 32 caracteres.
* El valor del atributo de **nombre** del elemento de **salida** no puede tener más de 64 caracteres.
* El valor del atributo de **tipo** de elemento de **resultado** debe ser la *visualización*.

### <a name="arguments"></a>Argumentos
Pueden pasar datos adicionales a la función R a través de los parámetros del módulo que se definen en el elemento de **argumentos** . Estos parámetros aparecen en el panel de propiedades de la derecha de la interfaz de usuario de aprendizaje de máquina cuando está seleccionado el módulo. Los argumentos pueden ser cualquiera de los tipos admitidos, o puede crear una enumeración personalizada cuando sea necesario. De forma similar a los elementos de **puertos** , los elementos de **argumentos** pueden tener un elemento de **Descripción** opcional que especifica el texto que aparece al pasar el mouse sobre el nombre del parámetro.
Propiedades opcionales para un módulo, como defaultValue, minValue y maxValue se pueden agregar a cualquier argumento como atributos a un elemento de **Propiedades** . Propiedades válidas para el elemento de **Propiedades** dependen del tipo de argumento y se describen con los tipos de los argumentos admitidos en la siguiente sección. Argumentos con la propiedad **isOptional** establecida en **"true"** no requieren que el usuario que especifique un valor. Si no se proporciona un valor para el argumento, el argumento no se pasa a la función de punto de entrada. Los argumentos de la función de punto de entrada que son opcionales que necesite corregirse explícitamente por la función, por ejemplo asigna un valor predeterminado de NULL en la definición de la función de punto de entrada. Un argumento opcional sólo aplicará las otras restricciones de los argumentos, es decir, Mín o Máx, si un valor proporcionado por el usuario.
Al igual que con las entradas y los resultados, es fundamental que cada uno de los parámetros tiene valores de identificador exclusivo asociados con ellos. En nuestro ejemplo de inicio rápido el parámetro/id asociado era *intercambiar*.

### <a name="arg-element"></a>Elemento de argumento
Un parámetro de módulo se define mediante el elemento secundario de **argumento** de la sección **argumentos** de un archivo de definición de XML. Como con los elementos secundarios en la sección de **puertos** , el orden de los parámetros de la sección **argumentos** define el diseño encontrado en la UX. Aparecen los parámetros de arriba hacia abajo en la interfaz de usuario en el mismo orden en que se definen en el archivo XML. Se enumeran los tipos compatibles con el aprendizaje de parámetros. 

**int** : un parámetro de tipo (32 bits) entero.

    <Arg id="intValue1" name="Int Param" type="int">
        <Properties min="0" max="100" default="0" />
        <Description>Integer Parameter</Description>
    </Arg>


* *Propiedades opcionales*: **min**, **max**, **predeterminado** y **isOptional**

**doble** : un parámetro de tipo double.

    <Arg id="doubleValue1" name="Double Param" type="double">
        <Properties min="0.000" max="0.999" default="0.3" />
        <Description>Double Parameter</Description>
    </Arg>


* *Propiedades opcionales*: **min**, **max**, **predeterminado** y **isOptional**

**bool** : un parámetro booleano representada por una casilla de verificación en UX.

    <Arg id="boolValue1" name="Boolean Param" type="bool">
        <Properties default="true" />
        <Description>Boolean Parameter</Description>
    </Arg>



* *Propiedades opcionales*: **predeterminado** : false si no ha establecido

**cadena**: una cadena estándar

    <Arg id="stringValue1" name="My string Param" type="string">
        <Properties isOptional="true" />
        <Description>String Parameter 1</Description>
    </Arg>  

* *Propiedades opcionales*: **predeterminada** y **isOptional**

**ColumnPicker**: un parámetro de la selección de columna. Este tipo se representa en la UX como un selector de columnas. El elemento de **propiedad** se usa aquí para especificar el identificador del puerto desde el que se seleccionan las columnas, donde el tipo de puerto de destino debe ser la *tabla de datos*. El resultado de la selección de columna se pasa a la función R como una lista de cadenas que contiene los nombres de columna seleccionada. 

        <Arg id="colset" name="Column set" type="ColumnPicker">   
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* *Propiedades necesarias*: **portId** - coincide con el id de un elemento de entrada con el tipo de *tabla de datos*.
* *Propiedades opcionales*:
    * **allowedTypes** - filtra los tipos de columna en el que puede seleccionar. Los valores válidos son: 
        *   Numérico
        *   Valor booleano
        *   Por categorías
        *   Cadena
        *   Etiqueta
        *   Característica
        *   Puntuación
        *   Todos los

    * **predeterminado** - selecciones predeterminadas válida para el selector de columna incluyen: 
        * Ninguno
        * NumericFeature
        * NumericLabel
        * NumericScore
        * NumericAll
        * BooleanFeature
        * BooleanLabel
        * BooleanScore
        * BooleanAll
        * CategoricalFeature
        * CategoricalLabel
        * CategoricalScore
        * CategoricalAll
        * StringFeature
        * StringLabel
        * StringScore
        * StringAll
        * AllLabel
        * AllFeature
        * AllScore
        * Todos los

                                                        
**Lista desplegable**: una lista enumerado especificado por el usuario (desplegable). Los elementos de la lista desplegable se especifican en el elemento de **Propiedades** con **un elemento** . El **identificador** de cada **elemento** debe ser único y una variable R válida. El valor del **nombre** de un **elemento** sirve como el texto que aparece y el valor que se pasa a la función R.

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>  

* *Propiedades opcionales*:
    * **predeterminado** : el valor de la propiedad predeterminada debe corresponder con un valor de id de uno de los elementos de **elemento** .


### <a name="auxiliary-files"></a>Archivos auxiliares

Cualquier archivo que se coloca en el archivo ZIP de módulo personalizado va a estar disponible para su uso durante el tiempo de ejecución. Se mantienen las estructuras de directorio actual. Esto significa ese archivo origen funciona la misma localmente y en ejecución de aprendizaje del equipo de Azure. 

> [AZURE.NOTE] Observe que todos los archivos se extraen al directorio 'src' por lo que deberían tener todas las rutas ' src /' prefijo.

Por ejemplo, supongamos que desea quitar las filas con NAs del conjunto de datos y también quitar las filas duplicadas, antes de generar en CustomAddRows y ya ha escrito una función R que lo hace en un archivo RemoveDupNARows.R:

    RemoveDupNARows <- function(dataFrame) {
        #Remove Duplicate Rows:
        dataFrame <- unique(dataFrame)
        #Remove Rows with NAs:
        finalDataFrame <- dataFrame[complete.cases(dataFrame),]
        return(finalDataFrame)
    }
Origen de archivo auxiliar RemoveDupNARows.R en la función CustomAddRows:

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) {
        source("src/RemoveDupNARows.R")
            if (swap) { 
                dataset <- rbind(dataset2, dataset1))
            } else { 
                dataset <- rbind(dataset1, dataset2)) 
            } 
        dataset <- removeDupNARows(dataset)
        return (dataset)
    }

A continuación, cargar un archivo zip con 'CustomAddRows.R', 'CustomAddRows.xml' y 'RemoveDupNARows.R' como un módulo R personalizado.


## <a name="execution-environment"></a>Entorno de ejecución

El entorno de ejecución de la secuencia de comandos de R usa la misma versión de R como el módulo de **Ejecutar Script de R** y puede usar los mismos paquetes de forma predeterminada. También puede agregar paquetes R adicionales a su módulo personalizado incluye en el paquete zip de módulo personalizado. Simplemente cargarlos en el script de R como lo haría en su propio entorno R. 

**Limitaciones del entorno de ejecución de** incluyen:

* Sistema de archivos no persistentes: no se conservan los archivos escritos cuando se ejecuta el módulo personalizado a través de varias ejecuciones del mismo módulo.
* Sin acceso a la red



 
