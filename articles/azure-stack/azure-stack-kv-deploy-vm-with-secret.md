<properties
    pageTitle="Implementar una máquina virtual mediante una contraseña almacenada en depósito de clave de pila de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo implementar una máquina virtual mediante una contraseña almacenada en depósito de clave de pila de Azure"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="deploy-a-vm-by-retrieving-the-password-stored-in-key-vault"></a>Implementar una máquina virtual mediante la recuperación de la contraseña almacenada en depósito de clave

Cuando necesite pasar un valor seguro (por ejemplo, una contraseña) como un parámetro durante la implementación, puede almacenar el valor como un secreto en un depósito de clave de pila de Azure y hacer referencia al valor en otras plantillas de administrador de recursos de Azure. Incluya sólo una referencia al secreto en la plantilla para que nunca se expone el secreto. No es necesario introducir manualmente el valor para el secreto cada vez que se implementa los recursos. Especificar qué usuarios o identidades de servicio pueden tener acceso al secreto.

## <a name="reference-a-secret-with-static-id"></a>Hacer referencia a un secreto con identificador estático

Se hace referencia el secreto desde dentro de un archivo de parámetros, que se pasa a la plantilla. Referencia el secreto pasando el identificador de recursos del depósito clave y el nombre del secreto. En este ejemplo, el secreto de clave depósito ya debe existir. Usar un valor estático de su identificador de recurso.

    "parameters": {
    "adminPassword": {
    "reference": {
    "keyVault": {
    "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
    },
    "secretName": "sqlAdminPassword"


>[AZURE.NOTE]El parámetro que acepta el secreto debería *securestring*.

## <a name="next-steps"></a>Pasos siguientes
[Implementar una aplicación de ejemplo con clave de cámara](azure-stack-kv-sample-app.md)

[Implementar una máquina virtual con un certificado de depósito de clave](azure-stack-kv-push-secret-into-vm.md)

