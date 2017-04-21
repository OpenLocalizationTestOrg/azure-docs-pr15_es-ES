<properties
   pageTitle="Escenarios y ejemplos de gobernanza de suscripción | Microsoft Azure"
   description="Proporciona ejemplos de cómo implementar gobernanza de Azure suscripción para escenarios comunes."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="rdendtler"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2016"
   ms.author="rodend;karlku;tomfitz"/>

# <a name="examples-of-implementing-azure-enterprise-scaffold"></a>Ejemplos de implementación de scaffold enterprise de Azure

En este tema se proporciona ejemplos de cómo una empresa puede implementar las recomendaciones para un [scaffold Azure enterprise](resource-manager-subscription-governance.md). Utiliza una empresa ficticia denominada Contoso para ilustrar los procedimientos recomendados para escenarios comunes. 

## <a name="background"></a>Fondo

Contoso es una empresa en todo el mundo que ofrece soluciones de cadena de suministro de los clientes de todo el contenido de un modelo de "Software como servicio" a un modelo de empaquetado implementado en local.  Software que se convierten en todo el mundo con los centros de desarrollo significativo en India, Estados Unidos y Canadá. 

La parte de ISV de la compañía se divide en varias unidades de negocio independientes que administración productos en una empresa significativa. Cada empresa tiene su propio desarrolladores, los jefes de producto y arquitectos. 

La unidad de negocio de servicios de tecnología de empresa (HTA) proporciona la capacidad de TI centralizada y administra varios centros de datos donde las unidades de negocio hospedan sus aplicaciones. Junto con la administración de los centros de datos, la organización de ETS proporciona y administra colaboración centralizada (por ejemplo, correo electrónico y sitios Web) y servicios de red y telefonía. También administran las cargas de trabajo orientados al cliente para unidades de negocio más pequeñas que no tienen personal de operaciones. 

En este tema se utilizan los roles siguientes:

- Dave es el Administrador de Azure ETS.
- Ana es Director de desarrollo de Contoso de la unidad de negocio de la cadena de suministro. 

Contoso es necesario crear una aplicación de línea de negocio y la aplicación orientados al cliente. Ha decidido ejecutar las aplicaciones de Azure. David lee el tema de [gobernanza de suscripción normativas](resource-manager-subscription-governance.md) y está listo para implementar las recomendaciones. 

## <a name="scenario-1-line-of-business-application"></a>Escenario 1: aplicación de línea de negocio

Contoso es crear un sistema de administración de código fuente (BitBucket) para usarlo con los desarrolladores en todo el mundo.  La aplicación utiliza la infraestructura como servicio (IaaS) para hospedar y consta de los servidores web y un servidor de base de datos. Los programadores acceso a los servidores en su entorno de desarrollo, pero que no necesitan tener acceso a los servidores de Azure. Contoso ETS desea permitir que el equipo y el propietario de la aplicación administrar la aplicación. La aplicación solo está disponible durante la red corporativa de Contoso. Dave debe configurar la suscripción para esta aplicación. La suscripción también hospedará otro software de programador en el futuro.  

### <a name="naming-standards--resource-groups"></a>Estándares de nomenclatura y grupos de recursos

David crea una suscripción para admitir herramientas de desarrollador que sean comunes en todas las unidades de la empresa. Necesita crear nombres significativos para los grupos de recursos y de suscripción (para la aplicación y las redes). El administrador crea los grupos de suscripción y los recursos siguientes:

| Elemento | Nombre | Descripción |
| ---- | ---- | ----------- |
| Suscripción | Contoso ETS DeveloperTools producción | Es compatible con las herramientas de desarrollador comunes |
| Grupo de recursos | rgBitBucket | Contiene el servidor de aplicaciones web y el servidor de base de datos |
| Grupo de recursos | rgCoreNetworks | Contiene las redes virtuales y conexión de puerta de enlace de sitio a otro |


### <a name="role-based-access-control"></a>Control de acceso basado en roles

