<properties 
    pageTitle="Administrar cuentas de servicios multimedia de Azure con PowerShell" 
    description="Obtenga información sobre cómo administrar cuentas de Azure Media Services con los cmdlets de PowerShell." 
    authors="Juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016"
    ms.author="juliako"/>


#<a name="manage-azure-media-services-accounts-with-powershell"></a>Administrar cuentas de servicios multimedia de Azure con PowerShell

> [AZURE.SELECTOR]
- [Portal](media-services-portal-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [REST](http://msdn.microsoft.com/library/azure/dn194267.aspx)

> [AZURE.NOTE] Para poder crear una cuenta de Azure Media Services, debe tener una cuenta de Azure. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Prueba gratuita de Azure</a>.

##<a name="overview"></a>Información general 

Este artículo enumeran los cmdlets de PowerShell de Azure para Azure Media Services (AMS) en el marco del Administrador de recursos de Azure. Los cmdlets existe en el espacio de nombres **Microsoft.Azure.Commands.Media** .

## <a name="versions"></a>Versiones

**ApiVersion**: "10 / 01/2015"
               

## <a name="new-azurermmediaservice"></a>Nueva AzureRmMediaService

Crea un servicio de medios.

### <a name="syntax"></a>Sintaxis

Conjunto de parámetros: StorageAccountIdParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccountId] <string> [-Tags <hashtable>]  [<CommonParameters>]

Conjunto de parámetros: StorageAccountsParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccounts] <PSStorageAccount[]> [-Tags <hashtable>]  [<CommonParameters>]

### <a name="parameters"></a>Parámetros

**-ResourceGroupName &lt;cadena&gt;**

Especifica el nombre del grupo de recursos a la que pertenece este servicio de medios.

Alias | Ninguno
---|---
¿Obligatorio?   |  Verdadero
¿Posición?   |  0
Valor predeterminado |Ninguno
¿Aceptar entradas de canalización? |True(ByPropertyName)
¿Aceptar caracteres comodín?  |falso

**-Nombre de la cuenta &lt;cadena&gt;**

Especifica el nombre del servicio de medios.

Alias |Nombre
---|---
¿Obligatorio? |Verdadero
¿Posición? |1
Valor predeterminado |Ninguno
¿Aceptar entradas de canalización? |falso
¿Aceptar caracteres comodín? |falso

**-Ubicación &lt;cadena&gt;**

Especifica la ubicación del recurso del servicio de medios.

Alias |Ninguno
---|---
¿Obligatorio? |Verdadero
¿Posición? |2
Valor predeterminado  |Ninguno
¿Aceptar entradas de canalización? |True(ByPropertyName)
¿Aceptar caracteres comodín? |falso

**-StorageAccountId &lt;cadena&gt;**

Especifica una cuenta de almacenamiento principal asociado con el servicio de medios.

- Nueva cuenta de almacenamiento (creado con la API del Administrador de recursos) solo admitido.

- La cuenta de almacenamiento debe existir y tiene la misma ubicación con el servicio de medios.

Alias |Ninguno
---|---
¿Obligatorio? |Verdadero
¿Posición? |3
Valor predeterminado  |Ninguno
¿Aceptar entradas de canalización? |True(ByPropertyName)
Nombre del conjunto de parámetros |StorageAccountIdParamSet
¿Aceptar caracteres comodín?|falso

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

Especifica las cuentas de almacenamiento asociado con el servicio de medios.

- Nueva cuenta de almacenamiento (creado con la API del Administrador de recursos) solo admitido.

- La cuenta de almacenamiento debe existir y tiene la misma ubicación con el servicio de medios.

- Solo una cuenta de almacenamiento puede especificarse como principal.

Alias |Ninguno
---|---
¿Obligatorio?  |Verdadero
¿Posición?  |3
Valor predeterminado |Ninguno
¿Aceptar entradas de canalización? |True(ByPropertyName)
Nombre del conjunto de parámetros |StorageAccountsParamSet
¿Aceptar caracteres comodín? |falso

