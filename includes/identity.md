Administración de identidades es tan importante en la nube pública como local. Para ayudarle a esto, Azure es compatible con varias tecnologías de identidad de nube diferente. Estos incluyen:

- Puede ejecutar Windows Server Active Directory (denominado normalmente solo AD) en la nube mediante máquinas virtuales creadas con máquinas virtuales de Azure. Este enfoque tiene sentido cuando usa Azure ampliar su centro de datos local en la nube.


- Puede usar Azure Active Directory para dar a los usuarios de inicio de sesión único a aplicaciones de [Software como servicio (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) . Por ejemplo, Office 365 de Microsoft usa esta tecnología y aplicaciones que se ejecutan en otras plataformas de nube o Azure también pueden usar.


- Aplicaciones que se ejecutan en la nube o local pueden usar el Control de acceso de Azure Active Directory para que los usuarios puedan inicie sesión con las identidades de Facebook, Google, Microsoft y otros proveedores de identidades.


Este artículo describen los tres de estas opciones.

## <a name="table-of-contents"></a>Tabla de contenido

- [Ejecución de Active Directory de Windows Server en máquinas virtuales](#adinvm)

- [Uso de Azure Active Directory](#ad)

- [Usar el Control de acceso de Azure Active Directory](#ac)


## <a name="adinvm"></a>Ejecución de Active Directory de Windows Server en máquinas virtuales

Ejecuta AD de Windows Server en máquinas virtuales de Windows Azure es muy similar a ejecutar local. [Figura 1](#fig1) muestra un ejemplo típico de aspecto.

![Azure Active Directory en Máquina Virtual](./media/identity/identity_01_ADinVM.png)


<a name="Fig1"></a>Figura 1: Puede ejecutar Active Directory de Windows Server en máquinas virtuales de Azure conectados al centro de datos de la organización local mediante red Virtual de Azure.

En el ejemplo que se muestra aquí, se está ejecutando AD de Windows Server en máquinas virtuales creadas con máquinas virtuales de Azure, la tecnología de la plataforma de IaaS. Estas máquinas virtuales y algunas otras se agrupan en una red virtual conectada a un centro de datos locales con Azure una red Virtual. La red virtual talla fuera de un grupo de máquinas virtuales de nube que interactúan con la red local a través de una conexión de red privada virtual (VPN). Hacer esto le permite estas máquinas virtuales Azure aspecto otra subred al centro de datos local. Como se muestra en la ilustración, dos de esas VM ejecuta los controladores de dominio de AD de Windows Server. Puede ejecutar aplicaciones, como SharePoint o que se usa para desarrollo y pruebas de otra manera, como las demás máquinas virtuales en la red virtual. El centro de datos local también está ejecutando dos controladores de dominio de AD de Windows Server.

Hay varias opciones para conectar los controladores de dominio en la nube con los que ejecutan local:

- Hacer que todos ellos en parte de un dominio de Active Directory único.

- Crear independiente AD dominios local y en la nube que forman parte del mismo bosque.

- Crear bosques AD independientes en la nube y local y después conectar los bosques mediante confianzas entre bosques o Windows Server Active Directory Federation Services (AD FS), que también puede ejecutar en máquinas virtuales de Windows Azure.

Se realiza independientemente de la opción, un administrador debe asegurarse de que las solicitudes de autenticación de usuarios locales vayan a los controladores de dominio de nube solo cuando sea necesario, ya que el vínculo a la nube es probable que sea más lento que redes local. Otro factor a tener en cuenta en conexión nube y los controladores de dominio local es el tráfico generado por la replicación. Controladores de dominio en la nube suelen en sus propios sitios de AD, que permite a un administrador programar la frecuencia con la replicación se realiza. Cargos Azure para el tráfico enviado de un centro de datos de Azure, aunque no bytes enviados en que puede afectar a las opciones de replicación del administrador. También es necesario señalar que mientras Azure proporcionan su propia compatibilidad del sistema de nombres de dominio (DNS), este servicio falta características requeridas Active Directory (como la compatibilidad con los registros DNS dinámico y SRV). Por este motivo, ejecuta Windows Server AD en Azure requiere la configuración de sus propios servidores DNS en la nube.

Ejecuta AD de Windows Server en máquinas virtuales de Azure puede sentido en diversas situaciones diferentes. Estos son algunos ejemplos:

- Si usa máquinas virtuales de Azure como una extensión de su propio centro de datos, puede ejecutar aplicaciones en la nube que necesitan controladores de dominio local para controlar cosas como las solicitudes de autenticación de Windows integrada o consultas LDAP. SharePoint, por ejemplo, interactúa con frecuencia con Active Directory y, a continuación, aunque es posible ejecutar una granja de SharePoint en Azure con un directorio local, configurar controladores de dominio en la nube, significativamente mejorará el rendimiento. (Es importante tener en cuenta que esto no es necesario realizar, una gran cantidad de aplicaciones se pueden ejecutar correctamente en la nube con sólo los controladores de dominio local).

- Supongamos que una sucursal planes carece de los recursos para ejecutar los controladores de su propio dominio. En la actualidad, los usuarios deben autenticarse en controladores de dominio en el otro lado del mundo: tardan inicios de sesión. Ejecución de un centro de datos más cerca de Microsoft Active Directory en Azure puede acelerar esto sin necesidad de varios servidores en la sucursal.

- Una organización que use Azure para recuperación puede mantener un conjunto pequeño de activos máquinas virtuales en la nube, incluidos un controlador de dominio. A continuación, podrá prepararse para expandir este sitio según sea necesario para tomar el control de errores en otro lugar.

También existen otras posibilidades. Por ejemplo, no tiene la obligación conectar Windows Server AD en la nube a un centro de datos local. Si desea ejecutar una granja de SharePoint que sirve un determinado conjunto de usuarios, por ejemplo, todos ellos se inicie sesión en únicamente con las identidades en la nube, puede crear un bosque independiente de Azure. Cómo usar esta tecnología depende de qué son los objetivos. (Para información más detallada sobre el uso de Windows Server AD con Azure, [Consulte aquí](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx).)

## <a name="ad"></a>Uso de Azure Active Directory

A medida que aplicaciones SaaS más comunes, provocan una pregunta obvia: ¿qué tipo de servicio de directorio deben usar estas aplicaciones en la nube? Respuesta de Microsoft a esa pregunta es Azure Active Directory.

Existen dos opciones principales para usar este servicio de directorio de la nube:

- Personas y las organizaciones que usan aplicaciones de SaaS solo pueden confiar en Azure Active Directory como su servicio de directorio único.

- Las organizaciones que ejecutan Windows Server Active Directory pueden conectar su directorio local con Azure Active Directory y usarlo para dar a su usuarios inicio de sesión único a aplicaciones SaaS.


[Figura 2](#fig2) muestra la primera de estas dos opciones, donde Azure Active Directory es todo lo necesario.

![Azure Active Directory en Máquina Virtual](./media/identity/identity_02_AD.png)

<a name="fig2"></a>Figura 2: Azure Active Directory ofrece usuarios inicio una organización de sesión único a aplicaciones de SaaS, incluidos Office 365.

Como se muestra en la figura, Azure AD es un servicio de varios inquilino. Esto significa que puede admitir simultáneamente muchas organizaciones diferentes, almacenar la información de directorio acerca de los usuarios en cada uno de ellos. En este ejemplo, un usuario de la organización A está intentando tener acceso a una aplicación de SaaS. Esta aplicación podría ser parte de Office 365, como SharePoint Online, o puede ser otra cosa: aplicaciones ajenas a Microsoft también pueden usar esta tecnología. Porque Azure AD es compatible con el protocolo de SAML 2.0, todo lo necesario desde una aplicación es la capacidad para interactuar con este estándar de la industria. (De hecho, pueden ejecutar aplicaciones que utilicen Azure AD en el centro de datos, no solo un centro de datos Azure.)

El proceso comienza cuando el usuario tiene acceso a una aplicación de SaaS (paso 1). Para usar esta aplicación, el usuario debe presentar un símbolo de Azure AD.

Este token contiene información que identifica el usuario y está firmado digitalmente por Azure AD. Para obtener el token, el usuario se autentica a sí mismo en Azure AD al proporcionar un nombre de usuario y una contraseña (paso 2). Azure AD, a continuación, devuelve el token que necesita (paso 3).

Este token se envía a la aplicación de SaaS (paso 4), que valida firma del token y utiliza su contenido (paso 5). Normalmente, la aplicación usará la información de identidad que contiene el token para decidir qué información se permite al usuario de Access y quizás de otras maneras.

Si la aplicación necesita más información sobre el usuario que lo que se incluye en el token, puede solicitar esto directamente desde Azure AD con la API de Azure AD Graph (paso 6). En la versión inicial de Azure AD, el esquema de directorio es muy sencillo: contiene solo los usuarios y grupos y las relaciones entre ellas. Aplicaciones pueden utilizar esta información para obtener información sobre las conexiones entre los usuarios. Por ejemplo, supongamos que una aplicación necesita saber quién es el Administrador de este usuario decidir si ha concedido acceso a algunas fragmento de datos. Puede obtener consultando Azure AD a través de la API de gráfico.

La API de gráfico usa un protocolo de REST normal, lo que resulta fácil de usar de la mayoría de los clientes, incluidos los dispositivos móviles. La API también es compatible con las extensiones definidas por OData, agregar elementos como un lenguaje de consulta para permitir a los clientes tener acceso a datos de formas más útiles. (Para obtener más información sobre OData, consulte [Introducción a OData](http://download.microsoft.com/download/E/5/A/E5A59052-EE48-4D64-897B-5F7C608165B8/IntroducingOData.pdf)). Porque la API de gráfico se puede utilizar para obtener información sobre las relaciones entre los usuarios, permite aplicaciones a comprender el gráfico social que está incrustado en el esquema de Azure AD de una organización concreta (lo que se denomina la API de gráfico). Y para autenticarse en Azure AD para las solicitudes de la API de gráfico, una aplicación usa OAuth 2.0.

Si una organización no utiliza Active Directory de Windows Server, y no tiene servidores local o dominios - a se basa únicamente en las aplicaciones de la nube que utilizan Azure AD, usando solo este directorio de nube daría usuarios inicio de la empresa de sesión único a todas ellas. Aún mientras este escenario obtiene más comunes de cada día, continuará utilizando la mayoría de organizaciones locales dominios creados con Active Directory de Windows Server. Azure AD tiene una útil función aquí, como se muestra en [la figura 3](#fig3) .

![Azure Active Directory en una máquina Virtual](./media/identity/identity_03_AD.png)
<a id="fig3"></a>figura 3: una organización puede federarse Windows Server Active Directory con Azure Active Directory para dar a su usuarios inicio de sesión único a aplicaciones SaaS.

En este escenario, un usuario de la organización B desee acceder a una aplicación de SaaS. Antes de hacerlo, los administradores de directorio de la organización deben establecer una relación de federación con Azure AD con AD FS, como se muestra en la ilustración. Los administradores también deben configurar la sincronización de datos entre local Windows Server la organización AD y Azure AD. Esto copia automáticamente información de usuario y grupo desde el directorio local en Azure AD. Tenga en cuenta lo permite: de hecho, la organización está ampliando su directorio local en la nube. Combinación de Windows Server AD y Azure AD de esta forma, la organización le ofrece un servicio de directorio que puedan administrarse como una entidad única, mientras sigue teniendo un espacio en ambos local y en la nube.

Para usar Azure AD, el usuario primero inicie sesión su dominio de Active Directory local como de costumbre (paso 1). Cuando intenta obtener acceso a la aplicación de SaaS (paso 2), los resultados del proceso de federación en Azure AD le emite un token de esta aplicación (paso 3). (Para obtener más información sobre el funcionamiento de la federación, consulte [identidad basada en notificaciones para Windows: tecnologías y escenarios](http://www.davidchappell.com/writing/white_papers/Claims-Based_Identity_for_Windows_v3.0--Chappell.docx).) Como antes, este token contiene información que identifica el usuario y, a continuación, se ha firmado digitalmente por Azure AD. Este token se envía a la aplicación de SaaS (paso 4), que valida firma del token y utiliza su contenido (paso 5). Y se encuentra en el escenario anterior, la SaaS aplicación puede utilizar la API de gráfico para obtener más información acerca de este usuario si es necesario (paso 6).

En la actualidad, Azure AD no reemplaza completa local Windows Server AD. Como se mencionó anteriormente, el directorio de nube tiene un mucho más sencillo esquema, y también falta cosas como la directiva de grupo, la capacidad de almacenar información sobre equipos y soporte técnico para LDAP. (De hecho, no se puede configurar un equipo Windows para que los usuarios puedan iniciar sesión en ella usando nada pero Azure AD - esto no es un escenario admitido). En su lugar, los objetivos iniciales de Azure AD incluyen permitir que aplicaciones de acceso de los usuarios empresariales en la nube sin mantener un inicio de sesión independiente y liberar local Administradores del directorio de sincronizar manualmente su directorio local con todas las aplicaciones SaaS que su organización usa. Con el tiempo, sin embargo, espere este servicio de directorio de la nube para abordar una amplia gama de escenarios.

## <a name="ac"></a>Usar el Control de acceso de Azure Active Directory

Tecnologías de identidades basada en nube pueden usarse para solucionar una gran variedad de problemas. Azure Active Directory puede dar usuarios inicio una organización de sesión único para varias aplicaciones de SaaS, por ejemplo. Pero también se pueden utilizar tecnologías de identidad en la nube de otras maneras.

Supongamos, por ejemplo, una aplicación que desea permitir que sus usuarios inicie sesión con tokens emitidos por varios *proveedores de identidades (IdPs)*. Una gran cantidad de proveedores de identidades diferentes existe en la actualidad, como Facebook, Google, Microsoft y otras personas, y aplicaciones con frecuencia que los usuarios puedan iniciar sesión en una de estas identidades de. ¿Por qué debe preocuparse una aplicación para mantener su propia lista de usuarios y contraseñas cuando en su lugar puede confiar en identidades ya existentes? Aceptar identidades existentes simplifica la vida tanto para los usuarios, que tienen una menos el nombre de usuario y contraseña para recordar como para las personas que crean la aplicación, que ya no necesite para mantener sus propias listas de nombres de usuario y contraseñas.

Pero mientras cada proveedor de identidades emite algún tipo de token, esos símbolos no estándares: cada IdP tiene su propio formato. Además, la información de esos símbolos también no estándar. Una aplicación que desea aceptar tokens emitidos por, diga, Facebook, Google y Microsoft se enfrenta con el desafío de escribir el código único para controlar cada uno de estos formatos diferentes.

Pero, ¿por qué hacer esto? ¿Por qué no crear en su lugar intermediario que puede generar un único formato token con una representación común de la información de identidad? Este enfoque debería simplificarse la vida para los desarrolladores que crean aplicaciones, ya que tendrán manejar solo tipo de token. Azure Active Control de acceso de directorio hace exactamente esto, proporcionar a intermediario en la nube para trabajar con diversos símbolos. [La figura 4](#fig4) muestra cómo funciona

![Azure Active Directory en una máquina Virtual](./media/identity/identity_04_IdentityProviders.png)
<a id="fig4"></a>figura 4: Control de acceso de Azure Active Directory facilita la aplicaciones Aceptar los símbolos de identidad emitidos por proveedores de identidades diferentes.

El proceso comienza cuando un usuario intenta obtener acceso a la aplicación desde un explorador. La aplicación redirige le un IdP de su elección (y que también confía la aplicación). Autentica su propio a este IdP como, escriba un nombre de usuario y una contraseña (paso 1), y el IdP devuelve un símbolo que contiene información sobre su (paso 2).

Como se muestra en la figura, Control de acceso es compatible con un rango de diferentes IdPs basada en la nube, incluidas las cuentas creadas por Google, Yahoo, Facebook, Microsoft (anteriormente conocida como Windows Live ID) y cualquier proveedor de OpenID. También es compatible con las identidades creadas con Azure Active Directory y, a través de la federación con AD FS, Active Directory de Windows Server. El objetivo es cubrir las identidades más utilizadas en la actualidad, si haya emitido por IdPs en la nube o local.

Una vez que el explorador del usuario tiene un token IdP desde su elegido IdP, envía este token a Control de acceso (paso 3). Control de acceso valida el token, asegurarse de que realmente fue emitido por este IdP, a continuación, se crea un nuevo token según las reglas que se han definido para esta aplicación. Como Azure Active Directory, Control de acceso es un servicio de múltiples arrendatario, pero los inquilinos son aplicaciones en lugar de las organizaciones de cliente. Cada aplicación puede obtener su propio espacio de nombres, como se muestra en la ilustración y puede definir varias reglas sobre autorización y mucho más.

Estas reglas le permiten Administrador de la aplicación a definir el modo de transformación de tokens desde diversos IdPs en un símbolo de Control de acceso. Por ejemplo, si IdPs diferentes usan distintos tipos para representar los nombres de usuario, las reglas de Control de acceso pueden transformar todas estas en un tipo de nombre de usuario comunes. Control de acceso, a continuación, envía este token nuevo al explorador (paso 4), que se envía a la aplicación (paso 5). Una vez que tiene el token de Control de acceso, la aplicación comprueba que este token realmente fue emitido por el Control de acceso, a continuación, usa la información que contiene (paso 6).

Mientras este proceso puede parecer un poco complicado, lo vida bastante más sencillo para el creador de la aplicación. En lugar de controlar diversos símbolos que contiene información diferente, la aplicación puede aceptar identidades emitidas por varios proveedores de identidades mientras sigue recibiendo solo un único token con información familiar. Además, en lugar de requerir cada aplicación estén configurados para confiar en diversos IdPs, Control de acceso en su lugar mantienen las relaciones de confianza: solo debe confiar en una aplicación.

Es necesario señalar que nada de Control de acceso está ligada a Windows - bien puede utilizarse con una aplicación de Linux que acepta solo las identidades de Google y Facebook. Y aunque el Control de acceso es parte de la familia de Azure Active Directory, se puede considerar como un servicio totalmente distinto de lo que se describe en la sección anterior. Aunque ambas tecnologías funcionan con la identidad, dirección problemas muy diferentes (aunque Microsoft ha comentado que espera integrar los dos en algún momento).

Trabajar con la identidad es importante en casi todas las aplicaciones. Es el objetivo de Control de acceso facilitar a los desarrolladores a crear aplicaciones que aceptan las identidades de los proveedores de identidades diverso. Si coloca este servicio en la nube, Microsoft realizó disponibles para cualquier aplicación que se ejecuta en cualquier plataforma.

##<a name="about-the-author"></a>Acerca de la autora

David Chappell es el director de Chappell & Associates [www.davidchappell.com](http://www.davidchappell.com) en San Francisco, California.
