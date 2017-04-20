<properties
   pageTitle="Conector LDAP genérico | Microsoft Azure"
   description="Este artículo describe cómo configurar el conector de LDAP genérico de Microsoft."
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

# <a name="generic-ldap-connector-technical-reference"></a>Referencia técnica de conector LDAP genérico
Este artículo describe el conector LDAP genérico. El artículo se aplica a los siguientes productos:

- Administrador de identidad de Microsoft 2016 (MIM2016)
- Administrador de identidad de Forefront 2010 R2 (FIM2010R2)
    -   Debe usar revisión 4.1.3671.0 o posterior [KB3092178](https://support.microsoft.com/kb/3092178).

Para MIM2016 y FIM2010R2, el conector está disponible como descarga desde el [Centro de descarga de Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

Cuando se hace referencia a las solicitudes de cambio de IETF, este documento está utilizando el formato (RFC [número RFC] [de sección en el documento RFC] o), por ejemplo (RFC 4512/4.3).
Puede encontrar más información en http://tools.ietf.org/html/rfc4500 (debe reemplazar 4500 con el número correcto de RFC).

## <a name="overview-of-the-generic-ldap-connector"></a>Información general sobre el conector LDAP genérico
El conector LDAP genérico permite integrar el servicio de sincronización con el servidor LDAP v3.

Determinadas operaciones y elementos del esquema, como los necesarios para realizar la importación delta, no están especificados en las solicitudes de cambio de IETF. Para estas operaciones solo directorios LDAP que se especifica explícitamente son compatibles.

Desde una perspectiva de alto nivel, las siguientes características son compatibles con la versión actual del conector:

Característica | Soporte técnico
--- | --- |
Origen de datos conectado | El conector es compatible con todos los servidores de v3 LDAP (RFC 4510 compatible). Se ha probado con los siguientes elementos: <li>Servicios de directorio ligero de Microsoft Active Directory (AD LDS)</li><li>Catálogo Global de Microsoft Active Directory (AD catálogo global)</li><li>Servidor de directorio 389</li><li>Servidor de directorio de Apache</li><li>IBM Tivoli DS</li><li>Directorio de Isode</li><li>NetIQ eDirectory</li><li>Novell eDirectory</li><li>Abrir DJ</li><li>Abrir DS</li><li>LDAP abierto (openldap.org)</li><li>Oracle (anteriormente DOM) directorio servidor Enterprise Edition</li><li>Servidor de directorio Virtual RadiantOne (VDS)</li><li>Servidor de directorio Sun One</li>**Directorios notables incompatibles:** <li>Microsoft Active Directory servicios de dominio (AD DS) [Utilice el conector de Active Directory integrado en su lugar]</li><li>Directorio de Internet de Oracle (OID)</li>
Escenarios   | <li>Administración del ciclo de vida de objeto</li><li>Administración de grupo</li><li>Administración de contraseñas</li>
Operaciones |Se admiten las siguientes operaciones en todos los directorios LDAP: <li>Importación completa</li><li>Exportar</li>Solo se admiten las siguientes operaciones en directorios especificados:<li>Importación de Delta</li><li>Establecer contraseña, cambiar contraseña</li>
Esquema | <li>Esquema se detecta en el esquema LDAP (RFC3673 y RFC4512/4.2)</li><li>Admite clases estructurales, aux clases y clase de objeto de extensibleObject (RFC4512/4.3)</li>

### <a name="delta-import-and-password-management-support"></a>Soporte de administración de importación y la contraseña de Delta
Directorios compatibles para la importación de Delta y administración de contraseña:

- Servicios de directorio ligero de Microsoft Active Directory (AD LDS)
    - Es compatible con todas las operaciones de importación de delta
    - Es compatible con establecer contraseña
- Catálogo Global de Microsoft Active Directory (AD catálogo global)
    - Es compatible con todas las operaciones de importación de delta
    - Es compatible con establecer contraseña
- Servidor de directorio 389
    - Es compatible con todas las operaciones de importación de delta
    - Permite establece una contraseña y cambiar contraseña
- Servidor de directorio de Apache
    - No permite la importación de delta dado que este directorio no tiene un registro de cambios persistentes
    - Es compatible con establecer contraseña
- IBM Tivoli DS
    - Es compatible con todas las operaciones de importación de delta
    - Permite establece una contraseña y cambiar contraseña
- Directorio de Isode
    - Es compatible con todas las operaciones de importación de delta
    - Permite establece una contraseña y cambiar contraseña
- Novell eDirectory y NetIQ eDirectory
    - Es compatible con las operaciones de agregar, actualizar y cambiar el nombre para la importación de delta
    - No admite las operaciones de eliminación para la importación de delta
    - Permite establece una contraseña y cambiar contraseña
- Abrir DJ
    - Es compatible con todas las operaciones de importación de delta
    - Permite establece una contraseña y cambiar contraseña
- Abrir DS
    - Es compatible con todas las operaciones de importación de delta
    - Permite establece una contraseña y cambiar contraseña
- LDAP abierto (openldap.org)
    - Es compatible con todas las operaciones de importación de delta
    - Es compatible con establecer contraseña
    - No admite cambiar contraseña
- Oracle (anteriormente DOM) directorio servidor Enterprise Edition
    - Es compatible con todas las operaciones de importación de delta
    - Permite establece una contraseña y cambiar contraseña
- Servidor de directorio Virtual RadiantOne (VDS)
    - Debe estar usando la versión 7.1.1 o superior
    - Es compatible con todas las operaciones de importación de delta
    - Permite establece una contraseña y cambiar contraseña
-  Servidor de directorio Sun One
    - Es compatible con todas las operaciones de importación de delta
    - Permite establece una contraseña y cambiar contraseña

### <a name="prerequisites"></a>Requisitos previos
Antes de utilizar el conector, asegúrese de que tiene los siguientes en el servidor de sincronización:

- 4.5.2 de Microsoft .NET Framework o versiones posteriores

### <a name="detecting-the-ldap-server"></a>Detectar el servidor LDAP
El conector se basa en varias técnicas para detectar e identificar el servidor LDAP. El conector utiliza el DSE raíz, versión y nombre del proveedor y, a continuación, inspecciona el esquema para buscar objetos únicos y atributos que sabe que existe en determinados servidores LDAP. Estos datos, si se encuentra, se utiliza para rellenar previamente las opciones de configuración del conector.

### <a name="connected-data-source-permissions"></a>Permisos de origen de datos conectados
Para realizar la importación y exportación operaciones en los objetos en el directorio conectado, la cuenta del conector debe tener permisos suficientes. El conector debe escribir permisos para poder exportar y permisos de lectura para que pueda importar. Configuración de permisos se realiza dentro de la experiencia de administración del propio directorio de destino.

### <a name="ports-and-protocols"></a>Puertos y protocolos
El conector utiliza el número de puerto especificado en la configuración, cuyo valor predeterminado es 389 para LDAP y 636 para LDAPS.

Para LDAPS, debe usar SSL 3.0 o TLS. SSL 2.0 no es compatible y no se puede activar.

### <a name="required-controls-and-features"></a>Características y controles necesarios
Las siguientes controles LDAP/características debe estar disponibles en el servidor LDAP para el conector funcione correctamente:  
`1.3.6.1.4.1.4203.1.5.3`Filtros de verdadero o falso

El filtro de True o False con frecuencia no se indica como compatible con directorios LDAP y puede que aparezca en la **Página Global** en **Obligatorio características no se encuentra**. Se usa para crear filtros **o** en las consultas LDAP, por ejemplo al importar varios tipos de objeto. Si puede importar más de un tipo de objeto, su servidor LDAP es compatible con esta característica.

Si utiliza un directorio donde un identificador único es el delimitador siguiente también debe ser disponible (consulte la sección [Configurar delimitadores](#configure-anchors) más adelante en este artículo para obtener más información):  
`1.3.6.1.4.1.4203.1.5.1`Todos los atributos de funcionamiento

Si el directorio tiene más objetos de que pueden caber en una llamada en el directorio, se recomienda utilizar la paginación. Paginación para que funcione, necesita una de las siguientes opciones:

**Opción 1:**  
`1.2.840.113556.1.4.319`pagedResultsControl

**Opción 2:**  
`2.16.840.1.113730.3.4.9`VLVControl  
`1.2.840.113556.1.4.473`SortControl

Si ambas opciones están habilitadas en la configuración del conector, se utiliza pagedResultsControl.

`1.2.840.113556.1.4.417`ShowDeletedControl

ShowDeletedControl solo se usa con el método de importación de USNChanged delta para poder ver los objetos eliminados.

El conector intenta detectar las opciones presentes en el servidor. Si no se puede detectar las opciones, una advertencia está presente en la página Global en las propiedades del conector. No todos los servidores LDAP presentar de todos los controles y características que admiten e incluso si está presente esta advertencia, el conector funcionen sin problemas.

### <a name="delta-import"></a>Importación de Delta
Importar delta solo está disponible cuando se ha detectado un directorio de soporte técnico. Actualmente se usan los métodos siguientes:

- Accesslog LDAP. Consulte [el registro de http://www.openldap.org/doc/admin24/overlays.html#Access](http://www.openldap.org/doc/admin24/overlays.html#Access Logging)
- Changelog LDAP. Consulte [http://tools.ietf.org/html/draft-good-ldap-changelog-04](http://tools.ietf.org/html/draft-good-ldap-changelog-04)
- Marca de tiempo. Para eDirectory de Novell/NetIQ, el conector utiliza la última fecha y hora para obtener creado y actualizado objetos. Novell/NetIQ eDirectory no proporciona que un equivalente significa para recuperar los objetos eliminados. También se puede usar esta opción si ningún otro método de importación de delta está activo en el servidor LDAP. Esta opción no es posible importar eliminar objetos.
- USNChanged. Vea: [https://msdn.microsoft.com/library/ms677627.aspx](https://msdn.microsoft.com/library/ms677627.aspx)

### <a name="not-supported"></a>No compatible
No se admiten las siguientes características LDAP:

- Referencias de LDAP entre servidores (RFC 4511/4.1.10)

## <a name="create-a-new-connector"></a>Crear un nuevo conector
Para crear un conector de LDAP genérico, en **El servicio de sincronización** , seleccione **Agente de administración** y **crear**. Seleccione el conector **LDAP genérico (Microsoft)** .

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericldap/createconnector.png)

### <a name="connectivity"></a>Conectividad
En la página conectividad, debe especificar la información de Host, puerto y enlace. Según lo que enlace esté seleccionado, adicionales puede proporcionarse información en las secciones siguientes.

![Conectividad](./media/active-directory-aadconnectsync-connector-genericldap/connectivity.png)

- La configuración de tiempo de espera de conexión solo se utiliza para la primera conexión al servidor cuando se detecte el esquema.
- Si el enlace es anónimo, a continuación, ni nombre de usuario / contraseña ni certificado se usan.
- Otros enlaces, escriba la información sobre alguno en nombre de usuario y contraseña o seleccione un certificado.
- Si está utilizando Kerberos para autenticar, también proporcionan el territorio o el dominio del usuario.

El cuadro de texto **alias de atributo** se usa para los atributos definidos en el esquema con sintaxis RFC4522. Estos atributos no se puede detectar durante la detección de esquema y el conector necesita ayuda para identificar los atributos. Por ejemplo siguiente es necesaria para introducirse en el cuadro de alias de atributo para identificar correctamente el atributo userCertificate como un atributo binario:

`userCertificate;binary`

El siguiente es un ejemplo de cómo esta configuración puede tener un aspecto como:

![Conectividad](./media/active-directory-aadconnectsync-connector-genericldap/connectivityattributes.png)

Seleccione la casilla de verificación **incluir atributos operativos en esquema** para incluir también atributos creados por el servidor. Esto incluye atributos como cuando se creó el objeto y la última hora de actualización.

Seleccione **incluir atributos extensibles esquema** si se usan objetos extensibles (RFC4512/4.3) y activar esta opción permite que todos los atributos que se utilizará en todos los objetos. Al seleccionar esta opción hace que el esquema muy grande para a menos que el directorio conectado está usando esta característica se recomienda mantener la opción no está seleccionada.

### <a name="global-parameters"></a>Parámetros globales
En la página parámetros globales, configure el nombre completo para el registro de cambios delta y características adicionales de LDAP. La página se rellena previamente con la información provista por el servidor LDAP.

![Conectividad](./media/active-directory-aadconnectsync-connector-genericldap/globalparameters.png)

En la sección superior muestra información proporcionada por el servidor, como el nombre del servidor. El conector también comprueba que los controles obligatorios están presentes en el DSE raíz. Si estos controles no aparecen, se muestra una advertencia. Algunos directorios LDAP no enumeran todas las características en el DSE raíz y es posible que el conector funciona sin problemas, incluso si está presente una advertencia.

Las casillas de verificación **admite controles** controlan el comportamiento de ciertas operaciones:

- Con la opción Eliminar árbol seleccionado, se elimina una jerarquía con una llamada LDAP. Con la opción Eliminar árbol no está seleccionada, el conector no una eliminación recursiva si es necesario.
- Con los resultados de la páginas seleccionados, el conector no una importación de página con el tamaño especificado en los pasos de ejecución.
- La VLVControl y SortControl es una alternativa a la pagedResultsControl para leer los datos del directorio LDAP.
- Si no se seleccionen las tres opciones (pagedResultsControl, VLVControl y SortControl) el conector importa todos los objetos en una operación que puede producir un error si se trata de un directorio de gran tamaño.
- ShowDeletedControl solo se usa cuando el método de importación Delta es USNChanged.

El registro de cambios DN es el contexto de nomenclatura utilizado por el registro de cambios delta, por ejemplo **cn = changelog**. Este valor debe especificarse para poder realizar la importación de delta.

A continuación se muestra una lista de registro de cambios de forma predeterminada DNs:

Directorio | Registro de cambios delta
--- | ---
Microsoft AD LDS y AD catálogo global | Detectar automáticamente. USNChanged.
Servidor de directorio de Apache | No está disponible.
Directorio 389 | Registro de cambios. Usar el valor predeterminado: **cn = changelog**
IBM Tivoli DS | Registro de cambios. Usar el valor predeterminado: **cn = changelog**
Directorio de Isode | Registro de cambios. Usar el valor predeterminado: **cn = changelog**
Novell/NetIQ eDirectory | No está disponible. Marca de tiempo. Utiliza el conector actualizó por última vez fecha y hora para agregar y actualizar registros.
Abrir DJ/DS | Registro de cambios.  Usar el valor predeterminado: **cn = changelog**
LDAP abierto | Registro de acceso. Usar el valor predeterminado: **cn = accesslog**
Oracle DSEE | Registro de cambios. Usar el valor predeterminado: **cn = changelog**
RadiantOne VDS | Directorio virtual. Depende del directorio conectado a VDS.
Servidor de directorio Sun One | Registro de cambios. Usar el valor predeterminado: **cn = changelog**

El atributo de contraseña es el nombre del atributo que debe utilizar el conector para establecer la contraseña en cambiar la contraseña y las operaciones de establecer contraseña.
Este valor está configurada para **userPassword** por defecto, pero se puede cambiar cuando sea necesario para un sistema LDAP determinado.

En la lista de particiones adicionales, es posible agregar espacios de nombres adicionales detectado automáticamente. Por ejemplo, esta configuración puede usarse si varios servidores forman un clúster lógico, que debe importar todas al mismo tiempo. Igual que Active Directory puede tener varios dominios en un bosque pero todos los dominios comparten un esquema, el mismo puede simular especificando los espacios adicionales en este cuadro. Cada espacio de nombres puede importar desde diferentes servidores y está configurado aún más en la página Configurar particiones y jerarquías. Utilice Ctrl + Entrar para obtener una nueva línea.

### <a name="configure-provisioning-hierarchy"></a>Configurar la jerarquía de aprovisionamiento
Esta página se utiliza para asignar el componente DN, por ejemplo OU, en el tipo de objeto que debe aprovisionar, por ejemplo organizationalUnit.

![Jerarquía de aprovisionamiento](./media/active-directory-aadconnectsync-connector-genericldap/provisioninghierarchy.png)

Mediante la configuración de aprovisionamiento de jerarquía, puede configurar el conector para crear automáticamente una estructura cuando sea necesario. Por ejemplo, si hay un espacio de nombres dc = contoso, dc = com y una nueva objeto cn = Juan, ou = Seattle, c = US, dc = contoso, dc = com se aprovisiona, a continuación, el conector puede crear un objeto del país del tipo de Estados Unidos y un organizationalUnit para Seattle si estos ya no están presentes en el directorio.

### <a name="configure-partitions-and-hierarchies"></a>Configurar las particiones y jerarquías
En la página particiones y jerarquías, seleccione todos los espacios de nombres con objetos que se va a importar y exportar.

![Particiones](./media/active-directory-aadconnectsync-connector-genericldap/partitions.png)

Para cada espacio de nombres, también es posible configurar las opciones de conectividad que reemplacen a los valores especificados en la pantalla de conectividad. Si estos valores se dejan a su valor en blanco de forma predeterminada, se usa la información de la pantalla de conectividad.

También es posible seleccionar qué contenedores y las unidades organizativas debe importación y exportación para el conector.

### <a name="configure-anchors"></a>Configurar los delimitadores
Esta página siempre tienen un valor preconfigurado y no se puede cambiar. Si ha identificado el proveedor del servidor, el anclaje puede rellenarse con un atributo inmutable, por ejemplo, el GUID de un objeto. Si no se ha detectado o se sabe que no tiene un atributo inmutable, el conector utiliza dn (nombre completo) como delimitador.

![delimitadores](./media/active-directory-aadconnectsync-connector-genericldap/anchors.png)

A continuación se muestra una lista de servidores LDAP y el delimitador que se utiliza:

Directorio | Atributo de anclaje
--- | ---
Microsoft AD LDS y AD catálogo global | GUID de objeto
Servidor de directorio 389 | DN
Directorio de Apache | DN
IBM Tivoli DS | DN
Directorio de Isode | DN
Novell/NetIQ eDirectory | GUID
Abrir DJ/DS | DN
LDAP abierto | DN
Oracle ODSEE | DN
RadiantOne VDS | DN
Servidor de directorio Sun One | DN

## <a name="other-notes"></a>Otras notas
Esta sección proporciona información de los aspectos que son específicos de este conector o por otras razones, es importante conocer.

### <a name="delta-import"></a>Importación de Delta
La marca de agua de delta en LDAP abierto es UTC Fecha y hora. Por este motivo, se debe sincronizar el reloj entre el servicio de sincronización de FIM y LDAP abierto. Si no es así, se pueden omitir algunas entradas en el registro de cambios delta.

Para eDirectory de Novell, la importación de delta no detecta las eliminaciones de objeto. Por este motivo, es necesario ejecutar una importación completa periódicamente para buscar todos los objetos eliminados.

Para directorios con un registro de cambios delta que se basa en la fecha y hora, se recomienda ejecutar una importación completa en momentos periódicos. Este proceso permite que el motor de sincronización para buscar y diferencias entre el servidor LDAP y lo que está actualmente en el espacio del conector.

## <a name="troubleshooting"></a>Solución de problemas

-   Para obtener información acerca de cómo habilitar el registro solucionar el conector, vea [cómo habilitar el seguimiento de ETW para los conectores](http://go.microsoft.com/fwlink/?LinkId=335731).
