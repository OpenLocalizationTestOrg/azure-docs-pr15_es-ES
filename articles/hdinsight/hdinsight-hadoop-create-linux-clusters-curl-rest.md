<properties
    pageTitle="Crear Hadoop, HBase o tormenta clústeres en Linux en HDInsight con doblez y las API de REST de Azure | Microsoft Azure"
    description="Aprenda a crear clústeres basados en Linux HDInsight con doblez, plantillas de administrador de recursos de Azure y las API de REST de Azure. Puede especificar el tipo de clúster (Hadoop, HBase o tormenta) o utilizar secuencias de comandos para instalar componentes personalizados."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/11/2016"
    ms.author="larryfr"/>

#<a name="create-linux-based-clusters-in-hdinsight-using-curl-and-the-azure-rest-api"></a>Crear clústeres basados en Linux en HDInsight con doblez y las API de REST de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

La API de REST de Azure le permite realizar operaciones de administración de servicios alojados en la plataforma Windows Azure, incluida la creación de nuevos recursos como clústeres HDInsight basados en Linux. En este documento, obtendrá información sobre cómo crear plantillas de administrador de recursos de Azure para configurar un clúster de HDInsight y su almacenamiento asociado, a continuación, usar doblez para implementar la plantilla de la API de REST de Azure para crear un nuevo clúster de HDInsight.

> [AZURE.IMPORTANT] Los pasos de este documento utiliza el número predeterminado de nodos de trabajo (4) para un clúster de HDInsight. Si piensa en más de 32 nodos de trabajo, en la creación de un clúster o ajustando el clúster tras la creación, debe seleccionar un tamaño de nodo principal con al menos 8 núcleos y 14GB de ram.
>
> Para obtener más información sobre los tamaños de nodo y los costos asociados, consulte [HDInsight de precios](https://azure.microsoft.com/pricing/details/hdinsight/).

##<a name="prerequisites"></a>Requisitos previos

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


- **Suscripción de un Azure**. Vea la [versión de prueba gratuita de Azure obtener](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __CLI azure__. Azure CLI se usa para crear un servicio principal, que se utiliza para generar tokens de autenticación para las solicitudes de la API de REST de Azure.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

- __doblez__. Esta utilidad está disponible a través de su sistema de administración del paquete, o puede descargarse desde [http://curl.haxx.se/](http://curl.haxx.se/).

    > [AZURE.NOTE] Si está utilizando PowerShell para ejecutar los comandos de este documento, primero debe quitar la `curl` alias que crea de manera predeterminada. Este alias utiliza WebRequest de invocación, un cmdlet de PowerShell, en lugar de doblez cuando se usa el `curl` comando desde un símbolo del sistema de PowerShell y devolverá errores para muchos de los comandos usados en este documento.
    > 
    > Para quitar este alias, utilice la siguiente en el símbolo del sistema de PowerShell:
    >
    > `Remove-item alias:curl`
    >
    > Una vez que se ha quitado el alias, debe usar la versión de doblez que haya instalado en el sistema.

### <a name="access-control-requirements"></a>Requisitos de control de acceso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="create-a-template"></a>Crear una plantilla

Plantillas de administración de recursos de Azure son documentos JSON que describen un __grupo de recursos__ y todos los recursos en él (por ejemplo, HDInsight.) Este enfoque plantilla basada permite definir todos los recursos que necesita para HDInsight en una plantilla y administrar los cambios en el grupo completo a través de __implementaciones__ que aplicar los cambios al grupo.

Plantillas normalmente se proporcionan en dos partes; la propia plantilla y un archivo de parámetros que rellenar con valores específicos para su configuración. Ejemplo, el nombre de clúster, el nombre de administrador y contraseña. Cuando se usa directamente la API de REST, debe combinarlas en un archivo. El formato de este documento JSON es:

    {
        "properties": {
            "template": {
                contents of template file
            },
            "mode": "deploymentmode",
            "Parameters": {
                contents of the parameters element from parameters file
            }
        }
    }

Por ejemplo, la siguiente es una combinación de los archivos de plantilla y los parámetros de [https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-ssh-password), que crea un clúster de Linux utilizando una contraseña para proteger la cuenta de usuario SSH.

    {
        "properties": {
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "location": {
                        "type": "string",
                        "allowedValues": ["Central US",
                        "East Asia",
                        "East US",
                        "Japan East",
                        "Japan West",
                        "North Europe",
                        "South Central US",
                        "Southeast Asia",
                        "West Europe",
                        "West US"],
                        "metadata": {
                            "description": "The location where all azure resources will be deployed."
                        }
                    },
                    "clusterType": {
                        "type": "string",
                        "allowedValues": ["hadoop",
                        "hbase",
                        "storm",
                        "spark"],
                        "metadata": {
                            "description": "The type of the HDInsight cluster to create."
                        }
                    },
                    "clusterName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the HDInsight cluster to create."
                        }
                    },
                    "clusterLoginUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
                        }
                    },
                    "clusterLoginPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "sshUserName": {
                        "type": "string",
                        "metadata": {
                            "description": "These credentials can be used to remotely access the cluster."
                        }
                    },
                    "sshPassword": {
                        "type": "securestring",
                        "metadata": {
                            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
                        }
                    },
                    "clusterStorageAccountName": {
                        "type": "string",
                        "metadata": {
                            "description": "The name of the storage account to be created and be used as the cluster's storage."
                        }
                    },
                    "clusterWorkerNodeCount": {
                        "type": "int",
                        "defaultValue": 4,
                        "metadata": {
                            "description": "The number of nodes in the HDInsight cluster."
                        }
                    }
                },
                "variables": {
                    "defaultApiVersion": "2015-05-01-preview",
                    "clusterApiVersion": "2015-03-01-preview"
                },
                "resources": [{
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('defaultApiVersion')]",
                    "dependsOn": [],
                    "tags": {
                        
                    },
                    "properties": {
                        "accountType": "Standard_LRS"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('location')]",
                    "apiVersion": "[variables('clusterApiVersion')]",
                    "dependsOn": ["[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"],
                    "tags": {
                        
                    },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "[parameters('clusterType')]",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [{
                                "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                "isDefault": true,
                                "container": "[parameters('clusterName')]",
                                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                            }]
                        },
                        "computeProfile": {
                            "roles": [{
                                "name": "headnode",
                                "targetInstanceCount": "2",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            },
                            {
                                "name": "workernode",
                                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                "hardwareProfile": {
                                    "vmSize": "Standard_D3"
                                },
                                "osProfile": {
                                    "linuxOperatingSystemProfile": {
                                        "username": "[parameters('sshUserName')]",
                                        "password": "[parameters('sshPassword')]"
                                    }
                                }
                            }]
                        }
                    }
                }],
                "outputs": {
                    "cluster": {
                        "type": "object",
                        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                    }
                }
            },
            "mode": "incremental",
            "Parameters": {
                "location": {
                    "value": "North Europe"
                },
                "clusterName": {
                    "value": "newclustername"
                },
                "clusterType": {
                    "value": "hadoop"
                },
                "clusterStorageAccountName": {
                    "value": "newstoragename"
                },
                "clusterLoginUserName": {
                    "value": "admin"
                },
                "clusterLoginPassword": {
                    "value": "changeme"
                },
                "sshUserName": {
                    "value": "sshuser"
                },
                "sshPassword": {
                    "value": "changeme"
                }
            }
        }
    }

