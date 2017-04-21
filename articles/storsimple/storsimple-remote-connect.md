<properties 
   pageTitle="Conectarse de forma remota al dispositivo StorSimple | Microsoft Azure"
   description="Se explica cómo configurar el dispositivo para la administración remota y cómo conectarse a Windows PowerShell para StorSimple a través de HTTP o HTTPS."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/21/2016"
   ms.author="alkohli" />

# <a name="connect-remotely-to-your-storsimple-device"></a>Conectarse a su dispositivo StorSimple de forma remota

## <a name="overview"></a>Información general

Puede usar Windows PowerShell remoto para conectarse a su dispositivo StorSimple. Cuando se conecte de esta manera, no verá un menú. (Verá un menú únicamente si usa la consola de serie en el dispositivo para conectar). Con Windows PowerShell remoto, debe conectarse a un espacio de ejecución específica. También puede especificar el idioma para mostrar. 

Para obtener más información sobre el uso remoto de Windows PowerShell para administrar el dispositivo, vaya a [Usar Windows PowerShell para StorSimple administrar el dispositivo de StorSimple](storsimple-windows-powershell-administration.md).

En este tutorial se explica cómo configurar el dispositivo para la administración remota y, a continuación, cómo conectarse a Windows PowerShell para StorSimple. Puede usar HTTP o HTTPS para conectarse a través de Windows PowerShell remoto. Sin embargo, cuando decida cómo conectarse a Windows PowerShell para StorSimple, considere lo siguiente: 

- Conectar directamente a la consola de serie de dispositivo es seguro, pero no conectarse a la consola serie a través de los modificadores de red está. Tenga cuidado de los riesgos de seguridad al conectarse a la consola de serie de dispositivo sobre conmutadores de red. 

- Conectar a través de una sesión HTTP puede ofrecer más seguridad que conectarse a través de la consola serie a través de la red. Aunque no es el método más seguro, es aceptable en redes de confianza. 

- Conectar a través de una sesión HTTPS con un certificado autofirmado es más segura y la opción recomendada.

Puede conectar remotamente a la interfaz de Windows PowerShell. Sin embargo, el acceso remoto al dispositivo StorSimple a través de la interfaz de Windows PowerShell no está habilitado de forma predeterminada. Debe habilitar la gestión remota en el dispositivo en primer lugar y, a continuación, en el cliente que se usa para acceder a su dispositivo.

Los pasos descritos en este artículo se realizaron en un sistema host que ejecutan Windows Server 2012 R2.

## <a name="connect-through-http"></a>Conectar a través de HTTP

Conectar con Windows PowerShell para StorSimple a través de una sesión HTTP ofrece más seguridad que conectan a través de la consola de serie de su dispositivo StorSimple. Aunque no es el método más seguro, es aceptable en redes de confianza.

Puede usar el portal de clásico Azure o la consola de serie para configurar la administración remota. Seleccione uno de los procedimientos siguientes:

