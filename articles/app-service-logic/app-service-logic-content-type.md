<properties
   pageTitle="Aplicaciones de lógica contenidas escriba control | Microsoft Azure"
   description="Comprender cómo trata tipos de contenido de diseño y el tiempo de ejecución de aplicaciones de lógica"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="logic-apps-content-type-handling"></a>Control de tipo de contenido de aplicaciones de lógica

Hay muchos tipos diferentes de contenido que pueden pasar a través de una aplicación de lógica: incluidos JSON, XML, archivos sin formato y datos binarios.  Mientras se admiten todos los tipos de contenido, algunos nativa se entienden por el motor de lógica de aplicaciones y otros usuarios pueden requerir conversión o conversiones según sea necesario.  El siguiente artículo describe cómo administra el motor de los diferentes tipos de contenido y cómo se pueden correctamente controlar según sea necesario.

## <a name="content-type-header"></a>Encabezado de tipo de contenido

Para empezar a simple, echemos un vistazo a los dos `Content-Types` que no requiere conversión ni conversión usar dentro de una aplicación lógica - `application/json` y `text/plain`.

### <a name="applicationjson"></a>Aplicación/json

El motor de flujo de trabajo se basa en el `Content-Type` llama encabezado HTTP para determinar el control adecuado.  Cualquier solicitud con el tipo de contenido `application/json` se almacenará y se trata como un objeto JSON.  Además, se puede analizar contenido JSON predeterminada sin necesidad de ninguna conversión.  Así que una solicitud que tenga el encabezado de tipo de contenido `application/json ` así:

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

puede analizar en un flujo de trabajo con una expresión como `@body('myAction')['foo'][0]` para obtener un valor (en este caso, `bar`).  No es necesaria ninguna conversión adicional.  Si está trabajando con datos JSON pero no tienen un encabezado especificado, manualmente puede convertir en JSON utilizando la `@json()` función (por ejemplo: `@json(triggerBody())['foo']`).

### <a name="textplain"></a>Texto sin formato

Similar a `application/json`, mensajes HTTP recibidos con la `Content-Type` encabezado de `text/plain` se almacenarán en su formulario sin formato.  Además, si se incluyen en las acciones siguientes sin ninguna conversión la solicitud se enviará con un `Content-Type`: `text/plain` encabezado.  Por ejemplo, si trabaja con un archivo plano puede recibir el contenido HTTP siguiente:

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

as `text/plain`.  Si en la siguiente acción envía como el cuerpo de solicitud de otro (`@body('flatfile')`), la solicitud tendría un `text/plain` encabezado de tipo de contenido.  Si está trabajando con datos de texto sin formato pero no tienen un encabezado especificado, manualmente puede convertir en texto utilizando la `@string()` función (por ejemplo: `@string(triggerBody())`)

### <a name="applicationxml-and-applicationoctet-stream-and-converter-functions"></a>Aplicación/xml y Application/octet-stream y funciones del convertidor

El motor de aplicación lógica siempre se conservará el `Content-Type` que ha recibido en la respuesta o solicitud HTTP.  Esto significa si se recibe un contenido con `Content-Type` de `application/octet-stream`, incluida en una acción posteriores con ninguna conversión dará como resultado una solicitud de salida con `Content-Type`: `application/octet-stream`.  De esta manera el motor pueden guaruntee datos no se perderán mientras se desplaza por el flujo de trabajo.  Sin embargo, el estado de acción (entradas y salidas) se almacenan en un objeto JSON como pase a lo largo del flujo de trabajo.  Esto significa para conservar algunos tipos de datos, el motor convertirá el contenido en una cadena de base 64 binario codificado con metadatos correspondientes que conserve ambas `$content` y `$content-type` -que se convertirán automáticamente.  Puede convertir manualmente entre los tipos de contenido usando integrado en funciones convertidor:

* `@json()`-Convierte datos a`application/json`
* `@xml()`-Convierte datos a`application/xml`
* `@binary()`-Convierte datos a`application/octet-stream`
* `@string()`-Convierte datos a`text/plain`
* `@base64()`-Convierte el contenido en una cadena de base 64
* `@base64toString()`-Convierte una cadena de base 64 codificado`text/plain`
* `@base64toBinary()`-Convierte una cadena de base 64 codificado`application/octet-stream`
* `@encodeDataUri()`-Codifica una cadena como una matriz de bytes dataUri
* `@decodeDataUri()`-Descodifica un dataUri en una matriz de bytes

Por ejemplo, si recibe una solicitud HTTP con `Content-Type`: `application/xml` de:

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Podría emitido y usarla más tarde con algo parecido a `@xml(triggerBody())`, o dentro de una función, como `@xpath(xml(triggerBody()), '/CustomerName')`.

### <a name="other-content-types"></a>Tipos de otro contenido

Otros tipos de contenido son compatibles y funcionarán con una aplicación de la lógica, pero puede requerir recuperar manualmente el cuerpo del mensaje al descodificar la `$content`.  Por ejemplo, si hubiese desencadenante de un `application/x-www-url-formencoded` solicitud que examinó parecido al siguiente:

```
CustomerName=Frank&Address=123+Avenue
```

desde esta un no en texto sin formato o JSON se almacenarán en la acción como:

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

Donde `$content` es la carga codificada como una cadena de base 64 para conservar todos los datos.  Puesto que actualmente no hay una función nativa para datos de formulario, podría usar sigue estos datos dentro de un flujo de trabajo manualmente teniendo acceso a los datos con una función, como `@string(body('formdataAction'))`.  Si quería mi solicitud saliente también tener la `application/x-www-url-formencoded` encabezado de tipo de contenido, podría simplemente agregarlo al cuerpo de acción sin ninguna conversión como `@body('formdataAction')`.  Sin embargo, esto solo funcionará si cuerpo es el único parámetro en el `body` entrada.  Si intenta hacer `@body('formdataAction')` dentro de un `application/json` solicitar obtendrán un error de tiempo de ejecución como enviará el cuerpo codificado.
