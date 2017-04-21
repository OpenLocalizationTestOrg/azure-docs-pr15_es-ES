<properties
    pageTitle="Cómo crear e implementar un servicio de nube | Microsoft Azure"
    description="Obtenga información sobre cómo crear e implementar un servicio de nube con el portal de Azure."
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
    ms.date="10/11/2016"
    ms.author="adegeo"/>




# <a name="how-to-create-and-deploy-a-cloud-service"></a>Cómo crear e implementar un servicio de nube

> [AZURE.SELECTOR]
- [Portal de Azure](cloud-services-how-to-create-deploy-portal.md)
- [Portal de clásico de Azure](cloud-services-how-to-create-deploy.md)

El portal de Azure proporciona dos formas de crear e implementar un servicio de nube: *Crear rápida* y *Personalizada*.

En este artículo se explica cómo usar el método de creación rápida para crear un nuevo servicio de nube y luego use **cargar** para cargar e implementar un paquete de servicio de nube de Azure. Cuando utiliza este método, el portal de Azure hace vínculos cómodo disponibles para completar todos los requisitos a medida que avanza. Si está listo para implementar el servicio de nube cuando lo cree, puede hacer ambos a la vez mediante crear personalizado.

> [AZURE.NOTE] Si planea publicar su servicio de nube de Visual Studio Team Services (VSTS), utilice crear rápido y luego configurar publicación VSTS desde el tutorial de Azure o los paneles. Para obtener más información, consulte [La entrega de continuo a Azure mediante servicios de Visual Studio Team][TFSTutorialForCloudService], o consulte la ayuda para la página de **Inicio rápido** .

## <a name="concepts"></a>Conceptos
Implementar una aplicación como un servicio de nube en Azure se necesitan tres componentes:

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

- Si desea implementar un servicio de nube Secure Sockets Layer (SSL) que utiliza para el cifrado de datos, [configure la aplicación](cloud-services-configure-ssl-certificate-portal.md#modify) para SSL.

- Si desea configurar conexiones de escritorio remoto a instancias de la función, [configure las funciones](cloud-services-role-enable-remote-desktop.md) de escritorio remoto. Solo puede realizarse en el portal de clásico.

- Si desea configurar detallado de supervisión de su servicio de nube, habilitar diagnósticos de Azure para el servicio de nube. *Supervisión mínima* (el nivel de supervisión predeterminado) usa contadores de rendimiento recopilado de los sistemas operativos de host para las instancias de función (máquinas virtuales). *Supervisión detallado* recopila métricas adicionales en función de los datos de rendimiento de las instancias de la función para habilitar un análisis detallado de los problemas que se producen durante el proceso de aplicación. Para averiguar cómo habilitar diagnósticos de Azure, vea [Habilitar diagnósticos en Azure](cloud-services-dotnet-diagnostics.md).

Para crear un servicio de nube con implementaciones de roles de web o trabajo, debe [crear el paquete de servicio](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Antes de empezar

- Si todavía no ha instalado el SDK de Azure, haga clic en **Instalar el SDK de Azure** para abrir la [página de descargas de Azure](https://azure.microsoft.com/downloads/)y, a continuación, descargue el SDK para el idioma en el que prefiere desarrollar el código. (Tendrá una oportunidad para hacer esto más adelante).

- Si las instancias de la función requieren un certificado, cree los certificados. Servicios de nube requieren un archivo .pfx con una clave privada. [Puede cargar los certificados a Azure]() que cree e implementar el servicio de nube.

- Si va a implementar el servicio de nube a un grupo de afinidad, cree el grupo afinidad. Puede usar un grupo de afinidad para implementar el servicio de nube y otros servicios de Azure en la misma ubicación en una región. Puede crear el grupo afinidad en el área de **redes** del portal Azure clásico, en la página **grupos de afinidad** .


## <a name="create-and-deploy"></a>Crear e implementar

1. Inicie sesión en el [portal de Azure](https://portal.azure.com/).
2. Haga clic en **Nuevo > máquinas virtuales**y, a continuación, desplácese hacia abajo y haga clic en **Servicio de nube**.

    ![Publicar su servicio de nube](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)

3. En la parte inferior de la página de información que se muestra, haga clic en **crear**. 
4. En el módulo de **Servicio de nube de** nuevo, escriba un valor en el **nombre DNS**.
5. Crear un nuevo **Grupo de recursos** o seleccione uno existente.
6. Seleccione una **ubicación**.
7. Haga clic en el **paquete**. Se abrirá el módulo de **cargar un paquete** . Rellene los campos necesarios.  

     Si alguno de los roles contiene una sola instancia, asegúrese de **que implementar incluso si una o varias funciones contienen una sola instancia** está seleccionado.

8. Asegúrese de que está seleccionada la **implementación de inicio** .
9. Haga clic en **Aceptar** en el que se cerrará el módulo de **cargar un paquete** .
10. Si no tiene ningún certificado para agregar, haga clic en **crear**.

    ![Publicar su servicio de nube](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Cargar un certificado

Si el paquete de implementación se ha [configurado para usar certificados](cloud-services-configure-ssl-certificate-portal.md#modify), puede cargar el certificado ahora.

1. Seleccione **certificados**y en el módulo de **certificados de agregar** , seleccione el archivo .pfx de certificado SSL y, a continuación, proporcione la **contraseña** para el certificado
2. Haga clic en **adjuntar certificado**y, a continuación, haga clic en **Aceptar** en el módulo de **certificados de agregar** .
3. Haga clic en **crear** en el módulo de **Servicio de nube** . Cuando la implementación ha alcanzado el estado **Listo** , puede continuar con los siguientes pasos.

    ![Publicar su servicio de nube](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)


## <a name="verify-your-deployment-completed-successfully"></a>Comprobar la distribución que se completó correctamente

1. Haga clic en la instancia de servicio de nube.

    El estado debe mostrar que el servicio se está **ejecutando**.

2. **Essentials**, haga clic en la **Dirección URL del sitio** para abrir su servicio de nube en un explorador web.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)


[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>Pasos siguientes

* [Configuración general de su servicio de nube](cloud-services-how-to-configure-portal.md).
* Configurar un [nombre de dominio personalizado](cloud-services-custom-domain-name-portal.md).
* [Administrar el servicio de nube](cloud-services-how-to-manage-portal.md).
* Configurar [certificados ssl](cloud-services-configure-ssl-certificate-portal.md).