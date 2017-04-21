<properties title="" pageTitle="Escribir la documentación de Azure - hoja de voz y el estilo de referencia sobre" description="Información de voz y el estilo que le ayudarán a crear contenido técnico del centro de documentación Azure." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="required" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="12/16/2014" ms.author="glenga" />

#<a name="writing-azure-documentation---style-and-voice-cheat-sheet"></a>Escribir la documentación de Azure - hoja de voz y el estilo de referencia sobre

Aquí tiene una hoja de referencia rápida que contiene punteros sobre cómo escribir artículos técnicos para tecnologías y servicios de Azure. Estas instrucciones se aplican si va a crear la nueva documentación o actualización de documentación existente.

Como mínimo, tenga en:

- Corrector ortográfico y gramatical Active los temas, incluso si tiene que cortar y pegar en Word para hacerlo.
- Utilice una voz informal y descriptiva, como estuviera hablando a otra persona individuales.
- Utilice oraciones simples. Son más fáciles de entender y traductores humanos y máquinas más fácilmente los traducir.

Las secciones siguientes contienen una más detalles:

+ [Usar una voz e intuitivo]
+ [Considere la posibilidad de localización y traducción automática]
+ [Otros problemas de voz y el estilo para inspeccionar]


##<a name="use-a-customer-friendly-voice"></a>Usar una voz e intuitivo

Aspiramos siga estos principios al escribir contenido técnico para Azure. Nos podemos no siempre acceder a ellos, pero es necesario mantener intentando!

- **Usar palabras comunes**: intenta usar lenguaje natural, las palabras que sus clientes usar; ser menos formal, pero no menos técnicos; proporcionan ejemplos que explican conceptos nuevos.

- **Escribir brevemente**: no pierda palabras y no se explica más. Ser positiva y no use palabras o una gran cantidad de cualificadores. Mantener oraciones cortos y concisos. Mantener el tema centrado. Si una tarea tiene un calificador, coloque al principio de la frase o párrafo. Además, mantenga el número de notas al mínimo. Use una captura de pantalla cuando pueden guardar palabras.

- **Fácil de explorar**: colocar las cosas más importantes primero. Usar secciones para fragmentar procedimientos largos en más fáciles de administrar grupos de pasos (procedimientos con más de 12 pasos probablemente son demasiado largos). Use una captura de pantalla cuando agregan claridad.

- **Mostrar empatía**: utilice un tono de apoyo en el artículo y mantener un mínimo de exención de responsabilidad. Honestamente llamar a las áreas que serán frustrantes a clientes. Asegúrese de que el artículo se centra en lo que importa al cliente, simplemente no le dan una charla técnica.

##<a name="consider-localization-and-machine-translation"></a>Considere la posibilidad de localización y traducción automática
Nuestros artículos técnicos se convierten en muchos otros idiomas y algunos se modifican para mercados determinados. Personas que también esté utilizando traducción automática en la web para leer los artículos técnicos. Por lo tanto, escribir con las directrices siguientes en cuenta:

- **Asegúrese de que el artículo no contiene gramática, ortografía o errores de puntuación**: se trata de algo deberíamos hacer en general. Teclado de descuento 2.0 tiene un corrector ortográfico básicos, pero también debe pegar (HTML representado) contenido desde el artículo en Word, que tiene una más rigurosa corrector ortográfico y gramatical.

- **Realizar la mayor breves posible oraciones**: compuesto oraciones o cadenas de cláusulas dificultan la traducción. Dividir una oración si puede hacerlo sin ser demasiado redundantes o extraño parecidos. No quiere artículos escritos en lenguaje natural bien.

- **Construcción de frase simple y coherente de uso**: coherencia es mejor para su traducción. Evite parentheticals y aparte y asunto sea cerca del principio de la oración como sea posible. Consulte algunos temas publicados - si el tema tiene una sencilla, fácil de leer estilo, como un modelo.

- **Términos que denotan coherente de uso de mayúsculas y minúsculas**: nuevamente, coherencia es clave. Azure usa mayúsculas y minúsculas de oración para títulos, así que nunca en mayúscula la palabra si todavía no lo al comienzo de una frase o un sustantivo NOMPROPIO.

- **Incluir "palabras pequeñas"**: palabras que se considere importantes en inglés y pequeña debido a se entienden de contexto (como "a", "la", "que" y "es") son fundamentales para la traducción automática - Asegúrese de que se incluyan!

##<a name="other-style-and-voice-issues-to-watch-for"></a>Otros problemas de voz y el estilo para inspeccionar

- No dividir pasos con comentarios o aparte.

- Para conocer los pasos que incluyen los fragmentos de código, coloque la información adicional sobre el paso en el código como comentarios. Esto reduce la cantidad de texto que los usuarios tendrán que lea y se copiará la información de clave en el proyecto de código para recordar a las personas de lo que hace el código al que hacen referencia a él más adelante.

- El nombre de producto oficial es "Microsoft Azure", pero, podemos casi siempre decir "Azure", como en "Azure Mobile Services".

- No crear acrónimos que empiecen por "MA" o "a". Utilice "Azure" de la primera referencia antes de un nombre de servicio o característica y, a continuación, suéltelo (por ejemplo, "Azure Mobile Services" se convierte en "Servicios móviles" después de usar por primera vez). Evite acrónimos en general: confusiones.

- Azure usa mayúsculas y minúsculas de oración para todos los títulos.

- Utilice "sesión" y no "log."

- Incluir las palabras "seguir" o "siguiente" en cada oración que precede a un fragmento de código de lista.

- "Base de datos SQL" es la característica de Azure. Una "base de datos SQL" es una instancia de base de datos que se ejecuta en la base de datos de SQL.

- Almacenamiento de Azure incluye varios "data management services" que incluyen el servicio de la tabla, el servicio de blobs de Windows y el servicio de cola. (No se llama "servicio de almacenamiento de tablas de Azure".)




###<a name="contributors-guide-links"></a>Vínculos de la Guía de colaboradores

- [Artículo de información general](./../README.md)
- [Índice de artículos de guía](./contributor-guide-index.md)



<!--Anchors-->
[Usar una voz e intuitivo]: #use-a-customer-friendly-voice
[Considere la posibilidad de localización y traducción automática]: #consider-localization-and-machine-translation
[otros problemas de voz y el estilo para inspeccionar]: #other-style-and-voice-issues-to-watch-for
