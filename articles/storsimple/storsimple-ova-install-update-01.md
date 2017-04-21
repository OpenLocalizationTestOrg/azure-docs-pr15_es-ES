<properties 
   pageTitle="Instalar actualizaciones de una matriz Virtual de StorSimple | Microsoft Azure"
   description="Describe cómo usar la interfaz de usuario de web StorSimple Virtual Array para aplicar actualizaciones del método portal y revisión"
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
   ms.date="09/07/2016"
   ms.author="alkohli" />

# <a name="install-updates-on-your-storsimple-virtual-array"></a>Instalar actualizaciones de la matriz Virtual de StorSimple

## <a name="overview"></a>Información general

En este artículo se describe los pasos necesarios para instalar actualizaciones de la matriz Virtual de StorSimple a través de la interfaz de usuario de web local y el portal de clásico de Azure. Debe aplicar actualizaciones de software o revisiones para mantener actualizada la matriz Virtual de StorSimple. 

Tenga en cuenta que instalar una actualización o una revisión se reinicie el dispositivo. Dado que la matriz Virtual StorSimple es un dispositivo de nodo único, se interrumpe cualquier i/OS en curso y el tiempo de inactividad de experiencias de su dispositivo. 

Antes de aplicar una actualización, se recomienda realizar los volúmenes o recursos compartidos sin conexión en el host de la primera y, a continuación, en el dispositivo. Esto minimiza la posibilidad de daños en los datos.

> [AZURE.IMPORTANT] Si está ejecutando actualización 0,1 o las versiones de software de GA, debe utilizar el método de revisión a través de la interfaz de usuario de web local para instalar la actualización de 0,3. Si se están ejecutando actualización 0,2, le recomendamos que instale las actualizaciones a través del portal clásica Azure.

## <a name="use-the-local-web-ui"></a>Usar la interfaz de usuario de web local 
 
Hay dos pasos cuando se utiliza la interfaz de usuario de web local:

- Descargar la actualización o la revisión
- Instalar la actualización o la revisión

### <a name="download-the-update-or-the-hotfix"></a>Descargar la actualización o la revisión

Realice los pasos siguientes para descargar la actualización de software desde el catálogo de Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Para descargar la actualización o la revisión

1. Inicie Internet Explorer y vaya a [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Si esta es la primera vez con el catálogo de Microsoft Update en este equipo, haga clic en **instalar** cuando se le solicite para instalar el complemento de catálogo de Microsoft Update.
  
3. En el cuadro de búsqueda del catálogo de Microsoft Update, escriba el número de Knowledge Base (KB) de la corrección que desee descargar. Escriba **3182061** para actualizar 0,3 y, a continuación, haga clic en **Buscar**.

    Revisión aparece el listado, por ejemplo, **StorSimple Virtual matriz actualización 0,3**.

    ![Catálogo de búsqueda](./media/storsimple-ova-install-update-01/download1.png)

4. Haga clic en **Agregar**. La actualización se agrega a la cesta.

5. Haga clic en **Ver cesta**.

6. Haga clic en **Descargar**. Especificar o **Buscar** una ubicación local donde desea que las descargas que aparezca. Las actualizaciones se descargan a la ubicación especificada y se coloca en una subcarpeta con el mismo nombre que la actualización. La carpeta también se copia a un recurso compartido de red que sea accesible desde el dispositivo.

7. Abra la carpeta copiada, debería ver un archivo de paquete de Microsoft Update independiente `WindowsTH-KB3011067-x64`. Este archivo se usa para instalar la actualización o la revisión.


### <a name="install-the-update-or-the-hotfix"></a>Instalar la actualización o la revisión

Antes de la instalación de actualización o revisión, asegúrese de que tiene la actualización o la revisión descargado localmente en el host o accesible a través de un recurso compartido de red. 

Use este método para instalar actualizaciones en un dispositivo que ejecuta GA o actualizar 0,1 versiones de software. Este procedimiento le lleva a menos de 2 minutos en completarse. Realice los pasos siguientes para instalar la actualización o la revisión.


#### <a name="to-install-the-update-or-the-hotfix"></a>Para instalar la actualización o la revisión

1. En la interfaz de usuario de web local, vaya a **Mantenimiento** > **Actualización de Software**.

    ![dispositivo de actualización](./media/storsimple-ova-install-update-01/update1m.png)

2. En **Actualizar ruta de acceso de archivo**, escriba el nombre de archivo para la actualización o la revisión. También puede buscar el archivo de instalación de actualización o revisión si coloca en un recurso compartido de red. Haga clic en **Aplicar**.

    ![dispositivo de actualización](./media/storsimple-ova-install-update-01/update2m.png)

3.  Se muestra una advertencia. Dado esto es un dispositivo de nodo único, después de que se aplique la actualización, reinicie el dispositivo y hay tiempo de inactividad. Haga clic en el icono de verificación.

    ![dispositivo de actualización](./media/storsimple-ova-install-update-01/update3m.png)

4. Inicia la actualización. Después de que el dispositivo se actualiza correctamente, se reinicia. La interfaz de usuario local no es accesible en esta duración.

    ![dispositivo de actualización](./media/storsimple-ova-install-update-01/update5m.png)

5. Una vez completado el reinicio, pasará a la página de **Inicio de sesión** . Para comprobar que se ha actualizado el software del dispositivo, en la interfaz de usuario de web local, vaya a **Mantenimiento** > **Actualización de Software**. La versión del software mostrado debería **10.0.0.0.0.10288.0** para actualizar 0,3.

    > [AZURE.NOTE] Las versiones de software se informe en forma ligeramente diferente en la interfaz de usuario de web local y el portal de clásico de Azure. Por ejemplo, el portal clásico Azure informes **10.0.10288.0** para la misma versión y **10.0.0.0.0.10288** de informes de la interfaz de usuario de web local. 

    ![dispositivo de actualización](./media/storsimple-ova-install-update-01/update6m.png)





## <a name="use-the-azure-classic-portal"></a>Usar el portal de clásico de Azure

Si ejecuta la actualización 0,2, le recomendamos que instale las actualizaciones a través del portal clásica Azure. El procedimiento portal requiere que el usuario digitalizar, descargue e instale las actualizaciones. Este procedimiento tarda aproximadamente 7 minutos en completarse. Realice los pasos siguientes para instalar la actualización o la revisión.

[AZURE.INCLUDE [storsimple-ova-install-update-via-portal](../../includes/storsimple-ova-install-update-via-portal.md)]

Una vez completada (como se indica el estado del trabajo al 100%) la instalación, vaya a **dispositivos > Mantenimiento > actualizaciones de Software**. La versión del software mostrado debería 10.0.10288.0.

![dispositivo de actualización](./media/storsimple-ova-install-update-01/azupdate12m.png)

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre la [administración de la matriz Virtual de StorSimple](storsimple-ova-web-ui-admin.md).
