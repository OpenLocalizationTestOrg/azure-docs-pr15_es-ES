<properties
    pageTitle="Compile e implemente una aplicación de Java API de servicio de la aplicación de Azure"
    description="Obtenga información sobre cómo crear un paquete de aplicación de la API de Java e implementar al servicio de la aplicación de Azure."
    services="app-service\api"
    documentationCenter="java"
    authors="bradygaster"
    manager="mohisri"
    editor="tdykstra"/>

<tags
    ms.service="app-service-api"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="get-started-article"
    ms.date="08/31/2016"
    ms.author="rachelap"/>

# <a name="build-and-deploy-a-java-api-app-in-azure-app-service"></a>Compile e implemente una aplicación de Java API de servicio de la aplicación de Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

Este tutorial muestra cómo crear una aplicación de Java e implementar aplicaciones de API de servicio de Azure aplicación mediante [Git]. Pueden seguir las instrucciones de este tutorial en ningún sistema operativo que sea capaz de ejecutar Java. El código de este tutorial se integra con [Maven]. [RS Jax] se usa para crear el servicio REST y se genera basándose en la especificación de metadatos [Swagger] mediante el [Editor de Swagger].

## <a name="prerequisites"></a>Requisitos previos

1. [Kit de desarrollo de Java 8] \(o posterior)
1. [Maven] instalado en su equipo de desarrollo
1. [Git] instalado en su equipo de desarrollo
1. Una suscripción de pago o [versión de prueba gratuita] a [Microsoft Azure]
1. Una aplicación de prueba HTTP como [Postman]

## <a name="scaffold-the-api-using-swaggerio"></a>Scaffold la API usando Swagger.IO

Mediante el editor de swagger.io en línea, puede escribir código JSON Swagger o YAML que representa la estructura de la API. Una vez que la superficie de API diseñada, puede exportar código para una gran variedad de plataformas y marcos. En la siguiente sección, se modificará el código scaffolded para incluir la funcionalidad ficticio. 

Esta demostración comenzará con texto JSON Swagger que va a pegar en el editor de swagger.io, que se utilizará para generar el código utilizando JAX RS para tener acceso a un extremo de la API de REST. A continuación, deberá editar el código scaffolded para devolver datos ficticios, simular una API de REST creada sobre un mecanismo de persistencia de datos.  

1. Copie el siguiente código de JSON Swagger al Portapapeles:

        {
            "swagger": "2.0",
            "info": {
                "version": "v1",
                "title": "Contact List",
                "description": "A Contact list API based on Swagger and built using Java"
            },
            "host": "localhost",
            "schemes": [
                "http",
                "https"
            ],
            "basePath": "/api",
            "paths": {
                "/contacts": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_get",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                },
                "/contacts/{id}": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_getById",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "parameters": [
                            {
                                "name": "id",
                                "in": "path",
                                "required": true,
                                "type": "integer",
                                "format": "int32"
                            }
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                }
            },
            "definitions": {
                "Contact": {
                    "type": "object",
                    "properties": {
                        "Id": {
                            "format": "int32",
                            "type": "integer"
                        },
                        "Name": {
                            "type": "string"
                        },
                        "EmailAddress": {
                            "type": "string"
                        }
                    }
                }
            }
        }

1. Vaya a la [Swagger en línea Editor]. Una vez allí, haga clic en el elemento de menú **archivo -> Pegar JSON** .

    ![Elemento de menú Pegar JSON][paste-json]

1. Pegue los contactos lista API Swagger JSON que copió anteriormente. 

    ![Pegar el código JSON en Swagger][pasted-swagger]

1. Ver las páginas de documentación y resumen de API representado en el editor. 

    ![Vista Swagger generados por documentos][view-swagger-generated-docs]

1. Seleccione la opción de menú **Generar servidor-> JAX RS** a scaffold el código de servidor podrá modificar más adelante para agregar implementación ficticio. 

    ![Generar código elemento de menú][generate-code-menu-item]

    Una vez que se genera el código, deberá proporcionar un archivo ZIP para descargar. Este archivo contiene el código scaffolded por el generador de código Swagger y todos los asociados secuencias de comandos de generación. Descomprima toda la biblioteca en un directorio de su estación de trabajo de desarrollo. 

## <a name="edit-the-code-to-add-api-implementation"></a>Editar el código para agregar la implementación de la API

En esta sección, va a reemplazar la implementación del servidor del código generado Swagger con el código personalizado. El nuevo código devolverá entidades de una lista de matrices de contacto para el cliente de la llamada. 

1. Abra el archivo de modelo *Contact.java* , que se encuentra en la carpeta de *generación/src/java/swagger/io/modelo* usando [Código de Visual Studio] o el editor de texto. 

    ![Archivo de modelo de contacto abierto][open-contact-model-file]

1. Agregue el siguiente constructor a la clase de **contacto** . 

        public Contact(Integer id, String name, String email) 
        {
            this.id = id;
            this.name = name;
            this.emailAddress = email;
        }

