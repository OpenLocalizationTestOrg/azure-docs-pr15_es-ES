<properties
    pageTitle="Extremos de servicio de Azure"
    description="Describe la configuración de extremo de servicio de Azure en el Kit de herramientas de Azure para Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268600.aspx -->

# <a name="azure-service-endpoints"></a>Extremos de servicio de Azure #

Extremos de servicio Azure determinan si la aplicación se implementa en y administrada por la plataforma de Windows Azure global, Azure a través de 21Vianet en China o privadas plataforma Windows Azure. El cuadro de diálogo de **Extremos de servicio** le permite especificar qué extremos de servicio que desea usar. Para abrir el cuadro de diálogo de **Extremos de servicio** , dentro de Eclipse, haga clic en **ventana**, haga clic en **Preferencias**y, a continuación, expanda **Azure**y, a continuación, haga clic en **Los extremos de servicio**. Establecer el campo **Activo establecer** determina qué extremos de servicio de Azure se usará para los proyectos de Azure en el área de trabajo actual.

A continuación muestra el cuadro de diálogo de **Extremos de servicio** .

![][ic719493]

## <a name="to-set-the-service-endpoints"></a>Para establecer los extremos de servicio ##

En el cuadro de diálogo de **Extremos de servicio** , realice una de las siguientes acciones:

* Si desea usar la plataforma de Windows Azure global, en la lista desplegable **Establecer activa** , seleccione **windowsazure.com** y haga clic en **Aceptar**.
* Si desea usar Azure a través de 21Vianet en China, en la lista desplegable **Establecer activa** , seleccione **windowsazure.cn (China)** y haga clic en **Aceptar**.
* Si desea usar una plataforma de Windows Azure privada:
    1. Haga clic en **Editar**.
    2. Se abrirá un cuadro de diálogo que le informa de que se cierra el cuadro de diálogo de **Extremos de servicio** , y se abrirá el archivo de conjuntos de preferencias. Haga clic en **Aceptar**.
    3. En el archivo preferencesets.xml, cree una nueva `preferenceset` elemento. Para este elemento nuevo, cree `name`, `blob`, `management`, `portalURL` y `publishsettings` atributos y agregar valores que corresponden a la plataforma de Windows Azure privado. Puede usar los valores proporcionados para el existente `preferenceset` elementos como plantillas. **Nota**: el valor utilizado para el `blob` atributo debe contener el texto "blob" en la dirección URL.
    4. Guarde y cierre preferencesets.xml.
    5. Volver a abrir el cuadro de diálogo de **Extremos de servicio** .
    6. En la lista desplegable **Establecer activo** , seleccione el conjunto activo que ha creado y haga clic en **Aceptar**.
    7. Una vez que haya creado su plataforma de Windows Azure privada `preferenceset` elemento, puede cambiar los valores asignados a ella haciendo clic en el botón **Editar** en el cuadro de diálogo de **Extremo de los servicios** . También puede crear varios plataforma de Windows Azure privada `preferenceset` elementos, si así lo desea.

## <a name="see-also"></a>Vea también ##

[Kit de herramientas de Azure para Eclipse][]

[Instalar el Kit de herramientas de Azure Eclipse][] 

[Crear una aplicación del mundo Hola para Azure en Eclipse][]

Para obtener más información sobre el uso de Azure con Java, consulte el [Centro para desarrolladores de Java de Azure][].

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Crear una aplicación del mundo Hola para Azure en Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalar el Kit de herramientas de Azure Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719493]: ./media/azure-toolkit-for-eclipse-azure-service-endpoints/ic719493.png
