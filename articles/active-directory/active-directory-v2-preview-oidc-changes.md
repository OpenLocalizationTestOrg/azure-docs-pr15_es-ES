<properties
    pageTitle="Cambios en el extremo de la versión 2.0 de Azure AD | Microsoft Azure"
    description="Descripción de los cambios realizados a los protocolos de versión preliminar pública de aplicación modelo versión 2.0."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="important-updates-to-the-v20-authentication-protocols"></a>Actualizaciones importantes para los protocolos de autenticación de la versión 2.0
¡Desarrolladores de atención! Sobre las próximas dos semanas, se pueden realizar actualizaciones unas en nuestros versión 2.0 los protocolos de autenticación que podrán significar principales cambios para las aplicaciones que ha escrito durante el periodo de vista previa.  

## <a name="who-does-this-affect"></a>¿Quién afecta esto?
Las aplicaciones que se han escrito para usar la versión 2.0 convergen extremo de autenticación

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
```

Puede encontrar más información en el extremo de la versión 2.0 [aquí](active-directory-appmodel-v2-overview.md).

Si ha creado una aplicación utilizando el extremo de la versión 2.0 mediante la codificación directamente en el protocolo de la versión 2.0, con cualquiera de nuestros middlewares web OpenID conectar o OAuth o usar otras bibliotecas de fiesta 3ª para realizar la autenticación, debe estar preparado para probar sus proyectos y realice cambios si es necesario.

## <a name="who-doesnt-this-affect"></a>¿Quién no afecta esto?
Las aplicaciones que se han escrito en el extremo de autenticación de producción Azure AD

```
https://login.microsoftonline.com/common/oauth2/authorize
```

Este protocolo se establece en piedra y no estar experimentando los cambios.

Además, si usa la aplicación **solo** nuestra biblioteca ADAL para realizar la autenticación, no debe cambiar nada.  ADAL ha protegido la aplicación de los cambios.  

## <a name="what-are-the-changes"></a>¿Cuáles son los cambios?
### <a name="removing-the-x5t-value-from-jwt-headers"></a>Quitando el valor x5t encabezados JWT
El punto final de la versión 2.0 usa tokens JWT ampliamente, que contiene una sección de parámetros de encabezado con metadatos pertinentes sobre el token.  Si descodificar el encabezado de uno de nuestros JWTs actuales, encontrará algo parecido:

```
{ 
    "type": "JWT",
    "alg": "RS256",
    "x5t": "MnC_VZcATfM5pOYiJHMba9goEKY",
    "kid": "MnC_VZcATfM5pOYiJHMba9goEKY"
}
```

En Propiedades de las "x5t" y "niño" identifican la clave pública que debe usarse para validar firma del token, como recuperados desde el extremo de metadatos OpenID conectarse.

El cambio que estamos haciendo aquí es quitar la propiedad "x5t".  Puede seguir usando los mismos mecanismos para validar tokens, pero debe confiar en la propiedad "niño" para recuperar la clave pública correcta, según lo especificado en el protocolo OpenID conectarse. 

> [AZURE.IMPORTANT] **Su trabajo: asegúrese de que su aplicación no depende de la existencia del valor x5t.**

### <a name="removing-profileinfo"></a>Quitar profile_info
Anteriormente, el extremo de la versión 2.0 se ha devolver un base 64 codificado JSON objeto en respuestas tokens denominadas `profile_info`.  Cuando se solicita un token de acceso desde el extremo de la versión 2.0 mediante el envío de una solicitud de:

```
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