En este ejemplo se utilizará en los pasos de este documento. Debe reemplazar los _valores_ de marcador de posición en la sección __parámetros__ al final del documento con los valores que desea usar para el clúster.

##<a name="login-to-your-azure-subscription"></a>Inicie sesión en su suscripción de Azure

Siga los pasos documentados en [Conectar con una suscripción de Azure desde la interfaz de línea de comandos de Azure (Azure CLI)](../xplat-cli-connect.md) y conectarse a su suscripción utilizando el `azure login` comando.

##<a name="create-a-service-principal"></a>Crear una entidad de seguridad de servicio

> [AZURE.NOTE] Estos pasos son una versión abreviada de la información contenida en la sección _principal con una contraseña - Azure CLI del servicio de autenticación_ del documento [autenticar a un principal de servicio con el Administrador de recursos de Azure](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-password---azure-cli) . Estos pasos crean un nuevo servicio principal que se puede utilizar para autenticar las solicitudes de API de REST utilizadas para crear recursos Azure como un clúster de HDInsight.

1. Desde el símbolo del sistema, sesión de terminal o shell, use el comando siguiente para obtener una lista de las suscripciones de Azure.

        azure account list
        
    En la lista, seleccione la suscripción que desea usar y observe la columna __Id__ . Este es el __identificador de suscripción__ y se utilizará en la mayoría de los pasos de este documento.

