<properties 
    pageTitle="Descripción general del paquete de integración de empresa | Servicio de aplicaciones de Microsoft Azure | Microsoft Azure" 
    description="Usar las características de paquete de integración de Enterprise para habilitar escenarios empresariales proceso y la integración con servicios de aplicación de Microsoft Azure" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="msftman" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/08/2016" 
    ms.author="deonhe"/>

# <a name="enterprise-integration-with-xml-transforms"></a>Integración de Enterprise con transformaciones XML

## <a name="overview"></a>Información general
El conector de transformación de integración de Enterprise convierte los datos de un formato a otro formato. Por ejemplo, puede tener un mensaje entrante que contiene la fecha actual en el formato YearMonthDay. Puede usar una transformación para cambiar el formato de la fecha en el formato MonthDayYear.

## <a name="what-does-a-transform-do"></a>¿Qué hace una transformación?
Una transformación, que es también conocida como un mapa, consta de un esquema XML de origen (la entrada) y un esquema XML de destino (la salida). Puede usar diferentes funciones integradas para ayudar a manipular o controlar los datos, incluidos manipulación de cadenas, asignaciones condicional, expresiones aritméticas, formateadores en tiempo de fecha y construcciones de bucle incluso.

## <a name="how-to-create-a-transform"></a>¿Cómo puedo crear una transformación?
Puede crear un mapa de transformación mediante el [SDK de integración de Enterprise](https://aka.ms/vsmapsandschemas)de Visual Studio. Cuando haya terminado de crear y probar la transformación, cargue la transformación en su cuenta de integración. 

## <a name="how-to-use-a-transform"></a>Cómo usar una transformación
Después de cargar la transformación en su cuenta de integración, puede usarlo para crear una aplicación de lógica. La aplicación de lógica después desplazará las transformaciones siempre que se activa la aplicación lógica (y no hay contenido de entrada que necesita para transformar).

**Estos son los pasos para usar una transformación**:

### <a name="prerequisites"></a>Requisitos previos 
En la vista previa, necesitará:  

-  [Crear un contenedor de las funciones de Azure] (https://ms.portal.azure.com/#create/Microsoft.FunctionApp "Crear un contenedor de las funciones de Azure")  
-  [Agregar una función al contenedor de funciones de Azure] (https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-transform-function%2Fazuredeploy.json "Esta plantilla crea una función de webhook según C# azure con las capacidades de transformación para usar en escenarios de integración de aplicaciones de lógica")    
-  Crear una cuenta de integración y agregarle un mapa  

>[AZURE.TIP] Anote el nombre del contenedor de las funciones de Azure y la función Azure, necesitará en el siguiente paso.  

Ahora que ha tenido en cuenta los requisitos previos, es el tiempo para crear la aplicación de lógica:  

1. Crear una aplicación de lógica y [vincularlo a su cuenta de integración](./app-service-logic-enterprise-integration-accounts.md "información sobre cómo vincular una cuenta de integración a una aplicación de la lógica") que contenga el mapa.
2. Agregar un desencadenador de **solicitud - solicitud HTTP un cuando se reciba** a su aplicación lógica  
![](./media/app-service-logic-enterprise-integration-transforms/transform-1.png)    
3. Agregar la acción **Transformar XML** por seleccionando **Agregar una acción**   
![](./media/app-service-logic-enterprise-integration-transforms/transform-2.png)   
4. Escriba la palabra *transformar* en el cuadro de búsqueda para filtrar todas las acciones que desea usar  
![](./media/app-service-logic-enterprise-integration-transforms/transform-3.png)  
5. Seleccione la acción **Transformar XML**   
![](./media/app-service-logic-enterprise-integration-transforms/transform-4.png)  
6. Seleccione el **Contenedor de la función** que contiene la función que se usa. Este es el nombre del contenedor de las funciones de Azure que creó anteriormente en estos pasos.
7. Seleccione la **función** que desee usar. Este es el nombre de la función de Azure que creó anteriormente.
8. Agregue el **contenido** que se transformar XML. Tenga en cuenta que puede usar los datos XML que aparece en la solicitud HTTP como el **contenido**. En este ejemplo, seleccione el cuerpo de la solicitud HTTP que activó la aplicación lógica.
9. Seleccione el nombre de la **asignación** que desea usar para realizar la transformación. El mapa ya debe estar en su cuenta de integración. En un paso anterior, ya que dio el acceso de la aplicación de lógica a su cuenta de integración que contiene su mapa.
10. Guarde su trabajo  
![](./media/app-service-logic-enterprise-integration-transforms/transform-5.png) 

En este momento, haya terminado de configurar su mapa. En una aplicación del mundo real, desea almacenar los datos transformados en una aplicación de LOB como SalesForce. Puede fácilmente como la acción que desea enviar el resultado de la transformación a Salesforce. 

Ahora puede probar su transformación realizando una solicitud al extremo HTTP.  

## <a name="features-and-use-cases"></a>Características y casos de uso

- La transformación creada en un mapa puede ser simple, como copiar un nombre y dirección de un documento a otro. O bien, puede crear transformaciones más complejas con las operaciones de mapa de cuadro.  
- Varias operaciones de mapa o funciones están disponibles, incluyendo cadenas, funciones de tiempo de fecha y así sucesivamente.  
- Puede hacer una copia directa de datos entre los esquemas. En el asignador incluido en el SDK, esto es tan sencillo como dibujar una línea que conecta los elementos del esquema de origen con sus homólogos en el esquema de destino.  
- Al crear un mapa, verá una representación gráfica de la asignación que mostrar todas las relaciones y los vínculos que cree.
- Use la característica de mapa de prueba para agregar un mensaje de XML de muestra. Con un solo clic, puede probar el mapa que ha creado y ver el resultado generado.  
- Cargar asignaciones existentes  
- Es compatible con el formato XML.


## <a name="learn-more"></a>Aprende más
- [Obtenga más información sobre el paquete de integración de Enterprise] (./app-service-logic-enterprise-integration-overview.md "Obtenga más información sobre el paquete de integración de Enterprise")  
- [Obtenga más información acerca de mapas] (./app-service-logic-enterprise-integration-maps.md "Obtenga más información acerca de la integración de enterprise de mapas")  
 