**-Etiquetas &lt;tabla hash&gt;**

Especifica una tabla de hash de las etiquetas que están asociados con el servicio de medios.

- Ejemplo:@{"tag1"="value1";"tag2"=:value2"}

Alias |Ninguno
---|---
¿Obligatorio?  |falso
¿Posición?  |con el nombre
Valor predeterminado |Ninguno
¿Aceptar entradas de canalización? |falso
¿Aceptar caracteres comodín? |falso

**&lt;ParámetrosDeComando&gt;**

Este cmdlet admite los parámetros comunes:-depurar - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - detallado, - WarningAction y - WarningVariable.

### <a name="inputs"></a>Entradas

El tipo de entrada es el tipo de los objetos que puede canalización al cmdlet.

### <a name="outputs"></a>Resultados

El tipo de resultado es el tipo de los objetos que emite el cmdlet.

## <a name="set-azurermmediaservice"></a>Establecer AzureRmMediaService

Actualiza un servicio de medios.

### <a name="syntax"></a>Sintaxis

    Set-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Tags <hashtable>] [-StorageAccounts <PSStorageAccount[]>]  [<CommonParameters>]

### <a name="parameters"></a>Parámetros

**-ResourceGroupName &lt;cadena&gt;**

Especifica el nombre del grupo de recursos a la que pertenece este servicio de medios.

Alias |Ninguno
---|---
¿Obligatorio?  |Verdadero
¿Posición?  |0
Valor predeterminado |Ninguno
¿Aceptar entradas de canalización? |True(ByPropertyName)
¿Aceptar caracteres comodín? |falso

**-Nombre de la cuenta &lt;cadena&gt;**

Especifica el nombre del servicio de medios.

Alias |Nombre
---|---
¿Obligatorio? |True
¿Posición? |1
Valor predeterminado |Ninguno
¿Aceptar entradas de canalización? |True(ByPropertyName)
¿Aceptar caracteres comodín? |Falso

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

Especifica las cuentas de almacenamiento asociado con el servicio de medios.

- Nueva cuenta de almacenamiento (creado con la API del Administrador de recursos) solo admitido.

- La cuenta de almacenamiento debe existir y tiene la misma ubicación con el servicio de medios.

- Solo una cuenta de almacenamiento puede especificarse como principal.

Alias |Ninguno
---|---
¿Obligatorio? |falso
¿Posición? |Con el nombre
Valor predeterminado |Ninguno
¿Aceptar entradas de canalización? |True(ByPropertyName)
Nombre del conjunto de parámetros |StorageAccountsParamSet
¿Aceptar caracteres comodín? |falso

**-Etiquetas &lt;tabla hash&gt;**

Especifica una tabla de hash de las etiquetas que están asociadas a este servicio de medios.

- Las etiquetas que están asociadas con el servicio de medios se reemplazan por el valor especificado por el cliente.

Alias |Ninguno
---|---
¿Obligatorio? |Falso
¿Posición?  |Con el nombre
Valor predeterminado |Ninguno
¿Aceptar entradas de canalización? |True(ByPropertyName)
¿Aceptar caracteres comodín? |falso

**&lt;ParámetrosDeComando&gt;**

Este cmdlet admite los parámetros comunes:-depurar - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - detallado, - WarningAction y - WarningVariable.

### <a name="inputs"></a>Entradas

El tipo de entrada es el tipo de los objetos que puede canalización al cmdlet.

### <a name="outputs"></a>Resultados

El tipo de resultado es el tipo de los objetos que emite el cmdlet.

## <a name="remove-azurermmediaservice"></a>Quitar AzureRmMediaService

Quita un servicio de medios.

### <a name="syntax"></a>Sintaxis

    Remove-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parámetros

**-ResourceGroupName &lt;cadena&gt;**

Especifica el nombre del grupo de recursos a la que pertenece este servicio de medios.

Alias |Ninguno
---|---
¿Obligatorio? |Verdadero
¿Posición? |0
Valor predeterminado |Ninguno
¿Aceptar entradas de canalización? |True(ByPropertyName)
¿Aceptar caracteres comodín? |falso

