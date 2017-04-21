<properties services="virtual-machines" title="Setting up Azure CLI for service management" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## <a name="using-azure-cli"></a>Utilice CLI Azure

Los siguientes pasos ayudan a utilizar CLI de Azure fácilmente con la versión más reciente y la suscripción correcta. Si necesita instalar CLI de Azure y conectarse a su cuenta en primer lugar, vea la [Azure de línea de comandos interfaz Azure](xplat-cli-install.md).

### <a name="step-1-update-azure-cli-version"></a>Paso 1: Versión de actualización Azure CLI

Para usar comandos fundamental con el modo de administración de servicio CLI de Azure, debe tener una versión reciente si es posible. Para comprobar la versión, escriba `azure --version`. Verá algo parecido:

    $ azure --version
    0.8.17 (node: 0.10.25)

Si desea actualizar su versión de Azure CLI, consulte [CLI de Azure](https://github.com/Azure/azure-xplat-cli).

### <a name="step-2-set-the-azure-account-and-subscription"></a>Paso 2: Configurar la cuenta de Azure y suscripción

Una vez que ha conectado su CLI Azure con la cuenta que desea usar, puede que tenga más de una suscripción. Si lo hace, debe revisar las suscripciones disponibles para su cuenta escribiendo `azure account list`y, a continuación, seleccione la suscripción que desea usar escribiendo `azure account set <subscription id or name> true` donde _suscripción identificador o nombre_ es el identificador de la suscripción o el nombre de la suscripción que le gustaría trabajar con en la sesión actual. Verá algo parecido a lo siguiente:

    $ azure account set "Visual Studio Ultimate with MSDN" true
    info:    Executing command account set
    info:    Setting subscription to "Visual Studio Ultimate with MSDN" with id "0e220bf6-5caa-4e9f-8383-51f16b6c109f".
    info:    Changes saved
    info:    account set command OK

> [AZURE.NOTE] Si todavía no tiene una cuenta de Azure, pero tiene una suscripción a la suscripción a MSDN, puede obtener gratuita Azure créditos activando los [beneficios de suscriptor MSDN aquí](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) --o pueden usar la cuenta gratuita. Cualquiera funcionará para el acceso de Azure.
