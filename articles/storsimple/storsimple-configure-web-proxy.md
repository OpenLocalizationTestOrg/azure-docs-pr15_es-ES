<properties 
   pageTitle="Configurar el servidor proxy de web para un dispositivo StorSimple | Microsoft Azure"
   description="Aprenda a usar Windows PowerShell para StorSimple para establecer la configuración de proxy de web de su dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="configure-web-proxy-for-your-storsimple-device"></a>Configurar a proxy de web de su dispositivo StorSimple

## <a name="overview"></a>Información general

Este tutorial describe cómo usar Windows PowerShell para StorSimple para configurar y ver la configuración de proxy de web de su dispositivo StorSimple. La configuración de proxy de web se usa el dispositivo StorSimple cuando se comunica con la nube. Un servidor proxy web se usa para agregar otro nivel de seguridad, filtro de contenido, caché a requisitos de ancho de banda de facilidad o incluso ayuda con el análisis.

Proxy Web es una configuración opcional de su dispositivo StorSimple. Puede configurar el proxy de web sólo a través de Windows PowerShell para StorSimple. La configuración es un proceso de dos pasos:

1. Configurar la configuración de proxy web mediante el Asistente de configuración o Windows PowerShell para StorSimple cmdlets.

2. A continuación, habilite a la configuración de proxy de web configurado a través de Windows PowerShell para StorSimple cmdlets.

Una vez completada la configuración de proxy de web, puede ver la configuración de proxy de web configurado en el servicio de Microsoft Azure StorSimple Manager y Windows PowerShell para StorSimple. 

Después de leer este tutorial, podrá:

- Configurar a proxy web mediante cmdlets y el Asistente de configuración
- Habilitar a proxy web mediante el uso de cmdlets
- Ver la configuración de proxy web en el portal de clásico de Azure
- Solucionar problemas durante la configuración de proxy de web


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Configurar el proxy de web a través de Windows PowerShell para StorSimple

Use uno de estos pasos para configurar la configuración de proxy de web:

- Asistente de configuración que le guiará por los pasos de configuración.

- Cmdlets de Windows PowerShell para StorSimple.

Cada uno de estos métodos se describe en las secciones siguientes.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Configurar a proxy web mediante el Asistente de configuración

Puede usar al Asistente de configuración que le guiará por los pasos de configuración de proxy de web. Siga estos pasos para configurar a proxy web en el dispositivo.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Para configurar el proxy web mediante el Asistente de configuración

1. En el menú de consola serie, seleccione la opción 1, **inicie sesión con acceso completo** y proporcione la **contraseña de administrador de dispositivos**. Escriba el comando siguiente para iniciar una sesión del Asistente de configuración:

    `Invoke-HcsSetupWizard`

2. Si es la primera vez que se ha utilizado al Asistente de configuración de registro del dispositivo, debe configurar la configuración de red requerido hasta llegar a la configuración de proxy de web. Si el dispositivo ya está registrado, puede aceptar la configuración de red configuradas hasta llegar a la configuración de proxy de web. En el Asistente de configuración, cuando se le solicite configurar la configuración de proxy de web, escriba **Sí**.

3. **Dirección URL de Proxy de Web**, especifique la dirección IP o el nombre de dominio completo (FQDN) de su servidor proxy de web y el número de puerto TCP que desea que el dispositivo para usar cuando se comunica con la nube. Use el siguiente formato:

    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`

    De forma predeterminada, se especifica el número de puerto TCP 8080.

4. Elija el tipo de autenticación como **NTLM**, **básica**o **Ninguno**. Básica es la autenticación menos seguro para la configuración del servidor proxy. NT LAN Manager (NTLM) es un protocolo de autenticación complejo y muy segura que usa un sistema de mensajería de triple (a veces cuatro si se requiere la integridad adicional) para autenticar un usuario. La autenticación predeterminada es NTLM. Para obtener más información, vea [básica](http://hc.apache.org/httpclient-3.x/authentication.html) y la [autenticación de NTLM](http://hc.apache.org/httpclient-3.x/authentication.html). 

    > [AZURE.IMPORTANT] **En el servicio de administrador de StorSimple, los gráficos de supervisión de dispositivo no funcionan cuando básica o la autenticación NTLM está habilitada en la configuración del servidor proxy para el dispositivo. Para que los gráficos de supervisión para que funcione, debe asegurarse de que la autenticación se establece en ninguno.**

5. Si está utilizando autenticación, proporcione un **Nombre de usuario de Proxy de Web** y una **Contraseña de Proxy de Web**. También necesitará confirme la contraseña.

    ![Configurar a Proxy Web en StorSimple dispositivo1](./media/storsimple-configure-web-proxy/IC751830.png)

Si va a registrar el dispositivo por primera vez, continúe con el registro. Si el dispositivo ya se ha registrado, el asistente se cerrará. Se guardará la configuración.

También ahora se habilitarán proxy Web. Puede omitir el paso [Habilitar proxy web](#enable-web-proxy) y vaya directamente a [la configuración de proxy de web de vista en el portal de clásico de Azure](#view-web-proxy-settings-in-the-azure-classic-portal).


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Configurar a proxy web a través de Windows PowerShell para los cmdlets StorSimple

Una forma alternativa para configurar la configuración de proxy web es a través de Windows PowerShell para StorSimple cmdlets. Siga estos pasos para configurar el servidor proxy de web.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Configurar proxy web a través de los cmdlets

1. En el menú de consola serie, seleccione la opción 1, **inicie sesión con acceso completo**. Cuando se le solicite, proporcione la **contraseña de administrador de dispositivos**. La contraseña predeterminada es `Password1`.

2. En el símbolo del sistema, escriba:

    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`

    Proporcione y confirme la contraseña cuando se le solicite, tal como se muestra a continuación.

    ![Configurar a Proxy Web en StorSimple Device3](./media/storsimple-configure-web-proxy/IC751831.png)

