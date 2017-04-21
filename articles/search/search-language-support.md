<properties
   pageTitle="Crear un índice para documentos en varios idiomas en búsqueda de Azure | Microsoft Azure | Servicio de nube hospedado de búsqueda"
   description=" Búsqueda de Azure admite 56 idiomas, aprovechar analizadores de idioma de tecnología Lucene y procesamiento de lenguaje Natural de Microsoft."
   services="search"
   documentationCenter=""
   authors="yahnoosh"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="07/14/2016"
   ms.author="jlembicz"/>

# <a name="create-an-index-for-documents-in-multiple-languages-in-azure-search"></a>Crear un índice para documentos en varios idiomas en búsqueda de Azure
> [AZURE.SELECTOR]
- [Portal](search-language-support.md)
- [REST](https://msdn.microsoft.com/library/azure/dn879793.aspx)
- [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)

Liberación de la potencia de los analizadores de idioma es tan fácil como una propiedad de configuración en un campo de búsqueda en la definición de índice. Ahora puede realizar este paso en el portal.

A continuación, le capturas de pantalla de los módulos de Azure Portal para la búsqueda de Azure que permiten a los usuarios definir un esquema de índice. Desde este módulo, los usuarios pueden crear todos los campos y establezca la propiedad de analizador para cada uno de ellos.

> [AZURE.IMPORTANT] Solo puede establecer un analizador de idioma durante la definición de campo, como en al crear un nuevo índice desde el principio hacia arriba o al agregar un nuevo campo a un índice existente. Asegúrese de que especifique completamente todos los atributos, incluido el analizador al crear el campo. No podrá editar los atributos o cambiar el tipo de analizador una vez que guarde los cambios.

## <a name="define-a-new-field-definition"></a>Definir una nueva definición de campo

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com) y abra el módulo de servicio del servicio de búsqueda.
2. Haga clic en **Agregar índice** en la barra de comandos en la parte superior del panel de servicio para iniciar un nuevo índice o abra un índice existente para configurar un analizador de en los nuevos campos que se va a agregar a un índice existente.
3. Aparece el módulo de campos, las opciones para definir el esquema de índice, incluida la ficha Analyzer utiliza para elegir un analizador de idioma.
4. En campos, inicie una definición de campo al proporcionar un nombre, elegir el tipo de datos y establecer los atributos para marcar el campo como texto que permiten búsquedas, puede recuperar resultados de búsqueda, puede usar en estructuras de navegación de faceta clasificable y así sucesivamente. 
5. Antes de pasar al siguiente campo, abra la pestaña **Analyzer** . 

   
![][1]
*Para seleccionar un análisis, haga clic en la pestaña Analyzer en el módulo de campos*

## <a name="choose-an-analyzer"></a>Elija un analizador

6. Desplácese para buscar el campo que se va a definir. 
7. Si todavía no lo ha marcado el campo de búsqueda, haga clic en la casilla de verificación para marcar como **Searchable**.
8. Haga clic en el área de analizador para mostrar la lista de los analizadores disponibles.
9. Elija el analizador que desea usar.

![][2]
*Seleccione uno de los analizadores compatibles para cada campo*

De forma predeterminada, todos los campos de búsqueda usan el [Analizador de Lucene estándar](http://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) que es independiente del idioma. Para ver la lista completa de los analizadores admitidos, vea [Compatibilidad con idiomas en búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn879793.aspx).

Una vez que el analizador de idioma está activado para un campo, se utilizará con cada solicitud de indización y búsqueda de ese campo. Cuando se ejecuta una consulta en varios campos con diferentes analizadores, la consulta se procesa de manera independiente por los analizadores adecuados para cada campo.

Muchos web y aplicaciones móviles son usuarios todo el mundo con diferentes idiomas. Es posible definir un índice para un escenario similar a esta mediante la creación de un campo para cada idioma admitido.

![][3]
*Definición de índice con un campo de descripción para cada idioma admitido*

Si se conoce el idioma del agente emitir una consulta, puede definirse una solicitud de búsqueda a un campo específico con el parámetro de consulta **searchFields** . La siguiente consulta será emitida sólo con respecto a la descripción en polaco:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2015-02-28`

Puede consultar el índice desde el portal, con **el Explorador de búsqueda** para pegar en una consulta similar a la anterior. Búsqueda del explorador está disponible en la barra de comandos en el módulo de servicio. Para obtener información detallada, consulte [el índice de búsqueda de Azure en el portal de la consulta](search-explorer.md) .

A veces el idioma del agente emitir una consulta no se conoce, en cuyo caso la consulta pueda emitir en todos los campos al mismo tiempo. Si es necesario, puede definirse preferencia de resultados en un idioma específico con [puntuación perfiles](https://msdn.microsoft.com/library/azure/dn798928.aspx). En el ejemplo siguiente, coincidencias encontradas en la descripción en inglés será un valor superior en relación con coincidencias en polaco y francés:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2015-02-28`

Si es un desarrollador de .NET, tenga en cuenta que puede configurar los analizadores de idioma mediante la [Búsqueda de Azure .NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Search). La última versión incluye compatibilidad con los analizadores de idioma de Microsoft.

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png
