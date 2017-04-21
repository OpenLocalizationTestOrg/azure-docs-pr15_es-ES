<properties 
    pageTitle="Integración de directorios entre autenticación multifactor de Azure y Active Directory"
    description="Esta es la página de autenticación multifactor de Azure que describe cómo integrar el servidor de autenticación multifactor de Azure con Active Directory para poder sincronizar los directorios."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="directory-integration-between-azure-mfa-server-and-active-directory"></a>Integración de directorios entre Azure AMF Server y Active Directory

La sección Integración de directorios permite configurar el servidor para integrar con Active Directory o de otro directorio LDAP.  Permite configurar atributos para que coincida con el esquema de directorio y configurar la sincronización automática de los usuarios.

## <a name="settings"></a>Configuración
De forma predeterminada, el servidor de autenticación multifactor de Azure está configurado para importar o sincronizar los usuarios desde Active Directory.  La ficha permite invalidar el comportamiento predeterminado y asociar a un directorio LDAP diferente, un directorio de ADAM o controlador de dominio de Active Directory específico.  También se proporciona para el uso de la autenticación LDAP al proxy LDAP o para enlazar LDAP como destino de radio, autenticación previa para la autenticación de IIS, o principal para el Portal de usuario.  La siguiente tabla describe las configuraciones individuales.

![Configuración](./media/multi-factor-authentication-get-started-server-dirint/dirint.png)

| Característica | Descripción |
| ------- | ----------- |
| Uso de Active Directory | Seleccione la opción Usar Active Directory para utilizar Active Directory para importar y sincronización.  Esta es la configuración predeterminada. <br>Nota: El equipo debe estar unido a un dominio y debe haber iniciado sesión con una cuenta de dominio para la integración de Active Directory funcione correctamente. |
| Incluir dominios de confianza | Active la casilla de verificación Incluir dominios de confianza para que el agente intente conectarse a dominios de confianza para el dominio actual, otro dominio del bosque o dominios implicados en una relación de confianza.  Cuando no importar o sincronizar los usuarios desde cualquiera de los dominios de confianza, desactive la casilla de verificación para mejorar el rendimiento.  El valor predeterminado está activado. |
| Usar configuración de LDAP específica | Seleccione la opción Usar LDAP para usar la configuración de LDAP especificada para importar y sincronización. Nota: Al utilizar LDAP está activada, la interfaz de usuario cambia referencias de Active Directory para LDAP. |
| Botón Editar | El botón Editar permite la configuración actual de LDAP que modificar. |
| Usar las consultas de ámbito de atributo | Indica si se deben usar consultas de ámbito de atributo.  Permitir que las consultas de ámbito de atributo para las búsquedas de directorio eficaz calificar registros basándose en las entradas de atributo del otro registro.  El servidor de autenticación multifactor de Azure usa consultas de ámbito de atributo para los usuarios que están miembro de un grupo de seguridad de consulta eficaz.   <br>Nota: Hay algunos casos donde las consultas de ámbito de atributo son compatibles, pero no debe usarse.  Por ejemplo, Active Directory puede tener problemas con las consultas de ámbito de atributo cuando un grupo de seguridad contiene miembros de más de un dominio.  En este caso, la casilla de verificación debe estar desactivada. |

La siguiente tabla describe las opciones de configuración de LDAP.

