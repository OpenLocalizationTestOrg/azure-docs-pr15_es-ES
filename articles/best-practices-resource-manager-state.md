<properties
    pageTitle="Estado de control en el Administrador de recursos plantillas | Microsoft Azure"
    description="Muestra recomendado enfoques para el uso de objetos complejos para compartir datos de estado con plantillas de administrador de recursos de Azure y vinculado"
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="tomfitz"/>

# <a name="sharing-state-in-azure-resource-manager-templates"></a>Compartir el estado en plantillas de administrador de recursos de Azure

Este tema muestra los procedimientos recomendados para administrar y compartir el estado de las plantillas. Los parámetros y variables que se muestra en este tema se muestran ejemplos del tipo de objetos puede definir cómodamente organizar sus requisitos de implementación. En estos ejemplos, puede implementar sus propios objetos con valores de propiedad pertinentes para su entorno.

Este tema es parte de un documento más grande. Para leer el documento completo, descargue [clase recursos Manager plantillas consideraciones y comprobada prácticas](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf).


## <a name="provide-standard-configuration-settings"></a>Proporcionar configuración estándar

En lugar de ofrecer una plantilla que proporciona total flexibilidad e innumerables variaciones, es un modelo común proporcionar una selección de configuraciones conocidas. De hecho, los usuarios puedan seleccionar tamaños estándar de la camiseta como espacio aislado, pequeño, mediano y grande. Otros ejemplos de tamaños de la camiseta son ofertas de productos, como Comunidad edition o enterprise edition. En otros casos, puede ser configuraciones específicas de carga de trabajo de una tecnología, como mapa de reducir o sin sql.

Con objetos complejos, puede crear las variables que contienen colecciones de datos, a veces se conoce como "los contenedores de propiedades" y utilizar esos datos para dirigir la declaración de recursos en la plantilla. Este enfoque proporciona configuraciones buenas conocidas de varios tamaños están preconfiguradas para clientes. Sin configuraciones conocidas, los usuarios de la plantilla deben determinar clúster de tamaño en su propia, tenga en cuenta las restricciones de recursos de plataforma y hacer cálculos para identificar las particiones resultante de cuentas de almacenamiento y otros recursos (debido a los límites de tamaño y recursos de clúster). Además de mejorar la experiencia del cliente, algunas configuraciones conocidas son más fáciles de soporte técnico y pueden ayudarle a ofrecer un nivel superior de la densidad.

En el ejemplo siguiente se muestra cómo definir las variables que contienen objetos complejos para representar conjuntos de datos. Las colecciones de definen valores que se usan para el tamaño de la máquina virtual, la configuración de red, la configuración del sistema operativo y la configuración de disponibilidad.

    "variables": {
      "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
      "tshirtSizeSmall": {
        "vmSize": "Standard_A1",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
        "vmCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 1,
          "pool": "db",
          "map": [0,0],
          "jumpbox": 0
        }
      },
      "tshirtSizeMedium": {
        "vmSize": "Standard_A3",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-8disk-resources.json')]",
        "vmCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 2,
          "pool": "db",
          "map": [0,1],
          "jumpbox": 0
        }
      },
      "tshirtSizeLarge": {
        "vmSize": "Standard_A4",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
        "vmCount": 3,
        "slaveCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 2,
          "pool": "db",
          "map": [0,1,1],
          "jumpbox": 0
        }
      },
      "osSettings": {
        "scripts": [
          "[concat(variables('templateBaseUrl'), 'install_postgresql.sh')]",
          "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
        ],
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "14.04.2-LTS",
          "version": "latest"
        }
      },
      "networkSettings": {
        "vnetName": "[parameters('virtualNetworkName')]",
        "addressPrefix": "10.0.0.0/16",
        "subnets": {
          "dmz": {
            "name": "dmz",
            "prefix": "10.0.0.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          },
          "data": {
            "name": "data",
            "prefix": "10.0.1.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          }
        }
      },
      "availabilitySetSettings": {
        "name": "pgsqlAvailabilitySet",
        "fdCount": 3,
        "udCount": 5
      }
    }

Observe que la variable **tshirtSize** concatena el tamaño de la camiseta proporcionado a través de un parámetro (**pequeño**, **mediano**, **grande**) para el texto **tshirtSize**. Utilice esta variable para recuperar la variable de objeto complejo asociado para ese tamaño camiseta.

A continuación, puede hacer referencia a estas variables más adelante en la plantilla. La capacidad de hacer referencia a variables denominado y sus propiedades simplifica la sintaxis de la plantilla y hace que sea fácil de entender el contexto. En el ejemplo siguiente se define un recurso para implementar mediante los objetos que se muestra anteriormente para establecer los valores. Por ejemplo, se establece el tamaño de la máquina virtual recuperando el valor de `variables('tshirtSize').vmSize` mientras que el valor para el tamaño del disco que se recuperen de `variables('tshirtSize').diskSize`. Además, se establece el URI de la plantilla vinculada con el valor de `variables('tshirtSize').vmTemplate`.

    "name": "master-node",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared')]"
    ],
    "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('tshirtSize').vmTemplate]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "value": "[parameters('adminPassword')]"
          },
          "replicatorPassword": {
            "value": "[parameters('replicatorPassword')]"
          },
          "osSettings": {
            "value": "[variables('osSettings')]"
          },
          "subnet": {
            "value": "[variables('networkSettings').subnets.data]"
          },
          "commonSettings": {
            "value": {
              "region": "[parameters('region')]",
              "adminUsername": "[parameters('adminUsername')]",
              "namespace": "ms"
            }
          },
          "storageSettings": {
            "value":"[variables('tshirtSize').storage]"
          },
          "machineSettings": {
            "value": {
              "vmSize": "[variables('tshirtSize').vmSize]",
              "diskSize": "[variables('tshirtSize').diskSize]",
              "vmCount": 1,
              "availabilitySet": "[variables('availabilitySetSettings').name]"
            }
          },
          "masterIpAddress": {
            "value": "0"
          },
          "dbType": {
            "value": "MASTER"
          }
        }
      }
    }

