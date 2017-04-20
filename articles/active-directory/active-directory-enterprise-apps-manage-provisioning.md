<properties
    pageTitle="Administración de aplicaciones empresariales en la vista previa de Azure Active Directory de aprovisionamiento de usuarios | Microsoft Azure"
    description="Obtenga información sobre cómo administrar el aprovisionamiento de cuentas de usuario para las aplicaciones empresariales con la versión preliminar de Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="asmalser"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/12/2016"
    ms.author="asmalser"/>

#<a name="preview-managing-user-account-provisioning-for-enterprise-apps-in-the-new-azure-portal"></a>Vista previa: Administración de cuenta de usuario de aprovisionamiento de aplicaciones empresariales en el nuevo portal de Azure

En este artículo se describe cómo usar el [portal de Azure](https://portal.azure.com) para administrar cuentas de usuario automática de aprovisionamiento y anular el aprovisionamiento de para las aplicaciones que lo admiten, especialmente en los que se han agregado de la categoría "destacada" de la [Galería de la aplicación de Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). Esta experiencia de administración en el nuevo portal de Azure está en vista previa público y, en este artículo se describe las características nuevas, así como algunas limitaciones temporales que están en su lugar durante el período de vista previa. [¿Qué es la vista previa?](active-directory-preview-explainer.md)

Para obtener más información sobre el aprovisionamiento de cuentas de usuario automática y cómo funciona, consulte [automatizar el aprovisionamiento de usuario y Deprovisioning a aplicaciones SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md).

##<a name="finding-your-apps-in-the-new-portal"></a>Buscar sus aplicaciones en el nuevo portal

A partir de septiembre de 2016, todas las aplicaciones que se han configurado para un solo inicio de sesión en un directorio, un administrador del directorio utilizando la [Galería de la aplicación de Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery) dentro del [portal clásica Azure](https://manage.windowsazure.com), pueden ahora ver y administrar en el nuevo portal de Azure.

Estas aplicaciones se pueden encontrar en la sección **Aplicaciones empresariales** del nuevo portal Azure, que se puede acceder a través del menú **Más servicios** en el área de navegación izquierdo. Aplicaciones empresariales son aplicaciones que se han implementado y se utiliza por los usuarios de su organización.

![Módulo de aplicaciones empresariales][0]

Seleccionando el vínculo de **todas las aplicaciones** de la izquierda, muestra una lista de todas las aplicaciones que se han configurado, incluidas las aplicaciones que se han añadido desde la galería. Seleccionar una aplicación de carga del módulo de recursos para esa aplicación, donde pueden ver los informes de esa aplicación y pueden administrarse diversas opciones de configuración.

Configuración de aprovisionamiento de cuenta de usuario se puede administrar seleccionando el **aprovisionamiento** de la izquierda.

![Módulo de recursos de aplicación][1]


##<a name="provisioning-modes"></a>Modos de aprovisionamiento

El módulo de **aprovisionamiento** comienza con un menú de **modo** que muestra los modos de aprovisionamiento son compatibles para una aplicación de empresa y le permite estar configurado. Las opciones disponibles son:

* **Automático** : esta opción aparece si Azure AD admite basada en API aprovisionamiento automático o anular el aprovisionamiento de cuentas de usuario para esta aplicación. Al seleccionar este modo muestra una interfaz que guía a los administradores a través de la configuración de Azure Active Directory para conectarse a la API de administración de usuario de la aplicación, crear asignaciones de cuentas y flujos de trabajo que definen cómo deben flujo de datos de la cuenta de usuario entre Azure AD y la aplicación y administración de Azure AD el aprovisionamiento de servicio.

* **Manual** : esta opción se muestra si Azure AD no admite aprovisionamiento automático de cuentas de usuario para esta aplicación. Esta opción significa que se administra los registros de cuenta de usuario almacenados en la aplicación con un proceso externo, en función de las capacidades de administración y el aprovisionamiento de usuario proporcionadas por la aplicación (que puede incluir el aprovisionamiento de SAML Just).


##<a name="configuring-automatic-user-account-provisioning"></a>Configuración de aprovisionamiento de cuentas de usuario automática

Seleccionar la opción **automática** muestra una pantalla en la que se divide en cuatro secciones:

###<a name="admin-credentials"></a>Credenciales de administrador
Esto es donde las credenciales necesitan para que Azure AD para conectarse a la administración de usuarios de la aplicación se introducen API. La información necesaria varía dependiendo de la aplicación. Para obtener información sobre los requisitos para aplicaciones específicas y los tipos de credenciales, consulte el [tutorial de configuración para esa aplicación específica](active-directory-saas-app-provisioning.md#list-of-apps-that-support-automated-user-provisioning).

Seleccionar el botón **Probar conexión** le permite probar las credenciales al tener Azure aplicación con las credenciales proporcionadas de aprovisionamiento de intento de AD para conectarse a la aplicación.

###<a name="mappings"></a>Asignaciones
Esto es donde los administradores pueden ver y modificar el flujo de atributos de usuario entre Azure AD y la aplicación de destino, cuando se aprovisiona o se actualizan las cuentas de usuario.

Hay un conjunto de asignaciones entre los objetos de usuario de Azure AD y objetos de usuario de cada aplicación SaaS preconfigurado. Algunas aplicaciones administración otros tipos de objetos, como contactos o grupos. Seleccione una de estas asignaciones en la tabla se muestra el editor de la asignación a la derecha, donde puede ver y personalizar.

![Módulo de recursos de aplicación][2]

Personalizaciones compatibles durante la vista previa del primer incluyen:

* Habilitar y deshabilitar asignaciones para objetos específicos, como el objeto de usuario de Azure AD al objeto de usuario de la aplicación de SaaS.

* Editar fluyan de qué atributos del objeto de usuario de Azure AD al objeto de usuario de la aplicación. Para obtener más información sobre la asignación de atributo, vea [Descripción de tipos de asignación de atributo](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-types).

* Filtrar qué acciones de aprovisionamiento Azure AD debe ejecutar en la aplicación de destino, que es una nueva característica en el portal de Azure. En lugar de Azure AD totalmente-sincronizar objetos, puede limitar las acciones que realiza. Por ejemplo, solamente selección de **actualización**, actualizaciones sola de Azure AD cuentas en una aplicación de usuario existente y no crear nuevos. Seleccionando sólo **crear**, Azure solo crea nuevas cuentas de usuario pero no actualizan las existentes. Esta característica permite a los administradores crear asignaciones diferentes para la creación de cuenta y actualizar los flujos de trabajo. La capacidad total para crear varias asignaciones por aplicación está planificada para más adelante en el período de vista previa.

###<a name="settings"></a>Configuración
Esta sección permite a los administradores a iniciar y detener la Azure AD el aprovisionamiento de servicio para la aplicación seleccionada, así como si lo desea borrar la caché de aprovisionamiento y reinicie el servicio.

Si se habilita el aprovisionamiento de por la primera vez para una aplicación, activar el servicio cambiando el **Estado de aprovisionamiento** en **activado**. Hace que la Azure AD el aprovisionamiento de servicio realizar una sincronización inicial, donde lee los usuarios asignados en la sección **usuarios y grupos** , las consultas de la aplicación de destino para ellos y, a continuación, realiza las acciones de aprovisionamiento definidas en la sección de Azure AD **asignaciones** . Durante este proceso, el servicio de aprovisionamiento almacena datos en caché sobre qué cuentas de usuario que se administra, para que cuentas no administrado dentro de las aplicaciones de destino que estaban nunca en el ámbito de la asignación no se ven afectadas por eliminar las operaciones. Tras la sincronización inicial, el servicio de aprovisionamiento sincroniza automáticamente objetos de usuario y grupo en un intervalo de diez minutos.

Cambiar el **Estado de aprovisionamiento** a **desactivar** simplemente hace una pausa en el servicio de aprovisionamiento. En este estado, Azure no crear, actualizar o quitar cualquier usuario o agrupar objetos en la aplicación. Cambiar el estado a activado hace que el servicio recoger donde la dejó.

Seleccionar la casilla de verificación **Borrar estado actual y reiniciar la sincronización** y guardar detiene el servicio de aprovisionamiento, volcados es la administración de los datos en caché sobre qué cuentas de Azure AD, reinicia los servicios y realiza la sincronización inicial de nuevo. Esta opción permite a los administradores iniciar el proceso de implementación de nuevo.

###<a name="synchronization-details"></a>Detalles de sincronización
Esta sección proporciona detalles de adición sobre el funcionamiento del servicio de aprovisionamiento, incluidas las horas y el apellido que ejecutó el servicio de aprovisionamiento frente a la aplicación y se administra cuántos objetos de usuario y grupo.

Los vínculos son siempre al **informe de la actividad de aprovisionamiento**, que proporciona un registro de todos los usuarios y grupos creado, actualizado y eliminado entre Azure AD y el destino de aplicación y para el **informe de errores de aprovisionamiento** que proporciona más detallada mensajes de error de usuario y agrupar los objetos que no se puede leer, crear, actualizar o quitar. 

[0]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-blade.PNG
[1]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning.PNG
[2]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning-mapping.PNG
