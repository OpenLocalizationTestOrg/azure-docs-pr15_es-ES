<properties 
    pageTitle="Cómo usar el API Inspector para seguimiento llamadas en administración de la API de Azure" 
    description="Obtenga información sobre cómo realizar un seguimiento de llamadas con el Inspector de la API de administración de la API de Azure." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-use-the-api-inspector-to-trace-calls-in-azure-api-management"></a>Cómo usar el API Inspector para seguimiento llamadas en administración de la API de Azure

Administración de la API proporciona una herramienta de API Inspector para ayudarle con la depuración y solución de problemas de la API. El API Inspector puede usarse mediante programación y también se puede usar directamente desde el portal de programadores. 

Además de las operaciones de seguimiento, API Inspector también realiza un seguimiento de las evaluaciones de [expresiones de directiva](https://msdn.microsoft.com/library/azure/dn910913.aspx) . Para ver una demostración, vea [177 ataque carta de nube: más características de administración de API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) y avance rápido a 21:00.

Esta guía proporciona una descripción del uso de la API Inspector.

>[AZURE.NOTE] API Inspector trazas solo se generan y disponible para las solicitudes que contiene las claves de suscripción que pertenecen a la cuenta de [Administrador](api-management-howto-create-groups.md) .

## <a name="trace-call"></a> Usar API Inspector a una llamada de seguimiento

Para usar el API Inspector, agregue un **seguimiento de apim ocp: true** encabezado a la llamada de la operación de la solicitud y, a continuación, descargar e inspeccionar el seguimiento de la dirección URL indicada por el encabezado de respuesta **ocp apim seguimiento de ubicación** . Esto puede hacerse mediante programación y también puede hacerse directamente desde el portal de programadores.

En este tutorial se muestra cómo usar el API Inspector para operaciones de seguimiento con la API de calculadora básica que está configurado en el tutorial de introducción al obtener de [administrar su primera API](api-management-get-started.md) . Si todavía no lo ha finalizado ese tutorial sólo tardará unos minutos para importar la API de calculadora básica, o puede usar otra API de su elección, como la API de eco. Cada instancia del servicio de administración de la API viene preconfigurado con una API de eco que se pueden usar para probar y obtener información sobre la administración de la API. La API de eco devuelve cualquier entrada se envía a ella. Para utilizarla, puede invocar cualquier verbo HTTP y, a continuación, el valor devuelto será simplemente lo que ha enviado. 



Para empezar, haga clic en el **portal de programadores** en el Portal de Azure clásico de su servicio de administración de la API. Las operaciones se pueden llamar directamente desde el portal de programadores que proporciona un modo adecuado para ver y probar las operaciones de una API.

>Si aún no ha creado una instancia de servicio de administración de la API, vea [crear una instancia de servicio de administración de la API][] en el tutorial de [Introducción a la administración de la API de Azure][] .

![Portal de administración de la API de desarrollador][api-management-developer-portal-menu]

Haga clic en **las API de** en el menú superior y, a continuación, haga clic en **Calculadora básica**.

![API de eco][api-management-api]

Haga clic en **probar** para probar la operación de **Agregar dos enteros** .

![Pruébelo:][api-management-open-console]

Mantener el valor predeterminado de los valores de parámetro y seleccione la clave de suscripción para el producto que desea usar de la lista desplegable **clave de suscripción** .

De forma predeterminada en el portal de programadores el **Seguimiento de Apim Ocp** encabezado ya está establecida en **true**. Este encabezado configura si no se genera un seguimiento.

![Enviar][api-management-http-get]

Haga clic en **Enviar** para invocar la operación.

![Enviar][api-management-send-results]

En la respuesta encabezados será un **ocp apim seguimiento ubicación** con un valor similar al siguiente ejemplo.

    ocp-apim-trace-location : https://contosoltdxw7zagdfsprykd.blob.core.windows.net/apiinspectorcontainer/ZW3e23NsW4wQyS-SHjS0Og2-2?sv=2013-08-15&sr=b&sig=Mgx7cMHsLmVDv%2B%2BSzvg3JR8qGTHoOyIAV7xDsZbF7%2Bk%3D&se=2014-05-04T21%3A00%3A13Z&sp=r&verify_guid=a56a17d83de04fcb8b9766df38514742

El seguimiento de se puede descargar desde la ubicación especificada y revisado como se muestra en el siguiente paso.

## <a name="inspect-trace"> </a>Inspeccionar el seguimiento

Para revisar los valores de la traza, descargue el archivo de seguimiento de la dirección URL de **ocp apim seguimiento de ubicación** . Es un archivo de texto con formato JSON y contiene entradas similares al siguiente ejemplo.

    {
        "traceId": "abcd8ea63d134c1fabe6371566c7cbea",
        "traceEntries": {
            "inbound": [
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.2998610Z",
                    "elapsed": "00:00:00.0725926",
                    "data": {
                        "request": {
                            "method": "GET",
                            "url": "https://contoso5.azure-api.net/calc/add?a=51&b=49",
                            "headers": [
                                {
                                    "name": "Ocp-Apim-Subscription-Key",
                                    "value": "5d7c41af64a44a68a2ea46580d271a59"
                                },
                                {
                                    "name": "Connection",
                                    "value": "Keep-Alive"
                                },
                                {
                                    "name": "Host",
                                    "value": "contoso5.azure-api.net"
                                }
                            ]
                        }
                    }
                },
                {
                    "source": "mapper",
                    "timestamp": "2015-06-23T19:51:35.2998610Z",
                    "elapsed": "00:00:00.0726213",
                    "data": {
                        "configuration": {
                            "api": {
                                "from": "/calc",
                                "to": {
                                    "scheme": "http",
                                    "host": "calcapi.cloudapp.net",
                                    "port": 80,
                                    "path": "/api",
                                    "queryString": "",
                                    "query": {},
                                    "isDefaultPort": true
                                }
                            },
                            "operation": {
                                "method": "GET",
                                "uriTemplate": "/add?a={a}&b={b}"
                            },
                            "user": {
                                "id": 1,
                                "groups": [
                                    "Administrators",
                                    "Developers"
                                ]
                            },
                            "product": {
                                "id": 1
                            }
                        }
                    }
                },
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.2998610Z",
                    "elapsed": "00:00:00.0727522",
                    "data": {
                        "message": "Request is being forwarded to the backend service.",
                        "request": {
                            "method": "GET",
                            "url": "http://calcapi.cloudapp.net/api/add?a=51&b=49",
                            "headers": [
                                {
                                    "name": "Ocp-Apim-Subscription-Key",
                                    "value": "5d7c41af64a44a68a2ea46580d271a59"
                                },
                                {
                                    "name": "X-Forwarded-For",
                                    "value": "33.52.215.35"
                                }
                            ]
                        }
                    }
                }
            ],
            "outbound": [
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.4256650Z",
                    "elapsed": "00:00:00.1960601",
                    "data": {
                        "response": {
                            "status": {
                                "code": 200,
                                "reason": "OK"
                            },
                            "headers": [
                                {
                                    "name": "Pragma",
                                    "value": "no-cache"
                                },
                                {
                                    "name": "Content-Length",
                                    "value": "124"
                                },
                                {
                                    "name": "Cache-Control",
                                    "value": "no-cache"
                                },
                                {
                                    "name": "Content-Type",
                                    "value": "application/xml; charset=utf-8"
                                },
                                {
                                    "name": "Date",
                                    "value": "Tue, 23 Jun 2015 19:51:35 GMT"
                                },
                                {
                                    "name": "Expires",
                                    "value": "-1"
                                },
                                {
                                    "name": "Server",
                                    "value": "Microsoft-IIS/8.5"
                                },
                                {
                                    "name": "X-AspNet-Version",
                                    "value": "4.0.30319"
                                },
                                {
                                    "name": "X-Powered-By",
                                    "value": "ASP.NET"
                                }
                            ]
                        }
                    }
                },
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.4256650Z",
                    "elapsed": "00:00:00.1961112",
                    "data": {
                        "message": "Response headers have been sent to the caller. Starting to stream the response body."
                    }
                },
                {
                    "source": "handler",
                    "timestamp": "2015-06-23T19:51:35.4256650Z",
                    "elapsed": "00:00:00.1963155",
                    "data": {
                        "message": "Response body streaming to the caller is complete."
                    }
                }
            ]
        }
    }

