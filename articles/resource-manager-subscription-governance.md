<properties
   pageTitle="Procedimientos recomendados para empresas mover a Azure | Microsoft Azure"
   description="Describe un scaffold que las empresas pueden utilizar para garantizar un entorno seguro y fácil de administrar."
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

# <a name="azure-enterprise-scaffold---prescriptive-subscription-governance"></a>Scaffold enterprise Azure - gobernanza de suscripción normativas

Las empresas están adoptando cada vez más la nube pública para su agilidad y flexibilidad. Están utilizando la resistencia de la nube para generar ingresos u optimizar recursos para la empresa. Microsoft Azure proporciona un gran número de servicios que las empresas pueden ensamblar como bloques de creación a una amplia gama de aplicaciones y cargas de trabajo de direcciones. 

Pero a veces es difícil saber dónde comenzar. Después de decidir usar Azure, suelen surgen algunas preguntas:

- "¿Cómo cumple los requisitos legales soberanía de los datos de determinados países?"
- "¿Cómo me aseguro de que alguien no cambiado sin darse cuenta un sistema crítico?"
- "¿Cómo sé qué auxiliares todos los recursos para que puedo cuenta para él y factura nuevo con precisión?"

La perspectiva de una suscripción vacía con ningún guías guardia es bastante complicada. Este espacio en blanco, puede reducir la ir a Azure.

En este artículo se proporciona un punto de partida para profesionales técnicos cubrir la necesidad de gobernanza y saldo con la necesidad de agilidad. Presenta el concepto de un scaffold enterprise que guía a las organizaciones implementar y administrar sus suscripciones Azure. 

## <a name="need-for-governance"></a>Necesidad de gobierno

Al mover a Azure, debe tener en el tema de gobernanza temprano para garantizar el uso correcto de la nube dentro de la empresa. Lamentablemente, la hora y la burocracia de creación de un sistema de gobernanza completa significa algunos grupos de negocio ir directamente a proveedores sin necesidad de TI de la empresa. Este enfoque puede dejar la empresa abierta a vulnerabilidad si los recursos no se administran correctamente. Las características de la nube pública agilidad, flexibilidad y precios de consumo - son importantes para grupos de empresas que necesitan rápidamente satisfacer las necesidades de los clientes (internos y externos). Pero, TI debe asegurarse de que los datos y sistemas de un modo eficaz están protegidos.

En la vida real, scaffolding se usa para crear la base de la estructura. La scaffold guías el esquema general y proporciona puntos de anclaje sistemas más permanente para instalarse. Un scaffold enterprise es el mismo: un conjunto de controles flexibles y Azure capacidades que proporciona la estructura para el entorno y los delimitadores para servicios basados en la nube pública. Proporciona los generadores (TI y los grupos de negocio) una base para crear y adjuntar nuevos servicios.

La scaffold se basa en prácticas que hemos reunido desde muchos compromisos con los clientes de varios tamaños. Rango de los clientes de pequeñas empresas a desarrollar soluciones en la nube para empresas Fortune 500 y proveedores de software independientes que migrar y desarrollar soluciones en la nube. Scaffold de empresa es "específico" flexible para admitir cargas de trabajo de TI tradicionales y cargas de trabajo ágiles; como por ejemplo, los desarrolladores crear aplicaciones de software como-servicio (SaaS) se basan en capacidades de Azure.

Scaffold de empresa está pensado para ser la base de cada suscripción nueva dentro de Azure. Permite a los administradores para asegurarse de que las cargas de trabajo cumplen los requisitos de gobierno mínimo de una organización sin evitar que los grupos de la empresa y los desarrolladores rápidamente sus propios objetivos de la reunión.

> [AZURE.IMPORTANT] Gobierno es fundamental para el éxito de Azure. En este artículo está destinado a la implementación técnica de un scaffold enterprise, pero solo toque en el proceso y las relaciones entre los componentes más amplio. Gobernanza de directiva fluye de arriba hacia abajo y depende de lo que el negocio desea lograr. Naturalmente, la creación de un modelo de gobierno para Azure incluye representantes de TI, pero lo más importante debería tener fuerte representación de estudio de grupo de empresa y administración de riesgos y seguridad. Al final, un scaffold enterprise consiste en mitigar el riesgo de la empresa para facilitar los objetivos y los objetivos de la organización.