2. Crear una nueva aplicación de Azure Active Directory.

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    Reemplace los valores de la `--name`, `--home-page`, y `--identifier-uris` con sus propios valores. Proporcione una contraseña para la nueva entrada de Active Directory.
    
    > [AZURE.NOTE] Dado que va a crear esta aplicación para la autenticación a través de una entidad de seguridad del servicio, el `--home-page` y `--identifier-uris` valores no necesitan hacer referencia a una página web real hospedado en internet; ¿necesita ser URI único.
    
    Desde los datos devueltos, guardar el valor de __ID__ .
    
        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK
    
3. Crear un servicio principal utilizando el valor de __ID__ devuelto previamente.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
     Desde los datos devueltos, guardar el valor del __Identificador de objeto__ .
     
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
4. Asignar el rol de __propietario__ del servicio principal utilizando el valor del __Identificador de objeto__ devuelto previamente. También debe utilizar el __identificador de suscripción__ obtenido anteriormente.
    
        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Owner -c /subscriptions/{SubscriptionID}/
        
    Una vez completado este comando, el servicio principal ahora tiene acceso de propietario para el identificador de suscripción especificada.

##<a name="get-an-authentication-token"></a>Obtener un símbolo de autenticación

1. Use las siguientes acciones para encontrar el __Identificador del inquilino__ de su suscripción.

        azure account show -s <subscription ID>
        
    Desde los datos devueltos, busque el __Identificador del inquilino__.
    
        info:    Executing command account show
        data:    Name                        : MyAzureAccount
        data:    ID                          : 45a1014d-0f27-25d2-b838-b8f373d6d52e
        data:    State                       : Enabled
        data:    Tenant ID                   : 22f988bf-56f1-41af-91ab-3d7cd011db47
        data:    Is Default                  : true
        data:    Environment                 : AzureCloud
        data:    Has Certificate             : No
        data:    Has Access Token            : Yes
        data:    User name                   : myname@contoso.org
        data:    
        info:    account show command OK

2. Generar un nuevo token con la API de REST de Azure.

        curl -X "POST" "https://login.microsoftonline.com/TenantID/oauth2/token" \
        -H "Cookie: flight-uxoptin=true; stsservicecookie=ests; x-ms-gateway-slice=productionb; stsservicecookie=ests" \
        -H "Content-Type: application/x-www-form-urlencoded" \
        --data-urlencode "client_id=AppID" \
        --data-urlencode "grant_type=client_credentials" \
        --data-urlencode "client_secret=password" \
        --data-urlencode "resource=https://management.azure.com/"
    
    Reemplace __TenantID__, __ID__y __contraseña__ con los valores que se obtienen o utilizado previamente.

    Si esta solicitud se realiza correctamente, recibirá una respuesta de la 200 serie y el cuerpo de la respuesta contendrá un documento JSON.

    El documento JSON devuelto por esta solicitud contendrá un elemento denominado __access_token__; el valor de este elemento es el token de acceso que se debe usar para la autenticación de las solicitudes utilizadas en las siguientes secciones de este documento.
    
        {
            "token_type":"Bearer",
            "expires_in":"3599",
            "expires_on":"1463409994",
            "not_before":"1463406094",
            "resource":"https://management.azure.com/","access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWoNBVGZNNXBPWWlKSE1iYTlnb0VLWSIsImtpZCI6Ik1uQ19WWmNBVGZNNXBPWWlKSE1iYTlnb0VLWSJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tLyIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI2Ny8iLCJpYXQiOjE0NjM0MDYwOTQsIm5iZiI6MTQ2MzQwNjA5NCwiZXhwIjoxNDYzNDA5OTk5LCJhcHBpZCI6IjBlYzcyMzM0LTZkMDMtNDhmYi04OWU1LTU2NTJiODBiZDliYiIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJvaWQiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJzdWIiOiJlNjgxZTZiMi1mZThkLTRkZGUtYjZiMS0xNjAyZDQyNWQzOWYiLCJ0aWQiOiI3MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDciLCJ2ZXIiOiIxLjAifQ.nJVERbeDHLGHn7ZsbVGBJyHOu2PYhG5dji6F63gu8XN2Cvol3J1HO1uB4H3nCSt9DTu_jMHqAur_NNyobgNM21GojbEZAvd0I9NY0UDumBEvDZfMKneqp7a_cgAU7IYRcTPneSxbD6wo-8gIgfN9KDql98b0uEzixIVIWra2Q1bUUYETYqyaJNdS4RUmlJKNNpENllAyHQLv7hXnap1IuzP-f5CNIbbj9UgXxLiOtW5JhUAwWLZ3-WMhNRpUO2SIB7W7tQ0AbjXw3aUYr7el066J51z5tC1AK9UC-mD_fO_HUP6ZmPzu5gLA6DxkIIYP3grPnRVoUDltHQvwgONDOw"
        }

