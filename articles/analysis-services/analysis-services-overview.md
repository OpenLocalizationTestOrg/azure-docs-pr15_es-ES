<properties
   pageTitle="¿Qué es servicios de análisis de Azure | Microsoft Azure"
   description="Obtenga una visión general de Analysis Services en Azure."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="owend"/>

# <a name="what-is-azure-analysis-services"></a>¿Qué es servicios de análisis de Azure?
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Integrada en el motor de análisis comprobada en Microsoft SQL Server Analysis Services, Azure Analysis Services proporciona empresariales modelado de datos en la nube.

> [AZURE.IMPORTANT] Azure Analysis Services está en **vista previa**. Hay algunas cosas que no están funcionando todavía. Asegúrese de desproteger [las expectativas de vista previa](#preview-expectations) más adelante en este artículo. Y asegúrese de seguir controlando nuestro [blog de Azure Analysis Services](https://go.microsoft.com/fwlink/?linkid=830920) para obtener la información más reciente.

## <a name="built-on-sql-server-analysis-services"></a>Integrada en SQL Server Analysis Services
Azure Analysis Services es compatible con el mismo SQL Server 2016 Analysis Services Enterprise Edition que ya conoce. Azure Analysis Services es compatible con los modelos tabulares en el nivel de compatibilidad de 1200. Se admiten DirectQuery, particiones, seguridad a nivel de fila, bidireccional relaciones y traducciones.

## <a name="use-the-tools-you-already-know"></a>Use las herramientas que ya conoce
![Herramientas de desarrollo BI](./media/analysis-services-overview/aas-overview-dev-tools.png)

Al crear modelos de datos de Azure Analysis Services, use las herramientas para SQL Server Analysis Services. Crear e implementar modelos tabulares usando las últimas versiones de [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) o uso de plantillas de [Powershell de Azure](../powershell-install-configure.md) y el [Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md) en [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connect-to-data-sources"></a>Conectarse a orígenes de datos
Modelos de datos se implementan en servidores de soporte técnico de Azure conectarse a datos orígenes locales en su organización o en la nube. Combinar datos de ambas local y la nube de orígenes de datos para una solución de inteligencia empresarial híbrida.

![Orígenes de datos](./media/analysis-services-overview/aas-overview-data-sources.png)

Porque el servidor no está en la nube, conectarse a orígenes de datos de nube es transparente. Al conectarse a orígenes de datos locales, la [puerta de enlace de datos local](analysis-services-gateway.md) garantiza conexiones rápidas y seguras con el servidor de Analysis Services en la nube.  

 \*Algunos orígenes de datos no se admiten todavía en la vista previa. Para obtener más información, vea [las expectativas de vista previa](#preview-expectations) más adelante en este artículo.

## <a name="explore-your-data-from-anywhere"></a>Explorar los datos desde cualquier lugar
Conectarse y [obtener datos](analysis-services-connect.md) de los servidores de sobre en cualquier lugar. Azure Analysis Services admite la conexión de Power BI Desktop, Excel, aplicaciones personalizadas y herramientas basadas en el explorador.

![Visualizaciones de datos](./media/analysis-services-overview/aas-overview-visualization.png)

 \*Power BI incrustados no es todavía compatible en vista previa.

## <a name="secure"></a>Proteger

#### <a name="user-authentication"></a>Autenticación de usuario
Autenticación de usuario de Azure Analysis services se encarga de [Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md). Al intentar iniciar sesión en una base de datos de Azure Analysis Services, los usuarios utilizar una identidad de organización cuenta con acceso a la base de datos que están intentando tener acceso. Estas identidades de usuario deben ser miembros de Azure Active Directory predeterminado para la suscripción donde reside el servidor de Azure Analysis Services. [Integración de directorios](https://technet.microsoft.com/library/jj573653.aspx) entre AAD y un local de Active Directory es una excelente forma de obtener su local acceso de usuarios a una base de datos de Azure Analysis Services, pero no es necesaria para todos los escenarios.

Los usuarios inicie sesión con el nombre principal de usuario (UPN) de su cuenta y su contraseña. Cuando sincroniza con un Active Directory local, UPN del usuario suele ser su dirección de correo de organización.

Permisos para administrar lo recursos de servidor de Azure Analysis Services se administran asignando usuarios a funciones dentro de su suscripción de Azure. De forma predeterminada, los administradores de suscripción tienen permisos de propietario para lo recursos de servidor en Azure. Pueden agregar más usuarios con el Administrador de recursos de Azure.

#### <a name="data-security"></a>Seguridad de los datos
Azure Analysis Services utiliza el almacenamiento de blobs de Windows Azure para conservar el almacenamiento y los metadatos de las bases de datos de Analysis Services. Archivos de datos en Blob están encriptados con Azure Blob Server lado cifrado (SSE). Cuando utiliza el modo de consulta directa, se almacenan sólo metadatos; los datos reales que se accede desde el origen de datos en tiempo de consulta.

#### <a name="on-premises-data-sources"></a>Orígenes de datos locales
Proteger el acceso a datos residentes local en su organización se consigue instalar y configurar una [puerta de enlace de datos local](analysis-services-gateway.md). Las puertas de enlace proporcionan acceso a datos para consulta directa y modos en memoria. Cuando un modelo de Azure Analysis Services se conecta a un origen de datos local, se crea una consulta junto con las credenciales cifradas para el origen de datos local. El servicio de nube de puerta de enlace analiza la consulta e inserta la solicitud a un Bus de servicio de Azure. La puerta de enlace local explora el Bus de servicio de Azure para solicitudes pendientes. La puerta de enlace obtiene la consulta, descifra las credenciales y se conecta al origen de datos para la ejecución. Los resultados se envían desde el origen de datos, vuelva a la puerta de enlace y, a continuación, en la base de datos de Azure Analysis Services.

Azure Analysis Services se rige por los [Términos de Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) y la [Declaración de privacidad de Microsoft Online Services](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).
Para obtener más información sobre la seguridad de Azure, consulte el [Centro de confianza de Microsoft](https://www.microsoft.com/trustcenter/Security/AzureSecurity).

## <a name="get-help"></a>Obtener ayuda
Azure Analysis Services es sencillo para configurar y administrar. Puede encontrar toda la información que necesita para crear y administrar un servidor aquí. Al crear un modelo de datos para implementar el servidor, es similar a como para crear un modelo de datos que se implementa en un servidor local. Hay una amplia biblioteca de conceptuales, procedimientos, tutoriales y artículos de referencia en [Analysis Services en MSDN](https://msdn.microsoft.com/library/bb522607.aspx).

También tenemos una serie de vídeos útiles en [Azure Analysis Services en canal 9](https://channel9.msdn.com/series/Azure-Analysis-Services).

Cosas va a cambiar rápidamente. Siempre puede obtener la información más reciente en el [blog de Azure Analysis Services](https://go.microsoft.com/fwlink/?linkid=830920).

## <a name="community"></a>Comunidad
Analysis Services tiene una comunidad atractivos de usuarios. Unirse a la conversación en el [foro de Azure Analysis Services](https://aka.ms/azureanalysisservicesforum).

## <a name="feedback"></a>Comentarios
¿Tiene sugerencias o solicitudes de características? Asegúrese de dejar sus comentarios en [Comentarios de servicios de análisis de Azure](https://aka.ms/azureanalysisservicesfeedback).

¿Tiene sugerencias sobre la documentación? Puede agregar comentarios mediante Disqus en la parte inferior de cada artículo.

## <a name="preview-expectations"></a>Expectativas de vista previa
Azure Analysis Services está en vista previa. Hay algunas cosas que debe tener en cuenta.

##### <a name="server-modes"></a>Modos de servidor
Azure Analysis Services admite actualmente modo Tabular para modelos tabulares en el nivel de compatibilidad de 1200. Modo multidimensional y minería de datos y PowerPivot para SharePoint modo no son compatibles.

##### <a name="data-sources"></a>Orígenes de datos
Vista previa, de los siguientes orígenes de datos son compatibles con modelos tabulares de 1200 implementados en un servidor de Azure Analysis Services.

| **Nube** | **Local** |
|--------------|------------|
| Base de datos SQL | SQL Server |
| Almacén de datos SQL | PUNTOS DE ACCESO |
|      | Oracle |
|       | Teradata |


### <a name="data-source-providers"></a>Proveedores de datos de origen
Modelos de datos de Azure Analysis Services pueden requerir proveedores de datos distintos para conectarse a orígenes de datos de modelos de datos de SQL Server Analysis Services. Requisitos del proveedor de datos dependen del origen de datos está en la nube o local y el tipo de modelo de datos; Consulta en memoria o directo. Para obtener más información, vea [conexiones de origen de datos](analysis-services-datasource.md).


### <a name="client-connections"></a>Conexiones de cliente
Power BI incrustados no es todavía compatible en vista previa.

No se admiten libros de Excel con conexiones directas con un servidor de Azure Analysis Services y guardado en OneDrive o SharePoint Online.



## <a name="next-steps"></a>Pasos siguientes
Ahora que sabe más acerca de los servicios de análisis de Azure, es hora para empezar. Obtenga información sobre cómo [crear un servidor](analysis-services-create-server.md) de Azure e [implementar un modelo tabular](analysis-services-deploy.md) .
