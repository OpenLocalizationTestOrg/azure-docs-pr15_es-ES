<properties 
    pageTitle="Cómo configurar un servicio de nube (portal clásico) | Microsoft Azure" 
    description="Obtenga información sobre cómo configurar los servicios en la nube en Azure. Aprenda a actualizar la configuración del servicio de nube y configurar el acceso remoto a las instancias de la función." 
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




# <a name="how-to-configure-cloud-services"></a>Cómo configurar los servicios de nube

> [AZURE.SELECTOR]
- [Portal de Azure](cloud-services-how-to-configure-portal.md)
- [Portal de clásico de Azure](cloud-services-how-to-configure.md)

Puede configurar las opciones más utilizadas de un servicio de nube en el portal de clásico de Azure. O bien, si desea actualizar los archivos de configuración directamente, descargar un archivo de configuración de servicio para actualizar y, a continuación, cargar el archivo actualizado y actualizar el servicio de nube con los cambios de configuración. En ambos casos, las actualizaciones de configuración se resaltan los a todas las instancias de la función.

El portal clásico Azure también le permite [Habilitar la conexión a Escritorio remoto para un rol de servicios de nube de Azure](cloud-services-role-enable-remote-desktop.md)

Azure solo puede garantizar la disponibilidad de servicio de 99,95 por ciento durante las actualizaciones de configuración si tiene al menos dos instancias de rol para cada rol. Que permite una máquina virtual procesar las solicitudes de cliente mientras se actualiza el otro. Para obtener más información, consulte [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Cambiar un servicio de nube

1. En el [portal de clásica Azure](http://manage.windowsazure.com/), haga clic en **Servicios en la nube**, haga clic en el nombre del servicio de nube y, a continuación, haga clic en **Configurar**.

    ![Página de configuración](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png)
    
    En la página **Configurar** , puede configurar la supervisión, actualizar la configuración de la función y elija el sistema operativo invitado y familiares para instancias de la función. 

2. En la **supervisión**, establecer el nivel de supervisión detallado o mínimas y configurar las cadenas de conexión de diagnósticos necesarios para supervisar detallado.

3. Para las funciones de servicio (agrupadas por rol), puede actualizar los valores siguientes:
    
    >**Configuración**  
    >Modifique los valores de varios parámetros de configuración que se especifican en los elementos *ConfigurationSettings* del archivo de configuración (.cscfg) de servicio.
    >
    >**Certificados**  
    >Cambiar la huella digital del certificado que se utiliza en el cifrado de SSL para una función. Para cambiar un certificado, primero debe cargar el nuevo certificado (en la página de **certificados** ). A continuación, actualice la huella digital de la cadena de certificado que se muestra en la configuración de la función.

4. En el **sistema operativo**, puede cambiar la familia de sistemas operativos o la versión de las instancias de función o elija **automático** para habilitar las actualizaciones automáticas de la versión actual del sistema operativo. La configuración del sistema operativo se aplica a las funciones de web y las funciones de trabajo, pero no afectan a los equipos virtuales.

    Durante la implementación, la versión más reciente del sistema operativo está instalada en todas las instancias de la función y los sistemas operativos se actualizan automáticamente de forma predeterminada. 
    
    Si necesita su servicio de nube para ejecutarse en una versión de sistema operativo diferente debido a los requisitos de compatibilidad en el código, puede elegir una familia de sistemas operativos y la versión. Al elegir una versión de sistema operativo específico, se suspenden actualizaciones automática del sistema operativo para el servicio de nube. Debe asegurarse de que los sistemas operativos recibir actualizaciones.
    
    Si resolver los problemas de compatibilidad que tienen sus aplicaciones con la versión más reciente del sistema operativo, puede habilitar actualizaciones automáticas del sistema operativo mediante la configuración de la versión del sistema operativo en **automático**. 
    
    ![Configuración del sistema operativo](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png)

5. Para guardar la configuración e insertarlos a las instancias de la función, haga clic en **Guardar**. (Haga clic en **Descartar** para cancelar los cambios.) **Guardar** y **Descartar** se agregan a la barra de comandos después de cambiar un valor.

## <a name="update-a-cloud-service-configuration-file"></a>Actualizar un archivo de configuración de servicio de nube

1. Descargar un archivo de configuración del servicio de nube (.cscfg) con la configuración actual. En la página **Configurar** el servicio de nube, haga clic en **Descargar**. A continuación, haga clic en **Guardar**, o haga clic en **Guardar como** para guardar el archivo.

2. Después de actualizar el archivo de configuración de servicio, cargar y aplicar las actualizaciones de configuración:

    1. En la página **Configurar** , haga clic en **cargar**.
    
        ![Cargar configuración](./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png)
    
    2. En **el archivo de configuración**, use **Examinar** para seleccionar el archivo actualizado .cscfg.
    
    3. Si su servicio de nube contiene los roles que tienen una única instancia, seleccione la casilla de verificación **Aplicar configuración, incluso si una o varias funciones contienen una sola instancia** para habilitar las actualizaciones de configuración de los roles continuar.
    
        A menos que defina al menos dos instancias de cada rol, Azure no garantiza al menos 99,95% disponibilidad de su servicio de nube durante las actualizaciones de configuración de servicio. Para obtener más información, consulte [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/).
    
    4. Haga clic en **Aceptar** (marca). 


## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre cómo [implementar un servicio de nube](cloud-services-how-to-create-deploy.md).
* Configurar un [nombre de dominio personalizado](cloud-services-custom-domain-name.md).
* [Administrar el servicio de nube](cloud-services-how-to-manage.md).
* [Habilitar la conexión a Escritorio remoto para un rol de servicios de nube de Azure](cloud-services-role-enable-remote-desktop.md)
* Configurar [certificados ssl](cloud-services-configure-ssl-certificate.md).