##<a name="create-a-resource-group"></a>Crear un grupo de recursos

Utilice la siguiente para crear un nuevo grupo de recursos. En primer lugar debe crear el grupo antes de crear los recursos, como el clúster HDInsight. 

* Reemplazar __SubscriptionID__ con el identificador de suscripción recibido al crear la entidad de seguridad del servicio.
* Reemplace __AccessToken__ con el token de acceso que se ha recibido en el paso anterior.
* Reemplace __DataCenterLocation__ con la que desea crear el grupo de recursos y recursos, en el centro de datos. Por ejemplo, 'Sur Central de EE'. 
* Reemplace __ResourceGroupName__ con el nombre que desea usar para este grupo:

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName?api-version=2015-01-01" \
    -H "Authorization: Bearer AccessToken" \
    -H "Content-Type: application/json" \
    -d $'{
"location": "DataCenterLocation"
}'
```

Si esta solicitud se realiza correctamente, recibirá una respuesta de la 200 serie y el cuerpo de la respuesta contendrá un documento JSON que contiene información acerca del grupo. La `"provisioningState"` elemento contendrá un valor de `"Succeeded"`.

##<a name="create-a-deployment"></a>Crear una implementación

Use estos procedimientos para implementar la configuración del clúster (plantilla y un parámetro de valores), al grupo de recursos.

* Reemplazar __SubscriptionID__ y __AccessToken__ con los valores utilizados anteriormente. 
* Reemplace __ResourceGroupName__ con el nombre del grupo de recursos que creó en la sección anterior.
* Reemplace __DeploymentName__ con el nombre que desea utilizar para esta implementación.

```
curl -X "PUT" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json" \
-d "{set your body string to the template and parameters}"
```

> [AZURE.NOTE] Si ha guardado el documento JSON que contiene la plantilla y parámetros a un archivo, puede usar los siguientes en lugar de `-d "{ template and parameters}"`:
>
> `--data-binary "@/path/to/file.json"`

Si esta solicitud se realiza correctamente, recibirá una respuesta de la 200 serie y el cuerpo de la respuesta contendrá un documento JSON que contiene información sobre la operación de implementación.

> [AZURE.IMPORTANT] Tenga en cuenta que la implementación se ha enviado, pero no se ha completado en este momento. Puede tardar varios minutos, normalmente unos 15 para la implementación completar.

##<a name="check-the-status-of-a-deployment"></a>Comprobar el estado de una implementación

Para comprobar el estado de la implementación, use la siguiente:

* Reemplazar __SubscriptionID__ y __AccessToken__ con los valores utilizados anteriormente. 
* Reemplace __ResourceGroupName__ con el nombre del grupo de recursos que creó en la sección anterior.

```
curl -X "GET" "https://management.azure.com/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.resources/deployments/DeploymentName?api-version=2015-01-01" \
-H "Authorization: Bearer AccessToken" \
-H "Content-Type: application/json"
```

Un documento JSON que contiene información sobre la operación de implementación devolverá información. La `"provisioningState"` elemento contendrá el estado de la implementación; Si contiene un valor de `"Succeeded"`, a continuación, la implementación se ha completado correctamente. En este momento, el clúster debe estar disponible para su uso.

##<a name="next-steps"></a>Pasos siguientes

Ahora que ha creado correctamente un clúster HDInsight, use los siguientes para obtener información sobre cómo trabajar con el clúster. 

###<a name="hadoop-clusters"></a>Clústeres Hadoop

* [Usar subárbol con HDInsight](hdinsight-use-hive.md)
* [Usar cerdo con HDInsight](hdinsight-use-pig.md)
* [Usar MapReduce con HDInsight](hdinsight-use-mapreduce.md)

###<a name="hbase-clusters"></a>Clústeres HBase

* [Introducción a HBase en HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desarrollar aplicaciones Java para HBase en HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###<a name="storm-clusters"></a>Clústeres de tormenta

* [Desarrollar topologías Java para tormenta en HDInsight](hdinsight-storm-develop-java-topology.md)
* [Utilizar componentes de Python en tormenta en HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implementar y supervisar topologías con tormenta en HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)
