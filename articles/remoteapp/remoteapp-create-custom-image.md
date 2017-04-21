<properties
    pageTitle="Cómo crear una imagen de la plantilla personalizada para RemoteApp de Azure | Microsoft Azure"
    description="Aprenda a crear una imagen de la plantilla personalizada de RemoteApp de Azure. Puede usar esta plantilla con colección un híbrido o la nube."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="how-to-create-a-custom-template-image-for-azure-remoteapp"></a>Cómo crear una imagen de la plantilla personalizada para RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

RemoteApp Azure usa una imagen de la plantilla de Windows Server 2012 R2 para hospedar todos los programas que desea compartir con los usuarios. Para crear una imagen de la plantilla de RemoteApp personalizada, puede empezar con una imagen existente o cree uno nuevo. 


> [AZURE.TIP] ¿Sabía que puede crear una imagen desde una máquina virtual de Azure? Historia true y, reduce la cantidad de tiempo se tarda en importar la imagen. Consulte los pasos [a continuación](remoteapp-image-on-azurevm.md).

Los requisitos de la imagen que se pueden cargar para su uso con Azure RemoteApp son:


- El tamaño de la imagen debe ser un múltiplo de MB. Si intenta cargar una imagen que no es un múltiplo exacto, se producirá un error en la carga.
- El tamaño de la imagen debe ser 127 GB o más pequeña.
- Debe estar en un archivo de disco duro virtual (archivos VHDX [Hyper-V virtuales unidades de disco duro] no se admiten actualmente).
- El disco duro virtual no debe ser una máquina virtual de generación 2.
- Puede ser el disco duro virtual de tamaño fijo o expansión dinámica. Se recomienda un disco duro virtual de expansión dinámica porque se tarda menos tiempo para cargar en Azure que un archivo de disco duro virtual de tamaño fijo.
- El disco debe inicializarse con el registro de inicio maestro (MBR) partición estilo. No se admite el estilo de particiones GUID (GPT) de la tabla de partición.
- El disco duro virtual debe contener una única instalación de Windows Server 2012 R2. Puede contener varios volúmenes, pero solo que contiene una instalación de Windows.
- Deben estar instalados el rol de Host de sesión de escritorio remoto (RDSH) y la característica de experiencia de escritorio.
- La función de agente de conexión a Escritorio remoto debe *no* estar instalado.
- El archivo de sistema (CIFRADOS) debe estar deshabilitado.
- La imagen debe estar preparada con Sysprep mediante los parámetros **/oobe /generalize /shutdown** (no utilice el parámetro **/mode:vm** ).
- No se admite la carga de su disco duro virtual de una cadena de instantáneas.


**Antes de empezar**

Debe hacer lo siguiente antes de crear el servicio:

- [Registrarse](https://azure.microsoft.com/services/remoteapp/) para RemoteApp.
- Crear una cuenta de usuario en Active Directory para usar como la cuenta de servicio de RemoteApp. Restringir los permisos para esta cuenta de manera que solo puede unirse a equipos al dominio. Para obtener más información, vea [Configurar Azure Active Directory para RemoteApp](remoteapp-ad.md) .
- Recopilar información acerca de la red local: información y detalles del dispositivo VPN la dirección IP.
- Instale el módulo de [PowerShell de Azure](../powershell-install-configure.md) .
- Recopilar información acerca de los usuarios que desea conceder acceso a. Puede ser información de la cuenta de Microsoft o información de cuenta de trabajo de Active Directory para los usuarios.



## <a name="create-a-template-image"></a>Crear una imagen de plantilla ##

Estos son los pasos de alto niveles para crear una nueva imagen de plantilla desde cero:

1.  Busque una imagen de DVD de actualización de Windows Server 2012 R2 o ISO.
2.  Crear un archivo de disco duro virtual.
4.  Instalar Windows Server 2012 R2.
5.  Instale el rol de Host de sesión de escritorio remoto (RDSH) y la característica de experiencia de escritorio.
6.  Instalar características adicionales necesarios para que las aplicaciones.
7.  Instalar y configurar las aplicaciones. Para facilitar el uso compartidas aplicaciones, agregar las aplicaciones o programas que desea compartir en el menú **Inicio** de la imagen, específicamente en **%systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs.
8.  Realizar las configuraciones de Windows adicionales necesarios para que las aplicaciones.
9.  Deshabilitar el sistema de archivos cifrados (EFS).
10. **Requerida:** Vaya a Windows Update e instale todas las actualizaciones importantes.
9.  La imagen SYSPREP.

Los pasos detallados para crear una nueva imagen son:

1.  Busque una imagen de DVD de actualización de Windows Server 2012 R2 o ISO.
2.  Crear un archivo de disco duro virtual mediante la administración de disco.
    1.  Inicie administración de discos (diskmgmt.msc).
    2.  Crear un disco duro virtual de expansión dinámica de 40 GB o más de tamaño. (Calcular la cantidad de espacio necesario para Windows, sus aplicaciones y las personalizaciones. Windows Server con la característica de experiencia de escritorio instalado y el rol RDSH requieren unos 10 GB de espacio).
        1.  Haga clic en **acción > crear el disco duro virtual**.
        2.  Especifique la ubicación, el tamaño y el formato de disco duro virtual. Seleccione **expansión dinámica**y, a continuación, haga clic en **Aceptar**.

            Esto se ejecute durante unos segundos. Una vez finalizada la creación del disco duro virtual, verá un disco nuevo sin cualquier letra de unidad y, en estado de "No inicializado" en la consola de administración de disco.

        - Haga clic en el disco (no el espacio sin asignar) y, a continuación, haga clic en **Inicializar disco**. Seleccione **MBR** (registro de arranque maestro) como el estilo de partición y, a continuación, haga clic en **Aceptar**.
        - Crear un nuevo volumen: haga clic en el espacio sin asignar y, a continuación, haga clic en **Nuevo volumen Simple**. Puede aceptar los valores predeterminados del asistente, pero asegúrese de que asignar una letra de unidad para evitar posibles problemas al cargar la imagen de la plantilla.
        - Haga clic en el disco y, a continuación, haga clic en **Separar VHD**.





1. Instalar Windows Server 2012 R2:
    1. Crear una nueva máquina virtual. Use el nuevo Asistente de máquina Virtual de Hyper-V administrador o cliente Hyper-V.
        1. En la página Especificar generación, elija **generación 1**.
        2. En la página Conectar disco duro Virtual, seleccione **usar un disco duro virtual existente**y busque el disco duro virtual que creó en el paso anterior.
        2. En la página Opciones de instalación, seleccione **instalar un sistema operativo desde un CD-ROM o DVD_ROM de inicio**y, a continuación, seleccione la ubicación de los medios de instalación de Windows Server 2012 R2.
        3. Elija otras opciones en el asistente necesario para instalar Windows y las aplicaciones. Finalice al asistente.
    2.  Cuando finalice el asistente, modifique la configuración de la máquina virtual y realice los cambios necesarios para instalar Windows y los programas, como el número de procesadores virtuales y, a continuación, haga clic en **Aceptar**.
    4.  Conéctese a la máquina virtual e instale Windows Server 2012 R2.
1. Instale el rol de Host de sesión de escritorio remoto (RDSH) y la característica de experiencia de escritorio:
    1. Inicie el administrador del servidor.
    2. Haga clic en **Agregar funciones y características** en la pantalla de bienvenida o desde el menú **Administrar** .
    3. En la página antes de empezar, haga clic en **siguiente** .
    4. Seleccione **basado en roles o característica de instalación**y, a continuación, haga clic en **siguiente**.
    5. Seleccione el equipo local de la lista y, a continuación, haga clic en **siguiente**.
    6. Seleccione **Servicios de escritorio remoto**y, a continuación, haga clic en **siguiente**.
    7. Expanda **Interfaces de usuario y la infraestructura** y seleccione la **Experiencia de escritorio**.
    8. Haga clic en **Agregar características**y, a continuación, haga clic en **siguiente**.
    9. En la página de servicios de escritorio remoto, haga clic en **siguiente**.
    10. Haga clic en **Host de sesión de escritorio remoto**.
    11. Haga clic en **Agregar características**y, a continuación, haga clic en **siguiente**.
    12. En la página de opciones de instalación confirmar, seleccione **reiniciar el servidor de destino automáticamente si es necesario**y, a continuación, haga clic en **Sí** en la advertencia de reiniciar.
    13. Haga clic en **instalar**. Se reinicie el equipo.
1.  Instalar características adicionales necesarios para que sus aplicaciones, como .NET Framework 3.5. Para instalar las características, ejecute el Asistente de características y funciones agregar.
7.  Instalar y configurar los programas y las aplicaciones que desea publicar a través de RemoteApp.

>[AZURE.IMPORTANT]
>
>Instale la función RDSH antes de instalar aplicaciones para asegurarse de que se detectan problemas con la compatibilidad de la aplicación antes de que la imagen se carga a RemoteApp.
>
>Asegúrese de que un acceso directo a la aplicación**(archivo)** aparece en el menú **Inicio** para todos los usuarios (%systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs). Asegúrese de que el icono que se observan en el menú **Inicio** es lo que desea que vean los usuarios. Si no es así, cámbielo. (No *tiene* para agregar la aplicación al inicio menú, pero facilita mucho a publicar la aplicación de RemoteApp. En caso contrario, debe proporcionar la ruta de instalación de la aplicación cuando se publica la aplicación.)


8.  Realizar las configuraciones de Windows adicionales necesarios para que las aplicaciones.
9.  Deshabilitar el sistema de archivos cifrados (EFS). Ejecute el comando siguiente en una ventana de comandos con privilegios elevados:

        Fsutil behavior set disableencryption 1

    Como alternativa, puede establecer o agregar el siguiente valor DWORD en el registro:

        HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1
9.  Si va a crear la imagen dentro de una máquina virtual Azure, cambiar el nombre de la ** \%windir%\Panther\Unattend.xml** de archivos, como se bloqueará el script de carga utilizado más adelante desde el trabajo. Cambiar el nombre del archivo a Unattend.old para que todavía tendrá el archivo en caso de que necesita volver a la implementación.
10. Vaya a Windows Update e instale todas las actualizaciones importantes. Debe ejecutar varias veces para obtener todas las actualizaciones de Windows Update. (A veces instalar una actualización y actualización propio requiere una actualización).
10. La imagen SYSPREP. En un símbolo del sistema con privilegios elevados, ejecute el siguiente comando:

    **C:\Windows\System32\sysprep\sysprep.exe / generalize /oobe /shutdown**

    **Nota:** No utilice el modificador **/mode:vm** del comando SYSPREP incluso si se trata de una máquina virtual.


## <a name="next-steps"></a>Pasos siguientes ##
Ahora que tiene la imagen de la plantilla personalizada, debe cargar la imagen a la colección de RemoteApp. Para crear la colección, use la información en los siguientes artículos:


- [Cómo crear una colección de híbrido de RemoteApp](remoteapp-create-hybrid-deployment.md)
- [Cómo crear una colección de nube de RemoteApp](remoteapp-create-cloud-deployment.md)
 