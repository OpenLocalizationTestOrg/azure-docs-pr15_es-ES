<properties
   pageTitle="Sincronización de Azure AD Connect: descripción de la arquitectura | Microsoft Azure"
   description="En este tema se describe la arquitectura de sincronización de Azure AD Connect y se explica los términos usados."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/31/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Sincronización de Azure AD Connect: descripción de la arquitectura
Este tema trata sobre la arquitectura básica para la sincronización de Azure AD Connect. En muchos aspectos, es similar a sus predecesoras MIIS 2003, ILM 2007 y 2010 FIM. Sincronización de Azure AD Connect es la evolución de estas tecnologías. Si está familiarizado con cualquiera de estas tecnologías anteriores, el contenido de este tema se podrán familiar también. Si es nuevo en sincronización, este tema es para usted. Sin embargo no es necesario conocer los detalles de este tema para tener éxito en hacer que las personalizaciones de sincronización de Azure AD Connect (denominado motor de sincronización en este tema).

## <a name="architecture"></a>Arquitectura
El motor de sincronización crea una vista integrada de objetos que se almacenan en varios orígenes de datos conectados y administra la información de identidad en los orígenes de datos. Esta vista integrada depende de la información de identidad recuperada de orígenes de datos conectados y un conjunto de reglas que determinan cómo procesar esta información.

### <a name="connected-data-sources-and-connectors"></a>Orígenes de datos conectados y conectores
El motor de sincronización procesa la información de identidad de repositorios de datos diferentes, como Active Directory o una base de datos de SQL Server. Cada repositorio de datos que organiza los datos en un formato de base de datos y que proporciona métodos de acceso a datos estándar es un potencial candidato de origen de datos para el motor de sincronización. Los repositorios de datos que se sincronizan motor de sincronización se denominan **conectado a orígenes de datos** o **conectado directorios** (CD).

El motor de sincronización encapsula interacción con un origen de datos conectado dentro de un módulo denominado un **conector**. Cada tipo de origen de datos conectado tiene un conector específico. El conector traduce una operación necesaria en el formato que comprende el origen de datos conectados.

Conectores realizan llamadas de API para intercambiar información de identidad (leer y escribir) con un origen de datos conectados. También es posible agregar un conector personalizado mediante el marco de conectividad extensible. La ilustración siguiente muestra cómo un conector conecta un origen de datos conectado al motor de sincronización.

![Arq1](./media/active-directory-aadconnectsync-understanding-architecture/arch1.png)

Pueden flujo de datos en cualquier dirección, pero no puede flujo en ambas direcciones al mismo tiempo. En otras palabras, un conector puede estar configurado para permitir datos fluya desde el origen de datos conectado al motor de sincronización o desde el motor de sincronización con el origen de datos conectados, pero solo alguna de estas operaciones puede ocurrir en cualquier momento para un objeto y atributo. La dirección puede ser diferente para diferentes objetos y atributos diferentes.

Para configurar un conector, especifique los tipos de objeto que desea sincronizar. Especificar los tipos de objeto define el ámbito de objetos que se incluyen en el proceso de sincronización. El siguiente paso es seleccionar los atributos para sincronizar, que se conoce como una lista de inclusión de atributos. Esta configuración se puede cambiar cualquier momento en respuesta a cambios en las reglas de la empresa. Al usar el Asistente para instalación de Azure AD Connect, estos valores se configuran para usted.

Para exportar objetos a un origen de datos conectados, la lista de inclusión de atributo debe incluir al menos los atributos mínimos necesarios para crear un tipo específico de un origen de datos conectados. Por ejemplo, el atributo **sAMAccountName** debe incluirse en la lista de inclusión de atributo para exportar un objeto de usuario en Active Directory porque todos los objetos de usuario en Active Directory deben tener un atributo **sAMAccountName** definido. De nuevo, el Asistente para instalación hace esta configuración para usted.

Si el origen de datos conectados utiliza componentes estructurales, como particiones o contenedores para organizar objetos, puede limitar las áreas en el origen de datos conectados que se usan para una solución determinada.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Estructura interna del espacio de nombres del motor de sincronización
El espacio de nombres del motor de sincronización completa consta de dos espacios de nombres que almacenan la información de identidad. Dos espacios de nombres son:

- El espacio de conector (CS)
- Metaverse (MV)

El **espacio del conector** es un área de ensayo que contiene representaciones de los objetos designados desde un origen de datos conectado y los atributos especificados en la lista de inclusión de atributo. El motor de sincronización utiliza el espacio del conector a determinar qué ha cambiado en el origen de datos conectados a cambios entrantes de fase. El motor de sincronización también usa el espacio del conector para organizar los cambios de salida para exportar al origen de datos conectados. El motor de sincronización mantiene un espacio distinto de conector como un área de ensayo para cada conector.

Mediante el uso de un área de ensayo, el motor de sincronización permanece independiente de los orígenes de datos conectados y no afecta a su disponibilidad y la accesibilidad. Como resultado, puede procesar información de identidad en cualquier momento mediante el uso de los datos en el área de ensayo. El motor de sincronización puede solicitar sólo los cambios realizados en el origen de datos conectado desde la última ha finalizado la sesión de comunicación o inserción solo los cambios a la información de identidad que el origen de datos conectado aún no ha recibido, lo que reduce el tráfico de red entre el motor de sincronización y el origen de datos conectados.

Además, el motor de sincronización almacena información de estado sobre todos los objetos que etapas en el espacio del conector. Cuando se reciben nuevos datos, el motor de sincronización siempre se evalúa como si ya se ha sincronizado los datos.

El **metaverso** es un área de almacenamiento que contiene la información de identidad agregado de varios orígenes de datos conectados, proporcionar una única vista global, integrada de todos los objetos combinados. Objetos de Metaverse se crean en función de la información de identidad se recupera de los orígenes de datos conectados y un conjunto de reglas que le permite personalizar el proceso de sincronización.

La ilustración siguiente muestra los espacios de nombres de espacio de conector y metaverse dentro del motor de sincronización.

