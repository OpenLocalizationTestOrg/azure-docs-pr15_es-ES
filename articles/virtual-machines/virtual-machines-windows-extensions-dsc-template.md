<properties
   pageTitle="Desea la plantilla de administrador de recursos de configuración de estado | Microsoft Azure"
   description="Definición de la plantilla de administrador de recursos para la configuración de estado deseado en Azure con ejemplos y solución de problemas"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# <a name="windows-vmss-and-desired-state-configuration-with-azure-resource-manager-templates"></a>VMSS de Windows y configuración de estado deseado con las plantillas de administrador de recursos de Azure
En este artículo se describe la plantilla de administrador de recursos para el [controlador de extensión de configuración de estado deseado](virtual-machines-windows-extensions-dsc-overview.md). 

## <a name="template-example-for-a-windows-vm"></a>Ejemplo de plantilla para una máquina virtual de Windows

El fragmento de código siguiente se pasa a la sección de recursos de la plantilla.

```json
            "name": "Microsoft.Powershell.DSC",
            "type": "extensions",
             "location": "[resourceGroup().location]",
             "apiVersion": "2015-06-15",
             "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": true,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "dscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }

```

## <a name="template-example-for-windows-vmss"></a>Ejemplo de plantilla para Windows VMSS

Un nodo VMSS tiene una sección de "propiedades de" con el "VirtualMachineProfile", "extensionProfile" atributo. DSC se agrega en "extensiones". 

```json
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": true,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="detailed-settings-information"></a>Información de configuración detallada

El esquema siguiente es para la parte de la configuración de la extensión de Azure DSC en una plantilla de administrador de recursos de Azure.

```json

