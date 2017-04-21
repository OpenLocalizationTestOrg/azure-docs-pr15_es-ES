<properties 
   pageTitle="Configuración de servidor de iSCSI matriz Virtual de StorSimple | Microsoft Azure"
   description="Describe cómo realizar la configuración inicial, registrar su servidor de iSCSI StorSimple y completar la configuración del dispositivo."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/18/2016"
   ms.author="alkohli" />


# <a name="deploy-storsimple-virtual-array--set-up-your-virtual-device-as-an-iscsi-server"></a>Implementar StorSimple Virtual matriz: configurar el dispositivo virtual como un servidor iSCSI

![flujo de proceso de configuración de iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Información general

Este tutorial de implementación se aplica a la versión de disponibilidad general (GA) de marzo de 2016 ejecución de Microsoft Azure StorSimple matriz Virtual (también conocido como el dispositivo virtual de StorSimple local o el dispositivo virtual de StorSimple). Este tutorial describe cómo realizar la configuración inicial, registrar su servidor de iSCSI StorSimple, completar la configuración de dispositivo y, a continuación, crear, montaje, inicializar y dar formato a volúmenes en su servidor de iSCSI StorSimple dispositivo virtual. La información de configuración de StorSimple en este artículo se aplica solo a StorSimple Virtual matrices. 

Los procedimientos descritos a continuación tarden unos 30 minutos en 1 hora para completar. La información publicada en este artículo se aplica solo a StorSimple Virtual matrices.

## <a name="setup-prerequisites"></a>Requisitos previos de instalación

Antes de configurar y configurar el dispositivo virtual de StorSimple, asegúrese de:

- Tiene configurado un dispositivo virtual y conectado a ella, como se describe en [Implementar StorSimple Virtual matriz - aprovisionar una matriz virtual en Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) o [Implementar StorSimple Virtual matriz - aprovisionar una matriz virtual en VMware](storsimple-ova-deploy2-provision-vmware.md).

- Tiene la clave de registro de servicio del servicio de administrador de StorSimple que ha creado para administrar dispositivos virtuales de StorSimple. Para obtener más información, vea **paso 2: obtener la clave de registro de servicio** en [Implementar StorSimple Virtual matriz: preparar el portal](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key).

- Si este es el dispositivo virtual segundo o posterior que se va a registrar con un servicio de StorSimple administrador existente, debería tener la clave de cifrado de datos de servicio. Esta clave se genera cuando el primer dispositivo se registró correctamente con este servicio. Si ha perdido esta clave, vea **obtener la clave de cifrado de datos de servicio** de [usar la interfaz de usuario Web para administrar la matriz Virtual de StorSimple](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

## <a name="step-by-step-setup"></a>Configuración paso a paso 

Use las siguientes instrucciones paso a paso para instalar y configurar el dispositivo virtual de StorSimple:

-  [Paso 1: Completar la configuración de la interfaz de usuario de web local y registrar su dispositivo](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
-  [Paso 2: Completar la configuración de dispositivo requerido](#step-2-complete-the-required-device-setup)
-  [Paso 3: Agregar un volumen](#step-3-add-a-volume)
-  [Paso 4: Montaje, inicializar y dar formato a un volumen](#step-4-mount-initialize-and-format-a-volume)  

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Paso 1: Completar la configuración de la interfaz de usuario de web local y registrar su dispositivo 

#### <a name="to-complete-the-setup-and-register-the-device"></a>Para completar la configuración y registrar el dispositivo

1. Abra una ventana del explorador y conectarse a la interfaz de usuario de web escribiendo:

    `https://<ip-address of network interface>`

    Usar la dirección URL de conexión especificada en el paso anterior. Verá un error que le informa de que hay un problema con el certificado de seguridad del sitio Web. Haga clic en **Continuar para esta página web**.

    ![error de certificado de seguridad](./media/storsimple-ova-deploy3-iscsi-setup/image3.png)

2. Inicie sesión en la interfaz de usuario de su dispositivo virtual de web como **StorSimpleAdmin**. Escriba la contraseña de administrador del dispositivo ha cambiado en el paso 3: iniciar el dispositivo virtual en [Implementar StorSimple Virtual matriz - aprovisionar un dispositivo virtual en Hyper-V](storsimple-ova-deploy2-provision-hyperv.md) o [Implementar StorSimple Virtual matriz - aprovisionar un dispositivo virtual de VMware](storsimple-ova-deploy2-provision-vmware.md).

    ![Página de inicio de sesión](./media/storsimple-ova-deploy3-iscsi-setup/image4.png)

3. Se le dirigirá a la página **principal** . Esta página describe las diferentes configuraciones necesarias para configurar y registrar el dispositivo virtual con el servicio de administrador de StorSimple. Observe que la **configuración de red**, **la configuración de proxy de Web**y **la configuración de hora** son opcionales. La única configuración necesaria es **configuración de dispositivo** y la **configuración de la nube**.

    ![Página principal](./media/storsimple-ova-deploy3-iscsi-setup/image5.png)

4. En la página de **configuración de la red** en **interfaces de red**, datos 0 se configurarán automáticamente para usted. Cada interfaz de red está configurada por defecto para obtener una dirección IP automáticamente (DHCP). Por lo tanto, una dirección IP, subred y la puerta de enlace se asignará automáticamente (para IPv4 y IPv6).

    Mientras planea implementar el dispositivo como servidor iSCSI (para aprovisionar almacenamiento de bloque), se recomienda que deshabilitar la opción **dirección IP obtener automáticamente** y configurar direcciones IP estáticas.

    ![Página de configuración de red](./media/storsimple-ova-deploy3-iscsi-setup/image6.png)

    Si agrega más de una interfaz de red durante el aprovisionamiento del dispositivo, puede configurarlos aquí. Nota que puede configurar la interfaz de red como IPv4 solo o como IPv4 y IPv6. IPv6 no se admiten sólo configuraciones.

5. Los servidores DNS son necesarios porque se utilizan cuando el dispositivo intenta comunicarse con los proveedores de servicios de almacenamiento de nube o para resolver el dispositivo por nombre si está configurado como un servidor de archivos. En la página de **configuración de la red** en los **servidores DNS**:

    1. Se configurará automáticamente un servidor DNS principal y secundario. Si decide configurar direcciones IP estáticas, puede especificar los servidores DNS. Para alta disponibilidad, se recomienda que configure un principal y un servidor DNS secundario.

    2. Haga clic en **Aplicar**. Esto aplicará y validar la configuración de red.

6. En la página **Configuración del dispositivo** :

    1. Asignar un único **nombre** para el dispositivo. Este nombre puede ser 1 y 15 caracteres y puede contener letras, números y guiones.

    2. Haga clic en el icono de **servidor iSCSI** ![icono del servidor iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/image7.png) para el **tipo** de dispositivo que está creando. Un servidor iSCSI le permitirá para proporcionar el almacenamiento de bloque.

    3. Especifique si desea que este dispositivo esté unido al dominio. Si el dispositivo es un servidor de iSCSI, a continuación, unirse al dominio es opcional. Si decide no unirse a su servidor de iSCSI a un dominio, haga clic en **Aplicar**, espere a que la configuración que se aplicarán y, a continuación, vaya al paso siguiente.

        Si desea unir el dispositivo a un dominio. Escriba un **nombre de dominio**y, a continuación, haga clic en **Aplicar**.

        > [AZURE.NOTE] Si unirse a su servidor de iSCSI a un dominio, asegúrese de que la matriz virtual está en su propia unidad organizativa (OU) para Microsoft Azure Active Directory y no objetos de directiva de grupo (GPO) se aplican a él.

    5. Aparecerá un cuadro de diálogo. Escriba sus credenciales de dominio en el formato especificado. Haga clic en el icono de verificación ![icono de comprobación](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). Se puede comprobar las credenciales del dominio. Verá un mensaje de error si las credenciales son correctas.

        ![credenciales](./media/storsimple-ova-deploy3-iscsi-setup/image8.png)

    6. Haga clic en **Aplicar**. Esto aplicará y validar la configuración del dispositivo.
 
7. (Opcionalmente) configure su servidor proxy de web. Aunque la configuración de proxy web es opcional, tenga en cuenta que si usa a un servidor proxy web, sólo puede configurar aquí.

    ![configurar a proxy web](./media/storsimple-ova-deploy3-iscsi-setup/image9.png)

    En la página **proxy Web** :

    1. Proporcione la **dirección URL de proxy de Web** en este formato: *http://host-IP dirección* o *el número de FDQN:Port*. Tenga en cuenta que no se admiten las direcciones URL HTTPS.

    2. Especificar la **autenticación** **básica** o **Ninguno**.

    3. Si está utilizando la autenticación, también debe proporcionar un **nombre de usuario** y **contraseña**.

    4. Haga clic en **Aplicar**. Esto validar y aplicar la configuración de proxy de web configurado.
 
8. (Opcionalmente) establecer la configuración de hora para el dispositivo, como la zona horaria y los servidores NTP primario y secundarios. Servidores NTP son necesarios porque el dispositivo debe sincronizar la hora para que pueden autenticar con los proveedores de servicios de nube.

    ![Configuración de hora](./media/storsimple-ova-deploy3-iscsi-setup/image10.png)

    En la página **configuración de hora** :

    1. En la lista desplegable, seleccione la **zona horaria** según la ubicación geográfica en la que se implementa el dispositivo. La zona horaria predeterminada para el dispositivo está PST. El dispositivo usará esta zona horaria para todas las operaciones programadas.

    2. Especificar un **servidor NTP principal** de su dispositivo o acepte el valor predeterminado de time.windows.com. Asegúrese de que su red permite tráfico NTP pasar de su centro de datos a Internet.

    3. Opcionalmente, especifique un **servidor NTP secundario** de su dispositivo.

    4. Haga clic en **Aplicar**. Esto validar y aplicar la configuración de tiempo configurado.

9. Configurar las opciones de nube de su dispositivo. En este paso, se complete la configuración de dispositivo local y, a continuación, registrar el dispositivo con el servicio de administrador de StorSimple.

    1. Escriba la **clave de registro de servicio** que obtuvo en **paso 2: obtener la clave de registro de servicio** en [Implementar StorSimple Virtual matriz: preparar el Portal](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key).

    2. Si no es el primer dispositivo que va a registrar con este servicio, deberá proporcionar la **clave de cifrado de datos de servicio**. Esta clave es necesaria con la clave de registro de servicio para registrar dispositivos adicionales con el servicio de administrador de StorSimple. Para obtener más información, consulte [obtener la clave de cifrado de datos de servicio](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) en la interfaz de usuario de web local.

    3. Haga clic en **registrarse**. Esto reinicie el dispositivo. Debe esperar 2 y 3 minutos antes de que el dispositivo está registrado correctamente. Después de reinicia el dispositivo, se le dirigirá a la página de inicio de sesión.

       ![Dispositivo de registro](./media/storsimple-ova-deploy3-iscsi-setup/image11.png)

10. Volver al portal clásico Azure. En la página de **dispositivos** , compruebe que el dispositivo está conectado correctamente al servicio consultando el estado. El estado del dispositivo debe estar **activo**.

    ![Página de dispositivos](./media/storsimple-ova-deploy3-iscsi-setup/image12.png)

## <a name="step-2-complete-the-required-device-setup"></a>Paso 2: Completar la configuración de dispositivo requerido

Para completar la configuración de dispositivo del dispositivo StorSimple, necesita:

- Seleccione una cuenta de almacenamiento para asociar con el dispositivo.

- Elegir la configuración de cifrado de los datos que se envían a la nube.

Siga estos pasos en el portal de Azure clásico para completar la configuración de dispositivo requerido.

#### <a name="to-complete-the-minimum-device-setup"></a>Para completar la configuración de dispositivo mínimos

1. En la página de **dispositivos** , seleccione el dispositivo que acaba de crear. Este dispositivo podría mostrarse como **activa**. Haga clic en la flecha siguiente el nombre del dispositivo y, a continuación, haga clic en **Inicio rápido**.

    ![Página de dispositivos](./media/storsimple-ova-deploy3-iscsi-setup/image13.png)

2. Haga clic en **configuración de dispositivo completo** para iniciar al Asistente de dispositivo configurar.

    ![Configurar el Asistente para dispositivos](./media/storsimple-ova-deploy3-iscsi-setup/image14.png)

3. En el Asistente de dispositivo configurar, en la página **Configuración básica** , haga lo siguiente:

   1. Especifique una cuenta de almacenamiento para usarlo con el dispositivo. En esta suscripción, puede seleccionar una cuenta existente de almacenamiento de la lista desplegable, o puede especificar **Agregar más** para elegir una cuenta de una suscripción diferente.

   2. Definir la configuración de cifrado para todos los datos almacenados en los que se enviarán a la nube. (StorSimple usa cifrado AES 256). Para cifrar los datos, seleccione la casilla de verificación **Habilitar cifrado de almacenamiento de nube** . Escriba un cifrado de almacenamiento de nube que contenga 32 caracteres. Vuelva a escribir la clave para confirmarla.

   3. Haga clic en el icono de verificación ![icono de comprobación](./media/storsimple-ova-deploy3-iscsi-setup/image15.png).

    ![Configuración básica](./media/storsimple-ova-deploy3-iscsi-setup/image16.png)

    Ahora se actualizará la configuración. Después de que la configuración se actualiza correctamente, el botón de configuración de dispositivo completo estará disponible. Volverá a la página de **Inicio rápido** de dispositivos.                                                        

>[AZURE.NOTE]Puede modificar todas las demás opciones de dispositivo en cualquier momento mediante el acceso a la página **Configurar** .

## <a name="step-3-add-a-volume"></a>Paso 3: Agregar un volumen

Realice los pasos siguientes en el portal de Azure clásico para crear un volumen.

#### <a name="to-create-a-volume"></a>Para crear un volumen

1. En la página de **Inicio rápido** de dispositivos, haga clic en **Agregar un volumen**. Esto inicia el asistente Agregar un volumen.

2. En el asistente Agregar un volumen, en **Configuración básica**, haga lo siguiente:

    1. Proporcione un nombre único para el volumen. El nombre debe ser una cadena que contiene caracteres de 3 a 127.

    2. Proporcione una descripción para el volumen. La descripción le ayudará a identificar los propietarios de volumen.

    3. Seleccione un tipo de uso para el volumen. El tipo de uso puede ser **en niveles volumen** o **localmente anclados volumen.** (**En niveles volumen** es el valor predeterminado). Para cargas de trabajo que requieren garantías locales, baja latencia y rendimiento superior, seleccione **localmente anclados** **volumen**. Para todos los datos, seleccione **en niveles** de **volumen**.

        Un volumen localmente anclado aprovisionado grosor y garantiza que los datos en el volumen principales permanezcan en el dispositivo y no supresión en la nube. Si crea un volumen localmente anclado, el dispositivo buscará el espacio disponible en los niveles locales para aprovisionar un volumen de tamaño solicitado. Creación de un volumen localmente anclado puede requerir salpicadura datos existentes desde el dispositivo a la nube y el tiempo necesario para crear el volumen sea largo. El tiempo total depende el tamaño del volumen aprovisiona, ancho de banda de red disponibles y los datos en el dispositivo.

        Un volumen en niveles por otro lado se aprovisiona thin y puede crearse muy rápidamente. Cuando se crea un volumen en niveles, aproximadamente un 10% de espacio se aprovisiona en el nivel local y aprovisionado 90% del espacio en la nube. Por ejemplo, si suministrado un volumen de 1 TB, 100 GB se encuentran en el espacio local y 900 GB se utilizará en la nube cuando los niveles de datos. Esto implica a su vez es si se queda sin espacio local en el dispositivo, no se puede aprovisionar un recurso compartido en niveles (porque el 10% no estará disponible).

    4. Especificar la capacidad de preparación para el volumen. Tenga en cuenta que la capacidad especificada debe ser menor que la capacidad disponible. Si va a crear un volumen en niveles, el tamaño debe estar entre 500 GB y 5 TB. Para un volumen localmente anclado, especifique el tamaño de volumen entre 50 y 500 GB. Usar la capacidad disponible como una guía de aprovisionamiento de un volumen. Si la capacidad local disponible es 0 GB, a continuación, no podrá proporcionando un localmente anclada o un volumen en niveles.

        ![Configuración básica](./media/storsimple-ova-deploy3-iscsi-setup/image17.png)

    5. Haga clic en el icono de flecha ![icono de flecha](./media/storsimple-ova-deploy3-iscsi-setup/image18.png) para ir a la página siguiente.

3. En la página **Configuración adicional** , agregue un nuevo registro de control de acceso (ACR):

    1. Proporcione un **nombre** de su ACR.

    2. En **nombre del iniciador iSCSI**, proporcione el iSCSI nombre completo (IQN) de su host de Windows. Si no tiene el IQN, vaya al [Apéndice A: obtener el IQN de un host de Windows Server](#appendix-a-get-the-iqn-of-a-windows-server-host).

    3. Le recomendamos que habilite una copia de seguridad de forma predeterminada, seleccione la casilla de verificación **Habilitar una copia de seguridad predeterminado para este volumen** . La copia de seguridad predeterminados creará una directiva que se ejecuta en 22:30 cada día (hora del dispositivo) y crea una instantánea de la nube de este volumen.

        ![opciones adicionales](./media/storsimple-ova-deploy3-iscsi-setup/image19.png)

    4. Haga clic en el icono de verificación ![icono de comprobación](./media/storsimple-ova-deploy3-iscsi-setup/image15.png). Esto inicia el trabajo de creación de volumen. Verá un mensaje de progreso similar al siguiente.

        ![mensaje de progreso](./media/storsimple-ova-deploy3-iscsi-setup/image20.png)

        Se creará un volumen con la configuración especificada. De forma predeterminada, supervisión y copia de seguridad estarán habilitados para el volumen.

    5. Para confirmar que el volumen se creó correctamente, vaya a la página de **volúmenes** . Verá el volumen mostrado.

        ![](./media/storsimple-ova-deploy3-iscsi-setup/image21.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Paso 4: Montaje, inicializar y dar formato a un volumen

Realice los pasos siguientes para montaje, inicializar y dar formato a los volúmenes StorSimple en un host de Windows Server.

#### <a name="to-mount-initialize-and-format-a-volume"></a>Montaje, inicializar y dar formato a un volumen

1. Iniciar el iniciador de iSCSI de Microsoft.

2. En la ventana **Propiedades del iniciador iSCSI** , en la pestaña de **detección** , haga clic en **Descubrir Portal**.

    ![descubrir portal](./media/storsimple-ova-deploy3-iscsi-setup/image22.png)

3. En el cuadro de diálogo **Descubrir Portal de destino** , proporcionar la dirección IP de la interfaz de red habilitado para iSCSI y, a continuación, haga clic en **Aceptar**.

    ![Dirección IP](./media/storsimple-ova-deploy3-iscsi-setup/image23.png)

4. En la ventana **Propiedades del iniciador iSCSI** , en la ficha **destinos** , busque los **destinos Discovered**. (Cada volumen será un destino detectado). El estado del dispositivo debe aparecer como **inactivo**.

    ![destinos detectados](./media/storsimple-ova-deploy3-iscsi-setup/image24.png)

5. Seleccione un dispositivo de destino y, a continuación, haga clic en **Conectar**. Después de conecta el dispositivo, debe cambiar el estado **conectado**. (Para obtener más información sobre cómo usar el iniciador de iSCSI de Microsoft, consulte [instalar y configurar Microsoft iniciador iSCSI] [1].

    ![Seleccione el dispositivo de destino](./media/storsimple-ova-deploy3-iscsi-setup/image25.png)

6. En el host de Windows, presione la tecla del logotipo de Windows + X y, a continuación, haga clic en **Ejecutar**.

7. En el cuadro de diálogo **Ejecutar** , escriba **Diskmgmt.msc**. Haga clic en **Aceptar**, y aparecerá el cuadro de diálogo de **Administración de disco** . El panel derecho mostrará los volúmenes en su host.

8. En la ventana **Administración de discos** , los volúmenes montados aparecerá como se muestra en la siguiente ilustración. Haga clic en el volumen descubierto (haga clic en el nombre del disco) y, a continuación, haga clic en **línea**.

    ![administración de disco](./media/storsimple-ova-deploy3-iscsi-setup/image26.png)

9. Haga clic y seleccione **Inicializar disco**.

    ![Inicializar disco 1](./media/storsimple-ova-deploy3-iscsi-setup/image27.png)

10. En el cuadro de diálogo, seleccione los discos iniciar y, a continuación, haga clic en **Aceptar**.

    ![Inicializar disco 2](./media/storsimple-ova-deploy3-iscsi-setup/image28.png)

11. Se inicia el Asistente para nuevo volumen Simple. Seleccione un tamaño de disco y, a continuación, haga clic en **siguiente**.

    ![Asistente para nuevo volumen 1](./media/storsimple-ova-deploy3-iscsi-setup/image29.png)

12. Asignar una letra de unidad al volumen y, a continuación, haga clic en **siguiente**.

    ![Asistente para nuevo volumen 2](./media/storsimple-ova-deploy3-iscsi-setup/image30.png)

13. Especifique los parámetros para formatear el volumen. **En Windows Server, NTFS solo es compatible.** Establecer la Australia a 64 KB. Proporcionar una etiqueta para el volumen. Es una práctica recomendada para este nombre sea idéntico al nombre de volumen proporcionado en el dispositivo virtual de StorSimple. Haga clic en **siguiente**.

    ![Asistente para nuevo volumen 3](./media/storsimple-ova-deploy3-iscsi-setup/image31.png)

14. Compruebe los valores para el volumen y, a continuación, haga clic en **Finalizar**.

    ![Asistente para nuevo volumen 4](./media/storsimple-ova-deploy3-iscsi-setup/image32.png)

    Los volúmenes aparecerá como **en línea** en la página de **Administración de disco** .

    ![volúmenes en línea](./media/storsimple-ova-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo usar la interfaz de usuario de web local para [Administrar la matriz Virtual de StorSimple](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Apéndice A: obtener el IQN de un host de Windows Server

Realice los pasos siguientes para obtener el iSCSI nombre completo (IQN) de un host de Windows que se está ejecutando Windows Server 2012.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>Para obtener el IQN de un host de Windows

1. Iniciar el iniciador de iSCSI de Microsoft en su host de Windows.

2. En la ventana **Propiedades del iniciador iSCSI** , en la pestaña **configuración** , seleccione y copie la cadena en el campo **Nombre del iniciador** .

    ![propiedades del iniciador iSCSI](./media/storsimple-ova-deploy3-iscsi-setup/image34.png)

2. Guardar esta cadena.

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



