<properties
   pageTitle="Cómo administrar las configuraciones de servicio y perfiles | Microsoft Azure"
   description="Obtenga información sobre cómo trabajar con archivos de configuración de configuraciones y perfiles de servicio | qué almacenar la configuración de los entornos de implementación y configuración de publicación para los servicios en la nube."
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

# <a name="how-to-manage-service-configurations-and-profiles"></a>Cómo administrar perfiles y configuraciones de servicios

## <a name="overview"></a>Información general

Cuando se publica un servicio de nube, Visual Studio almacena información de configuración en dos tipos de archivos de configuración: configuraciones y perfiles de servicio. Configuraciones de servicio (archivos .cscfg) almacenan la configuración de los entornos de implementación de un servicio de nube de Azure. Azure usa estos archivos de configuración cuando administra sus servicios en la nube. Por otro lado, el almacén de perfiles (archivos .azurePubxml) configuración de publicación para servicios en la nube. Estos valores son un registro de lo que elija al usar al Asistente para la publicación y Visual Studio utiliza localmente. En este tema se explica cómo trabajar con los dos tipos de archivos de configuración.

## <a name="service-configurations"></a>Configuraciones de servicios

Puede crear varias configuraciones de servicio para cada uno de los entornos de implementación. Por ejemplo, puede crear una configuración de servicio para el entorno local que usa para ejecutar y probar una aplicación de Azure y otra configuración de servicio en su entorno de producción.

Puede agregar, eliminar, cambiar el nombre y modificar estas configuraciones de servicio en función de sus necesidades. Puede administrar estas configuraciones de servicio de Visual Studio, como se muestra en la siguiente ilustración.

![Administrar las configuraciones de servicio](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-service-config.png)

También puede abrir el cuadro de diálogo **Administrar las configuraciones** de páginas de propiedades de la función. Para abrir las propiedades de una función en el proyecto de Azure, abra el menú contextual para esa función y, a continuación, elija **Propiedades**. En la pestaña **configuración** , expandir la lista de **Configuración del servicio** y, a continuación, seleccione **Administrar** para abrir el cuadro de diálogo **Administrar configuraciones** .

### <a name="to-add-a-service-configuration"></a>Para agregar una configuración de servicio

1. En el Explorador de soluciones de abrir el menú contextual para el proyecto de Azure y, a continuación, seleccione **Administrar configuraciones**.

    Aparece el cuadro de diálogo **Administrar las configuraciones de servicio** .

1. Para agregar una configuración de servicio, debe crear una copia de una configuración existente. Para ello, elija la configuración que desea copiar de la lista Nombre y, a continuación, seleccione **Crear copia**.

1. (Opcional) Asigne un nombre diferente a la configuración del servicio, elija la nueva configuración de servicio en la lista Nombre y, a continuación, seleccione **Cambiar nombre**. En el cuadro de texto **nombre** , escriba el nombre que desea utilizar para esta configuración de servicio y, a continuación, seleccione **Aceptar**.

    Un nuevo archivo de configuración de servicio que se denomina ServiceConfiguration. [Nombre del nuevo] .cscfg se agrega al proyecto Azure en el Explorador de soluciones.


### <a name="to-delete-a-service-configuration"></a>Para eliminar una configuración de servicio

1. En el Explorador de soluciones, abra el menú contextual para el proyecto de Azure y, a continuación, seleccione **Administrar configuraciones**.

    Aparece el cuadro de diálogo **Administrar las configuraciones de servicio** .

1. Para eliminar una configuración de servicio, elija la configuración que desea eliminar de la lista **nombre** y, a continuación, seleccione **Quitar**. Aparece un cuadro de diálogo para comprobar que desea eliminar esta configuración.

1. Seleccione **Eliminar**.

     El archivo de configuración de servicio se quita de Azure proyecto en el Explorador de soluciones.


### <a name="to-rename-a-service-configuration"></a>Para cambiar el nombre de una configuración de servicio

1. En el Explorador de soluciones, abra el menú contextual para el proyecto de Azure y, a continuación, seleccione **Administrar configuraciones**.

    Aparece el cuadro de diálogo **Administrar las configuraciones de servicio** .

1. Para cambiar el nombre de una configuración de servicio, elija la nueva configuración de servicio en la lista **nombre** y, a continuación, seleccione **Cambiar nombre**. En el cuadro de texto **nombre** , escriba el nombre que desea utilizar para esta configuración de servicio y, a continuación, seleccione **Aceptar**.

    Cambia el nombre del archivo de configuración de servicio en el proyecto de Azure en el Explorador de soluciones.

### <a name="to-change-a-service-configuration"></a>Para cambiar la configuración del servicio