"settings": {
  "wmfVersion": "latest",
  "configuration": {
    "url": "http://validURLToConfigLocation",
    "script": "ConfigurationScript.ps1",
    "function": "ConfigurationFunction"
  },
  "configurationArguments": {
    "argument1": "Value1",
    "argument2": "Value2"
  },
  "configurationData": {
    "url": "https://foo.psd1"
  },
  "privacy": {
    "dataCollection": "enable"
  },
  "advancedOptions": {
    "downloadMappings": {
      "customWmfLocation": "http://myWMFlocation"
    }
  } 
},
"protectedSettings": {
  "configurationArguments": {
    "parameterOfTypePSCredential1": {
      "userName": "UsernameValue1",
      "password": "PasswordValue1"
    },
    "parameterOfTypePSCredential2": {
      "userName": "UsernameValue2",
      "password": "PasswordValue2"
    }
  },
  "configurationUrlSasToken": "?g!bber1sht0k3n",
  "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}

```

## <a name="details"></a>Detalles
| Nombre de propiedad | Tipo | Descripción |
| --- | --- | --- |
| settings.wmfVersion | cadena | Especifica la versión de Windows Management Framework que debe instalarse en la máquina virtual. Establecer esta propiedad en 'última' instala la versión más actualizada de WMF. Los valores posibles solo actuales para esta propiedad son **'4.0', '5.0', ' 5.0PP' y 'último'**. Estos valores posibles están sujetos a actualizaciones. El valor predeterminado es 'último'.|
| Settings.Configuration.URL | cadena | Especifica la ubicación de la dirección URL desde la que se va a descargar el archivo zip de configuración de DLC. Si la dirección URL proporcionada requiere un token de SA para access, debe establecer la propiedad protectedSettings.configurationUrlSasToken al valor del token de su SA. Esta propiedad es necesaria si se han definido settings.configuration.script o settings.configuration.function. |
| Settings.Configuration.Script | cadena | Especifica el nombre de archivo de la secuencia de comandos que contiene la definición de la configuración de DSC. Esta secuencia de comandos debe estar en la carpeta raíz del archivo zip descargado desde la dirección URL especificada por la propiedad configuration.url. Esta propiedad es necesaria si se han definido settings.configuration.url o settings.configuration.script. |
| Settings.Configuration.Function | cadena | Especifica el nombre de la configuración de DSC. La configuración de denominada debe incluirse en la secuencia de comandos definida por configuration.script. Esta propiedad es necesaria si se han definido settings.configuration.url o settings.configuration.function. |
| settings.configurationArguments | Colección | Define los parámetros que desea pasar a la configuración de DSC. Esta propiedad no está cifrada. |
| settings.configurationData.url | cadena | Especifica la dirección URL de descarga del archivo de datos (.pds1) de la configuración para usar como entrada para la configuración de DSC. Si la dirección URL proporcionada requiere un token de SA para access, debe establecer la propiedad protectedSettings.configurationDataUrlSasToken al valor del token de su SA.|
| settings.privacy.dataEnabled | cadena | Habilita o deshabilita la colección de telemetría. Los valores posibles solo para esta propiedad son **'Activar', 'Deshabilitar' ", o $null**. Salir de esta propiedad en blanco o null permite telemetría. El valor predeterminado es ". [Más información](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/) |
| settings.advancedOptions.downloadMappings | Colección | Define ubicaciones alternativas de descarga WMF. [Más información](http://blogs.msdn.com/b/powershell/archive/2015/10/21/azure-dsc-extension-2-2-amp-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location.aspx) |
| protectedSettings.configurationArguments | Colección | Define los parámetros que desea pasar a la configuración de DSC. Esta propiedad está cifrada. |
| protectedSettings.configurationUrlSasToken | cadena | Especifica el token de SA para tener acceso a la dirección URL definida por configuration.url. Esta propiedad está cifrada. |
| protectedSettings.configurationDataUrlSasToken | cadena | Especifica el token de SA para tener acceso a la dirección URL definida por configurationData.url. Esta propiedad está cifrada. |

## <a name="settings-vs-protectedsettings"></a>Configuración frente a ProtectedSettings
Todos los valores se guardan en un archivo de texto de configuración de la máquina virtual.
Propiedades bajo 'configuración' son propiedades públicas porque no están cifrados en el archivo de configuración de texto.
Propiedades de 'protectedSettings' se cifran con un certificado y no se muestran como texto sin formato de este archivo en la máquina virtual.

Si la configuración necesita credenciales, que pueden incluirse en protectedSettings:

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
            "userName": "UsernameValue1",
            "password": "PasswordValue1"
        }
    }
}
```

## <a name="example"></a>Ejemplo

En el ejemplo siguiente se deriva de la sección "Introducción" de la [página de información general sobre el controlador de extensión DSC](virtual-machines-windows-extensions-dsc-overview.md).
Este ejemplo usa plantillas de administrador de recursos en lugar de los cmdlets para implementar la extensión. Guardar la configuración de "IisInstall.ps1", colocarla en una. Un archivo COMPRIMIDO y cargue el archivo en una dirección URL accesible. Este ejemplo usa almacenamiento de blobs de Windows Azure, pero es posible descargar. COMPRIMIR archivos desde cualquier ubicación.

En la plantilla de administrador de recursos de Azure, el siguiente código indica que la máquina virtual para descargar el archivo correcto y ejecutar la función adecuada de PowerShell:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
    } 
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="updating-from-the-previous-format"></a>Actualizar desde el formato anterior
Cualquier configuración en el formato anterior (que contiene las propiedades públicas ModulesUrl, ConfigurationFunction, SasToken o propiedades) automáticamente adapta al formato actual y ejecuta exactamente igual que antes.

El siguiente esquema es lo que el anterior configuración tenía el siguiente aspecto:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties":  {
        "ParameterToConfigurationFunction1":  "Value1",
        "ParameterToConfigurationFunction2":  "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1" 
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": { 
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Le mostramos cómo se adapta el formato anterior al formato actual:

