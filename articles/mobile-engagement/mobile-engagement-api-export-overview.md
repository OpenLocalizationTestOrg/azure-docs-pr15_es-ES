<properties
    pageTitle="Introducción a la API exportar compromiso móvil"
    description="Aprenda los conceptos básicos sobre cómo exportar los datos sin formato generados por los dispositivos de su usuario aprovechar en sus propias herramientas"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="kpiteira"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile"
    ms.date="04/26/2016"
    ms.author="kpiteira"/>

# <a name="mobile-engagement-export-api-overview"></a>Introducción a la API exportar compromiso móvil

## <a name="introduction"></a>Introducción

En este documento, obtendrá información sobre los conceptos básicos sobre cómo exportar los datos sin formato generados por los dispositivos de su usuario aprovechar en sus propias herramientas.

## <a name="pre-requisites"></a>Requisitos previos

Exportar los datos sin formato de contratación Mobile requiere:

- Configuración de autenticación de API para que pueda usar la API (consulte [configuración manual de autenticación](mobile-engagement-api-authentication-manual.md)),
- Use las API REST o [.net SDK](mobile-engagement-dotnet-sdk-service-api.md)
- Una cuenta de almacenamiento de Azure.

>[AZURE.NOTE] También recomendamos el [Explorador de Microsoft Azure almacenamiento](http://storageexplorer.com/)de excelente, al menos durante la fase de desarrollo ya que proporciona una interfaz de usuario fácil de usar para interactuar con el almacenamiento de Azure.

## <a name="what-can-be-exported"></a>¿Qué se puede exportar?

Compromiso de Mobile permite a sus usuarios recopilar muchos tipos de datos y, por tanto, tiene varios tipos de exportación que se adapte a estos tipos de datos diferentes.
Hay 2 tipos esenciales de exportación:

- Instantánea: utilizar normalmente para exportar los datos que representa un estado y que Mobile compromiso no tiene un historial. Esto incluye etiquetas (aplicación info), tokens o inserción campaña votos por ejemplo. En consecuencia, estos tipos de exportación no están relacionados a una fecha.
- histórico: se usa este tipo de exportación de datos que se acumulan con el tiempo como eventos o actividades por ejemplo.

La siguiente tabla describe la exhaustiva todas las posibles exportaciones:

| Tipo de exportación | Tipo de datos | Descripción                                                                                                                                 |
|-------------|-----------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Instantánea    | Inserción      | Genera una exportación de votos campañas de inserción de una forma por deviceid/ID de usuario                                                              |
| Instantánea    | Etiqueta       | Genera una exportación de las etiquetas (información de la aplicación) asociados a cada dispositivos                                                                       |
| Instantánea    | Dispositivo    | Genera una exportación de la mayoría de los datos sobre dispositivos como la technicals (modelo, configuración regional, zona horaria,...), las etiquetas, la primera vez visto... |
| Instantánea    | Token     | Genera una exportación de todos los tokens válidos                                                                                                 |
| Histórica  | Actividad  | Genera una exportación de todas las actividades para cada dispositivos en un período de tiempo                                                           |
| Histórica  | Evento     | Genera una exportación de todas las actividades para cada dispositivos en un período de tiempo                                                           |
| Histórica  | Trabajo       | Genera una exportación de todos los trabajos para cada dispositivos en un período de tiempo                                                                 |
| Histórica  | Error     | Genera una exportación de todos los errores para cada dispositivos en un período de tiempo                                                               |

## <a name="how-does-it-work"></a>¿Cómo funciona?

Exportaciones son largas ejecutando las tareas que puede producir archivos grandes de datos. Por este motivo, no se puede invocar para devolver inmediatamente descargar un archivo.
Para exportar datos de contratación Mobile, tendrá que crear un **Trabajo de exportación** a través de la API donde puede especificar generalmente:

- El tipo de exportación (instantánea o histórico)
- El tipo de datos
- El **Contenedor de almacenamiento de Azure** (incluyendo una SA válido con acceso de escritura) donde se escribirá el resultado de la exportación.

Tenga en cuenta que puede tardar unos minutos para su trabajo iniciarlo y, a continuación, se puede ejecutar desde unos segundos para las aplicaciones pequeños a varias horas para las aplicaciones con una gran cantidad de usuarios o actividad.

Una vez creada la tarea, es posible comprobar su estado para ver si se está ejecutando o si se ha completado.

Una vez que la tarea se realizó correctamente, el archivo de datos resultante está disponible en el contenedor de almacenamiento proporcionado.