El siguiente objeto JSON se verá la respuesta:
```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

La `profile_info` información del valor contenido acerca del usuario que ha iniciado sesión en la aplicación: su nombre para mostrar, nombre, apellidos, dirección de correo electrónico, identificador y así sucesivamente.  En primer lugar, la `profile_info` se ha utilizado para el almacenamiento en caché token y mostrar efectos.

Estamos quitando el `profile_info` valor, pero no se preocupe, aún proporcionamos esta información para los desarrolladores en un lugar diferente.  En lugar de `profile_info`, el extremo de la versión 2.0 devolverá ahora una `id_token` en cada respuesta de token:

```
{ 
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https://outlook.office.com/mail.read",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

Puede descodificar y analizar el id_token para recuperar la misma información que ha recibido de profile_info.  El id_token es un JSON Web Token (JWT), con contenido según lo especificado por OpenID conectarse.  El código para hacerlo así que debe ser muy similar: necesita simplemente extraer el segmento central (el cuerpo) de la id_token y base 64 descodificar para tener acceso al objeto JSON dentro.

Sobre las próximas dos semanas, debe codificar la aplicación para recuperar la información de usuario desde el `id_token` o `profile_info`; Si está presente.  Cuando se realiza el cambio de este modo, la aplicación sin problemas puede controlar la transición de `profile_info` a `id_token` sin interrupciones.

> [AZURE.IMPORTANT] **Su trabajo: asegúrese de que su aplicación no depende de la existencia de la `profile_info` valor.**

### <a name="removing-idtokenexpiresin"></a>Quitar id_token_expires_in
Similar a `profile_info`, también estamos quitando el `id_token_expires_in` parámetro de las respuestas.  Anteriormente, el punto final de la versión 2.0 devolverá un valor para `id_token_expires_in` junto con cada respuesta id_token, por ejemplo, en una respuesta de autorizar:

```
https://myapp.com?id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...&id_token_expires_in=3599...
```

O en una respuesta de token:

```
{ 
    "token_type": "Bearer",
    "id_token_expires_in": 3599,
    "scope": "openid",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
    "refresh_token": "OAAABAAAAiL9Kn2Z27UubvWFPbm0gL...",
    "profile_info": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsI...",
}
```

La `id_token_expires_in` valor indica el número de segundos que la id_token ¿siguen siendo válidos para.  Ahora, estamos quitando el `id_token_expires_in` valor completamente.  En su lugar, puede usar el estándar OpenID conectar `nbf` y `exp` notificaciones para examinar la validez de una id_token.  Vea la [referencia de token de versión 2.0](active-directory-v2-tokens.md) para obtener más información sobre estas notificaciones.

> [AZURE.IMPORTANT] **Su trabajo: asegúrese de que su aplicación no depende de la existencia de la `id_token_expires_in` valor.**


### <a name="changing-the-claims-returned-by-scopeopenid"></a>Cambiar las notificaciones devueltas por scope = openid
Este cambio será más importante, de hecho, afectará a casi todas las aplicaciones que usa el punto final de la versión 2.0.  Muchas aplicaciones envían solicitudes al extremo versión 2.0 mediante la `openid` ámbito, como:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid offline_access https://outlook.office.com/mail.read
```

Hoy, cuando el usuario concede consentimiento para la `openid` ámbito, la aplicación recibe una gran cantidad de información sobre el usuario en el id_token resultante.  Estas notificaciones pueden incluir su nombre, preferido de nombre de usuario, dirección de correo electrónico, el identificador de objeto y más.

En la actualización, que estamos cambiando la información que el `openid` ámbito permite el acceso de la aplicación, a comform mejor con la especificación OpenID conectarse.  La `openid` ámbito solo se permiten la aplicación iniciar sesión de usuario en y recibir un identificador específico de la aplicación para el usuario en el `sub` de notificación de la id_token.  Las notificaciones en un id_token con solo la `openid` ámbito concedido será desprovistos de cualquier información de identificación personal.  Notificaciones de id_token de ejemplo son:

```
{ 
    "aud": "580e250c-8f26-49d0-bee8-1c078add1609",
    "iss": "https://login.microsoftonline.com/b9410318-09af-49c2-b0c3-653adc1f376e/v2.0 ",
    "iat": 1449520283,
    "nbf": 1449520283,
    "exp": 1449524183,
    "nonce": "12345",
    "sub": "MF4f-ggWMEji12KynJUNQZphaUTvLcQug5jdF2nl01Q",
    "tid": "b9410318-09af-49c2-b0c3-653adc1f376e",
    "ver": "2.0",
}
```

Si desea obtener información de identificación personal (PII) sobre el usuario en la aplicación, la aplicación necesitará solicitar permisos adicionales del usuario.  Presentamos compatibilidad con dos nuevos ámbitos de la especificación OpenID conectarse: la `email` y `profile` ámbitos – que permita hacerlo.

- La `email` ámbito es muy sencillo: permite el acceso de la aplicación a la dirección de correo electrónico principal del usuario a través de la `email` reclamar en la id_token.  Tenga en cuenta que el `email` reclamación no esté siempre presente en id_tokens: solo se incluirá en si está disponible en el perfil de usuario.
- La `profile` Id. de objeto de ámbito permite el acceso de la aplicación a todos los demás información básica sobre el usuario: su nombre, el nombre de usuario preferido y así sucesivamente.

Esto le permite a su aplicación de forma mínima revelación de código: solicitar al usuario solo el conjunto de información que requiere la aplicación para hacer su trabajo.  Si desea continuar recibiendo el conjunto completo de la información de usuario que la aplicación está recibiendo, debe incluir todos los ámbitos de tres de las solicitudes de autorización:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=...
&redirect_uri=...
&response_mode=form_post
&response_type=id_token
&scope=openid profile email offline_access https://outlook.office.com/mail.read
```

La aplicación puede comenzar a enviar el `email` y `profile` ámbitos inmediatamente y el extremo de la versión 2.0 aceptará estos dos ámbitos y empiece a solicitar permisos de los usuarios según sea necesario.  Sin embargo, el cambio de la interpretación de la `openid` ámbito no tendrán efecto para unas semanas.

> [AZURE.IMPORTANT] **Su trabajo: agregar el `profile` y `email` ámbitos si su aplicación requiere información sobre el usuario.**  Tenga en cuenta que ADAL incluirá ambos permisos en solicitudes de forma predeterminada. 

### <a name="removing-the-issuer-trailing-slash"></a>Quitar al emisor barra oblicua final.
Anteriormente, el valor del emisor que aparece en tokens desde el extremo de la versión 2.0 tomó el formulario

```
https://login.microsoftonline.com/{some-guid}/v2.0/
```

Donde el guid fue el tenantId del inquilino Azure AD que emite el token.  Con estos cambios, se convierte en el valor emisor

```
https://login.microsoftonline.com/{some-guid}/v2.0 
```

en ambos tokens y en el documento de descubrimiento OpenID conectarse.

> [AZURE.IMPORTANT] **Su trabajo: asegúrese de que su aplicación acepta el valor de emisor con y sin una barra diagonal durante la validación del emisor.**

## <a name="why-change"></a>¿Por qué cambiar?
El motivo principal para introducir estos cambios es que sea compatible con la especificación estándar OpenID conectarse.  Al ser OpenID conectar compatible, esperamos minimizar las diferencias entre la integración con servicios de identidad de Microsoft y con otros servicios de identidad de la industria.  Desea permitir a los desarrolladores utilizar sus bibliotecas de autenticación favorito Abrir origen sin tener que alterar las bibliotecas para ajustar las diferencias de Microsoft.

## <a name="what-can-you-do"></a>¿Qué puede hacer?
Actualmente, puede comenzar a realizar todos los cambios que se indica más arriba.  Debe inmediatamente:

1.  **Quite las dependencias en los `x5t` parámetro de encabezado.**
2.  **Controlar la transición de `profile_info` a `id_token` en respuestas de tokens.**
3.  **Quite las dependencias en los `id_token_expires_in` parámetro de respuesta.**
3.  **Agregar la `profile` y `email` ámbitos a su aplicación si la aplicación necesita información básica del usuario.**
4.  **Acepte los valores del emisor de tokens con y sin una barra diagonal.**

La [documentación de protocolo versión 2.0](active-directory-v2-protocols.md) ya se ha actualizado para reflejar estos cambios, por lo que puede usar como referencia para ayudar a actualizar el código.

Si tiene alguna pregunta sobre el ámbito de los cambios, no dude en contactar con nosotros en Twitter en @AzureAD.

## <a name="how-often-will-protocol-changes-occur"></a>¿Con qué frecuencia se producen cambios de protocolo?
No hemos prever cualquier romper cambia a los protocolos de autenticación.  Deliberadamente nos estamos agrupación estos cambios en una versión de modo que no tiene que ir a través de este tipo de proceso de actualización cada vez pronto.  Por supuesto, seguiremos agregar características al servicio de autenticación de versión 2.0 convergentes puede aprovechar, pero esos cambios debería aditivos y no salto de código existente.

Por último, vamos a decirle gracias por probar cosas durante el período de vista previa.  La información y experiencias de nuestros primeros usuarios han sumamente útil hasta ahora y esperamos que seguirá compartir sus ideas y opiniones.

¡Feliz codificación!

La división de identidad de Microsoft
