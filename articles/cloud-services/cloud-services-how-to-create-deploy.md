<properties
    pageTitle="Cómo crear e implementar un servicio de nube | Microsoft Azure"
    description="Obtenga información sobre cómo crear e implementar un servicio de nube con el método de creación rápida de Azure."
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>




# <a name="how-to-create-and-deploy-a-cloud-service"></a>Cómo crear e implementar un servicio de nube

> [AZURE.SELECTOR]
- [Portal de Azure](cloud-services-how-to-create-deploy-portal.md)
- [Portal de clásico de Azure](cloud-services-how-to-create-deploy.md)

El portal clásico Azure proporciona dos formas de crear e implementar un servicio de nube: **Crear rápida** y **Personalizada**.

En este tema se explica cómo usar el método de creación rápida para crear un nuevo servicio de nube y luego use **cargar** para cargar e implementar un paquete de servicio de nube de Azure. Cuando utiliza este método, el portal clásico Azure hace vínculos cómodo disponibles para completar todos los requisitos a medida que avanza. Si está listo para implementar el servicio de nube cuando lo cree, puede hacer ambos a la vez mediante **Crear personalizado**.

> [AZURE.NOTE] Si planea publicar su servicio de nube de Visual Studio Team Services (VSTS), utilice crear rápido y luego configurar publicación VSTS de **Inicio rápido** o en el panel. Para obtener más información, consulte [La entrega de continuo a Azure mediante servicios de Visual Studio Team][TFSTutorialForCloudService], o consulte la ayuda para la página de **Inicio rápido** .

## <a name="concepts"></a>Conceptos
Se necesitan tres componentes para implementar una aplicación como un servicio de nube en Azure:

- **Definición de servicio**  
  El archivo de definición de servicio de nube (.csdef) define el modelo de servicio, incluidos el número de funciones.

- **Configuración del servicio**  
  El archivo de configuración de servicio de nube (.cscfg) proporciona a opciones de configuración de la nube funciones de servicio e individuales, incluidos el número de instancias de la función.

- **Paquete de servicio**  
  El paquete de servicio (.cspkg) contiene las configuraciones y el código de la aplicación y el archivo de definición de servicio.
  
