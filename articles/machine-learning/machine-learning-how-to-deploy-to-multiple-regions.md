<properties
    pageTitle="Cómo implementar un servicio Web para varias regiones | Microsoft Azure"
    description="Pasos para implementar regiones (copia) un nuevo servicio Web a otro."
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondl"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="v-donglo"/>

# <a name="how-to-deploy-a-web-service-to-multiple-regions"></a>Cómo implementar un servicio Web en varias regiones

Los nuevos servicios Web de Azure permiten implementar fácilmente un servicio web a varias áreas sin necesidad de varias suscripciones o áreas de trabajo. 

Precios están una región específica, por lo tanto, debe definir un plan de facturación para cada región en la que va a implementar el servicio web.

## <a name="to-create-a-plan-in-another-region"></a>Para crear un plan en otra región

1. Inicie sesión en el [equipo de Microsoft Azure servicios Web de aprendizaje](https://services.azureml.net/).
2. Haga clic en la opción de menú **planes** .
3. En los planes sobre la página de vista, haga clic en **nuevo**.
4. En la lista desplegable de **suscripción** , seleccione la suscripción en la que se guardarán el nuevo plan.
5. En la lista desplegable **región** , seleccione una región para el nuevo plan. Las opciones del Plan para la región seleccionada se mostrará en la sección **Opciones del Plan** de la página.
6. En la lista desplegable **Grupo de recursos** , seleccione un grupo de recursos para el plan. Obtener más información sobre los grupos de recursos, vea [Administrar Azure recursos a través del portal](../azure-portal/resource-group-portal.md).
7. En **Nombre del Plan** , escriba el nombre del plan.
8. En **Opciones del Plan**, haga clic en el nivel de facturación para el nuevo plan.
9. Haga clic en **crear**.


## <a name="deploying-the-web-service-to-another-region"></a>Implementar el servicio web para otra región

1. Haga clic en la opción de menú de **Servicios Web** .
2. Seleccione el servicio Web se implementa en un área de nuevo.
3. Haga clic en **Copiar**.
4. En **Nombre del servicio Web**, escriba un nombre nuevo para el servicio web.
5. En **Descripción del servicio Web**, escriba una descripción para el servicio web.
6. En la lista desplegable de **suscripción** , seleccione la suscripción en la que se guardarán el nuevo servicio web.
7. En la lista desplegable **Grupo de recursos** , seleccione un grupo de recursos para el servicio web. Obtener más información sobre los grupos de recursos, vea [Administrar Azure recursos a través del portal](../azure-portal/resource-group-portal.md).
8. En la lista desplegable **región** , seleccione la región en la que se va a implementar el servicio web.
9. En la lista desplegable de la **cuenta de almacenamiento** , seleccione una cuenta de almacenamiento en la que desea almacenar el servicio web.
10. En la lista desplegable de **Plan de precios** , seleccione un plan en la región seleccionada en el paso 8.
11. Haga clic en **Copiar**.

