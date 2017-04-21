<properties
    pageTitle="Información general de conectores de aplicaciones de lógica | Microsoft Azure"
    description="Información general de conectores que se pueden usar en una aplicación de lógica"
    services=""
    documentationCenter="" 
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# <a name="using-connectors-in-a-logic-app"></a>Uso de conectores en una aplicación de lógica

Conectores proporcionan acceso rápido a eventos, datos y acciones en los servicios, protocolos y plataformas.  La lista completa de conectores que admite la lógica de aplicaciones puede [encontrarse aquí](apis-list.md).  Conectores pueden usarse como un desencadenador o una acción en una aplicación de lógica y puede requerir una *conexión* configurada para usar (por ejemplo: autorizar una cuenta de Twitter para tener acceso o publicar en su nombre).

## <a name="basics"></a>Conceptos básicos

Conectores son servicios que puede tener acceso a como parte de una aplicación de lógica integrar con otros servicios como Dynamics, Azure, Salesforce, [y más](apis-list.md).  Se implementa y administradas por Microsoft, por lo que se pueden generar los flujos de trabajo de integración con escala, el rendimiento y la seguridad tenerse en cuenta.  Puede agregar un conector a una aplicación de lógica por buscar y seleccionar una acción de conector o desencadenador en **Microsoft mostrar API administradas**.

![Menú Acción para seleccionar desencadenador][1]

Cada acción de conector o desencadenador tendrá su conjunto de propiedades para configurar.  Puede haga clic en los botones de información para obtener más información acerca de la acción o hacer referencia a su documentación [para obtener más información](apis-list.md).

Si desea integrar con un servicio o API que no está todavía un conector, también puede ampliar aplicaciones de lógica a través de un [conector personalizado](../app-service-logic/app-service-logic-create-api-app.md) o simplemente llamar directamente al servicio a través de un protocolo como HTTP.

## <a name="triggers"></a>Desencadenadores

Algunos conectores tienen un desencadenador, lo que indica un evento de conector se aplique una aplicación lógica y pase todos los datos como parte del desencadenador.  Un desencadenador siempre es el primer paso en una aplicación de lógica.  Desencadenadores populares incluyen operaciones como:
 
 * Periodicidad - ejecutar cada hora
 * Cuando se recibe una solicitud HTTP
 * Cuando se agrega un elemento a una cola
 * Cuando se recibe un correo electrónico
 
Algunos desencadenadores aplicará la instantánea que pasa un evento a través de una notificación a la aplicación de lógica y otros usuarios tendrán un intervalo de periodicidad configurado en la frecuencia con la aplicación de lógica comprueba el servicio de un evento (hasta cada 15 segundos).  

Una vez que se recibe un evento, se aplicará la aplicación lógica ejecutar y las acciones del flujo de trabajo se inicia.  También podrá obtener acceso a los datos desde el desencadenador del flujo de trabajo (por ejemplo el desencadenador 'en un tweet nuevo' pasará la tweet en la ejecución).

## <a name="actions"></a>Acciones

La mayoría de conectores tiene una o varias acciones que se pueden ejecutar como parte del flujo de trabajo.  Las acciones son los pasos que se producen después de que se ha desencadenado la ejecución de un desencadenador.  Para agregar una acción, haga clic en el botón **Nuevo paso** y buscar el conector que desea usar.  Una vez seleccionado (y después configurar las [conexiones](#connections) que pueden ser necesario realizar), verá la tarjeta de la acción que se pueden configurar.  Puede seleccionar datos de pasos anteriores haciendo clic en cualquiera de los símbolos de salidas o escribir en cualquier otra configuración según sea necesario.

![Configurar una acción de conector][2]

## <a name="connections"></a>Conexiones

La mayoría de los conectores requieren que configurar una *conexión* para poder usar el conector.  Una *conexión* es cualquier configuración de conexión o de inicio de sesión necesaria para tener acceso al conector.  Para los conectores que utilizan OAuth, crear una conexión significa que iniciar sesión en el servicio (como Office 365, Salesforce o GitHub) donde puede cifra y se almacenan de forma segura en una tienda secreta Azure el token de acceso.  Otros conectores (como FTP y SQL) requieren una conexión que contiene la configuración como dirección del servidor, el nombre de usuario y contraseña.  Estos detalles de configuración de conexión también se cifra y almacena de forma segura.  Conexiones podrá tener acceso al servicio mientras permite que el servicio.  Para las conexiones de OAuth de Azure Active Directory (por ejemplo, Office 365 y Dynamics) podemos continuar actualizar el token de acceso de forma indefinida.  Otros servicios pueden colocar límites en cuánto podemos utilizar un símbolo sin que se actualiza.  En general determinadas acciones como cambiar una contraseña, invalidarán todos los tokens de acceso.  

Conexiones se pueden ver y administrar en Azure haciendo clic en **Examinar** y seleccionando la **API de conexiones**.  Desde el recurso de conexiones de API puede ver, editar, actualizar o volver a autorizar las conexiones que haya creado.

## <a name="next-steps"></a>Pasos siguientes

- [Crear su primera aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)
- [Más comunes usos y ejemplos de aplicaciones de lógica](../app-service-logic/app-service-logic-examples-and-scenarios.md)
- [Introducción a acciones y desencadenadores de integración de enterprise](../app-service-logic/app-service-logic-enterprise-integration-overview.md)

<!--Image References -->
[1]: ./media/connectors-overview/addAction.png
[2]: ./media/connectors-overview/configureAction.png