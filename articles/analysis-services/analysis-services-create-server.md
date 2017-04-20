<properties
   pageTitle="Crear un servidor de Analysis Services en Azure | Microsoft Azure"
   description="Obtenga información sobre cómo crear una instancia del servidor de Analysis Services en Azure."
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
   ms.date="10/24/2016"
   ms.author="owend"/>

# <a name="create-an-analysis-services-server"></a>Crear un servidor de Analysis Services
En este artículo le guiará a través de la creación de un nuevo recurso de servidor de Analysis Services en su suscripción de Azure.

## <a name="before-you-begin"></a>Antes de empezar
Para empezar, debe:

- **Suscripción de Azure**: visite [Prueba gratuita de Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) para crear una cuenta.
- **Grupo de recursos**: usar un grupo de recursos que ya tiene o [cree uno nuevo](../azure-resource-manager/resource-group-overview.md).

> [AZURE.NOTE] Crear un servidor de Analysis Services podría provocar un nuevo servicio facturable. Para obtener más información, consulte precios de Analysis Services.

## <a name="create-an-analysis-services-server"></a>Crear un servidor de Analysis Services

1. Inicie sesión en el [portal de Azure](https://portal.azure.com).

2. Haga clic en **+ nuevo** > **inteligencia + análisis** > **Analysis Services**.

3. En el módulo de **Analysis Services** , rellene los campos necesarios y, a continuación, presione **crear**.

    ![Crear servidor](./media/analysis-services-create-server/aas-create-server-blade.png)

    - **Nombre del servidor**: escriba un nombre único que se utiliza para hacer referencia al servidor.

    - **Suscripción**: seleccione la suscripción a este servidor efectos a.

    - **Grupo de recursos**: estos son los contenedores diseñados para ayudarle a administrar una colección de recursos de Azure. Para obtener más información, vea [los grupos de recursos](../resource-group-overview.md).

    - **Ubicación**: ubicación de centro de datos de Azure este aloja el servidor. Elija una ubicación más cercana a la base de usuarios más grande.

    - **Nivel de precios**: seleccione un nivel de precios. Los modelos tabulares hasta 100 GB son compatibles. Siempre puede cambiar el nivel de precios más adelante.

4. Haga clic en **crear**.

Crear suele en un minuto; a menudo unos pocos segundos. Si selecciona **Agregar portal**, desplácese hasta el portal para ver el nuevo servidor. O bien, vaya a **más servicios** > **Analysis Services** para ver si el servidor está listo. Si no aparece la actualización de la lista.

 ![Panel](./media/analysis-services-create-server/aas-create-server-dashboard.png)


## <a name="next-steps"></a>Pasos siguientes
Una vez que haya creado su servidor, puede [implementar un modelo](analysis-services-deploy.md) a ella usando SSDT o con SSMS.

Si un modelo que se implementa en el servidor se conecta a orígenes de datos local, debe instalar una [puerta de enlace de datos local](analysis-services-gateway.md) en un equipo de su red.