La imagen siguiente describe los componentes de la scaffold. La base se basa en un plan sólido para departamentos, cuentas y suscripciones. Los pilares constan de directivas del Administrador de recursos y los estándares de nomenclatura fuerte. El resto de scaffold proviene de core capacidades de Azure y las características que permiten un entorno seguro y fácil de administrar.

![componentes de scaffold](./media/resource-manager-subscription-governance/components.png)

> [AZURE.NOTE] Azure ha crecido rápidamente desde su presentación en 2008. Este crecimiento requiere Microsoft los equipos de ingeniería a pensar su enfoque para administrar e implementar servicios. El modelo de administrador de recursos de Azure se introdujo en 2014 y reemplaza el modelo de implementación clásico. Administrador de recursos permite a las organizaciones implementar más fácilmente, organizar y controlar los recursos de Azure. Administrador de recursos incluye puesta en paralelo al crear los recursos para una implementación más rápida de soluciones interdependientes complejas. También incluye el control de acceso granulares y la capacidad de recursos de la etiqueta con metadatos. Microsoft recomienda crear todos los recursos a través del modelo de administrador de recursos. La scaffold enterprise explícitamente está diseñado para el modelo de administrador de recursos.

## <a name="define-your-hierarchy"></a>Definir la jerarquía

La base de la scaffold es la inscripción de empresa de Azure (y el Portal de empresa). La inscripción de enterprise define la forma y el uso de servicios de Azure dentro de una compañía y es la estructura de gobierno principal. En el contrato enterprise, los clientes pueden subdividen aún más el entorno de departamentos, cuentas y, por último, en suscripciones. Una suscripción de Azure es la unidad básica donde se encuentran todos los recursos. También se define varios límites dentro de Azure, como el número de núcleos, recursos, etcetera.

![jerarquía](./media/resource-manager-subscription-governance/agreement.png)

Cada empresa es diferente y la jerarquía en la imagen anterior permite flexibilidad significativa en cómo está organizado Azure dentro de la compañía. Antes de implementar las directrices contenidas en este documento, debe modelar la jerarquía y comprender el impacto de facturación, el acceso a los recursos y la complejidad.

Los tres modelos comunes de inscripción en Azure son:

- El modelo **funcional**

    ![funcional](./media/resource-manager-subscription-governance/functional.png)

- El modelo de **unidad de negocio** 

    ![empresa](./media/resource-manager-subscription-governance/business.png)

- El modelo **geográfica**

    ![geográfica](./media/resource-manager-subscription-governance/geographic.png)

Aplicar la scaffold en el nivel de suscripción para ampliar el gobierno requisitos de la empresa en la suscripción.

## <a name="naming-standards"></a>Estándares de nomenclatura

Estándares de nomenclatura es la primera pilares de la scaffold. Estándares de nomenclatura bien diseñados le permiten identificar recursos en el portal, en una factura y dentro de secuencias de comandos. Más probable es que ya tiene estándares de nomenclatura para infraestructura local. Al agregar Azure a su entorno, debe extender los estándares de nomenclatura para los recursos de Azure. Estándar de nomenclatura facilitar la administración más eficaz del entorno en todos los niveles.

> [AZURE.TIP]
>
> - Revise y adoptar dónde posible la [Guía de patrones y procedimientos recomendados](./guidance/guidance-naming-conventions.md). Esta guía le ayuda a decidir sobre un estándar de nomenclatura significativo.
> - Use camelCasing de nombres de los recursos (como myResourceGroup y vnetNetworkName). Nota: Hay determinados recursos, como las cuentas de almacenamiento, donde la única opción es usar minúsculas (y no otros caracteres especiales).
> - Considere la posibilidad de usar directivas del Administrador de recursos de Azure (descritas en la sección siguiente) para aplicar los estándares de nomenclatura.
 
## <a name="policies-and-auditing"></a>Directivas y auditoría

La segunda pilares de la scaffold implica la creación de [directivas de administrador de recursos de Azure](resource-manager-policy.md) y [el registro de actividad de auditoría](resource-group-audit.md). Las directivas del Administrador de recursos proporcionan la capacidad de administración de riesgos en Azure. Puede definir las directivas que garantizan la soberanía de datos mediante la restricción, aplicación o auditoría determinadas acciones. 