## <a name="pass-state-to-a-template"></a>Pasar de estado a una plantilla

Compartir el estado en una plantilla a través de los parámetros que desea proporcionar durante la implementación.

En la siguiente tabla se enumera los parámetros utilizados con frecuencia en las plantillas.

Nombre | Valor | Descripción
---- | ----- | -----------
ubicación    | Cadena de una lista de regiones Azure restringida   | La ubicación donde se implementan los recursos.
storageAccountNamePrefix    | Cadena    | Nombre DNS único para la cuenta de almacenamiento donde se colocan los discos de la máquina virtual
nombre de dominio  | Cadena    | Nombre de dominio de la disposición del público jumpbox VM en el formato: **{domainName}. {} ubicación}.cloudapp.com** por ejemplo: **mydomainname.westus.cloudapp.azure.com**
adminUsername   | Cadena    | Nombre de usuario para las máquinas virtuales
adminPassword   | Cadena    | Contraseña para las VM
tshirtSize  | Cadena de una lista de tamaños de la camiseta ofrecida restringida   | El tamaño de la unidad de escala con nombre aprovisionar. Por ejemplo, "Pequeña", "Medium", "Grande"
virtualNetworkName  | Cadena    | Nombre de la red virtual que el consumidor desea utilizar.
enableJumpbox   | Cadena de una lista restringida (habilitado o deshabilitado) | Parámetro que identifica si se habilita un jumpbox para el entorno. Valores: "habilitado", "deshabilitado"

El parámetro de **tshirtSize** utilizado en la sección anterior se define como:

    "parameters": {
      "tshirtSize": {
        "type": "string",
        "defaultValue": "Small",
        "allowedValues": [
          "Small",
          "Medium",
          "Large"
        ],
        "metadata": {
          "Description": "T-shirt size of the MongoDB deployment"
        }
      }
    }


## <a name="pass-state-to-linked-templates"></a>Pasar el estado a plantillas vinculadas

Cuando se conecte a plantillas vinculadas, a menudo usar una combinación de estática y genera variables.

### <a name="static-variables"></a>Variables estáticas

Las variables estáticas a menudo se usan para proporcionar los valores bases, como las direcciones URL que se usan en toda una plantilla.

En el siguiente fragmento de plantilla `templateBaseUrl` especifica la ubicación de la raíz de la plantilla en GitHub. La siguiente línea crea una nueva variable `sharedTemplateUrl` que concatena la dirección URL base con el nombre de la plantilla de recursos compartidos conocido. Debajo de esta línea se utiliza una variable de objeto complejo para almacenar un tamaño de la camiseta, donde se concatena a la ubicación de la plantilla de configuración conocido y se almacena en la dirección URL base del `vmTemplate` propiedad.

