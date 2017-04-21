<properties 
    pageTitle="Nombre del emisor y clave de emisor en servicios de BizTalk | Microsoft Azure" 
    description="Obtenga información sobre cómo recuperar el nombre del emisor y clave emisor para Bus de servicio o Control de acceso (ACS) en servicios de BizTalk. MABS, WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="mandia"/>




# <a name="biztalk-services-issuer-name-and-issuer-key"></a>Servicios de BizTalk: Nombre del emisor y la clave del emisor

Servicios de BizTalk Azure usa el nombre del emisor de Bus de servicio y clave emisor y el nombre del emisor de Control de acceso y la clave de emisor. Específicamente:

Tarea | Qué nombre del emisor y la clave del emisor
--- | ---
Implementación de la aplicación de Visual Studio | Nombre del emisor de Control de acceso y la clave de emisor
Configuración del Portal de servicios de BizTalk Azure | Nombre del emisor de Control de acceso y la clave de emisor
Creación de transmisiones LOB con los servicios del adaptador BizTalk en Visual Studio | Nombre del emisor de Bus de servicio y clave emisor

Este tema enumeran los pasos para recuperar el nombre del emisor y la clave de emisor. 

## <a name="access-control-issuer-name-and-issuer-key"></a>Nombre del emisor de Control de acceso y la clave de emisor
El nombre del emisor de Control de acceso y la clave de emisor usa la siguiente:

- La aplicación de servicio de Azure BizTalk creada en Visual Studio: para implementar correctamente la aplicación BizTalk Service en Visual Studio para Azure, escriba el nombre del emisor de Control de acceso y la clave de emisor. 
- El Portal de servicios de Azure BizTalk: Al crear un BizTalk Service y abra el Portal de servicios de BizTalk, el nombre del emisor de Control de acceso y la clave de emisor se registran automáticamente para las implementaciones con los mismos valores de Control de acceso.

### <a name="to-copy-and-paste-the-access-control-issuer-name-and-issuer-key"></a>Para copiar y pegar la clave del emisor y el nombre del emisor de Control de acceso

1. Inicie sesión en el [portal de clásico de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En el panel de navegación izquierdo, seleccione **Servicios de BizTalk**.
3. Seleccione el servicio de BizTalk. 
4. Seleccione la **Información de conexión** en la barra de tareas. El Namespace de Control de acceso predeterminada emisor (nombre del emisor) y predeterminado clave (emisor) se muestran y pueden copiar y pegar.  

En resumen:  
Nombre del emisor = emisor de forma predeterminada  
Clave emisor = clave predeterminada


También puede seleccionar **Abrir Portal de administración de ACS** para obtener los valores de Control de acceso:

1. Inicie sesión en el [portal de clásico de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En el panel de navegación izquierdo, seleccione **Servicios de BizTalk**.
3. Seleccione el servicio de BizTalk.
4. Seleccione el botón de información de conexión y seleccione **Abrir Portal de administración de ACS**.
5. En el Portal de **Configuración del servicio**, seleccione **Las identidades de servicio**. Muestra la identidad del servicio, que es el valor de nombre del emisor de Control de acceso. Seleccione el vínculo de identidad de servicio para ver la contraseña, que es el valor de clave del emisor. Se pueden copiar los valores.<br/><br/>
Por ejemplo, **Las identidades de servicio**, consulte "propietario". "Propietario" es el nombre del emisor de Control de acceso. Al hacer clic en el vínculo "propietario", verá la **contraseña**. Al hacer clic en el vínculo "Contraseña", verá el valor. Este valor de contraseña es la clave de emisor de Control de acceso.  

En resumen:  
Nombre del emisor = nombre de la identidad de servicio  
Clave emisor = valor de contraseña

En el panel de navegación izquierdo, también puede seleccionar **Active Directory** para recuperar los valores de Control de acceso. 

> [AZURE.IMPORTANT]Cuando se crea un Namespace de Control de acceso con **Active Directory**, la identidad de un servicio **no** se crea automáticamente. Cuando aprovisionar un BizTalk Service, un Namespace de Control de acceso, la identidad del servicio había denominado "propietario" (nombre del emisor), contraseña (clave emisor) y clave simétrica se crean automáticamente.<br /> 
[Cómo: servicio de administración de ACS usar para configurar las identidades de servicio](http://go.microsoft.com/fwlink/p/?LinkID=303942) proporciona más información sobre las identidades de servicio de Control de acceso.


## <a name="service-bus-issuer-name-and-issuer-key"></a>Nombre del emisor de Bus de servicio y clave emisor
Nombre del emisor de Bus de servicio y clave emisor utiliza servicios del adaptador BizTalk. En el proyecto de servicios de BizTalk en Visual Studio, se utilizan los servicios del adaptador BizTalk para conectarse a un sistema de línea de negocio (LOB) local. Para conectarse, cree la retransmisión LOB y, a continuación, escriba los detalles del sistema LOB. Al hacerlo, también se escriba el nombre del emisor de Bus de servicio y la clave de emisor.

### <a name="to-retrieve-the-service-bus-issuer-name-and-issuer-key"></a>Para recuperar el nombre del emisor de Bus de servicio y la clave de emisor

1. Inicie sesión en el [portal de clásico de Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. En el panel de navegación izquierdo, seleccione **Bus de servicio**.
3. Seleccione el espacio de nombres. En la barra de tareas, seleccione **Información de conexión**. Muestra el **Emisor predeterminado** (nombre del emisor) y la **Clave predeterminado** (clave de emisor). Se pueden copiar los valores.  

En resumen:  
Nombre del emisor = emisor de forma predeterminada  
Clave emisor = clave predeterminada

## <a name="next"></a>Siguiente
Temas de Azure BizTalk servicios adicionales:

-  [Instalación de los servicios de BizTalk Azure SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Tutoriales: Servicios de Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [¿Cómo puedo comenzar a utilizar el SDK de servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Servicios de Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>


## <a name="see-also"></a>Vea también
-  [Cómo: usar el servicio de administración de ACS para configurar las identidades de servicio](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
- [Servicios de BizTalk: Developer, Basic, estándar y Premium ediciones gráfico](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [Servicios de BizTalk: Portal clásico de Azure usando el aprovisionamiento](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Servicios de BizTalk: Gráfico de estado de aprovisionamiento](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [Servicios de BizTalk: Fichas de paneles, el Monitor y la escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Servicios de BizTalk: Copia de seguridad y restauración](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Servicios de BizTalk: límite](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
 