**-Nombre de la cuenta &lt;cadena&gt;**

Especifica el nombre del servicio de medios.

Alias |Ninguno
---|---
¿Obligatorio? |Verdadero
¿Posición? |2
Valor predeterminado |Ninguno
¿Aceptar entradas de canalización?  |True(ByPropertyName)
¿Aceptar caracteres comodín? |Falso

**&lt;ParámetrosDeComando&gt;**

Este cmdlet admite los parámetros comunes:-depurar - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - detallado, - WarningAction y - WarningVariable.

### <a name="inputs"></a>Entradas

El tipo de entrada es el tipo de los objetos que puede canalización al cmdlet.

### <a name="outputs"></a>Resultados

El tipo de resultado es el tipo de los objetos que emite el cmdlet.

## <a name="get-azurermmediaservice"></a>Get-AzureRmMediaService

Obtiene todos los servicios de medios en un grupo de recursos o un servicio de medios con un nombre dado.

### <a name="syntax"></a>Sintaxis

ParameterSet: ResourceGroupParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string>  [<CommonParameters>] 

ParameterSet: AccountNameParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parámetros

**-ResourceGroupName &lt;cadena&gt;**

Especifica el nombre del grupo de recursos a la que pertenece este servicio de medios.

Alias |Ninguno
---|---
¿Obligatorio? |Verdadero
¿Posición?  |0
Valor predeterminado |Ninguno
¿Aceptar entradas de canalización? |True(ByPropertyName)
Nombre del conjunto de parámetros |ResourceGroupParameterSet, AccountNameParameterSet
¿Aceptar caracteres comodín?   falso

**-Nombre de la cuenta &lt;cadena&gt;**

Especifica el nombre del servicio de medios.

Alias |Ninguno
---|---
¿Obligatorio? |Verdadero
¿Posición?  |1
Valor predeterminado |Ninguno
¿Aceptar entradas de canalización? |True(ByPropertyName)
Nombre del conjunto de parámetros  |AccountNameParameterSet
¿Aceptar caracteres comodín? |falso

**&lt;ParámetrosDeComando&gt;**

Este cmdlet admite los parámetros comunes:-depurar - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - detallado, - WarningAction y - WarningVariable.

### <a name="inputs"></a>Entradas

El tipo de entrada es el tipo de los objetos que puede canalización al cmdlet.

### <a name="outputs"></a>Resultados

El tipo de resultado es el tipo de los objetos que emite el cmdlet.

## <a name="get-azurermmediaservicekeys"></a>Get-AzureRmMediaServiceKeys

Obtiene las claves de un servicio de medios.

