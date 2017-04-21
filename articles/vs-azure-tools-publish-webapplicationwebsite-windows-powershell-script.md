<properties
   pageTitle="Publicar-WebApplicationWebSite (script de Windows PowerShell) | Microsoft Azure"
   description="Obtenga información sobre cómo publicar un proyecto web en un sitio Web de Azure. Esta secuencia de comandos crea los recursos necesarios en su suscripción de Azure si no existen."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="publish-webapplicationwebsite-windows-powershell-script"></a>Publicar-WebApplicationWebSite (script de Windows PowerShell)

##<a name="syntax"></a>Sintaxis

Publica un proyecto web en un sitio Web de Azure. La secuencia de comandos crea los recursos necesarios en su suscripción de Azure si no existen.

    Publish-WebApplicationWebSite
    –Configuration <configuration>
    -SubscriptionName <subscriptionName>
    -WebDeployPackage <packageName>
    -DatabaseServerPassword @{Name = "name"; Password = "password"}
    -SendHostMessagesToOutput
    -Verbose


## <a name="configuration"></a>Configuración

La ruta de acceso al archivo de configuración de JSON que describe los detalles de la implementación.

|Parámetro|Valor predeterminado|
|---|---|
|Alias|Ninguno|
|¿Obligatorio?|Verdadero|
|Posición|con el nombre|
|Valor predeterminado|Ninguno|
|¿Aceptar entradas de canalización?|falso|
|¿Aceptar caracteres comodín?|falso|

## <a name="subscriptionname"></a>SubscriptionName

El nombre de la suscripción de Azure que desea crear el sitio Web en.

|Parámetro|Valor predeterminado|
|---|---|
|Alias|Ninguno|
|¿Obligatorio?|falso|
|Posición|con el nombre|
|Valor predeterminado|Ninguno|
|¿Aceptar entradas de canalización?|falso|
|¿Aceptar caracteres comodín?|falso|

## <a name="webdeploypackage"></a>WebDeployPackage

La ruta de acceso para el paquete de implementación web para publicar en el sitio Web. Puede crear este paquete mediante el Asistente para la publicación Web en Visual Studio. Para obtener más información, vea [Introducción a los servicios de nube de Azure y ASP.NET](http://go.microsoft.com/fwlink/p/?LinkID=623089).

|Parámetro|Valor predeterminado|
|---|---|
|Alias|Ninguno|
|¿Obligatorio?|falso|
|Posición|con el nombre|
|Valor predeterminado|Ninguno|
|¿Aceptar entradas de canalización?|falso|
|¿Aceptar caracteres comodín?|falso|

## <a name="databaseserverpassword"></a>DatabaseServerPassword

El nombre de usuario y la contraseña para la base de datos SQL Azure.

|Parámetro|Valor predeterminado|
|---|---|
|Alias|Ninguno|
|¿Obligatorio?|falso|
|Posición|con el nombre|
|Valor predeterminado|Ninguno|
|¿Aceptar entradas de canalización?|falso|
|¿Aceptar caracteres comodín?|falso|

## <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput

Si es true, imprimir mensajes de la secuencia de comandos a la secuencia de salida.

|Parámetro|Valor predeterminado|
|---|---|
|Alias|Ninguno|
|¿Obligatorio?|falso|
|Posición|con el nombre|
|Valor predeterminado|falso|
|¿Aceptar entradas de canalización?|falso|
|¿Aceptar caracteres comodín?|falso|

## <a name="remarks"></a>Notas

Para ver una explicación completa de cómo utilizar la secuencia de comandos para crear entornos de prueba y desarrollo, vea [Usar Windows scripts de PowerShell que publicar en entornos de prueba y desarrollo](vs-azure-tools-publishing-using-powershell-scripts.md).

El archivo de configuración de JSON especifica los detalles de lo que implementará. Incluye la información que especificó al crear el proyecto, como el nombre y el nombre de usuario para el sitio Web. También incluye la base de datos a disposición, si existe. El código siguiente muestra un ejemplo de archivo de configuración de JSON:

    {
        "environmentSettings": {
            "webSite": {
                "name": "WebApplication10554",
                "location": "West US"
            },
            "databases": [
                {
                    "connectionStringName": "DefaultConnection",
                    "databaseName": "WebApplication10554_db",
                    "serverName": "iss00brc88",
                    "user": "sqluser2",
                    "password": "",
                    "edition": "",
                    "size": "",
                    "collation": "",
                    "location": "West US"
                }
            ]
        }
    }

Puede editar el archivo de configuración de JSON para cambiar lo que se implementa. Se requiere una sección del sitio Web, pero la sección de la base de datos es opcional.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte [Publicar-WebApplicationVM (script de Windows PowerShell)](vs-azure-tools-publish-webapplicationvm.md)