- Directiva es un sistema de **Permitir** de forma predeterminada. Controlar acciones definiendo y asignar directivas a recursos denegación o auditar acciones en recursos.
- Las directivas se describen las definiciones de directiva en un lenguaje de definición de directiva (if-then condiciones).
- Crear directivas con JSON (notación de objetos Javascript) con formato de archivos. Después de definir una directiva, se asigna a un ámbito particular: suscripción, grupo de recursos. o recursos.

Las directivas tienen varias acciones que permiten un enfoque poseen escenarios. Las acciones son:

-   **Denegar**: bloquea la solicitud de recursos
-   **Auditoría**: permite la solicitud, pero se agrega una línea en el registro de actividad (que puede usarse para proporcionar alertas o desencadenar runbooks)
-   **Anexar**: agrega información especificada para el recurso. Por ejemplo, si no hay una etiqueta "CostCenter" en un recurso, agregue esa etiqueta con un valor predeterminado.

### <a name="common-uses-of-resource-manager-policies"></a>Usos comunes de directivas de administrador de recursos

Azure directivas del Administrador de recursos son una herramienta eficaz en el Kit de herramientas de Azure. Permiten evitar gastos inesperados, para identificar un centro de costos para recursos a través de etiquetado y para asegurarse de que se cumplen los requisitos de cumplimiento. Cuando las directivas se combinan con las características de auditoría integradas, puede forma soluciones flexibles y complejas. Las directivas permiten a las compañías proporcionar controles de cargas de trabajo de "TI tradicional" y "Agile" cargas de trabajo; como por ejemplo, desarrollo de aplicaciones de cliente. Los modelos más comunes que vemos para directivas son:

- **Datos de conformidad de Geo soberanía** - Azure proporciona regiones en todo el mundo. A menudo, las empresas desean controlar dónde se crean los recursos (ya sea para garantizar la soberanía de datos o simplemente para asegurarse de que los recursos se crean cerca de los consumidores de fin de los recursos).
- **Administración de costos** : suscripción un Azure puede contener recursos de muchos tipos y escala. Las empresas con qué frecuencia desean asegurarse de que las suscripciones estándares evitan el uso de recursos innecesariamente grandes, lo que pueden costar cientos de dólares, un mes o más.
- **Predeterminado gobernanza de etiquetas necesarias** - que requieren etiquetas es una de las características más comunes y altamente deseadas. Uso de directivas de administrador de recursos de Azure empresas son capaces para asegurarse de que un recurso esté etiquetado adecuadamente. Las etiquetas más comunes son: tipo de departamento, el propietario del recurso y el entorno (por ejemplo - producción, prueba, desarrollo)

**Ejemplos**

"Tradicional TI" suscripción para aplicaciones de línea de negocio

-   Exigir etiquetas de departamento y propietario de todos los recursos
-   Restringir la creación de recursos a la región de Norteamérica
-   Restringir la capacidad para crear máquinas virtuales de serie G y HDInsight clústeres

Entorno "Ágil" unidades de negocio crear aplicaciones de nube

- Para satisfacer los requisitos de la soberanía de datos, permitir la creación de recursos sólo en una región específica.
- Aplicar la etiqueta del entorno en todos los recursos. Si un recurso se crea sin una etiqueta, anexe la **entorno: desconocido** etiqueta al recurso.
- Auditoría cuando los recursos se crean fuera de Norteamérica, pero no impide que.
- Auditoría cuando se crean los recursos de costo de alto.

> [AZURE.TIP]
>
> El uso de más comunes de directivas de administrador de recursos entre organizaciones es control *donde* los recursos pueden crearse y *qué* tipos de recursos se pueden crear. Además de proporcionar controles en *dónde* y *qué*, muchas empresas utilizan directivas para asegurarse de recursos tienen los metadatos correspondientes para cobrar para el consumo. Se recomienda aplicar directivas en el nivel de suscripción para:
>
> - Datos de conformidad de geo soberanía
> - Administración de costos
> - Etiquetas necesarias (según la necesidad empresarial, por ejemplo, de facturación, propietario de la aplicación)
>
> Puede aplicar directivas adicionales de los niveles inferiores del ámbito.
 
