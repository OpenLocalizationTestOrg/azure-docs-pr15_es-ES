<properties
    pageTitle="Permitir que la aplicación a información confidencial de Azure pila clave depósito revtrieve | Microsoft Azure"
    description="Usar una aplicación de ejemplo para trabajar con depósito de clave de pila de Azure"
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

# <a name="run-the-sample-application-for-key-vault"></a>Ejecute la aplicación de ejemplo para depósito de clave 

En esta guía, deberá usar una aplicación de ejemplo para recuperar información confidencial y las contraseñas de la cámara de clave.

## <a name="download-the-samples-and-prepare"></a>Descargar los ejemplos y preparar

Descargue los ejemplos de cliente de Azure clave depósito desde la [página de ejemplos de Azure depósito de clave de cliente](https://www.microsoft.com/en-us/download/details.aspx?id=45343).

Extraer el contenido del archivo .zip en el equipo local.

Leer el archivo **README.md** (es decir, un archivo de texto) y, a continuación, siga las instrucciones.

## <a name="run-sample-1--hellokeyvault"></a>Ejecutar ejemplo #1: HelloKeyVault
HelloKeyVault es una aplicación de consola que le guiará por los escenarios clave que son compatibles con la cámara de clave:

  1. Crear o importar una clave (software o HSM)
  2. Cifrar un secreto con una clave de contenido
  3. Ajustar la clave de contenido con una clave de depósito de clave
  4. Desajustar la clave de contenido
  5. Descifrar el secreto
  6. Establecer un secreto

Aplicación consola debe ejecutarse sin cambios, excepto en que la configuración adecuada en App.Config se actualizará según los pasos siguientes:

1. Actualizar la configuración de aplicación en HelloKeyVault\App.config con la dirección URL de la cámara, identificador principal de la aplicación y secreto. Opcionalmente, la información se puede generar mediante **scripts\GetAppConfigSettings.ps1**.
2. Actualizar los valores de las variables obligatorio en GetAppConfigSettings.ps1.
3. Inicia la ventana de Windows PowerShell.
4. Ejecute el script GetAppConfigSettings.ps1 dentro de la ventana de PowerShell.
5. Copiar los resultados de la secuencia de comandos en el archivo HelloKeyVault\App.config.


## <a name="next-steps"></a>Pasos siguientes

[Implementar una máquina virtual con una contraseña de la cámara de clave](azure-stack-kv-deploy-vm-with-secret.md)

[Implementar una máquina virtual con un certificado de depósito de clave](azure-stack-kv-push-secret-into-vm.md)