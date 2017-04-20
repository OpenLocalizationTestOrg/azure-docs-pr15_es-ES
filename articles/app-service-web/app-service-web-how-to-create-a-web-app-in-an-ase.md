<properties
    pageTitle="Crear una aplicación web en un entorno de servicio de aplicación"
    description="Aprenda a crear planes de servicio de aplicaciones web y aplicaciones en un entorno de servicio de la aplicación"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="10/17/2016"
    ms.author="ccompy"/>

# <a name="create-a-web-app-in-an-app-service-environment"></a>Crear una aplicación web en un entorno de servicio de aplicación

## <a name="overview"></a>Información general

Este tutorial muestra cómo crear aplicaciones web y planes de servicio de aplicaciones en un [Entorno de aplicación de servicio](app-service-app-service-environment-intro.md) (ASE). 

> [AZURE.NOTE] Si desea obtener información sobre cómo crear una aplicación web pero no tiene que realizar en un entorno de servicio de aplicación, vea [crear una aplicación web de .NET](web-sites-dotnet-get-started.md) o uno de los tutoriales relacionados para otros idiomas y marcos.

## <a name="prerequisites"></a>Requisitos previos

En este tutorial se supone que ha creado un entorno de servicio de aplicación. Si todavía no lo ha hecho todavía, vea [crear un entorno de servicio de aplicación](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Crear una aplicación web

1. En el [Portal de Azure](https://portal.azure.com/), haga clic en **Nuevo > Web + Mobile > Online**. 

    ![][1]

2. Seleccione la suscripción.  

    Si tiene varias suscripciones tenga en cuenta que, para crear una aplicación en su entorno de servicio de la aplicación, debe usar la misma suscripción que utilizó para crear el entorno. 

3. Seleccione o cree un grupo de recursos.

    *Grupos de recursos* le permiten administrar recursos relacionados de Azure como una unidad y son útiles para establecer reglas de *control de acceso basado en roles* (RBAC) para las aplicaciones. Para obtener más información, vea [administrar los recursos de Azure][ResourceGroups]. 

4. Seleccione o cree un plan de servicio de aplicación.

    *Planes de servicio de la aplicación* se administran conjuntos de aplicaciones web.  Normalmente cuando seleccione precios, se aplica el precio cargado el plan de servicios de aplicación, en lugar de a las aplicaciones individuales. En un ASE paga para las instancias de cálculo asignadas a la ASE en lugar de enumeró con ASP.  Para ajustar el número de instancias de una aplicación web escalar las instancias de la aplicación de servicio plan y afecta a todas las aplicaciones web de ese plan.  Algunas características como ranuras del sitio o la integración de VNET también tienen las restricciones de cantidad dentro del plan.  Para obtener más información, vea [información general de los planes de servicio de la aplicación de Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)

    Puede identificar los planes de servicio de aplicaciones en su ASE consultando la ubicación que anotó en el nombre del plan.  

    ![][5]

    Si desea usar un plan de servicio de aplicación que ya existe en su entorno de servicio de aplicación, seleccione ese plan. Si desea crear un nuevo plan de servicio de aplicaciones, vea la siguiente sección de este tutorial, [crear un plan de servicio de aplicaciones en un entorno de servicio de la aplicación](#createplan).

5. Escriba el nombre para la aplicación web y, a continuación, haga clic en **crear**. 

    Si su ASE utiliza una VIP externo es la dirección URL de una aplicación en un ASE: [*nombre*]. [*nombre de su entorno de aplicación de servicio*]. p.azurewebsites.net en lugar de [*nombre*]. azurewebsites.net
    
    Si su ASE usa una VIP interno, a continuación, en la dirección URL de una aplicación que es ASE: [*nombre*]. [*subdominio especificado durante la creación de ASE*]   
    Después de seleccionar el ASP durante la creación de ASE, verá el subdominio actualizar debajo del **nombre**

## <a name="createplan"></a>Crear un plan de servicio de aplicación

Cuando se crea un plan de servicio de aplicaciones en un entorno de servicio de aplicación, las opciones de trabajo son diferentes, como no hay ningún trabajador compartido en un ASE.  Los trabajadores que debe usar están la que se han asignado a la ASE por el administrador.  Esto significa que para crear un nuevo plan, debe tener asignados a su grupo de trabajo ASE que el número total de instancias a través de todos los planes que ya están en ese grupo de trabajo de sus colegas más.  Si no tiene suficiente los trabajadores de su grupo de trabajo ASE para crear su plan, necesita trabajar con el Administrador de ASE conseguirlos agregado.

Otra diferencia con los planes de servicio de aplicación hospedado en un entorno de servicio de la aplicación es la falta de selección de precios.  Cuando tiene un entorno de servicio de la aplicación que está pagando por los recursos de cálculo utilizados por el sistema y no tiene agregados cargos para los planes en el entorno.  Normalmente al crear un plan de servicio de la aplicación selecciona un plan de precio que determina la facturación.  Un entorno de servicio de la aplicación es, esencialmente, una ubicación privada donde puede crear contenido.  Pago para el entorno y no para hospedar el contenido.

Las instrucciones siguientes muestran cómo crear un plan de servicio de la aplicación mientras está creando una aplicación web, como se explica en la sección anterior del tutorial.

1. Haga clic en **Crear nuevo** en el interfaz de usuario de selección de plan y proporcione un nombre para su plan de como lo haría normalmente fuera de un ASE.

2. Seleccione la ASE que desea usar en el selector de ubicación.

    Dado que un entorno de servicio de la aplicación es, esencialmente, una ubicación de implementación privada, se muestra en la ubicación. 

    ![][2]

    Después de la selección de un ASE en el selector de ubicación, la creación del plan de servicio de aplicaciones UI actualiza.  La ubicación ahora muestra el nombre del sistema ASE y la región en y, a continuación, el selector de plan de precios se reemplaza con un selector de grupo de trabajo.  

    ![][3]

### <a name="selecting-a-worker-pool"></a>Seleccionar un grupo de trabajo

Normalmente en el servicio de aplicación de Azure y fuera de un entorno de servicio de la aplicación, hay 3 tamaños de cálculo que están disponibles con la selección de un plan de precio dedicado.  De manera similar, para un ASE puede definir hasta 3 grupos de trabajadores y especifique el tamaño de cálculo que se utiliza para ese grupo de trabajo.  Lo que significa de inquilinos de la ASE es que en lugar de seleccionar un plan de precio con tamaño de cálculo para su plan de servicios de aplicación, seleccione lo que se denomina un *grupo de trabajo*.  

La selección de grupo de trabajo IU muestra el tamaño de cálculo utilizado para dicho grupo de trabajo debajo del nombre.  La cantidad disponible se refiere a calculan cuántas instancias están disponibles para su uso en ese grupo.  El grupo total puede tener más instancias que este número, pero este valor se refiere a simplemente cuántos no están en uso.  Si necesita ajustar el entorno del servicio de aplicación para agregar más recursos informáticos vea [Configurar el entorno del servicio de aplicación](app-service-web-configure-an-app-service-environment.md).

![][4]

En este ejemplo, verá dos grupos de trabajo disponibles. Eso es porque el Administrador de ASE sólo asigna hosts en los grupos de trabajo de dos.  La tercera podría mostrarse cuando hay máquinas virtuales asignadas en él.  

## <a name="after-web-app-creation"></a>Después de la creación de aplicaciones web

Existen algunas consideraciones para ejecutar aplicaciones web y administrar planes de servicio de aplicaciones en un ASE que se deben tener en cuenta.  

Como se mencionó anteriormente, el propietario de la ASE es responsable el tamaño del sistema y como resultado también son responsables de asegurar que hay suficiente capacidad para hospedar los planes de servicio de la aplicación que desee. Si no hay ningún trabajador disponible, no podrá crear su plan de servicios de aplicación.  También es True para el escalado de la aplicación web.  Si necesita más instancias tendrá que obtener el Administrador de entorno de servicio de aplicación para agregar más trabajadores.

Después de crear la aplicación web y el plan de servicio de la aplicación es una buena idea escalar hacia arriba.  En un ASE siempre debe tener al menos 2 instancias de su plan de servicios de aplicación para proporcionar tolerancia a errores para las aplicaciones.  Escala de un plan de servicio de aplicaciones en un ASE es el mismo como normal a través del plan de servicio de aplicación interfaz de usuario.  Para obtener más información sobre la escala, [cómo ajustar el tamaño de una aplicación web en un entorno de servicio de aplicación](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment
[ResourceGroups]: http://azure.microsoft.com/documentation/articles/resource-group-portal/
[AzurePowershell]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