| Característica | Descripción |
| ------- | ----------- |
| Servidor | Escriba el nombre de host o la dirección IP del servidor que ejecuta el directorio LDAP.  También se puede especificar un servidor de copia de seguridad separados por punto y coma. <br>Nota: Cuando enlazar tipo es SSL, un nombre de host completo es generalmente necesario. |
| DN base | Escriba el nombre completo del objeto de directorio base desde la que se iniciará todas las consultas de directorio.  Por ejemplo, dc = abc, dc = com. |
| Enlazar tipo - consultas | Seleccione el tipo de enlace apropiado para su uso cuando se enlazan a buscar en el directorio LDAP.  Se utiliza para importaciones, la sincronización y la resolución de nombre de usuario. <br><br>  Anónimo - se realizará un enlace anónimo.  DN de enlace y enlazar contraseña no se utilizará.  Esto solo funciona si el directorio LDAP permite a enlace anónimo y permisos permiten la consulta de los registros correspondientes y atributos.  <br><br> Simple - DN de enlace y la contraseña de enlazar pasarán como texto sin formato para enlazar con el directorio LDAP.  Esto sólo debe utilizarse con fines de pruebas para comprobar que puede tener acceso el servidor y que la cuenta de enlace tiene el acceso adecuado.  Se recomienda utilizar el SSL en su lugar después de haber instalado el certificado adecuado.  <br><br> SSL - enlace DN y enlazar contraseña se cifran mediante SSL a enlazar el directorio LDAP.  Esto requiere que un certificado esté instalado localmente que confía en el directorio LDAP.  <br><br> Windows: enlace de nombre de usuario y contraseña enlazar se usará para conectarse a un controlador de dominio de Active Directory o el directorio de ADAM de forma segura.  Si enlaza el nombre de usuario se deja en blanco, se utilizará la cuenta de usuario que ha iniciado sesión enlazar. |
| Enlazar tipo - autenticaciones | Seleccione el tipo de enlace apropiado para su uso cuando se realiza la autenticación de enlace LDAP.  Consulte el enlace descripciones en un tipo de enlace - consultas de tipo.  Por ejemplo, esto permite enlace anónimo que se usará para las consultas mientras se utiliza el enlace SSL para proteger autenticaciones de enlace LDAP. |
| Enlace DN o nombre de usuario de enlace | Escriba el nombre completo del registro de usuario para la cuenta que desee utilizar al enlazar con el directorio LDAP.<br><br>Solo se utiliza el nombre completo de enlace cuando tipo enlazar es Simple o SSL.  <br><br>Escriba el nombre de la cuenta de Windows a utilizar al enlazar con el directorio LDAP al tipo de enlazar es Windows.  Si deja en blanco, se utilizará la cuenta de usuario que ha iniciado sesión enlazar. |
| Contraseña de vinculación | Escriba la contraseña de enlace para el DN enlazar o el nombre de usuario que se usa para enlazar con el directorio LDAP.  Para configurar la contraseña para el servicio de AdSync del servidor de autenticación multifactor, debe estar habilitada la sincronización y el servicio debe estar ejecutándose en el equipo local.  La contraseña se guardará en los nombres de usuario de Windows almacenados y las contraseñas en la cuenta que se ejecuta el servicio de AdSync del servidor de autenticación multifactor como.  También se guardará la contraseña en la cuenta que se ejecuta la interfaz de usuario del servidor de autenticación multifactor como y en la cuenta que se ejecuta el servicio de servidor de autenticación multifactor como.  <br><br> Nota: Puesto que la contraseña sólo se almacena en los nombres de usuario de Windows almacenados y las contraseñas del servidor local, este paso se debe realizar en cada servidor de autenticación multifactor que necesita obtener acceso a la contraseña. |
| Límite de tamaño de la consulta | Especifique el límite de tamaño para el número máximo de usuarios que va a devolver una búsqueda en el directorio.  Este límite debe coincidir con la configuración en el directorio LDAP.  Para realizar búsquedas grandes donde no se admite la paginación, importar y sincronización intentará recuperar los usuarios en lotes.  Si el límite de tamaño especificado aquí es mayor que el límite configurado en el directorio LDAP, es podrán que algunos usuarios pueden perdidas. |
| Botón de prueba | Haga clic en el botón de prueba para probar el enlace con el servidor LDAP.  <br><br> Nota: La opción utilizar LDAP no es necesario que se seleccione para probar el enlace.  Esto permite el enlace comprobar antes de usar la configuración de LDAP. |

## <a name="filters"></a>Filtros
Los filtros permiten establecer criterios para calificar los registros cuando se realiza una búsqueda en el directorio.  Mediante la configuración del filtro puede definir el ámbito de los objetos que desea sincronizar.  

![Filtros](./media/multi-factor-authentication-get-started-server-dirint/dirint2.png)

La autenticación multifactor Azure tiene las siguientes opciones de 3.

- **Filtro de contenedor** : especifique los criterios de filtro que se utiliza para calificar los registros del contenedor cuando se realiza una búsqueda en el directorio.  De Active Directory y ADAM, (| () Generalmente se utiliza objectClass=organizationalUnit)(objectClass=container)).  Para otros directorios LDAP, se deben usar criterios de filtro que califica cada tipo de objeto contenedor según el esquema de directorio.  <br>Nota: Si deja en blanco, ((objectClass=organizationalUnit)(objectClass=container)) se utilizará de forma predeterminada.