### <a name="syntax"></a>Sintaxis

    Get-AzureRmMediaServiceKeys [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parámetros

**-ResourceGroupName &lt;cadena&gt;**

Especifica el nombre del grupo de recursos a la que pertenece este servicio de medios.

Alias |Ninguno
---|---
¿Obligatorio? |Verdadero
¿Posición?  |0
Valor predeterminado |Ninguno
¿Aceptar entradas de canalización? |True(ByPropertyName)
¿Aceptar caracteres comodín? |falso

**-Nombre de la cuenta &lt;cadena&gt;**

Especifica el nombre del servicio de medios.

Alias |Ninguno
---|---
¿Obligatorio? |Verdadero
¿Posición? |1
Valor predeterminado |Ninguno
¿Aceptar entradas de canalización? |True(ByPropertyName)
¿Aceptar caracteres comodín? |falso

**&lt;ParámetrosDeComando&gt;**

Este cmdlet admite los parámetros comunes:-depurar - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - detallado, - WarningAction y - WarningVariable.

### <a name="inputs"></a>Entradas

El tipo de entrada es el tipo de los objetos que puede canalización al cmdlet.

### <a name="outputs"></a>Resultados

El tipo de resultado es el tipo de los objetos que emite el cmdlet.

## <a name="set-azurermmediaservicekey"></a>Establecer AzureRmMediaServiceKey

Vuelve a crear una clave principal o secundaria de un servicio de medios.

### <a name="syntax"></a>Sintaxis

    Set-AzureRmMediaServiceKey [-ResourceGroupName] <string> [-AccountName] <string> [-KeyType] <KeyType> {Primary | Secondary}  [<CommonParameters>]

### <a name="parameters"></a>Parámetros

**-ResourceGroupName &lt;cadena&gt;**

Especifica el nombre del grupo de recursos a la que pertenece este servicio de medios.

Alias |Ninguno
---|---
¿Obligatorio?  |Verdadero
¿Posición?  |0
Valor predeterminado |Ninguno
¿Aceptar entradas de canalización?  |True(ByPropertyName)
¿Aceptar caracteres comodín? |falso

**-Nombre de la cuenta &lt;cadena&gt;**

Especifica el nombre del servicio de medios.

Alias |Ninguno
---|---
¿Obligatorio? |Verdadero
¿Posición?  |1
Valor predeterminado |Ninguno
¿Aceptar entradas de canalización?   |True(ByPropertyName)
¿Aceptar caracteres comodín? |falso

**Tipo de clave - &lt;tipo de clave&gt;**

Especifica el tipo de clave del servicio de medios.

- Primaria o secundaria

Alias |Ninguno
---|---
¿Obligatorio?  |Verdadero
¿Posición?  |2
Valor predeterminado |Ninguno
¿Aceptar entradas de canalización? |falso
¿Aceptar caracteres comodín? |falso

**&lt;ParámetrosDeComando&gt;**

Este cmdlet admite los parámetros comunes:-depurar - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - detallado, - WarningAction y - WarningVariable.

### <a name="inputs"></a>Entradas

El tipo de entrada es el tipo de los objetos que puede canalización al cmdlet.

### <a name="outputs"></a>Resultados

El tipo de resultado es el tipo de los objetos que emite el cmdlet.

## <a name="sync-azurermmediaservicestoragekeys"></a>Sincronización AzureRmMediaServiceStorageKeys

Sincroniza las claves de la cuenta de almacenamiento para una cuenta de almacenamiento asociado con el servicio de medios.

### <a name="syntax"></a>Sintaxis

    Sync-AzureRmMediaServiceStorageKeys [-ResourceGroupName] <string> [-MediaServiceAccountName] <string>    [-StorageAccountId] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parámetros

**-ResourceGroupName &lt;cadena&gt;**

Especifica el nombre del grupo de recursos a la que pertenece este servicio de medios.

Alias |Ninguno
---|---
¿Obligatorio? |Verdadero
¿Posición? |0
Valor predeterminado |Ninguno
¿Aceptar entradas de canalización? |True(ByPropertyName)
¿Aceptar caracteres comodín? |falso

**-Nombre de la cuenta &lt;cadena&gt;**

Especifica el nombre del servicio de medios.

Alias |Ninguno
---|---
¿Obligatorio? |Verdadero
¿Posición? |1
Valor predeterminado |Ninguno
¿Aceptar entradas de canalización? |True(ByPropertyName)
¿Aceptar caracteres comodín? |falso

**-StorageAccountId &lt;cadena&gt;**

Especifica la cuenta de almacenamiento asociada con el servicio de medios.

Alias |Id.
---|---
¿Obligatorio? |Verdadero
¿Posición?  |2
Valor predeterminado |Ninguno
¿Aceptar entradas de canalización? |      True(ByPropertyName)
¿Aceptar caracteres comodín? |falso

**&lt;ParámetrosDeComando&gt;**

Este cmdlet admite los parámetros comunes:-depurar - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - detallado, - WarningAction y - WarningVariable.

### <a name="inputs"></a>Entradas

El tipo de entrada es el tipo de los objetos que puede canalización al cmdlet.

### <a name="outputs"></a>Resultados

El tipo de resultado es el tipo de los objetos que emite el cmdlet.

## <a name="next-step"></a>Siguiente paso 

Consulte Media Services rutas de aprendizaje.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Proporcionar comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
