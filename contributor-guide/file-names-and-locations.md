<properties title="" pageTitle="Nombres de archivo y ubicaciones para artículos técnicos de Azure" description="Explica la estructura de archivo para los artículos y las convenciones de nomenclatura que debería seguir al crear un nuevo artículo." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="required" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="03/14/2016" ms.author="tysonn" />

#<a name="file-names-and-locations-for-azure-technical-articles"></a>Nombres de archivo y ubicaciones para artículos técnicos de Azure

En nuestro repositorio de contenido técnico, usamos una sola carpeta (la carpeta de **artículos** ) para todos los artículos. No hay ninguna jerarquía de carpetas: todos los artículos de live en la estructura de archivo plano. Si crea carpetas con artículos en ellos, no se pueden publicar sus artículos.

En lugar de usar una estructura de archivos como un principio de la organización, usamos una convención de nomenclatura estricta de archivo que identifica claramente temas y que contribuyen a detectabilidad en la web.

Aquí es lo que necesita saber:

+ [Reglas]
+ [Trama]
+ [Ejemplos de estándares]
+ [Contenido del catálogo de soluciones]
+ [Aprobación de nombre de archivo]

##<a name="rules"></a>Reglas

- Nombres de archivos pueden contener sólo letras minúsculas, números y guiones. 
- Sin espacios ni caracteres de puntuación. Utilice los guiones para separar palabras y números en el nombre de archivo.
- Caracteres no más de 80 - se trata de un límite de sistema de publicación
- Verbos de acción de uso específicas, como desarrollarán, compran, creación y solucionar problemas. Sin palabras - ión.
- No incluir ninguna palabra pequeña - una y, la, en o, etcetera.
- Todos los archivos deben estar en descuento y usar la extensión de archivo .md.
- Revisar las palabras evitar acrónimos no aprobadas o innecesarios en nombres de archivo

Acrónimos y initialisms en nombres de archivo - directrices específicas:

- No debe abreviar los nombres de servicio de Azure: las primeras palabras del nombre de archivo deben ser el estándar deletrear el nombre de servicio o la tecnología de Azure. 
-   No permitir rm o arm como acrónimos en cualquier lugar de un nombre de archivo
- Otras abreviaturas estándar son aceptables según sea necesario en nombres de archivo. 

##<a name="pattern"></a>Trama

Esto es el patrón general:

 **Service-Platform-Language-Content-Product-Version.MD**

Usar los elementos del patrón que aplicarán y revisión la lista de artículos en el repositorio para hacerse una idea de nombres existentes. Nombres que no empiecen por una plataforma de desarrollo o un nombre de servicio es probablemente sospechoso y retrasado por.

##<a name="standard-examples"></a>Ejemplos de estándares

Estos son algunos ejemplos de nombres válidos que sigan el patrón. :

- nube-servicios-dotnet-continuo-delivery.md
- Mobile servicios ios get started.md
- account.md administrar documentdb
- Mobile-Services-dotnet-backend-Get-Started-Settings-Sync.MD
- Active-Directory-Java-Authenticate-Users-Access-Control-Eclipse.MD
- Virtual-Machines-Install-Windows-Server-2008R2.MD
- caché aspnet sesión estado proveedor
- Azure-SDK-dotnet-Release-Notes-2-8
- storsimple-Disaster-Recovery-Using-Azure-Site-Recovery

##<a name="marketplace-content"></a>Contenido del catálogo de soluciones

Para distinguir el contenido que se centra en aportaciones de socios para Azure marketplace, empiece los nombres de archivo con "marketplace". Este contenido no debe ser demasiado comunes, como la mayor parte del contenido asociado debe crearse en los sitios web de socios propio.

- Marketplace-MongoDB-Virtual-Machines-Install-Windows-Server-2008R2.MD

##<a name="file-name-approval"></a>Aprobación de nombre de archivo

Es el trabajo de nuestro grupo de revisores de solicitud de extracción para revisar los nombres de archivo cuando se envía un archivo nuevo en el repositorio por primera vez. Revisores de solicitud de extracción deben revisar el nombre de archivo y proporcionar comentarios a través de la secuencia de comentario de solicitud de extracción si es necesario realizar cambios. El nombre de archivo debe corregirse antes de que se acepte la solicitud de extracción. Los colaboradores pueden insertar fácilmente la actualización a la solicitud de extracción pendiente.

##<a name="folder-names-in-the-repo"></a>Nombres de las carpetas en la repo

Se deben crear carpetas para los servicios y el nombre de archivo debe coincidir con la indicación de servicio. Utilice sólo letras y guiones y todas las letras en minúscula. Obtener la aprobación del Administrador de repositorio antes de crear una nueva carpeta que no es un servicio de lanzamiento.

##<a name="changing-case-in-file-names"></a>Cambiar mayúsculas y minúsculas en los nombres de archivo

Sistemas operativos Windows distinguen mayúsculas de minúsculas, por lo que si necesita cambiar un nombre de archivo para la corrección de mayúsculas y minúsculas, es preferible hacer un cambio de fondo, a menos que puede realizar el cambio en Linux o Mac. Por ejemplo:

  biztalk-administration-and-Development-Task-List-in-BizTalk-Services--> biztalk-services-administration-and-development-task-list

Use el comando siguiente para cambiar el nombre de un archivo:
```
  git mv <articles/service-folder/current-file-name.md> <articles/service-folder/new-file-name>
```

###<a name="contributors-guide-links"></a>Vínculos de la Guía de colaboradores

- [Artículo de información general](./../README.md)
- [Índice de artículos de guía](./contributor-guide-index.md)


<!--Anchors-->
[Reglas]: #rules
[Trama]: #pattern
[Ejemplos de estándares]: #standard-examples
[Contenido del catálogo de soluciones]: #marketplace-content
[Aprobación de nombre de archivo]: #file-name-approval