- **Filtro de grupo de seguridad** : especifique los criterios de filtro que se utiliza para calificar los registros del grupo de seguridad al realizar una búsqueda en el directorio.  De Active Directory y ADAM, (&(objectCategory=group) (groupType:1.2.840.113556.1.4.804:=-2147483648)) suele usarse.  Para otros directorios LDAP, se deben usar criterios de filtro que califica cada tipo de objeto de grupo de seguridad según el esquema de directorio.  <br>Nota: Si deja en blanco, (&(objectCategory=group) (groupType:1.2.840.113556.1.4.804:=-2147483648)) se usan de forma predeterminada.

- **Filtro de usuario** : especifique los criterios de filtro que se utiliza para calificar los registros de usuario cuando se realiza una búsqueda en el directorio.  De Active Directory y ADAM, (& (generalmente se utiliza objectClass=user)(objectCategory=person)).  Para otros directorios LDAP, (objectClass = inetOrgPerson) o algo similar, se debe utilizar según el esquema de directorio. <br>Nota: Si deja en blanco, (& (objectCategory=person)(objectClass=user)) se utilizará de forma predeterminada.

## <a name="attributes"></a>Atributos
Atributos se pueden personalizar según sea necesario para un directorio concreto.  Esto le permite agregar atributos personalizados y ajustar el rendimiento de la sincronización sólo los atributos que necesita.  El valor de cada campo de atributo debe ser el nombre del atributo según se define en el esquema de directorio.  Utilice la siguiente tabla para obtener información adicional.

![Atributos](./media/multi-factor-authentication-get-started-server-dirint/dirint3.png)

