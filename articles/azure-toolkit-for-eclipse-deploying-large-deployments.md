<properties
    pageTitle="Implementar implementaciones grandes"
    description="Aprenda a implementar grandes implementaciones con el Kit de herramientas de Azure para Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268601.aspx -->

# <a name="deploying-large-deployments"></a>Implementar implementaciones grandes #

Si la implementación es demasiado grande para que se incluyan en la carpeta de approot predeterminada, puede usar un recurso de almacenamiento local como la carpeta raíz de implementación para su JDK y servidor de aplicaciones.

## <a name="to-use-a-local-storage-resource-as-the-deployment-root-folder-for-large-deployments"></a>Para usar un recurso de almacenamiento local como la carpeta raíz de implementación para implementaciones grandes ##

1. Crear un nuevo recurso de almacenamiento local. No importa el nombre del recurso. Recursos de almacenamiento se definen en el nivel de función. Es la manera más rápida de obtener acceso al cuadro de diálogo de configuración de almacenamiento local, desde donde puede crear un nuevo recurso de almacenamiento local, con los pasos siguientes: haga clic en la función en la vista del **Explorador de proyectos** (expanda el nodo de proyecto Azure si no ve el rol), haga clic en **Azure**y, a continuación, haga clic en **Almacenamiento Local**. En el cuadro de diálogo de **Almacenamiento Local** , haga clic en **Agregar** para crear un nuevo recurso de almacenamiento local.
1. Establecer el tamaño deseado al menos 2048 MB (nada menos puede causar el mismo archivo tamaño problemas mientras se encuentra en la approot).
1. Compruebe que esté activada la **limpiar el contenido cuando la instancia de rol es recicla** ; Esto le ayudará a impedir que lógica de inicio de la implementación en conflicto con los archivos existentes en el recurso se ejecuta cuando la instancia de rol recicla.
1. Asegúrese de que se establece el valor de la **variable de entorno almacenar la ruta del directorio del recurso después de la implementación** con la cadena **DEPLOYROOT**. El cuadro de diálogo de recursos de almacenamiento local tendrá un aspecto similar al siguiente.
    ![][ic667943]

Como alternativa, si usa **DEPLOYROOT** como el *nombre* del recurso local y no cambiar el nombre de la variable del entorno generada automáticamente (que se establecerá en **DEPLOYROOT_PATH** en ese caso), que funcionarían para su aplicación.

Información adicional sobre la creación de un recurso de almacenamiento local se puede encontrar en [Propiedades de almacenamiento Local][].

## <a name="see-also"></a>Vea también ##

[Kit de herramientas de Azure para Eclipse][]

[Crear una aplicación del mundo Hola para Azure en Eclipse][]

[Instalar el Kit de herramientas de Azure Eclipse][] 

Para obtener más información sobre el uso de Azure con Java, consulte el [Centro para desarrolladores de Java de Azure][].

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Crear una aplicación del mundo Hola para Azure en Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalar el Kit de herramientas de Azure Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Propiedades de almacenamiento local]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties

<!-- IMG List -->

[ic667943]: ./media/azure-toolkit-for-eclipse-deploying-large-deployments/ic667943.png