### <a name="audit---what-happened"></a>Auditoría - ¿qué ha ocurrido?

Para ver cómo funciona el entorno, debe auditar la actividad de usuario. La mayoría de tipos de recursos en Azure crean registros de diagnóstico que se pueden analizar a través de una herramienta de registro o en serie de administración de las operaciones de Azure. Puede recopilar registros de actividad a través de varias suscripciones para proporcionar un departamento o vista de la empresa. Registros de auditoría son una herramienta de diagnóstico importante y un mecanismo fundamental para desencadenar eventos en el entorno de Azure.

Registros de actividad desde el Administrador de recursos implementaciones permiten determinar las **operaciones** que tomó coloque y quién las realizó. Registros de actividad pueden recopilar y agregados mediante herramientas como el análisis de registro.

## <a name="resource-tags"></a>Etiquetas de recursos

Como los usuarios de su organización agregan recursos a la suscripción, es más importante asociar recursos con el departamento correspondiente, el cliente y el entorno. Puede adjuntar metadatos a recursos a través de [etiquetas](resource-group-using-tags.md). Usar etiquetas para proporcionar información sobre el recurso o el propietario. Las etiquetas le permiten agregar no solo y agrupar recursos de distintas maneras, pero usar esos datos para los fines de cargo al usuario. Puede etiquetar recursos con hasta 15 pares de clave: valor. 

Etiquetas de recursos son flexibles y deben estar vinculadas a la mayoría de los recursos. Ejemplos de etiquetas de recursos comunes son:

- Facturación
- Departamento (o unidad de negocio)
- Entorno (desarrollo de producción, fase)
- Nivel (nivel de Web, nivel de aplicación)
- Propietario de la aplicación
- Nombre de proyecto

![etiquetas](./media/resource-manager-subscription-governance/resource-group-tagging.png)

Para obtener más ejemplos de etiquetas, vea [recomendado convenciones de nomenclatura para recursos de Azure](./guidance/guidance-naming-conventions.md).

> [AZURE.TIP]
>
> Crear una estrategia de etiquetado que identifica en todas las suscripciones qué metadatos es necesaria para la empresa, finanzas, seguridad, administración de riesgos y administración general del entorno. Considere la posibilidad de realizar una directiva que exige etiquetado para:
>
> - Grupos de recursos
> - Almacenamiento de información
> - Máquinas virtuales de Windows
> - Servidores de aplicaciones web y entornos de servicio

## <a name="resource-group"></a>Grupo de recursos 

Administrador de recursos le permite colocar recursos en grupos significativos para la afinidad administración, de facturación o natural. Como se mencionó anteriormente, Azure tiene dos modelos de implementación. En el modelo clásico anterior, la unidad básica de administración era la suscripción. Era difícil dividir recursos dentro de una suscripción que dio lugar a la creación de un gran número de suscripciones. Con el modelo de administrador de recursos, hemos visto la introducción de grupos de recursos. Grupos de recursos son contenedores de recursos que tienen un ciclo de vida comunes o compartan un atributo como "todos los servidores SQL" o "Aplicación".

Grupos de recursos no pueden estar dentro de otra y recursos sólo pueden pertenecer a un grupo de recursos. Puede aplicar determinadas acciones en todos los recursos en un grupo de recursos. Por ejemplo, al eliminar un grupo de recursos quita todos los recursos del grupo de recursos. Normalmente, coloque un sistema relacionado o toda la aplicación en el mismo grupo de recursos. Por ejemplo, una aplicación de tres capas denominada aplicación Web de Contoso podría contener el servidor web, servidor de aplicaciones y SQL server en el mismo grupo de recursos.

> [AZURE.TIP]
>
> Organizar los grupos de recursos puede variar de cargas de trabajo de "TI tradicional" a las cargas de trabajo de "TI ágil"
>
> - "Tradicional TI" cargas de trabajo con más frecuencia se agrupan por elementos dentro del mismo ciclo de vida, como una aplicación. Agrupar por aplicación permite la administración de aplicaciones individuales.
> - "Ágil TI" cargas de trabajo tienden a centrarse en aplicaciones externas nube orientados al cliente. Los grupos de recursos deben reflejar las capas de la implementación (por ejemplo, el nivel de Web, el nivel de aplicación) y la administración.

