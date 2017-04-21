<properties
    pageTitle="Administrar Azure clave depósito mediante la automatización de Azure | Microsoft Azure"
    description="Obtenga información acerca de cómo puede utilizar el servicio de automatización de Azure para administrar depósito de clave de Azure."
    services="Key-Vault, automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="magoedte;csand"/>

#<a name="managing-azure-key-vault-using-azure-automation"></a>Administración de Azure clave depósito mediante la automatización de Azure

Esta guía presentan el servicio de automatización de Azure y cómo puede utilizarse para simplificar la administración de sus claves y la información confidencial en depósito de clave de Azure.

## <a name="what-is-azure-automation"></a>¿Qué es la automatización de Azure?

[Automatización de Azure](../automation/automation-intro.md) es un servicio de Azure para simplificar la administración de la nube mediante la automatización de procesos y la configuración de estado que desee. Mediante la automatización de Azure, manual, repetido, tareas de ejecución larga y provocar errores se pueden automatizar para aumentar la confiabilidad, eficiencia y tiempo de valor para su organización.

Automatización de Azure proporciona un motor de ejecución de flujo de trabajo es muy confiable de alta disponibilidad escala para satisfacer sus necesidades. En automatización de Azure, procesos pueden ser dio manualmente, parte 3 º sistemas o intervalos programados para que las tareas que se producen exactamente cuando sea necesario.

Reducir la sobrecarga operativa y liberar TI y el personal de DevOps para centrarse en el trabajo que agrega el valor para el negocio al mover las tareas de administración de la nube se ejecute automáticamente mediante la automatización de Azure.


## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>¿Cómo puede ayudar automatización de Azure a administrar depósito de clave de Azure?

Tecla depósito pueden administrarse en Azure automatización mediante [cmdlets de AzureRM clave depósito] (https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) y los [cmdlets de Azure clásica clave depósito](https://msdn.microsoft.com/library/azure/dn868052.aspx). El módulo de administración de depósito clásico de clave Azure está disponible automáticamente en Azure automatización, y puede importar el [módulo de AzureRM KeyVault](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) en automatización de Azure, por lo que puede realizar muchas de las tareas de administración de depósito de clave en el servicio. También puede emparejar estos cmdlets en automatización de Azure con los cmdlets de otros servicios de Azure, para automatizar tareas complejas en servicios de Azure y 3er sistemas de terceros.

Con los cmdlets de Azure clave depósito puede realizar estas tareas entre otros: 

- Crear y configurar un depósito clave
- Crear o importar una clave
- Crear o actualizar un secreto
- Actualizar los atributos de una clave
- Obtener una clave o un secreto
- Eliminar una clave o secreto

Aquí se muestran algunos ejemplos de uso de PowerShell para administrar depósito de clave:  

* [Depósito clave Azure - paso a paso](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [Instalar y configurar un depósito de clave de Azure](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)


## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de automatización de Azure y cómo puede utilizarse para administrar depósito de clave de Azure, siga estos vínculos para obtener más información acerca de la automatización de Azure.

* Vea la automatización Azure [Introducción tutoriales](../automation/automation-first-runbook-graphical.md).
* Vea los [scripts de PowerShell de depósito de clave de Azure](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5).
