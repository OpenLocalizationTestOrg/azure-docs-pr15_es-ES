<properties
   pageTitle="Cómo actualizar proyectos a la versión actual de las herramientas de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo actualizar un proyecto de Azure en Visual Studio a la versión actual de las herramientas de Azure"
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

# <a name="how-to-upgrade-projects-to-the-current-version-of-the-azure-tools-for-visual-studio"></a>Cómo actualizar proyectos a la versión actual de Azure Tools para Visual Studio

## <a name="overview"></a>Información general

Después de instalar la versión actual de las herramientas de Azure (o una versión anterior a la más reciente que 1,6), todos los proyectos que se crearon usando herramientas de Azure suelte antes de 1,6 (noviembre de 2011) se actualizarán automáticamente cuando se abran. Si ha creado proyectos usando la versión (noviembre de 2011) 1,6 de estas herramientas y aún tiene esa versión instalada, puede abrir dichos proyectos en la versión anterior y más tarde decide si desea actualizarlos.

## <a name="how-your-project-changes-when-you-upgrade-it"></a>Cómo se cambia el proyecto cuando se actualice

Si un proyecto se actualiza automáticamente o especificar desea actualizar, se modifica el proyecto para trabajar con las versiones actuales de determinados ensamblados y también se cambian algunas propiedades como se describe en esta sección. Si el proyecto requiere otros cambios para que sea compatible con la versión más reciente de las herramientas, debe realizar los cambios manualmente.

- Para hacer referencia a la versión más reciente de Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitoirTraceListener.dll se actualiza el archivo web.config de funciones web y el archivo app.config para funciones de trabajo.

- Los ensamblados Microsoft.WindowsAzure.StorageClient.dll, Microsoft.WindowsAzure.Diagnostics.dll y Microsoft.WindowsAzure.ServiceRuntime.dll se actualizan a las nuevas versiones.

- Perfiles de publicación que se almacenaron en el archivo de proyecto Azure (.ccproj) se mueven a un archivo independiente, con la extensión .azurePubXml, en el subdirectorio **Publicar** .

- Algunas propiedades en el perfil de publicación se actualizan para admitir características nuevas y modificadas. **AllowUpgrade** se reemplazan por **DeploymentReplacementMethod** , ya que puede actualizar un servicio de nube implementada incrementalmente o simultáneamente.

- La propiedad **UseIISExpressByDefault** se agrega y establecida en falso para que el servidor web que se utiliza para depurar no cambiará automáticamente desde Internet Information Services (IIS) IIS Express. IIS Express es el servidor web predeterminado para los proyectos que se crean con las versiones más recientes de las herramientas.

- Si el almacenamiento en caché de Azure está alojado en uno o varios de los roles de su proyecto, se modifican algunas propiedades de la configuración del servicio (archivo .cscfg) y la definición del servicio (archivo .csdef) cuando se actualiza un proyecto. Si utiliza el paquete NuGet de almacenamiento en caché de Azure en el proyecto, el proyecto se actualizará a la versión más reciente del paquete. Debe abrir el archivo web.config y compruebe que la configuración del cliente se mantiene correctamente durante el proceso de actualización. Si agrega las referencias a ensamblados de cliente Azure almacenamiento en caché sin usar el paquete de NuGet, estos ensamblados no se actualizarán; debe actualizar manualmente las referencias a las nuevas versiones.

>[AZURE.IMPORTANT] Para los proyectos de F #, debe actualizar manualmente las referencias a ensamblados Azure para que hacen referencia a las nuevas versiones de esos ensamblados.

### <a name="how-to-upgrade-an-azure-project-to-the-current-release"></a>Cómo actualizar un proyecto de Azure a la versión actual

1. Instalar la versión actual de las herramientas de Azure en la instalación de Visual Studio que desee usar para el proyecto actualizado y, a continuación, abra el proyecto que desea actualizar. Si el proyecto ha sido creado con herramientas de Azure versión antes de 1,6 (noviembre de 2011), el proyecto se actualiza automáticamente a la versión actual. Si el proyecto se creó con noviembre de 2011 suelte y todavía está instalada esta versión, se abre el proyecto en esta versión.

1. En el Explorador de soluciones, abra el menú contextual para el nodo de proyecto, elija **Propiedades**y, a continuación, elija la pestaña de la **aplicación** del cuadro de diálogo que aparece.

    La pestaña de la **aplicación** muestra la versión de herramientas que está asociada con el proyecto. Si aparece la versión actual de herramientas de Azure, el proyecto ya se ha actualizado. Si ha instalado una versión más reciente de las herramientas que lo que se muestra en la pestaña, aparecerá un botón **de actualización** .

1. Seleccione el botón **Actualizar** para actualizar un proyecto a la versión actual de las herramientas.

1. Generar el proyecto y, a continuación, corrija los errores resultantes de cambios en la API. Para obtener información acerca de cómo modificar el código para la nueva versión, consulte la documentación de la API específica.