Después de crear su suscripción, Dave desea asegurarse de que el correspondientes a los equipos a y los propietarios de la aplicación pueden tener acceso a sus recursos. Dave reconoce que cada grupo tiene requisitos distintos. Utiliza los grupos que se han sincronizado de Contoso Active Directory (AD) locales con Azure Active Directory y proporciona el nivel de acceso a los equipos correcto. 

Dave asigna los siguientes roles para la suscripción: 

| Función | Asignado a | Descripción |
| ---- | ----------- | ----------- |
| [Propietario](./active-directory/role-based-access-built-in-roles.md#owner) | Administra el identificador de Contoso AD | Este ID está controlado con Just en access Time (JIT) a través de la herramienta de administración de identidades de Contoso y garantiza que totalmente es auditar el acceso de propietario de la suscripción. |
| [Administrador de seguridad](./active-directory/role-based-access-built-in-roles.md#security-manager) | Departamento de administración de seguridad y el riesgo | Esta función permite a los usuarios buscar en el centro de seguridad de Azure y el estado de los recursos. |
| [Colaborador de red](./active-directory/role-based-access-built-in-roles.md##network-contributor) | Equipo de red | Esta función permite el equipo de red de Contoso administrar la VPN de sitio a sitio y las redes virtuales. |
| *Función personalizada* | Propietario de la aplicación | David crea una función que permite modificar los recursos del grupo de recursos. Para obtener más información, consulte [Funciones personalizadas en Azure RBAC](./active-directory/role-based-access-control-custom-roles.md) |

### <a name="policies"></a>Directivas de

Dave tiene los siguientes requisitos para administrar los recursos en la suscripción:

- Puesto que las herramientas de desarrollo admiten los desarrolladores en todo el mundo, no quiere impedir que los usuarios la creación de recursos en cualquier región. Sin embargo, debe saber dónde se crean los recursos. 
- Él se refiere con los costos. Por lo tanto, quiere impedir que los propietarios de la aplicación cree innecesariamente costosas máquinas virtuales de Windows.  
- Puesto que esta aplicación actúa desarrolladores en muchas unidades de negocio, quiere etiquetar cada recurso con el propietario de unidad y la aplicación de empresa. Mediante el uso de estas etiquetas, ETS puede cobrar los equipos correspondientes.

El administrador crea las siguientes [directivas del Administrador de recursos](resource-manager-policy.md): 

| Campo | Efecto | Descripción |
| ----- | ------ | ----------- |
| ubicación | auditoría | La creación de los recursos en cualquier región de auditoría |
| tipo | denegar | Denegar la creación de máquinas virtuales de serie G |
| etiquetas | denegar | Requerir la etiqueta de propietario de la aplicación |
| etiquetas | denegar | Requerir la etiqueta del centro de costo |
| etiquetas | anexar | Anexar el nombre de etiqueta **unidad de negocio** y el valor de etiqueta **ETS** a todos los recursos |


### <a name="resource-tags"></a>Etiquetas de recursos

Dave comprende que debe tener información específica en la lista para identificar el centro de costo para la implementación BitBucket. Además, Dave desea conocer todos los recursos que posee ETS. 

Añade las siguientes [etiquetas](resource-group-using-tags.md) a los grupos de recursos y recursos. 
 
| Nombre de etiqueta | Valor de etiqueta |
| -------- | --------- |
| ApplicationOwner | El nombre de la persona que administra esta aplicación. |
| CostCenter | El centro de costo del grupo al que está pagando por el consumo de Azure. |
| Unidad de negocio | **ETS** (la unidad de negocio asociada con la suscripción) |

### <a name="core-network"></a>Red principal

El equipo de administración de seguridad y riesgos de información de Contoso ETS revisa un plan propuesto de Dave para mover la aplicación para Azure. Desea asegurarse de que la aplicación no se expone a internet.  Dave también tiene aplicaciones de desarrollador en el futuro se moverá a Azure. Estas aplicaciones requieren interfaces públicas.  Para cumplir estos requisitos, que ofrece redes virtuales internas y externas y un grupo de seguridad de la red para restringir el acceso.

El administrador crea los siguientes recursos:

| Tipo de recurso | Nombre | Descripción |
| ------------- | ---- | ----------- |
| Red virtual | vnInternal | Se usa con la aplicación BitBucket y está conectado a través de ExpressRoute a la red corporativa de Contoso.  Una subred (sbBitBucket) proporciona la aplicación con un espacio de direcciones IP específica. |
| Red virtual | vnExternal | Disponible para futuras aplicaciones que requieren extremos de público. |
| Grupo de seguridad de red | nsgBitBucket | Garantiza que la superficie de ataque de esta carga de trabajo se minimiza al permitir conexiones solo en el puerto 443 para la subred donde encuentra la aplicación (sbBitBucket). |

### <a name="resource-locks"></a>Bloqueos de recursos 

Dave reconoce que la conectividad de red corporativa de Contoso a la red virtual interna debe protegerse de ejercer secuencias de comandos o el borrado accidental. 

Crea el [bloqueo de recursos](resource-group-lock-resources.md)de siguientes:

| Tipo de bloqueo | Recursos | Descripción |
| --------- | -------- | ----------- |
| **CanNotDelete** | vnInternal | Impide que los usuarios eliminar la red virtual o subredes, pero no impide la adición de nuevas subredes. |

### <a name="azure-automation"></a>Automatización de Azure 

Dave tiene nada para automatizar de esta aplicación. Aunque se creó una cuenta de Azure automatización, inicialmente no usa. 

### <a name="azure-security-center"></a>Centro de seguridad de Azure 

Administración del servicio de TI de Contoso debe identificar rápidamente y controlar amenazas. También desean comprender qué problemas exista.  

Para cumplir estos requisitos, Dave habilita el [Centro de seguridad de Azure](./security-center/security-center-intro.md)y proporciona acceso a la función de administrador de seguridad. 

## <a name="scenario-2-customer-facing-app"></a>Escenario 2: aplicación de orientados al cliente

El liderazgo de la empresa en la unidad de negocio de la cadena de suministro ha identificado distintas oportunidades para aumentar el compromiso con los clientes de Contoso mediante una tarjeta de fidelidad. Grupo de Ana debe crear esta aplicación y decide que Azure aumenta su capacidad para satisfacer las necesidades empresariales. Ana funciona con Dave de ETS configurar dos suscripciones para desarrollar y utilizar esta aplicación.

### <a name="azure-subscriptions"></a>Suscripciones de Azure 

David inicia sesión en el Portal de empresa de Azure y ve que el departamento de la cadena de suministro ya existe.  Sin embargo, como el primer proyecto de desarrollo para el equipo de la cadena de suministro en Azure este proyecto, Dave reconoce la necesidad de una nueva cuenta de equipo de desarrollo de Ana.  Crea la cuenta "I+d" para su equipo y asigna acceso a Ana. Ana inicia sesión a través del portal de la cuenta y crea dos suscripciones: uno para mantener los servidores de desarrollo y otro para mantener los servidores de producción.  Sigue los estándares de nomenclatura establecidos anteriormente al crear las siguientes suscripciones: 

| Uso de la suscripción | Nombre |
| ---------------- | ---- |
| Desarrollo | Desarrollo de LoyaltyCard SupplyChain ResearchDevelopment |
| Producción | SupplyChain operaciones LoyaltyCard producción |

### <a name="policies"></a>Directivas de

David y Ana analizar la aplicación e identifican que esta aplicación solo sirve a clientes de la región de América del Norte.  Ana y su equipo va a utilizar el entorno del servicio de aplicación de Azure y SQL Azure para crear la aplicación. Necesitan crear máquinas virtuales durante el desarrollo.  Ana quiere asegurarse de que su programadores tienen los recursos que necesitan para explorar y examinar los problemas sin Tira de ETS. 

La **suscripción de desarrollo**, cree la directiva siguiente:

| Campo | Efecto | Descripción |
| ----- | ------ | ----------- |
| ubicación | auditoría | La creación de los recursos en cualquier región de auditoría. |

No limitan el tipo de sku que un usuario puede crear en desarrollo y no requieren etiquetas para los grupos de recursos y recursos.

En la **suscripción de producción**, que crear las siguientes directivas:

| Campo | Efecto | Descripción |
| ----- | ------ | ----------- |
| ubicación | denegar | Denegar la creación de los recursos fuera de los centros de datos de Estados Unidos. |
| etiquetas | denegar | Requerir la etiqueta de propietario de la aplicación |
| etiquetas | denegar | Requerir la etiqueta de departamento. | 
| etiquetas | anexar | Anexar etiqueta para cada grupo de recursos que indica el entorno de producción. |

No limitan el tipo de sku que puede crear un usuario de producción.

### <a name="resource-tags"></a>Etiquetas de recursos 

Dave comprende que debe tener información específica para identificar los grupos de negocio correcta de facturación y la propiedad. Define etiquetas de recursos para grupos de recursos y recursos. 
 
Nombre de etiqueta | Valor de etiqueta |
| -------- | --------- |
| ApplicationOwner | El nombre de la persona que administra esta aplicación. |
| Departamento | El centro de costo del grupo al que está pagando por el consumo de Azure. |
| EnvironmentType | **Producción** (Aunque la suscripción incluye **producción** en el nombre, incluida esta etiqueta permite identificarlos fácilmente cuando examina los recursos en el portal de o en la factura). |

### <a name="core-networks"></a>Redes principales

El equipo de administración de seguridad y riesgos de información de Contoso ETS revisa un plan propuesto de Dave para mover la aplicación para Azure. Desea asegurarse de que la aplicación de la tarjeta de fidelidad es aislada y protegida en una red DMZ correctamente.  Para cumplir con este requisito, Dave y Ana crear una red virtual externa y un grupo de seguridad de la red a aislar la aplicación de la tarjeta de fidelidad desde la red corporativa de Contoso.  

En la **suscripción de desarrollo**, que crear:

| Tipo de recurso | Nombre | Descripción |
| ------------- | ---- | ----------- |
| Red virtual | vnInternal | Sirve el entorno de desarrollo de tarjeta de fidelidad de Contoso y está conectado a través de ExpressRoute a la red corporativa de Contoso. |

La **suscripción de producción**, cree:

| Tipo de recurso | Nombre | Descripción |
| ------------- | ---- | ----------- |
| Red virtual | vnExternal | Hospeda la aplicación de la tarjeta de fidelidad y no está conectado directamente a ExpressRoute de Contoso. Código se inserta a través de su sistema de código fuente directamente a los servicios de PaaS. |
| Grupo de seguridad de red | nsgBitBucket | Garantiza que la superficie de ataque de esta carga de trabajo se minimiza al permitir sólo comunicación entrante en TCP 443.  Contoso también está investigando usando un Firewall de aplicación Web de protección adicional. |  

### <a name="resource-locks"></a>Bloqueos de recursos 

David y Ana otorgar y decidan agregar bloqueos de recursos en algunos de los recursos clave en el entorno para evitar el borrado accidental durante una inserción de código erróneas. 

Crear el bloqueo siguiente:

| Tipo de bloqueo | Recursos | Descripción |
| --------- | -------- | ----------- |
| **CanNotDelete** | vnExternal | Para evitar que los usuarios eliminar la red virtual o subredes. El bloqueo no impide la adición de nuevas subredes. |

### <a name="azure-automation"></a>Automatización de Azure 

Ana y su equipo de desarrollo tienen una amplia runbooks para administrar el entorno de esta aplicación. La runbooks permita la adición o eliminación de nodos de la aplicación y otras tareas de DevOps. 

Para usar estos runbooks, permiten [la automatización](./automation/automation-intro.md).

### <a name="azure-security-center"></a>Centro de seguridad de Azure 

Administración del servicio de TI de Contoso debe identificar rápidamente y controlar amenazas. También desean comprender qué problemas exista.  

Para cumplir estos requisitos, Dave permite centro de seguridad de Azure. Se asegura de que el centro de seguridad de Azure supervisa los recursos y proporciona acceso a los equipos DevOps y seguridad. 

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre cómo crear plantillas de administrador de recursos, consulte [prácticas recomendadas para crear plantillas de administrador de recursos de Azure](resource-manager-template-best-practices.md).

*[Arturo Kuhnhausen](https://github.com/karlkuhnhausen) contribuido a este tema.*