## <a name="role-based-access-control"></a>Control de acceso basado en roles

Probablemente se esté preguntando "¿quién debe tener acceso a los recursos?" y "¿Cómo puedo controlar este acceso?" Están fundamental permitir o denegar el acceso al portal de Azure y controlar el acceso a los recursos en el portal. 

Cuando se publicó inicialmente Azure, controles de acceso a una suscripción fueron básicos: administrador o compañeros. Obtener acceso a una suscripción en el clásico modelo implicado el acceso a todos los recursos en el portal. Esta falta de un control más preciso dirigidas a la proliferación de suscripciones para proporcionar un nivel de control de acceso razonable para la inscripción de Azure.

Esta proliferación de suscripciones ya no es necesaria. Con control de acceso basado en roles, puede asignar a usuarios a roles estándares (como tipos comunes de "lector" y "escritor" de funciones). También puede definir funciones personalizadas.

> [AZURE.TIP]
>  
> - Conecte el almacén de identidades corporativa (normalmente de Active Directory) a Azure Active Directory mediante la herramienta de AD Connect.
> - Controlar el administrador o Co-Administrador de una suscripción con una identidad administrada. **No** asignar el administrador, Co-administrador a un nuevo propietario de la suscripción. En su lugar, utilice las funciones RBAC para proporcionar derechos de **propietario** a un grupo o individuales.
> - Agregar usuarios de Azure a un grupo (por ejemplo, los propietarios de X de aplicaciones) en Active Directory. Utilice el grupo sincronizado para proporcionar a los miembros del grupo los derechos adecuados para administrar el grupo de recursos que contiene la aplicación.
> - Siga el principio de conceder los **privilegios mínimos** necesarios para realizar el trabajo previsto. Por ejemplo:
>     - Grupo de distribución: Un grupo que solo puede implementar recursos.
>     - Administración de la máquina virtual: Un grupo que se puede reiniciar máquinas virtuales (para operaciones)

## <a name="azure-resource-locks"></a>Bloqueos de recursos de Azure

Cuando la organización agrega servicios principales a la suscripción, es más importante para asegurarse de que los servicios que están disponibles para evitar interrupciones del negocio. [Bloqueos de recursos](resource-group-lock-resources.md) le permiten restringir las operaciones de recursos de alto valor donde modificarlos o eliminarlos tendría un impacto significativo en las aplicaciones o la infraestructura de la nube. Puede aplicar bloqueos a una suscripción, el grupo de recursos o el recurso. Normalmente, aplicar bloqueos a recursos fundamentales como cuentas de almacenamiento, redes virtuales y puertas de enlace. 

Bloqueos de recursos actualmente son compatibles con dos valores: CanNotDelete como de sólo lectura. Significa CanNotDelete que los usuarios (con los derechos adecuados) todavía pueden lee o modificar un recurso, pero no puede eliminarlo. Sólo lectura significa que los usuarios autorizados no pueden eliminar o modificar un recurso.

Para crear o eliminar bloqueos de administración, debe tener acceso a `Microsoft.Authorization/*` o `Microsoft.Authorization/locks/*` acciones.
De las funciones integradas, solo el propietario y el Administrador de acceso de usuario se le concede esas acciones.

> [AZURE.TIP] Opciones de la red principal deben estar protegidas con bloqueos. El borrado accidental de una puerta de enlace, sería desastroso una suscripción de Azure VPN de sitio a otro. Azure no le permite eliminar una red virtual que está en uso, pero aplicar restricciones más es una medida útil. Las directivas también son fundamentales para el mantenimiento de controles adecuados. Le recomendamos que aplique un bloqueo **CanNotDelete** a las directivas que se usan.
>
> - Una red virtual: CanNotDelete
> - Grupo de seguridad de red: CanNotDelete
> - Directivas: CanNotDelete

## <a name="core-networking-resources"></a>Recursos de la red principal

