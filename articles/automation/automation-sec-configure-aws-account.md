<properties
   pageTitle="Configurar la autenticación con los servicios Web de Amazon | Microsoft Azure"
   description="En este artículo se describe cómo crear y validar una credencial AWS para runbooks en la administración de recursos AWS de automatización de Azure."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="autenticación de AWS, configurar aws"/>
<tags
   ms.service="automation"
   ms.workload="tbd"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="09/12/2016"
   ms.author="magoedte"/>

# <a name="authenticate-runbooks-with-amazon-web-services"></a>Autenticar Runbooks con servicios Web de Amazon
Automatizar tareas con recursos en servicios de Web de Amazon (AWS) se puede realizar con la automatización runbooks en Azure.  Puede automatizar muchas de las tareas en AWS con automatización runbooks igual que con recursos en Azure.  Todo lo que se requiere son dos cosas:

* Una suscripción AWS y un conjunto de credenciales.  Específicamente la tecla de acceso AWS y clave secreta.  Para obtener más información, consulte el artículo [Usando AWS credenciales](http://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* Una suscripción de Azure y cuenta con la automatización.  Para obtener más información acerca de cómo configurar una cuenta de Azure automatización, revise el artículo [Configurar ejecutar como cuenta de Azure](../automation/automation-sec-configure-azure-runas-account.md).  

Para autenticar con AWS, debe especificar un conjunto de credenciales AWS autenticar sus runbooks ejecución de automatización de Azure. Si ya tiene una cuenta de automatización creada y desea usarlo para autenticar con AWS, puede seguir los pasos de la sección siguiente.  Si desea dedicado a una cuenta de recursos de runbooks orientados a AWS, debe primero crear una nueva [cuenta de automatización ejecutar como](../automation/automation-sec-configure-azure-runas-account.md) (omitir la opción de crear una entidad de seguridad de servicio) y, a continuación, siga los pasos siguientes.

## <a name="configure-automation-account"></a>Configurar la cuenta de automatización
Automatización de Azure comunicarse con AWS, primero debe recuperar sus credenciales AWS y almacenarlos como activos de automatización de Azure.  Realice los pasos siguientes documentados en el documento AWS [Administrar las teclas de acceso para su cuenta de AWS](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) para crear una tecla de acceso y copiar el **Identificador de tecla de acceso** y la **Tecla de acceso de secreto** (si lo desea descargar el archivo de clave para guardarlo en un lugar seguro).

Después de que ha creado y copiar las claves de seguridad AWS, debe crear un activo de credenciales con una cuenta de Azure automatización para almacenar y hacer referencia a ellos con sus runbooks de forma segura.  Siga los pasos de la sección **para crear una nueva credencial** en el artículo de [activos de credenciales en la automatización de Azure](../automation/automation-certificates.md/###To create a new credential with the Azure portal) y escriba la información siguiente:

1. En el cuadro **nombre** , escriba **AWScred** o un valor adecuado siguiendo los estándares de nomenclatura.  
2. En el cuadro **nombre de usuario** , escriba el **Identificador de acceso** y la **Tecla de acceso de secreto** en el cuadro **contraseña** y **Confirmar contraseña** .   

## <a name="next-steps"></a>Pasos siguientes

- El artículo de solución de [implementación de automatización de una máquina virtual de los servicios Web de Amazon](../automation/automation-scenario-aws-deployment.md) para aprender a crear runbooks para automatizar tareas en AWS Reivew.
