<properties
    pageTitle="Ejemplos de Lucene de consulta de búsqueda de Azure | Búsqueda de Microsoft Azure"
    description="Lucene la sintaxis de consulta de búsqueda parcial, proximidad búsqueda, aumentar el término, búsqueda de expresiones regulares y búsqueda con caracteres comodín."
    services="search"
    documentationCenter=""
    authors="LiamCa"
    manager="pablocas"
    editor=""
    tags="Lucene query analyzer syntax"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="liamca"
/>

# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>Ejemplos de sintaxis de consulta de Lucene para crear consultas de búsqueda de Azure

Al crear consultas de búsqueda de Azure, puede usar la [sintaxis de la consulta simple](https://msdn.microsoft.com/library/azure/dn798920.aspx) de predeterminado o el alternativo [Lucene analizador de consultas de búsqueda de Azure](https://msdn.microsoft.com/library/azure/mt589323.aspx). El analizador de consultas de Lucene admite más complejas construcciones de consulta, como consultas de ámbito de campo, búsqueda parcial, búsqueda de proximidad, aumentar el término y búsqueda de la expresión de reqular.

En este artículo, puede recorrer los ejemplos que muestran la sintaxis de la consulta Lucene y resultados en paralelo. Ejemplos de ejecutar en un índice de búsqueda previamente cargado en [JSFiddle](https://jsfiddle.net/), un editor de código en línea para probar secuencias de comandos y HTML. 

Haga clic en las direcciones URL de ejemplo de consulta para abrir JSFiddle en una ventana independiente del explorador.

> [AZURE.NOTE] Los ejemplos siguientes aprovechen un índice de búsqueda que consta de trabajos disponibles en función de un conjunto de datos proporcionado por [Ciudad de Nueva York OpenData](https://nycopendata.socrata.com/) iniciativa. No se deben considerar estos datos actual o completado. El índice es en un servicio de espacio aislado proporcionado por Microsoft. No es necesario una suscripción Azure o la búsqueda de Azure puede probar estas consultas.

## <a name="viewing-the-examples-in-this-article"></a>Ver los ejemplos de este artículo

Todos los ejemplos de este artículo especifican el analizador de consultas de Lucene mediante el parámetro de búsqueda**queryType** . Cuando se usa el analizador de consultas de Lucene desde el código, deberá especificar el **queryType** en cada solicitud.  Los valores válidos son **sencillo**|**completa**, con **simple** como la predeterminada y **completa** para el analizador de consulta Lucene. Ver [Documentos de búsqueda (API de REST de servicio de búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx) para obtener más información acerca de cómo especificar los parámetros de la consulta.

**Ejemplo 1** : haga la siguiente consulta fragmento de código para abrirlo en una página nueva del explorador que carga JSFiddle y ejecuta la consulta:
- [& queryType = completo y buscar = *](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

Esta consulta devuelve los documentos de nuestro índice de trabajos (cargado en un servicio de espacio aislado)

En la ventana del explorador, verá el origen de JavaScript y el resultado HTML en paralelo. La secuencia de comandos hace referencia a una consulta, que proporciona las direcciones URL de ejemplo en este artículo. Por ejemplo, en el **ejemplo 1**, la consulta subyacente es la siguiente:

    http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

Aviso de la consulta usa un índice de búsqueda de Azure preconfigurado denominado nycjobs. El parámetro **searchFields** restringe la búsqueda a solo el campo de título de empresa. El **queryType** se establece en **completa**, lo que indica que la búsqueda de Azure para usar el analizador de consultas de Lucene para esta consulta.

### <a name="fielded-query-operation"></a>Operación de consulta clasificadas por campos

Puede modificar los ejemplos de este artículo especificando una construcción **fieldname:searchterm** para definir una operación de consulta clasificadas por campos, donde el campo es una sola palabra, y el término de búsqueda también es una sola palabra o una frase, de manera opcional con operadores booleanos. Algunos ejemplos incluyen las siguientes:

- business_title:(senior NOT junior)
- estado: ("Nueva York" y "Nueva Jersey")

Asegúrese de poner varias cadenas entre comillas si desea que las dos cadenas que se debe evaluar como una sola entidad, como en este caso buscando dos ciudades distintas en el campo ubicación. Además, asegúrese de se pone en mayúsculas el operador verá con no y and.

El campo especificado en **fieldname:searchterm** debe ser un campo de búsqueda. Para obtener más información sobre cómo se usan los atributos de índice en definiciones de campo, consulte [Create Index (API de REST de servicio de búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx) .

## <a name="fuzzy-search"></a>Búsqueda aproximada

Una búsqueda parcial encuentra coincidencias en términos que tienen una construcción similar. Por [Lucene documentación](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), búsquedas parciales se basan en [Distancia Damerau Levenshtein](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Para realizar una búsqueda parcial, use la tilde "~" símbolo al final de una sola palabra con un parámetro opcional, un valor entre 0 y 2, que especifica la distancia de edición. Por ejemplo, "azul ~" o "azul ~ 1" devolverá azul, azules y pegar.

**Ejemplo 2** : contextual el fragmento de la consulta siguiente para probarlo. Esta consulta busca los títulos de empresa con el jefe de términos en ellos, pero no sin experiencia:

- [& queryType = completo y buscar = business_title:senior no sin experiencia](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="proximity-search"></a>Búsqueda de proximidad

Búsquedas de proximidad se utilizan para encontrar términos que están cerca de la otra en un documento. Insertar una tilde "~" símbolo al final de una frase seguida del número de palabras que crear el límite de proximidad. Por ejemplo, "hotel airport" ~ 5 encontrará el hotel términos y aeropuerto dentro de 5 palabras en un documento.

**Ejemplo 3** : contextual el siguiente fragmento de consulta. Esta consulta busca trabajos con la asociar términos (donde se está mal escrito):

- [& queryType = completo y buscar = business_title:asosiate ~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

**Ejemplo 4** : botón secundario en la consulta. Buscar trabajos con el término "analista" donde está separada por no más de una palabra:

- [& queryType = completo y buscar = business_title: "analista" ~ 1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**Ejemplo 5** : pruebe a quitar las palabras entre el término "analista".

- [& queryType = completo y buscar = business_title: "analista" ~ 0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting"></a>Aumentar de términos

Aumentar el término hace referencia a un documento posterior si contiene el término aumentado, con respecto a los documentos que no contienen el término de clasificación. Esto difiere de puntuación perfiles en que los perfiles de puntuación mejore determinados campos, en lugar de términos específicos. En el ejemplo siguiente se le ayuda a ilustrar las diferencias.

Considere la posibilidad de coincide con un perfil de puntuación aumenta en un campo, por ejemplo, **género** en el ejemplo musicstoreindex. Aumentar el término podría utilizarse para aumentar aún más determinada búsqueda términos superiores a otras personas. Por ejemplo, "roca ^ 2 electrónica" aumentarán los documentos que contienen los términos de búsqueda en el campo de **género** superior a otros campos de búsqueda en el índice. Además, los documentos que contienen el término de búsqueda "roca" se valoración superior que el otro término de búsqueda "electrónico" por el valor de amplificación términos (2).

Para aumentar un término, utilice el símbolo de intercalación, "^", símbolo con un factor de amplificación (número) al final del término que está buscando. Cuanto mayor sea el factor de amplificación, más relevantes será el término en relación con otros términos de búsqueda. De forma predeterminada, el factor de amplificación es 1. Aunque el factor de amplificación debe ser positivo, puede ser menor que 1 (por ejemplo, 0,2).

**Ejemplo 6** : botón secundario en la consulta. Buscar trabajos con el término "analista de equipo" donde vemos que no hay ningún resultado con el equipo de palabras y analista todavía trabajos de analista están en la parte superior de los resultados.

- [& queryType = completo y buscar = analista de business_title:computer](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**Ejemplo 7** : inténtelo de nuevo, este aumento de tiempo de resultados con el equipo de términos sobre el analista de término si ambas palabras no existe.

- [& queryType = completo y buscar = business_title:computer ^ 2 analista](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression"></a>Expresión regular

Búsqueda de una expresión regular encuentra a una coincidencia basada en el contenido entre diagonales "/", como documentado en la [clase RegExp](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Ejemplo 8** : botón secundario en la consulta. Buscar el término Senior o primer ciclos de trabajos con cualquiera.

- `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

La dirección URL de este ejemplo no se representará correctamente en la página. Como solución alternativa, copie la dirección URL siguiente y péguela en la dirección URL de explorador:    `http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`


## <a name="wildcard-search"></a>Búsqueda con caracteres comodín

Puede utilizar sintaxis generalmente reconocido de varias (\*) o simples (?) realizar búsquedas de caracteres comodín. Tenga en cuenta que el analizador de consultas de Lucene admite el uso de estos símbolos con un término único y no una frase.

**Ejemplo 9** : botón secundario en la consulta. Buscar trabajos que contienen el prefijo 'programa' que se incluye títulos de empresa con los términos de programación y programador en él.

- [& queryType = completo & $select = business_title & búsqueda = business_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26queryType=full%26$select=business_title%26search=business_title:prog*)

¿No puede usar un * o? símbolo como el primer carácter de una búsqueda.


## <a name="next-steps"></a>Pasos siguientes

Pruebe a especificar el analizador de consultas de Lucene en el código. Los vínculos siguientes explican cómo configurar las consultas de búsqueda para .NET y las API de REST. Los vínculos use la sintaxis predeterminada de simple para que debe aplicar lo que ha aprendido de este artículo para especificar el **queryType**.

- [Consultar el índice de búsqueda de Azure mediante el SDK de .NET](search-query-dotnet.md)
- [Consultar el índice de búsqueda de Azure con la API de REST](search-query-rest-api.md)


 