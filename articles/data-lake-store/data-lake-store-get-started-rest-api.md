<properties 
   pageTitle="Introducción a la tienda de lago de datos con la API de REST | Microsoft Azure" 
   description="Usar las API de REST de WebHDFS para realizar operaciones en el almacén de datos de lago" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-rest-apis"></a>Introducción a Azure datos lago almacén mediante las API de REST

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API DE REST](data-lake-store-get-started-rest-api.md)
- [CLI de Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

En este artículo, aprenderá cómo usar las API de REST de WebHDFS y API de REST de almacén de datos lago para realizar la administración de cuentas, así como las operaciones de sistema de archivos en el almacén de lago de datos de Azure. Almacén de lago de datos de Azure expone su propia API de REST para operaciones de administración de cuenta. Sin embargo, dado que almacén de datos de lago es compatible con ecosistema HDFS y Hadoop, admite el uso de las API de REST de WebHDFS para operaciones de sistema de archivos.

>[AZURE.NOTE] Para obtener información detallada sobre la API de REST de soporte técnico para el almacén de datos de lago, vea [Referencia de la API de Azure datos lago almacén resto](https://msdn.microsoft.com/library/mt693424.aspx).

## <a name="prerequisites"></a>Requisitos previos

- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/pricing/free-trial/).

- **Crear una aplicación de Azure Active Directory**. Utilizar la aplicación de Azure AD para autenticar la aplicación de almacenamiento de lago de datos con Azure AD. Existen diferentes métodos para autenticar con Azure AD, que son la **autenticación de usuario final** o **servicio de servicio**. Para obtener instrucciones y más información acerca de cómo autenticar, vea [autenticar con el almacén de datos de lago con Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

- [doblez](http://curl.haxx.se/). En este artículo utiliza doblez para demostrar cómo realizar llamadas de la API de REST con una cuenta de almacén de datos lago.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>¿Cómo se puede autenticar mediante Azure Active Directory?

Puede usar dos métodos para autenticar con Azure Active Directory.

### <a name="end-user-authentication-interactive"></a>Autenticación de usuario final (interactiva)

En este escenario, la aplicación pide al usuario que inicie sesión y todas las operaciones se realizan en el contexto del usuario. Realice los pasos siguientes para la autenticación interactiva.

1. A través de la aplicación, redirija al usuario a la siguiente URL:

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

    >[AZURE.NOTE] \<URI de REDIRECCIÓN > es necesario codificar para su uso en una dirección URL. Por lo tanto, para https://localhost, use `https%3A%2F%2Flocalhost`)

    Para el propósito de este tutorial, puede reemplazar los valores de marcador de posición de la dirección URL que arriba y péguelos en la barra de direcciones del explorador web. Se le redirigirá para autenticar con el inicio de sesión de Azure. Una vez que inicie sesión correctamente, la respuesta se muestra en la barra de direcciones del explorador. La respuesta será el siguiente formato:
        
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Capturar el código de autorización de la respuesta. En este tutorial, puede copiar el código de autorización de la barra de direcciones del explorador web y pasar en la convocatoria de la publicación de al extremo token, tal como se muestra a continuación:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>

    >[AZURE.NOTE] En este caso, el \<REDIRECCIÓN URI > no se necesita codificar.

3. La respuesta es un objeto JSON que contiene un token de acceso (por ejemplo, `"access_token": "<ACCESS_TOKEN>"`) y un símbolo de actualización (por ejemplo, `"refresh_token": "<REFRESH_TOKEN>"`). La aplicación utiliza el token de acceso al acceder a la tienda de lago de datos de Azure y el token de actualización para obtener otro token de acceso cuando expira un token de acceso.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before": "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4.  Cuando expira el token de acceso, puede solicitar un nuevo token de acceso mediante el token de actualización, tal como se muestra a continuación:

         curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
            -F grant_type=refresh_token \
            -F resource=https://management.core.windows.net/ \
            -F client_id=<CLIENT-ID> \
            -F refresh_token=<REFRESH-TOKEN>
 
Para obtener más información sobre la autenticación de usuario interactivo, vea el [código de autorización conceder flujo](https://msdn.microsoft.com/library/azure/dn645542.aspx).

### <a name="service-to-service-authentication-non-interactive"></a>Servicio de servicio de autenticación (no interactiva)

En este escenario, la proporciona sus propias credenciales para realizar las operaciones de la aplicación. Para ello, se debe enviar una solicitud de publicación como el que se muestra a continuación. 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

El resultado de esta solicitud incluirá un token de autorización (indicado por `access-token` en el siguiente resultado) que pasará posteriormente con las llamadas de la API de REST. Guardar este token de autenticación en un archivo de texto; lo necesitará más adelante en este artículo.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Este artículo se utiliza la **no interactivo** enfoque. Para obtener más información sobre no interactivo (llamadas del servicio de servicio), vea [servicio a llamadas de servicio con las credenciales](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## <a name="create-a-data-lake-store-account"></a>Crear una cuenta de almacén de datos lago

Esta operación se basa en la API de REST llamada definido [aquí](https://msdn.microsoft.com/library/mt694078.aspx).

Use el siguiente comando cURL. Reemplazar ** \<yourstorename >** con su nombre de almacén de datos lago.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

En el comando anterior, reemplace \< `REDACTED` \> con el token de autorización recuperar una versión anterior. La carga de solicitud para este comando se encuentra en el archivo **input.json** que se proporcionó para la `-d` parámetro anteriormente. El contenido del archivo input.json similar al siguiente:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }   

## <a name="create-folders-in-a-data-lake-store-account"></a>Crear carpetas en una cuenta de almacén de datos lago

Esta operación se basa en la API de REST WebHDFS llamada definido [aquí](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Use el siguiente comando cURL. Reemplazar ** \<yourstorename >** con su nombre de almacén de datos lago.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

En el comando anterior, reemplace \< `REDACTED` \> con el token de autorización recuperar una versión anterior. Este comando crea un directorio denominado **mytempdir** en la carpeta raíz de su cuenta de almacén de datos lago.

Si la operación se realiza correctamente, verá una respuesta similar a esta:

    {"boolean":true}

## <a name="list-folders-in-a-data-lake-store-account"></a>Lista de carpetas en una cuenta de almacén de datos lago

Esta operación se basa en la API de REST WebHDFS llamada definido [aquí](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Use el siguiente comando cURL. Reemplazar ** \<yourstorename >** con su nombre de almacén de datos lago.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

En el comando anterior, reemplace \< `REDACTED` \> con el token de autorización recuperar una versión anterior.

Si la operación se realiza correctamente, verá una respuesta similar a esta:

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "NotSupportYet",
            "group": "NotSupportYet"
        }]
    }
    }

## <a name="upload-data-into-a-data-lake-store-account"></a>Cargar datos en una cuenta de almacén de datos lago

Esta operación se basa en la API de REST WebHDFS llamada definido [aquí](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Cargar datos con la API de REST de WebHDFS es un proceso de dos pasos, como se explica a continuación.

1. Enviar una solicitud HTTP poner sin enviar los datos del archivo que va a cargar. En el siguiente comando, reemplace ** \<yourstorename >** con su nombre de almacén de datos lago.

        curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE

    El resultado de este comando se contendrá una dirección URL de redirección temporal, como se muestra a continuación.

        HTTP/1.1 100 Continue

        HTTP/1.1 307 Temporary Redirect
        ...
        ...
        Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
        ...
        ...

2. Ahora debe enviar otra solicitud HTTP poner con la URL que aparece para la propiedad de **ubicación** en la respuesta. Reemplazar ** \<yourstorename >** con su nombre de almacén de datos lago.

        curl -i -X PUT -T myinputfile.txt -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=CREATE&write=true

    El resultado será similar al siguiente:

        HTTP/1.1 100 Continue

        HTTP/1.1 201 Created
        ...
        ...

## <a name="read-data-from-a-data-lake-store-account"></a>Leer datos desde una cuenta de almacén de datos lago

Esta operación se basa en la API de REST WebHDFS llamada definido [aquí](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

Leer los datos de un almacén de datos de lago cuenta es un proceso de dos pasos.

* Enviar una solicitud GET contra el extremo `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Esto devolverá una ubicación para enviar la solicitud GET siguiente.
* A continuación, envíe la solicitud GET contra el extremo `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Esta opción mostrará el contenido del archivo.

Sin embargo, porque no hay ninguna diferencia de los parámetros de entrada entre el primer y el segundo paso, puede usar el `-L` parámetro para enviar la primera solicitud. `-L`opción esencialmente combina dos solicitudes en uno y le será doblez rehacer la solicitud en la nueva ubicación. Por último, se muestran los resultados de todas las llamadas de solicitud, como se muestra a continuación. Reemplazar ** \<yourstorename >** con su nombre de almacén de datos lago.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN

Debe ver un resultado similar al siguiente:

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...
    
    HTTP/1.1 200 OK
    ...
    
    Hello, Data Lake Store user!

## <a name="rename-a-file-in-a-data-lake-store-account"></a>Cambiar el nombre de un archivo en una cuenta de almacén de datos lago

Esta operación se basa en la API de REST WebHDFS llamada definido [aquí](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Use el siguiente comando cURL para cambiar el nombre de un archivo. Reemplazar ** \<yourstorename >** con su nombre de almacén de datos lago.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

Debe ver un resultado similar al siguiente:

    HTTP/1.1 200 OK
    ...
    
    {"boolean":true}

## <a name="delete-a-file-from-a-data-lake-store-account"></a>Eliminar un archivo desde una cuenta de almacén de datos lago

Esta operación se basa en la API de REST WebHDFS llamada definido [aquí](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Use el siguiente comando cURL para eliminar un archivo. Reemplazar ** \<yourstorename >** con su nombre de almacén de datos lago.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

Debe ver un resultado similar al siguiente:

    HTTP/1.1 200 OK
    ...
    
    {"boolean":true}

## <a name="delete-a-data-lake-store-account"></a>Eliminar una cuenta de almacén de datos lago

Esta operación se basa en la API de REST llamada definido [aquí](https://msdn.microsoft.com/library/mt694075.aspx).

Use el siguiente comando cURL para eliminar una cuenta de almacén de datos lago. Reemplazar ** \<yourstorename >** con su nombre de almacén de datos lago.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Debe ver un resultado similar al siguiente:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="see-also"></a>Vea también

- [Abrir las aplicaciones de origen de datos grande compatibles con lago almacén de datos de Azure](data-lake-store-compatible-oss-other-applications.md)
 