- Si desea cambiar la configuración de un servicio, abra el menú contextual para el rol específico que desea cambiar en el proyecto de Azure y, a continuación, seleccione **Propiedades**. Vea [Cómo: configurar los Roles de un servicio de nube de Azure con Visual Studio](https://msdn.microsoft.com/library/azure/hh369931.aspx) para obtener más información.

## <a name="make-different-setting-combinations-by-using-profiles"></a>Asegúrese de establecer diferentes combinaciones mediante el uso de perfiles

Con un perfil, puede rellenar automáticamente en el **Asistente para publicación** con diferentes combinaciones de configuración para distintos fines. Por ejemplo, puede tener un perfil para depuración y otra versión crea. En ese caso, tendría su perfil **Depurar** **IntelliTrace** habilitado y la configuración de **depuración** seleccionado y su perfil de **lanzamiento** tendría **IntelliTrace** deshabilitado y la configuración de la **versión** seleccionada. También puede utilizar perfiles diferentes para implementar un servicio con una cuenta de almacenamiento diferente.

Al ejecutar el Asistente para la primera vez, se crea un perfil predeterminado. Visual Studio almacena el perfil en un archivo que tiene una extensión .azurePubXml, que se agrega a su proyecto de Azure en la carpeta de **perfiles** . Si especifica manualmente distintas opciones al ejecutar el asistente más adelante, el archivo se actualiza automáticamente. Antes de ejecutar el siguiente procedimiento, debe ya haya publicado su servicio de nube al menos una vez.

### <a name="to-add-a-profile"></a>Para agregar un perfil

1. Abrir el menú contextual para el proyecto de Azure y, a continuación, seleccione **Publicar**.

1. Junto a la lista de **perfil de destino** , seleccione el botón **Guardar perfil** , como se muestra en la siguiente ilustración. Esto crea un perfil.

    ![Crear un nuevo perfil](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/create-new-profile.png)

1. Después de crea el perfil, seleccione **<... administrar >** en la lista de **perfil de destino** .

    Aparece el cuadro de diálogo **Administrar perfiles** , como se muestra en la siguiente ilustración.

    ![Cuadro de diálogo Perfiles administrar](./media/vs-azure-tools-service-configurations-and-profiles-how-to-manage/manage-profiles.png)

1. En la lista **nombre** , elija un perfil y, a continuación, seleccione **Crear una copia**.

1. Elija el botón **Cerrar** .

    El nuevo perfil aparece en la lista de perfil de destino.

1. En la lista de **perfil de destino** , seleccione el perfil que acaba de crear. La configuración del asistente Publicar se rellena con las opciones del perfil seleccionado.

1. Seleccione los botones **anterior** y **siguiente** para mostrar cada página del Asistente para publicar y, a continuación, personalice la configuración para este perfil. Para obtener más información, vea [Asistente para publicar aplicaciones de Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085) .

1. Cuando termine de personalizar la configuración, seleccione **siguiente** para volver a la página de configuración. El perfil se guarda cuando se publica el servicio mediante estas opciones o si selecciona la opción **Guardar** junto a la lista de perfiles.

### <a name="to-rename-or-delete-a-profile"></a>Para cambiar el nombre o eliminar un perfil

1. Abrir el menú contextual para el proyecto de Azure y, a continuación, seleccione **Publicar**.

1. En la lista de **perfil de destino** , seleccione **Administrar**.

1. En el cuadro de diálogo **Administrar perfiles** , seleccione el perfil que desee eliminar y, a continuación, seleccione **Quitar**.

1. En el cuadro de diálogo de confirmación que aparece, seleccione **Aceptar**.

1. Seleccione **Cerrar**.

### <a name="to-change-a-profile"></a>Para cambiar un perfil

1. Abrir el menú contextual para el proyecto de Azure y, a continuación, seleccione **Publicar**.

1. En la lista de **perfil de destino** , seleccione el perfil que desee cambiar.

1. Seleccione los botones **anterior** y **siguiente** para mostrar cada página del Asistente para publicar y, a continuación, cambie la configuración que desee. Para obtener más información, vea [Asistente para publicar aplicaciones de Azure](http://go.microsoft.com/fwlink/p/?LinkID=623085) .

1. Una vez que termine de modificar la configuración, seleccione **siguiente** para volver a la página de **configuración** .

1. (Opcional) seleccione **Publicar** para publicar el servicio de nube con la nueva configuración. Si no desea publicar su servicio de nube en este momento y cerrar al Asistente para la publicación, Visual Studio le pregunta si desea guardar los cambios en el perfil.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información acerca de cómo configurar otras partes del proyecto de Visual Studio Azure, consulte [configurar un proyecto de Azure](http://go.microsoft.com/fwlink/p/?LinkID=623075)
