<properties 
    pageTitle="Solución de problemas de análisis - la herramienta de búsqueda eficaces de aplicación perspectivas | Microsoft Azure" 
    description="¿Problemas con el análisis de información de la aplicación? Empiece aquí. " 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/11/2016" 
    ms.author="awills"/>


# <a name="troubleshoot-analytics-in-application-insights"></a>Solucionar problemas de análisis en la aplicación perspectivas


¿Problemas con la [aplicación perspectivas análisis](app-insights-analytics.md)? Empiece aquí. El análisis es la herramienta de búsqueda eficaz de perspectivas de aplicación de Visual Studio.



## <a name="limits"></a>Límites

* En la actualidad, resultados de la consulta se limitan a solo durante una semana de los últimos datos.
* Probemos en los exploradores: últimas ediciones de Chrome, borde e Internet Explorer.


## <a name="known-incompatible-browser-extensions"></a>Extensiones de explorador incompatibles conocidos

* Ghostery

Deshabilitar la extensión o use un explorador diferente.


##<a name="e-a"></a>"Error inesperado"

![Pantalla de error inesperado](./media/app-insights-analytics-troubleshooting/010.png)

Error interno en tiempo de ejecución de portal: excepción no controlada.

* Limpie la caché del explorador. 

## <a name="e-b"></a>403... Intente volver a cargar

![403... Intente volver a cargar](./media/app-insights-analytics-troubleshooting/020.png)

Una autenticación relacionados con el se ha producido un error (durante la autenticación o durante la generación de token de acceso). El portal no puede tener ninguna forma de recuperar sin cambiar la configuración del explorador.

* Compruebe [las cookies de terceros están habilitadas](#cookies) en el explorador. 


## <a name="authentication"></a>403... Compruebe la zona de seguridad

![403.. .verify zona de seguridad](./media/app-insights-analytics-troubleshooting/030.png)

Una autenticación relacionados con el se ha producido un error (durante la autenticación o durante la generación de token de acceso). El portal no puede tener ninguna forma de recuperar sin cambiar la configuración del explorador.

1. Compruebe [las cookies de terceros están habilitadas](#cookies) en el explorador. 

2. ¿Ha usado un favorito, el marcador o el vínculo guardado para abrir el portal de análisis? ¿Se ha iniciado sesión con credenciales diferentes a las que utilizó cuando guarda el vínculo?

2. Pruebe a usar una ventana de explorador privada o incógnito (después de cerrar todas las ventanas de este tipo). Debe proporcionar sus credenciales. 

2. Abrir otra ventana del explorador (normal) y vaya a [Azure](https://portal.azure.com). Cerrar sesión. Abra el vínculo e inicie sesión con las credenciales correctas.

2. Los usuarios de borde e Internet Explorer también pueden obtener este error cuando no se admite la configuración de zona de confianza.

    Comprobar el [portal de análisis](https://analytics.applicationinsights.io) y [portal de Azure Active Directory](https://portal.azure.com) se encuentran en la misma zona de seguridad:

 * En Internet Explorer, abra **Opciones de Internet**, **seguridad**, **sitios de confianza**, **sitios**:

    ![Cuadro de diálogo Opciones de Internet, agregar un sitio a los sitios de confianza](./media/app-insights-analytics-troubleshooting/033.png)

    En la lista de sitios Web, si cualquiera de las siguientes direcciones URL se incluyen, asegúrese de que los demás se incluyen también:

    https://Analytics.applicationinsights.IO<br/>
   https://Login.microsoftonline.com<br/>
   https://Login.Windows.NET


## <a name="e-d"></a>404... No se encuentra el recurso

![... 404 no se encuentra el recurso](./media/app-insights-analytics-troubleshooting/040.png)

Recursos de la aplicación se ha eliminado de la información de la aplicación y ya no está disponible. Esto puede deberse a que ha guardado la dirección URL a la página de análisis.


## <a name="e-e"></a>403... Sin autorización

![403... no autorizado](./media/app-insights-analytics-troubleshooting/050.png)

No tiene permiso para abrir esta aplicación en el análisis.

* ¿Aparece el vínculo de otra persona? Pídales para asegurarse de que se encuentra en [los lectores o colaboradores para este grupo de recursos](app-insights-resources-roles-access-control.md).
* ¿Guardó el vínculo con las credenciales diferentes? Abra el [portal de Azure](https://portal.azure.com), cerrar la sesión y vuelva a intentar este vínculo nuevo, lo que proporciona las credenciales correctas.

## <a name="html-storage"></a>403... Almacenamiento de HTML5

Nuestro portal usa sessionStorage y localStorage de HTML5.

* Chrome: Configuración de privacidad, la configuración de contenido.
* Internet Explorer: Opciones de Internet, ficha Opciones avanzadas, seguridad, habilitar almacenamiento DOM.


![403... intenta habilitar almacenamiento HTML5](./media/app-insights-analytics-troubleshooting/060.png)

## <a name="e-g"></a>404... Suscripción no encontrado


![404... Suscripción no encontrado](./media/app-insights-analytics-troubleshooting/070.png)

La dirección URL no es válida. 

* Abra el recurso de aplicación en [el portal de información de la aplicación](https://portal.azure.com). A continuación, use el botón análisis.

## <a name="e-h"></a>404... no existe la página

![404... No existe la página](./media/app-insights-analytics-troubleshooting/080.png)

La dirección URL no es válida.

* Abra el recurso de aplicación en [el portal de información de la aplicación](https://portal.azure.com). A continuación, use el botón análisis.

## <a name="cookies"></a>Habilitar las cookies de terceros

  Consulte [cómo deshabilitar las cookies de terceros](http://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers), pero observe que son necesarias para **Habilitar** .

## <a name="e-x"></a>Si se produce un error en todo lo demás    

[Póngase en contacto con nosotros](app-insights-get-dev-support.md).
 
[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

