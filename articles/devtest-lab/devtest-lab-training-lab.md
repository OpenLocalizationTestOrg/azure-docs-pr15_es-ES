<properties
    pageTitle="Usar Azure DevTest prácticas de formación | Microsoft Azure"
    description="Obtenga información sobre cómo usar Azure DevTest prácticas para escenarios de aprendizaje."
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="steved0x"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="sdanie"/>

# <a name="use-azure-devtest-labs-for-training"></a>Usar Azure DevTest prácticas de formación

Prácticas de Azure DevTest puede utilizarse para implementar muchos escenarios clave además de desarrollo o prueba. Uno de estos escenarios es configurar un laboratorio de aprendizaje. Prácticas de Azure DevTest le permite crear un laboratorio donde puede proporcionar plantillas personalizadas que puede utilizar cada formación para crear entornos idénticos y aislados para formación. Puede asegurarse de que los entornos de formación están disponibles para cada formación solo cuando se necesite y contienen suficiente recursos - como máquinas virtuales - necesarios para el curso. Por último, puede compartir fácilmente la práctica con prácticas, que puede tener acceso a un solo clic.   

![Usar DevTest prácticas de formación](./media/devtest-lab-training-lab/devtest-lab-training.png)

Prácticas de Azure DevTest cumple los siguientes requisitos necesarios para llevar a cabo formación en cualquier entorno virtual: 


-   Prácticas no pueden ver las máquinas virtuales creadas por otras prácticas
-   Cada equipo formación debe ser idéntico
-   Prácticas pueden aprovisionar rápidamente sus entornos de formación
-   Controlar los costos al garantizar que prácticas no pueden obtener más máquinas virtuales de los que necesitan para el curso y también apagado máquinas virtuales cuando no está utilizando
-   Compartir fácilmente los cursos de formación con cada formación
-   Volver a utilizar repetidamente los cursos de formación


En este artículo, aprenderá diferentes funciones de Azure DevTest prácticas que se pueden usar para cumplir con los requisitos de formación descrito anteriormente y los pasos detallados que puede seguir para configurar un laboratorio de aprendizaje.  


## <a name="implementing-training-with-azure-devtest-labs"></a>Implementación de formación con Azure DevTest prácticas

1. **Crear la práctica** 

    Prácticas son el punto de partida en Azure DevTest prácticas. Una vez que cree un laboratorio, puede realizar tareas tales como agregar usuarios (prácticas) a la práctica, establecer directivas para controlar los costos, defina las imágenes de máquina virtual que pueden crear rápidamente y mucho más.   

    Obtener más haciendo clic en los vínculos en la tabla siguiente:

  	| Tarea                                                            | Lo aprendido                                                    |
|-----------------------------------------------------------------|----------------------------------------------------------------------|
| [Crear un laboratorio de prácticas de DevTest de Azure](devtest-lab-create-lab.md) | Obtenga información sobre cómo crear un laboratorio de prácticas de DevTest de Azure en el portal de Azure. |

2. **Crear máquinas virtuales de aprendizaje en minutos con imágenes de marketplace predefinidos y personalizados** 
    
    Puede elegir imágenes prediseñadas desde una amplia variedad de imágenes en el catálogo de soluciones de Azure y hacer que estén disponibles para los participantes en la práctica. Si las imágenes prediseñadas no satisfacen sus necesidades, puede crear una imagen personalizada mediante la creación de un laboratorio VM mediante una imagen listos para usar de Azure Marketplace, instalar todo el software que necesita para el curso y guardar la máquina virtual como una imagen personalizada en la práctica. 

    Obtener más haciendo clic en los vínculos en la tabla siguiente:

  	| Tarea                                                                              | Lo aprendido                                                                                                                                  |
