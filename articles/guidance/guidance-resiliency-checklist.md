<properties
   pageTitle="Lista de comprobación de la resistencia | Microsoft Azure"
   description="Lista de comprobación que se proporciona pautas para preocupaciones de la resistencia durante el diseño."
   services=""
   documentationCenter="na"
   authors="petertaylor9999"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="petertay"/>

# <a name="azure-resiliency-guidance-resiliency-checklist"></a>Orientación de la resistencia Azure: lista de comprobación de la resistencia

Diseño de la aplicación de la resistencia requiere planear y mitigar una gran variedad de modos de error podría ocurrir. Revise los elementos de esta lista de comprobación en el diseño de la aplicación para que sea más flexible.

## <a name="requirements"></a>Requisitos

- **Definir los requisitos de disponibilidad del cliente.** Su cliente tendrá requisitos de disponibilidad de los componentes de la aplicación y esto afectará el diseño de la aplicación. Obtener el contrato de cliente para los objetivos de la disponibilidad de cada parte de la aplicación, en caso contrario, el diseño no cumpla las expectativas del cliente. Para obtener más información, vea la sección [definir los requisitos de la resistencia](guidance-resiliency-overview.md#defining-your-resiliency-requirements) del documento de [Diseño de aplicaciones flexibles para Azure](guidance-resiliency-overview.md) .

## <a name="failure-mode-analysis"></a>Análisis de modo de errores

- **Realizar un análisis de modo de error (FMA) para la aplicación.** FMA es un proceso para la creación de la resistencia a una aplicación del principio de la fase de diseño. Los objetivos de un FMA incluyen:  

    - Identificar qué tipos de errores que surjan una aplicación. 
    - Capturar los posibles efectos y repercusiones de cada tipo de error en la aplicación.
    - Identificar estrategias de recuperación. 

    Para obtener más información, vea [diseñar aplicaciones flexibles para Azure: análisis de modo de errores][fma].  

## <a name="application"></a>Aplicación

- **Implementar varias instancias de servicios.** Inevitable se producirá un error de los servicios y, a continuación, si su aplicación depende de una sola instancia de un servicio se inevitable producirá también. Para crear varias instancias de [Azure aplicación de servicio](../app-service/app-service-value-prop-what-is.md), seleccione un [Plan de servicio de aplicación](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) que ofrece varias instancias. Para los servicios de nube de Azure, configure cada uno de los roles para utilizar [varias instancias](../cloud-services/cloud-services-choose-me.md#scaling-and-management). [Máquinas virtuales de Azure (VM)](../virtual-machines/virtual-machines-windows-about.md), asegúrese de que la arquitectura de la máquina virtual incluye más de una VM y que cada VM está incluido en un [conjunto de disponibilidad][availability-sets].   

- **Utilizar un equilibrador de carga para distribuir solicitudes.** Equilibrio de carga distribuye solicitudes de la aplicación a las instancias de servicio correcto quitando instancias mal Estados de giro. Si su servicio utiliza la aplicación de servicio de Azure o los servicios de nube de Azure, ya está equilibrada de carga para usted. Sin embargo, si su aplicación utiliza máquinas virtuales de Azure, debe aprovisionar un equilibrador de carga. Vea la información general del [Equilibrador de carga de Azure](../load-balancer/load-balancer-overview.md) para obtener más detalles. 

- **Configurar puertas de enlace de aplicación de Azure para utilizar varias instancias.** Según los requisitos de la aplicación, una [Puerta de enlace de aplicaciones de Azure](../application-gateway/application-gateway-introduction.md) puede ser mejor adecuado para distribuir las solicitudes de servicios de la aplicación. Sin embargo, solo instancias del servicio de puerta de enlace de aplicación no se garantiza por un SLA lo que es posible que la aplicación puede fallar si se produce un error en la instancia de puerta de enlace de aplicación. Aprovisionar más de una instancia de puerta de enlace de aplicación más grande o mediana para garantizar la disponibilidad del servicio en los términos del [SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_0/).

- **Uso de conjuntos de disponibilidad para cada nivel de aplicación**. Colocar las instancias de un [conjunto de disponibilidad] [ availability-sets] garantiza conectividad con al menos una instancia de máquina virtual dentro de los términos del [SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_2/). Si no se encuentran sus máquinas virtuales en un conjunto de disponibilidad que no tiene garantiza protegerlas y es posible que podría fallar todo o que se pueden actualizar de forma simultánea. 

- **Considere la posibilidad de implementar la aplicación a través de varias áreas.** Si la aplicación se implementa en una sola región, raro toda la región no está disponible, la aplicación también estará disponible. Esto puede ser aceptable en los términos del SLA de la aplicación. Si es así, considere la posibilidad de implementar la aplicación y sus servicios a través de varias áreas. Una implementación de múltiples región puede usar un modelo de activo activo (distribuir solicitudes entre varias instancias activas) o un activo pasivo (mantener una instancia de "caliente" en reserva, en caso de que se produce un error en la instancia primaria). Le recomendamos que implementa varias instancias de servicios de la aplicación en pares regionales. Para obtener más información, consulte [empresarial continuidad y recuperación ante desastres (BCDR): regiones de Azure emparejadas](../best-practices-availability-paired-regions.md).

- **Implementar modelos de la resistencia para operaciones remotas donde corresponda.** Si su aplicación depende de la comunicación entre los servicios remotos, la ruta de comunicación inevitable se producirá un error. Si hay varios errores, las restantes instancias buen estadas de los servicios de la aplicación se podrían abrumadas con solicitudes. Existen varios patrones útil para trabajar con errores comunes, incluidos el patrón de tiempo de espera del [Reintentar trama][retry-pattern], el [disyuntor] [ circuit-breaker] trama y otros usuarios. Para obtener más información, vea [Diseño de aplicaciones flexibles para Azure](guidance-resiliency-overview.md#implementing-resiliency-strategies). 

- **Utilizar ajuste automático para responder a aumentos de carga.** Si su aplicación no está configurada para escalar automáticamente a medida que aumenta la carga, es posible que los servicios de la aplicación se producirá un error si sature de solicitudes de usuario. Para obtener más detalles, consulte lo siguiente:

    - General: [lista de comprobación de escalabilidad](../best-practices-scalability-checklist.md) 
    - Azure de aplicación de servicio: [escalar recuento de instancias de forma manual o automática][app-service-autoscale]
    - Servicios de nube: [cómo Autoescala un servicio de nube][cloud-service-autoscale]
    - Máquinas virtuales de Windows: [establece la escala de máquina virtual y ajuste de escala automático][vmss-autoscale]


- **Implementar operaciones asincrónicas siempre que sea posible.** Operaciones sincrónicas pueden apropiarse en exclusiva de recursos y bloquear otras operaciones mientras espera completar el proceso. Diseñar cada parte de la aplicación para permitir las operaciones asincrónica siempre que sea posible. Para obtener más información sobre cómo implementar la programación asincrónica en C#, vea [programación asincrónica con asincrónico y espera][asynchronous-c-sharp].

- **Use el administrador del tráfico de Azure para enrutar el tráfico de la aplicación a diferentes regiones.**  [Administrador de tráfico Azure] [ traffic-manager] realiza en el nivel DNS de equilibrio de carga y enrutar el tráfico a diferentes regiones basadas en el [enrutamiento del tráfico] [ traffic-manager-routing] método que se especifique y la salud de los extremos de la aplicación. 

- **Configurar y probar sondeos de salud de su equilibradores de carga y los administradores de tráfico.** Asegúrese de que la lógica de salud comprueba las partes críticas del sistema y responda adecuadamente a sondeos de estado.

    - Sondeos el estado de [Administrador de tráfico de Azure] [ traffic-manager] [Equilibrador de carga de Azure] y[ load-balancer] cumple una función específica. Administrador de tráfico, el sondeo de salud determina si se conmutar a otra región. Equilibradores de carga, determina si se va a quitar una máquina virtual de giro.      

    - Para un sondeo de administrador de tráfico, el extremo de salud debe comprobar las dependencias críticas que se implementan dentro de la misma región y cuyo error debe desencadenar una migración tras error para otra región.  

    - Equilibradores de carga, el extremo de salud debe informar del estado de la máquina virtual. No incluya otros niveles o servicios externos. En caso contrario, un error que se produce fuera de la máquina virtual hará que el equilibrador de carga quitar la máquina virtual de giro.

    - Para obtener instrucciones sobre cómo implementar la supervisión de estado en la aplicación, vea [Trama de supervisión de estado del extremo](https://msdn.microsoft.com/library/dn589789.aspx).

- **Supervisar los servicios de terceros.** Si su aplicación tiene dependencias en los servicios de terceros, identificar dónde y cómo pueden fallar estos servicios de terceros y el efecto que estos errores tendrá en la aplicación. Un servicio de terceros no puede incluir supervisión y diagnóstico, por lo que es importante registrar sus llamadas de ellos y les relacionar con el estado de la aplicación y con un identificador único de registro de diagnóstico. Para obtener más información acerca de prácticas recomendadas para la supervisión y diagnósticos, consulte la [Guía de supervisión y diagnóstico] [ monitoring-and-diagnostics-guidance] documento.

- **Asegúrese de que cualquier servicio de terceros que consume proporciona un SLA.** Si su aplicación depende de un servicio de terceros, pero el tercero no ofrece ninguna garantía de disponibilidad en el formulario de un SLA, también no garantiza la disponibilidad de la aplicación. El SLA sólo es tan bueno como el componente menos disponible de la aplicación.


## <a name="data-management"></a>Administración de datos

- **Comprender los métodos de replicación para orígenes de datos de la aplicación.** Los datos de la aplicación se almacenan en orígenes de datos distintos y tienen requisitos de disponibilidad diferentes. Evaluar los métodos de replicación para cada tipo de almacenamiento de datos en Azure, incluida la [Replicación de almacenamiento de Azure](../storage/storage-redundancy.md) y [Base de datos activa Geo replicación de SQL](../sql-database/sql-database-geo-replication-overview.md) para asegurarse de que se cumplen los requisitos de datos de la aplicación.

- **Asegúrese de que ninguna cuenta de usuario tiene acceso a los datos de producción y copia de seguridad.** Las copias de seguridad de datos están en peligro si una sola cuenta de usuario tiene permiso de escritura a orígenes de copia de seguridad y de producción. Un usuario malintencionado deliberadamente podría eliminar todos los datos, mientras que un usuario habitual accidentalmente podría eliminar. Diseñar la aplicación para limitar los permisos de cada cuenta de usuario para que solo los usuarios que requieren acceso de escritura tienen acceso de escritura y es solo para producción o copia de seguridad, pero no en ambos.

- **Documento de origen de datos conmutar y conmutar procesar y probarlo.** En el caso de que el origen de datos no forma catastrófica, tendrá un operador humano seguir un conjunto de instrucciones documentados conmutar a un nuevo origen de datos. Si los pasos documentados tienen errores, un operador no podrá correctamente seguirlos y conmutar el recurso. Probar con regularidad los pasos a seguir para comprobar que un operador seguirlos se podrán conmutar correctamente y conmutar el origen de datos.

- **Validar las copias de seguridad de datos.** Con regularidad, compruebe que los datos de copia de seguridad están lo que espera, ejecute una secuencia de comandos para validar consultas, esquema y la integridad de datos. No hay ningún punto de tener una copia de seguridad, si no es útil restaurar los orígenes de datos. Inicie sesión y notificar cualquier incoherencia para que el servicio de copia de seguridad se puede reparar.

- **Considere el uso de un tipo de cuenta de almacenamiento geo redundantes.** Los datos almacenados en una cuenta de almacenamiento de Azure siempre se duplica localmente. Sin embargo, hay varias estrategias de replicación que elegir cuando se aprovisiona una cuenta de almacenamiento. Seleccione [Acceso de lectura de Azure Geo almacenamiento redundante (RA GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage) para proteger los datos de aplicación en el caso poco cuando no esté disponible toda una región.

    > [AZURE.NOTE] Para las VM, no confiar en la replicación de RA GRS para restaurar los discos VM (archivos de disco duro virtual). En su lugar, use la [Copia de seguridad de Azure][azure-backup].   

## <a name="operations"></a>Operaciones

- **Implementar supervisión y alertas de los procedimientos recomendados en la aplicación.** Sin adecuada supervisión, diagnósticos y alertas, no hay ninguna manera para detectar errores en la aplicación y alertas de un operador para corregirlos. Para obtener más información acerca de prácticas recomendadas, consulte la [Guía de supervisión y diagnóstico] [ monitoring-and-diagnostics-guidance] documento.

- **Medir estadísticas remoto de llamadas y hacer que la información disponible para el equipo de la aplicación.**  Si no realizar un seguimiento e informe estadísticas remoto de llamadas en tiempo real y proporcionar una forma sencilla de leer esta información, el equipo de operaciones no tiene una vista instantánea sobre el estado de la aplicación. Y si solo la hora de medida promedio remoto de llamadas, no tendrá suficiente información para revelar problemas en los servicios. Resumir métricas remoto de llamadas como latencia, el rendimiento y errores en los valores de percentiles 99 y 95. Realizar análisis estadísticos la métrica para descubrir errores que aparecen dentro de cada percentil.

- **Controlar el número de reintentos y excepciones transitorias sobre un intervalo de tiempo correspondiente.** Si no realizar un seguimiento y supervisar excepciones transitorias y reintentos a lo largo del tiempo, es posible que un error o un problema podría ocultar lógica de reintento de la aplicación. Es decir, si la supervisión y el registro solo muestra el éxito o error de una operación, se ocultarán el hecho de que la operación tenía que volverá a varias veces a causa de excepciones. Una línea de tendencia de aumento excepciones a lo largo del tiempo indica que el servicio está teniendo un problema y puede producir un error. Para obtener más información, vea [Reintentar instrucciones específicas de servicios][retry-service-guidance].

- **Implementar un sistema de advertencia temprana que avisa a un operador.** Identificar la clave de rendimiento indicadores de estado de la aplicación, como excepciones transitorias y remoto latencia de llamadas y establecer los valores de umbral apropiado para cada uno de ellos. Enviar una alerta a operaciones cuando se alcance el valor de umbral. Configurar estos umbrales en niveles de identifican problemas antes de que se conviertan en críticos y requieren una respuesta de recuperación.

- **El proceso de publicación para la aplicación del documento.** Sin la documentación del proceso de lanzamiento detallada, un operador puede implementar una actualización errónea o establecer correctamente la configuración de la aplicación. Claramente definir el proceso de versiones de documentos y asegúrese de que esté disponible para todo el equipo de operaciones. Procedimientos recomendados para su implementación y son resistente de la aplicación se detallan en la [implementación y son resistente] [ guidance-resilient-deployment] sección del documento de orientación de la resistencia.

- **Asegúrese de que más de una persona del equipo está capacitada para supervisar la aplicación y realice los pasos de recuperación manual.** Si solo tiene un operador en el equipo que puede supervisar la aplicación y empezar los pasos de recuperación, esa persona se convierte en un punto único de error. Entrenar a varias personas de la detección y recuperación y asegúrese de que siempre hay al menos una activo en cualquier momento.

- **Automatizar el proceso de implementación de la aplicación.** Si su personal de operaciones es necesario para implementar manualmente la aplicación, los errores humanos pueden provocar la implementación no se realice correctamente. Para obtener más información acerca de prácticas recomendadas para automatizar la implementación de la aplicación, vea la [implementación flexible] [ guidance-resilient-deployment] sección del documento de orientación de la resistencia.

- **Diseñar el proceso de lanzamiento para maximizar la disponibilidad de las aplicaciones.** Si el proceso de lanzamiento requiere servicios de desconectarse durante la implementación, la aplicación estará disponible hasta que llegan volver a conectarse. Utilice la técnica de implementación [verde o azul](http://martinfowler.com/bliki/BlueGreenDeployment.html) o [Canarias suelte](http://martinfowler.com/bliki/CanaryRelease.html) para implementar la aplicación en producción. Ambas técnicas implican implementar el código de lanzamiento junto con el código de producción para que los usuarios de código de versión se pueden redirigir a código de producción en caso de error. Para obtener más información, vea la [implementación flexible] [ guidance-resilient-deployment] sección del documento de orientación de la resistencia.

- **Registro de auditoría e implementaciones de la aplicación.** Si usa técnicas de implementación como verde o azul o canary habrá más de una versión de la aplicación que se ejecuta en producción en versiones de prueba. Si se produce un problema, es fundamental determinar qué versión de la aplicación está causando un problema. Implementar una estrategia de registro de información sólida para capturar toda la información específica de la versión como sea posible. 

- **Asegúrese de que la aplicación no se ejecuta frente a [límites de suscripción de Azure](../azure-subscription-service-limits.md).** Suscripciones de Azure tienen límites en determinados tipos de recursos, como el número de grupos de recursos, el número de núcleos y el número de cuentas de almacenamiento.  Si los requisitos de aplicación superan los límites de suscripción de Azure, crear otro Azure suscripción y aprovisionar suficientes recursos allí.

- **Asegúrese de que la aplicación no se ejecuta frente [limita por servicio](../azure-subscription-service-limits.md).** Servicios de Azure individuales tienen límites de consumo &mdash; por ejemplo, límites de almacenamiento, el rendimiento, el número de conexiones, solicitudes por segundo y otras métricas. La aplicación se producirá un error si intenta utilizar recursos más allá de estos límites. Esto se traducirán en tiempo de inactividad de servicio límite y posibles para los usuarios afectados. 

    Según el servicio específico y los requisitos de la aplicación, puede evitar estos límites a menudo por escalado (por ejemplo, elegir otro nivel de precio) u horizontal (agregando nuevas instancias).  

- **Diseñar los requisitos de almacenamiento de la aplicación que se incluyan en destinos de rendimiento y escalabilidad de almacenamiento de Azure.** Almacenamiento de Azure está diseñado para funcionar dentro de destinos predefinidos de rendimiento y escalabilidad, así que diseñar la aplicación para utilizar el almacenamiento de información dentro de esos objetivos. Si supera estos destinos su aplicación experimentará el límite de almacenamiento. Para corregir este error, proporcionando cuentas adicionales de almacenamiento. Si ejecuta frente del límite de la cuenta de almacenamiento, aprovisionar suscripciones adicionales de Azure y, a continuación, proporcionando cuentas adicionales de almacenamiento. Para obtener más información, vea [destinos de rendimiento y escalabilidad de almacenamiento de Azure](../storage/storage-scalability-targets.md).

- **Seleccione el tamaño correcto de la máquina virtual de la aplicación.** Mida la CPU, memoria, disco e i/OS de sus máquinas virtuales de producción real y compruebe que el tamaño de la memoria virtual que ha seleccionado es suficiente. Si no es así, la aplicación puede experimentar problemas de capacidad como las máquinas virtuales aproximan a sus límites. Tamaños VM se describen en detalle en el documento de [tamaños de máquinas virtuales de Azure](../virtual-machines/virtual-machines-windows-sizes.md) .

- **Determinar si la carga de trabajo de la aplicación es estable o fluctuación lo largo del tiempo.** Si la carga de trabajo varía con el tiempo, usar Azure VM escala establece a escala automáticamente el número de instancias de máquina virtual. En caso contrario, tendrá que aumentar o disminuir el número de máquinas virtuales manualmente. Para obtener más información, vea la [Información general de conjuntos de escala de máquina Virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

- **Seleccione el nivel de servicio adecuado para la base de datos de SQL Azure.** Si su aplicación utiliza la base de datos de SQL Azure, asegúrese de que ha seleccionado el nivel de servicio adecuado. Si selecciona un nivel que no sea capaz de controlar los requisitos de unidad (DTU) de transacción de base de datos de la aplicación, se reducirá el uso de datos. Para obtener más información sobre cómo seleccionar el plan de servicio correcto, vea la [Opciones de base de datos SQL y rendimiento: comprender qué está disponible en cada nivel de servicio](../sql-database/sql-database-service-tiers.md) documento. 

- **Tiene un plan de deshacer para su implementación.** Es posible que la implementación de la aplicación puede fallar y hacer que la aplicación esté disponible. Diseñar un proceso de deshacer para volver a la última versión buena conocida y minimizar el tiempo de inactividad. Consulte la [implementación flexible] [ guidance-resilient-deployment] sección del documento de orientación de la resistencia para obtener más información. 

- **Crear un proceso para interactuar con el soporte técnico de Azure.** Si no está establecido el proceso para ponerse en contacto con [soporte técnico de Azure](https://azure.microsoft.com/support/plans/) antes de que sea necesario ponerse en contacto con soporte técnico, el tiempo de inactividad se se prolonga como el proceso de soporte técnico se navega por primera vez. Incluir el proceso para ponerse en contacto con soporte técnico y crecientes problemas como parte de la resistencia de la aplicación desde el principio.

- **Asegúrese de que la aplicación no utilice más que el número máximo de cuentas de almacenamiento por suscripción.** Azure permite un máximo de 200 cuentas de almacenamiento por suscripción. Si la aplicación requiere más cuentas de almacenamiento que se encuentran disponibles en la suscripción, tendrá que crear una nueva suscripción y crear cuentas de almacenamiento adicional. Para obtener más información, consulte [suscripción Azure y límites de servicio, cuotas y las restricciones](../azure-subscription-service-limits.md#storage-limits).

- **Asegúrese de que su aplicación no supera los objetivos de escalabilidad de discos de la máquina virtual.** Una máquina virtual IaaS de Azure admite la asociación de un número de discos de datos dependiendo de varios factores, incluidos el tamaño de la máquina virtual y el tipo de cuenta de almacenamiento. Si la aplicación supera los objetivos de escalabilidad de discos de la máquina virtual, proporcionando cuentas de almacenamiento adicional y crear los discos de máquina virtual allí. Para obtener más información, consulte [objetivos de rendimiento y escalabilidad de almacenamiento de Azure] (.. / storage/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)

## <a name="test"></a>Prueba

- **Realizar conmutación y probar la aplicación.** Si todavía no lo ha probado completamente conmutación y, no puede ser determinado de que los servicios dependientes en la aplicación que vuelva hacia arriba de manera sincronizada durante la recuperación. Asegúrese de que depende de la aplicación de servicios de migración y errores en el orden correcto.

- **Realizar pruebas para la aplicación de inserción de errores.** La aplicación puede fallar por diferentes motivos, como la caducidad de certificado agotamiento de los recursos del sistema en una máquina virtual o errores de almacenamiento. Probar la aplicación en un entorno lo más cerca posible de producción, simular o desencadenante errores reales. Por ejemplo, eliminar certificados, forma artificial consumir recursos del sistema o eliminar un origen de almacenamiento. Compruebe la capacidad de la aplicación para recuperar de todos los tipos de errores, sólo y en combinación. Comprobación de errores no propagarse o en cascada a través del sistema.

- **Ejecutar pruebas de producción con los datos de usuario de síntesis y real.** Prueba y producción son casi nunca idénticos, por lo que es importante que utilice verde o azul o una implementación canary y probar la aplicación en producción. Esto le permite probar la aplicación en producción en carga real y asegúrese de que funcionará como se esperaba cuando está completamente distribuido.

## <a name="security"></a>Seguridad

- **Implementar la protección de nivel de aplicación contra distribuido ataques de denegación de servicio.** Servicios de Azure están protegidos contra ataques DDos en la capa de red. Sin embargo, Azure no puede proteger frente a ataques de nivel de aplicación, ya que es difícil distinguir entre las solicitudes de usuario es true de solicitudes de usuario malintencionado. Para obtener más información acerca de cómo protegerse de ataques DDoS de nivel de aplicación, vea la sección "Protección contra DDoS" de [Seguridad de la red de Microsoft Azure](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf) (descargar PDF).

- **Implementar el principio de menor privilegio para tener acceso a recursos de la aplicación.** El valor predeterminado para el acceso a recursos de la aplicación debería restrictivo posible. Conceder permisos de nivel superior en una base de aprobación. Conceder acceso demasiado permisivo a recursos de la aplicación predeterminada puede dar lugar a alguien deliberadamente o por accidente eliminar recursos. Azure proporciona [control de acceso basado en roles](../active-directory/role-based-access-built-in-roles.md) para administrar los privilegios de usuario, pero es importante comprobar los permisos de privilegios mínimos de otros recursos que tienen sus propios sistemas de permisos, como SQL Server. 

## <a name="telemetry"></a>Telemetría

- **Inicie sesión en los datos de telemetría mientras se ejecuta la aplicación en el entorno de producción.** Capturar información de telemetría sólida mientras se ejecuta la aplicación en el entorno de producción o no tiene suficiente información para diagnosticar la causa de problemas mientras están atendiendo a los usuarios. Más información está disponible en el registro de los procedimientos recomendados está disponible en la [Guía de supervisión y diagnóstico] [ monitoring-and-diagnostics-guidance] documento.

- **Implementar registro mediante un modelo asincrónico.** Si se sincrónico operaciones de registro, puede bloquear el código de la aplicación. Asegúrese de que las operaciones de registro se implementan como operaciones asincrónicas. 

- **Relacionar datos de registro a través de los límites del servicio.** En una aplicación de n niveles típica, una solicitud de usuario puede recorrer varios los límites del servicio. Por ejemplo, una solicitud de usuario normalmente se ha originado en el nivel de web y se pasa al nivel de empresa y finalmente se conservan en el nivel de datos. En escenarios más complejos, una solicitud de usuario puede distribuirse a muchos almacena distintos de datos y servicios. Asegúrese de que el sistema de registro establece una correlación entre llamadas a través de los límites del servicio para que puede realizar un seguimiento de la solicitud en toda la aplicación.

##  <a name="azure-resources"></a>Recursos de Azure 

- **Use las plantillas de administrador de recursos de Azure a disposición los recursos.** Plantillas de administrador de recursos que sea más fácil automatizar las implementaciones a través de PowerShell o la CLI Azure, lo que lleva a un proceso de implementación más fiable. Para obtener más información, vea [información general del Administrador de recursos de Azure][resource-manager].

- **Asignar recursos a un nombre descriptivo.** Darle un nombre descriptivo de recursos facilita encontrar un recurso específico y entender su función. Para obtener más información, vea [recomendado convenciones de nomenclatura para recursos de Azure](guidance-naming-conventions.md) 

- **Utilizar el control de acceso basado en roles (RBAC)**. Use RBAC para controlar el acceso a los recursos de Azure que implementa. RBAC le permite asignar roles de autorización a los miembros del equipo DevOps, para evitar el borrado accidental o cambios a recursos implementados. Para obtener más información, vea [Introducción a la administración de acceso en el portal de Azure](../active-directory/role-based-access-control-what-is.md) 

- **Usar bloqueos de recursos para los recursos críticos, como máquinas virtuales.** Bloqueos de recursos impiden que un operador elimine accidentalmente un recurso. Para obtener más información, vea [recursos de bloqueo con el Administrador de recursos de Azure](../resource-group-lock-resources.md) 

- **Pares regionales.** Cuando se implementa en dos regiones, elija regiones en el mismo par regional. En caso de una interrupción amplia, tiene prioridad la recuperación de una región de cada par. Algunos servicios como almacenamiento Geo redundante proporcionan replicación automática en la región pareja. Para obtener más información, consulte [empresarial continuidad y recuperación ante desastres (BCDR): regiones de Azure emparejadas](../best-practices-availability-paired-regions.md) 

- **Organizar los grupos de recursos, función y del ciclo de Vida**.  En general, un grupo de recursos debe contener recursos que comparten el mismo ciclo de vida. Facilita administrar implementaciones, eliminar distribuciones de prueba y asignar derechos de acceso, reduciendo las posibilidades que una implementación de producción accidentalmente se ha eliminado o modificada. Crear grupos de recursos independientes de producción, desarrollo y entornos de prueba. En una implementación de múltiples región, coloque recursos para cada región en grupos de recursos independientes. Esto facilita volver a implementar una región sin afectar a la demás regiones. 

## <a name="azure-services"></a>Servicios de Azure 

Los siguientes elementos de lista de comprobación se aplican a servicios específicos en Azure.

###  <a name="app-service"></a>Aplicación de servicio 

- **Use nivel estándar o Premium.** Estos niveles admiten ranuras provisionales y automatizado copias de seguridad. Para obtener más información, vea [introducción exhaustiva de planes de servicio de la aplicación de Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) 

- **Evite escalar hacia arriba o hacia abajo.** En su lugar, seleccione un nivel y el tamaño de la instancia que cumplen los requisitos de rendimiento en carga típica y, a continuación, [escalar](../app-service-web/web-sites-scale.md) las instancias para controlar los cambios en el volumen de tráfico. Ajuste de escala hacia arriba y abajo, puede activar reinicio de la aplicación.  

- **Configuración del almacén como la configuración de la aplicación.** Usar configuración de la aplicación para que contenga los valores de configuración como la configuración de la aplicación. Defina la configuración de las plantillas de administrador de recursos o uso de PowerShell, para que puede aplicar como parte de una implementación automatizada o actualizar el proceso, que es más fiable. Para obtener más información, vea [Configurar web apps en el servicio de aplicación de Azure](../app-service-web/web-sites-configure.md). 

- **Crear planes de servicio de aplicación independientes de prueba y producción.** No use ranuras en la implementación de producción para probar.  Todas las aplicaciones en el mismo plan de servicio de aplicación compartan las mismas instancias de máquina virtual. Si coloca en el mismo plan de producción y distribuciones de prueba, negativo puede afectar a la implementación de producción. Por ejemplo, las pruebas de carga ralentice el sitio de producción. Si coloca implementaciones de prueba en un plan independiente, aislar ellos desde la versión de producción.  

- **Aplicaciones web independientes de web API**. Si su solución tiene un sitio web front-end y un sitio web API, considere la posibilidad de desglosar ellos en aplicaciones de servicio de aplicación independientes. Este diseño facilita la descomponer la solución de carga de trabajo. Puede ejecutar la aplicación web y la API en planes de servicio de aplicación independientes, por lo que puede ampliarse de forma independiente. Si no necesita ese nivel de escalabilidad en primer lugar, puede implementar las aplicaciones en el mismo plan y muévalos a un plan independiente más adelante, si es necesario.

- **Evitar el uso de la característica de copia de seguridad de servicio de aplicaciones para realizar copias de seguridad de las bases de datos de SQL Azure.** En su lugar, use [las copias de seguridad de automatizado de base de datos de SQL][sql-backup]. Copia de seguridad de servicio de aplicación exporta la base de datos a un archivo de .bacpac SQL, que cuesta DTUs.  

- **Implementar en una franja de ensayo.** Crear un espacio de implementación de ensayo. Implementar actualizaciones de la aplicación en la franja de ensayo y comprobar la implementación antes de intercambiar en producción. Esto reduce la probabilidad de una actualización errónea en producción. También asegura que todas las instancias se caliente antes de que se ha intercambiado en producción. Muchas aplicaciones tienen una preparación significativa y la hora de inicio de fría. Para obtener más información, vea [Configurar el ensayo entornos de web apps en el servicio de aplicación de Azure](../app-service-web/web-sites-staged-publishing.md). 

-  **Crear un espacio de implementación para mantener la implementación de última buena conocida (LKG).** Cuando se implementa una actualización en producción, mueva la implementación de producción anterior en la franja de última configuración válida Conocida. Esto facilita revertir una implementación incorrecta. Si detecta un problema más adelante, puede volver rápidamente a la versión de la última configuración válida Conocida. Para obtener más información, vea [arquitectura de referencia de Azure: aplicación web básica](guidance-web-apps-basic.md). 

- **Habilitar el registro de diagnóstico**, incluido el registro de la aplicación y el registro del servidor web. Registro es importante para supervisar y diagnósticos. Vea [Habilitar registro de diagnósticos para aplicaciones web en la aplicación de servicio de Azure](../app-service-web/web-sites-enable-diagnostic-log.md)

- **Registro de almacenamiento de blobs**. Esto facilita recopilar y analizar los datos. 

- **Crear una cuenta de almacenamiento independiente para los registros.** No use la misma cuenta de almacenamiento para los registros y los datos de la aplicación. Esto ayuda a evitar que el registro de reducción de rendimiento de la aplicación. 

- **Supervisar el rendimiento.** Use un servicio como [Nuevo Relic](http://newrelic.com/) o [Aplicación perspectivas](../application-insights/app-insights-overview.md) para supervisar el rendimiento de aplicación y el comportamiento de carga de supervisión de rendimiento.  Supervisar el rendimiento, proporciona una perspectiva en tiempo real en la aplicación. Permite diagnosticar problemas y realizar análisis de la causa de errores. 


###  <a name="application-gateway"></a>Puerta de enlace de aplicación 

- **Aprovisionar al menos dos instancias.** Implementar aplicación de puerta de enlace con al menos dos instancias. Una sola instancia es un punto único de error. Usar dos o más instancias de redundancia y escalabilidad. Para poder optar por el [SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_0/), debe proporcionar dos o más instancias más grandes o medianas. 

### <a name="azure-search"></a>Búsqueda de Azure

- **Aprovisionar más de una réplica.** Use al menos dos réplicas para la lectura de alta disponibilidad o tres para alta disponibilidad de lectura y escritura.

- **Configurar indizadores para implementaciones de múltiples región.** Si tiene una implementación de múltiples región, tenga en cuenta las opciones de continuidad de indización.

    - Si el origen de datos es replicar geo, seleccione cada indizador de cada servicio de búsqueda de Azure regional su réplica de origen de datos local.  

    - Si el origen de datos no está replicada geo, seleccione varios indizadores al mismo origen de datos, para que los servicios de búsqueda de Azure en varias regiones continuamente e independiente de índice del origen de datos. Para obtener más información, consulte [Consideraciones de rendimiento y optimización de búsqueda de Azure][search-optimization].

###  <a name="azure-storage"></a>Almacenamiento de Azure 

- **Datos de aplicación, utilice el almacenamiento de acceso de lectura geo redundantes (RA GRS).** Almacenamiento de RA GRS duplica los datos en una región secundaria y proporciona acceso de solo lectura de la región secundario. Si hay una interrupción de almacenamiento en la región principal, la aplicación puede leer los datos de la región secundaria. Para obtener más información, vea [replicación de almacenamiento de Azure](../storage/storage-redundancy.md).

- **Discos de VM para usar el almacenamiento de Premium** Para obtener más información, vea [almacenamiento Premium: almacenamiento de alto rendimiento para cargas de trabajo de máquina Virtual de Azure](../storage/storage-premium-storage.md).

- **Para el almacenamiento de cola, cree una cola de copia de seguridad en otra región.** Para el almacenamiento de cola, una réplica de solo lectura tiene un uso limitado, porque no se puede poner en cola o quitar elementos de la cola. En su lugar, cree una cola de copia de seguridad de una cuenta de almacenamiento en otra región. Si hay una interrupción de almacenamiento, la aplicación puede utilizar la cola de la copia de seguridad, hasta que el área principal vuelva a estar disponible. De este modo, la aplicación todavía puede procesar solicitudes nuevas.  

###  <a name="documentdb"></a>DocumentDB 

- **Replicar la base de datos en todas las regiones.** Con una cuenta de región múltiples, la base de datos de DocumentDB tiene una escritura región y varias áreas de lectura. Si se produce un error en la región de escritura, puede leer desde otra réplica. El SDK del cliente controla este aspecto automáticamente. También puede conmutar la región de escritura para otra región. Para obtener más información, vea [distribuir datos globalmente con DocumentDB](../documentdb/documentdb-distribute-data-globally.md).


###  <a name="sql-database"></a>Base de datos SQL 

- **Use nivel estándar o Premium.** Estos niveles proporcionan un período ya en un momento restaurar (35 días). Para obtener más información, consulte [rendimiento y opciones de la base de datos de SQL](../sql-database/sql-database-service-tiers.md).

- **Habilitar la auditoría de la base de datos de SQL.** Auditoría de puede usarse para diagnosticar ataques o errores humanos. Para obtener más información, vea [Introducción a la auditoría de la base de datos SQL](../sql-database/sql-database-auditing-get-started.md). 

- **Replicación de Geo Active usar** Usar la replicación de Geo Active para crear un secundario legible en una región diferente.  Si se produce un error en la base de datos principal, o simplemente necesita desconectar, realizar una migración tras error manual a la base de datos secundario.  Hasta conmutar, la base de datos secundaria será de solo lectura.  Para obtener más información, vea [Base de datos activa Geo replicación de SQL](../sql-database/sql-database-geo-replication-overview.md). 

- **Sharding de uso**. Considere la posibilidad de usar sharding para partición horizontal de la base de datos. Sharding puede proporcionar aislamiento de errores. Para obtener más información, consulte [escala fuera con la base de datos de SQL Azure](../sql-database/sql-database-elastic-scale-introduction.md). 

- **Utilice Restaurar de punto en el tiempo para recuperar de los errores humanos.**  Restaurar un momento devuelve la base de datos en un momento anterior en el tiempo. Para obtener más información, vea [recuperar una base de datos de SQL Azure con copias de seguridad de base de datos automatizada][sql-restore].

- **Use geo restaurar para recuperar de una interrupción del servicio.** Restaurar geo restaura una base de datos de una copia de seguridad geo redundantes.  Para obtener más información, vea [recuperar una base de datos de SQL Azure con copias de seguridad de base de datos automatizada][sql-restore].


###  <a name="sql-server-running-in-a-vm"></a>SQL Server (que se ejecuta en una máquina virtual)

- **Replicar la base de datos.** Usar SQL Server siempre en disponibilidad de grupos para replicar la base de datos. Proporciona alta disponibilidad si se produce un error en una instancia de SQL Server. Para obtener más información, consulte [más información...](guidance-compute-n-tier-vm.md) 

- **Realizar copias de seguridad de la base de datos**. Si ya está usando la [Copia de seguridad de Azure](https://azure.microsoft.com/documentation/services/backup/) para realizar copias de seguridad de sus máquinas virtuales, considere la posibilidad de usar [Copias de seguridad de Azure para cargas de trabajo de SQL Server mediante DPM](../backup/backup-azure-backup-sql.md). Con este método, hay un rol de administrador de reserva para la organización y un procedimiento de recuperación unificado para máquinas virtuales y SQL Server. En caso contrario, use la [Copia de seguridad SQL Server administra a Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx). 


###  <a name="traffic-manager"></a>Administrador de tráfico 

- **Realizar una recuperación manual.** Después de la migración de administrador de tráfico, realizar una recuperación manual, en lugar de volver automáticamente con errores. Antes de que no atrás, compruebe que todos los subsistemas de aplicación están correcto.  En caso contrario, puede crear una situación donde la aplicación voltea y hacia atrás entre los centros de datos. Para obtener más información, vea [Ejecutar máquinas virtuales en varias áreas](guidance-compute-multiple-datacenters.md).

- **Crear un extremo de sondeo de estado**. Crear un extremo personalizado que informa sobre el estado general de la aplicación. Esto permite a tráfico Manager conmutar si cualquier ruta crítica falla, no solo el front-end. El punto final debe devolver un código de error HTTP si cualquier dependencia crítica está mal estado o no disponible. No informar de los errores de los servicios no esenciales, sin embargo. En caso contrario, el sondeo de salud puede desencadenar una migración tras error cuando no es necesario, crear falsas. Para obtener más información, consulte [Administrador de tráfico extremo supervisión y migración tras error](../traffic-manager/traffic-manager-monitoring.md).


###  <a name="virtual-machines"></a>Máquinas virtuales de Windows 

- **Evite ejecutar una carga de trabajo en una única VM.** Una sola implementación de VM no está y son resistente a planificado o mantenimiento. En su lugar, coloque varios VM en un conjunto de disponibilidad o un conjunto de escala VM, con un equilibrador de carga en primer plano. Para poder optar por el SLA, se debe implementar al menos dos máquinas virtuales en el mismo conjunto de disponibilidad. Para obtener más información, vea [Información general de conjuntos de escala de máquina Virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). 

- **Especificar la disponibilidad establecer al aprovisionar la máquina virtual.** Actualmente, no hay ninguna manera de agregar un administrador de recursos de VM a una disponibilidad establecer después de la máquina virtual se aprovisiona. Cuando se agrega una nueva máquina virtual para una disponibilidad existente establecer, asegúrese de crear una NIC para la máquina virtual y agregar la NIC al grupo de direcciones de back-end en el equilibrador de carga. En caso contrario, el equilibrador de carga no enruta el tráfico de red a máquina. 

- **Coloque cada nivel de la aplicación en un conjunto de disponibilidad independiente.** En una aplicación de N niveles, no coloque máquinas virtuales de diferentes niveles en el mismo conjunto de disponibilidad. Máquinas virtuales en un conjunto de disponibilidad se ubican en dominios de error (FDs) y actualización dominios (Ud.). Sin embargo, para obtener el beneficio de redundancia de FDs y UDs, cada VM en el conjunto de disponibilidad debe poder controlar las mismas solicitudes de cliente. 

- **Elija el tamaño correcto de VM en función de los requisitos de rendimiento.** Al mover una carga de trabajo existente a Azure, comience con el tamaño de la memoria virtual que se parezca a los servidores locales. A continuación, medir el rendimiento de la carga de trabajo real con respecto a la CPU, memoria y disco IOPS y ajustar el tamaño, si es necesario. Esto ayuda a garantizar que la aplicación se comporta según lo esperado en un entorno de nube. Además, si necesita varias NIC, tenga en cuenta el límite de NIC para cada tamaño. 

- **Utilice el almacenamiento de premium para VHD.** Almacenamiento de Azure Premium proporciona soporte de disco de alto rendimiento, baja latencia. Para obtener más información, vea [almacenamiento Premium: almacenamiento de alto rendimiento para cargas de trabajo de máquina Virtual de Azure](../storage/storage-premium-storage.md) elegir un tamaño VM que admite el almacenamiento de premium. 

- **Crear una cuenta de almacenamiento independiente para cada máquina virtual.** Coloque los VHD para una VM a una cuenta de almacenamiento independiente. Esto ayuda a evitar alcance los límites IOPS de cuentas de almacenamiento. Para obtener más información, vea [destinos de rendimiento y escalabilidad de almacenamiento de Azure](../storage/storage-scalability-targets.md). Sin embargo, si va a implementar una gran cantidad de máquinas virtuales, tenga en cuenta del límite por suscripción para cuentas de almacenamiento. Vea [los límites de almacenamiento](../azure-subscription-service-limits.md#storage-limits).

- **Crear una cuenta de almacenamiento independiente para los registros de diagnóstico**. No escritura registros de diagnóstico a la misma cuenta de almacenamiento como VHD, para evitar que el registro de diagnóstico afectan a las IOPS para los discos VM. Una cuenta de almacenamiento redundante local (LRS) estándar es suficiente para registros de diagnóstico. 

- **Instalar aplicaciones en un disco de datos, no el disco de sistema operativo.** En caso contrario, es posible alcanzar el límite de tamaño de disco. 

- **Utilice copia de seguridad de Azure para realizar copias de seguridad de máquinas virtuales.** Copias de seguridad de protección contra la pérdida de datos accidental. Para obtener más información, vea [Proteger Azure máquinas virtuales con un depósito de servicios de recuperación](../backup/backup-azure-vms-first-look-arm.md).

- **Habilitar los registros de diagnóstico**, incluidos métricas básicas de mantenimiento, los registros de infraestructura y los [Diagnósticos de arranque][boot-diagnostics]. Diagnósticos de arranque pueden ayudarle a diagnosticar un error de inicio si obtiene su máquina virtual en un estado no sea de inicio. Para obtener más información, vea [Información general de Azure registros de diagnóstico][diagnostics-logs].

- **Usar la extensión AzureLogCollector**. (Sólo VM de Windows). Esta extensión agrega registros de la plataforma Windows Azure y cargas con el almacenamiento de Azure, sin el operador remotamente iniciando sesión en la máquina virtual. Para obtener más información, vea [AzureLogCollector extensión](../virtual-machines/virtual-machines-windows-log-collector-extension.md).


###  <a name="virtual-network"></a>Red virtual 

- **Lista blanca o bloque de direcciones IP públicas, agregar un GSN a la subred.** Bloquear el acceso de usuarios malintencionados o permitir el acceso únicamente de los usuarios con privilegios de acceso a la aplicación.  

- **Crear un sondeo de estado personalizado.** Puede probar el estado sondeos de equilibrador de carga HTTP o TCP. Si una máquina virtual ejecuta un servidor HTTP, el sondeo HTTP es un mejor indicador de estado de un sondeo TCP. Para un sondeo HTTP, use un extremo personalizado que informa del estado general de la aplicación, incluidas todas las dependencias de tareas críticas. Para obtener más información, vea [información general del equilibrador de carga de Azure](../load-balancer/load-balancer-overview.md).

- **No bloquear el sondeo de estado.** El sondeo de estado del equilibrador de carga se envía desde una dirección IP conocida, 168.63.129.16. No bloquear el tráfico a o desde esta dirección IP en las directivas de firewall o reglas (GSN) del grupo de seguridad de red. Bloquee el sondeo de salud haría que el equilibrador de carga quitar la máquina virtual de giro. 

- **Habilitar el registro del equilibrador de carga.** Los registros muestran cuántas VM en back-end no se están recibiendo el tráfico de red debido a las respuestas de error de sondeo. Para obtener más información, vea [análisis de registro de equilibrador de carga de Azure](../load-balancer/load-balancer-monitor-log.md).


<!-- links -->
[app-service-autoscale]: ../monitoring-and-diagnostics/insights-how-to-scale.md
[asynchronous-c-sharp]:https://msdn.microsoft.com/library/mt674882.aspx
[availability-sets]:../virtual-machines/virtual-machines-windows-manage-availability.md
[azure-backup]: https://azure.microsoft.com/documentation/services/backup/
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[circuit-breaker]: https://msdn.microsoft.com/library/dn589784.aspx
[cloud-service-autoscale]: ../cloud-services/cloud-services-how-to-scale.md
[diagnostics-logs]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[fma]: guidance-resiliency-failure-mode-analysis.md
[guidance-resilient-deployment]: guidance-resiliency-overview.md#resilient-deployment
[load-balancer]: load-balancer/load-balancer-overview.md
[monitoring-and-diagnostics-guidance]: ../best-practices-monitoring.md
[resource-manager]: ../azure-resource-manager/resource-group-overview.md
[retry-pattern]: https://msdn.microsoft.com/library/dn589788.aspx
[retry-service-guidance]: ../best-practices-retry-service-specific.md
[search-optimization]: ../search/search-performance-optimization.md
[sql-backup]: ../sql-database/sql-database-automated-backups.md
[sql-restore]: ../sql-database/sql-database-recovery-using-backups.md
[traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[traffic-manager-routing]: ../traffic-manager/traffic-manager-routing-methods.md
[vmss-autoscale]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md