| Nombre de propiedad | Equivalente de esquema anterior |
| --- | --- |
| settings.wmfVersion | Configuración. WMFVersion |
| Settings.Configuration.URL | Configuración. ModulesUrl |
| Settings.Configuration.Script | Primera parte de la configuración. ConfigurationFunction (antes '\\\\') |
| Settings.Configuration.Function | Segunda parte de la configuración. ConfigurationFunction (después de '\\\\') |
| settings.configurationArguments | Configuración. Propiedades |
| settings.configurationData.url | protectedSettings.DataBlobUri (sin token SA) |
| settings.privacy.dataEnabled | Configuración. Privacy.DataEnabled |
| settings.advancedOptions.downloadMappings | Configuración. AdvancedOptions.DownloadMappings |
| protectedSettings.configurationArguments | protectedSettings.Properties |
| protectedSettings.configurationUrlSasToken | Configuración. SasToken |
| protectedSettings.configurationDataUrlSasToken | Token de SA desde protectedSettings.DataBlobUri |


## <a name="troubleshooting---error-code-1100"></a>Solución de problemas - código de Error 1100
Código de error 1100 indica que hay un problema con la entrada del usuario a la extensión DSC.
El texto de estos errores es variable y puede cambiar.
Estas son algunas de los errores que puede surgir y cómo corregirlos.

### <a name="invalid-values"></a>Valores no válidos
"Privacy.dataCollection es '{0}'. Los valores posibles solo son ", 'Habilitar' y 'Desactivar'" "WmfVersion es '{0}'. Solo los valores posibles son... y 'última' "

Problema: No se permite un valor proporcionado.

Solución: Cambie el valor no válido a un valor válido. Vea la tabla en la sección de detalles.

### <a name="invalid-url"></a>Dirección URL no válida
"ConfigurationData.url es '{0}'. No es una dirección URL válida""DataBlobUri es '{0}'. No es una dirección URL válida""Configuration.url es '{0}'. No es una dirección URL válida"

Problema: A proporcionado que URL no es válida.

Solución: Compruebe todas las direcciones URL proporcionadas. Asegúrese de que todas las direcciones URL resuelven a ubicaciones válidas que puede tener acceso la extensión en el equipo remoto.

### <a name="invalid-configurationargument-type"></a>Tipo de ConfigurationArgument no válido
"Tipo de configurationArguments no válidos {0}"

Problema: No se puede resolver la propiedad ConfigurationArguments a un objeto de la tabla hash. 

Solución: Asegúrese de su propiedad ConfigurationArguments una tabla hash. Siga el formato que se proporcionan en el ejemplo anterior. Vigile los presupuestos, comas y llaves.

### <a name="duplicate-configurationarguments"></a>Duplicar ConfigurationArguments
"No se encontró argumentos duplicados '{0}' en configurationArguments públicos y protegidos"

Problema: El ConfigurationArguments en configuración del público y la ConfigurationArguments en la configuración protegida contienen propiedades con el mismo nombre.

Solución: Quite una de las propiedades duplicadas.

### <a name="missing-properties"></a>Faltan propiedades
"Configuration.function requiere que se especifique configuration.url o configuration.module"

"Configuration.url requiere que configuration.script se especifica"

"Configuration.script requiere que configuration.url se especifica"

"Configuration.url requiere que configuration.function se especifica"

"ConfigurationUrlSasToken requiere que configuration.url se especifica"

"ConfigurationDataUrlSasToken requiere que configurationData.url se especifica"

Problema: Una propiedad definida necesita otra propiedad que falta.

Soluciones: 
- Proporcionar la propiedad que falta.
- Quitar la propiedad que necesita la propiedad que falta.


## <a name="next-steps"></a>Pasos siguientes
Obtener información acerca de los conjuntos de escala DSC y máquina virtual de [Utilizar escala conjuntos de máquina Virtual con la extensión de DSC de Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md)

Encontrar más detalles acerca de la [administración de segura de credenciales de DSC](virtual-machines-windows-extensions-dsc-credentials.md). 

Para obtener más información sobre el controlador de extensión DSC de Azure, vea [Introducción al controlador de extensión de configuración de estado de Azure deseado](virtual-machines-windows-extensions-dsc-overview.md). 

Para obtener más información acerca de PowerShell DSC, [visite el centro de documentación de PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 
