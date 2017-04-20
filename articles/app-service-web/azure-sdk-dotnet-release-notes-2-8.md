
<properties 
   pageTitle="Notas de la versión de Azure SDK para .NET 2,8" 
   description="Notas de la versión de Azure SDK para .NET 2,8" 
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
 
# <a name="azure-sdk-for-net-28-281-and-282"></a>Azure SDK para .NET 2,8, 2.8.1 y 2.8.2

##<a name="overview"></a>Información general
 
Este artículo contiene las notas (que incluye los problemas conocidos y cambios importantes) para el SDK de Azure para .NET 2,8, 2.8.1 y 2.8.2 versiones. 

Para obtener una lista completa de nuevas características y actualizaciones realizadas en esta versión, consulte el anuncio de [Azure SDK 2,8 para 2013 de Visual Studio y Visual Studio de 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) . 

##  <a name="azure-sdk-for-net-28"></a>Azure SDK para .NET 2,8

### <a name="download-azure-sdk-for-net-28"></a>Descargar Azure SDK para .NET 2,8

[Azure SDK para .NET 2,8 de Visual Studio de 2015](http://go.microsoft.com/fwlink/?LinkId=699285) 

[Azure SDK para .NET 2,8 de 2013 de Visual Studio](http://go.microsoft.com/fwlink/?LinkId=699287)
 
### <a name="net-452-support"></a>.NET 4.5.2 admite 

####<a name="known-issues"></a>Problemas conocidos

Azure 2,8 SDK de .NET le permite crear .NET 4.5.2 paquetes de servicio de nube. Sin embargo 4.5.2 de .NET framework no se instalará en el predeterminado suelte de imágenes de sistema operativo de invitado hasta que el sistema operativo invitado de enero de 2016. Antes de eso, 4.5.2 .NET framework estará disponible a través de una versión de lanzamiento de SO invitado independiente: noviembre de 2015 02. Consulte la página de [Azure invitado OS versiones y matriz de compatibilidad de SDK](../cloud-services/cloud-services-guestos-update-matrix.md) para controlar cuándo se publicará la imagen.  Una vez que se publique la imagen de noviembre de 2015 02 puede usar dicha imagen actualizando el archivo de configuración de servicio de nube (.cscfg). En la configuración del servicio archivo establezca el atributo osVersion del elemento ServiceConfiguration en la cadena "WA-invitado-OS-4.26_201511-02". Si decide participar en usar esta imagen ya no se proporciona actualizaciones automáticas para el sistema operativo de invitado. Para obtener las actualizaciones automáticas osVersion debe estar establecida en "*" y .NET 4.5.2 solo estarán disponibles a través de actualizaciones automáticas en enero de 2016.

###<a name="azure-data-factory"></a>Generador de datos de Azure

####<a name="known-issues"></a>Problemas conocidos 

Durante la creación de proyectos de **Plantilla de fábrica datos** relacionados con los datos de ejemplo, secuencia de comandos de shell de power azure puede fallar si versión del shell de power azure instalado en el equipo se encuentra después 0.9.8.

Para crear este tipo de proyecto de correctamente, debe instalar la [versión de shell de azure power 0.9.8](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi).


### <a name="azure-resource-manager-tools"></a>Herramientas de administrador de recursos de Azure 

####<a name="breaking-changes"></a>Cambios importantes

La secuencia de comandos de PowerShell proporcionado por el proyecto del grupo de recursos de Azure se ha actualizado en esta versión para trabajar con los cmdlets de PowerShell de Azure nuevos versión 1.0.  Esta secuencia de comandos nuevo no funcionará desde con Visual Studio cuando se usa una versión de SDK antes de 2,8.  

Las secuencias de comandos de proyectos creados en versiones anteriores de SDK no se ejecutarán desde dentro de Visual Studio cuando se usa el SDK de 2,8.  Todas las secuencias de comandos seguirán funcionando fuera de Visual Studio con la versión de los cmdlets de PowerShell de Azure.  

El SDK de 2,8 requiere la versión 1.0 de los cmdlets de PowerShell de Azure.  Todas las otras versiones de SDK requieren versión 0.9.8 de los cmdlets de PowerShell de Azure.  Para obtener más información, consulte [este](http://go.microsoft.com/fwlink/?LinkID=623011) blog.

###<a name="web-tools-extensions"></a>Extensiones de herramientas Web

####<a name="known-issues"></a>Problemas conocidos

Los siguientes problemas conocidos se tratará en la siguiente versión.

- Aplicación de servicio relacionados con la nube y el Explorador de servidores gesto para entornos sin producción (como China Azure o los clientes de la pila de Azure) no funcionan. Los clientes en estas áreas afectadas, descargar el perfil de publicación desde el portal de Azure se permitirá la capacidad de publicación. Una versión futura repare gestos como "Adjuntar depurador" y "Ver registros de transmisión" Azure China y a los clientes de la pila. 
- Los clientes pueden ver errores durante el servicio de aplicación de creación de instancia de la información de la aplicación a la que está implementando está en una región distinta oriental de Estados Unidos. En estos casos, la creación de un servicio de aplicación en el portal y descargar el perfil de publicación permitirán escenarios de publicación. 

###<a name="azure-hdinsight-tools"></a>Herramientas HDInsight de Azure

####<a name="new-updates"></a>Nuevas actualizaciones

- Puede ejecutar la consulta de sección en el clúster a través de HiveServer2 con prácticamente ninguna sobrecarga y ver que el trabajo de registros en tiempo real.
- Con la nueva sección ejecución vista de tareas puede profundizar en su trabajo más profundo, encontrar más información e identificar posibles problemas.

Para obtener más información, vea [Azure SDK 2,8 para 2013 de Visual Studio y Visual Studio de 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/). 

## <a name="azure-sdk-for-net-281"></a>Azure SDK para .NET 2.8.1

### <a name="known-issues-for-visual-studio-2013-and-visual-studio-2015"></a>Problemas conocidos de 2013 de Visual Studio y Visual Studio de 2015
 
1. WebJob desencadenada publica en ranuras tendrá errores y mostrar y no establecer una programación, pero se ingresar la WebJob a Azure. Los clientes que necesitan un trabajo programado, a continuación, pueden usar el Portal de Azure para configurar la programación de la WebJob. 
2. Clientes de Python pueden experimentar problemas de depurador. Grupo de servicio está implementando una solución para este pero si los clientes están afectados, por favor, deje que Microsoft sabe en los foros o en el blog de anuncio o liberar sección de comentarios de notas. 
3. Los clientes de algunas regiones (como Sur India) experimentará errores de aprovisionamiento de servicio de aplicación. Esto es coherente con el portal y clientes que experimentan este problema, pueden usar el portal de Azure para solicitar acceso a publicar en estas áreas de geo. Una vez que solicitan acceso a estas regiones mediante el aprovisionamiento de portal Azure debería funcionar. 

##<a name="azure-sdk-for-net-282"></a>Azure SDK para .NET 2.8.2

Después de la instalación de la 2.8.2 herramientas de clientes pueden experimentar el problema siguiente.         

- Si usa Windows 10 y no ha instalado Internet Explorer, puede recibir un error "No se encontró Internet Explorer".
Para resolver el problema, instale Internet Explorer utilizando el cuadro de diálogo Agregar o quitar componentes de Windows.

Si observa este problema, use la característica Enviar una sonrisa para informar.

Para obtener más información, consulte [esta](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-2-for-net/) entrada.
##<a name="other-updates"></a>Otras actualizaciones

Otras actualizaciones, consulte [anuncio de Azure SDK 2,8 publicar](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

##<a name="also-see"></a>Vea también

[Publicación de anuncio de Azure SDK 2,8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[Información de retirada de Azure SDK para .NET y las API y soporte técnico](https://msdn.microsoft.com/library/azure/dn479282.aspx)

