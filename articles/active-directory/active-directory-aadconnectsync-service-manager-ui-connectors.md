<properties
    pageTitle="Sincronización de Azure AD Connect: Administrador de servicios de sincronización UI | Microsoft Azure"
    description="Entender la ficha conectores en el Administrador de servicios de sincronización de Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-synchronization-service-manager"></a>Sincronización de Azure AD Connect: Administrador de servicios de sincronización

[Operaciones](active-directory-aadconnectsync-service-manager-ui-operations.md) | [Conectores](active-directory-aadconnectsync-service-manager-ui-connectors.md) | [Diseñador de Metaverse](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) | [Búsqueda en Metaverse](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)
--- | --- | --- | ---

![Administrador de servicios de sincronización](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

La ficha conectores se usa para administrar todos los sistemas a que el motor de sincronización está conectado.

## <a name="connector-actions"></a>Acciones de conector

Acción | Comentario
--- | ---
Crear | No use. Para conectarse a bosques AD adicionales, utilice al Asistente de instalación.
Propiedades | Se usa para el dominio y OU filtrado.
[Eliminar](#delete) | Utilizar para eliminar los datos en el espacio del conector o eliminar la conexión a un bosque.
[Configurar perfiles de ejecución](#configure-run-profiles) | Excepto para dominios filtrado, nada que configure aquí. Puede usar esta acción para ver perfiles de ejecución ya configurados.
Ejecutar | Se utiliza para iniciar una ejecución de un perfil de un solo uso.
Detener | Deja un conector que se está ejecutando un perfil.
Conector de exportación | No use.
Conector de importación | No use.
Actualizar el conector | No use.
Actualizar esquema | Actualiza el esquema en caché. Es preferible usar la opción en el Asistente para instalación en su lugar, desde el que también actualizaciones sincronización reglas.
[Espacio de conector de búsqueda](#search-connector-space) | Usado para buscar objetos y [seguir un objeto y sus datos a través del sistema](#follow-an-object-and-its-data-through-the-system).

### <a name="delete"></a>Eliminar
La acción de eliminación se utiliza para dos cosas diferentes.
![Administrador de servicios de sincronización](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

La opción **Eliminar espacio de conector solo** quita todos los datos pero mantener la configuración.

La opción **Eliminar conector y espacio de conector** quita los datos y la configuración. Esta opción se usa cuando no desea conectarse a un bosque ya.

Ambas opciones sincronización todos los objetos y actualización los objetos de metaverse. Esta acción es una operación larga.

### <a name="configure-run-profiles"></a>Configurar perfiles de ejecución
Esta opción permite ver los perfiles de ejecución configurados para un conector.

![Administrador de servicios de sincronización](./media/active-directory-aadconnectsync-service-manager-ui/configurerunprofiles.png)

### <a name="search-connector-space"></a>Espacio de conector de búsqueda
La acción de espacio de conector de búsqueda es muy útil para buscar objetos y solucionar problemas de datos.

![Administrador de servicios de sincronización](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

Comience seleccionando un **ámbito**. Puede buscar base de datos (RDN, DN, delimitador, subárbol) o estado del objeto (todas las demás opciones).  
![Administrador de servicios de sincronización](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png)  
Si hace una búsqueda de subárbol por ejemplo, obtener todos los objetos en una unidad organizativa.
![Administrador de servicios de sincronización](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png) desde esta cuadrícula puede seleccionar un objeto, seleccione **Propiedades**y [sígala](#follow-an-object-and-its-data-through-the-system) desde el espacio de conector de origen, en el metaverso y que el espacio del conector de destino.

## <a name="follow-an-object-and-its-data-through-the-system"></a>Seguir a un objeto y sus datos a través del sistema
Al solucionar un problema con los datos, siga un objeto del espacio de conector de origen a metaverse y al destino espacio del conector es un procedimiento clave para comprender por qué los datos no tienen los valores esperados.

### <a name="connector-space-object-properties"></a>Propiedades de objeto de espacio de conector
**Importar**  
Cuando se abre un objeto de cs, hay varias pestañas en la parte superior. La ficha **Importar** muestra los datos que se traslada después de una importación.
![Administrador de servicios de sincronización](./media/active-directory-aadconnectsync-service-manager-ui/csimport.png) el **Valor antiguo** muestra lo que está almacenado en el sistema y el **Nuevo valor** lo que se ha recibido del sistema de origen y aún no se ha aplicado. En este caso, dado que hay un error de sincronización, no se puede aplicar el cambio.

**Error**  
La página de error solo está visible si hay un problema con el objeto. Vea los detalles en la página operaciones para obtener más información acerca de cómo [solucionar problemas de sincronización](active-directory-aadconnectsync-service-manager-ui-operations.md#troubleshoot-errors-in-operations-tab).

**Linaje**  
La ficha linaje muestra cómo el objeto de espacio de conector está relacionado con el objeto de metaverse. Puede ver cuando el conector importaron por última vez un cambio en el sistema conectado y las reglas que se aplican a rellenar datos en el metaverso.
![Administrador de servicios de sincronización](./media/active-directory-aadconnectsync-service-manager-ui/cslineage.png) en la columna **acción** , puede ver hay una regla de sincronización **entrante** con la acción de **aprovisionamiento**. Que indica que este objeto del espacio de conector está presente, siempre que el objeto de metaverso permanece. Si la lista de reglas de sincronización en su lugar muestra una regla de sincronización con la dirección **saliente** y **aprovisionar**, indica que este objeto se elimina cuando se elimina el objeto de metaverse.
![Administrador de servicios de sincronización](./media/active-directory-aadconnectsync-service-manager-ui/cslineageout.png) también puede ver en la columna **PasswordSync** que el espacio de conector entrante puede contribuir cambios en la contraseña desde una regla de sincronización tiene el valor **True**. Esta contraseña se envía a Azure AD a través de la regla de salida.

En la pestaña linaje, puede ir a metaverse haciendo clic en [Propiedades de objeto de Metaverse](#metaverse-object-properties).

En la parte inferior de todas las pestañas, hay dos botones: **vista previa** e **inicie sesión**.

**Vista previa**  
La página de vista previa se usa para sincronizar un único objeto. Es útil si está solucionando problemas de algunas reglas de sincronización de cliente y desea ver el efecto de un cambio en un solo objeto. Puede seleccionar entre **Sincronización completa** y **sincronización de Delta**. También puede seleccionar entre **Generar Preview**, que solo mantiene el cambio en la memoria y **Vista previa de confirmación**, las etapas que todos los cambios en los espacios de conector de destino.
![Administrador de servicios de sincronización](./media/active-directory-aadconnectsync-service-manager-ui/preview1.png) puede inspeccionar el objeto y la regla que se aplica un flujo de atributo determinado.
![Administrador de servicios de sincronización](./media/active-directory-aadconnectsync-service-manager-ui/preview2.png)

**Registro**  
La página de registro se usa para ver el estado de sincronización de contraseña y el historial, consulte [solucionar problemas de sincronización de contraseñas](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization) para obtener más información.

### <a name="metaverse-object-properties"></a>Propiedades de objeto de Metaverse
**Atributos**  
En la ficha atributos, puede ver los valores y el conector que se ha contribuido.
![Administrador de servicios de sincronización](./media/active-directory-aadconnectsync-service-manager-ui/mvattributes.png)
**conectores**  
La ficha conectores muestra todos los espacios de conector que tienen una representación del objeto.
![Administrador de servicios de sincronización](./media/active-directory-aadconnectsync-service-manager-ui/mvconnectors.png) esta pestaña también le permite desplazarse al [objeto del espacio del conector](#connector-space-object-properties).

## <a name="next-steps"></a>Pasos siguientes
Más información sobre la configuración de [sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Más información acerca de cómo [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
