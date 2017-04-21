<properties
   pageTitle="Publicar un servicio de nube con las herramientas de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo publicar proyectos de servicio de nube Azure mediante Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="publishing-a-cloud-service-using-the-azure-tools"></a>Publicar un servicio de nube con las herramientas de Azure

Con las herramientas de Azure para Microsoft Visual Studio, puede publicar su aplicación Azure directamente desde Visual Studio. Visual Studio admite publicación integrada para el ensayo o el entorno de producción de un servicio de nube.

Antes de publicar una aplicación de Azure, debe tener una suscripción de Azure. También debe configurar una cuenta de servicio y almacenamiento de la nube para usarlo con la aplicación. Puede configurar estas en el [portal de clásico de Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

>[AZURE.IMPORTANT] Cuando se publica, puede seleccionar el entorno de implementación de su servicio de nube. También debe seleccionar una cuenta de almacenamiento que se utiliza para almacenar el paquete de aplicación para su implementación. Después de la implementación, se quita el paquete de aplicación de la cuenta de almacenamiento.

Al desarrollar y probar una aplicación de Azure, puede usar Web implementar publicar cambios incrementalmente para las funciones web. Después de publicar su aplicación en un entorno de implementación, implementar Web le permite implementar cambios directamente en la máquina virtual que ejecuta el rol de web. No tiene empaquetar y publicar su aplicación de Azure completa cada vez que desea actualizar su rol web para probar los cambios. Con este método puede tener los cambios del rol web disponibles en la nube para realizar pruebas sin esperar a que tiene la aplicación publicada en un entorno de implementación.

Use los siguientes procedimientos para publicar su aplicación de Azure y actualizar un rol web mediante Web implementar:

- Publicar o empaquetar una aplicación de Azure desde Visual Studio

- Actualizar un rol web como parte del ciclo de prueba y desarrollo

## <a name="publish-or-package-an-azure-application-from-visual-studio"></a>Publicar o empaquetar una aplicación de Azure desde Visual Studio

Cuando se publica la aplicación de Azure, puede hacer una de las siguientes tareas:

- Crear un paquete de servicio: puede usar este paquete y el archivo de configuración de servicio para publicar su aplicación en un entorno de implementación desde el [Portal de Azure clásico](http://go.microsoft.com/fwlink/?LinkID=213885).

- Publicar el proyecto de Visual Studio Azure: para publicar la aplicación directamente a Azure, utilice el Asistente para la publicación. Para obtener más información, consulte el [Asistente para publicar aplicaciones de Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-create-a-service-package-from-visual-studio"></a>Para crear un paquete de servicio de Visual Studio

1. Cuando esté listo para publicar la aplicación, abra el Explorador de soluciones, abra el menú contextual para el proyecto de Azure que contenga sus funciones y elija Publicar.

1. Para crear un paquete de servicio solo, siga estos pasos:  

  1. En el menú contextual para el proyecto de Azure, elija **paquete**.

  1. En el cuadro de diálogo de **Paquete de aplicaciones de Azure** , elija la configuración del servicio para el que desea crear un paquete y, a continuación, elija la configuración de compilación.

  1. (opcional) Para activar escritorio remoto para el servicio de nube después de publicarla, active la casilla de verificación **Habilitar Escritorio remoto para todas las funciones** y, a continuación, seleccione **configuración** configurar Escritorio remoto. Si desea depurar su servicio de nube después de publicarla, active depuración remota, seleccione **Habilitar depurador remoto para todos los Roles**.

      Para obtener más información, consulte [Uso de escritorio remoto con las funciones de Azure](vs-azure-tools-remote-desktop-roles.md).

  1. Para crear el paquete, elija el vínculo de **paquete** .

      Explorador de archivos muestra la ubicación del archivo del paquete recién creado. Puede copiar esta ubicación para que puede utilizarlo desde el [portal de clásico de Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

  1. Para publicar este paquete en un entorno de implementación, debe utilizar esta ubicación como la ubicación del paquete cuando se crea un servicio de nube e implementa este paquete en un entorno con el [portal clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

1. (Opcional) Para cancelar el proceso de implementación, en el menú contextual para el elemento de línea en el registro de actividad, elija **Cancelar y eliminar**. Esto detiene el proceso de implementación y elimina el entorno de implementación de Azure.

    >[AZURE.NOTE] Para quitar este entorno de implementación después de que se ha implementado, debe usar el [portal clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

1. (Opcional) Una vez han iniciado las instancias de la función, Visual Studio muestra automáticamente el entorno de implementación en el nodo de **Servicios en la nube** en Explorador de servidores. Desde aquí puede ver el estado de las instancias de la función individuales. Vea [los recursos de administración de Azure con el Explorador de la nube](vs-azure-tools-resources-managing-with-cloud-explorer.md). La ilustración siguiente muestra las instancias de la función mientras se encuentran en estado Inicializando:

    ![VST_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## <a name="update-a-web-role-as-part-of-the-development-and-testing-cycle"></a>Actualizar un rol Web como parte del ciclo de prueba y desarrollo

Si la infraestructura de back-end de su aplicación es estable, pero los roles de web más una actualización frecuente, puede utilizar Web implementar para actualizar un rol de la web en su proyecto. Esto es útil cuando no desee volver a crear y volver a las funciones de trabajo de back-end, o si tiene varias funciones web y desea actualizar solo uno de los roles de web.

### <a name="requirements"></a>Requisitos

Estos son los requisitos para usar Web implementar actualizar su rol web:

- **Para desarrollo y pruebas solo con fines:** Los cambios realizados directamente en la máquina virtual donde se ejecuta el rol de web. Si esta máquina virtual tiene que ser reciclados, los cambios se pierden porque el paquete original que publicó se usa para volver a crear la máquina virtual para el rol. Debe volver a publicar la aplicación para obtener los cambios más recientes de la función de web.

- **Se pueden actualizar solo los roles de web:** Funciones de trabajo no se pueden actualizar. Además, no puede actualizar el RoleEntryPoint en web role.cs.

- **Solo admite una instancia de una función web:** No puede tener varias instancias de una función de web en su entorno de implementación. Sin embargo, varias funciones de web por cada sólo con una instancia son compatibles.

- **Debe habilitar las conexiones de escritorio remotas:** Esto es necesario para implementar Web puede usar el usuario y la contraseña para conectarse a la máquina virtual para implementar los cambios en el servidor que ejecuta Internet Information Services (IIS). Además, debe conectarse a la máquina virtual para agregar un certificado de confianza a IIS en este equipo virtual. (Esto garantiza que la conexión remota de IIS que utiliza Web implementar es segura).

El procedimiento siguiente asume que está usando al Asistente para **Publicar aplicaciones de Azure** .

### <a name="to-enable-web-deploy-when-you-publish-your-application"></a>Para habilitar la implementación Web cuando se publique la aplicación

1. Para habilitar la **Habilitar Web implementar** para todas las casilla de verificación de roles de web, primero debe configurar conexiones a Escritorio remoto. Seleccione **Habilitar Escritorio remoto** para todas las funciones y, a continuación, introduzca las credenciales que se usará para conectarse remotamente en el cuadro **Configuración de escritorio remoto** que aparece. Para obtener más información, consulte [Uso de escritorio remoto con las funciones de Azure](vs-azure-tools-remote-desktop-roles.md) .

1. Para habilitar la implementación Web para todos los roles de web en la aplicación, seleccione **Habilitar Web implementar para todos los roles de web**.

    Aparece un triángulo amarillo de advertencia. Implementar Web usa un certificado autofirmado de confianza de forma predeterminada, que no se recomienda para cargar los datos confidenciales. Si necesita proteger este proceso para la información confidencial, puede agregar un certificado SSL que se usará para implementar Web conexiones. Este certificado debe ser un certificado de confianza. Para obtener información acerca de cómo hacerlo, vea la sección **A realizar Web implementar seguro** más adelante en este tema.

1. Seleccione **siguiente** para mostrar la pantalla de **Resumen** y, a continuación, elija **Publicar** para implementar el servicio de nube.

    Se publica el servicio de nube. La máquina virtual que se crea tiene habilitadas para IIS para que Web implementar puede usarse para actualizar los roles de web sin publicarlos las conexiones remotas.

    >[AZURE.NOTE] Si tiene más de una instancia configurada para un rol de web, aparece un mensaje de advertencia que indica que cada rol web se limitará a una instancia sólo en el paquete que creó para publicar la aplicación. Seleccione **Aceptar** para continuar. Como se indica en la sección requisitos, puede tener más de una función de web, pero sólo una instancia de cada rol.

### <a name="to-update-your-web-role-by-using-web-deploy"></a>Para actualizar su rol Web mediante implementar Web

1. Para utilizar Web implementar, realizar cambios en el código en el proyecto por cualquiera de las funciones web en Visual Studio que desea publicar y, a continuación, haga clic en este nodo de proyecto en la solución y elija **Publicar**. Aparece el cuadro de diálogo **Publicar en Web** .

1. (Opcional) Si ha agregado un certificado SSL de confianza para conexiones remotas para IIS, puede desactivar la casilla de verificación **Permitir certificados** . Para obtener información sobre cómo agregar un certificado para proteger la implementación Web, vea la sección **A realizar Web implementar seguro** más adelante en este tema.

1. Para utilizar Web implementar, el mecanismo de publicación tiene el nombre de usuario y contraseña que configurar para la conexión a Escritorio remoto cuando se publica por primera vez el paquete.

  1. En **nombre de usuario**, escriba el nombre de usuario.

  1. En **contraseña**, escriba la contraseña.

  1. (Opcional) Si desea guardar contraseña en este perfil, seleccione **Guardar contraseña**.

1. Para publicar los cambios en su rol de web, elija **Publicar**.

    Muestra la línea de estado **iniciado publicar**. Cuando haya completado la publicación, aparece **publicación correcta** . Ahora se han implementado los cambios a la función web en la máquina virtual. Ahora puede iniciar la aplicación de Azure en el entorno de Azure para probar sus cambios.

### <a name="to-make-web-deploy-secure"></a>Para establecer Web implementar segura

1. Implementar Web usa un certificado autofirmado de confianza de forma predeterminada, que no se recomienda para cargar los datos confidenciales. Si necesita proteger este proceso para la información confidencial, puede agregar un certificado SSL que se usará para implementar Web conexiones. Este certificado debe ser un certificado de confianza, que se obtiene de una entidad de certificación (CA).

    Para hacer que implementar Web segura para cada máquina virtual para cada uno de los roles de web, deberá cargar el certificado de confianza que desee usar para web implementar el [portal clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885). Esto se asegura de que el certificado se agrega a la máquina virtual que se crea el rol web cuando se publica la aplicación.

1. Para agregar un certificado SSL de confianza a IIS para conexiones remotas, siga estos pasos:

  1. Para conectarse a la máquina virtual que ejecuta el rol de web, seleccione la instancia de la función web en el **Explorador de nube** o el **Explorador de servidores**y, a continuación, elija el comando **Conectar utilizando Escritorio remoto** . Para conocer los pasos detallados para conectarse a la máquina virtual, consulte [Uso de escritorio remoto con las funciones de Azure](vs-azure-tools-remote-desktop-roles.md).

      Su navegador le pedirá que descargue una. Archivo RDP.

  1. Para agregar un certificado SSL, abra el servicio de administración en el Administrador de IIS. En el Administrador de IIS, habilitar SSL abriendo el vínculo de **enlaces** en el panel **acción** . Aparece el cuadro de diálogo **Agregar enlace de sitio** . Elija **Agregar**y, a continuación, elija HTTPS en la lista desplegable **tipo** . En la lista de **certificado SSL** , elija el certificado SSL que había firmado por una CA y que ha cargado en el [portal de clásico de Azure](http://go.microsoft.com/fwlink/?LinkID=213885). Para obtener más información, vea [Configurar la conexión al servicio de administración](http://go.microsoft.com/fwlink/?LinkId=215824).

      >[AZURE.NOTE] Si agrega un certificado SSL de confianza, el triángulo amarillo de advertencia ya no aparece en el **Asistente para publicación**.

## <a name="include-files-in-the-service-package"></a>Incluir archivos en el paquete de servicio

Debe incluir archivos específicos en el paquete de servicio para que estén disponibles en la máquina virtual que creó para una función. Por ejemplo, que desea agregar un .exe o un archivo .msi que se usa un script de inicio para el paquete de servicio. O bien, tendrá que agregar un ensamblado que requiere un proyecto de rol de función o de trabajo de web. Para incluir los archivos que deben agregarse a la solución para la aplicación de Azure.

### <a name="to-include-files-in-the-service-package"></a>Para incluir archivos en el paquete de servicio

1. Para agregar un ensamblado a un paquete de servicio, siga estos pasos:

  1. En el **Explorador de soluciones** , abra el nodo de proyecto para el proyecto que falta el ensamblado de referencia.

  1. Para agregar el ensamblado al proyecto, abra el menú contextual para la carpeta **referencias** y, a continuación, elija **Agregar referencia**. Aparece el cuadro de diálogo Agregar referencia.

  1. Seleccione la referencia que desea agregar y, a continuación, elija el botón **Aceptar** .

      La referencia se agrega a la lista en la carpeta **referencias** .

  1. Abrir el menú contextual para el ensamblado que haya agregado y elija **Propiedades**. Aparecerá el cuadro de diálogo **Propiedades** .

      Para incluir este ensamblado en el paquete de servicio, en la **lista copia Local** elija **True**.

1. En el **Explorador de soluciones** , abra el nodo de proyecto para el proyecto que falta el ensamblado de referencia.

1. Para agregar el ensamblado al proyecto, abra el menú contextual para la carpeta **referencias** y, a continuación, elija **Agregar referencia**. Aparece el cuadro de diálogo **Agregar referencia** .

1. Seleccione la referencia que desea agregar y, a continuación, elija el botón **Aceptar** .

    La referencia se agrega a la lista en la carpeta **referencias** .

1. Abrir el menú contextual para el ensamblado que haya agregado y elija **Propiedades**. Aparecerá el cuadro de diálogo Propiedades.

1. Para incluir este ensamblado en el paquete de servicio, en la lista de la **Copia Local** , elija **Verdadero**.

1. Para incluir archivos en el paquete de servicio que se han agregado a su proyecto de rol web, abra el menú contextual para el archivo y, a continuación, elija **Propiedades**. En la ventana de **Propiedades** , elija **contenido** del cuadro de lista de **Acción de compilación** .

1. Para incluir archivos en el paquete de servicio que se han agregado a su proyecto de la función de trabajo, abra el menú contextual para el archivo y, a continuación, elija **Propiedades**. En la ventana **Propiedades** , elija **Copiar si es posterior** en el cuadro de lista de **Copiar en el directorio de salida** .

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre la publicación en Azure desde Visual Studio, consulte el [Asistente para publicar aplicaciones de Azure](vs-azure-tools-publish-azure-application-wizard.md).
