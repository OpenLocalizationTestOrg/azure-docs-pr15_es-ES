<properties 
    pageTitle="Conectores para empresas y aplicaciones de la API en las aplicaciones de lógica | Microsoft Azure" 
    description="Obtenga información sobre cómo crear y configurar conectores EDI, EDIFACT, AS2 y TPM; arquitectura de microservices" 
    services="logic-apps" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="anneta" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="mandia"/> 

# <a name="business-to-business-connectors-and-api-apps"></a>Conectores para empresas y aplicaciones de la API

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Lógica de aplicaciones incluye muchas aplicaciones de la API de BizTalk que son fundamentales para entornos de integración. Estas aplicaciones API se basan en conceptos y herramientas utilizadas en BizTalk Server, pero ahora está disponibles como parte de la lógica de aplicaciones. 

Una categoría de estas aplicaciones de la API son las aplicaciones de la API de empresa a empresa (B2B). Con estas aplicaciones de la API de B2B, fácilmente puede agregar a socios, crear contratos y hacer todo lo que debería local mediante EDI, AS2 y EDIFACT.  

Estas aplicaciones de la API de B2B ofrece capacidades de "Desencadenador" y "Acción". Un desencadenador inicia una nueva instancia en función de un evento específico, como la llegada de un X12 mensaje de un socio. Una acción es el resultado, como después de recibir un X12 del mensaje, a continuación, envíe el mensaje usando AS2.


## <a name="what-is-a-business-to-business-connector-or-api-apps"></a>¿Qué es un conector para empresas o aplicaciones de la API
La función de negocio a negocio (B2B) incluye aplicaciones API existente y predefinida que permiten diferentes empresas, divisiones, aplicaciones, y así sucesivamente para comunicarse mediante AS2, EDI y EDIFACT. 

Las aplicaciones de la API de B2B incluyen: 

Conector o aplicaciones de la API | Descripción
--- | ---
Administración de socios comerciales de BizTalk | Un App API que crea con asociados y contratos de relaciones de negocio a negocio (B2B). Las relaciones de utilizan el AS2, EDIFACT y X12 protocolo.<br/><br/>La aplicación de la API de TPM requisito es la base del conector AS2 y la X12 o aplicaciones de la API de EDIFACT. 
Conector AS2 | Un conector que recibe y envía mensajes mediante el transporte AS2. El conector transportes datos de forma segura y confiable por Internet.
BizTalk EDIFACT | Un App API que recibe y envía mensajes con EDIFACT. EDIFACT comúnmente también se conoce como UN/EDIFACT (intercambio de administración de datos de las Naciones Unidas o electrónico, comercio y transporte) y se usa ampliamente en industrias.
BizTalk X12 | Un App API que recibe y envía mensajes mediante la X12 protocolo. X12 es también conocidos como ASC X 12 (acreditada estándares Comité X12) y se usa ampliamente en industrias. 


Estas aplicaciones de la API que puede completar diferentes EDI mensajería tareas. Por ejemplo, usando el conector AS2, puede segura recibir y enviar diferentes tipos de mensajes (EDI, XML, archivo plano, etc.) a un cliente, una división dentro de su empresa como recursos humanos, o cualquier persona que usa AS2. 

Puede crear tantas aplicaciones de la API como desee y crearlos fácilmente. También puede volver a usar una única API App dentro de varios escenarios o flujos de trabajo.

Puede hacer esto sin escribir código. Vamos a empezar. 


## <a name="requirements-to-get-started"></a>Requisitos para empezar
Al crear aplicaciones de la API de B2B, hay algunos recursos necesarios. Estos elementos se deben creados por el usuario antes de que se pueden utilizar en otras aplicaciones de la API. Estos requisitos incluyen: 

