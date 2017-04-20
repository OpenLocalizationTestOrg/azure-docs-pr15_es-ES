<properties
    pageTitle="Conectarse a Azure pila con CLI | Microsoft Azure"
    description="Aprenda a usar la interfaz de línea de comandos de varias plataformas (CLI) para administrar e implementar recursos en pila de Azure"
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="helaw"/>

# <a name="install-and-configure-azure-stack-cli"></a>Instalar y configurar CLI de pila de Azure

En este documento, le guiará a través del proceso de uso de Azure interfaz de línea de comandos (CLI) para administrar los recursos de la pila de Azure en Linux y Mac plataformas de cliente.  

## <a name="install-azure-stack-cli"></a>Instalar la pila de Azure CLI

Si se encuentra en Mac o Linux, puede obtener CLI mediante el comando siguiente:
  
    `npm install -g azure-cli@0.10.4`.


## <a name="connect-to-azure-stack"></a>Conectarse a la pila de Azure
En los pasos siguientes, debe configurar Azure CLI para conectarse a la pila de Azure. A continuación, inicie sesión y recuperar información de la suscripción.

1.  Recuperar el valor de active directory recursos id ejecutando esta PowerShell:
        
          (Invoke-RestMethod -Uri https://api.azurestack.local/metadata/endpoints?api-version=1.0 -Method Get).authentication.audiences[0]

2.  Use el siguiente comando CLI para agregar el entorno de pila de Azure, asegúrese de actualizar *: active directory recursos id* con los datos se recupera la dirección URL en el paso anterior:

           azure account env add AzureStack --resource-manager-endpoint-url "https://api.azurestack.local" --management-endpoint-url "https://api.azurestack.local" --active-directory-endpoint-url  "https://login.windows.net" --portal-url "https://portal.azurestack.local" --gallery-endpoint-url "https://portal.azurestack.local" --active-directory-resource-id "https://azurestack.local-api/" --active-directory-graph-resource-id "https://graph.windows.net/"

3.  Iniciar sesión usando el comando siguiente ( *nombre de usuario* de reemplazar con el nombre de usuario):

        azure login -e AzureStack -u “<username>”

    >[AZURE.NOTE]Si recibe los problemas de validación de certificado, deshabilitar la validación de certificado, ejecute el comando `set        NODE_TLS_REJECT_UNAUTHORIZED=0`.

4.  Establecer el modo de configuración de Azure para el Administrador de recursos de Azure mediante el comando siguiente:

        azure config mode arm

5.  Recuperar una lista de suscripciones.

        azure account list     

## <a name="next-steps"></a>Pasos siguientes

[Implementar plantillas de CLI de Azure](azure-stack-deploy-template-command-line.md)

[Conectar con PowerShell](azure-stack-connect-powershell.md)

[Administrar permisos de usuario](azure-stack-manage-permissions.md)