1. Abra el archivo de implementación de servicio *ContactsApiServiceImpl.java* , que se encuentra en la carpeta *src/main/java/io/swagger/api/implementa* , usando [Código de Visual Studio] o el editor de texto.

    ![Archivo de código de servicio de contacto abierto][open-contact-service-code-file]

1. Sobrescribir el código en el archivo con este código de nuevo para agregar una implementación ficticio para el código de servicio. 

        package io.swagger.api.impl;

        import io.swagger.api.*;
        import io.swagger.model.*;
        import com.sun.jersey.multipart.FormDataParam;
        import io.swagger.model.Contact;
        import java.util.*;
        import io.swagger.api.NotFoundException;
        import java.io.InputStream;
        import com.sun.jersey.core.header.FormDataContentDisposition;
        import com.sun.jersey.multipart.FormDataParam;
        import javax.ws.rs.core.Response;
        import javax.ws.rs.core.SecurityContext;

        @javax.annotation.Generated(value = "class io.swagger.codegen.languages.JaxRSServerCodegen", date = "2015-11-24T21:54:11.648Z")
        public class ContactsApiServiceImpl extends ContactsApiService {
  
            private ArrayList<Contact> loadContacts()
            {
                ArrayList<Contact> list = new ArrayList<Contact>();
                list.add(new Contact(1, "Barney Poland", "barney@contoso.com"));
                list.add(new Contact(2, "Lacy Barrera", "lacy@contoso.com"));
                list.add(new Contact(3, "Lora Riggs", "lora@contoso.com"));
                return list;
            }
  
            @Override
            public Response contactsGet(SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                return Response.ok().entity(list).build();
                }
  
            @Override
            public Response contactsGetById(Integer id, SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                Contact ret = null;
            
                for(int i=0; i<list.size(); i++)
                {
                    if(list.get(i).getId() == id)
                        {
                            ret = list.get(i);
                        }
                }
                return Response.ok().entity(ret).build();
            }
        }

1. Abra un símbolo del sistema y cambie el directorio a la carpeta raíz de la aplicación.

1. Ejecute el siguiente comando de experto para generar el código y ejecutar mediante el servidor de aplicación embarcadero localmente. 

        mvn package jetty:run

1. Verá la ventana de comandos reflejar que embarcadero ha iniciado el código en el puerto 8080. 

    ![Archivo de código de servicio de contacto abierto][run-jetty-war]

1. Usar [Postman] para realizar una solicitud de "obtener todos los contactos" método API api/http://localhost: 8080/contactos.

    ![Llamar a la API de contactos][calling-contacts-api]

1. Use [Postman] para realizar una solicitud al método API "obtener contacto específico" ubicado en http://localhost: 8080, api, contactos/2.

    ![Llamar a la API de contactos][calling-specific-contact-api]

1. Por último, puede crear el archivo de Java guerra (archivo Web) ejecutando el siguiente comando de experto en la consola. 

        mvn package war:war

1. Una vez creado el archivo de guerra, se pondrán en la carpeta de **destino** . Vaya a la carpeta de **destino** y cambie el nombre del archivo de guerra **ROOT.war**. (Asegúrese de que las mayúsculas y minúsculas coincide con este formato).

         rename swagger-jaxrs-server-1.0.0.war ROOT.war

1. Por último, ejecute los siguientes comandos de la carpeta raíz de la aplicación para crear una carpeta **implementar** usar para implementar el archivo guerra Azure. 

         mkdir deploy
         mkdir deploy\webapps
         copy target\ROOT.war deploy\webapps
         cd deploy

## <a name="publish-the-output-to-azure-app-service"></a>Publicar la salida al servicio de aplicación de Azure

En esta sección deberá aprender a crear un App API de nuevo con el Portal de Azure, preparar esa App API para hospedar aplicaciones Java y distribuir el archivo de guerra recién creado al servicio de aplicación de Azure para ejecutar el App API nueva. 

1. Crear una nueva aplicación de la API en el [portal de Azure], haciendo clic en el elemento de menú **Nuevo -> Web + Mobile -> aplicación API** , introducir los detalles de la aplicación y, a continuación, haga clic en **crear**.

    ![Crear un nuevo App API][create-api-app]

1. Una vez que se ha creado la aplicación de la API, abra el módulo de **configuración** de la aplicación y, a continuación, haga clic en el elemento de menú de **configuración de la aplicación** . Seleccione las últimas versiones de Java de las opciones disponibles, a continuación, seleccione la última Tomcat en el menú **Web contenedor** y, a continuación, haga clic en **Guardar**.

    ![Configurar Java en el módulo API App][set-up-java]

1. Haga clic en el elemento de menú de configuración de **credenciales de implementación** y proporcione un nombre de usuario y la contraseña que desee usar para la publicación de archivos a su App API. 

    ![Establezca las credenciales de implementación][deployment-credentials]