Requisito | Descripción
--- | ---
Base de datos SQL Azure | Almacena los elementos de B2B incluidos socios, esquemas, certificados y acuerdos. Cada una de las aplicaciones de la API de B2B requiere su propia base de datos de SQL Azure. <br/><br/>**Nota** Copie la cadena de conexión en esta base de datos.<br/><br/>[Crear una base de datos SQL Azure](../sql-database/sql-database-get-started.md)
Contenedor de almacenamiento de blobs de Windows Azure | Almacena propiedades del mensaje cuando AS2 archivado está habilitado. Si no necesita AS2 archivado de mensajes, no es necesario un contenedor de almacenamiento. <br/><br/>**Nota** Si va a habilitar archivado, copie la cadena de conexión para el almacenamiento de blobs.<br/><br/>[Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md)
Namespace de Bus de servicio y sus valores de clave | Almacena X12 y EDIFACT lotes de datos. Si no necesita procesamiento por lotes, un espacio de nombres de Bus de servicio no es necesario.<br/><br/>**Nota** Si va a habilitar el procesamiento por lotes, copie estos valores.<br/><br/>[Crear un Namespace de Bus de servicio](http://msdn.microsoft.com/library/azure/hh690931.aspx)
Instancia TPM | Se requiere una instancia de administración de socios comerciales (TPM) de BizTalk para crear un conector AS2 y X12 o EDIFACT API de aplicación. Cuando se crea la aplicación de la API de TPM, se crea la instancia de TPM. <br/><br/>**Nota** Conocer el nombre de la aplicación de la API de TPM. 


## <a name="create-the-api-apps"></a>Crear la API de aplicaciones
Aplicaciones de la API de B2B pueden crearse con el portal de Azure o mediante las API de REST. 


### <a name="create-the-api-apps-using-rest-apis"></a>Crear las aplicaciones de la API con la API de REST
[Consulte la documentación sobre cómo usar las API de REST.](http://go.microsoft.com/fwlink/p/?LinkId=529766)


### <a name="create-the-b2b-api-apps-in-the-azure-portal"></a>Crear las aplicaciones de la API de B2B en el Portal de Azure
En el portal de Azure, puede crear aplicaciones de la API de B2B al crear lógica de aplicaciones, aplicaciones Web o aplicaciones móviles. O bien, puede crearla con su propio módulo. Estos dos procedimientos están fáciles para depende de sus necesidades o preferencias. Algunos usuarios prefieren crear primero todas las aplicaciones de la API de B2B con sus propiedades específicas. A continuación, crear la lógica de aplicaciones/aplicaciones/Mobile aplicaciones Web y agregar las aplicaciones de la API de B2B que creó.  

Los pasos siguientes crean las aplicaciones de la API de B2B usando el módulo de aplicaciones de la API.


#### <a name="create-the-biztalk-trading-partner-management-tpm-api-apps"></a>Crear el BizTalk cotización aplicaciones API de Partner administración (TPM)

> [AZURE.NOTE] Se requiere una instancia de administración de socios comerciales (TPM) de BizTalk para crear un conector AS2 y X12 o EDIFACT API de aplicación. Cuando se crea la aplicación de la API de TPM, se crea la instancia de TPM.

Los siguientes pasos al crear la instancia TPM:

1. En el portal de Azure Startboard (la página de inicio), seleccione el **catálogo de soluciones**. **Aplicaciones de la API** enumera todas las aplicaciones de API existente y conectores. También puede **Buscar** para las aplicaciones de API B2B específicos.
2. Seleccione **administración de socios comerciales de BizTalk**. En el nuevo módulo, seleccione **crear**. 
3. Escriba las propiedades: 

    (Propiedad) | Descripción
--- | ---
Nombre | Escriba un nombre para la instancia TPM. Por ejemplo, puede llamar *AccountsPayableTPM*.
Configuración del paquete | Escriba la **Cadena de conexión de base de datos** ADO.NET a la base de datos de SQL Azure que creó. <br/><br/>Cuando copie la cadena de conexión, la contraseña no se agrega a la cadena de conexión. Asegúrese de escribir la contraseña en la cadena de conexión.
Plan de servicios de aplicación | Muestra el plan de pago. Puede cambiarla si necesita más o menos recursos.
Nivel de precios | Propiedad de sólo lectura que muestra la categoría de precio dentro de su suscripción de Azure. 
Grupo de recursos | Crear una nueva o usar un grupo existente. Todas las aplicaciones de la API y conectores para la lógica de aplicaciones, aplicaciones Web y aplicaciones móviles deben estar en el mismo grupo de recursos. <br/><br/>[Uso de los grupos de recursos](../azure-resource-manager/resource-group-overview.md) se explica esta propiedad. 
Suscripción | Propiedad de solo lectura que enumera su suscripción actual.
Ubicación | La ubicación geográfica que hospeda su servicio de Azure. 
Agregar a Startboard | Seleccione esta opción para agregar la aplicación de la API de B2B a su estribor (la página de inicio).

4. Seleccione **crear**. 

Después de crear la aplicación de la API de TPM (TPM instancia), a continuación, puede crear el conector AS2 o la X12 o aplicaciones de la API de EDIFACT. 


#### <a name="create-the-as2-connector"></a>Crear el conector AS2

1. En el portal de Azure Startboard (la página de inicio), seleccione el **catálogo de soluciones**. **Aplicaciones de la API** enumera todas las aplicaciones de API existente y conectores. También puede **Buscar** para las aplicaciones de API B2B específicos.
2. Seleccione el **conector AS2**. En el nuevo módulo, seleccione **crear**. 
3. Escriba las propiedades: 

    (Propiedad) | Descripción
--- | ---
Nombre | Escriba un nombre para el conector AS2. Por ejemplo, puede llamar *AS2Connector*.
Configuración del paquete | Escriba los valores específicos para ese App API, como el nombre de instancia de TPM. <br/><br/>En este tema para las propiedades específicas, vea [Configuración del paquete AS2 agregar](#AddAS2Conn) . 
Plan de servicios de aplicación | Muestra el plan de pago. Puede cambiarla si necesita más o menos recursos.
Nivel de precios | Propiedad de sólo lectura que muestra la categoría de precio dentro de su suscripción de Azure. 
Grupo de recursos | Crear una nueva o usar un grupo existente. [Uso de los grupos de recursos](../azure-resource-manager/resource-group-overview.md) se explica esta propiedad. 
Suscripción | Propiedad de solo lectura que enumera su suscripción actual.
Ubicación | La ubicación geográfica que hospeda su servicio de Azure. 
Agregar a Startboard | Seleccione esta opción para agregar la aplicación de la API de B2B a su estribor (la página de inicio).

    **<a name="AddAS2Conn"></a>Conector AS2 configuración del paquete**

    (Propiedad) | Descripción
--- | --- 
Cadena de conexión de base de datos | Escriba la cadena de conexión de ADO.NET a la base de datos de SQL Azure que creó. Cuando copie la cadena de conexión, la contraseña no se agrega a la cadena de conexión. Asegúrese de escribir la contraseña en la cadena de conexión antes de pegar.
Habilitar el archivado en los mensajes entrantes | Opcional. Habilitar esta propiedad almacenar las propiedades del mensaje de un mensaje entrante de AS2 recibido de un socio. 
Cadena de conexión de almacenamiento de blobs de Windows Azure  | Escriba la cadena de conexión para el contenedor de almacenamiento de blobs de Windows Azure que ha creado. Cuando se habilita el archivado, los mensajes codificados y descodificados se almacenan en este contenedor de almacenamiento.
Nombre de la instancia TPM | Escriba el nombre de la **Administración de socios comerciales de BizTalk** App API que creó anteriormente. Cuando se crea el conector AS2, este conector ejecuta los contratos de AS2 dentro de la instancia específica del TPM.

4. Seleccione **crear**. 


#### <a name="create-the-x12-or-edifact-api-apps"></a>Crear las aplicaciones de la API X12 o EDIFACT

1. En el portal de Azure Startboard (la página de inicio), seleccione el **catálogo de soluciones**. **Aplicaciones de la API** enumera todas las aplicaciones de API existente y conectores. También puede **Buscar** para las aplicaciones de API B2B específicos.
2. Seleccione **BizTalk X 12** o **EDIFACT BizTalk**. En el nuevo módulo, seleccione **crear**. 
3. Escriba las propiedades: 

    (Propiedad) | Descripción
--- | ---
Nombre | Escriba un nombre para la aplicación de la API de B2B. Por ejemplo, puede llamar *EDI850APIApp*.
Configuración del paquete | Escriba los valores específicos para ese App API, como el nombre de instancia de TPM. <br/><br/>En este tema para las propiedades específicas, vea [X12 o EDIFACT configuración del paquete](#AddX12) . 
Plan de servicios de aplicación | Muestra el plan de pago. Puede cambiarla si necesita más o menos recursos.
Nivel de precios | Propiedad de sólo lectura que muestra la categoría de precio dentro de su suscripción de Azure. 
Grupo de recursos | Crear una nueva o usar un grupo existente. [Uso de los grupos de recursos](../azure-resource-manager/resource-group-overview.md) se explica esta propiedad. 
Suscripción | Propiedad de solo lectura que enumera su suscripción actual.
Ubicación | La ubicación geográfica que hospeda su servicio de Azure. 
Agregar a Startboard | Seleccione esta opción para agregar la aplicación de la API de B2B a su estribor (la página de inicio).

    **<a name="AddX12"></a>Configuración del paquete de aplicaciones API X12 y EDIFACT**  

    (Propiedad) | Descripción
--- | --- 
Cadena de conexión de base de datos | Escriba la cadena de conexión de ADO.NET a la base de datos de SQL Azure que creó. Cuando copie la cadena de conexión, la contraseña no se agrega a la cadena de conexión. Asegúrese de escribir la contraseña en la cadena de conexión antes de pegar.
Namespace de Bus de servicio | Escriba el espacio de nombres de Bus de servicio que ha creado. Se requiere solo cuando el procesamiento por lotes está habilitado. 
Nombre de la clave de acceso compartido de servicio Bus Namespace | Escriba el espacio de nombres de Bus de servicio que creó de tecla de acceso. Se requiere solo cuando el procesamiento por lotes está habilitado. 
Valor de clave de acceso compartido de servicio Bus Namespace | Escriba el valor de tecla de acceso que creó de espacio de nombres de Bus de servicio. Se requiere solo cuando el procesamiento por lotes está habilitado. 
Nombre de la instancia TPM | Escriba el nombre de la **Administración de socios comerciales de BizTalk** App API que creó anteriormente. Cuando se crea la X12 o aplicaciones de la API de EDIFACT, este App API ejecuta los contratos de X12/EDFIACT dentro de la instancia específica del TPM.

4. Seleccione **crear**. 


## <a name="add-your-partners-agreements-certificates-and-schemas"></a>Agregar socios, agreements, certificados y esquemas 
En el portal de Azure, abra la aplicación de la API de TPM. En la sección **componentes** , agregue los asociados, Agreements, certificados y los esquemas. 

También puede agregar agreements a los conectores de AS2 X12 API de aplicaciones y aplicaciones de la API de EDIFACT. 


## <a name="monitor-your-api-apps"></a>Supervisar las aplicaciones de la API
En el portal de Azure, abra la aplicación de la API de TPM. En la sección **acciones** , puede ver las operaciones de administración diferentes. Por ejemplo, puede:

- Eventos de Error y vista informativo
- Ver el recuento de subprocesos y el uso de memoria del proceso de trabajo (w3wp)
- Ver los registros del servidor web y de aplicaciones

Más información en [Monitor de sus aplicaciones de lógica](app-service-logic-monitor-your-logic-apps.md).


## <a name="add-the-api-apps-to-your-application"></a>Agregar las aplicaciones de la API a la aplicación 
Aplicación de servicio de Microsoft Azure expone diferentes tipos de aplicaciones que pueden usar estas aplicaciones de la API de B2B. Puede crear un nuevo o agregar las aplicaciones de API B2B existente lógica de aplicaciones, aplicaciones móviles o aplicaciones de Web. 

Dentro de la aplicación, simplemente seleccionar automáticamente las aplicaciones de la API de B2B de la Galería agrega a la aplicación.  

> [AZURE.IMPORTANT] Para agregar conectores y aplicaciones de la API que creó anteriormente, cree la lógica de aplicaciones, aplicaciones móviles o aplicaciones Web en el mismo grupo de recursos. 

Los siguientes pasos agregan las aplicaciones de la API de B2B lógica de aplicaciones, aplicaciones móviles o aplicaciones Web: 

1. En el portal de Azure Startboard (página principal), vaya al **catálogo de soluciones**y busque su lógica, Mobile o aplicaciones Web. 

    Si va a crear una nueva aplicación, busque la lógica de aplicaciones, aplicaciones móviles o aplicaciones Web. Seleccione la aplicación y en el nuevo módulo, seleccione **crear**. [Crear una aplicación de la lógica](app-service-logic-create-a-logic-app.md) se enumeran los pasos. 

2. Abra la aplicación y seleccione **desencadenadores y acciones**. 

3. Desde la **Galería**, seleccione la aplicación de API B2B, se agregará automáticamente a la aplicación. También puede crear una nueva aplicación API de B2B.

    > [AZURE.IMPORTANT] El conector AS2 y X12, aplicaciones de la API de EDIFACT requieren una instancia de TPM. Por lo que si está creando nuevas aplicaciones API de B2B, la aplicación de la API de TPM en primer lugar y a continuación, cree el conector AS2, X12 API App o EDIFACT API de aplicación. 

4. Seleccione **Aceptar** para guardar los cambios. 

>[AZURE.NOTE] Empezar a trabajar con aplicaciones de Azure lógica antes de suscribirse para una cuenta de Azure, [Intente lógica de aplicaciones](https://tryappservice.azure.com/?appservice=logic). Puede crear una aplicación de la lógica de corta duración starter inmediatamente. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="more-b2b-resources"></a>Más recursos de B2B

[Crear un proceso de B2B](app-service-logic-create-a-b2b-process.md)<br/>
[Crear un acuerdo con el socio comercial](app-service-logic-create-a-trading-partner-agreement.md)<br/>
[¿Qué son los conectores y aplicaciones de la API de BizTalk](app-service-logic-what-are-biztalk-api-apps.md)


## <a name="read-about-logic-apps-and-web-apps"></a>Obtenga información sobre las aplicaciones de lógica y aplicaciones Web
[¿Qué aplicaciones de lógica?](app-service-logic-what-are-logic-apps.md)<br/>
[Sitios Web y aplicaciones Web en el servicio de aplicación de Azure](../app-service-web/app-service-web-overview.md)


## <a name="more-connectors"></a>Conectores más

[Lista de aplicaciones de API y conectores](app-service-logic-connectors-list.md)<br/><br/>
[¿Qué son los conectores y aplicaciones de la API de BizTalk](app-service-logic-what-are-biztalk-api-apps.md) 