Puede obtener más información sobre estas y cómo crear un paquete [aquí](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Preparar la aplicación
Antes de implementar un servicio de nube, debe crear el paquete de servicio de nube (.cspkg) desde el código de la aplicación y un archivo de configuración de servicio de nube (.cscfg). El SDK de Azure proporciona herramientas para preparar estos archivos de implementación necesarios. Puede instalar el SDK de la página de [Descargas de Azure](https://azure.microsoft.com/downloads/) , en el idioma en el que prefiere desarrollar el código de la aplicación.

Tres características de servicio de nube requieren configuraciones especiales antes de exportar un paquete de servicio:

- Si desea implementar un servicio de nube Secure Sockets Layer (SSL) que utiliza para el cifrado de datos, [configure la aplicación](cloud-services-configure-ssl-certificate.md#step-2-modify-the-service-definition-and-configuration-files) para SSL.

- Si desea configurar conexiones de escritorio remoto a instancias de la función, [configure las funciones](cloud-services-role-enable-remote-desktop.md) de escritorio remoto.

- Si desea configurar detallado de supervisión de su servicio de nube, habilitar diagnósticos de Azure para el servicio de nube. *Supervisión mínima* (el nivel de supervisión predeterminado) usa contadores de rendimiento recopilado de los sistemas operativos de host para las instancias de función (máquinas virtuales). "Supervisión detallado * recopila métricas adicionales en función de los datos de rendimiento de las instancias de la función para habilitar un análisis detallado de los problemas que se producen durante el proceso de aplicación. Para averiguar cómo habilitar diagnósticos de Azure, vea [Habilitar diagnósticos en Azure](cloud-services-dotnet-diagnostics.md).

Para crear un servicio de nube con implementaciones de roles de web o trabajo, debe [crear el paquete de servicio](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Antes de empezar

- Si todavía no ha instalado el SDK de Azure, haga clic en **Instalar el SDK de Azure** para abrir la [página de descargas de Azure](https://azure.microsoft.com/downloads/)y, a continuación, descargue el SDK para el idioma en el que prefiere desarrollar el código. (Tendrá una oportunidad para hacer esto más adelante).

- Si las instancias de la función requieren un certificado, cree los certificados. Servicios de nube requieren un archivo .pfx con una clave privada. Puede [cargar los certificados de Azure](cloud-services-configure-ssl-certificate.md#step-3-upload-a-certificate) como crear e implementar el servicio de nube.

- Si va a implementar el servicio de nube a un grupo de afinidad, cree el grupo afinidad. Puede usar un grupo de afinidad para implementar el servicio de nube y otros servicios de Azure en la misma ubicación en una región. Puede crear el grupo afinidad en el área de **redes** del portal Azure clásico, en la página **grupos de afinidad** .


## <a name="how-to-create-a-cloud-service-using-quick-create"></a>Cómo: crear un servicio de nube mediante Creación rápida

1. En el [portal de clásica Azure](http://manage.windowsazure.com/), haga clic en **nuevo**>**calcular**>**Servicio de nube**>**Crear rápido**.

    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)

2. En **dirección URL**, escriba un nombre de subdominio a usar en la dirección URL pública para tener acceso a su servicio de nube en implementaciones de producción. El formato de dirección URL para implementaciones de producción es: http://*myURL*. cloudapp.net.

3. En el **grupo afinidad o región**, seleccione la región geográfica o afinidad de grupo para implementar el servicio de nube. Seleccione un grupo de afinidad si desea implementar su servicio de nube en la misma ubicación que otros servicios de Azure dentro de una región.

4. Haga clic en **Crear servicio de nube**.

    ![CloudServices_Region](./media/cloud-services-how-to-create-deploy/CloudServices_Regionlist.png)

    Puede supervisar el estado del proceso en el área de mensaje en la parte inferior de la ventana.

    Se abre el área de **Servicios en la nube** , con el nuevo servicio de nube que se muestran. Cuando se cambia el estado a creado, creación de servicio de nube se completó correctamente.

    ![CloudServices_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)


## <a name="how-to-upload-a-certificate-for-a-cloud-service"></a>Cómo: cargar un certificado de un servicio de nube

1. En el [portal de clásica Azure](http://manage.windowsazure.com/), haga clic en **Servicios en la nube**, haga clic en el nombre del servicio de nube y, a continuación, haga clic en **certificados**.

    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)


2. Haga clic en **cargar un certificado** o **cargar**.

3. En un **archivo**, use **Examinar** para seleccionar el certificado (archivo .pfx).

4. En **contraseña**, escriba la clave privada para el certificado.

5. Haga clic en **Aceptar** (marca).

    ![CloudServices_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)

    Puede ver el progreso de la carga en el área de mensaje, que se muestra a continuación. Cuando finalice la carga, el certificado se agrega a la tabla. En el área de mensaje, haga clic en Aceptar para cerrar el mensaje.

    ![CloudServices_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

## <a name="how-to-deploy-a-cloud-service"></a>Cómo: implementar un servicio de nube

1. En el [portal de clásica Azure](http://manage.windowsazure.com/), haga clic en **Servicios en la nube**, haga clic en el nombre del servicio de nube y, a continuación, haga clic en **panel**.

2. Haga clic en **cargar una nueva implementación de producción** o en **cargar**.

3. En la **etiqueta de implementación**, escriba un nombre para la nueva implementación: por ejemplo, MyCloudServicev4.

3. En **paquete**, use **Examinar** para seleccionar el archivo de paquete de servicio (.cspkg) para usar.

4. En **configuración**, use **Examinar** para seleccionar el archivo de configurar de servicio (.cscfg) a utilizar.

5. Si el servicio de nube incluirá las funciones con solo una instancia, active la casilla **implementar incluso si una o varias funciones contienen una sola instancia** para habilitar la implementación continuar.

    Azure solo puede garantizar el acceso de 99,95 por ciento del servicio de nube durante las actualizaciones de servicio y mantenimiento si cada rol tiene al menos dos instancias. Si es necesario, puede agregar las instancias de la función adicionales en la página de **escala** después de implementar el servicio de nube. Para obtener más información, consulte [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/).

6. Haga clic en **Aceptar** (marca) para comenzar la implementación de servicio de nube.

    ![CloudServices_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)

    Puede supervisar el estado de la implementación en el área de mensaje. Haga clic en Aceptar para ocultar el mensaje.

    ![CloudServices_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

## <a name="verify-your-deployment-completed-successfully"></a>Comprobar la distribución que se completó correctamente

1. Haga clic en **panel**.

    El estado debe mostrar que el servicio se está **ejecutando**.

2. **Vistazo rápido**, haga clic en la dirección URL del sitio para abrir su servicio de nube en un explorador web.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)


[TFSTutorialForCloudService]: cloud-services-continuous-delivery-use-vso.md
 
## <a name="next-steps"></a>Pasos siguientes

* [Configuración general de su servicio de nube](cloud-services-how-to-configure.md).
* Configurar un [nombre de dominio personalizado](cloud-services-custom-domain-name.md).
* [Administrar el servicio de nube](cloud-services-how-to-manage.md).
* Configurar [certificados ssl](cloud-services-configure-ssl-certificate.md).