| Característica | Descripción |
| ------- | ----------- |
| Identificador único | Escriba el nombre del atributo del atributo que sirve como el identificador único del contenedor, grupo de seguridad y registros de usuario.  Suele ser GUID de objeto de Active Directory.  En otras implementaciones de LDAP, puede ser entryUUID o algo similar.  GUID de objeto de forma predeterminada. |
| ... Botones (atributo select) | Cada campo de atributo tiene un botón "... junto a ella que se mostrará el cuadro de diálogo Seleccionar atributo permitir un atributo desde una lista". <br><br>Seleccione el cuadro de diálogo de atributo.<br><br>Nota: Los atributos pueden escribirse manualmente y no están necesario para que coincidan con un atributo en la lista de atributos. |
| Tipo de identificador único | Seleccione el tipo del atributo identificador único.  En Active Directory, el atributo GUID de objeto es de tipo GUID.  En otras implementaciones de LDAP, puede ser de tipo matriz de bytes ASCII o cadena.  GUID de forma predeterminada. <br><br>Nota: Es importante que establezca correctamente debido a que se hace referencia a elementos de sincronización por su identificador único y el tipo de identificador único se usa para buscar directamente el objeto en el directorio.  Si se establece en cadena cuando el directorio almacena el valor como una matriz de bytes de caracteres ASCII evitará sincronización funcione correctamente. |
| Nombre distintivo | Escriba el nombre del atributo del atributo que contiene el nombre completo de cada registro.  En Active Directory, suele ser distinguishedName.  En otras implementaciones de LDAP, puede ser entryDN o algo similar.  El valor predeterminado es distinguishedName. <br><br>Nota: Si no existe un atributo que contiene el nombre completo, puede utilizarse el atributo adspath.  La "LDAP: / /<server>/" parte de la ruta se eliminará automáticamente desactivar salir solo el nombre completo del objeto. |
| Nombre del contenedor | Escriba el nombre del atributo del atributo que contiene el nombre de un registro de contenedor.  El valor de este atributo se mostrará en la jerarquía de contenedor al importar desde Active Directory o agregar elementos de sincronización.  El valor predeterminado es el nombre. <br><br>Nota: Si diferentes contenedores usar atributos diferentes para sus nombres, varios atributos de nombre de contenedor pueden especificarse en separados por punto y coma.  El primer atributo de nombre de contenedor se encuentra en un objeto contenedor se utilizará para mostrar su nombre. |
| Nombre del grupo de seguridad | Escriba el nombre del atributo del atributo que contiene el nombre de un registro de grupo de seguridad.  El valor de este atributo se mostrará en la lista de grupos de seguridad al importar desde Active Directory o agregar elementos de sincronización.  El valor predeterminado es el nombre. |
| Usuarios | Los siguientes atributos se usan para buscar, mostrar, importar y sincronizar la información de usuario desde el directorio. |
| Nombre de usuario | Escriba el nombre del atributo del atributo que contiene el nombre de usuario en un registro de usuario.  El valor de este atributo se utilizará como el nombre de usuario del servidor de autenticación multifactor.  Un segundo atributo puede especificarse como una copia de seguridad de la primera.  El segundo atributo solo se utilizará si el primer atributo no contiene un valor para el usuario.  Los valores predeterminados son userPrincipalName y sAMAccountName. |
| Nombre | Escriba el nombre del atributo del atributo que contiene el nombre de un registro de usuario.  El valor predeterminado es givenName. |
| Apellido | Escriba el nombre del atributo del atributo que contiene el apellido en un registro de usuario.  El valor predeterminado es sn. |
| Dirección de correo electrónico | Escriba el nombre del atributo del atributo que contiene la dirección de correo electrónico en un registro de usuario.  Dirección de correo electrónico se utilizará para enviar Bienvenido y actualizar correos electrónicos al usuario.  El valor predeterminado es correo. |
| Grupo de usuarios | Escriba el nombre del atributo del atributo que contiene el grupo de usuarios en un registro de usuario.  Grupo de usuarios puede usarse para filtrar los usuarios en el agente e informes en el Portal de administración de servidor de autenticación multifactor. |
| Descripción | Escriba el nombre del atributo del atributo que contiene la descripción de un registro de usuario.  Descripción solo se usa para realizar búsquedas.  El valor predeterminado es descripción. |
| Idioma de la llamada de voz | Escriba el nombre del atributo del atributo que contiene el nombre abreviado del idioma que se va a utilizar para las llamadas de voz para el usuario. |
| Idioma del texto SMS | Escriba el nombre del atributo del atributo que contiene el nombre abreviado del idioma para mensajes de texto SMS para el usuario. |
| Idioma de la aplicación de teléfono | Escriba el nombre del atributo del atributo que contiene el nombre abreviado del idioma para mensajes de texto de aplicación de teléfono para el usuario. |
| Idioma de token JURAMENTO | Escriba el nombre del atributo del atributo que contiene el nombre abreviado del idioma que utilice JURAMENTO token mensajes de texto para el usuario. |
| Teléfonos | Los siguientes atributos se usan para importar o sincronizar los números de teléfono del usuario.  Si no se especifica un nombre para el tipo de teléfono, el tipo de teléfono no estará disponible cuando importación desde Active Directory o agregar elementos de sincronización. |
| Empresa | Escriba el nombre del atributo del atributo que contiene el número de teléfono de la empresa en un registro de usuario.  El valor predeterminado es telephoneNumber. |
| Inicio | Escriba el nombre del atributo del atributo que contiene el número de teléfono particular de un registro de usuario.  El valor predeterminado es homePhone. |
| Localizador | Escriba el nombre del atributo del atributo que contiene el número de paginación en un registro de usuario.  Localizador de forma predeterminada. |
| Móvil | Escriba el nombre del atributo del atributo que contiene el número de teléfono móvil en un registro de usuario.  El valor predeterminado es móvil. |
| Fax | Escriba el nombre del atributo del atributo que contiene el número de fax en un registro de usuario.  El valor predeterminado es facsimileTelephoneNumber. |
| Teléfono IP | Escriba el nombre del atributo del atributo que contiene el número de teléfono IP en un registro de usuario.  El valor predeterminado es ipPhone. |
| Personalizado | Escriba el nombre del atributo del atributo que contiene un número de teléfono personalizado en |
|  | un registro de usuario.  El valor predeterminado es en blanco. |
| Extensión | Escriba el nombre del atributo del atributo que contiene la extensión del número de teléfono en un registro de usuario.  El valor del campo de extensión se utilizará como la extensión solo el número de teléfono principal.  El valor predeterminado es en blanco. <br><br>Nota: Si no se especifica el atributo de extensión, las extensiones se pueden incluidas como parte del atributo teléfono.  La extensión debe ir precedida de una 'x' para que se pueda analizar.  Por ejemplo 555-123-4567 x890 daría como resultado 555-123-4567 como el número de teléfono y 890 la extensión. |
| Restaurar valores predeterminados de botón | Haga clic en el botón Restaurar valores predeterminados para devolver todos los atributos a su valor predeterminado.  Los valores predeterminados deben funcionar correctamente con el esquema de Active Directory o ADAM normal. |

