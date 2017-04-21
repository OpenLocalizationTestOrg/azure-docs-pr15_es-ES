<properties
    pageTitle="Solución de problemas de degradado estado en el administrador del tráfico de Azure"
    description="Cómo solucionar problemas de perfiles de tráfico Manager cuando se muestran como degradado estado."
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Solución de problemas de degradado estado en el administrador del tráfico de Azure

En este artículo se describe cómo solucionar problemas de un perfil de administrador de tráfico de Azure que se muestra un estado de degradado. En este escenario, considere la posibilidad de que ha configurado un perfil de administrador de tráfico que apunta a algunos de los servicios de cloudapp.net hospedado. Al comprobar el estado de su jefe de tráfico, verá que el estado está degradado.

![estado degradado](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degraded.png)

Si va a la ficha extremos de ese perfil, verá uno o varios de los extremos con un estado sin conexión:

![sin conexión](./media/traffic-manager-troubleshooting-degraded/traffic-manager-offline.png)

## <a name="understanding-traffic-manager-probes"></a>Descripción del Administrador de tráfico de sondeos

- Administrador de tráfico considera un extremo para ser en línea solo cuando el sondeo recibe una respuesta de HTTP 200 desde la ruta de acceso de sondeo. Otra respuesta 200 no es un error.
- Una redirección de 30 x falla, incluso si la dirección URL redirigida devuelve un 200.
- Para los sondeos HTTPs, se pasan por alto los errores de certificado.
- No importa el contenido real de la ruta de acceso de sondeo, siempre y cuando se devuelve un 200. Buscar una dirección URL a contenido estático como "/ favicon.ico" es una técnica común. Contenido dinámico, como las páginas ASP, no devuelvan siempre 200, incluso cuando la aplicación es correcto.
- Un procedimiento recomendado es establecer la ruta de acceso de sondeo algo que tiene suficiente lógica para determinar que el sitio está hacia arriba o hacia abajo. En el ejemplo anterior, mediante la configuración de la ruta de acceso "/ favicon.ico", sólo están pruebas que w3wp.exe está respondiendo. Este sondeo no puede indicar que la aplicación web es correcto. Una mejor opción sería establecer una ruta de acceso a un algo como "/ Probe.aspx" que tiene una lógica para determinar el estado del sitio. Por ejemplo, podría usar contadores de rendimiento de la CPU o medir el número de solicitudes de errores. O bien, podría intentar obtener acceso a recursos de base de datos o el estado de sesión para asegurarse de que funciona la aplicación web.
- Si se degradan todos los extremos de un perfil, a continuación, Administrador de tráfico trata todos los extremos como correcto y redirige el tráfico a todos los extremos. Este comportamiento garantiza que no surjan problemas con el mecanismo de sondeo en una completa interrupción del servicio.

## <a name="troubleshooting"></a>Solución de problemas

Para solucionar un error de sondeo, necesita una herramienta que muestra el código de estado HTTP devuelto desde la dirección URL de sondeo. Hay muchas herramientas que muestran la respuesta HTTP sin formato.

* [Fiddler](http://www.telerik.com/fiddler)
* [doblez](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Además, puede usar la pestaña de la red de las herramientas de depuración de F12 en Internet Explorer para ver las respuestas HTTP.

En este ejemplo desea ver la respuesta de nuestra dirección URL de sondeo: http://watestsdp2008r2.cloudapp.net:80/sondeo. En el siguiente ejemplo de PowerShell ilustra el problema.

```powershell
    Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Ejemplo de salida:

```text
    StatusCode StatusDescription
    ---------- -----------------
            301 Moved Permanently
```

Observe que hemos recibido una respuesta de redirección. Como se indicó anteriormente, cualquier StatusCode aparte de 200 se considera un error. Administrador de tráfico cambia el estado del extremo a sin conexión. Para resolver el problema, compruebe la configuración de sitio Web para asegurarse de que se puede devolver el StatusCode adecuada de la ruta de acceso de sondeo. Volver a configurar el sondeo de tráfico administrador para que apunten a una ruta de acceso que devuelve un 200.

Si usa el protocolo HTTPS el sondeo, debe deshabilitar el certificado de comprobación para evitar errores SSL/TLS durante la prueba. Las siguientes instrucciones de PowerShell deshabilitar la validación de certificado para la sesión actual de PowerShell:

```powershell
    add-type @"
    using System.Net;
    using System.Security.Cryptography.X509Certificates;
    public class TrustAllCertsPolicy : ICertificatePolicy {
        public bool CheckValidationResult(
        ServicePoint srvPoint, X509Certificate certificate,
        WebRequest request, int certificateProblem) {
        return true;
        }
    }
    "@
    [System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>Pasos siguientes

[Acerca de los métodos de enrutamiento de tráfico de tráfico administrador](traffic-manager-routing-methods.md)

[¿Qué es el Administrador de tráfico](traffic-manager-overview.md)

[Servicios de nube](http://go.microsoft.com/fwlink/?LinkId=314074)

[Aplicaciones Web de Azure](https://azure.microsoft.com/documentation/services/app-service/web/)

[Operaciones en el Administrador de tráfico (referencia de la API de REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlets de administrador de tráfico de Azure][1]

[1]: https://msdn.microsoft.com/library/mt125941(v=azure.200).aspx
