<properties
   pageTitle="Documentación de Ayuda de múltiples Geo | Microsoft Azure"
   description="Obtenga información sobre cómo crear un área de trabajo y publicar un servicio web en una región Azure diferente de los Estados Unidos Central Sur (SCUS) región Azure."
   services="machine-learning"
   documentationCenter=""
   authors="tedway"
   manager="jhubbard"
   editor="rmca14"
   tags=""/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="tedway; neerajkh"/>

# <a name="multi-geo-help-documentation"></a>Documentación de Ayuda de múltiples Geo

Este artículo describe cómo puede crear un área de trabajo y publicar un servicio web en otras áreas de Azure.

## <a name="create-a-workspace"></a>Crear un área de trabajo

1. Inicie sesión en el Portal de clásico de Azure.

2.  Haga clic en **+ nuevo** > **DATA SERVICES** > **aprendizaje** > **crear rápido**.  En **ubicación** , seleccione otra región, como **Sudeste asiático**.
![Imagen de múltiples Geo ayuda 1][1]
3. Seleccione el área de trabajo y, a continuación, haga clic en **iniciar sesión m Studio**.
![Imagen de múltiples Geo ayuda 2][2]

4. Ahora tiene un área de trabajo en otra región que pueda usar igual que cualquier otra área de trabajo. Para cambiar entre las áreas de trabajo, busque la esquina superior derecha de la pantalla. Haga clic en la lista desplegable, seleccione la región y, a continuación, seleccione el área de trabajo. Todo lo que es local en la región del área de trabajo; Por ejemplo, todos los servicios web creados a partir de un área de trabajo estarán en el mismo el área de trabajo se encuentra en la región.
![Imagen de múltiples Geo ayuda 3][3]

## <a name="open-an-experiment-from-gallery"></a>Abrir un ensayo de galería

Si abre un ensayo de galería, también puede seleccionar qué región que desea copiar el ensayo a.

![Imagen de múltiples Geo ayuda 4][4a]

## <a name="web-service-management"></a>Administración del servicio Web

Para administrar mediante programación los servicios web, como de reciclaje, usar la dirección de la región específica:
- https://asiasoutheast.Management.azureml.NET
- https://europewest.Management.azureml.NET

### <a name="things-to-note"></a>Cosas que tenga en cuenta

1.  Solo puede copiar experimentación entre áreas de trabajo que pertenecen a la misma región de esta manera. Si necesita copiar ensayo a través de áreas de trabajo en diferentes regiones, puede usar el cmdlet de [PowerShell](http://aka.ms/amlps) [*AmlExperiment copia*](https://github.com/hning86/azuremlps/blob/master/README.md#copy-amlexperiment) para llevar a cabo que. Otra solución es publicar el ensayo en Galería de modo que no figuran, ábralo en el área de trabajo de las regiones.
2.  El selector de región solo mostrará las áreas de trabajo de una región específica a la vez. En el futuro, podrá ver una lista completa de áreas de trabajo que tiene acceso a través de todas las regiones al mismo tiempo.  
3.  Se creará y alojado en sur Central US un área de trabajo libre o el área de trabajo (anónimo) de acceso de invitado En el futuro, podrá crear áreas de trabajo de Access Free/invitado en la región que elija.  
4.  Servicios Web implementados de un área de trabajo del Sureste asiático también se alojarán en Asia Sureste. En el futuro, podrá tener la flexibilidad de crear experimentación en una región e implementar generada extremos de servicios web en diferentes regiones.  

## <a name="more-information"></a>Más información

Formule una pregunta en el [foro de aprendizaje del equipo de Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning).

<!--Image references-->
[1]: ./media/machine-learning-multi-geo/multi-geo_1.png
[2]: ./media/machine-learning-multi-geo/multi-geo_2.png
[3]: ./media/machine-learning-multi-geo/multi-geo_3.png
[4a]: ./media/machine-learning-multi-geo/multi-geo_4a.png