## <a name="next-steps"> </a>Pasos siguientes

-   Ver una demostración de seguimiento de las expresiones de directiva en [177 ataque carta de nube: más características de administración de API](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/). Avanzar rápidamente a 21:00 para ver la demostración.

>[AZURE.VIDEO episode-177-more-api-management-features-with-vlad-vinogradsky]

[Use API Inspector to trace a call]: #trace-call
[Inspect the trace]: #inspect-trace
[Next steps]: #next-steps

[Configure API settings]: api-management-howto-create-apis.md#configure-api-settings
[Responses]: api-management-howto-add-operations.md#responses
[How create and publish a product]: api-management-howto-add-products.md

[Introducción a la administración de la API de Azure]: api-management-get-started.md
[Crear una instancia de servicio de administración de la API]: api-management-get-started.md#create-service-instance
[Azure Classic Portal]: https://manage.windowsazure.com/


[api-management-developer-portal-menu]: ./media/api-management-howto-api-inspector/api-management-developer-portal-menu.png
[api-management-api]: ./media/api-management-howto-api-inspector/api-management-api.png
[api-management-echo-api-get]: ./media/api-management-howto-api-inspector/api-management-echo-api-get.png
[api-management-developer-key]: ./media/api-management-howto-api-inspector/api-management-developer-key.png
[api-management-open-console]: ./media/api-management-howto-api-inspector/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-api-inspector/api-management-http-get.png
[api-management-send-results]: ./media/api-management-howto-api-inspector/api-management-send-results.png




 