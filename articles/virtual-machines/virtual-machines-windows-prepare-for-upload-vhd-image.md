<properties
    pageTitle="Preparar un disco duro virtual de Windows para cargar en Azure | Microsoft Azure"
    description="Prácticas recomendadas para preparar un disco duro virtual de Windows antes de enviarla a Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="genlin"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/18/2016"
    ms.author="glimoli;genli"/>

# <a name="prepare-a-windows-vhd-to-upload-to-azure"></a>Preparar un disco duro virtual de Windows para cargar en Azure
Para cargar una máquina virtual de Windows locales a Azure, debe preparar correctamente el disco duro virtual (VHD). Existen varios pasos recomendados para completar antes de cargar un disco duro virtual en Azure. Este artículo le muestra cómo preparar un disco duro virtual de Windows para cargar en Microsoft Azure, y también se explica [cuándo y cómo usar Sysprep](#step23).

## <a name="prepare-the-virtual-disk"></a>Preparar el disco virtual

>[AZURE.NOTE] 
> Azure solo es compatible con [máquinas virtuales de generación 1](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx) que están en el formato de archivo de disco duro virtual. El formato más reciente de VHDX no es compatible con Azure. 
>
> El disco duro virtual debe tener un tamaño fijo, no dinámico. Si es necesario, las siguientes instrucciones detallan conversión de discos VHDX o dinámica. El tamaño máximo permitido para el disco duro virtual es 1.023 GB.


Asegúrese de que el disco duro virtual de Windows funciona correctamente en el servidor local. Resolver los errores dentro de la máquina virtual propio antes de intentar convertir o cargar en Azure.

Si necesita convertir el disco virtual en el formato requerido para Azure, use uno de los métodos especificados en las secciones siguientes. Copia de la máquina virtual antes de ejecutar cualquier proceso de conversión de disco virtual o Sysprep.

### <a name="convert-using-hyper-v-manager"></a>Convertir con el Administrador de Hyper-V
- Abrir el Administrador de Hyper-V y seleccione el equipo local a la izquierda. En el menú por encima, haga clic en **acción** > **Editar disco**.
    - En la pantalla de **disco duro Virtual busque** , busque y seleccione el disco virtual.
    - Seleccione **convertir** en la siguiente pantalla
        - Si necesita convertir de VHDX, seleccione el **disco duro virtual** y haga clic en **siguiente**
        - Si necesita convertir de disco dinámico, seleccione **tamaño fijo** y haga clic en **siguiente**

    - Busque y seleccione la **ruta de acceso para el nuevo archivo de disco duro virtual**.
    - Haga clic en **Finalizar** para cerrar.

### <a name="convert-using-powershell"></a>Convertir con PowerShell
Puede convertir un disco virtual mediante el [cmdlet de PowerShell de disco duro virtual de convertir](http://technet.microsoft.com/library/hh848454.aspx). En el ejemplo siguiente, que estamos convertir un VHDX en el disco duro virtual y convertir de dinámico a tipo fijo:

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

### <a name="convert-from-vmware-vmdk-disk-format"></a>Convertir formato de disco VMware VMDK
Si tiene una imagen de máquina virtual de Windows en el [formato de archivo VMDK](https://en.wikipedia.org/wiki/VMDK), convertir en un disco duro virtual con el [Conversor de máquina Virtual de Microsoft](https://www.microsoft.com/download/details.aspx?id=42497). Lea el blog de [cómo convertir un VMDK VMware a disco duro virtual de Hyper-V](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) para obtener más información.

## <a name="prepare-windows-configuration-for-upload"></a>Preparar la configuración de Windows para cargar

> [AZURE.NOTE] Ejecute los siguientes comandos con [privilegios de administrador](https://technet.microsoft.com/library/cc947813.aspx).

1. Quitar cualquier ruta estática persistente en la tabla de enrutamiento:

    - Para ver la tabla de rutas, ejecute `route print`.
    - Consulte las secciones de **Las rutas de persistencia** . Si hay una ruta persistente, use la [ruta eliminar](https://technet.microsoft.com/library/cc739598.apx) para quitarla.

2. Quitar al proxy WinHTTP:

    ```
    netsh winhttp reset proxy
    ```

3. Configurar la directiva SAN [Onlineall](https://technet.microsoft.com/library/gg252636.aspx)de disco:

    ```
    diskpart san policy=onlineall
    ```

4. Usar la hora de la hora Universal coordinada (UTC) para Windows y establecer el tipo de inicio del servicio de hora de Windows (w32time) a **automáticamente**:

    ```
    REG ADD HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
    sc config w32time start= auto
    ```


## <a name="configure-windows-services"></a>Configurar los servicios de Windows
5. Asegúrese de que cada uno de los siguientes servicios de Windows se establece en los **valores predeterminados de Windows**. Que están configurados con la configuración de inicio que se indica en la siguiente lista. Puede ejecutar estos comandos para restablecer la configuración de inicio:

    ```
    sc config bfe start= auto

    sc config dcomlaunch start= auto

    sc config dhcp start= auto

    sc config dnscache start= auto

    sc config IKEEXT start= auto

    sc config iphlpsvc start= auto

    sc config PolicyAgent start= demand

    sc config LSM start= auto

    sc config netlogon start= demand

    sc config netman start= demand

    sc config NcaSvc start= demand

    sc config netprofm start= demand

    sc config NlaSvc start= auto

    sc config nsi start= auto

    sc config RpcSs start= auto

    sc config RpcEptMapper start= auto

    sc config termService start= demand

    sc config MpsSvc start= auto

    sc config WinHttpAutoProxySvc start= demand

    sc config LanmanWorkstation start= auto

    sc config RemoteRegistry start= auto
    ```


## <a name="configure-remote-desktop-configuration"></a>Configurar la configuración de escritorio remoto
6. Si hay cualquier certificado autofirmado que están vinculada a la escucha de protocolo de escritorio remoto (RDP), elimínelo:

    ```
    REG DELETE "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp\SSLCertificateSHA1Hash”
    ```

    Para obtener más información sobre la configuración de certificados para escucha RDP, vea [Configuraciones de certificado de escucha en Windows Server](https://blogs.technet.microsoft.com/askperf/2014/05/28/listener-certificate-configurations-in-windows-server-2012-2012-r2/)

7. Configurar los valores de la [actividad](https://technet.microsoft.com/library/cc957549.aspx) de servicio RDP:

    ```
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveEnable /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v KeepAliveInterval /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v KeepAliveTimeout /t REG_DWORD /d 1 /f
    ```

8. Configurar el modo de autenticación para el servicio RDP:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD  /d 1 /f

    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD  /d 1 /f
    ```

9. Activar el servicio de RDP agregando las siguientes subclaves del registro:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD  /d 0 /f
    ```


## <a name="configure-windows-firewall-rules"></a>Configurar reglas de Firewall de Windows
10. Permitir WinRM a través de los tres perfiles de firewall (dominio, privado y público) y habilitar el servicio remota de PowerShell:

    ```
    Enable-PSRemoting -force
    ```

11. Asegúrese de que son las siguientes reglas de firewall del sistema operativo de invitado en su lugar:

    - Entrada

    ```
    netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (LLMNR-UDP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Datagram-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (NB-Name-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (Pub-WSD-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (SSDP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (UPnP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Network Discovery (WSD EventsSecure-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes

    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
    ```

    - Entrante y saliente

    ```
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes

    netsh advfirewall firewall set rule group="Core Networking" new enable=yes
    ```

    - Saliente

    ```
    netsh advfirewall firewall set rule dir=out name="Network Discovery (LLMNR-UDP-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Datagram-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (NB-Name-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (Pub-WSD-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (SSDP-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnPHost-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (UPnP-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD Events-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD EventsSecure-Out)" new enable=yes

    netsh advfirewall firewall set rule dir=out name="Network Discovery (WSD-Out)" new enable=yes
    ```


## <a name="additional-windows-configuration-steps"></a>Pasos adicionales de configuración de Windows
12. Ejecutar `winmgmt /verifyrepository` para confirmar que el repositorio de instrumentación de administración de Windows (WMI) es coherente. Si el repositorio está dañado, consulte [esta entrada de blog](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

13. Asegúrese de que la configuración de datos de configuración de arranque (BDC) coincide con lo siguiente:

    ```
    bcdedit /set {bootmgr} integrityservices enable

    bcdedit /set {default} device partition=C:

    bcdedit /set {default} integrityservices enable

    bcdedit /set {default} recoveryenabled Off

    bcdedit /set {default} osdevice partition=C:

    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
    ```

14. Quite los filtros de interfaz de controlador de transporte adicionales, como el software que analiza los paquetes TCP.
15. Para asegurarse de que el disco está correcto y coherentes, ejecute el `CHKDSK /f` comando.
16. Desinstale cualquier otro software de terceros y controlador de componentes físicos o cualquier otra tecnología de virtualización.
17. Asegúrese de que una aplicación de terceros no está usando el puerto 3389. Este puerto se usa para el servicio RDP en Azure. Puede usar el `netstat -anob` comando para comprobar los puertos que usan las aplicaciones.
18. Si el disco duro virtual de Windows que desea cargar no es un controlador de dominio, siga [estos pasos adicionales](https://support.microsoft.com/kb/2904015) para preparar el disco.
19. Reinicie la máquina virtual para asegurarse de que Windows es correcto aún puede ponerse mediante el uso de la conexión RDP.
20. Restablecer la contraseña de administrador local actual y asegúrese de que puede usar esta cuenta para iniciar sesión en Windows a través de la conexión RDP.  Este permiso de acceso se controla mediante el objeto de directiva "Permitir registro a través de servicios de escritorio remoto". Este objeto se encuentra debajo de "Equipo Configuración Windows\Configuración seguridad\Directivas asignación de directivas derechos."


## <a name="install-windows-updates"></a>Instalar las actualizaciones de Windows
22. Instale las últimas actualizaciones de Windows. Si eso no es posible, asegúrese de que se instalan las actualizaciones siguientes:

    - [KB3137061](https://support.microsoft.com/kb/3137061) Máquinas virtuales de Microsoft Azure no recuperarse de una interrupción de la red y se producen problemas de daños en los datos

    - [KB3115224](https://support.microsoft.com/kb/3115224) Mejoras en la confiabilidad para máquinas virtuales que se ejecutan en un host de Windows Server 2012 R2 o Windows Server 2012

    - [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: Actualización de seguridad de Microsoft Windows a la elevación de privilegio dirección: 8 de marzo de 2016

    - [KB3063075](https://support.microsoft.com/kb/3063075) Muchos de los eventos de identificador 129 se registran cuando se ejecuta una máquina virtual de Windows Server 2012 R2 en Microsoft Azure

    - [KB3137061](https://support.microsoft.com/kb/3137061) Máquinas virtuales de Microsoft Azure no recuperarse de una interrupción de la red y se producen problemas de daños en los datos

    - [KB3114025](https://support.microsoft.com/kb/3114025) Rendimiento lento cuando tiene acceso a almacenamiento de Azure archivos de Windows 8.1 o Server 2012 R2

    - [KB3033930](https://support.microsoft.com/kb/3033930) Revisión aumenta el límite de 64K en búferes de RÍO por el proceso de servicio de Azure en Windows

    - [KB3004545](https://support.microsoft.com/kb/3004545) No puede acceder a los equipos virtuales que se hospedan en servicios de hospedaje Azure a través de una conexión VPN en Windows

    - [KB3082343](https://support.microsoft.com/kb/3082343) Se pierde la conexión de VPN entre local cuando Azure túneles VPN de sitio a sitio usa Windows Server 2012 R2 RRAS

    - [KB3140410](https://support.microsoft.com/kb/3140410) MS16-031: Actualización de seguridad de Microsoft Windows a la elevación de privilegio dirección: 8 de marzo de 2016

    - [KB3146723](https://support.microsoft.com/kb/3146723) MS16-048: Descripción de la actualización de seguridad para CSRSS: 12 de abril de 2016
    - [KB2904100](https://support.microsoft.com/kb/2904100) Sistema se bloquea durante disco i/OS en Windows<a id="step23"></a>
23. Si desea crear una imagen para implementar varios equipos de él, debe generalizar la imagen ejecutando `sysprep` antes de cargar el disco duro virtual en Azure. No es necesario ejecutar `sysprep` para usar un disco duro virtual especializado. Para obtener más información acerca de cómo crear una imagen personalizada, consulte los siguientes artículos:

    - [Crear una imagen VM desde una máquina virtual de Azure existente mediante el modelo de implementación de administrador de recursos](virtual-machines-windows-create-vm-generalized.md)
    - [Crear una imagen VM desde una máquina virtual de Azure existente usando el módem de implementación estándar](virtual-machines-windows-classic-capture-image.md)
    - [Compatibilidad con Sysprep para funciones de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)



## <a name="suggested-extra-configurations"></a>Configuraciones adicionales sugeridas

La siguiente configuración no afectan al cargar el disco duro virtual. Sin embargo, le recomendamos encarecidamente que ha configurado.

- Instalar el [agente de máquinas virtuales de Windows Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Después de instalar al agente, puede habilitar las extensiones de máquina virtual. Las extensiones VM implementan la mayoría de las funciones críticas que desea usar con sus máquinas virtuales como restablecer las contraseñas, configurar RDP y muchas otras personas.

- El registro de descarga puede ser útil para solucionar problemas del bloqueo de Windows. Habilitar la colección de registro de descarga:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f`

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpFolder /t REG_EXPAND_SZ /d "c:\CrashDumps" /f

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpCount /t REG_DWORD /d 10 /f

    REG ADD "HKLM\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps" /v DumpType /t REG_DWORD /d 2 /f

    sc config wer start= auto
    ```

- Después de crea la máquina virtual en Azure, configurar el archivo de paginación de tamaño definidas por el sistema de la unidad D:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management" /t REG_MULTI_SZ /v PagingFiles /d "D:\pagefile.sys 0 0" /f
    ```


## <a name="next-steps"></a>Pasos siguientes

- [Cargar una imagen de máquina virtual de Windows en Azure para implementaciones del Administrador de recursos](virtual-machines-windows-upload-image.md)