Para modificar atributos, simplemente haga clic en el botón Editar en la ficha atributos.  Aparecerá un windows que le permite editar los atributos.

![Edición de atributos](./media/multi-factor-authentication-get-started-server-dirint/dirint4.png)

## <a name="synchronization"></a>Sincronización
Sincronización mantiene la base de datos de usuario multifactor de Azure sincronizado con los usuarios de Active Directory o en otro directorio de protocolo ligero de acceso de directorios de protocolo ligero de acceso a directorios (LDAP).  El proceso es similar a importar usuarios manualmente desde Active Directory, pero busca periódicamente los cambios del grupo de seguridad y de usuario de Active Directory para procesar.  También proporciona para deshabilitar o quitar usuarios quitado de un grupo de seguridad o contenedor y quitar usuarios eliminados de Active Directory.

El servicio de varios factores Auth ADSync es un servicio de Windows que realiza el sondeo periódico de Active Directory.  Esto no es debe confundirse con la sincronización de Azure AD o Azure AD Connect.  la ADSync de autenticación multifactor, aunque integrada en una base de código similares, es específico del servidor de autenticación multifactor de Azure.  Se instala en un estado detenido y se inicia el servicio de servidor de autenticación multifactor cuando está configurado para ejecutar.  Si tiene una configuración de servidor de autenticación multifactor de varios servidor, la ADSync autenticación multifactor sólo puede ejecutarse en un solo servidor.

El servicio de ADSync de autenticación multifactor usa la extensión de servidor LDAP DirSync proporcionada por Microsoft para buscar eficazmente cambios.  Esta llamada de control de sincronización de directorios debe tener "directorio" obtener cambios hacia la derecha y obtener cambios DS-replicación extendido controlar el acceso hacia la derecha.  De forma predeterminada, estos derechos se asignan a las cuentas de administrador y LocalSystem en controladores de dominio.  El servicio de AdSync de autenticación multifactor está configurado para ejecutarse como LocalSystem de forma predeterminada.  Por lo tanto, es más sencillo ejecutar el servicio en un controlador de dominio.  El servicio puede ejecutar como una cuenta con menos permisos si configúrelo para realizar siempre una sincronización completa.  Esto es menos eficaz, pero requiere menos privilegios de cuenta.

Si ha configurado para utilizar LDAP y el directorio LDAP admite el control de sincronización de directorios, a continuación, el sondeo de usuario y los cambios del grupo de seguridad funcionará la misma como ocurre con Active Directory.  Si el directorio LDAP no admite el control de sincronización de directorios, se realizará una sincronización completa durante cada ciclo.

![Sincronización](./media/multi-factor-authentication-get-started-server-dirint/dirint5.png)

Utilice la siguiente tabla para obtener información adicional sobre cada una de las configuraciones individuales en la pestaña sincronización.

