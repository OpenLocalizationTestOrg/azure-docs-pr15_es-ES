<properties
    pageTitle="Información general de portal de Microsoft Azure"
    description="Obtenga información sobre cómo usar el portal de Microsoft Azure."
    services=""
    documentationCenter=""
    authors="davidwrede"
    manager="dwrede"
    editor="jimbe"/>

<tags
    ms.service="na"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="12/16/2015"
    ms.author="dwrede"/>

# <a name="microsoft-azure-portal-overview"></a>Información general de portal de Microsoft Azure

El portal de Microsoft Azure es un lugar central donde puede aprovisionar y administrar los recursos de Azure.  Este tutorial se familiarice con el portal y muestran cómo utilizar algunas de estas capacidades claves:
- **Catálogo de soluciones completa** que le permite examinar miles de elementos de Microsoft y otros proveedores que pueden adquirir o se aprovisione.
- **Experiencia unificada y scalable examinar** que hace que sea fácil encontrar los recursos que le interesa y realiza diversas operaciones de administración.
- **Páginas de administración coherente** (o módulos) que le permiten administrar diversos de Azure servicios a través de una manera coherente de exposición de la configuración, acciones, facturación información, los datos de supervisión y el uso de salud y mucho más.
- Una **experiencia personal** que le permite crear una pantalla de inicio personalizadas que muestra la información que desea ver cada vez que inicie sesión.  También puede personalizar cualquiera de los módulos de administración que contienen los mosaicos.

 ![Orientación de la interfaz de usuario de Portal Azure][UIOrientation]

## <a name="before-you-get-started"></a>Antes de empezar

Necesitará una suscripción válida a Azure para ir a través de este tutorial.  Si no tiene una, a continuación, [registrarse para una prueba gratuita](https://azure.microsoft.com/pricing/free-trial/) hoy mismo.  Una vez que tenga una suscripción, puede acceder al portal en [https://portal.azure.com].

## <a name="how-to-create-a-resource"></a>Cómo crear un recurso

Azure tiene un catálogo de soluciones con miles de elementos que se pueden crear desde un solo lugar.  Supongamos que desea crear una máquina virtual de Windows Server 2012 nuevo.  La + nuevo concentrador es el punto de entrada a un conjunto de curated de categorías destacadas desde el catálogo de soluciones.  Cada categoría tiene un conjunto pequeño de elementos destacados junto con un vínculo al catálogo de soluciones completa que muestra todas las categorías y búsqueda. Para crear dicha máquina virtual de Windows Server 2012 nuevo, realice las siguientes acciones:  

1.  Windows Server 2012 se presente, por lo que puede seleccionar desde la categoría de cálculo.  
2.  Rellenar algunas entradas básicas en un formulario.
3.  Haga clic en 'Crear' y comenzará la VM proporcionando inmediatamente.

El concentrador de notificaciones le avisará cuando se ha creado el recurso y se abrirá un módulo de administración (que siempre examine los recursos más adelante).

![Categorías de portal][PortalCategories]


## <a name="how-to-find-your-resources"></a>Cómo encontrar los recursos

Siempre puede anclar frecuente recursos a su startboard, pero es posible que deba examinar algo que no tienen acceso con frecuencia.  El concentrador de examinar que se muestra a continuación es la manera de obtener acceso a todos los recursos.  Puede filtrar por la suscripción, elija columnas, o cambiar el tamaño y vaya a los módulos de administración haciendo clic en elementos individuales.

![Examinar concentrador][BrowseHub]

## <a name="how-to-manage-and-delegate-access-to-a-resource"></a>Cómo administrar y delegar el acceso a un recurso

Desde este módulo que puede conectarse a la máquina virtual mediante Escritorio remoto, supervisar los indicadores de rendimiento clave, controlar el acceso a esta VM con acceso basado en roles (RBAC), configure la máquina virtual y realizar otras tareas de administración importantes.  Delegación de acceso basado en roles es fundamental para administrar en escala.  Haga clic [aquí](./active-directory/role-based-access-control-configure.md) para obtener más información sobre ella. Para delegar el acceso a un recurso, realice las siguientes acciones:

1.  Vaya a los recursos.
2.  Haga clic en todas las configuraciones de la sección Essentials.
3.  En la lista configuración, haga clic en 'Usuarios'.
4.  En la barra de comandos, haga clic en 'Agregar'.
5.  Elija un usuario y una función.

![Administrar un recurso][ManageResource]

## <a name="how-to-customize-a-resource-blade"></a>Cómo personalizar una hoja de recursos

Azure haya configurado previamente recibiera los módulos para los recursos, pero los mosaicos en estos módulos son los suyos a control.  Puede ir fácilmente a personalizar el modo para agregar, quitar, cambiar el tamaño o reorganizar los mosaicos. Para personalizar un módulo, realice las siguientes acciones:

1.  Vaya a los recursos.
2.  Haga clic en la '...' en la parte superior de la hoja que desea personalizar.
3.  Haga clic en 'Agregar elementos'.
4.  Iniciar arrastrar y soltar elementos.  

![Personalizar aspas][CustomizeBlades]

## <a name="how-to-get-help"></a>Cómo obtener ayuda

Si alguna vez tiene un problema, estamos aquí para usted.  El portal tiene una página de ayuda y soporte técnico que se indican en la dirección correcta.  Dependiendo de su [plan de asistencia](https://azure.microsoft.com/support/plans/), también puede crear vales de soporte técnico directamente en el portal.  Después de crear una incidencia de soporte técnico, puede administrar el ciclo de vida del vale desde dentro del portal. Puede ir a la Ayuda y página de soporte técnico, vaya a examinar -> ayuda + soporte técnico.  

![Ayuda y soporte técnico][HelpSupport]

## <a name="summary"></a>Resumen

Vamos a revisar lo que ha aprendido en este tutorial:
- Ha aprendido a registrarse, obtener una suscripción y desplácese hasta el portal
- ¿Tiene orientación en el portal de interfaz de usuario y cómo crear y explorar los recursos que ha aprendido
- Ha aprendido a crear un recurso y explorar los recursos
- Ha aprendido la estructura o administración de módulos y cómo puede administrar de forma coherente diferentes tipos de recursos
- Ha aprendido a personalizar el portal para recopilar la información que le interesan a la parte frontal y centro
- Ha aprendido a controlar el acceso a los recursos con acceso basado en roles (RBAC)
- Ha aprendido a obtener ayuda y soporte técnico

El portal de Microsoft Azure simplifica considerablemente crear y administrar las aplicaciones en la nube.  Eche un vistazo en el [blog de administración](https://azure.microsoft.com/blog/topics/management/) para mantener actualizados que estamos constantemente [escuchar comentarios](https://feedback.azure.com/forums/223579-azure-preview-portal/) y realizar mejoras.  [Blog de ScottGu](http://weblogs.asp.net/scottgu) es otro lugar ideal para buscar todas las actualizaciones de Azure.

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png
