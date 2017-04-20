<properties
   pageTitle="Seguridad de Azure automatización | Microsoft Azure"
   description="Este artículo proporciona una descripción general de seguridad de automatización y los diferentes métodos de autenticación disponibles para las cuentas de automatización en automatización de Azure."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="seguridad de automatización, automatización segura" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/29/2016"
   ms.author="magoedte" />

# <a name="azure-automation-security"></a>Seguridad de automatización de Azure
Automatización de Azure le permite automatizar tareas con recursos en Azure, local y con otros proveedores de nube como Amazon Web Services (AWS).  Para un runbook realizar sus acciones necesarios, debe tener permisos de acceso seguro a los recursos con los mínimos derechos necesarios dentro de la suscripción.  
En este artículo se tratan los distintos escenarios de autenticación compatibles con la automatización de Azure y le mostrará cómo empezar a trabajar en función del entorno o entornos que necesita para administrar.  

## <a name="automation-account-overview"></a>Información general sobre la cuenta de automatización
Cuando se inicia la automatización de Azure por primera vez, debe crear al menos una cuenta de automatización. Cuentas de automatización permiten aislar los recursos de automatización (runbooks, activos, las configuraciones) de los recursos de otras cuentas de automatización. Puede usar cuentas de automatización para separar los recursos en entornos lógicos independientes. Por ejemplo, puede usar una cuenta de desarrollo, otro para producción y otro para su entorno local.  Una cuenta de Azure automatización es diferente de su cuenta de Microsoft o cuentas que creó en su suscripción de Azure.

Los recursos de automatización para cada cuenta de automatización están asociados a una sola región de Azure, pero las cuentas de automatización pueden administrar recursos en cualquier región. La razón principal para crear cuentas de automatización en diferentes regiones sería si tiene directivas que requieren datos y los recursos que se limite a una región específica.

>[AZURE.NOTE]Cuentas de automatización y los recursos que contienen se crean en el portal de Azure, no se puede acceder en el portal de clásico de Azure. Si desea administrar estas cuentas o sus recursos con Windows PowerShell, debe usar los módulos del Administrador de recursos de Azure.

Todas las tareas que realiza con recursos mediante el Administrador de recursos de Azure y los cmdlets de Azure en la automatización de Azure deben autenticarse en Azure utilizando la autenticación de credenciales de identidad de organización de Azure Active Directory.  Autenticación basada en certificado era el método de autenticación original con el modo de administración de servicios de Azure, pero se complicada para la instalación.  Autenticación en Azure con usuario Azure AD se atrás introducidos en 2014 no sólo para simplificar el proceso para configurar una cuenta de autenticación, pero también son compatibles con la capacidad de forma no interactiva autenticar Azure con una sola cuenta de usuario que trabajó con el Administrador de recursos de Azure y recursos clásicos.   

Actualmente al crear una nueva cuenta de automatización en el portal de Azure, se crea automáticamente:

-  Ejecutar como cuenta de que crea a una nueva identidad de servicio de Azure Active Directory, un certificado y le asigna el control de acceso basado en roles (RBAC), Colaborador que se usan para administrar recursos de administrador de recursos mediante runbooks.
-  Ejecutar como cuenta clásica cargando un certificado de administración, que se usan para administrar recursos clásicos con runbooks o administración del servicio de Azure.  

Control de acceso basado en roles está disponible con Azure Administrador de recursos para conceder acciones permitidas a una cuenta de usuario de Azure AD y ejecutar como cuenta y autenticar a ese servicio principal.  Lea [control de acceso basado en roles en Azure automatización artículo](../automation/automation-role-based-access-control.md) para obtener más información para ayudar a desarrollar un modelo para la administración de permisos de automatización.  

Runbooks que se ejecuta en un trabajo de Runbook híbrido en el centro de datos o frente a servicios en AWS informáticos no puede usar el mismo método que se usa normalmente para autenticar runbooks a recursos de Azure.  Esto es porque los recursos están ejecutando fuera de Azure y por tanto, requieren sus propias credenciales de seguridad definidos en automatización para autenticar a los recursos que tienen acceso a localmente.  

## <a name="authentication-methods"></a>Métodos de autenticación

La siguiente tabla resume los métodos de autenticación diferente para cada entorno compatible con automatización de Azure y el artículo que describe cómo configurar la autenticación para su runbooks.

Método  |  Entorno  | Artículo
----------|----------|----------
Cuenta de usuario de Azure AD | Administrador de recursos de Azure y administración de servicios de Azure | [Autenticar Runbooks con la cuenta de usuario de Azure AD](../automation/automation-sec-configure-aduser-account.md)
Ejecutar Azure como cuenta | Administrador de recursos de Azure | [Autenticar Runbooks con Azure ejecutar como cuenta](../automation/automation-sec-configure-azure-runas-account.md)
Azure ejecutar clásico como cuenta | Administración del servicio de Azure | [Autenticar Runbooks con Azure ejecutar como cuenta](../automation/automation-sec-configure-azure-runas-account.md)
Autenticación de Windows | Centro de datos local | [Autenticar Runbooks para los trabajadores de Runbook híbrido](../automation/automation-hybrid-runbook-worker.md)
AWS credenciales | Servicios Web de Amazon | [Autenticar Runbooks con los servicios Web de Amazon (AWS)](../automation/automation-sec-configure-aws-account.md)



