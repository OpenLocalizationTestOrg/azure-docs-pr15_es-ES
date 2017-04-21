<properties
    pageTitle="Agregar Microsoft Translator en las aplicaciones de lógica | Microsoft Azure"
    description="Información general sobre el conector de Microsoft Translator con parámetros de la API de REST"
    services=""
    suite=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="08/18/2016"
   ms.author="mandia"/>

# <a name="get-started-with-the-microsoft-translator-connector"></a>Empezar a trabajar con el conector de Microsoft Translator
Conectarse a Microsoft Translator para traducir texto, detecta un idioma y mucho más. Microsoft Translator, puede: 

- Crear el flujo de negocio basándose en los datos que se obtiene desde Microsoft Translator. 
- Usar acciones para traducir texto, detecta un idioma y mucho más. Estas acciones obtienen una respuesta y, a continuación, hacer que el resultado esté disponible para otras acciones. Por ejemplo, cuando se crea un nuevo archivo en Dropbox, puede convertir el texto en el archivo a otro idioma con Microsoft Translator.

Para agregar una operación en las aplicaciones de lógica, consulte [crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Desencadenadores y acciones
Microsoft Translator incluye las siguientes acciones. No hay ningún desencadenador.

Desencadenadores | Acciones
--- | ---
Ninguno | <ul><li>Detectar el idioma</li><li>Texto a voz</li><li>Traducir texto</li><li>Obtener idiomas</li><li>Obtener los idiomas de voz</li></ul>

Todos los conectores admiten datos en formatos XML y JSON.


## <a name="create-a-connection-to-microsoft-translator"></a>Crear una conexión a Microsoft Translator

>[AZURE.INCLUDE [Steps to create a connection to Microsoft Translator](../../includes/connectors-create-api-microsofttranslator.md)]


## <a name="swagger-rest-api-reference"></a>Referencia de la API de REST de swagger
Se aplica a la versión: 1.0.

### <a name="detect-language"></a>Detectar el idioma    
Detecta el idioma de origen de acuerdo con texto.  
```GET: /Detect```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|consulta|cadena|Sí|consulta|Ninguno |Texto cuyo idioma se identificarán|

#### <a name="response"></a>Respuesta
|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


### <a name="text-to-speech"></a>Texto a voz    
Convierte un texto determinado en voz como una secuencia de audio en formato de onda.  
```GET: /Speak```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|consulta|cadena|Sí|consulta|Ninguno |Texto que desea convertir|
|idioma|cadena|Sí|consulta|Ninguno |Código de idioma para generar voz (ejemplo: ' en-us')|

#### <a name="response"></a>Respuesta
|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


### <a name="translate-text"></a>Traducir texto    
Convierte texto en un idioma especificado mediante Microsoft Translator.  
```GET: /Translate```

| Nombre| Tipo de datos|Obligatorio|Ubicado en|Valor predeterminado|Descripción|
| ---|---|---|---|---|---|
|consulta|cadena|Sí|consulta|Ninguno |Texto para traducir|
|languageTo|cadena|Sí|consulta| Ninguno|Código de idioma de destino (ejemplo: 'NG')|
|languageFrom|cadena|No|consulta|Ninguno |Idioma de origen; Si no se proporciona, Microsoft Translator intentará detectar automáticamente. (ejemplo: corto)|
|categoría|cadena|No|consulta|general |Categoría de traducción (predeterminado: 'general')|

#### <a name="response"></a>Respuesta
|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


### <a name="get-languages"></a>Obtener idiomas    
Recupera todos los idiomas que admite Microsoft Translator.  
```GET: /TranslatableLanguages```

No hay parámetros para esta llamada. 

#### <a name="response"></a>Respuesta
|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|


### <a name="get-speech-languages"></a>Obtener los idiomas de voz    
Recupera los idiomas disponibles para síntesis de voz.  
```GET: /SpeakLanguages``` 

No hay parámetros para esta llamada.

#### <a name="response"></a>Respuesta
|Nombre|Descripción|
|---|---|
|200|Vale|
|predeterminado|Error en la operación.|

## <a name="object-definitions"></a>Definiciones de objeto

#### <a name="language-language-model-for-microsoft-translator-translatable-languages"></a>Idioma: modelo de lenguaje para idiomas de Microsoft Translator se puede traducir

|Nombre de propiedad | Tipo de datos | Obligatorio|
|---|---|---|
|Código|cadena|No|
|Nombre|cadena|No|


## <a name="next-steps"></a>Pasos siguientes

[Crear una aplicación de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

Vuelva a la [lista de las API](apis-list.md).


<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/connectors-create-api-microsofttranslator/register-your-application.png
