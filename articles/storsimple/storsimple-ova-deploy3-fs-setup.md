<properties
   pageTitle="Implementar StorSimple Virtual matriz 3: configurar el dispositivo virtual como servidor de archivos"
   description="Este tutorial tercera de implementación de matriz Virtual de StorSimple indica que para configurar un dispositivo virtual como servidor de archivos."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/26/2016"
   ms.author="alkohli"/>

# <a name="deploy-storsimple-virtual-array---set-up-as-file-server"></a>Implementar StorSimple Virtual matriz: conjunto arriba como servidor de archivos

![](./media/storsimple-ova-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Introducción 

En este artículo se aplica a la versión de Microsoft Azure StorSimple matriz Virtual (también conocido como el dispositivo virtual de StorSimple local o dispositivo virtual de StorSimple) ejecución marzo de 2016 disponibilidad general (GA). En este artículo se describe cómo realizar la configuración inicial, registrar su servidor de archivos de StorSimple, completar la configuración de dispositivo y crear y conectarse a recursos compartidos de SMB. Este es el último artículo de la serie de tutoriales de implementación necesarias para implementar completamente la matriz virtual como un archivo o un servidor de iSCSI.

El proceso de instalación y configuración puede tardar unos 10 minutos en completarse.


## <a name="setup-prerequisites"></a>Requisitos previos de instalación

Antes de configurar y configurar el dispositivo virtual de StorSimple, asegúrese de:

-   Tiene configurado un dispositivo virtual y conectado a ella, como se describe en la [disposición una matriz Virtual StorSimple en Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) o la [disposición una matriz Virtual StorSimple en VMware](storsimple-ova-deploy2-provision-vmware.md).

-   Tiene la clave de registro de servicio del servicio de administrador de StorSimple que ha creado para administrar dispositivos virtuales de StorSimple. Para obtener más información, vea [paso 2: obtener la clave de registro de servicio](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) para StorSimple Virtual matriz.

-   Si este es el dispositivo virtual segundo o posterior que se va a registrar con un servicio de StorSimple administrador existente, debería tener la clave de cifrado de datos de servicio. Esta clave se genera cuando el primer dispositivo se registró correctamente con este servicio. Si ha perdido esta clave, vea [obtener la clave de cifrado de datos de servicio](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) de la matriz Virtual de StorSimple.

## <a name="step-by-step-setup"></a>Configuración paso a paso

Use las siguientes instrucciones paso a paso para instalar y configurar el dispositivo virtual de StorSimple.

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Paso 1: Completar la configuración de la interfaz de usuario de web local y registrar su dispositivo 


#### <a name="to-complete-the-setup-and-register-the-device"></a>Para completar la configuración y registrar el dispositivo

1.  Abra una ventana del explorador y conectarse a la interfaz de usuario de web local. Tipo: 

    `https://<ip-address of network interface>`

    Usar la dirección URL de conexión especificada en el paso anterior. Verá un error que indica que hay un problema con el certificado de seguridad del sitio Web. Haga clic en **Continuar para esta página Web**.

    ![](./media/storsimple-ova-deploy3-fs-setup/image2.png)

1.  Inicie sesión en la interfaz de usuario de su dispositivo virtual de web como **StorSimpleAdmin**. Escriba la contraseña de administrador del dispositivo ha cambiado en el paso 3: iniciar el dispositivo virtual en [aprovisionar una matriz Virtual StorSimple en Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) o en [una matriz Virtual StorSimple en VMware de aprovisionamiento](storsimple-ova-deploy2-provision-vmware.md).

    ![](./media/storsimple-ova-deploy3-fs-setup/image3.png)

1.  Se le dirigirá a la página **principal** . Esta página describe las diferentes configuraciones necesarias para configurar y registrar el dispositivo virtual con el servicio de administrador de StorSimple. Observe que la **configuración de red**, **la configuración de proxy de Web**y **la configuración de hora** son opcionales. La única configuración necesaria es **configuración de dispositivo** y la **configuración de la nube**.

    ![](./media/storsimple-ova-deploy3-fs-setup/image4.png)

1.  En la página de **configuración de red** en **interfaces de red**, datos 0 se configurará automáticamente para usted. Cada interfaz de red está configurada por defecto para obtener la dirección IP automáticamente (DHCP). Por lo tanto, una dirección IP, subred y puerta de enlace se asignará automáticamente (para IPv4 y IPv6).

    ![](./media/storsimple-ova-deploy3-fs-setup/image5.png)

    Si agrega más de una interfaz de red durante el aprovisionamiento del dispositivo, puede configurarlos aquí. Nota que puede configurar la interfaz de red como IPv4 solo o como IPv4 y IPv6. IPv6 no se admiten sólo configuraciones.

1.  Los servidores DNS son necesarios porque se utilizan cuando el dispositivo intenta comunicarse con los proveedores de servicios de almacenamiento de nube o para resolver el dispositivo por su nombre cuando está configurado como un servidor de archivos. En la página de **configuración de red** en los **servidores DNS**:

    1.  Se configurará automáticamente un servidor DNS principal y secundario. Si decide configurar direcciones IP estáticas, puede especificar los servidores DNS. Para alta disponibilidad, se recomienda que configure un principal y un servidor DNS secundario.

    2.  Haga clic en **Aplicar**. Esto aplicará y validar la configuración de red.

2.  En la página **Configuración del dispositivo** :

    1.  Asignar un único **nombre** para el dispositivo. Este nombre puede ser 1 y 15 caracteres y puede contener letras, números y guiones.

    2.  Haga clic en el icono del **servidor de archivos** ![](./media/storsimple-ova-deploy3-fs-setup/image6.png) para el **tipo** de dispositivo que está creando. Un servidor de archivos le permitirá crear carpetas compartidas.

    3.  Como un servidor de archivos en el dispositivo, debe unir el dispositivo a un dominio. Escriba un **nombre de dominio**.

    1.  Haga clic en **Aplicar**.

2.  Aparecerá un cuadro de diálogo. Escriba sus credenciales de dominio en el formato especificado. Haga clic en el icono de verificación. Se puede comprobar las credenciales del dominio. Verá un mensaje de error si las credenciales son correctas.

    ![](./media/storsimple-ova-deploy3-fs-setup/image7.png)

1.  Haga clic en **Aplicar**. Esto aplicará y validar la configuración del dispositivo.

    ![](./media/storsimple-ova-deploy3-fs-setup/image8.png)

    > [AZURE.NOTE]
    > 
    > Asegúrese de que la matriz virtual está en su propia unidad organizativa (OU) de Active Directory y no objetos de directiva de grupo (GPO) son aplicados o heredados. Directiva de grupo puede instalar aplicaciones como el software antivirus en la matriz Virtual StorSimple. Instalar software adicional no es compatible y podría provocar daños en los datos. 

1.  (Opcionalmente) configure su servidor proxy de web. Aunque la configuración de proxy web es opcional, tenga en cuenta que si usa a un servidor proxy web, sólo puede configurar aquí.

    ![](./media/storsimple-ova-deploy3-fs-setup/image9.png)

    En la página **proxy Web** :

    1.  Proporcione la **dirección URL de proxy de Web** en este formato: *http://&lt;dirección IP de host o FDQN&gt;: número de puerto*. Tenga en cuenta que no se admiten las direcciones URL HTTPS.

    2.  Especificar la **autenticación** **básica** o **Ninguno**.

    3.  Si utiliza la autenticación, también debe proporcionar un **nombre de usuario** y **contraseña**.

    4.  Haga clic en **Aplicar**. Esto validar y aplicar la configuración de proxy de web configurado.

1.  (Opcionalmente) establecer la configuración de hora para el dispositivo, como la zona horaria y los servidores NTP primario y secundarios. Servidores NTP son necesarios porque el dispositivo debe sincronizar la hora para que pueden autenticar con los proveedores de servicios de nube.

    ![](./media/storsimple-ova-deploy3-fs-setup/image10.png)

    En la página **configuración de hora** :

    1.  En la lista desplegable, seleccione la **zona horaria** según la ubicación geográfica en la que se implementa el dispositivo. La zona horaria predeterminada para el dispositivo está PST. El dispositivo usará esta zona horaria para todas las operaciones programadas.

    2.  Especificar un **servidor NTP principal** de su dispositivo o acepte el valor predeterminado de time.windows.com. Asegúrese de que su red permite tráfico NTP pasar de su centro de datos a Internet.

    3.  Opcionalmente, especifique un **servidor NTP secundario** de su dispositivo.

    4.  Haga clic en **Aplicar**. Esto validar y aplicar la configuración de tiempo configurado.

1.  Configurar las opciones de nube de su dispositivo. En este paso, se complete la configuración de dispositivo local y, a continuación, registrar el dispositivo con el servicio de administrador de StorSimple.

    1.  Escriba la **clave de registro de servicio** que obtuvo en [paso 2: obtener la clave de registro de servicio](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key) para StorSimple Virtual matriz.

    2.  Omitir este paso si este es el primer dispositivo registrar con este servicio y vaya al paso siguiente. Si no es el primer dispositivo que va a registrar con este servicio, deberá proporcionar la **clave de cifrado de datos de servicio**. Esta clave es necesaria con la clave de registro de servicio para registrar dispositivos adicionales con el servicio de administrador de StorSimple. Para obtener más información, consulte para obtener la [clave de cifrado de datos de servicio](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) en la interfaz de usuario de web local.

    3.  Haga clic en **registrarse**. Esto reinicie el dispositivo. Debe esperar 2 y 3 minutos antes de que el dispositivo está registrado correctamente. Después de reinicia el dispositivo, se le dirigirá a la página de inicio de sesión.

        ![](./media/storsimple-ova-deploy3-fs-setup/image13.png)
    

1.  Volver al portal clásico Azure. En la página de **dispositivos** , compruebe que el dispositivo está conectado correctamente al servicio consultando el estado. El estado del dispositivo debe estar **activo**.

![](./media/storsimple-ova-deploy3-fs-setup/image12.png)

## <a name="step-2-complete-the-required-device-setup"></a>Paso 2: Completar la configuración de dispositivo requerido

Para completar la configuración de dispositivo del dispositivo StorSimple, necesita:

-   Seleccione una cuenta de almacenamiento para asociar con el dispositivo.

-   Elegir la configuración de cifrado de los datos que se envían a la nube.

Realice los pasos siguientes en el [portal de clásica Azure](https://manage.windowsazure.com/) para completar la configuración de dispositivo requerido.

#### <a name="to-complete-the-minimum-device-setup"></a>Para completar la configuración de dispositivo mínimos

1.  En la página de **dispositivos** , seleccione el dispositivo que acaba de crear. Este dispositivo podría mostrarse como **activa**. Haga clic en la flecha situada junto al nombre del dispositivo y, a continuación, haga clic en **Inicio rápido**.

2.  Haga clic en **configuración de dispositivo completo** para iniciar al Asistente de dispositivo configurar.

3.  En el Asistente para configurar dispositivos en la página **Configuración básica** , haga lo siguiente:

    1.  Especifique una cuenta de almacenamiento para usarlo con el dispositivo. Puede seleccionar una cuenta existente de almacenamiento en esta suscripción en la lista desplegable o especificar **Agregar más** para elegir una cuenta de una suscripción diferente.

    2.  Definir la configuración de cifrado para todos los datos almacenados (cifrado AES) que se enviarán a la nube. Para cifrar los datos, compruebe el cuadro combinado para **Habilitar la clave de cifrado de almacenamiento de nube**. Escriba un cifrado de almacenamiento de nube que contenga 32 caracteres. Vuelva a escribir la clave para confirmarla. Se usará una clave AES 256 bits con la clave definidas por el usuario para el cifrado.

    3.  Haga clic en el icono de verificación ![](./media/storsimple-ova-deploy3-fs-setup/image15.png).

        ![](./media/storsimple-ova-deploy3-fs-setup/image16.png)

Ahora se actualizará la configuración. Después de que la configuración se actualiza correctamente, el botón de configuración de dispositivo completo estará atenuado. Volverá a la página de **Inicio rápido** de dispositivos.

 ![](./media/storsimple-ova-deploy3-fs-setup/image17.png)


> [AZURE.NOTE]                                                              
>
> Puede modificar todas las demás opciones de dispositivo en cualquier momento mediante el acceso a la página **Configurar** .

## <a name="step-3-add-a-share"></a>Paso 3: Agregar un recurso compartido

Realice los pasos siguientes en el [portal de clásica Azure](https://manage.windowsazure.com/) para crear un recurso compartido.

#### <a name="to-create-a-share"></a>Crear un recurso compartido

1.  En la página de **Inicio rápido** de dispositivos, haga clic en **Agregar un recurso compartido**. Esto inicia el asistente Agregar un recurso compartido.

    ![](./media/storsimple-ova-deploy3-fs-setup/image17.png)

1.  En la página **Configuración básica** , haga lo siguiente:

    1.  Especifique un nombre único para el recurso compartido. El nombre debe ser una cadena que contiene caracteres de 3 a 127.

    2.  (Opcional) Proporcione una descripción para el recurso compartido. La descripción le ayudará a identificar los propietarios de compartir.

    3.  Seleccione un tipo de uso para el recurso compartido. El tipo de uso puede ser **en niveles** o **localmente anclados**, siendo en niveles predeterminado. Para cargas de trabajo que requieren garantías locales, baja latencia y rendimiento superior, seleccione un recurso compartido **anclados localmente** . Para todos los datos, seleccione un recurso compartido **en niveles** .

    Un recurso compartido de forma local anclado aprovisionado grosor y garantiza que los datos en el recurso compartido principales sean locales en el dispositivo y no supresión en la nube. Un recurso compartido en niveles por otro lado thin aprovisionado. Cuando se crea un recurso compartido en niveles, se aprovisiona 10% del espacio en el nivel local y aprovisionado 90% del espacio en la nube. Por ejemplo, si suministrado un volumen de 1 TB, 100 GB se encuentran en el espacio local y 900 GB se utilizará en la nube cuando los niveles de datos. Por separado, esto significa que si se queda sin espacio local en el dispositivo, no puede proporcionar un recurso compartido en niveles.

1.  Especificar la capacidad de preparación para el recurso compartido. Tenga en cuenta que la capacidad especificada debe ser menor que la capacidad disponible. Si usa un recurso compartido en niveles, el tamaño de compartir debe estar entre 500 GB y 20 TB. Para un recurso compartido localmente anclado, especifique el tamaño de compartir entre 50 GB y 2 TB. Use la capacidad disponible como guía para aprovisionar a un recurso compartido. Si la capacidad local disponible es 0 GB, a continuación, no podrá proporcionando recursos compartidos locales o en niveles.

    ![](./media/storsimple-ova-deploy3-fs-setup/image18.png)

1.  Haga clic en el icono de flecha ![](./media/storsimple-ova-deploy3-fs-setup/image19.png) para ir a la página siguiente.

1.  En la página **Configuración adicional** , asignar los permisos para el usuario o el grupo que se obtiene acceso a este recurso compartido. Especifique el nombre del usuario o grupo de usuarios en *<john@contoso.com>* formato. Le recomendamos que use un grupo de usuarios (en lugar de un único usuario) para permitir que los privilegios de administrador tener acceso a estos recursos compartidos. Después de asignar los permisos aquí, a continuación, puede usar el Explorador de Windows para modificar los permisos.

    ![](./media/storsimple-ova-deploy3-fs-setup/image20.png)

1.  Haga clic en el icono de verificación ![](./media/storsimple-ova-deploy3-fs-setup/image21.png). Se creará un recurso compartido con la configuración especificada. De forma predeterminada, supervisión y copia de seguridad estarán habilitados para el recurso compartido.

## <a name="step-4-connect-to-the-share"></a>Paso 4: Conectar con el recurso compartido

Ahora debe conectarse a los recursos compartidos que creó en el paso anterior. Siga estos pasos en el host de Windows Server.

#### <a name="to-connect-to-the-share"></a>Para conectarse al recurso compartido

1.  Presione ![](./media/storsimple-ova-deploy3-fs-setup/image22.png) + r. En la ventana Ejecutar, especifique la * \\ * como la ruta de acceso, reemplazar *el nombre del servidor de archivo* con el nombre del dispositivo que ha asignado a su servidor de archivos. Haga clic en **Aceptar**.

    ![](./media/storsimple-ova-deploy3-fs-setup/image23.png)

2.  Se abrirá el explorador. Ahora debería poder ver los recursos compartidos que crean como carpetas. Seleccione y haga doble clic en un recurso compartido (carpeta) para ver el contenido.

    ![](./media/storsimple-ova-deploy3-fs-setup/image24.png)

3.  Ahora puede agregar archivos a estos recursos compartidos y realice una copia de seguridad.

![icono de vídeo](./media/storsimple-ova-deploy3-fs-setup/video_icon.png) **vídeo disponible**

Vea el vídeo para ver cómo puede configurar y registrar una matriz Virtual de StorSimple como un servidor de archivos.

> [AZURE.VIDEO configure-a-storsimple-virtual-array]

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo usar la interfaz de usuario de web local para [Administrar la matriz Virtual de StorSimple](storsimple-ova-web-ui-admin.md).
