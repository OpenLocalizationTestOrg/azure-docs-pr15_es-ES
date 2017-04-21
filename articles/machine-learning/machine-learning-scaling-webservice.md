<properties
   pageTitle="Servicio web de ajuste de escala | Microsoft Azure"
   description="Obtenga información sobre cómo ajustar el tamaño de un servicio web mediante el aumento de simultaneidad y agregar nuevos extremos."
   services="machine-learning"
   documentationCenter=""
   authors="neerajkh"
   manager="srikants"
   editor="cgronlun"
   keywords="Azure máquina de aprendizaje, servicios web, operationalization, escalar, extremo, simultaneidad"
   />
<tags
   ms.service="machine-learning"
   ms.devlang="NA"
   ms.workload="data-services"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.date="10/05/2016"
   ms.author="neerajkh"/>

# <a name="scaling-a-web-service"></a>Ajuste de escala de un servicio Web

>[AZURE.NOTE] Este tema describe técnicas aplicables a un servicio Web de aprendizaje de máquina clásico. 

De forma predeterminada, cada servicio Web publicado está configurado para admitir solicitudes simultáneas 20 y puede ser de hasta 200 solicitudes simultáneas. Mientras el portal clásico Azure proporciona una manera de establecer este valor, el aprendizaje Azure optimiza automáticamente la configuración para proporcionar el mejor rendimiento para el servicio web y se omite el valor portal. 

Si se admite el plan para llamar a la API con una carga mayor que un valor máximo de llamadas simultáneas de 200, debe crear varios extremos en el mismo servicio Web. Puede distribuir aleatoriamente la carga para todos ellos.

## <a name="add-new-endpoints-for-same-web-service"></a>Agregar nuevos extremos para el mismo servicio web

La escala de un servicio Web es una tarea común. Algunas de las razones para escalar son admite más de 200 solicitudes simultáneas, aumentar la disponibilidad a través de varios extremos o proporcionar extremos independientes para el servicio web. Puede aumentar la escala agregando extremos adicionales para el mismo servicio Web a través de [Azure portal clásico](https://manage.windowsazure.com/) o el portal de [Servicio Web de Azure máquina aprendizaje](https://services.azureml.net/) .

Para obtener más información sobre cómo agregar nuevos extremos, vea [Crear extremos](machine-learning-create-endpoint.md).

Tenga en cuenta que puede ser perjudicial si no está llamando a la API con una tasa igualmente alta con un recuento de simultaneidad alta. Es posible que vea los tiempos de espera se o picos en la latencia si coloca una carga relativamente baja en una API configurada para la carga es alta.

Las API sincrónicas se usan normalmente en situaciones donde se desea una latencia baja. Latencia aquí implica el tiempo que dure la API completar una solicitud y no es responsable de los retrasos en la red. Supongamos que tiene una API con una latencia de 50 ms. Consumir totalmente la capacidad disponible con nivel alto de acelerador y llamadas simultáneas de Max = 20, debe llamar a esta API 20 * 1000 / 50 = 400 veces por segundo. Extender aún más, un máximo de llamadas simultáneas de 200 le permite llamar a los tiempos de API 4000 por segundo, suponiendo que una latencia de 50 ms.

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png
