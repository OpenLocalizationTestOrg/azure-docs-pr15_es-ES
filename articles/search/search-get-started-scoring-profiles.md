<properties 
    pageTitle="Cómo usar la puntuación de perfiles en búsqueda de Azure | Microsoft Azure | Servicio de nube hospedado de búsqueda" 
    description="Ajustar la clasificación a través de perfiles de resultados de búsqueda de Azure, un servicio de búsqueda de nube hospedado en Microsoft Azure de búsqueda." 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="mblythe" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="heidist"/>

# <a name="how-to-use-scoring-profiles-in-azure-search"></a>Cómo usar perfiles de resultados de búsqueda de Azure

Perfiles de puntuación son una característica de Microsoft Azure búsqueda de personalizar el cálculo de resultados de búsqueda, que influyen en cómo se ordenan los elementos en una lista de resultados de búsqueda. Puede considerar que puntuación perfiles como una manera de importancia del modelo, al aumentar los elementos que cumplen los criterios predefinidos. Por ejemplo, supongamos que su aplicación es un sitio de reserva de hotel en línea. Al aumentar la `location` campo de búsqueda que incluyan un término como Seattle dará como resultado puntuaciones más altas para los elementos que tienen Seattle en el `location` campo. Tenga en cuenta que puede tener más de un perfil de puntuación o ninguno en absoluto, si la puntuación predeterminado es suficiente para la aplicación.

Para ayudarle a experimentar con los perfiles de puntuación, puede descargar una aplicación de ejemplo que usa perfiles de puntuación para cambiar el orden de clasificación de resultados de búsqueda. El ejemplo es una aplicación de consola, tal vez no muy realista para el desarrollo de la aplicación del mundo real: pero útil como herramienta de aprendizaje. 

La aplicación de ejemplo muestra la puntuación comportamientos con datos ficticios, denominados el `musicstoreindex`. La simplificar de la aplicación de ejemplo facilita la modificar consultas y perfiles de resultados y, a continuación, ver los efectos inmediatos en orden cuando se ejecuta el programa.

<a id="sub-1"></a>
## <a name="prerequisites"></a>Requisitos previos

La aplicación de ejemplo está escrita en C# mediante Visual Studio de 2013. Probar la gratuita [Visual Studio 2013 Express edition](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) si todavía no tiene una copia de Visual Studio.

Necesitará una suscripción de Azure y un servicio de búsqueda de Azure para completar el tutorial. Para obtener ayuda con la configuración del servicio, vea [crear un servicio de búsqueda en el portal](search-create-service-portal.md) .

[AZURE. INCLUIR [necesita una cuenta de Azure para completar este tutorial:](../../includes/free-trial-note.md)]

<a id="sub-2"></a>
## <a name="download-the-sample-application"></a>Descargar la aplicación de ejemplo