La ventaja de este enfoque es que si cambia la ubicación de la plantilla, solo debe cambiar la variable estática en un solo lugar, lo que pasa en las plantillas vinculadas.

    "variables": {
      "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
      "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
      "tshirtSizeSmall": {
        "vmSize": "Standard_A1",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
        "vmCount": 2,
        "slaveCount": 1,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 1,
          "pool": "db",
          "map": [0,0],
          "jumpbox": 0
        }
      }
    }

### <a name="generated-variables"></a>Variables generadas

Además de las variables estáticas, varias variables se generan dinámicamente. Esta sección identifican los tipos comunes de variables generadas.

#### <a name="tshirtsize"></a>tshirtSize

Está familiarizado con esta variable generada de los ejemplos anteriores.

#### <a name="networksettings"></a>networkSettings

En una capacidad, capacidad o plantilla de solución de ámbito de llevar a cabo, las plantillas vinculadas suele crean recursos que existen en una red. Un enfoque sencillo es usar un objeto complejo para almacenar la configuración de la red y pasarlos a plantillas vinculadas.

Un ejemplo de configuración de la red de comunicación puede verse a continuación.

    "networkSettings": {
      "vnetName": "[parameters('virtualNetworkName')]",
      "addressPrefix": "10.0.0.0/16",
      "subnets": {
        "dmz": {
          "name": "dmz",
          "prefix": "10.0.0.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        },
        "data": {
          "name": "data",
          "prefix": "10.0.1.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        }
      }
    }

#### <a name="availabilitysettings"></a>availabilitySettings

Recursos creados en plantillas vinculadas a menudo se colocan en un conjunto de disponibilidad. En el ejemplo siguiente, se especifica el nombre del conjunto de disponibilidad y los dominios de error y actualización contar usar.

    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

Si necesita varios conjuntos de disponibilidad (por ejemplo, uno para nodos maestros) y otro para los nodos de datos, puede usar un nombre como un prefijo, especificar varios conjuntos de disponibilidad, o siga el modelo mostrado anteriormente para crear una variable de un determinado tamaño de la camiseta.

#### <a name="storagesettings"></a>storageSettings

Detalles de almacenamiento a menudo están compartidos con plantillas vinculadas. En el ejemplo siguiente, un objeto de *storageSettings* proporciona detalles sobre los nombres de cuenta y contenedor de almacenamiento.

    "storageSettings": {
        "vhdStorageAccountName": "[parameters('storageAccountName')]",
        "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
        "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
    }

#### <a name="ossettings"></a>osSettings

Con las plantillas vinculadas, debe pasar configuración del sistema operativo a distintos tipos de nodos en tipos diferentes de configuración conocido. Un objeto complejo es una forma sencilla para almacenar y compartir información del sistema operativo y también facilita a varias opciones de sistema operativo de soporte técnico para su implementación.

En el ejemplo siguiente se muestra un objeto de *osSettings*:

    "osSettings": {
      "imageReference": {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "14.04.2-LTS",
        "version": "latest"
      }
    }

#### <a name="machinesettings"></a>machineSettings

Una variable generada, *machineSettings* es un objeto complejo que contiene una combinación de variables principales para crear una máquina virtual. Las variables incluyen el nombre de usuario administrador y la contraseña, un prefijo para los nombres de máquina virtual y a continuación, la referencia de una imagen de sistema operativo.

    "machineSettings": {
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "machineNamePrefix": "mongodb-",
        "osImageReference": {
            "publisher": "[variables('osFamilySpec').imagePublisher]",
            "offer": "[variables('osFamilySpec').imageOffer]",
            "sku": "[variables('osFamilySpec').imageSKU]",
            "version": "latest"
        }
    },

Tenga en cuenta que *osImageReference* recupera los valores de la variable *osSettings* definida en la plantilla principal. Esto significa que puede cambiar fácilmente el sistema operativo para una máquina virtual: por completo o en función de la preferencia del consumidor de plantilla.

#### <a name="vmscripts"></a>vmScripts

El objeto *vmScripts* contiene detalles sobre las secuencias de comandos para descargar y ejecutar en una instancia VM, incluidos fuera y dentro de las referencias. Fuera de las referencias incluyen la infraestructura. Las referencias de interior incluyen el software instalado instalado y la configuración.

Use la propiedad *scriptsToDownload* para las secuencias de comandos para descargar en la máquina virtual de la lista. Este objeto también contiene referencias a argumentos de línea de comandos para diferentes tipos de acciones. Estas acciones incluyen la ejecución de la instalación predeterminada para cada nodo individual, una instalación que se ejecuta después de que se implementan todos los nodos y las secuencias de comandos adicionales que pueden ser específicas de una plantilla determinada.

En este ejemplo es desde la plantilla utilizada para implementar MongoDB, que requiere un árbitro ofrecer alta disponibilidad. Se ha agregado el *arbiterNodeInstallCommand* a *vmScripts* para instalar a la autoridad decide.

La sección de variables es donde encontrará las variables que definen un texto específico para ejecutar la secuencia de comandos con los valores correctos.

    "vmScripts": {
        "scriptsToDownload": [
            "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
            "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
        ],
        "regularNodeInstallCommand": "[variables('installCommand')]",
        "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
        "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
    },


## <a name="return-state-from-a-template"></a>Devolver el estado de una plantilla

Pasar datos en una plantilla, también puede compartir los datos a la plantilla de la llamada. En la sección de **resultados** de una plantilla vinculada, puede proporcionar los pares de clave/valor que se pueden usar la plantilla de origen.

En el ejemplo siguiente se muestra cómo pasar la dirección IP privada generada en una plantilla vinculada.

    "outputs": {
        "masterip": {
            "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].properties.privateIPAddress]",
            "type": "string"
         }
    }

Dentro de la plantilla principal, puede usar esos datos con la siguiente sintaxis:

    "[reference('master-node').outputs.masterip.value]"

Puede usar esta expresión en la sección de resultados o en la sección de recursos de la plantilla principal. No puede usar la expresión en la sección de variables porque se basa en el estado de tiempo de ejecución. Para obtener este valor desde la plantilla principal, use:

    "outputs": { 
      "masterIpAddress": {
        "value": "[reference('master-node').outputs.masterip.value]",
        "type": "string"
      }
     
Para obtener un ejemplo de uso de la sección de resultados de una plantilla vinculada para devolver los discos de datos para una máquina virtual, vea [crear varios discos de datos para una máquina Virtual](resource-group-create-multiple.md#creating-multiple-data-disks-for-a-virtual-machine).

## <a name="define-authentication-settings-for-virtual-machine"></a>Definir la configuración de autenticación de máquina virtual

Puede utilizar el mismo patrón mostrado anteriormente para la configuración para especificar la configuración de autenticación para una máquina virtual. Crear un parámetro para pasar en el tipo de autenticación.

    "parameters": {
      "authenticationType": {
        "allowedValues": [
          "password",
          "sshPublicKey"
        ],
        "defaultValue": "password",
        "metadata": {
          "description": "Authentication type"
        },
        "type": "string"
      }
    }

Agregar variables para los diferentes tipos de autenticación y se utiliza una variable para almacenar el tipo de esta implementación en función del valor del parámetro.

    "variables": {
      "osProfile": "[variables(concat('osProfile', parameters('authenticationType')))]",
      "osProfilepassword": {
        "adminPassword": "[parameters('adminPassword')]",
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]"
      },
      "osProfilesshPublicKey": {
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]",
        "linuxConfiguration": {
          "disablePasswordAuthentication": "true",
          "ssh": {
            "publicKeys": [
              {
                "keyData": "[parameters('sshPublicKey')]",
                "path": "/home/notused/.ssh/authorized_keys"
              }
            ]
          }
        }
      }
    }

Al definir la máquina virtual, establezca la **osProfile** a la variable que creó.

    {
      "type": "Microsoft.Compute/virtualMachines",
      ...
      "osProfile": "[variables('osProfile')]"
    }


## <a name="next-steps"></a>Pasos siguientes
- Para obtener información sobre las secciones de la plantilla, vea [Crear plantillas de administrador de recursos de Azure](resource-group-authoring-templates.md)
- Para ver las funciones que están disponibles en una plantilla, vea [Funciones de plantilla de administrador de recursos de Azure](resource-group-template-functions.md)