Acceso a los recursos puede ser internos (dentro de la red corporativa) o externo (a través de internet). Es fácil para los usuarios de su organización accidentalmente colocar recursos en el lugar incorrecto y abrirlos potencialmente malintencionado de Access. Como ocurre con dispositivos locales, las empresas deben agregar controles adecuados para asegurarse de que los usuarios de Azure decisiones acertadas. Para el control de la suscripción, identificaremos recursos principales que proporcionan control básico de access. Los recursos principales constan de:

- **Redes virtuales** son objetos de contenedor para subredes. Aunque no es estrictamente necesario, se utiliza a menudo al conectar aplicaciones a recursos internos de la empresa.
- **Grupos de seguridad de red** son similares a un firewall y proporcionar reglas para cómo un recurso puede "hablar" en la red. Proporcionan control detallado de cómo si una subred (o máquina virtual) puede conectarse a Internet o a otras subredes en la misma red virtual.

![redes principales](./media/resource-manager-subscription-governance/core-network.png)

> [AZURE.TIP]
>  
> - Crear redes virtuales dedicadas a cargas de trabajo externo y las cargas de trabajo interna. Este enfoque reduce la posibilidad de forma involuntaria colocar máquinas virtuales que están destinados a cargas de trabajo internos en un espacio hacia externo.
> - Grupos de seguridad de red son fundamentales para esta configuración. Como mínimo, bloquear el acceso a internet de las redes virtuales internas y bloquear el acceso a la red corporativa de redes virtuales externas.

### <a name="automation"></a>Automatización

Administración de recursos de forma individual es lento y potencialmente error susceptible para determinadas operaciones. Azure proporciona varias funciones de automatización incluidos automatización de Azure, lógica de aplicaciones y funciones de Azure. [Automatización de Azure](./automation/automation-intro.md) permite a los administradores crear y definir runbooks para administrar tareas comunes de administración de recursos. Crear runbooks mediante un editor de código de PowerShell o un editor gráfico. Puede crear flujos de trabajo con varias etapas complejos. Automatización de Azure con qué frecuencia se usa para administrar tareas comunes como cerrando recursos sin usar o creación de recursos en respuesta a un desencadenador específico sin necesidad de intervención humana.

> [AZURE.TIP]
>
> - Crear una cuenta de Azure automatización y revise los disponibles en la [Galería de Runbook](./automation/automation-runbook-gallery.md)de runbooks disponibles (línea de comandos y gráfica).
> - Importar y personalizar runbooks clave para su propio uso.
>
> Un escenario común es la capacidad de máquinas virtuales de inicio y cierre de una programación. Hay runbooks de ejemplo que están disponibles en la galería que controlan este escenario y enseñar expandirlo.


## <a name="azure-security-center"></a>Centro de seguridad de Azure

Quizás uno de los bloqueadores mayores adopción de nube ha sido las preocupaciones sobre seguridad. Los administradores de TI riesgos y departamentos de seguridad tienen que asegurarse de que los recursos en Azure son seguros. 

El [Centro de seguridad de Azure](./security-center/security-center-intro.md) proporciona una vista central del estado de seguridad de los recursos en las suscripciones y proporciona recomendaciones que ayudan a evitar que los recursos comprometidos. Pueden habilitar directivas más granulares (por ejemplo, aplicar directivas a grupos de recursos específicos que permiten a la empresa ajustar su posición en el riesgo que comunique). Por último, centro de seguridad de Azure es una plataforma abierta que permite a Microsoft partners y proveedores de software independientes para crear software que se conecta al centro de seguridad de Azure para mejorar sus capacidades. 

> [AZURE.TIP]
>
> Centro de seguridad de Azure está habilitado de forma predeterminada en cada suscripción. Sin embargo, debe habilitar la recopilación de datos de máquinas virtuales de Windows para permitir que el centro de seguridad de Azure instalar al agente y comenzar a recopilar datos.
>
> ![recopilación de datos](./media/resource-manager-subscription-governance/data-collection.png)

## <a name="next-steps"></a>Pasos siguientes

- Ahora que ha aprendido gobernanza de suscripción, es el momento para ver estas recomendaciones en práctica. Vea [ejemplos de implementación de gobernanza de suscripción de Azure](resource-manager-subscription-examples.md).

*[Arturo Kuhnhausen](https://github.com/karlkuhnhausen) contribuido a este tema.*
