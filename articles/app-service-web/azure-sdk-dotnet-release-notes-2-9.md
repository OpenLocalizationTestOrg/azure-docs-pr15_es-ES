<properties 
   pageTitle="Notas de la versión de Azure SDK para .NET 2,9" 
   description="Notas de la versión de Azure SDK para .NET 2,9" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/17/2016"
   ms.author="juliako"/>

# <a name="azure-sdk-for-net-29-release-notes"></a>Notas de la versión de Azure SDK para .NET 2,9

##<a name="overview"></a>Información general

Este documento contiene las notas para el SDK de Azure para .NET 2,9 versión. 

Para obtener información detallada acerca de las actualizaciones en esta versión, consulte el [anuncio de Azure SDK 2,9 publicar](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## <a name="azure-sdk-29-for-visual-studio-2015-update-2-and-visual-studio-15-preview"></a>Obtener una vista previa de Azure SDK 2,9 para Visual Studio 2015 Update 2 y Visual Studio "15"
 
Esta actualización incluye las correcciones de errores siguientes:

- Problema relacionado en el resto de la API de generación de cliente en el que la cadena "Tipo desconocido" aparecerá como el nombre de la carpeta de generación de código o el nombre del espacio de nombres en texto en el código generado.
- Problema relacionado con WebJobs programada en el que la información de autenticación no se pasarán el proceso de aprovisionamiento de programador.

Esta actualización incluye la nueva característica de la siguiente:

- Compatibilidad con los servicios de aplicación secundario en la ficha "Servicios" del cuadro de diálogo de aprovisionamiento de servicio de aplicación. 

##<a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Datos de Azure lago Tools para Visual Studio 2015 Update 2
 
Esta actualización incluye lo siguiente:

- **Datos de azure lago Tools** para Visual Studio ahora se combina en el SDK de Azure versión .NET. La herramienta se instala automáticamente al instalar el SDK de Azure. 

    La herramienta se actualiza con frecuencia, vaya [aquí](http://aka.ms/datalaketool) para obtener las actualizaciones.

- **Explorador de servidores** ahora le permite ver todos y crear algunas entidades de metadatos U SQL. Para obtener más información, consulte [este](https://azure.microsoft.com/documentation/services/data-lake-analytics/) blog.


##<a name="hdinsight-tools"></a>Herramientas de HDInsight 

**HDInsight Tools** para Visual Studio ahora es compatible con HDInsight versión 3.3, incluidos que muestra gráficos Tez y correcciones de otro idioma.


##<a name="azure-resource-manager"></a>Administrador de recursos de Azure 

Esta versión agrega soporte de [KeyVault](../resource-manager-keyvault-parameter.md) para plantillas ARM.

##<a name="see-also"></a>Vea también

[Publicación de anuncio de Azure SDK 2,9](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)