1. Haga clic en el elemento de menú de configuración de **origen de la implementación** . Una vez, haga clic en el botón **Elegir origen** , seleccione la opción de **Repositorio Git Local** y, a continuación, haga clic en **Aceptar**. Esta opción creará un repositorio Git ejecuta en Azure, que tiene una asociación con la API de App. Cada vez que confirma código en la rama *principal* del repositorio Git, el código se publicará en la instancia de App API ejecución directo. 

    ![Configurar un nuevo repositorio Git local][select-git-repo]

1. Copie la dirección URL del nuevo Git depósito al Portapapeles. Guardar como será importante en un momento. 

    ![Configurar un nuevo repositorio Git para la aplicación][copy-git-repo-url]

1. GIT insertar el archivo guerra en el repositorio en línea. Para ello, vaya a la carpeta de **implementar** que creó anteriormente para que puede confirmar fácilmente el código hasta el repositorio que ejecuta en el servicio de aplicación. Una vez que se encuentra en la ventana de la consola y navegar en la carpeta donde se encuentra la carpeta aplicaciones Web, emita los siguientes comandos de Git para iniciar el proceso y lanzar una implementación. 

         git init
         git add .
         git commit -m "initial commit"
         git remote add azure [YOUR GIT URL]
         git push azure master

    Una vez que se emite la solicitud de **inserción** , se le pedirá la contraseña que creó anteriormente para la credencial de implementación. Después de escribir sus credenciales, verá su portal de mostrar que la actualización se ha implementado.

1. Si usa una vez más Postman para acertar el App API recién implementado ejecuta en el servicio de aplicación de Azure, verá que el comportamiento es coherente y que ahora devuelve datos de los contactos según lo esperado, y usando los cambios de código sencillo para el Swagger.io scaffolded código Java. 

    ![Uso de la API de REST de contactos de Java live en Azure][postman-calling-azure-contacts]

## <a name="next-steps"></a>Pasos siguientes

En este artículo, podía comenzar con un archivo de Swagger JSON y algunos scaffolded código Java que se obtienen desde el editor de Swagger.io. Desde allí, los cambios simples y un Git implementación proceso ha generado tiene una aplicación de API funcional escrita en Java. El siguiente tutorial se muestra cómo [utilizar las aplicaciones de la API de JavaScript los clientes mediante CORS][App Service API CORS]. Tutoriales posteriores de la serie muestran cómo implementar la autenticación y la autorización.

Para utilizar este ejemplo, puede obtener más información sobre el [SDK de almacenamiento para Java] para conservar los BLOB JSON. O bien, puede usar el [SDK de Java DB documento] para guardar los datos de contacto en Azure documento DB. 

Para obtener más información sobre el uso de Java en Azure, consulte el [Centro para desarrolladores de Java].

<!-- URL List -->

[App Service API CORS]: app-service-api-cors-consume-javascript.md
[Portal de Azure]: https://portal.azure.com/
[Documento Java DB SDK]: ../documentdb/documentdb-java-application.md
[versión de prueba gratuita]: https://azure.microsoft.com/pricing/free-trial/
[GIT]: http://www.git-scm.com/
[Centro para desarrolladores de Java]: /develop/java/
[Kit de desarrollo de Java 8]: http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
[JAX RS]: https://jax-rs-spec.java.net/
[Experto]: https://maven.apache.org/
[Microsoft Azure]: https://azure.microsoft.com/
[Editor de Swagger en línea]: http://editor.swagger.io/
[Postman]: https://www.getpostman.com/
[Almacenamiento SDK de Java]: ../storage/storage-java-how-to-use-blob-storage.md
[Swagger]: http://swagger.io/
[Editor de swagger]: http://editor.swagger.io/
[Código de Visual Studio]: https://code.visualstudio.com

<!-- IMG List -->

[paste-json]: ./media/app-service-api-java-api-app/paste-json.png
[pasted-swagger]: ./media/app-service-api-java-api-app/pasted-swagger.png
[view-swagger-generated-docs]: ./media/app-service-api-java-api-app/view-swagger-generated-docs.png
[generate-code-menu-item]: ./media/app-service-api-java-api-app/generate-code-menu-item.png
[open-contact-model-file]: ./media/app-service-api-java-api-app/open-contact-model-file.png
[open-contact-service-code-file]: ./media/app-service-api-java-api-app/open-contact-service-code-file.png
[run-jetty-war]: ./media/app-service-api-java-api-app/run-jetty-war.png
[calling-contacts-api]: ./media/app-service-api-java-api-app/calling-contacts-api.png
[calling-specific-contact-api]: ./media/app-service-api-java-api-app/calling-specific-contact-api.png
[create-api-app]: ./media/app-service-api-java-api-app/create-api-app.png
[set-up-java]: ./media/app-service-api-java-api-app/set-up-java.png
[deployment-credentials]: ./media/app-service-api-java-api-app/deployment-credentials.png
[select-git-repo]: ./media/app-service-api-java-api-app/select-git-repo.png
[copy-git-repo-url]: ./media/app-service-api-java-api-app/copy-git-repo-url.png
[postman-calling-azure-contacts]: ./media/app-service-api-java-api-app/postman-calling-azure-contacts.png