Vaya a la [Demostración de perfiles de puntuación de búsqueda de Azure](https://azuresearchscoringprofiles.codeplex.com/) en codeplex para descargar la aplicación de ejemplo que se describe en este tutorial.

En la ficha de código fuente, haga clic en **Descargar** para obtener un archivo zip de la solución. 

 ![][12]

<a id="sub-3"></a>
## <a name="edit-appconfig"></a>Editar app.config

1. Después de extraer los archivos, abra la solución en Visual Studio para editar el archivo de configuración.
1. En el Explorador de soluciones, haga doble clic en **app.config**. Este archivo especifica el extremo de servicio y un `api-key` utilizado para autenticar la solicitud. Puede obtener estos valores desde el Portal de clásico.
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
1. Vaya a Panel de servicio de búsqueda de Azure.
1. Haga clic en el icono de **Propiedades** para copiar la dirección URL del servicio
1. Haga clic en el mosaico de **teclas** para copiar la `api-key`.

Cuando haya terminado de agregar la dirección URL y `api-key` a app.config, configuración de la aplicación debe tener un aspecto similar a este:

   ![][11]


<a id="sub-4"></a>
## <a name="explore-the-application"></a>Explorar la aplicación

Prácticamente está listo para crear y ejecutar la aplicación, pero antes de hacerlo, eche un vistazo a los archivos JSON utilizados para crear y rellenar el índice.

**Schema.JSON** define el índice, incluidos los perfiles de puntuación que se resalta en esta demostración. Observe que el esquema define todos los campos que se utilizan en el índice, incluidos los campos que no se pueden buscar, como `margin`, que puede usar en un perfil de puntuación. En [Agregar un perfil de puntuación a un índice de búsqueda de Azure](http://msdn.microsoft.com/library/azure/dn798928.aspx)documentada puntuación sintaxis del perfil.

**Data1 3.json** proporciona los datos, álbumes de 246 a través de un conjunto de géneros. Los datos están una combinación de información del álbum y artistas real, con campos ficticios como `price` y `margin` usado para ilustrar las operaciones de búsqueda. Los archivos de datos se ajustan al índice y se cargan en el servicio de búsqueda de Azure. Una vez cargados e indizar los datos, puede emitir consultas.

**Program.cs** realiza las operaciones siguientes:

- Se abrirá una ventana de consola.

- Se conecta con la dirección URL del servicio de búsqueda de Azure y `api-key`.

- Elimina la `musicstoreindex` si existe.

- Crea un nuevo `musicstoreindex` con el archivo schema.json.

- Rellena el índice mediante los archivos de datos.

- Consulta el índice con cuatro consultas. Observe que los perfiles de puntuación se especificado como un parámetro de consulta. Todas las consultas de búsqueda para el mismo término, 'mejor'. La primera consulta muestra la puntuación de forma predeterminada. Las tres consultas restantes usan un perfil de puntuación.

<a id="sub-5"></a>
## <a name="build-and-run-the-application"></a>Crear y ejecutar la aplicación

Para descartar conectividad o problemas de referencia de ensamblado, crear y ejecutar la aplicación para asegurarse de que no existen problemas para trabajar primero. Debería ver una aplicación de consola abierto en segundo plano. Todas las consultas de cuatro ejecutarán en secuencia sin detenerse. En muchos sistemas, todo el programa se ejecuta en menos de 15 segundos. Si la aplicación de consola incluye un mensaje que indica "completado. Presione ENTRAR para continuar", el programa que se ha completado correctamente. 

Para comparar se ejecuta una consulta, puede marcar, copiar y pegar los resultados de la consulta desde la consola y pegarlos en un archivo de Excel. 

La ilustración siguiente muestra los resultados de la primera tres consultas en paralelo. Todas las consultas de usar el mismo término de búsqueda 'mejor', que aparece en varios títulos de álbum.

   ![][10]

La primera consulta utiliza la puntuación de forma predeterminada. Dado que el término de búsqueda sólo aparece en títulos de álbum y no se especifica ningún otro criterio, se devuelven artículos que tengan 'mejor' en el título del álbum en el orden en que los encuentra el servicio de búsqueda. 

La segunda consulta utiliza un perfil de puntuación, pero tenga en cuenta que el perfil no tenía ningún efecto. Los resultados son idénticos a los de la primera consulta. Esto es porque el perfil de puntuación aumenta un campo ('género') que no se germane a la consulta. El término de búsqueda 'mejor' no existe en cualquier campo 'género' de cualquier documento. Cuando un perfil de puntuación no tiene ningún efecto, los resultados están la misma que la puntuación de forma predeterminada.  

La consulta terceros es la primera prueba de puntuación impacto del perfil. El término de búsqueda es todavía 'mejor', por lo que estamos trabajando con el mismo conjunto de álbumes, pero porque el perfil de puntuación proporciona criterios adicionales que aumenta 'clasificación' y 'último-actualizado', algunos elementos se propulsión superior en la lista.

La ilustración siguiente muestra la consulta de la cuarta y última, aumentar 'margen'. El campo 'margen' es que no se puede buscar y no se puede devolver resultados de búsqueda. El valor de 'margen' se ha agregado manualmente a la hoja de cálculo para ilustrar el punto de elementos con mayores márgenes mostrarse superiores en la lista de resultados de búsqueda. 

   ![][9]

Ahora que experimentó con puntuación perfiles, pruebe a cambiar el programa para usar la sintaxis de la consulta diferente, puntuación perfiles o más datos. Vínculos de la sección siguiente proporcionan más información.

<a id="next-steps"></a>
## <a name="next-steps"></a>Pasos siguientes

Más información sobre perfiles de puntuación. Para obtener información detallada, vea [Agregar un perfil de puntuación a un índice de búsqueda de Azure](http://msdn.microsoft.com/library/azure/dn798928.aspx) .

Más información acerca de los parámetros de la sintaxis y consulta de búsqueda. Para obtener información detallada, vea [Buscar documentos (API de REST de búsqueda de Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx) .

¿Necesita paso atrás y obtener más información sobre la creación de índices? [Vea este vídeo](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh) para conocer los conceptos básicos.

<!--Anchors-->
[Prerequisites]: #sub-1
[Download the sample application]: #sub-2
[Edit app.config]: #sub-3
[Explore the application]: #sub-4
[Build and run the application]: #sub-5
[Next steps]: #next-steps

<!--Image references-->
[12]: ./media/search-get-started-scoring-profiles/AzureSearch_CodeplexDownload.PNG
[11]: ./media/search-get-started-scoring-profiles/AzureSearch_Scoring_AppConfig.PNG
[10]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX1.PNG
[9]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX2.PNG 