![Arch2](./media/active-directory-aadconnectsync-understanding-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Objetos de identidad de motor de sincronización
Los objetos en el motor de sincronización son representaciones de objetos en el origen de datos conectados o que tenga la vista integrada que motor de sincronización de dichos objetos. Cada objeto del motor de sincronización debe tener un identificador único global (GUID). GUID proporcionan integridad de datos y express relaciones entre objetos.

### <a name="connector-space-objects"></a>Objetos de espacio de conector
Cuando el motor de sincronización se comunica con un origen de datos conectado, lea la información de identidad en el origen de datos conectados y utiliza esa información para crear una representación de la identidad de objeto en el espacio del conector. No puede crear o eliminar estos objetos de forma individual. Sin embargo, puede eliminar manualmente todos los objetos de un espacio de conector.

Todos los objetos en el espacio del conector tienen dos atributos:

- Un identificador único global (GUID)
- Un nombre distintivo (DN)

Si el origen de datos conectados asigna un atributo único para el objeto, objetos en el espacio del conector también pueden tener un atributo de anclaje. El atributo de anclaje identifica un objeto en el origen de datos conectados. El motor de sincronización utiliza el delimitador para localizar la representación de este objeto correspondiente en el origen de datos conectados. Motor de sincronización se presupone que el delimitador de un objeto nunca cambia a lo largo del objeto.

Muchos de los conectores utilizan un identificador único conocido para generar un delimitador automáticamente para cada objeto cuando se importa. Por ejemplo, el conector de Active Directory usa el atributo **GUID de objeto** para un delimitador. Para los orígenes de datos conectados que no proporcionan un identificador único claramente definido, puede especificar la generación de anclaje como parte de la configuración del conector.

En cuanto a mayúsculas y minúsculas, que se basa el delimitador desde una o más atributos únicos de un objeto escriba, ni de los cambios y que únicamente identifica el objeto en el espacio de conector (por ejemplo, un número de empleado o un identificador de usuario).

Un objeto de espacio de conector puede ser uno de estos procedimientos:

- Un objeto de ensayo
- Un marcador de posición

### <a name="staging-objects"></a>Objetos de ensayo
Un objeto de ensayo representa una instancia de los tipos de objeto designado del origen de datos conectados. Además de la GUID y el nombre completo, un objeto de ensayo siempre tiene un valor que indica el tipo de objeto.

Objetos de ensayo que se han importado siempre tienen un valor para el atributo de anclaje. Objetos de ensayo recién configurados por el motor de sincronización y se está creando en el origen de datos conectados que no tiene un valor para el atributo de anclaje.

Objetos de ensayo albergar también distintos valores actuales de los atributos de negocio y operativa información necesaria motor de sincronización para realizar el proceso de sincronización. Información operativa incluye indicadores que indican el tipo de las actualizaciones que se almacenan en el objeto de ensayo. Si un objeto de ensayo ha recibido la nueva información de la identidad del origen de datos conectados que aún no se ha procesado, el objeto está marcado como **pendiente importar**. Si un objeto de ensayo tiene nueva información de identidad que aún no se ha exportado al origen de datos conectados, se marca como **pendiente exportar**.

Un objeto de ensayo puede ser un objeto de importación o exportación. El motor de sincronización crea un objeto de importación con información de objeto recibida desde el origen de datos conectados. Cuando el motor de sincronización recibe información sobre la existencia de un objeto nuevo que coincida con uno de los tipos de objeto seleccionados en el conector, crea un objeto de importar el espacio del conector como una representación del objeto en el origen de datos conectados.

La siguiente ilustración muestra un objeto de importación que representa un objeto en el origen de datos conectados.

![Arch3](./media/active-directory-aadconnectsync-understanding-architecture/arch3.png)

El motor de sincronización crea un objeto de exportación con información de objeto en el metaverso. Exportar los objetos se exportan al origen de datos conectados durante la sesión de comunicación siguiente. Desde la perspectiva del motor de sincronización, exportar objetos no existen en el origen de datos conectados todavía. Por lo tanto, el atributo de anclaje de un objeto de exportación no está disponible. Después de recibir el objeto del motor de sincronización, el origen de datos conectados crea un valor único para el atributo de anclaje del objeto.

La ilustración siguiente muestra cómo se crea un objeto de exportación con información de identidad en el metaverso.

![Arch4](./media/active-directory-aadconnectsync-understanding-architecture/arch4.png)

El motor de sincronización confirma la exportación del objeto al importar el objeto del origen de datos conectados. Exportar los objetos se convierten en objetos de importación cuando el motor de sincronización recibe durante la siguiente importación de ese origen de datos conectados.

### <a name="placeholders"></a>Marcadores de posición
El motor de sincronización utiliza un espacio de nombres plano para almacenar objetos. Sin embargo, algunos orígenes de datos conectados como Active Directory utilizan un espacio de nombres jerárquico. Para transformar información desde un espacio de nombres jerárquico en un espacio de nombres plano, motor de sincronización utiliza marcadores de posición para conservar a la jerarquía.

Cada marcador de posición representa un componente (por ejemplo, una unidad organizativa) de un nombre de objeto jerárquica que no se ha importado en el motor de sincronización pero se necesita para crear el nombre jerárquico. Rellenen espacios creados por referencias en el origen de datos conectados a objetos que no son de ensayo objetos en el espacio del conector.

El motor de sincronización también usa marcadores de posición para almacenar los objetos que se hace referencia que no se han importado. Por ejemplo, si la sincronización está configurada para incluir el atributo de administrador para el objeto *Abbie isla* y el valor recibido es un objeto que no se ha importado, como *CN = Lee Sperry, CN = usuarios, DC = fabrikam, DC = com*, el Administrador de información se almacena como marcadores de posición en el espacio del conector. Si el objeto de administrador más adelante se importa, se sobrescribe el objeto de marcador de posición por el objeto de ensayo que representa el administrador.

### <a name="metaverse-objects"></a>Objetos de Metaverse
Un objeto de metaverso contiene la vista agregada tiene ese motor de sincronización de los objetos provisionales en el espacio del conector. Motor de sincronización crea los objetos de metaverso con la información en Importar objetos. Se pueden vincular varios objetos de espacio de conector a un objeto de metaverso único, pero no se puede vincular un objeto de espacio de conector a más de un objeto de metaverse.

Objetos de metaverso no pueden crearse o eliminarse manualmente. El motor de sincronización elimina automáticamente los objetos de metaverso que no tiene un vínculo a cualquier objeto de espacio de conector en el espacio del conector.

Para asignar objetos dentro de un origen de datos conectados a un tipo de objeto correspondiente en el metaverso, motor de sincronización proporciona un esquema extensible con un conjunto predefinido de tipos de objetos y atributos asociados. Puede crear nuevos tipos de objetos y atributos de objetos de metaverso. Pueden ser atributos multivalor o un solo valor y los tipos de atributo pueden ser cadenas, referencias, números y valores booleanos.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Relaciones entre objetos de ensayo y metaverse
En el espacio de nombres del motor de sincronización, el flujo de datos está habilitado por la relación de vínculo entre objetos provisionales y metaverse. Un objeto de ensayo que está vinculado a un objeto de metaverso se denomina una **unido objeto** (o un **objeto de conector**). Un objeto de ensayo que no está vinculado a un objeto de metaverso se denomina una **separará el objeto** (o un **objeto desconector**). Los términos Unido y separado preferidos no confundir con los conectores responsables de importación y exportación de datos desde un directorio conectado.

Marcadores de posición nunca están vinculadas a un objeto de metaverso

Un objeto combinado consta de un objeto de ensayo y su relación vinculada a un objeto de metaverso único. Objetos combinados se utilizan para sincronizar los valores de atributo entre un objeto de espacio de conector y un objeto de metaverso.

Cuando un objeto de ensayo se convierte en un objeto combinado durante la sincronización, pueden flujo de atributos entre el objeto de ensayo y el objeto de metaverse. Flujo de atributos es bidireccional y se configura con reglas de atributo de importar y exportar reglas de atributo.

Un objeto de espacio de conector solo se pueden vincular a un único objeto de metaverse. Sin embargo, cada objeto de metaverso puede vincularse a varios objetos de espacio de conector en el mismo o en espacios de conector diferente, tal como se muestra en la siguiente ilustración.

![Arch5](./media/active-directory-aadconnectsync-understanding-architecture/arch5.png)

La relación entre el objeto de ensayo y un objeto de metaverso vinculada es persistente y se puede quitar las reglas que se especifican.

Un objeto separado es un objeto de ensayo que no está vinculado a cualquier objeto de metaverse. El atributo valores de un objeto separado no son procesado aún más cualquiera dentro de metaverse. No se actualizan los valores de atributo del objeto correspondiente del origen de datos conectadas por el motor de sincronización.

Mediante el uso de objetos separados, puede almacenar la información de identidad de motor de sincronización y procesamiento de más adelante. Mantener un objeto provisional como un objeto separado en el espacio del conector tiene muchas ventajas. Dado que el sistema ya ha almacenado provisionalmente la información necesaria sobre este objeto, no es necesario crear una representación de este objeto durante la importación siguiente del origen de datos conectados. De esta forma, el motor de sincronización siempre tiene una instantánea completa del origen de datos conectados, incluso si no hay ninguna conexión al origen de datos conectados. Objetos separados se pueden convertir en objetos combinados y viceversa, según las reglas que se especifican.

Se crea un objeto de importación como un objeto separado. Un objeto de exportación debe ser un objeto combinado. La lógica del sistema aplica esta regla y elimina todos los objetos de exportación no es un objeto combinado.

## <a name="sync-engine-identity-management-process"></a>Proceso de administración de identidades de motor de sincronización
El proceso de administración de identidades controla cómo se actualiza la información de identidad entre orígenes de datos conectados diferente. Administración de identidades se produce en tres procesos:

- Importar
- Sincronización
- Exportar

Durante el proceso de importación, el motor de sincronización evalúa la información de identidad entrante de un origen de datos conectados. Cuando se detectan cambios, crea nuevos objetos provisionales o actualiza los objetos de ensayo existentes en el espacio del conector para la sincronización.

Durante el proceso de sincronización, motor de sincronización actualiza el metaverso para reflejar los cambios que ocurrieron en el espacio del conector y el espacio del conector para reflejar los cambios que ocurrieron en metaverse.

Durante el proceso de exportación, el motor de sincronización inserta los cambios que se almacenan en objetos de ensayo y que estén marcados como pendientes de exportación.

La siguiente ilustración muestra dónde cada uno de los procesos se produce como flujos de información de identidad de un origen de datos conectados a otro.

![Arch6](./media/active-directory-aadconnectsync-understanding-architecture/arch6.png)

### <a name="import-process"></a>Proceso de importación
Durante el proceso de importación, el motor de sincronización evalúa las actualizaciones de la información de identidad. Motor de sincronización compara la información de identidad recibida desde el origen de datos conectado con la información de identidad sobre un objeto de ensayo y determina si el objeto de ensayo requiere actualizaciones. Si es necesario actualizar el objeto de ensayo con nuevos datos, el objeto de ensayo se marca como pendientes de importación.

Objetos en el espacio del conector antes de la sincronización de ensayo, el motor de sincronización puede procesar la información de identidad que ha cambiado. Este proceso proporciona las siguientes ventajas:

- **Sincronización eficiente**. Se minimiza la cantidad de datos que se procesan durante la sincronización.
- **Sincronización eficaz**. Puede cambiar la forma en que el motor de sincronización procesa información de identidad sin volver a conectar el motor de sincronización con el origen de datos.
- **Oportunidad para obtener una vista previa de sincronización**. Puede obtener una vista previa de sincronización para comprobar que las suposiciones sobre el proceso de administración de identidades son correctas.

Para cada objeto especificado en el conector, primero el motor de sincronización intenta encontrar una representación del objeto en el espacio de conector del conector. Motor de sincronización examina todos los objetos provisionales en el espacio del conector e intenta encontrar un objeto de ensayo correspondiente que tiene un atributo delimitador coincidente. Si no hay ningún objeto provisional existente tiene un atributo de anclaje coincidentes, motor de sincronización intenta encontrar un objeto de ensayo correspondiente con el mismo nombre completo.

Cuando el motor de sincronización encuentra un objeto de ensayo que coincida con nombre completo, pero no delimitador, se produce un comportamiento especial siguiente:

- Si el objeto que se encuentra en el espacio del conector no tiene ningún delimitador, el motor de sincronización quita este objeto en el espacio del conector y marca el objeto de metaverso que está vinculado a como **Reintentar aprovisionamiento en la siguiente sincronización ejecutar**. A continuación, crea el nuevo objeto de importación.
- Si el objeto que se encuentra en el espacio del conector tiene un delimitador, el motor de sincronización se supone que este objeto se ha cambiado el nombre o eliminado en el directorio conectado. Asigna un nombre completo de nuevo, temporal para el objeto de espacio de conector para que puede ensayar el objeto entrante. El objeto antiguo se convierte en **transitorias**, esperando a que el conector importar el cambio de nombre o eliminación para resolver la situación.

Si el motor de sincronización localiza un objeto de ensayo que corresponde al objeto especificado en el conector, determina qué tipo de cambios para aplicar. Por ejemplo, puede cambiar el nombre o eliminar el objeto en el origen de datos conectados motor de sincronización o solo pueden actualizar valores de los atributos del objeto.

Objetos de ensayo con datos actualizados se marcan como pendientes de importación. Diferentes tipos de pendientes importaciones están disponibles. Según el resultado del proceso de importación, un objeto de ensayo en el espacio del conector tiene uno de los siguientes pendientes tipos de importación:

- **Ninguno**. Cambios a cualquiera de los atributos del objeto provisional no están disponibles. Motor de sincronización no marca este tipo pendiente de importación.
- **Agregar**. El objeto de ensayo es un objeto de importar de nuevo en el espacio del conector. Motor de sincronización marca este tipo pendiente de importación para el procesamiento adicional en el metaverso.
- **Actualización**. Motor de sincronización encuentra un objeto de ensayo correspondiente en el espacio del conector y marca este tipo como pendientes de importación para que las actualizaciones de los atributos que se pueden procesar en metaverse. Las actualizaciones incluyen el cambio de nombre de objeto.
- **Eliminar**. Motor de sincronización encuentra un objeto de ensayo correspondiente en el espacio del conector y marca este tipo como pendientes de importación para que se pueden eliminar el objeto combinado.
- **Agregar o eliminar**. Motor de sincronización encuentra un objeto de ensayo correspondiente en el espacio del conector, pero no coinciden con los tipos de objeto. En este caso, un agregar eliminar modificación se ensaya. Agregar por eliminar A modificación indica que el motor de sincronización que debe producirse una sincronización completa de este objeto ya diferentes conjuntos de reglas que se aplican a este objeto cuando el objeto escriba cambios.

Al establecer el estado de importación pendiente de un objeto provisional, es posible reducir significativamente la cantidad de datos que procesa durante la sincronización, porque si lo hace, el sistema procesar sólo los objetos que se han actualizado los datos.

### <a name="synchronization-process"></a>Proceso de sincronización
Sincronización consta de dos procesos relacionados:

- Sincronización entrante, cuando se actualiza el contenido del metaverso usando los datos en el espacio del conector.
- Sincronización de salida, cuando se actualiza el contenido del espacio de conector con los datos en el metaverso.

Con la información que se almacenan en el espacio del conector, el proceso de sincronización entrante crea en el metaverso la vista integrada de los datos que se almacenan en los orígenes de datos conectados. Según se hayan configurado las reglas, se agregan todos los objetos provisionales o sólo aquellos con información de importación pendiente.

Las actualizaciones del proceso de sincronización de salida exportación objetos cuando cambien los objetos de metaverso.

Sincronización entrante crea la vista integrada en metaverse de la información de identidad procedente de orígenes de datos conectados. Motor de sincronización puede procesar información de identidad en cualquier momento, con la información más reciente de identidad que tiene desde el origen de datos conectados.

**Sincronización entrante**

Sincronización entrante incluye los siguientes procesos:

- **Aprovisionar** (también llamado **proyección** si es importante distinguir este proceso de aprovisionamiento de sincronización saliente). El motor de sincronización crea un nuevo objeto de metaverso basándose en un objeto de ensayo y vínculos a ellos. Aprovisionar es una operación de nivel de objeto.
- **Unirse a**. El motor de sincronización vincula un objeto de ensayo a un objeto de metaverso existente. Una combinación es una operación de nivel de objeto.
- **Flujo de atributos de importación**. Motor de sincronización actualiza los valores de atributo, denominados flujo de los atributos del objeto en el metaverso. Importar flujo de atributos es una operación de nivel de atributo que requiere un vínculo entre un objeto de ensayo y un objeto de metaverso.

Aprovisionar es el único proceso que crea objetos de metaverse. Aprovisionar afecta al importar objetos que son objetos separados. Durante la disposición, el motor de sincronización crea un objeto de metaverso correspondiente al tipo de objeto del objeto de importación y establece un vínculo entre dos objetos, creando así un objeto combinado.

El proceso de unión también establece un vínculo entre importar objetos y un objeto de metaverso. La diferencia entre join y aprovisionar es que el proceso de unión requiere que el objeto de importación están vinculadas a un objeto de metaverso existente, donde el proceso de aprovisionamiento crea un nuevo objeto de metaverse.

Motor de sincronización intenta unirse a un objeto de importación a un objeto de metaverso mediante criterios que se especifica en la configuración de regla de sincronización.

Motor de sincronización vínculos a un objeto separado durante los procesos de aprovisionamiento y unirse a un objeto de metaverso hacerle unido. Una vez completadas estas operaciones de nivel de objeto, el motor de sincronización puede actualizar los valores de atributo del objeto de metaverso asociado. Este proceso se denomina flujo de atributos de importación.

Importar flujo de atributos se produce en todos los objetos de importación que realizan los nuevos datos y están vinculados a un objeto de metaverso.

**Sincronización de salida**

Actualizaciones de sincronización saliente exportación objetos cuando un objeto de metaverso cambiar pero no se elimina. Es el objetivo de la sincronización de salida evaluar los cambios en los objetos de metaverso requieren las actualizaciones de objetos provisionales en los espacios de conector. En algunos casos, los cambios pueden requerir que ensayo objetos en todos los espacios de conector actualizarse. Objetos de ensayo que se cambian se marcan como pendientes de exportación, hace que exportar objetos. Estos objetos se resaltan más adelante los al origen de datos conectados durante el proceso de exportación de exportación.

Sincronización de salida tiene tres procesos:

- **Aprovisionamiento**
- **Baja**
- **Exportar flujo de atributos**

Aprovisionamiento y baja son ambas operaciones de nivel de objeto. Baja depende de aprovisionamiento porque el aprovisionamiento de solo puede iniciar. Baja se activa cuando el aprovisionamiento quita el vínculo entre un objeto de metaverso y un objeto de exportación.

Aprovisionamiento siempre se activa cuando se apliquen los cambios a los objetos de metaverse. Cuando se realizan cambios en los objetos de metaverso, el motor de sincronización puede realizar cualquiera de las siguientes tareas como parte del proceso de configuración:

- Crear objetos combinados, donde un objeto de metaverso está vinculado a un objeto de exportación recién creado.
- Cambiar el nombre de un objeto combinado.
- Retirar vínculos entre un objeto de metaverso y objetos, crear un objeto separado de ensayo.

Si aprovisionamiento requiere el motor de sincronización para crear un nuevo objeto de conector, el objeto de ensayo a los que está vinculado el objeto de metaverso siempre es un objeto de exportación, porque el objeto todavía no existe en el origen de datos conectados.

Si el aprovisionamiento de requiere el motor de sincronización para retirar un objeto combinado, crear un objeto separado, se activa la baja. El proceso de baja del usuario elimina el objeto.

Durante la baja, eliminación de un objeto de exportación no física elimina el objeto. El objeto se marca como **eliminado**, lo que significa que la operación de eliminación está preparada en el objeto.

Exportar flujo de atributos también se produce durante el proceso de sincronización de salida, similar a la forma en que el flujo de atributos de importación se produce durante la sincronización entrante. Flujo de atributos de exportación se produce sólo entre metaverse y exportación de los objetos que se unen.

### <a name="export-process"></a>Proceso de exportación
Durante el proceso de exportación, el motor de sincronización examina todos los objetos de exportación que estén marcados como pendientes de exportación en el espacio del conector y a continuación, envía actualizaciones al origen de datos conectado.

El motor de sincronización para determinar el éxito de exportación, pero lo suficientemente no puede determinar que el proceso de administración de identidades está completado. Siempre puede cambiar los objetos del origen de datos conectadas por otros procesos. Dado que el motor de sincronización no tiene una conexión permanente con el origen de datos conectados, no es suficiente realizar suposiciones sobre las propiedades de un objeto en el origen de datos conectados basado en una notificación de exportación correcta.

Por ejemplo, un proceso en el origen de datos conectados puede cambiar los atributos del objeto a sus valores originales (es decir, el origen de datos conectados puede sobrescribir los valores inmediatamente después de que los datos se inserta motor de sincronización y aplicados correctamente en el origen de datos conectados).

La almacena de motor de sincronización exportar e importa información de estado sobre cada objeto provisional. Si los valores de los atributos que se especifican en la lista de inclusión de atributo han cambiado desde la última exportación, el almacenamiento de importar y exportar estado permite motor de sincronización para reaccionar correctamente. Motor de sincronización utiliza el proceso de importación para confirmar los valores de los atributos que se han exportado al origen de datos conectados. Una comparación entre la información importada y exportada, tal como se muestra en la siguiente ilustración, permite motor de sincronización determinar si la exportación era correcta, o si es necesario repetir.

![Arch7](./media/active-directory-aadconnectsync-understanding-architecture/arch7.png)

Por ejemplo, si el motor de sincronización exporta atributo C, que tiene un valor de 5, a un origen de datos conectados, almacena C = 5 en su memoria del estado de exportación. Cada exportación adicional en los resultados de este objeto en un intento de volver a exportar C = 5 al origen de datos conectados porque el motor de sincronización, se supone que este valor no persistente aplicada al objeto (es decir, a menos que otro valor se importó recientemente desde el origen de datos conectados). Cuando se recibe C = 5 durante una operación de importación en el objeto, se borre la memoria de exportación.

## <a name="next-steps"></a>Pasos siguientes
Más información sobre la configuración de [sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Más información acerca de cómo [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