- [Usar el portal de clásico Azure para habilitar la administración remota sobre HTTP](#use-the-azure-classic-portal-to-enable-remote-management-over-http)

- [Usar la consola de serie para habilitar la administración remota sobre HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

Después de habilitar la administración remota, utilice el procedimiento siguiente para preparar al cliente para una conexión remota.

- [Preparar al cliente para conexión remota](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-classic-portal-to-enable-remote-management-over-http"></a>Usar el portal de clásico Azure para habilitar la administración remota sobre HTTP 

Realice los pasos siguientes en el portal de Azure clásico para habilitar la administración remota sobre HTTP.

#### <a name="to-enable-remote-management-through-the-azure-classic-portal"></a>Para habilitar la administración remota a través del portal de clásico de Azure

1. Acceder a los **dispositivos** > **Configurar** para su dispositivo.

2. Desplácese hacia abajo hasta la sección **Administración remota** .

3. **Habilitar la administración remota** se establece en **Sí**.

4. Ahora puede conectar utilizando HTTP. (El valor predeterminado es para conectarse a través de HTTPS). Asegúrese de que HTTP está activada.

    >[AZURE.NOTE] Conectarse a través de HTTP es aceptable sólo en redes de confianza.

6. Haga clic en **Guardar** en la parte inferior de la página.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Usar la consola de serie para habilitar la administración remota sobre HTTP

Realice los pasos siguientes en la consola de serie de dispositivo para habilitar la administración remota.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Para habilitar la administración remota a través de la consola de serie de dispositivo

1. En el menú de consola serie, seleccione la opción 1. Para obtener más información sobre el uso de la consola de serie en el dispositivo, vaya a [conectarse a Windows PowerShell para StorSimple a través de la consola de serie de dispositivo](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console).

2. En el símbolo del sistema, escriba:`Enable-HcsRemoteManagement –AllowHttp`

3. Se le notificará sobre la vulnerabilidad de seguridad de uso HTTP para conectar el dispositivo. Cuando se le solicite, confirme escribiendo **Y**.

4. Compruebe que está habilitado HTTP escribiendo:`Get-HcsSystem`

5. Compruebe que el campo **RemoteManagementMode** muestra **HttpsAndHttpEnabled**. La siguiente ilustración muestra esta configuración en PuTTY.

     ![Serie HTTPS y HTTP habilitada](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Preparar al cliente para conexión remota

Realice los pasos siguientes en el cliente para habilitar la administración remota.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Para preparar al cliente para conexión remota

1. Iniciar una sesión de Windows PowerShell como administrador.

2. Escriba el comando siguiente para agregar la dirección IP del dispositivo StorSimple a la lista de hosts de confianza del cliente: 

     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`

     Reemplazar <*device_ip*> con la dirección IP de su dispositivo Por ejemplo: 

     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`

3. Escriba el comando siguiente para guardar las credenciales de dispositivo en una variable: 

     *$cred = get-Credential*

4. En el cuadro de diálogo que aparece:

    1. Escriba el nombre de usuario en este formato: *device_ip\SSAdmin*.
    2. Escriba la contraseña de administrador de dispositivos que se estableció cuando el dispositivo se ha configurado con el Asistente de configuración. La contraseña predeterminada es *Contraseña1*.

7. Escriba este comando para iniciar una sesión de Windows PowerShell en el dispositivo:

     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`

     >[AZURE.NOTE] Para crear una sesión de Windows PowerShell para usar con el dispositivo virtual StorSimple, anexar la `–Port` parámetro y especifique el puerto público que ha configurado en acceso remoto para dispositivo Virtual de StorSimple.

     En este momento, debe tener una sesión de Windows PowerShell remota activa al dispositivo.

    ![Remota de PowerShell utilizando HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Conectar a través de HTTPS

Conectar con Windows PowerShell para StorSimple a través de una sesión HTTPS es el método más seguro y recomendado de forma remota conectarse a su dispositivo de Microsoft Azure StorSimple. Los siguientes procedimientos explican cómo configurar los equipos cliente y consola serie de modo que puedan usar HTTPS para conectar con Windows PowerShell para StorSimple.

Puede usar el portal de clásico Azure o la consola de serie para configurar la administración remota. Seleccione uno de los procedimientos siguientes:

- [Usar el portal de clásico Azure para habilitar la administración remota a través de HTTPS](#use-the-azure-classic-portal-to-enable-remote-management-over-https)

- [Usar la consola de serie para habilitar la administración remota a través de HTTPS](#use-the-serial-console-to-enable-remote-management-over-https)

Después de habilitar la administración remota, utilice los siguientes procedimientos para preparar el host para la administración remota y conecte el dispositivo desde el host remoto.

- [Preparar el host para la administración remota](#prepare-the-host-for-remote-management)

- [Conectar con el dispositivo desde el host remoto](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-classic-portal-to-enable-remote-management-over-https"></a>Usar el portal de clásico Azure para habilitar la administración remota a través de HTTPS

Siga estos pasos en el portal de Azure clásico para habilitar la administración remota a través de HTTPS.

#### <a name="to-enable-remote-management-over-https-from-the-azure-classic-portal"></a>Para habilitar la administración remota a través de HTTPS desde el portal de clásico de Azure

1. Acceder a los **dispositivos** > **Configurar** para su dispositivo.

2. Desplácese hacia abajo hasta la sección **Administración remota** .

3. **Habilitar la administración remota** se establece en **Sí**.

4. Ahora puede conectarse usando HTTPS. (El valor predeterminado es para conectarse a través de HTTPS). Asegúrese de que está seleccionado HTTPS. 

5. Haga clic en **Descargar certificado de administración remota**. Especifique una ubicación para guardar este archivo. Debe instalar este certificado en el equipo cliente o host que usará para conectarse al dispositivo.

6. Haga clic en **Guardar** en la parte inferior de la página.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Usar la consola de serie para habilitar la administración remota a través de HTTPS

Realice los pasos siguientes en la consola de serie de dispositivo para habilitar la administración remota.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Para habilitar la administración remota a través de la consola de serie de dispositivo

1. En el menú de consola serie, seleccione la opción 1. Para obtener más información sobre el uso de la consola de serie en el dispositivo, vaya a [conectarse a Windows PowerShell para StorSimple a través de la consola de serie de dispositivo](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console).

2. En el símbolo del sistema, escriba: 

     `Enable-HcsRemoteManagement`

    Esto debe habilitar HTTPS en el dispositivo.

3. Compruebe que se ha habilitado HTTPS escribiendo: 

     `Get-HcsSystem`

    Asegúrese de que el campo **RemoteManagementMode** muestra **HttpsEnabled**. La siguiente ilustración muestra esta configuración en PuTTY.

     ![Serie HTTPS habilitado](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)

4. Desde la salida de `Get-HcsSystem`, copie el número de serie del dispositivo y guardar para su uso posterior.

    >[AZURE.NOTE] El número de serie asigna el nombre de CN en el certificado.

5. Obtener un certificado de administración remota escribiendo: 
 
     `Get-HcsRemoteManagementCert`

    Aparecerá un certificado similar al siguiente.

    ![Obtener certificado de administración remota](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)

5. Copie la información en el certificado de **---inicial certificado---** **---** finalizar certificado (...) en un editor de texto como el Bloc de notas y guardarlo como un archivo .cer. (Se copie este archivo en el host remoto al preparar el host.)

    >[AZURE.NOTE] Para generar un nuevo certificado, utilice la `Set-HcsRemoteManagementCert` cmdlet.

### <a name="prepare-the-host-for-remote-management"></a>Preparar el host para la administración remota

Para preparar el equipo host para una conexión remota que usa una sesión HTTPS, realice los siguientes procedimientos:

- [Importar el archivo .cer en el almacén raíz del cliente o host remoto](#to-import-the-certificate-on-the-remote-host).

- [Agregar los números de serie de dispositivo al archivo hosts en el host remoto](#to-add-device-serial-numbers-to-the-remote-host).

A continuación se describe cada uno de estos procedimientos.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Para importar el certificado en el host remoto

1. Haga clic en el archivo .cer y seleccione **Instalar certificado**. Se iniciará al Asistente para importación de certificados.

    ![Asistente para la importación de certificado 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)

2. **Ubicación del almacén**, seleccione el **Equipo Local**y, a continuación, haga clic en **siguiente**.

3. Seleccione **colocar todos los certificados en el siguiente almacén**y, a continuación, haga clic en **Examinar**. Vaya a la tienda de raíz de su host remoto y, a continuación, haga clic en **siguiente**.

    ![Asistente para la importación de certificado 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)

4. Haga clic en **Finalizar**. Aparece un mensaje que indica que la importación es correcta.

    ![Asistente para la importación de certificado 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Para agregar números de serie de dispositivo al host remoto

1. Iniciar el Bloc de notas como administrador y, a continuación, abra el archivo de hosts ubicado en \Windows\System32\Drivers\etc.

2. Agregar las tres siguientes entradas al archivo hosts: **dirección IP de datos 0**, **dirección IP fijo controlador 0**y **la dirección IP de controlador de 1 fijo**.

3. Escriba el número de serie de dispositivo que había guardado previamente. Asignar esto a la dirección IP tal como se muestra en la siguiente imagen. Para controlador 0 y 1 de controlador, anexar **Controller0** y **controlador1** al final del número de serie (nombre CN).

    ![Agregar nombre CN a archivo hosts](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)

4. Guardar el archivo hosts.

### <a name="connect-to-the-device-from-the-remote-host"></a>Conectar con el dispositivo desde el host remoto

Usar Windows PowerShell y SSL para escribir una sesión SSAdmin en el dispositivo de un host remoto o un cliente. La sesión de SSAdmin asigna a la opción 1 en el menú de [consola serie](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console) de su dispositivo.

Siga el procedimiento siguiente en el equipo del que desea realizar la conexión de Windows PowerShell remota.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-ssl"></a>Para especificar una sesión SSAdmin en el dispositivo con Windows PowerShell y SSL

1. Iniciar una sesión de Windows PowerShell como administrador.

2. Agregar la dirección IP del dispositivo para hosts de confianza del cliente, escriba:

     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`

    Donde <*device_ip*> es la dirección IP de su dispositivo Por ejemplo: 

     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`

3. Crear una nueva credencial escribiendo: 

     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`

    Donde <*IP del dispositivo de destino*> es la dirección IP de datos 0 para su dispositivo; Por ejemplo, **10.126.173.90** como se muestra en la imagen anterior del archivo hosts. Además, proporcione la contraseña de administrador para su dispositivo.

4. Crear una sesión escribiendo:

     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`

    Para el parámetro - ComputerName en el cmdlet, proporcionar el <*número de serie de dispositivo de destino*>. Este número de serie que se ha asignado a la dirección IP de datos 0 en el archivo hosts en el host remoto; Por ejemplo, **SHX0991003G44MT** tal como se muestra en la siguiente imagen.

5. Tipo: 

     `Enter-PSSession $session`

6. Tendrán que esperar unos minutos y, a continuación, se conectará al dispositivo a través de HTTPS con SSL. Verá un mensaje que indica que está conectados a su dispositivo.

    ![Remota de PowerShell con HTTPS y SSL](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre cómo [usar Windows PowerShell para administrar el dispositivo de StorSimple](storsimple-windows-powershell-administration.md).

- Más información sobre cómo [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).