| Característica | Descripción |
| ------- | ----------- |
| Habilitar la sincronización con Active Directory | Cuando está activada, se iniciará el servicio de servidor de autenticación multifactor para sondear periódicamente Active Directory para los cambios. <br><br>Nota: se debe agregar al menos un elemento de sincronización y sincronizar ahora debe realizarse antes del servidor de autenticación multifactor servicio iniciará el procesamiento de cambios. |
| Sincronizar todos | Especifique el intervalo de tiempo que el servicio de servidor de autenticación multifactor esperará entre sondeo y procesamiento de cambios. <br><br> Nota: El intervalo especificado es el tiempo entre el comienzo de cada ciclo.  Si el tiempo de procesamiento de cambios supera el intervalo, el servicio se sondea inmediatamente. |
| Quitar usuarios ya no está en Active Directory | Cuando está activada, el servicio de servidor de autenticación multifactor procesará marcadores de exclusión de usuario de Active Directory eliminados y eliminar el usuario de servidor de autenticación multifactor relacionado. |
| Realizar siempre una sincronización completa | Cuando está activada, el servicio de servidor de autenticación multifactor siempre realizará una sincronización completa.  Cuando está desactivado, el servicio de servidor de autenticación multifactor realizará una sincronización incremental consultando solo los usuarios que han cambiado.  El valor predeterminado es desactivado. <br><br> Nota: Cuando está desactivado, una sincronización incremental sólo puede realizarse cuando el directorio admite el control de sincronización de directorios y la cuenta que se usa para enlazar al directorio tiene los permisos adecuados para realizar consultas incrementales de sincronización de directorios.  Si la cuenta no tiene los permisos adecuados o varios dominios implicados en la sincronización, realice una completa se recomienda la sincronización. |
| Requerir aprobación del administrador cuando más de X que se quitan o deshabilitan | Elementos de sincronización se pueden configurar para deshabilitar o eliminar usuarios que ya no están miembro del elemento contenedor o grupo de seguridad.  Como protección, aprobación del administrador puede ser necesario cuando el número de usuarios para deshabilitar o quitar supera un umbral.  Cuando está activada, aprobación será necesaria para umbral especificado.  El valor predeterminado es 5 y el intervalo es de 1 a 999. <br><br> Aprobación es más fácil por primera envía una notificación de correo electrónico a los administradores. La notificación de correo electrónico ofrece instrucciones para revisión y aprobación de la deshabilitación y eliminación de los usuarios.  Cuando se inicia la interfaz de usuario del servidor de autenticación multifactor, pedirá para su aprobación. |

El botón **Sincronizar ahora** le permite ejecutar una sincronización completa de los elementos de sincronización especificado.  Una sincronización completa es necesaria siempre que se agregan, modificados, quitar o reordenar elementos de sincronización.  También es necesario antes de que el servicio AdSync de autenticación multifactor será operativo ya que establece el punto de partida desde el que el servicio se sondea para cambios incrementales.  Si se han realizado cambios a los elementos de sincronización y no se ha realizado una sincronización completa, se le pedirá que sincronizar ahora al navegar a otra sección o cuando se cierra la interfaz de usuario.

El botón **Quitar** permite al administrador eliminar uno o varios elementos de sincronización de la lista de elementos de la sincronización de servidor de autenticación multifactor.

>[AZURE.WARNING]Una vez se haya eliminado un registro de elemento de sincronización, no se puede recuperar. Debe volver a agregar el registro de elemento de sincronización si ha eliminado por error.

El elemento de sincronización o elementos de sincronización se quitaron del servidor de autenticación multifactor.  El servicio de servidor de autenticación multifactor ya no procesará los elementos de sincronización.

Los botones Subir y Bajar permiten al administrador cambiar el orden de los elementos de sincronización.  El orden es importante, ya que el mismo usuario puede ser miembro de más de un elemento de sincronización (por ejemplo, un contenedor y un grupo de seguridad).  La configuración que se aplica al usuario durante la sincronización proceden el primer elemento de sincronización en la lista a la que está asociado el usuario.  Por lo tanto, los elementos de la sincronización se deben colocar en orden de prioridad.

>[AZURE.TIP]Después de quitar elementos de sincronización, se debe realizar una sincronización completa.  Debe realizar una sincronización completa después de ordenar los elementos de sincronización.  Haga clic en el botón Sincronizar ahora para realizar una sincronización completa.

## <a name="multi-factor-auth-servers"></a>Servidores de autenticación multifactor
Servidores de autenticación multifactor adicionales se pueden configurar para que actúe como un proxy RADIUS de copia de seguridad, proxy LDAP, o para la autenticación de IIS. La configuración de sincronización se compartirán entre todos los agentes. Sin embargo, solo uno de estos agentes puede tener la ejecución del servicio de servidor de autenticación multifactor. Esta pestaña permite seleccionar el servidor de autenticación multifactor que debe habilitarse para la sincronización.

![Servidores de múltiples multifactor Auth](./media/multi-factor-authentication-get-started-server-dirint/dirint6.png)