El proxy web está configurado ahora y debe habilitarse.

## <a name="enable-web-proxy"></a>Habilitar a proxy web

Proxy Web está deshabilitado de forma predeterminada. Después de configurar la configuración de proxy web en el dispositivo StorSimple, debe usar Windows PowerShell para StorSimple para habilitar a la configuración de proxy de web.

> [AZURE.NOTE] **Este paso no se requiere si utiliza al Asistente de configuración para configurar un proxy web. Proxy Web predeterminada está habilitado automáticamente después de una sesión del Asistente de configuración.**

En Windows PowerShell para StorSimple Habilitar proxy de web en el dispositivo, siga estos pasos:

#### <a name="to-enable-web-proxy"></a>Para habilitar a proxy web

1. En el menú de consola serie, seleccione la opción 1, **inicie sesión con acceso completo**. Cuando se le solicite, proporcione la **contraseña de administrador de dispositivos**. La contraseña predeterminada es `Password1`.

2. En el símbolo del sistema, escriba:

    `Enable-HcsWebProxy`

    Ahora ha habilitado la configuración de proxy web en el dispositivo StorSimple.

    ![Configurar a Proxy Web en StorSimple Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-classic-portal"></a>Ver la configuración de proxy web en el portal de clásico de Azure

La configuración de proxy web se configura a través de la interfaz de Windows PowerShell y no se puede cambiar desde el portal clásico. Sin embargo, puede ver los valores configurados en el portal clásico. Realice los pasos siguientes para ver a proxy web.

#### <a name="to-view-web-proxy-settings"></a>Para ver la configuración de proxy de web
1. Vaya a **servicio Administrador StorSimple > dispositivos**. Seleccione y haga clic en un dispositivo y, a continuación, vaya a **Configurar**.
1. Desplácese hacia abajo en **la página a la sección **configuración de proxy de Web** ** . Puede ver la configuración de proxy de web configurado en el dispositivo StorSimple tal como se muestra a continuación.

    ![Vista Web Proxy en el Portal de administración](./media/storsimple-configure-web-proxy/ViewWebProxyPortal_M.png)
 
## <a name="errors-during-web-proxy-configuration"></a>Errores durante la configuración de proxy de web

Si la configuración de proxy web se han configurado correctamente, se mostrará mensajes de error al usuario de Windows PowerShell para StorSimple. La siguiente tabla explica algunos de estos mensajes de error probables y sus acciones recomendadas.

|Nº de serie.|Código de error HRESULT|Causa posible|Acción recomendada|
|:---|:---|:---|:---|
|1.|0x80070001|Se ejecuta el comando desde el controlador de pasivo y no puede comunicarse con el controlador de activo.|Ejecute el comando en el controlador de activo. Para ejecutar el comando desde el controlador de pasivo, debe corregir la conectividad de pasivo con controlador active. Debe contratar Microsoft Support si esta conectividad ha quedado rota.|
|2.|0x800710dd: el identificador de operación no es válido|Configuración de proxy no es compatibles en StorSimple virtual dispositivo.|Configuración de proxy no es compatibles en StorSimple virtual dispositivo. Estas solo se pueden configurar en un dispositivo físico de StorSimple.|
|3.|0 x 80070057 - parámetro no válido|Uno de los parámetros proporcionados para la configuración de proxy no es válido.|La URI no está disponible en el formato correcto. Use el siguiente formato:`http://<IP address or FQDN of the web proxy server>:<TCP port number>`|
|4.|0x800706ba - servidor RPC no disponible|La causa es una de las siguientes acciones:</br></br>Clúster no está activa.</br></br>No se está ejecutando el servicio DataPath.</br></br>Se ejecuta el comando de controlador pasivo y no puede comunicarse con el controlador de activo.|Por favor, comuníquese con Microsoft Support para asegurarse de que el clúster está activo y datapath servicio se está ejecutando.</br></br>Ejecutar el comando desde el controlador activo. Si desea ejecutar el comando desde el controlador pasivo, debe asegurarse de que el controlador de pasivo puede comunicarse con el controlador de active. Debe contratar Microsoft Support si esta conectividad ha quedado rota.|
|5.|0X800706be: error de llamada RPC|Clúster es hacia abajo.|Por favor, integrarse Microsoft Support para asegurarse de que el clúster hacia arriba.|
|6.|0x8007138f - no se encuentra el recurso de clúster|No se encuentra el recurso de clúster de servicio de plataforma. Esto puede ocurrir cuando la instalación no es correcta.|Debe realizar una fábrica restablecer en el dispositivo. Debe crear un recurso de plataforma. Póngase en contacto con Microsoft Support para los pasos siguientes.|
|7.|0x8007138c - recursos de clúster sin conexión|Recursos de clúster de plataforma o datapath no están en línea.|Póngase en contacto con Microsoft Support para ayudar a garantizar que los recursos del servicio datapath y plataforma están en línea.|

> [AZURE.NOTE] 
> 
> -  La lista de mensajes de error anterior no es exhaustiva. 
> - Errores relacionados con la configuración de proxy web no se mostrará en el portal de clásico Azure en el servicio de administrador de StorSimple. Si hay un problema con el servidor proxy de web una vez completada la configuración, el estado del dispositivo cambiará a **sin conexión** en el portal de clásico. |

## <a name="next-steps"></a>Pasos siguientes

- Si experimenta problemas durante la implementación de su dispositivo o configuración de proxy de web, consulte [solucionar problemas de la implementación de dispositivo StorSimple](storsimple-troubleshoot-deployment.md).

- Para obtener información sobre cómo usar el servicio Administrador de StorSimple, vaya a [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).
