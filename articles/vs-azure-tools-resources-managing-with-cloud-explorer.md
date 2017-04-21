<properties 
   pageTitle="Administración de recursos de Azure con el Explorador de nube | Microsoft Azure"
   description="Aprenda a usar Explorer nube para examinar y administrar recursos de Azure en Visual Studio."
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

# <a name="managing-azure-resources-with-cloud-explorer"></a>Administración de recursos de Azure con el Explorador de nube

##<a name="overview"></a>Información general

Explorer nube está diseñado para permitirle rápida y más fácil explorar y administrar los recursos de Azure en Visual Studio IDE. Puede, por ejemplo, usar para abrir una aplicación Web en el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) o en un explorador, o adjuntar a un depurador a él, o puede ver las propiedades de un contenedor de blob y abrirlo en el Editor del contenedor de Blob.

Explorer nube se basa en la pila de administrador de recursos de Azure, igual que el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). Comprende recursos como grupos de recursos de Azure y Azure servicios como lógica aplicaciones y aplicaciones de la API y admite el [control de acceso basado en roles](./active-directory/role-based-access-control-configure.md) (RBAC). Para ver los recursos de Azure que se han agregado o cambiado, elija el botón **Actualizar** en la barra de herramientas del explorador de la nube.

Explorer nube se instala como parte de Visual Studio Tools para Azure SDK 2.7. 

## <a name="prerequisites"></a>Requisitos previos

- RTM de 2015 de Visual Studio.

- Las herramientas de Visual Studio para SDK de Azure. 
- También debe tener una cuenta de Azure y haber iniciado sesión en él para ver los recursos de Azure en el Explorador de la nube. Si no tiene una, puede crear una cuenta de dos minutos. Si tiene una suscripción a MSDN, consulte el [Beneficio de Azure para los suscriptores de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). En caso contrario, vea [crear una cuenta de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Si el Explorador de nube no está visible, puede ver, elija **vista**, **Explorer nube** de **Otras ventanas** en la barra de menús.

## <a name="manage-azure-accounts-and-subscriptions"></a>Administrar suscripciones y cuentas de Azure

Para ver los recursos de Azure en el Explorador de la nube, debe iniciar sesión en una cuenta de Azure con uno o más suscripciones activas. Si tiene más de una cuenta de Azure, puede agregarlos en el Explorador de la nube y, a continuación, elija las suscripciones que desea incluir en la vista de recursos del explorador de la nube.

Si todavía no lo ha usado Azure antes o no ha agregado las cuentas necesarias para Visual Studio, le pedirá que lo haga.

## <a name="to-add-azure-accounts-to-cloud-explorer"></a>Para agregar cuentas de Azure a Explorer nube

1. Elija el icono de configuración de la barra de herramientas del explorador de la nube.

1. Elija el vínculo **Agregar una cuenta** . Inicie sesión en la cuenta de Azure cuyos recursos desea explorar. La cuenta que acaba de agregar debe estar seleccionada en la lista desplegable de selector de cuenta. Las suscripciones para la cuenta aparecen en la entrada de la cuenta.

    ![Adición de suscripciones de Azure](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819514.png)

    ![Elegir las suscripciones de Azure](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819515.png)

1. Seleccione las casillas de las suscripciones de cuenta que desea examinar y, a continuación, elija el botón **Aplicar** .

    Los recursos de Azure para las suscripciones seleccionadas aparecen en el Explorador de la nube.

## <a name="to-remove-an-azure-account"></a>Quitar una cuenta de Azure

1. Elija **archivo**, **Configuración de la cuenta** en la barra de menús.

1. En la sección de **Todas las cuentas** del cuadro de diálogo **Configuración de la cuenta** , elija el comando **Quitar** junto a la cuenta que desea quitar. Nota que este comando solo quita la cuenta de Visual Studio, sí no afecta a la cuenta de Azure.

## <a name="view-resource-types-or-groups"></a>Tipos de recursos de la vista o grupos

Para ver los recursos de Azure, puede elegir **Tipos de recursos** o vista de **Grupos de recursos** .

![Lista desplegable de la vista de recursos](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819516.png)

- Vista de **Tipos de recursos** , que es la vista comunes que se utilizan en el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), muestra los recursos de Azure clasificados por su tipo, como aplicaciones web, cuentas de almacenamiento y máquinas virtuales de Windows. Esto es similar a cómo Azure recursos aparecen en el Explorador de servidor.

- Vista de grupos de recursos clasifica Azure recursos por grupo de recursos de Azure que está asociados.

 
    Un grupo de recursos es un paquete de recursos de Azure, normalmente utilizados por una aplicación específica. Para obtener más información acerca de los grupos de recursos de Azure, consulte [Información general del Administrador de recursos de Azure](./resource-group-overview.md).

## <a name="view-and-navigate-resources"></a>Ver y navegar por los recursos

Para desplazarse a un recurso de Azure y ver su información en el Explorador de la nube, expanda el elemento tipo o grupo de recursos asociados y, a continuación, elija el recurso. Al elegir un recurso, aparece información en las dos pestañas en la parte inferior del explorador de la nube.

![Elegir una vista de recursos](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC819517.png)

- La pestaña **acciones** muestra las acciones que puede realizar en el Explorador de nube para el recurso seleccionado. También puede ver las acciones disponibles en el menú contextual del recurso.

- La pestaña **Propiedades** muestra las propiedades del recurso, como su grupo de recursos, la configuración regional y el tipo que está asociado.

Cada recurso tiene la acción **abierta en el portal**. Cuando se selecciona esta acción, Explorer nube muestra el recurso seleccionado en el [portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). Esta característica es especialmente útil para navegar por los recursos demasiado anidadas.

Acciones adicionales y valores de propiedad también pueden aparecer en función de los recursos de Azure. Por ejemplo, aplicaciones web y aplicaciones de lógica también tienen las acciones **abierto en un explorador** y **adjuntar depurador** además **abierto en el portal**. Acciones para abrir los editores aparecen al elegir un blob de la cuenta de almacenamiento, la cola o la tabla. Aplicaciones de Azure tienen propiedades **URL** y **estado** , mientras que los recursos de almacenamiento tienen propiedades de cadena de clave y conexión.

## <a name="search-resources"></a>Recursos de búsqueda

Para buscar recursos con un nombre específico en las suscripciones de la cuenta de Azure, escriba el nombre en el cuadro de búsqueda en el Explorador de la nube.

![Buscar recursos en el Explorador de nube](./media/vs-azure-tools-resources-managing-with-cloud-explorer/IC820394.png)

Escribir caracteres en el cuadro de búsqueda, solo los recursos que coinciden con los caracteres aparecen en el árbol de recursos.

