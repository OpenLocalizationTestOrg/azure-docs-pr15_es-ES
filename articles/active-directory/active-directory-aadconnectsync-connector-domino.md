<properties
   pageTitle="Conector de Lotus Domino | Microsoft Azure"
   description="Este artículo describe cómo configurar Lotus Domino conector Microsoft."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="lotus-domino-connector-technical-reference"></a>Referencia técnica de Lotus Domino conector
En este artículo se describe el conector de Lotus Domino. El artículo se aplica a los siguientes productos:

- Administrador de identidad de Microsoft 2016 (MIM2016)
- Administrador de identidad de Forefront 2010 R2 (FIM2010R2)
    -   Debe usar revisión 4.1.3671.0 o posterior [KB3092178](https://support.microsoft.com/kb/3092178).

Para MIM2016 y FIM2010R2, el conector está disponible como descarga desde el [Centro de descarga de Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-lotus-domino-connector"></a>Información general sobre el conector de Lotus Domino
El conector de Lotus Domino le permite integrar el servicio de sincronización con el servidor de IBM Lotus Domino.

Desde una perspectiva de alto nivel, las siguientes características son compatibles con la versión actual del conector:

Característica | Soporte técnico
--- | ---
Origen de datos conectado | Servidor: <li>Lotus Domino 8.5</li><li>Lotus Domino 9.x</li>Cliente:<li>9.x de Lotus Notes</li>
Escenarios | <li>Administración del ciclo de vida de objeto</li><li>Administración de grupo</li><li>Administración de contraseñas</li>
Operaciones | <li>Completa y Delta de importación</li><li>Exportar</li><li>Establecer y cambiar la contraseña en contraseña HTTP</li>
Esquema | <li>Persona (usuario móvil, contacto (personas con ningún certificado))</li><li>Grupo</li><li>Recurso (reunión en línea de recursos, sala)</li><li>Base de datos de correo</li><li>Detección dinámica de atributos para objetos admitidos</li>

El conector de Lotus Domino utiliza al cliente de Lotus Notes para comunicarse con el servidor de Lotus Domino. Como consecuencia de esta dependencia, un cliente compatibles de Lotus Notes debe estar instalado en el servidor de sincronización. La comunicación entre el cliente y el servidor se implementa a través de la interfaz de interoperabilidad de Lotus Notes .NET (Interop.domino.dll). Esta interfaz facilita la comunicación entre la plataforma de Microsoft.NET y el cliente de Lotus Notes y permite el acceso a documentos de Lotus Domino y vistas. Para la importación de delta, también es posible que se utiliza la interfaz de C++ nativa (según el método de importación delta seleccionado).

### <a name="prerequisites"></a>Requisitos previos
Antes de utilizar el conector, asegúrese de que tiene los siguientes en el servidor de sincronización:

- 4.5.2 de Microsoft .NET Framework o versiones posteriores
- El cliente de Lotus Notes debe estar instalado en su servidor de sincronización
- El conector de Lotus Domino requiere Lotus Domino LDAP esquema base de datos predeterminada (schema.nsf) esté instalado en el servidor de directorio de Domino. Si no está presente, puede instalarlo ejecutando o reiniciar el servicio LDAP en el servidor de Domino.

### <a name="connected-data-source-permissions"></a>Permisos de origen de datos conectados
Para realizar cualquiera de las tareas compatibles en el conector de Lotus Domino, debe ser miembro de los grupos siguientes:

- Administradores de acceso completos
- Administradores
- Administradores de la base de datos

La siguiente tabla enumeran los permisos necesarios para cada operación:

Operación | Derechos de acceso
--- | ---
Importar | <li>Leer documentos públicos</li><li> Administrador de acceso total (cuando es miembro del grupo de administradores de acceso completo, automáticamente tiene un acceso eficaz a en ACL.)</li>
Exportar y establecer contraseña | Acceso eficaz: <li>Crear documentos</li><li>Eliminar documentos</li><li>Leer documentos públicos</li><li>Escribir documentos públicos</li><li>Documentos replicar o copia</li>Para operaciones de exportación, también tiene los siguientes roles: <li>CreateResource</li><li>GroupCreator</li><li>GroupModifier</li><li>UserCreator</li><li>UserModifier</li>

### <a name="direct-operations-and-adminp"></a>Las operaciones directas y AdminP
Operaciones vaya directamente al directorio de Domino o a través del proceso de AdminP. Los siguientes objetos de la lista de todos los admitidos, las operaciones de tablas y, si procede, el método de implementación relacionada:

**Libreta de direcciones principal**

Objeto | Crear | Actualización | Eliminar
--- | --- | --- | ---
Persona | AdminP | Directo | AdminP
Grupo | AdminP | Directo | AdminP
MailInDB | Directo | Directo | Directo
Recursos | AdminP | Directo | AdminP

**Libreta de direcciones secundario**

Objeto | Crear | Actualización | Eliminar
--- | --- | --- | ---
Persona | N/A. | Directo | Directo
Grupo | Directo | Directo | Directo
MailInDB | Directo | Directo | Directo
Recursos | N/A. | N/A. | N/A.

Cuando se crea un recurso, se crea un documento de notas. Del mismo modo, cuando se elimina un recurso, se elimina el documento de notas.

### <a name="ports-and-protocols"></a>Puertos y protocolos
Cliente de IBM Lotus Notes y servidores Domino comunican con notas remoto procedimiento llamar (NRPC) donde NRPC deben usar TCP/IP. El número de puerto predeterminado es 1352, pero se puede cambiar por el Administrador de Domino.

### <a name="not-supported"></a>No compatible
Las operaciones siguientes no son compatibles con la versión actual del conector de Lotus Domino:

- Mover buzón entre servidores.

## <a name="create-a-new-connector"></a>Crear un nuevo conector

### <a name="client-software-installation-and-configuration"></a>Configuración e instalación de Software de cliente
Lotus Notes deben estar instalados en el servidor **antes de** que está instalado el conector.

Cuando se instala, asegúrese de que seguir un **Único usuario instalar**. El valor predeterminado **Instalar multiusuario** no funciona.  
![Notes1](./media/active-directory-aadconnectsync-connector-domino/notes1.png)

En la página características, instale sólo las características de Lotus Notes necesarias y el **Inicio de sesión único de cliente**. Inicio de sesión único es necesaria para el conector poder iniciar sesión en el servidor de Domino.  
![Sobre la instalación2](./media/active-directory-aadconnectsync-connector-domino/notes2.png)

**Nota:** Iniciar Lotus Notes vez con un usuario que se encuentra en el mismo servidor de la cuenta que use como cuenta de servicio del conector. Además, asegúrese de cerrar al cliente de Lotus Notes en el servidor. No puede ejecutar al mismo tiempo que el conector intenta conectar con el servidor de Domino.

### <a name="create-connector"></a>Crear el conector
Para crear un conector de Lotus Domino, seleccione **Agente de administración** y **crear**en el **Servicio de sincronización** . Seleccione el conector de **Lotus Domino (Microsoft)** .  
![CreateConnector](./media/active-directory-aadconnectsync-connector-domino/createconnector.png)

Si su versión de servicio de sincronización ofrece la capacidad de configurar **arquitectura**, asegúrese de que el conector se establece en su valor predeterminado para que se ejecute en el **proceso**.

### <a name="connectivity"></a>Conectividad
En la página conectividad, debe especificar el nombre del servidor de Lotus Domino y escriba las credenciales de inicio de sesión.  
![Conectividad](./media/active-directory-aadconnectsync-connector-domino/connectivity.png)

La propiedad del servidor de Domino es compatible con dos formatos para el nombre del servidor:

- Nombre de servidor
- Nombre de servidor/DirectoryName

El formato de **Nombre de servidor/DirectoryName** es el formato preferido para este atributo porque proporciona una respuesta más rápida cuando el servidor de Domino en contacto con el conector.

El archivo de ID proporcionado se almacena en la base de datos de configuración del servicio de sincronización.

Para **Importar Delta** tiene estas opciones:

- **Ninguno**. El conector no cualquier importaciones delta.
- **Agregar o actualizar**. La importación de conector sirve delta agregar y las operaciones de actualización. Para eliminar, se requiere una operación de **Importación completa** . Interoperabilidad de .net usa esta operación.
- **Agregar, actualizar o eliminar**. La importación de conector sirve delta agregar, actualizar y eliminar operaciones. Esta operación usa las interfaces de C++ nativas.

En **Opciones de esquema** tiene las siguientes opciones:

- **Esquema de predeterminado**. El conector detecta el esquema desde el servidor de Domino. Esta es la opción predeterminada.
- **Esquema DSML**. Solo se utiliza si el servidor de Domino no expone el esquema. A continuación, puede crear un archivo DSML con el esquema e importarlo en su lugar. Para obtener más información sobre DSML, vea [OASIS](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=dsml).

Al hacer clic en siguiente, se comprueban los parámetros de configuración de ID de usuario y contraseña.

### <a name="global-parameters"></a>Parámetros globales
En la página parámetros globales, configurar la zona horaria y la importación y exportación opción de operación.  
![Parámetros globales](./media/active-directory-aadconnectsync-connector-domino/globalparameters.png)

El parámetro de **zona horaria del servidor Domino** define la ubicación de su servidor de Domino.

Esta opción de configuración es necesaria para admitir operaciones de **importación delta** porque permite la sincronización servicio determinar cambios entre las dos últimos importaciones.

#### <a name="import-settings-method"></a>Opciones de importación, método
**Realizar importación completa por** tiene estas opciones:

- Búsqueda
- Vista (recomendada)

**Búsqueda** usa indización en Domino, pero es frecuente que los índices no se actualizan en tiempo real y los datos devueltos desde el servidor no siempre están correctos. Para un sistema con muchos cambios, esta opción normalmente no funciona bien y proporciona falso elimina en algunos casos. Sin embargo, la **búsqueda** es más rápido que **la vista**.

**Vista** es la opción recomendada, ya que proporciona el estado correcto de datos. Es ligeramente más lento que la búsqueda.

#### <a name="creation-of-virtual-contact-objects"></a>Creación de objetos de contacto Virtual
La **Habilitar la creación de \_objeto contacto** tiene estas opciones:

- Ninguno
- Valores de referencia no
- Valores de referencia y no de referencia

En Domino, atributos de referencia pueden contener varios formatos diferentes para hacer referencia a otros objetos. Para poder representar variaciones, implementa el conector \_póngase en contacto con objetos, también conocido como **Contactos Virtual** (VC). Estos objetos se crean para que pueden unirse a objetos existentes de MV o proyectados como objetos nuevos. De esta forma, se conservan las referencias de atributo.

Al habilitar esta configuración y si el contenido de un atributo de referencia no es un formato DN, un \_se crea el objeto de contacto. Por ejemplo, un atributo de miembro de un grupo puede contener direcciones SMTP. También es posible tener nombre corto y otros atributos presentes en los atributos de referencia. En este escenario, seleccione **Valores sin referencia**. Esta configuración es el valor más común para las implementaciones de Domino.

Cuando Lotus Domino está configurado para tener libretas de direcciones independiente con diferentes nombres completos que representa el mismo objeto, es posible crear \_objetos de contacto para todos los valores de referencia que se encuentran en una libreta de direcciones. En este escenario, seleccione la opción de **referencia y valores sin referencia** .

Si tiene varios valores en el atributo **FullName** en Domino, que también desee habilitar la creación de contactos Virtual para que se pueden resolver referencias. Por ejemplo, este atributo puede tener varios valores después de un matrimonio o divorcio. Seleccione la casilla de verificación **Activar... FullName tiene varios valores** para este escenario.

Uniendo los atributos correctos, la \_objetos de contacto se podrían Unidos al objeto MV.

Estos objetos tienen VC =\_Agregar contacto a su nombre completo.

#### <a name="import-settings-conflict-object"></a>Configuración de importación, objeto en conflicto
**Excluir el objeto de conflicto**

En una implementación de Domino grande, es posible que varios objetos tienen el mismo DN debido a problemas de replicación. En estos casos, el conector verá dos objetos con diferentes UniversalIDs pero DN mismo. Este conflicto provocará un objeto transitorio creado en el espacio del conector. El conector puede pasar por alto los objetos que se han seleccionado en Domino como víctimas de replicación. Se recomienda mantener esta casilla de verificación seleccionada.

#### <a name="export-settings"></a>Exportar configuración
Si no está seleccionada la opción **Usar AdminP para actualizar referencias** , exportación de atributos de referencia, como miembro, es una llamada directa y no utiliza el proceso de AdminP. Solo puede usar esta opción cuando AdminP no se ha configurado para mantener la integridad referencial.

#### <a name="routing-information"></a>Información de enrutamiento
En Domino, es posible que un atributo de referencia tiene información de enrutamiento incrustada como un sufijo para el nombre completo. Por ejemplo, podría contener el atributo de miembro de un grupo **CN=example/organization@ABC**. El sufijo @ABC es la información de enrutamiento. La información de enrutamiento se usa por Domino para enviar mensajes de correo electrónico al sistema de Domino correcto, que puede ser un sistema en una organización diferente. En el campo información de enrutamiento, puede especificar el enrutamiento de sufijos utilizado dentro de la organización en el ámbito del conector. Si uno de estos valores se encuentra en un atributo de referencia como un sufijo, se quita la información de enrutamiento de la referencia. Si el sufijo enrutamiento en un valor de referencia no coincide con uno de los valores especificados, un \_se crea el objeto de contacto. Estos \_objetos de contacto se crean con ** RO=@ ** insertado en el nombre completo. Para estos \_objetos de contacto también se agregan los siguientes atributos para permitir que unirse a un objeto real, si es necesario: \_routingName, \_nombre de contacto, \_displayName y UniversalID.

#### <a name="additional-address-books"></a>Libretas de direcciones adicionales
Si no tiene **asistencia de directorio** instalado, que proporciona el nombre de libretas de direcciones secundario, puede introducir manualmente estas libretas de direcciones.

#### <a name="multivalued-transformation"></a>Transformación multivalor
Muchos atributos de Lotus Domino son multivalor. Los atributos de metaverse correspondientes son normalmente solo con valores. Al configurar la importación y la opción de la operación de exportación, habilite el conector para ayudarle con la traducción necesaria de los atributos afectados.

**Exportar**  
La opción de la operación de exportación admite dos modos:

- Elemento de datos anexados
- Reemplazar el elemento

**Reemplazar elemento** : cuando se selecciona esta opción, el conector siempre quitar los valores actuales del atributo en Domino y reemplácelos con los valores proporcionados. La proporcionada con valores puede ser un solo valor o varios valores.

Ejemplo: El atributo de Asistente de un objeto de persona tiene los siguientes valores:

- CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Si un asistente nuevo denominado **David Alexander** está asignado a este objeto de la persona, el resultado es:

- CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf

**Elemento de datos anexados** : cuando se selecciona esta opción, el conector conserva los valores existentes en el atributo de Domino e insertar nuevos valores en la parte superior de la lista de datos.

Ejemplo: El atributo de Asistente de un objeto de persona tiene los siguientes valores:

- CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

Si un asistente nuevo denominado **David Alexander** está asignado a este objeto de la persona, el resultado es:

- CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
- CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

**Importar**  
La opción de la operación de importación admite dos modos:

- Predeterminado
- Multivalor valor único

**Predeterminado** : cuando se selecciona la opción de forma predeterminada, todos los valores de todos los atributos se importan.

**Multivalued valor único** : cuando se selecciona esta opción, un atributo multivalor se convierte en un atributo de valor único. Si hay más de un valor, se utiliza el valor en la parte superior (este valor es normalmente también la más reciente).

Ejemplo: El atributo de Asistente de un objeto de persona tiene los siguientes valores:

- CN = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
- CN = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- CN = John Smith/OU=Contoso/O=Americas,NAB=names.nsf

La actualización más reciente para este atributo es **David Alexander**. Dado que la opción de la operación de importación se establece como Multivalued valor único, conector importa solo **David Alexander** en el espacio del conector.

No se aplica la lógica para convertir los atributos de valores múltiples en atributos de un solo valor para el atributo de miembro de grupo y para el atributo fullname de la persona.

También es posible configurar importar y exportar reglas de transformación de atributos multivalor por atributo, como una excepción a la regla global. Para configurar esta opción, escriba [tipoobjeto]. [attributename] en los cuadros de texto de **importar la lista de exclusión de atributos** y **exportar la lista de exclusión de atributos** . Por ejemplo, si escribe Person.Assistant y se establece el indicador global para importar todos los valores, solo el primer valor se importa para el asistente.

#### <a name="certifiers"></a>Certificadores
Se muestran todas las unidades de organización/organizativo por el conector. Para poder exportar los objetos de la persona a la libreta de direcciones principal, se requiere un certificador con su contraseña.

Si los certificadores todos tienen la misma contraseña, se puede usar la **contraseña para todos los Certifers** . Escriba la contraseña aquí y podrá especificar solo el archivo certificador.

Si importa únicamente, no tiene que especificar cualquier los certificadores.

### <a name="configure-provisioning-hierarchy"></a>Configurar la jerarquía de aprovisionamiento
Al configurar el conector de Lotus Domino, omitir esta página de diálogo. El conector de Lotus Domino no admite la jerarquía de aprovisionamiento.  
![Jerarquía de aprovisionamiento](./media/active-directory-aadconnectsync-connector-domino/provisioninghierarchy.png)

### <a name="configure-partitions-and-hierarchies"></a>Configurar las particiones y jerarquías
Al configurar particiones y jerarquías, debe seleccionar la libreta de direcciones principal denominada NAB=names.nsf. Además de la libreta de direcciones principal, puede seleccionar libretas de direcciones secundario si existen.  
![Particiones](./media/active-directory-aadconnectsync-connector-domino/partitions.png)

### <a name="select-attributes"></a>Seleccionar atributos
Al configurar sus atributos, debe seleccionar todos los atributos que llevan el prefijo ** \_MMS\_**. Estos atributos son necesarios al aprovisionar objetos nuevos a Lotus Domino

![Atributos](./media/active-directory-aadconnectsync-connector-domino/attributes.png)

## <a name="object-lifecycle-management"></a>Administración del ciclo de vida de objeto
Esta sección proporciona información general sobre los distintos objetos de Domino.

### <a name="person-objects"></a>Objetos de persona
El objeto de persona representa los usuarios de la organización y las unidades de organización. Además de los atributos de forma predeterminada, el Administrador de Domino puede agregar atributos personalizados a un objeto de la persona. Como mínimo, un objeto de persona debe incluir todos los atributos obligatorios. Para obtener una lista completa de atributos obligatorios, vea [Propiedades de Lotus Notes](#lotus-notes-properties). Para registrar un objeto de la persona, se deben cumplir los siguientes requisitos previos:

- La libreta de direcciones (names.nsf) debe se han definido y debe ser la libreta de direcciones principal.
- Debe tener el certificador O/OU Id y la contraseña para registrar un usuario concreto de la organización o unidad organizativa.
- Debe establecer un conjunto de propiedades de Lotus Notes para un objeto de persona específico. Estas propiedades se utilizan para aprovisionar el objeto de la persona. Para obtener más detalles, consulte la sección denominada [Propiedades de Lotus Notes](#lotus-notes-properties) más adelante en este documento.
- La contraseña HTTP inicial de una persona es un atributo y un conjunto durante el aprovisionamiento.
- El objeto de persona debe ser uno de los siguientes tres tipos compatibles:
    1. Usuario normal que tiene un archivo de correo y un archivo de id de usuario
    2. Movilidad de usuario (un usuario Normal que incluye todos los archivos de base de datos móviles)
    3. Contactos (usuario con ningún archivo id)

Personas (excepto los contactos) más se pueden agrupar en los usuarios de nosotros e internacional definida por el valor de la \_MMS\_IDRegType propiedad. Estas personas, use el cliente de Notes para tener acceso a los servidores de Lotus Domino, tener un identificador de notas y un documento de la persona. Si están usando el correo de notas, también tienen un archivo de correo. El usuario debe estar registrado para estar activo. Para obtener más información, consulte:

- [Configurar los usuarios de notas](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_SETTING_UP_NOTES_USERS.html)
- [Registro de usuario](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_REGISTERING_USERS.html)
- [Administrar usuarios](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_USERS_5151.html)
- [Cambiar el nombre de los usuarios](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_USER_AUTOMATICALLY.html)

Todas estas operaciones se realizan en Lotus Domino y se importarán en el servicio de sincronización.

### <a name="resources-and-rooms"></a>Salas y recursos
Un recurso es otro tipo de base de datos de Lotus Domino. Recursos pueden ser salas de conferencias con distintos tipos de equipos, como proyectores. Hay subtipos de recursos admitidos por el conector de Lotus Domino que se ha definido el atributo de tipo de recurso:

Tipo de recurso | Atributo de tipo de recurso
--- | ---
Sala | 1
Recurso (otros) | 2
Reunión en línea | 3

Para que el tipo de objeto de recursos para que funcione, es necesario lo siguiente:

- Base de datos de reserva de recursos ya debe existir en el servidor de Domino conectado
- El sitio ya está definido para el recurso

La base de datos de reserva de recursos contiene tres tipos de documentos:

- Perfil de sitio
- Recursos
- Reserva

Para obtener más detalles sobre la configuración de la base de datos de reserva de recursos, consulte [configuración de la base de datos de reserva de recursos](https://www-01.ibm.com/support/knowledgecenter/SSKTMJ_8.0.1/com.ibm.help.domino.admin.doc/DOC/H_SETTING_UP_THE_RESOURCE_RESERVATIONS_DATABASE.html).

**Crear, actualizar y eliminar recursos**  
Se realizan las operaciones de crear, actualizar y eliminar el conector de Lotus Domino en la base de datos de reserva de recursos. Recursos se crean como documentos en Names.nsf (es decir, la libreta de direcciones principal). Para obtener más detalles sobre la edición y eliminación de recursos, vea [modificar y eliminar documentos de recursos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_EDITING_AND_DELETING_RESOURCE_DOCUMENTS.html).

**Importar y exportar operación para recursos**  
Los recursos se pueden importar a y exportados desde el servicio de sincronización, como cualquier otro tipo de objeto. Seleccione el tipo de objeto como recurso durante la configuración. Para la operación de exportación correcta, debería tener información de tipo de recurso, la base de datos de conferencia y el nombre del sitio.

### <a name="mail-in-databases"></a>Bases de datos de correo
Una base de datos de correo es una base de datos está diseñado para recibir correos electrónicos. Es un buzón de Lotus Domino que no está asociado con cualquier cuenta de usuario de Lotus Domino específico (es decir, no tiene su propio archivo de ID y contraseña). Una base de datos de correo tiene un Id. único ("nombre corto") asociado y su propia dirección de correo electrónico.

Si es necesario para un buzón independiente con su propia dirección de correo electrónico que se puede compartir entre diferentes usuarios (por ejemplo, group@contoso.com), se crea una base de datos de correo. El acceso a este buzón se controla a través de su lista de Control de acceso (ACL), que contiene los nombres de los usuarios de notas que tiene permiso para abrir el buzón.

Para obtener una lista de los atributos necesarios, consulte la sección denominada [Atributos obligatorios](#mandatory-attributes) más adelante en este artículo.

Cuando una base de datos está diseñado para recibir un mensaje de correo, se crea un documento de correo en la base de datos de Lotus Domino. Este documento debe existir en el directorio de Domino de todos los servidores que almacena una copia de la base de datos. Para obtener información detallada sobre la creación de un documento de correo en la base de datos, vea [crear un documento de base de datos de correo](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_A_MAILIN_DATABASE_DOCUMENT_FOR_A_NEW_DATABASE_OVERVIEW.html).

Antes de crear una base de datos de correo, la base de datos ya debe existir (debe hayan sido creadas por el Administrador de Lotus) en el servidor de Domino.

### <a name="group-management"></a>Administración de grupo
Puede obtener una descripción detallada de la administración de grupos de Lotus Domino de los recursos siguientes:

- [Uso de grupos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_USING_GROUPS_OVER.html)
- [Creación de un grupo](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS_MIDTOPIC_55038956829238418.html)
- [Creación y modificación de grupos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS.html)
- [Administración de grupos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_GROUPS_1804.html)
- [Cambiar el nombre de un grupo](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_GROUP_STEPS.html)

### <a name="password-management"></a>Administración de contraseñas
Para un usuario de Lotus Domino registrado, hay dos tipos de contraseñas:

1. Contraseña de usuario (almacenado en el archivo User.id)
2. Internet o contraseña HTTP

El conector de Lotus Domino admite únicamente operaciones con contraseña HTTP.

Para realizar la administración de contraseñas, debe habilitar la administración de contraseñas para el conector en el Diseñador de agente de administración. Para habilitar la administración de contraseñas, seleccione **Habilitar la administración de la contraseña** en la página de diálogo **Configurar las extensiones** .  
![Configurar las extensiones](./media/active-directory-aadconnectsync-connector-domino/configureextensions.png)

Soporte de conector de Lotus Domino las siguientes operaciones en contraseña de Internet:

- Establecer contraseña: Establecer contraseña establece una nueva contraseña HTTP de Internet en el usuario de Domino. De forma predeterminada, la cuenta también es desbloqueada. El indicador de desbloqueo se expone en la interfaz WMI del motor de sincronización.
- Cambiar contraseña: En este escenario, un usuario que desee cambiar la contraseña o se le pide que cambiar contraseña tras un período especificado. Para realizar esta operación colocar, (la versión anterior y la nueva contraseña) son obligatorios. Una vez cambiada, se actualiza la nueva contraseña en Lotus Domino.

Para obtener más información, consulte:

- [Con la característica de bloqueo de Internet](http://www.ibm.com/developerworks/lotus/library/domino8-lockout/)
- [Administrar contraseñas de Internet](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_NOTES_AND_INTERNET_PASSWORD_SYNCHRONIZATION_7570_OVER.html)

## <a name="reference-information"></a>Información de referencia
Esta sección se muestra como descripciones de atributos y los requisitos de atributo para el conector de Lotus Domino.

### <a name="lotus-notes-properties"></a>Propiedades de Lotus Notes
Cuando se aprovisiona objetos de la persona en el directorio de Lotus Domino, los objetos deben tener un conjunto concreto de propiedades con valores específicos rellenado. Estos valores sólo son necesarios para crear operaciones.

En la tabla siguiente se enumera estas propiedades y proporciona una descripción de ellas.

(Propiedad) | Descripción
--- | ---
\_MMS_AltFullName | Nombre completo alternativo del usuario.
\_MMS_AltFullNameLanguage | El idioma que se usará para especificar el nombre completo alternativo del usuario.
\_MMS_CertDaysToExpire | El número de días desde la fecha actual del certificado expira. Si no se especifica la fecha predeterminada es dos años desde la fecha actual.
\_MMS_Certifier | Propiedad que contiene el nombre de una jerarquía organizativa del certificador. Por ejemplo: OU = OrganizationUnit, O = Org, C = país.
\_MMS_IDPath | Si la propiedad está vacía, ningún archivo de identificación de usuario se crea localmente en el servidor de sincronización. Si la propiedad contiene un nombre de archivo, se crea un archivo de ID de usuario en la carpeta madata. La propiedad también puede contener una ruta de acceso completa.
\_MMS_IDRegType | Se pueden clasificar personas como usuarios internacionales, nos usuarios y contactos. La siguiente tabla enumeran los valores posibles: <li>0 - contacto</li><li>1 - usuario Estados Unidos</li><li>2 - usuario internacional</li>
\_MMS_IDStoreType | Propiedad requerido para Estados Unidos y usuarios internacionales. La propiedad contiene un valor entero que especifica si la identificación de usuario se almacena como datos adjuntos en la libreta de direcciones de notas o en el archivo de correo de la persona. Si el archivo de identificador de usuario es un archivo adjunto en la libreta de direcciones, opcionalmente, se puede crear como un archivo con \_MMS_IDPath. <li>Vaciar - archivo de Id. de almacenamiento en depósito de ID, ningún archivo de identificación (utilizado para contactos).</li><li> 1 - datos adjuntos en la libreta de direcciones de notas. La \_MMS_Password propiedad debe establecerse para archivos de identificación de usuario que se encuentran los datos adjuntos</li><li>2 - almacenar Id. de archivo de correo de la persona. La \_MMS_UseAdminP debe estar establecida en false para permitir que el archivo de correo a crearse durante el registro de la persona. La \_MMS_Password propiedad debe establecerse para archivos de identificación de usuario.</li>
\_MMS_MailQuotaSizeLimit | El número de megabytes que se permiten en la base de datos del archivo de correo electrónico.
\_MMS_MailQuotaWarningThreshold | El número de megabytes que se permiten en la base de datos del archivo de correo electrónico antes de que se emite una advertencia.
\_MMS_MailTemplateName | El archivo de plantilla de correo electrónico que se usa para crear el archivo de correo electrónico del usuario. Si se especifica una plantilla, se crea el archivo de correo con la plantilla especificada. Si no se especifica ninguna plantilla, el archivo de plantilla predeterminado se usa para crear el archivo.
\_MMS_OU | Propiedad opcional que es el nombre de la OU en el certificador. Esta propiedad debe estar vacía para los contactos.
\_MMS_Password | Propiedad requerido para los usuarios. La propiedad contiene la contraseña para el archivo de identificación del objeto.
\_MMS_UseAdminP | Propiedad debe estar establecida en true si debe crear el archivo de correo por el proceso de AdminP en el servidor Domino (asincrónico para el proceso de exportación). Si la propiedad está establecida en false, el archivo de correo se crea con el usuario de Domino (sincrónico en el proceso de exportación).

Para un usuario con un archivo de identificación asociado, el \_MMS_Password propiedad debe contener un valor. Para el acceso de correo electrónico a través del cliente de Lotus Notes, el servidor de correo y MailFile propiedades de un usuario deben contener un valor.

Para obtener acceso a correo electrónico a través de un explorador Web, las siguientes propiedades deben contener valores:

- MailFile - propiedad requerido que contiene la ruta de acceso en el servidor de Lotus Domino donde está almacenado el archivo de correo.
- Servidor de correo - propiedad requerido que contiene el nombre del servidor de Lotus Domino. Este valor es el nombre para usar al crear el archivo de correo de Lotus en el servidor de Domino.
- HTTPPassword - propiedad opcional que contiene la contraseña de acceso Web para el objeto.

Para obtener acceso al servidor de Domino sin la capacidad de correo, la propiedad HTTPPassword debe contener un valor. La propiedad MailFile y la propiedad de servidor de correo electrónico pueden estar vacías.

Con \_MMS_ IDStoreType = 2 (Id. de la tienda en el archivo de correo), se establece la propiedad MailSystem de NotesRegistrationclass a REG_MAILSYSTEM_INOTES (3).

### <a name="mandatory-attributes"></a>Atributos obligatorios
El conector de Lotus Domino principalmente es compatible con estos tipos de objetos (tipos de documento):

- Grupo
- Base de datos de correo
- Persona
- Contacto (persona con ningún certificador)
- Recursos

En esta sección se enumera los atributos que son obligatorios para cada objeto admitido exportar a un servidor de Domino.

Tipo de objeto | Atributos obligatorios
--- | ---
Grupo | <li>Nombre de lista</li>
Principales de la base de datos | <li>FullName</li><li>MailFile</li><li>Servidor de correo</li><li>MailDomain</li>
Persona | <li>Apellido</li><li>MailFile</li><li>Nombre corto</li><li>\_MMS_Password</li><li>\_MMS_IDStoreType</li><li>\_MMS_Certifier</li><li>\_MMS_IDRegType</li><li>\_MMS_UseAdminP</li>
Contacto (persona con ningún certificador) | <li>\_MMS_IDRegType</li>
Recursos | <li>FullName</li><li>Tipo de recurso</li><li>ConfDB</li><li>CapacidadDeRecursos</li><li>Sitio</li><li>DisplayName</li><li>MailFile</li><li>Servidor de correo</li><li>MailDomain</li>

## <a name="common-issues-and-questions"></a>Preguntas y problemas comunes

### <a name="schema-detection-does-not-work"></a>Detección de esquema no funciona
Para poder detectar el esquema, es necesario que el archivo schema.nsf está presente en el servidor de Domino. Este archivo solo aparece si LDAP está instalado en el servidor. Si el esquema no es detectable, compruebe lo siguiente:

- La schema.nsf archivo está presente en la carpeta raíz del servidor Domino
- El usuario tiene permisos para ver el archivo schema.nsf.
- Forzar el reinicio del servidor LDAP. Abra la **Consola de Lotus Domino** y use el comando de **Saber LDAP ReloadSchema** a cargar el esquema.

### <a name="not-all-secondary-address-books-are-visible"></a>No todas las libretas de direcciones secundario están visibles
El conector de Domino depende de la característica **Asistencia de directorios** para poder encontrar las libretas de direcciones secundario. Si faltan las libretas de direcciones secundario, compruebe si [Asistencia de directorio](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_DIRECTORY_ASSISTANCE.html) se ha habilitado y configurado en el servidor de Domino.

### <a name="custom-attributes-in-domino"></a>Atributos personalizados en Domino
Hay varias formas de Domino para ampliar el esquema para que aparezca como un atributo personalizado consumible por el conector.

**Método 1: Ampliar el esquema de Lotus Domino**

1. Crear una copia de la plantilla de directorio de Domino {PUBNAMES. NTF} siguiente [estos pasos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (no debe personalizar el directorio de IBM Lotus Domino predeterminado plantilla):
2. Abra la plantilla del directorio de copia de Domino {CONTOSO. Plantilla NTF} que se creó en el Diseñador de Domino y siga estos pasos:
    - Haga clic en elementos compartidos y expandir subformularios
    - Haga doble clic en el subformulario de InheritableSchema ${ObjectName} (donde {ObjectName} es el nombre de la clase de objeto estructural predeterminado, por ejemplo: persona).
    - Nombre del atributo que desea agregar en esquema {MyPersonAtrribute} y correspondiente a ese atributo. Crear un campo, seleccione el menú **crear** y, a continuación, seleccione el **campo** de menú.
    - En el campo agregado, establezca sus propiedades seleccionando su tipo, estilo, tamaño, fuentes y otros parámetros relacionados en la ventana de propiedades de campo.
    - Mantener el atributo valor predeterminado mismo como el nombre del atributo (por ejemplo, si el nombre del atributo es MyPersonAttribute, mantener el valor predeterminado con el mismo nombre).
    - Guarde el subformulario InheritableSchema ${ObjectName} con valores actualizados.
3. Reemplazar la plantilla de directorio de Domino {PUBNAMES. NTF} con la nueva plantilla personalizada {CONTOSO. NTF} siguiente [estos pasos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
4. Cierre el Administrador de Domino y abra la consola de Domino para reiniciar el servicio LDAP y volver a cargar el esquema LDAP:
    - En la consola de Domino, insertar el comando en el texto del **Comando de Domino** archivado para reiniciar el servicio LDAP - [Reiniciar tarea LDAP]( http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
    - Para volver a cargar LDAP esquema Utilice el comando de saber LDAP - saber LDAP ReloadSchema
5. Abrir Administrador de Domino y seleccione la pestaña de personas y grupos para ver el atributo agregado se refleja en domino agregan persona.
6. Abra Schema.nsf desde la pestaña **archivos** y vea atributo agregado se refleja en la clase de objeto dominoPerson LDAP.

**Método 2: Crear un auxClass con atributo personalizado y asociar a la clase de objeto**

1. Crear una copia de la plantilla de directorio de Domino {PUBNAMES. NTF} siguiente [estos pasos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (nunca personalizar el directorio de IBM Lotus Domino predeterminado plantilla):
2. Abra la plantilla del directorio de copia de Domino {CONTOSO. Plantilla NTF} que se creó en el Diseñador de Domino.
3. En el panel izquierdo, seleccione compartir código y subformularios.
4. Haga clic en nuevo subformulario
5. Siga este procedimiento para especificar las propiedades para el nuevo subformulario:
    - Con el nuevo subformulario abierto, seleccione Propiedades del subformulario de diseño:
    - Junto a la propiedad nombre, escriba un nombre para la clase de objeto auxiliar: por ejemplo, TestSubform.
    - Mantener la propiedad de opciones "Incluir en el subformulario insertar... diálogo" seleccionado
    - Anule la selección del opciones propiedad "representación del paso a través HTML en notas."
    - Deje las demás propiedades de la misma y a continuación, cierre el cuadro Propiedades de subformulario.
    - Guarde y cierre el nuevo subformulario.
6. Siga este procedimiento para agregar un campo para definir la clase de objeto auxiliar:
    - Abra el subformulario que creó.
    - Elija crear: campo.
    - Junto al nombre en la pestaña conceptos básicos del cuadro de diálogo campo, especifique cualquier nombre, por ejemplo: {MyPersonTestAttribute}.
    - En el campo agregado, establezca sus propiedades seleccionando su tipo, estilo, tamaño, fuente y propiedades relacionadas.
    - Mantener el atributo valor predeterminado mismo como el nombre del atributo (por ejemplo, si el nombre del atributo es MyPersonTestAttribute, mantener el valor predeterminado con el mismo nombre).
    - Guarde el subformulario con valores actualizados y haga lo siguiente:
        - En el panel izquierdo, seleccione código compartido y, a continuación, subformularios
        - Seleccione el nuevo subformulario y elija Diseño - propiedades de diseño.
        - Haga clic en la tercera ficha de la izquierda y seleccione **cambiar propagar prohibir este diseño**.
7. Abra el subformulario ${ObjectName} ExtensibleSchema (donde {ObjectName} es el nombre de la clase de objeto estructural predeterminado, por ejemplo: persona).
8. Insertar recurso, seleccione el subformulario (que ha creado, por ejemplo: TestSubform) y guarde el subformulario ExtensibleSchema ${ObjectName}.
9. Reemplazar la plantilla de directorio de Domino {PUBNAMES. NTF} con la nueva plantilla personalizada {CONTOSO. NTF} siguiente [estos pasos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
10. Cierre el Administrador de Domino y abra la consola de Domino para reiniciar el servicio LDAP y volver a cargar el esquema LDAP:
    - En la consola de Domino, insertar el comando en el texto del **Comando de Domino** archivado para reiniciar el servicio LDAP - [Reiniciar tarea LDAP](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
    - Para volver a cargar LDAP esquema Utilice el comando de saber LDAP **Saber ReloadSchema de LDAP**.
11. Abrir el Administrador de Domino y seleccione la pestaña personas y grupos para ver el atributo agregado se refleja en domino Agregar persona (en otras personas de la pestaña).
12. Abra Schema.nsf desde la pestaña **archivos** y vea atributo agregado se refleja en la clase de objeto TestSubform LDAP auxiliar.

**Método 3: Agregar el atributo personalizado a la clase ExtensibleObject**

1. Abrir archivo de {Schema.nsf} colocado en el directorio raíz
2. Seleccione las clases de objeto LDAP desde el menú de la izquierda en **Todos los documentos de esquema** y haga clic en el botón **Agregar objeto clase** :
3. Proporcione el nombre de LDAP en el formulario de {zzzExtensibleSchema} (donde zzz es el nombre de la clase de objeto estructural predeterminado, por ejemplo persona). Por ejemplo, para extender el esquema de persona a la clase de objeto, proporcione el nombre LDAP {PersonExtensibleSchema}.
4. Proporcione el nombre de clase de objeto Superior, para el que desea ampliar el esquema. Por ejemplo, para extender el esquema de clase de objeto de persona, proporcione el nombre de clase de objeto Superior {dominoPerson}:
5. Proporcionar un OID válido correspondiente a la clase de objeto.
6. Seleccione atributos extendida o personalizados en los campos obligatorios o tipos de atributo opcional según el requisito:
7. Después de agregar los atributos necesarios para el ExtensibleObjectClass, haga clic en **Guardar y cerrar**.
8. Se crea un ExtensibleObjectClass de clase de objeto predeterminado respectivos con atributos ampliados.

## <a name="troubleshooting"></a>Solución de problemas

-   Para obtener información acerca de cómo habilitar el registro solucionar el conector, vea [cómo habilitar el seguimiento de ETW para los conectores](http://go.microsoft.com/fwlink/?LinkId=335731).