|-----------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| [Configurar imágenes de Azure Marketplace](devtest-lab-configure-marketplace-images.md) | Obtenga información sobre cómo puede blanca imágenes de Azure Marketplace. hacer que estén disponibles para su selección únicamente las imágenes que desee para el curso.                 |
| [Crear una imagen personalizada](devtest-lab-create-template.md)                           | Crear una imagen personalizada al preinstalar el software que necesita para la formación para que prácticas pueden crear rápidamente una máquina virtual con una imagen personalizada. |

3. **Crear plantillas reutilizables para equipos de formación** 

    Una fórmula en Azure DevTest prácticas es una lista de valores de propiedad predeterminados utilizados para crear una máquina virtual. Puede crear una fórmula en la práctica por elegir una imagen, un tamaño de memoria virtual (una combinación de CPU y RAM) y una red virtual. Cada formación puede ver la fórmula en la práctica y usarlo para crear una máquina virtual. 

    Obtener más haciendo clic en los vínculos en la tabla siguiente:

  	| Tarea                                                                         | Lo aprendido                                                                                                          |
|------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------|
| [Administrar las fórmulas de DevTest prácticas para crear máquinas virtuales](devtest-lab-manage-formulas.md) | Obtenga información sobre cómo crear una fórmula de selección de una imagen, el tamaño de memoria virtual (combinación de CPU y RAM) y una red virtual. |

4. **Controlar los costos**

    Prácticas de Azure DevTest permite establecer una directiva en la práctica para especificar el número máximo de máquinas virtuales que se pueden crear un candidato en la práctica. 

    Si están llevando a cabo formación del día y desea detener todas las máquinas virtuales en un momento determinado del día y reiniciar automáticamente ellos al día siguiente, puede conseguirlo estableciendo apagado automático y fácilmente las directivas de la práctica de inicio automático. 

    Por último, cuando se complete la formación puede eliminar todas las máquinas virtuales de a la vez mediante la ejecución de un script de PowerShell único. 

    Obtener más haciendo clic en los vínculos en la tabla siguiente:

  	| Tarea                                                                                                                                    | Lo aprendido                                                      |
|-----------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------|
| [Definir directivas de práctica](devtest-lab-set-lab-policy.md)                                                                                    | Controlar los costos mediante la configuración de directivas de la práctica.                       |
| [Eliminar la práctica máquinas virtuales con una secuencia de comandos de PowerShell](devtest-lab-faq.md#how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) | Eliminar todas las prácticas de una operación cuando se complete la formación. |

5. **Compartir la práctica con cada formación**

    Prácticas se pueden acceder directamente con un vínculo que comparte con sus prácticas. Su prácticas no incluso tienen una cuenta de Azure, siempre y cuando tengan una [cuenta de Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Prácticas no pueden ver las máquinas virtuales creadas por otras prácticas.  

    Obtener más haciendo clic en los vínculos en la tabla siguiente:

  	| Tarea                                                                                                                                | Lo aprendido                                                   |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------|
| [Agregar a una formación a un laboratorio en Azure DevTest prácticas](devtest-lab-add-devtest-user.md)                                                     | Usar el portal de Azure para agregar a prácticas a los cursos de formación.       |
| [Agregar a prácticas a la práctica mediante un script de PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) | Usar PowerShell para automatizar prácticas agregando a los cursos de formación. |
| [Obtener un vínculo a la práctica](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab)                                                  | Obtenga información sobre cómo un laboratorio se puede acceder directamente a través de un hipervínculo.        |

6. **Volver a usar una y otra vez la práctica** 

    Puede automatizar la creación de la práctica, incluida la configuración personalizada, mediante la creación de una plantilla de administrador de recursos y usarlo para crear prácticas idénticos repetidamente. 

    Obtener más haciendo clic en los vínculos en la tabla siguiente:

  	| Tarea                                                                                                                               | Lo aprendido                                                      |
|------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------|
| [Crear un laboratorio usando una plantilla de administrador de recursos](devtest-lab-faq.md#how-do-i-create-a-lab-from-an-azure-resource-manager-template) | Crear prácticas en Azure DevTest prácticas con plantillas de administrador de recursos. |

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]  

