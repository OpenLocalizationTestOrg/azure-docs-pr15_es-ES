<properties
    pageTitle="Agregar el conector de usuarios de Office 365 en las aplicaciones de lógica | Microsoft Azure"
    description="Información general del conector de usuarios de Office 365 con parámetros de la API de REST"
    services=""    
    documentationCenter=""     
    authors="msftman"    
    manager="erikre"    
    editor="" 
    tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-office-365-users-connector"></a>Empezar a trabajar con el conector de usuarios de Office 365

Conectarse a Office 365 a los usuarios obtener perfiles, usuarios de búsqueda y mucho más. 

>[AZURE.NOTE] Esta versión de este artículo se aplica a la versión del esquema de 2015-08-01-vista previa de aplicaciones de lógica.

Los usuarios de Office 365, puede:

- Crear el flujo de negocio basándose en los datos que recibe de los usuarios de Office 365. 
- Acciones de uso que obtener informes directos, obtener perfil de usuario de un administrador y mucho más. Estas acciones obtienen una respuesta y, a continuación, hacer que el resultado esté disponible para otras acciones. Por ejemplo, obtener informes directos de la persona y, a continuación, realice esta información y actualizar una base de datos de SQL Azure. 

Para agregar una operación en las aplicaciones de lógica, consulte [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones

El conector de usuarios de Office 365 tiene las siguientes acciones disponibles. No hay ningún desencadenador.

| Desencadenadores | Acciones|
| --- | --- |
|Ninguno | <ul><li>Obtener el administrador</li><li>Obtener mi perfil</li><li>Obtener informes directos</li><li>Obtener el perfil de usuario</li><li>Búsqueda de usuarios</li></ul>|

Todos los conectores admiten datos en formatos XML y JSON. 


## <a name="create-a-connection-to-office-365-users"></a>Crear una conexión a los usuarios de Office 365

Cuando agrega este conector a sus aplicaciones de lógica, debe iniciar sesión en su cuenta de usuarios de Office 365 y permitir que las aplicaciones de lógica para conectarse a su cuenta.

>[AZURE.INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]

Después de crear la conexión, escriba las propiedades de los usuarios de Office 365, como el identificador de usuario. La **referencia de la API de REST** en este tema se describen las propiedades.

>[AZURE.TIP] Puede utilizar esta misma conexión de los usuarios de Office 365 en otras aplicaciones de lógica.


## <a name="office-365-users-rest-api-reference"></a>Referencia de la API de REST de los usuarios de Office 365
Se aplica a la versión: 1.0.

### <a name="get-my-profile"></a>Obtener mi perfil 
Recupera el perfil del usuario actual.  
```GET: /users/me``` 

No hay parámetros para esta llamada.

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|La operación es correcto|
|202|La operación es correcto|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|


### <a name="get-user-profile"></a>Obtener el perfil de usuario 
Recupera un perfil de usuario específico.  
```GET: /users/{userId}``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|identificador de usuario|cadena|Sí|ruta de acceso|Ninguno|Identificador nombre o correo electrónico principal del usuario|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|La operación es correcto|
|202|La operación es correcto|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|


### <a name="get-manager"></a>Obtener el administrador 
Recupera el perfil de usuario para el administrador del usuario especificado.  
```GET: /users/{userId}/manager``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|identificador de usuario|cadena|Sí|ruta de acceso|Ninguno|Identificador nombre o correo electrónico principal del usuario|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|La operación es correcto|
|202|La operación es correcto|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|



### <a name="get-direct-reports"></a>Obtener informes directos 
Obtener informes directos.  
```GET: /users/{userId}/directReports``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|identificador de usuario|cadena|Sí|ruta de acceso|Ninguno|Identificador nombre o correo electrónico principal del usuario|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|La operación es correcto|
|202|La operación es correcto|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|



### <a name="search-for-users"></a>Búsqueda de usuarios 
Recupera los resultados de perfiles de usuario de búsqueda.  
```GET: /users``` 

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|searchTerm|cadena|No|consulta|Ninguno|Cadena de búsqueda (se aplica a: nombre para mostrar, nombre, apellidos, correo, nombre principal de usuario y el alias de correo)|

#### <a name="response"></a>Respuesta

|Nombre|Descripción|
|---|---|
|200|La operación es correcto|
|202|La operación es correcto|
|400|BadRequest|
|401|No autorizado|
|403|Prohibido|
|500|Error interno del servidor|
|predeterminado|Error en la operación.|



## <a name="object-definitions"></a>Definiciones de objeto

#### <a name="user-user-model-class"></a>Usuario: Clase de modelo de usuario

|Nombre de propiedad | Tipo de datos |Obligatorio
|---|---|---|
|DisplayName|cadena|No|
|GivenName|cadena|No|
|Apellido|cadena|No|
|Correo|cadena|No|
|MailNickname|cadena|No|
|TelephoneNumber|cadena|No|
|AccountEnabled|valor booleano|No|
|Id.|cadena|Sí
|UserPrincipalName|cadena|No|
|Departamento|cadena|No|
|Puesto temporal|cadena|No|
|TeléfonoMóvil|cadena|No|


## <a name="next-steps"></a>Pasos siguientes

[Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

Vuelva a la [lista de las API](apis-list.md).

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/connectors-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/connectors-create-api-office365-users/contoso-aad-app-configure.PNG
