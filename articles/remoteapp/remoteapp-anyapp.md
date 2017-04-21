<properties
   pageTitle="Ejecutar cualquier aplicación de Windows en cualquier dispositivo con RemoteApp de Azure | Microsoft Azure"
   description="Aprenda a compartir cualquier aplicación de Windows con los usuarios mediante RemoteApp de Azure."
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# <a name="run-any-windows-app-on-any-device-with-azure-remoteapp"></a>Ejecutar cualquier aplicación de Windows en cualquier dispositivo con RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Puede ejecutar una aplicación de Windows en cualquier lugar en cualquier dispositivo ahora mismo, gravemente - haciendo usando RemoteApp de Azure. Si es una aplicación personalizada creada hace 10 años o una aplicación de Office, los usuarios ya no tienen que estar ligada a un sistema operativo específico (por ejemplo, Windows XP) para las aplicaciones algunos.

Con RemoteApp de Azure, los usuarios también pueden usar sus propios dispositivos Android o Apple y obtener la misma experiencia que obtuvieron de Windows (o en teléfonos Windows). Para ello, la aplicación de Windows en una colección de máquinas virtuales de Windows en Azure de hospedaje: los usuarios pueden acceder a ellos desde cualquier lugar tienen una conexión a internet. 

Siga leyendo para obtener un ejemplo de exactamente cómo hacerlo.

En este artículo, vamos a compartir el acceso con todos los usuarios. Sin embargo, puede usar cualquier aplicación. Como puede instalar la aplicación en un equipo Windows Server 2012 R2, puede compartirlo mediante los pasos siguientes. Puede revisar los [requisitos de la aplicación](remoteapp-appreqs.md) para asegurarse de que funciona la aplicación.

Tenga en cuenta que porque Access es una base de datos y queremos que dicha base de datos sea útil, se llevarán a cabo algunos pasos adicionales para permitir el acceso a los usuarios el uso compartido de datos de Access. Si su aplicación no es una base de datos o no necesita que los usuarios puedan tener acceso a un archivo compartido, puede omitir esos pasos en este tutorial

