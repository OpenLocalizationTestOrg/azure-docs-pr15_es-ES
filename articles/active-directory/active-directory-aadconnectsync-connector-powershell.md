<properties
   pageTitle="Conector de PowerShell | Microsoft Azure"
   description="Este artículo describe cómo configurar Windows PowerShell conector Microsoft."
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

# <a name="windows-powershell-connector-technical-reference"></a>Referencia técnica de conector de Windows PowerShell
En este artículo se describe el conector de Windows PowerShell. El artículo se aplica a los siguientes productos:

- Administrador de identidad de Microsoft 2016 (MIM2016)
- Administrador de identidad de Forefront 2010 R2 (FIM2010R2)
    -   Debe usar revisión 4.1.3671.0 o posterior [KB3092178](https://support.microsoft.com/kb/3092178).

Para MIM2016 y FIM2010R2, el conector está disponible como descarga desde el [Centro de descarga de Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-powershell-connector"></a>Información general sobre el conector de PowerShell
El conector de PowerShell le permite integrar el servicio de sincronización con sistemas externos que ofrecen API basadas en Windows PowerShell. El conector proporciona un puente entre las funciones de agente de administración de conectividad extensible llamada 2 framework (ECMA2) y Windows PowerShell. Para obtener más información sobre el marco de trabajo ECMA, vea la [Referencia de agente de administración de 2.2 conectividad Extensible](https://msdn.microsoft.com/library/windows/desktop/hh859557.aspx).

### <a name="prerequisites"></a>Requisitos previos
Antes de utilizar el conector, asegúrese de que tiene los siguientes en el servidor de sincronización:

- 4.5.2 de Microsoft .NET Framework o versiones posteriores
- Windows PowerShell 2.0, 3.0 o 4.0

La directiva de ejecución en el servidor de servicio de sincronización debe estar configurada para permitir que el conector ejecutar las secuencias de comandos de Windows PowerShell. A menos que las secuencias de comandos que se ejecuta el conector se ha firmado digitalmente, configurar la directiva de ejecución al ejecutar este comando:  
`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

## <a name="create-a-new-connector"></a>Crear un nuevo conector
Para crear un conector de Windows PowerShell en el servicio de sincronización, debe proporcionar una serie de scripts de Windows PowerShell que se ejecutan los pasos solicitados por el servicio de sincronización. Según el origen de datos que se conecta a y la funcionalidad que necesita, debe implementar las secuencias de comandos varía. Esta sección describen las secuencias de comandos que se puede implementar y cuando sea necesarios.

El conector de Windows PowerShell está diseñado para almacenar cada una de las secuencias de comandos dentro de la base de datos de servicio de sincronización. Aunque es posible ejecutar secuencias de comandos que se almacenan en el sistema de archivos, es más fácil insertar el cuerpo de cada secuencia de comandos directamente a la configuración del conector.

Para crear un conector de PowerShell, seleccione **Agente de administración** y **crear**en el **Servicio de sincronización** . Seleccione el conector de **PowerShell (Microsoft)** .

![Crear el conector](./media/active-directory-aadconnectsync-connector-powershell/createconnector.png)

### <a name="connectivity"></a>Conectividad
Proporcionar parámetros de configuración para conectar con un sistema remoto. Estos valores se segura almacenados por el servicio de sincronización y disponibles para las secuencias de comandos de Windows PowerShell cuando se ejecuta el conector.

![Conectividad](./media/active-directory-aadconnectsync-connector-powershell/connectivity.png)

Puede configurar los siguientes parámetros de conectividad:

**Conectividad**

Parámetro | Valor predeterminado | Propósito
--- | --- | ---
Servidor | <Blank> | Nombre de servidor que el conector debe conectarse a.
Dominio | <Blank> | Dominio de la credencial para almacenar para usar cuando se ejecuta el conector.
Usuario | <Blank> | Nombre de usuario de la credencial para almacenar para usar cuando se ejecuta el conector.
Contraseña | <Blank> | Contraseña de la credencial para almacenar para usar cuando se ejecuta el conector.
Representar cuenta de conector | Falso | Cuando es true, el servicio de sincronización ejecuta las secuencias de comandos de Windows PowerShell en el contexto de las credenciales proporcionadas. Cuando sea posible, se recomienda que el parámetro **$Credentials** se pasa a cada script se usa en lugar de suplantación. Para obtener más información sobre los permisos adicionales que son necesarios para usar esta opción, vea [Configuración adicional de suplantación](#additional-configuration-for-impersonation).
Cargar el perfil de usuario cuando suplantación | Falso | Indica a Windows para cargar el perfil de usuario de credenciales del conector durante la suplantación. Si el usuario representado tiene un perfil móvil, el conector no carga el perfil móvil. Para obtener más información sobre los permisos adicionales que son necesarios para usar este parámetro, vea [Configuración adicional de suplantación](#additional-configuration-for-impersonation).
Tipo de inicio de sesión cuando suplantación | Ninguno | Tipo de inicio de sesión durante la suplantación. Para obtener más información, consulte la [dwLogonType] [ dw] documentación.
Sólo secuencias de comandos firmadas | Falso | Si es true, el conector de Windows PowerShell valida que cada script tiene una firma digital válida. Si es false, asegúrese de que Directiva de ejecución de Windows PowerShell del servidor de servicio de sincronización es RemoteSigned o sin restricciones.

**Módulo comunes**  
El conector permite almacenar un módulo de Windows PowerShell compartido en la configuración. Cuando el conector ejecuta una secuencia de comandos, se extrae el módulo Windows PowerShell para el sistema de archivos para que se pueda importar cada secuencia de comandos.

Para los scripts de importar, exportar y sincronización de contraseñas, el módulo común se extrae a la carpeta del conector MAData. Esquema, validación, jerarquía y partición de secuencias de comandos de detección, el módulo común se extrae a la carpeta % TEMP %. En ambos casos, la secuencia de comandos comunes módulo extraído se denomina según el valor de nombre de secuencia de comandos comunes de módulo.

Para cargar un módulo denominado FIMPowerShellConnectorModule.psm1 desde la carpeta MAData, utilice la siguiente instrucción:`Import-Module (Join-Path -Path [Microsoft.MetadirectoryServices.MAUtils]::MAFolder -ChildPath "FIMPowerShellConnectorModule.psm1")`

Para cargar un módulo denominado FIMPowerShellConnectorModule.psm1 desde la carpeta % TEMP %, utilice la siguiente instrucción:`Import-Module (Join-Path -Path $env:TEMP -ChildPath "FIMPowerShellConnectorModule.psm1")`

**Validación de parámetros**  
La secuencia de comandos de validación es una secuencia de comandos de Windows PowerShell opcional que se puede utilizar para asegurarse de que los parámetros de configuración de conector proporcionados por el administrador son válidos. Servidor de validación, las credenciales de conexión y parámetros de conectividad son usos comunes de la secuencia de comandos de validación. La secuencia de comandos de validación se llama después de las siguientes pestañas y cuadros de diálogo se modifican:

- Conectividad
- Parámetros globales
- Configuración de la partición

La secuencia de comandos de validación recibe los siguientes parámetros del conector:

Nombre | Tipo de datos | Descripción
--- | --- | ---
ConfigParameterPage | [ConfigParameterPage][cpp] | La pestaña Configuración o cuadro de diálogo que activó la solicitud de validación.
ConfigParameters | [KeyedCollection] [ keyk] [cadena [ConfigParameter][cp]] | Tabla de parámetros de configuración del conector.
Credenciales | [PSCredential][pscred] | Contiene credenciales especificadas por el administrador en la pestaña de conectividad.

La secuencia de comandos de validación debe devolver un único objeto ParameterValidationResult a la canalización.

**Detección de esquema**  
La secuencia de comandos de detección de esquema es obligatorio. Esta secuencia de comandos devuelve los tipos de objeto, atributos y las restricciones de atributo que usa el servicio de sincronización al configurar reglas de flujo de atributos. La secuencia de comandos de detección de esquema se ejecuta durante la creación del conector y rellena el esquema del conector. También se usa la acción Actualizar esquema en el Administrador de servicios de sincronización.

La secuencia de comandos de detección de esquema recibe los siguientes parámetros del conector:

Nombre | Tipo de datos | Descripción
--- | --- | ---
ConfigParameters | [KeyedCollection] [ keyk] [cadena [ConfigParameter][cp]] | Tabla de parámetros de configuración del conector.
Credenciales | [PSCredential][pscred] | Contiene credenciales especificadas por el administrador en la pestaña de conectividad.

La secuencia de comandos debe devolver un único [esquema] [ schema] objeto a la canalización. El objeto de esquema se compone de [SchemaType] [ schemaT] objetos que representan los tipos de objeto (por ejemplo: los usuarios y grupos). El objeto SchemaType contiene una colección de [SchemaAttribute] [ schemaA] objetos que representan los atributos (por ejemplo: nombre, apellidos y dirección postal) del tipo.

**Parámetros adicionales**  
Además de la configuración estándar, puede definir valores de configuración personalizado adicionales que son específicos de la instancia del conector. Estos parámetros se pueden especificar en el conector, partición, o ejecutar paso niveles y acceder a ellas desde el script de Windows PowerShell relevante. Valores de configuración personalizados se pueden almacenar en la base de datos de servicio de sincronización en texto sin formato o puede cifrar. El servicio de sincronización automáticamente cifra y descifra seguro configuración cuando sea necesario.

Para especificar valores de configuración personalizados, separar el nombre de cada parámetro con una coma (,).

Para obtener acceso a configuración personalizada de una secuencia de comandos, debe sufijo el nombre con un subrayado ( \_ ) y el ámbito del parámetro (Global, partición o RunStep). Por ejemplo, para acceder al parámetro de nombre de archivo Global, use este fragmento de código:`$ConfigurationParameters["FileName_Global"].Value`

### <a name="capabilities"></a>Capacidades de
La ficha capacidades del Diseñador de agente de administración de define el comportamiento y la funcionalidad del conector. No se puede modificar las selecciones realizadas en esta ficha cuando se ha creado el conector. Esta tabla muestra la configuración de la función.

![Capacidades de](./media/active-directory-aadconnectsync-connector-powershell/capabilities.png)

Capacidad | Descripción |
--- | --- |
[Estilo de nombre distintivo][dnstyle] | Indica si el conector es compatible con nombres completos y si es así, ¿qué estilo.
[Tipo de exportación][exportT] | Determina el tipo de objetos que se presentan a la secuencia de comandos de exportación. <li>AttributeReplace: incluye el conjunto completo de valores de un atributo multivalor cuando cambie el atributo.</li><li>AttributeUpdate – incluye únicamente las diferencias a un atributo multivalor cuando cambie el atributo.</li><li>MultivaluedReferenceAttributeUpdate - incluye un conjunto completo de valores para los atributos de valores múltiple sin referencia y delta solo para los atributos de referencia con varios valores.</li><li>ObjectReplace: incluye todos los atributos de un objeto cuando ningún atributo cambios</li>
[Normalización de datos][DataNorm] | Indica que el servicio de sincronización para normalizar los atributos de anclaje antes de que se proporcionan para las secuencias de comandos.
[Confirmación de objeto][oconf] | Configura el comportamiento de importación pendiente en el servicio de sincronización. <li>Normal: comportamiento predeterminado que espera exportados todos los cambios confirmado mediante la importación</li><li>NoDeleteConfirmation: cuando se elimina un objeto, no hay ninguna importación pendiente generado.</li><li>NoAddAndDeleteConfirmation: cuando se crea un objeto o se elimina, no hay importar pendiente generado.</li>
Usar el nombre completo como delimitador | Si se establece el estilo de nombre distintivo para LDAP, el atributo de anclaje para el espacio del conector es también el nombre completo.
Operaciones simultáneas de varios conectores | Cuando está activada, pueden ejecutar simultáneamente varios conectores de Windows PowerShell.
Particiones | Cuando está activada, el conector admite varias particiones y detección de partición.
Jerarquía | Cuando está activada, el conector es compatible con una estructura jerárquica de estilo LDAP.
Habilitar la importación | Cuando está activada, el conector importa los datos a través de scripts de importación.
Habilitar la importación de Delta | Cuando está activada, el conector puede solicitar diferencias de las secuencias de comandos de importación.
Habilitar exportación | Cuando está activada, el conector exporta datos a través de scripts de exportación.
Habilitar exportación completa | Cuando está activada, las secuencias de comandos de exportación admiten la exportación del espacio de conector por completo. Para usar esta opción, también se verificará habilitar exportar.
Sin valores de referencia en el primer paso de la exportación | Cuando está activada, se exportan los atributos de referencia en un segundo paso de exportación.
Habilitar cambio de nombre de objeto | Cuando está activada, se pueden modificar nombres completos.
Agregar eliminar como reemplazar | Cuando está activada, eliminar agregar operaciones se exportan como un solo reemplazo.
Habilitar las operaciones de contraseña | Cuando está activada, se admiten las secuencias de comandos de sincronización de contraseña.
Habilitar contraseña de exportación en la primera pasada | Cuando está activada, se exportan establecer durante el aprovisionamiento de contraseñas cuando se crea el objeto.

### <a name="global-parameters"></a>Parámetros globales
La ficha parámetros Global en el Diseñador de agente de administración le permite configurar las secuencias de comandos de Windows PowerShell que se ejecutan mediante el conector. También puede configurar valores globales para los valores de configuración personalizada definidos en la ficha conectividad.

**Detección de partición**  
Una partición es un espacio de nombres independiente dentro de un esquema compartido. Por ejemplo, en Active Directory cada dominio es una partición dentro del mismo. Una partición es la agrupación lógica para importar y exportar operaciones. Importar y exportar tienen partición como sucede un contexto y todas las operaciones en este contexto. Las particiones se supone que representan una jerarquía en LDAP. Se utiliza el nombre completo de una partición en Importar para comprobar que todos los objetos devueltos están en el ámbito de una partición. El nombre completo de partición también se utiliza durante el aprovisionamiento de metaverse hasta el espacio de conector para determinar la partición que debe ser asociado con un objeto durante la exportación.

La secuencia de comandos de detección de partición recibe los siguientes parámetros del conector:

Nombre | Tipo de datos | Descripción
--- | --- | ---
ConfigParameters  | [KeyedCollection][keyk][cadena [ConfigParameter][cp]] | Tabla de parámetros de configuración del conector.
Credenciales | [PSCredential][pscred] | Contiene credenciales especificadas por el administrador en la pestaña de conectividad.

La secuencia de comandos debe devolver un una sola [partición] [ part] objeto o una lista [T] de objetos de partición a la canalización.

**Detección de jerarquía**  
La secuencia de comandos de detección de jerarquía solo se utiliza cuando la capacidad de estilo de nombre distintivo es LDAP. La secuencia de comandos se usa para permitir examinar y seleccione un conjunto de contenedores que se considera fuera del ámbito para importar y exportar operaciones. La secuencia de comandos solo debe proporcionar una lista de los nodos secundarios directa de nodo raíz proporcionado a la secuencia de comandos.

La secuencia de comandos de detección de jerarquía recibe los siguientes parámetros del conector:

Nombre | Tipo de datos | Descripción
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][cadena [ConfigParameter][cp]] | Tabla de parámetros de configuración del conector.
Credenciales | [PSCredential][pscred] | Contiene credenciales especificadas por el administrador en la pestaña de conectividad.
ParentNode | [HierarchyNode][hn] | El nodo de raíz de la jerarquía en la que la secuencia de comandos debe devolver a elementos secundarios directos.

La secuencia de comandos debe devolver un ya sea un objeto de HierarchyNode secundario único o una lista [T] de los objetos secundarios HierarchyNode a la canalización.

#### <a name="import"></a>Importar
Conectores que admiten las operaciones de importación deben implementar tres secuencias de comandos.

**Iniciar importación**  
La importación de inicio de la secuencia de comandos se ejecuta al principio de un paso de importación ejecutar. Durante este paso, puede establecer una conexión con el sistema de origen y siga los pasos de preparación antes de importar datos desde el sistema conectado.

La importación de inicio de la secuencia de comandos recibe los siguientes parámetros del conector:

Nombre | Tipo de datos | Descripción
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][cadena [ConfigParameter][cp]] | Tabla de parámetros de configuración del conector.
Credenciales | [PSCredential][pscred] | Contiene credenciales especificadas por el administrador en la pestaña de conectividad.
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Informa a la secuencia de comandos sobre el tipo de importación ejecutar (delta o completa), partición, jerarquía, marca de agua y tamaño de página esperado.
Tipos de | [Esquema][schema] | Esquema para el espacio de conector que se importa.

La secuencia de comandos debe devolver una sola [OpenImportConnectionResults] [ oicres] objeto a la canalización, por ejemplo:`Write-Output (New-Object Microsoft.MetadirectoryServices.OpenImportConnectionResults)`

**Importar datos**  
La secuencia de comandos de importación de datos se denomina el conector hasta que el script indica que no hay ningún dato más para importar. El conector de Windows PowerShell tiene un tamaño de página de 9.999 objetos. Si la secuencia de comandos devuelve más de 9.999 objetos para importar, debe admitir paginación. Expone de conector se denomina una propiedad de datos personalizados que puede usar para un almacén de una marca de agua para que cada vez que la secuencia de comandos de importación de datos, la secuencia de comandos reanuda importar objetos donde la dejó.

La secuencia de comandos de datos de importación recibe los siguientes parámetros del conector:

Nombre | Tipo de datos | Descripción
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][cadena [ConfigParameter][cp]] | Tabla de parámetros de configuración del conector.
Credenciales | [PSCredential][pscred] | Contiene credenciales especificadas por el administrador en la pestaña de conectividad.
GetImportEntriesRunStep | [ImportRunStep][irs] | Contiene la marca de agua (CustomData) que puede utilizarse durante pagina importaciones y delta importa.
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Informa a la secuencia de comandos sobre el tipo de importación ejecutar (delta o completa), partición, jerarquía, marca de agua y tamaño de página esperado.
Tipos de | [Esquema][schema] | Esquema para el espacio de conector que se importa.

La secuencia de comandos de importación de datos debe escribir una lista [[CSEntryChange][csec]] objeto a la canalización. Esta colección se compone de los atributos de CSEntryChange que representan cada objeto que se importa. Durante una importación completa, esta colección debe tener un conjunto completo de objetos de CSEntryChange que tiene todos los atributos de cada objeto. Durante una importación Delta, el objeto CSEntryChange bien debe contener el delta de nivel de atributo para cada objeto que se va a importar o una representación completa de los objetos que han cambiado (Replace mode).

**Importación de finalización**  
Al final de la importación ejecutar, se ejecuta la secuencia de comandos final importar. Esta secuencia de comandos debe realizar las tareas de limpieza necesarias (por ejemplo, cerradas conexiones a sistemas) y responder a los errores.

La importación de fin de la secuencia de comandos recibe los siguientes parámetros del conector:

Nombre | Tipo de datos | Descripción
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][cadena [ConfigParameter][cp]] | Tabla de parámetros de configuración del conector.
Credenciales | [PSCredential][pscred] | Contiene credenciales especificadas por el administrador en la pestaña de conectividad.
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Informa a la secuencia de comandos sobre el tipo de importación ejecutar (delta o completa), partición, jerarquía, marca de agua y tamaño de página esperado.
CloseImportConnectionRunStep | [CloseImportConnectionRunStep][cecrs] | Informa a la secuencia de comandos sobre el motivo que se ha finalizado la importación.

La secuencia de comandos debe devolver una sola [CloseImportConnectionResults] [ cicres] objeto a la canalización, por ejemplo:`Write-Output (New-Object Microsoft.MetadirectoryServices.CloseImportConnectionResults)`

#### <a name="export"></a>Exportar
Idéntica a la arquitectura de importación del conector, conectores que admiten la exportación deben implementar tres secuencias de comandos.

**Iniciar la exportación**  
Ejecutar el script de exportación inicial al principio de un paso de exportación ejecutar. Durante este paso, puede establecer una conexión con el sistema de origen y realizar los pasos de preparación antes de exportar los datos en el sistema conectado.

El script de exportación inicial recibe los siguientes parámetros del conector:

Nombre | Tipo de datos | Descripción
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][cadena [ConfigParameter][cp]] | Tabla de parámetros de configuración del conector.
Credenciales | [PSCredential][pscred] | Contiene credenciales especificadas por el administrador en la pestaña de conectividad.
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Informa a la secuencia de comandos sobre el tipo de exportación ejecutar (delta o completa), partición, jerarquía y el tamaño de página esperada.
Tipos de | [Esquema][schema] | Esquema para el espacio del conector que se exporta.

La secuencia de comandos no debe devolver ningún resultado a la canalización.

**Exportar datos**  
El servicio de sincronización llama al script de exportación de datos tantas veces como sea necesario para procesar todas las exportaciones pendientes. Si el espacio del conector tiene más exportaciones pendientes que del conector tamaño de página, puede llamar el script de exportación de datos varias veces y posiblemente varias veces para el mismo objeto.

El script de exportación de datos recibe los siguientes parámetros del conector:

Nombre | Tipo de datos | Descripción
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][cadena [ConfigParameter][cp]] | Tabla de parámetros de configuración del conector.
Credenciales | [PSCredential][pscred] | Contiene credenciales especificadas por el administrador en la pestaña de conectividad.
CSEntries | IList[CSEntryChange][csec] | Lista de todos los objetos de espacio de conector con pendiente exportaciones que deben procesarse durante este paso.
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Informa a la secuencia de comandos sobre el tipo de exportación ejecutar (delta o completa), partición, jerarquía y el tamaño de página esperada.
Tipos de | [Esquema][schema] | Esquema para el espacio del conector que se exporta.

El script de exportación de datos debe devolver un [PutExportEntriesResults] [ peeres] objeto a la canalización. Este objeto no es necesario incluir información de resultado para cada conector exportado a menos que se produce un error o un cambio en el atributo de anclaje. Por ejemplo, para devolver un objeto de PutExportEntriesResults para la canalización de:`Write-Output (New-Object Microsoft.MetadirectoryServices.PutExportEntriesResults)`

**Exportación de finalización**  
Al final de la exportación ejecutar, la secuencia de comandos final exportar para ejecutar. Esta secuencia de comandos debe realizar las tareas de limpieza necesarias (por ejemplo, cerradas conexiones a sistemas) y responder a los errores.

El script de exportación final recibe los siguientes parámetros del conector:

Nombre | Tipo de datos | Descripción
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][cadena [ConfigParameter][cp]] | Tabla de parámetros de configuración del conector.
Credenciales | [PSCredential][pscred] | Contiene credenciales especificadas por el administrador en la pestaña de conectividad.
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Informa a la secuencia de comandos sobre el tipo de exportación ejecutar (delta o completa), partición, jerarquía y el tamaño de página esperada.
CloseExportConnectionRunStep | [CloseExportConnectionRunStep][cecrs] | Informa a la secuencia de comandos sobre el motivo que se ha finalizado la exportación.

La secuencia de comandos no debe devolver ningún resultado a la canalización.

#### <a name="password-synchronization"></a>Sincronización de contraseñas
Conectores de Windows PowerShell pueden usarse como destino de cambios o restablecimiento de contraseñas.

La secuencia de comandos de contraseña recibe los siguientes parámetros del conector:

Nombre | Tipo de datos | Descripción
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][cadena [ConfigParameter][cp]] | Tabla de parámetros de configuración del conector.
Credenciales | [PSCredential][pscred] | Contiene credenciales especificadas por el administrador en la pestaña de conectividad.
Partición | [Partición][part] | Partición de directorio que tiene el CSEntry.
CSEntry | [CSEntry][cse] | Entrada de espacio de conector para el objeto que se ha recibido un cambio de contraseña o restablecer.
Tipo de operación | Cadena | Indica si la operación es un restablecimiento (**SetPassword**) o un cambio (**ChangePassword**).
Opciones de contraseña | [Opciones de contraseña][pwdopt] | Indicadores que especifican la contraseña restablecen comportamiento. Este parámetro solo está disponible si el tipo de operación es **SetPassword**.
ContraseñaAntigua | Cadena | Se rellena con la contraseña del objeto anterior para que los cambios de contraseña. Este parámetro solo está disponible si el tipo de operación es **ChangePassword**.
NewPassword | Cadena | Se rellena con la contraseña del objeto nuevo que debe establecer la secuencia de comandos.

La secuencia de comandos de contraseña no se espera devolver los resultados a la canalización de Windows PowerShell. Si se produce un error en la secuencia de comandos de la contraseña, la secuencia de comandos debe producir una de las siguientes excepciones para informar a los servicios de sincronización del problema:

- [PasswordPolicyViolationException] [ pwdex1] – se produce si la contraseña no cumple la directiva de contraseñas en el sistema conectado.
- [PasswordIllFormedException] [ pwdex2] – se produce si la contraseña no es aceptable para el sistema conectado.
- [PasswordExtension] [ pwdex3] -inicia para todos los otros errores en la secuencia de comandos de la contraseña.

## <a name="sample-connectors"></a>Conectores de ejemplo
Para obtener una descripción completa de los conectores de ejemplo disponibles, vea [Conjunto de conector de ejemplo de Windows PowerShell conector][samp].

## <a name="other-notes"></a>Otras notas

### <a name="additional-configuration-for-impersonation"></a>Configuración adicional de suplantación
Conceda al usuario que representa en los siguientes permisos en el servidor de servicio de sincronización:

Acceso de lectura a las siguientes claves de registro:

- HKEY_USERS\\\Software\Microsoft\PowerShell [SynchronizationServiceServiceAccountSID]
- HKEY_USERS\\\Environment [SynchronizationServiceServiceAccountSID]

Para determinar el identificador de seguridad (SID) de la cuenta de servicio del servicio de sincronización, ejecute los siguientes comandos de PowerShell:

```
$account = New-Object System.Security.Principal.NTAccount "<domain>\<username>"
$account.Translate([System.Security.Principal.SecurityIdentifier]).Value
```

Acceso de lectura a las carpetas de sistema de archivo siguientes:

- %ProgramFiles%\Microsoft forefront identidad Manager\2010\Synchronization Service\Extensions
- %ProgramFiles%\Microsoft forefront identidad Manager\2010\Synchronization Service\ExtensionsCache
- %ProgramFiles%\Microsoft forefront identidad Manager\2010\Synchronization Service\MaData\\{ConnectorName}

Sustituya el nombre del conector de Windows PowerShell para el marcador de posición de {ConnectorName}.

## <a name="troubleshooting"></a>Solución de problemas

-   Para obtener información acerca de cómo habilitar el registro solucionar el conector, vea [cómo habilitar el seguimiento de ETW para los conectores](http://go.microsoft.com/fwlink/?LinkId=335731).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[cpp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameterpage.aspx
[keyk]: https://msdn.microsoft.com/library/ms132438.aspx
[cp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameter.aspx
[pscred]: https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx
[schema]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schema.aspx
[schemaT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schematype.aspx
[schemaA]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schemaattribute.aspx
[dnstyle]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.madistinguishednamestyle.aspx
[exportT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maexporttype.aspx
[DataNorm]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.manormalizations.aspx
[oconf]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maobjectconfirmation.aspx
[dw]: https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx
[part]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.partition.aspx
[hn]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.hierarchynode.aspx
[oicrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionrunstep.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[oicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionresults.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[cicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeimportconnectionresults.aspx
[oecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openexportconnectionrunstep.aspx
[irs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.importrunstep.aspx
[cse]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentry.aspx
[csec]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentrychange.aspx
[peeres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.putexportentriesresults.aspx
[pwdopt]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordoptions.aspx
[pwdex1]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordpolicyviolationexception.aspx
[pwdex2]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordillformedexception.aspx
[pwdex3]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordextensionexception.aspx
[samp]: http://go.microsoft.com/fwlink/?LinkId=394291
