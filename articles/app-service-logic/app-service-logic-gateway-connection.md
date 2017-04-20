<properties
   pageTitle="Lógica de aplicaciones de conexión de la puerta de enlace de datos local | Microsoft Azure"
   description="Obtener información sobre cómo crear una conexión a la puerta de enlace de datos local desde una aplicación de lógica."
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/05/2016"
   ms.author="jehollan"/>

# <a name="connect-to-the-on-premises-data-gateway-for-logic-apps"></a>Conectarse a la puerta de enlace de datos local para las aplicaciones de lógica

Conectores de aplicaciones de lógica compatibles permiten configurar su conexión de acceso a los datos locales a través de la puerta de enlace de datos local.  Los siguientes pasos le guiará a través de cómo instalar y configurar la puerta de enlace de datos local para trabajar con una aplicación de lógica.

## <a name="prerequisites"></a>Requisitos previos

* Debe usar un trabajo o escuela dirección de correo electrónico en Azure para asociar la puerta de enlace de datos local a su cuenta (basada en Azure Active Directory)
    * Si está utilizando una Account de Microsoft (por ejemplo, @outlook.com, @live.com) puede usar su cuenta de Azure para crear un trabajo o escuela dirección de correo electrónico siguiendo [los pasos que se indican a continuación](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal)

> [AZURE.WARNING] Hay una limitación actualmente que instalación de puerta de enlace sólo se completará cuando se usa una cuenta que se ha registrado con Power BI en local.  Mientras tanto para registrar una cuenta con "Power BI"libre para completar la instalación correctamente.

* Debe tener la puerta de enlace de datos local [instalada en un equipo local](app-service-logic-gateway-install.md).
* Puerta de enlace no debe ha sido solicitado por otra puerta de enlace del datos de Azure en local ([reclamar ocurre con la creación de paso 2 a continuación](#2-create-an-azure-on-premises-data-gateway-resource)): una instalación solo se puede asociar a recursos de una puerta de enlace.

## <a name="installing-and-configuring-the-connection"></a>Instalar y configurar la conexión

### <a name="1-install-the-on-premises-data-gateway"></a>1. Instale la puerta de enlace de datos local

Puede encontrar información sobre cómo instalar la puerta de enlace de datos local [en este artículo](app-service-logic-gateway-install.md).  La puerta de enlace debe estar instalado en un equipo local antes de continuar con el resto de los pasos.

### <a name="2-create-an-azure-on-premises-data-gateway-resource"></a>2. crear un recurso de puerta de enlace de datos de Azure en local

Una vez instalado, debe asociar la suscripción Azure con la puerta de enlace de datos local.

1. Inicio de sesión para usar el mismo trabajo o la dirección de correo electrónico de escuela que se usó durante la instalación de la puerta de enlace de Azure
1. Haga clic en el botón de **nuevo** recurso
1. Busque y seleccione la **puerta de enlace de datos local**
1. Complete la información para asociar la puerta de enlace con la cuenta - incluyendo seleccionando el **Nombre de la instalación**

    ![Conexión de puerta de enlace de datos local][1]
1. Haga clic en el botón **crear** para crear el recurso

### <a name="3-create-a-logic-app-connection-in-the-designer"></a>3. crear una conexión de la aplicación de lógica en el diseñador

Ahora que su suscripción de Azure está asociada a una instancia de la puerta de enlace de datos local, puede crear una conexión desde dentro de una aplicación de lógica.

1. Abra una aplicación de lógica y elija un conector que admite la conectividad local (a partir de este documento, SQL Server)
1. Seleccione la casilla de verificación para **conectarse a través de la puerta de enlace de datos local**

    ![Creación de puerta de enlace del Diseñador de la aplicación de lógica][2]
1. Seleccione la **puerta de enlace** para conectarse y completar cualquier otra información de conexión necesaria
1. Haga clic en **crear** para crear la conexión

Ahora la conexión debe configurarse correctamente para su uso en la aplicación de la lógica.  

## <a name="next-steps"></a>Pasos siguientes
- [Ejemplos y escenarios para lógica aplicaciones comunes](app-service-logic-examples-and-scenarios.md)
- [Características de integración de Enterprise](app-service-logic-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/app-service-logic-gateway-connection/createblade.PNG
[2]: ./media/app-service-logic-gateway-connection/blankconnection.PNG
[3]: ./media/app-service-logic-gateway-connection/checkbox.PNG