> [AZURE.NOTE] <a name="note"></a>Necesita una cuenta de Azure para completar este tutorial:
> - Puede [Abrir una cuenta de Azure gratis](https://azure.microsoft.com/free/?WT.mc_id=A261C142F): obtenga créditos puede usar para probar los servicios de Azure pagados e incluso después de que se utilizan hasta puede mantener la cuenta y usar libre Azure servicios, como sitios Web. Nunca se cargará su tarjeta de crédito a menos que explícitamente cambia la configuración y pídale que se cargará.
> - Puede [activar las ventajas de suscriptor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): MSDN su suscripción le proporciona créditos cada mes que puede usar para los servicios de Azure pagados.


## <a name="create-a-collection-in-remoteapp"></a>Crear una colección de RemoteApp

Iniciar mediante la creación de una colección. La colección sirve como un contenedor para las aplicaciones y los usuarios. Cada colección está basada en una imagen, puede crear su propio o usar uno incluido con la suscripción. En este tutorial, usaremos la imagen de prueba de Office 2013 - contiene la aplicación que desea compartir.

1. En el portal de Azure, desplácese hacia abajo en el árbol de navegación de la izquierda hasta que vea RemoteApp. Abra la página.
2. Haga clic en **crear una colección de RemoteApp**.
3. Haga clic en **crear rápido** y escriba un nombre para la colección.
4. Seleccione la región que desee usar para crear la colección. Para obtener la mejor experiencia, seleccione la región más cercana geográfica a la ubicación donde los usuarios tendrá acceso a la aplicación. Por ejemplo, en este tutorial, los usuarios se ubicará en Redmond, Washington. La región de Azure más cercana es **US oeste**.
5. Seleccione el plan de facturación que desea usar. El plan de facturación básico coloca 16 usuarios en una máquina virtual grande de Azure, mientras que el plan de facturación estándar tiene 10 usuarios en una máquina virtual de Azure grande. Como un ejemplo de general, el plan básico resulta muy útil para el flujo de trabajo de tipo de entrada de datos. Para una aplicación de productividad, como Office, ¿desea un plan estándar.
6. Por último, seleccione la imagen de Office 2013 Professional. Esta imagen contiene aplicaciones de Office 2013. Solo un aviso - esta imagen sólo es válida para colecciones de prueba y pruebas de concepto. Se ' no puede usar esta imagen en un conjunto de producción.
7. Ahora, haga clic en **crear RemoteApp colección**.

![Crear una colección de nube de RemoteApp](./media/remoteapp-anyapp/ra-anyappcreatecollection.png)

Se inicia la creación de la colección, pero puede tardar hasta una hora.

Ahora ya está listo para agregar los usuarios.

## <a name="share-the-app-with-users"></a>Compartir la aplicación con los usuarios

Una vez que la colección se ha creado correctamente, es el momento de publicar el acceso a los usuarios y agregar los usuarios que deben tener acceso a él.

Si ha explorado lejos el nodo de Azure RemoteApp durante la creación de la colección, empezar haciendo la manera de volver a él desde la página de inicio de Azure.

2. Haga clic en la colección que creó anteriormente para acceder a opciones adicionales y configurar la colección.
![Una nueva colección de nube de RemoteApp](./media/remoteapp-anyapp/ra-anyappcollection.png)
3. En la pestaña **publicación** , haga clic en **Publicar** en la parte inferior de la pantalla y, a continuación, haga clic en **programas del menú Inicio publicar**.
![Publicar un programa RemoteApp](./media/remoteapp-anyapp/ra-anyapppublish.png)
4. Seleccione las aplicaciones que desea publicar en la lista. Para nuestro propósito, elegimos acceso. Haga clic en **Finalizar**. Espere a que las aplicaciones finalizar la publicación.
![Acceso a la publicación en RemoteApp](./media/remoteapp-anyapp/ra-anyapppublishaccess.png)


1. Una vez que la aplicación ha terminado de publicación, cabeza sobre a la ficha de **Acceso de usuarios** para agregar todos los usuarios que necesitan tener acceso a sus aplicaciones. Escriba los nombres de usuario (dirección de correo electrónico) para los usuarios y, a continuación, haga clic en **Guardar**.

![Agregar usuarios a RemoteApp](./media/remoteapp-anyapp/ra-anyappaddusers.png)


1. Ahora, es el momento para informar a los usuarios sobre estas nuevas aplicaciones y cómo tener acceso a ellos. Para ello, envíe a los usuarios un correo electrónico que señalan la URL de descarga del cliente de escritorio remoto.
![El cliente de la dirección URL de descarga de RemoteApp](./media/remoteapp-anyapp/ra-anyappurl.png)

## <a name="configure-access-to-access"></a>Configurar el acceso a Access

Algunas aplicaciones necesitan configuración adicional después de implementar a través de RemoteApp. En particular, para el acceso, vamos a crear un archivo compartido en Azure que puede tener acceso cualquier usuario. (Si no desea hacerlo, puede crear una [colección de híbrido](remoteapp-create-hybrid-deployment.md) [en lugar de nuestra colección nube] que permite a los usuarios obtener acceso a archivos e información en su red local.) A continuación, se deberá informar a los usuarios para asignar una unidad local en su equipo en el sistema de archivos de Azure.

La primera parte como administrador hacer. A continuación, tenemos algunos pasos para sus usuarios.

1. Empiece por la publicación de la interfaz de línea de comandos (cmd.exe). En la pestaña de la **publicación** , seleccione **cmd**y, a continuación, haga clic en **Publicar > programa publicar mediante la ruta de acceso**.
2. Escriba el nombre de la aplicación y la ruta de acceso. Para nuestro propósito, use "El Explorador de archivos" como el nombre y la "% SYSTEMDRIVE%\windows\explorer.exe" como la ruta de acceso.
![Publique el archivo cmd.exe.](./media/remoteapp-anyapp/ra-publishcmd.png)
3. Ahora debe crear una [cuenta de almacenamiento](../storage/storage-create-storage-account.md)de Azure. Hemos llamado al nuestro "accessstorage", así que elija un nombre que tenga sentido para usted. (Para misquote Highlander, puede haber un único "accessstorage.") ![Cuenta de almacenamiento de Azure nuestros](./media/remoteapp-anyapp/ra-anyappazurestorage.png)
4. Ahora vuelva al panel para obtener la ruta de acceso a su almacenamiento (ubicación del extremo). Deberá usar esto en un poco, así que asegúrese de que copia en algún lugar.
![La ruta de acceso de la cuenta de almacenamiento](./media/remoteapp-anyapp/ra-anyappstoragelocation.png)
5. A continuación, una vez que se ha creado la cuenta de almacenamiento, necesita la clave de acceso principal. Haga clic en **administrar las teclas de acceso**y, a continuación, copie la tecla de acceso principal.
6. Ahora, establezca el contexto de la cuenta de almacenamiento y crear un nuevo recurso compartido de archivo de Access. En una ventana de Windows PowerShell con privilegios elevados, ejecute los cmdlets siguientes:

        $ctx=New-AzureStorageContext <account name> <account key>
        $s = New-AzureStorageShare <share name> -Context $ctx

    Así que nuestra parte, estos son los cmdlets que se ejecute:

        $ctx=New-AzureStorageContext accessstorage <key>
        $s = New-AzureStorageShare <share name> -Context $ctx


Ahora, es activar del usuario. En primer lugar, hacer que los usuarios instalar un [cliente de RemoteApp](remoteapp-clients.md). A continuación, los usuarios necesitan asignar una unidad de su cuenta para ese recurso compartido de archivos de Azure que creó y agregar sus archivos de Access. Esto es cómo lo hacen:

1. En el cliente de RemoteApp, obtener acceso a las aplicaciones publicadas. Inicie el programa cmd.exe.
2. Ejecute el comando siguiente para asignar una unidad desde el equipo para el recurso compartido de archivos:

        net use z: \\<accountname>.file.core.windows.net\<share name> /u:<user name> <account key>

    Si establece el parámetro **/ persistente** en Sí, la unidad asignada se conservan en las sesiones.
1. Ahora, inicie la aplicación de explorador de archivos de RemoteApp. Copie los archivos de Access que desea usar en la aplicación compartida para el recurso compartido de archivos.
![Colocación de archivos de Access en un recurso compartido de Azure](./media/remoteapp-anyapp/ra-anyappuseraccess.png)
1. Por último, abra Access y, a continuación, abra la base de datos que acaba de compartir. Debería ver los datos de Access de la nube.
![Una base de datos de Access real ejecutando desde la nube](./media/remoteapp-anyapp/ra-anyapprunningaccess.png)

Ahora puede usar Access en cualquiera de sus dispositivos, asegúrese de que instalar a un cliente de RemoteApp.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a crear una colección, pruebe a crear una [colección que usa Office 365](remoteapp-tutorial-o365anywhere.md). O bien, puede crear una [colección de híbrido ](remoteapp-create-hybrid-deployment.md)que puede tener acceso a su red local.

<!--Image references-->
 
