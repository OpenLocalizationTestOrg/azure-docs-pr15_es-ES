<properties
    pageTitle="¿Qué es el registro de autoservicio en Azure? | Microsoft Azure"
    description="Un registro de autoservicio de introducción en Azure, cómo administrar el proceso de suscripción y cómo tomar el control de un nombre de dominio DNS."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/23/2016"
    ms.author="curtand"/>


# <a name="what-is-self-service-signup-for-azure"></a>¿Qué es el registro de autoservicio en Azure?

En este tema se explica el proceso de suscripción autoservicio y cómo tomar el control de un nombre de dominio DNS.  

## <a name="why-use-self-service-signup"></a>¿Por qué usar el registro de autoservicio?

- Obtener a los clientes a los servicios que quieren más rápido.
- Crear ofertas basadas en correo electrónico de un servicio.
- Crear flujos de registro basado en correo electrónico que permiten a los usuarios crear identidades utilizando sus alias de correo electrónico fácil de recordar el trabajo rápidamente.
- Directorios de Azure no administrados pueden realizarse posteriormente en directorios administradas y reutilizar para otros servicios.

## <a name="terms-and-definitions"></a>Términos y definiciones

+ **Autoservicio registrarse**: este es el método por el que un usuario se suscribe a un servicio de nube y una identidad creada automáticamente en Azure Active Directory (AD Azure) basado en su dominio de correo electrónico.
+ **Directorio de Azure no administrado**: este es el directorio donde se crea esa identidad. Un directorio no administrado es un directorio que no tiene administrador global.
+ **Comprobar correo electrónico de usuario**: este es un tipo de cuenta de usuario en Azure AD. Un usuario que tiene una identidad crea automáticamente después de registrarse para una oferta de autoservicio se conoce como un usuario verificado por correo electrónico. Un usuario comprobado por correo electrónico es un miembro de un directorio etiquetado con creationmethod normal = EmailVerified.

## <a name="user-experience"></a>Experiencia de usuario

Por ejemplo, supongamos que un usuario cuyo correo electrónico es Dan@BellowsCollege.com recibe archivos confidenciales por correo electrónico. Los archivos han estado protegidos por Azure Rights Management (RMS de Azure). Pero la organización de Dan, fuelles universitario, no está suscrito a Azure RMS, ni se ha implementado RMS de Active Directory. En este caso, Dan puede iniciar sesión en una suscripción gratuita a RMS para personas para leer los archivos protegidos.

Si Dan es el primer usuario con una dirección de correo electrónico de BellowsCollege.com inicio de sesión de esta oferta, de autoservicio, a continuación, se creará un directorio no administrado para BellowsCollege.com en Azure AD. Si otros usuarios desde el dominio BellowsCollege.com registran para esta oferta o una oferta de autoservicio similar, también tendrán cuentas de usuario verificado por correo electrónico en el mismo directorio no administrado en Azure.

## <a name="admin-experience"></a>Experiencia de administración

Un administrador que posee el nombre de dominio DNS de un directorio de Azure no administrado puede tomar el control o combinar el directorio después de probar la propiedad. Las siguientes secciones explican la experiencia de administración con más detalle, pero aquí tiene un resumen:

- Cuando realiza sobre un directorio de Azure no administrado, simplemente será el administrador global del directorio no administrado. A veces se denomina una adquisición interno.
- Cuando se combina un directorio de Azure no administrado, agregue el nombre de dominio DNS del directorio no administrado al directorio de Azure administrado y se crea una asignación de recursos de los usuarios para los usuarios pueden continuar acceder a los servicios sin interrupciones. A veces se denomina una adquisición externo.

## <a name="what-gets-created-in-azure-active-directory"></a>¿Qué se creará en Azure Active Directory?

#### <a name="directory"></a>directorio

- Se crea un directorio de Azure Active Directory para el dominio, un directorio por dominio.
- El directorio de Azure AD no tiene ningún administrador global.

#### <a name="users"></a>Usuarios

- Para cada usuario que inicia el, se crea un objeto de usuario en el directorio de Azure AD.
- Cada objeto de usuario se marcará como externa.
- Cada usuario tiene acceso al servicio que suscrito.

### <a name="how-do-i-claim-a-self-service-azure-ad-directory-for-a-domain-i-own"></a>¿Cómo se puede reclamar una autoservicio Azure AD directorio para un dominio que soy el propietario?

Puede reclamar una autoservicio Azure AD directorio al realizar la validación de dominio. Validación de dominio demuestra que es el propietario del dominio mediante la creación de registros DNS.

Hay dos formas de realizar una adquisición DNS de un directorio de Azure AD:

- adquisición interno (Administrador descubre un directorio de Azure no administrado y desea convertir en un directorio administrado)
- adquisición externo (administrador intenta agregar un nuevo dominio a su directorio de Azure administrado)

Es podrían que le interese validar el propietario de un dominio porque está realizando sobre un directorio no administrado después de que un usuario realiza el registro de autoservicio o agregar un dominio nuevo a un directorio administrado existente. Por ejemplo, tiene un dominio denominado contoso.com y desea agregar un nuevo dominio denominado contoso.co.uk o contoso.uk.

## <a name="what-is-domain-takeover"></a>¿Qué es la adquisición de dominio?  

Esta sección explica cómo validar que usted es propietario de un dominio

### <a name="what-is-domain-validation-and-why-is-it-used"></a>¿Qué es la validación de dominio y por qué se usa?

Para llevar a cabo operaciones en un directorio, Azure AD requiere que validar la propiedad del dominio DNS.  Validación del dominio permite reclamar el directorio y bien promover el directorio de autoservicio en un directorio administrado, o combinar el directorio de autoservicio en un directorio administrado existente.

## <a name="examples-of-domain-validation"></a>Ejemplos de validación de dominio

Hay dos formas de realizar una adquisición DNS de un directorio:

+ adquisición de interno (por ejemplo, un administrador descubre un directorio no administrado de autoservicio y desea convertir en el directorio administrado)
+ adquisición de externo (por ejemplo, un administrador intenta agregar un dominio nuevo a un directorio administrado)

### <a name="internal-takeover---promote-a-self-service-unmanaged-directory-to-be-a-managed-directory"></a>Adquisición de interno - promover un directorio no administrado autoservicio para ser un directorio administrado

Cuando haga adquisición interno, el directorio se convierte desde un directorio no administrado a un directorio administrado. Debe completar la validación de nombre de dominio DNS, donde crear un registro MX o un registro TXT en la zona DNS. Esta acción:

+ Valide que posee el dominio
+ Hace que el directorio administrado
+ Hace que el administrador global del directorio

Supongamos que un administrador de TI de fuelles universitario descubre que los usuarios de la escuela han suscrito ofertas de autoservicio. El propietario registrado del DNS de nombres BellowsCollege.com, el Administrador de TI puede validar la propiedad del nombre DNS en Azure y, a continuación, tomar el control del directorio no administrado. El directorio se convierte en un directorio administrado y el Administrador de TI se asigna el rol de administrador global para el directorio BellowsCollege.com.

### <a name="external-takeover---merge-a-self-service-directory-into-an-existing-managed-directory"></a>Adquisición de externo - combinar un directorio de autoservicio en un directorio administrado existente

Una adquisición externo, ya tiene un directorio administrado y desea que todos los usuarios y grupos desde un directorio no administrado para unirse a ese directorio administrado, en lugar de dos propios separan los directorios.

Como administrador de un directorio administrado, agregar un dominio y ese dominio pasa a tener un directorio no administrado asociado.

Por ejemplo, supongamos que es un administrador de TI y ya tiene un directorio administrado para Contoso.com, un nombre de dominio que está registrado en su organización. Descubra que los usuarios de su organización han realizado autoservicio hacia arriba para una oferta usando el nombre de dominio de correo electrónico user@contoso.co.uk, que es otro nombre de dominio que es propietario de su organización. Los usuarios actualmente tienen cuentas en un directorio no administrado contoso.co.uk.

No desea administrar dos directorios independientes, para combinar el directorio no administrado contoso.co.uk al directorio de TI administrado existente para contoso.com.

Adquisición de externo sigue el mismo proceso de validación de DNS que adquisición interno.  Diferencia de ser: los usuarios y servicios se reasignarán en el directorio de TI administrado.

#### <a name="whats-the-impact-of-performing-an-external-takeover"></a>¿Cuál es el impacto de realizar una adquisición externo?

Con una adquisición externo, se crea una asignación de recursos de los usuarios para que los usuarios puedan tener acceso a servicios sin interrupciones. Muchas aplicaciones, incluyendo RMS para personas, controlan la asignación de recursos de los usuarios bien y los usuarios pueden seguir tener acceso a estos servicios sin cambios. Si una aplicación no controla la asignación de recursos de los usuarios un modo eficaz, adquisición externo puede estar bloqueado explícitamente para evitar que los usuarios una experiencia deficiente.

#### <a name="directory-takeover-support-by-service"></a>soporte técnico en recogida por el servicio de directorio

Los siguientes servicios admiten actualmente adquisición:

- RMS


Los siguientes servicios pronto admitirá adquisición:

- PowerBI

Siguiente no y requiere acción adicional de administración para migrar datos de usuario después de una adquisición externo.

- SharePoint o OneDrive


## <a name="how-to-perform-a-dns-domain-name-takeover"></a>Cómo llevar a cabo una adquisición de nombre de dominio DNS

Tiene varias opciones para saber cómo realizar una validación del dominio (y hacer una adquisición si lo desea):

1.  Portal de administración de Azure

    Se activa una adquisición realizando una adición del dominio.  Si ya existe un directorio del dominio, tendrá la opción de realizar una adquisición externo.

    Inicie sesión en el portal de Azure con sus credenciales.  Vaya al directorio existente y, a continuación, en **Agregar dominio**.

2.  Office 365

    Puede usar las opciones en la página [Administrar dominios](https://support.office.com/article/Navigate-to-the-Office-365-Manage-domains-page-026af1f2-0e6d-4f2d-9b33-fd147420fac2/) en Office 365 para trabajar con sus dominios y registros DNS. Consulte [comprobar su dominio en Office 365](https://support.office.com/article/Verify-your-domain-in-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611/).

3.  Windows PowerShell

    Los pasos siguientes son necesarios para realizar una validación con Windows PowerShell.

    Paso    |   Cmdlet para usar
    ------- | -------------
    Crear un objeto de credenciales | Get-Credential
    Conectarse a Azure AD | MsolService conectar
    obtener una lista de dominios   | Get-MsolDomain
    Crear un reto  | Get-MsolDomainVerificationDns
    Crear el registro DNS   | Haga lo siguiente en el servidor DNS
    Comprobar el desafío    | MsolEmailVerifiedDomain confirmar

Por ejemplo:

1. Conectarse a Azure AD con las credenciales que se utilizaron para responder a la oferta de autoservicio: módulo de importación MSOnline $msolcred = get-credential conectar-msolservice-$msolcred de credenciales

2. Obtener una lista de dominios:

    Get-MsolDomain

3. A continuación, ejecute el cmdlet Get-MsolDomainVerificationDns para crear un reto:

    Get-MsolDomainVerificationDns – DomainName *su_nombre_de_dominio* : modo DnsTxtRecord

    Por ejemplo:

    Get-MsolDomainVerificationDns: el nombre de dominio contoso.com: modo DnsTxtRecord

4. Copie el valor (el desafío) que se devuelve desde este comando.

    Por ejemplo:

    MS = 32DD01B82C05D27151EA9AE93C5890787F0E65D9

5. En el espacio de nombres DNS público, cree un registro txt DNS que contiene el valor que ha copiado en el paso anterior.

    El nombre de este registro es el nombre del dominio principal, si crear este registro de recursos mediante la función de DNS de Windows Server, deje el registro nombre en blanco y justo de pegar el valor en el cuadro de texto

6. Ejecute el cmdlet confirmar MsolDomain para comprobar el desafío:

    MsolEmailVerifiedDomain confirmar - DomainName *su_nombre_de_dominio*

    Por ejemplo:

    MsolEmailVerifiedDomain confirmar - DomainName contoso.com

Un reto correcta vuelve a la pregunta sin un error.

## <a name="how-do-i-control-self-service-settings"></a>¿Cómo controlar la configuración de autoservicio?

Los administradores tienen dos controles de autoservicio de hoy. Pueden controlar:

- Si los usuarios pueden unirse el directorio por correo electrónico.
- Si los usuarios pueden licencias a sí mismos para aplicaciones y servicios.


### <a name="how-can-i-control-these-capabilities"></a>¿Cómo puedo controlar estas capacidades?

Un administrador puede configurar estas funciones con estos parámetros de cmdlet Set-MsolCompanySettings de Azure AD:

+ **AllowEmailVerifiedUsers** controla si un usuario puede crear o unirse a un directorio no administrado. Si dicho parámetro $false, ningún usuario verificado por correo electrónico puede unirse al directorio.
+ **AllowAdHocSubscriptions** controla la posibilidad de que los usuarios realicen autoservicio hacia arriba. Si dicho parámetro $false, ningún usuario puede realizar el registro de autoservicio.


### <a name="how-do-the-controls-work-together"></a>¿Cómo los controles funcionan conjuntamente?

Estos dos parámetros pueden usarse junto a definir un control más preciso sobre autoservicio hacia arriba. Por ejemplo, el siguiente comando permite a los usuarios realizar autoservicio, pero solo si los usuarios ya tienen una cuenta de Azure AD (es decir, los usuarios que se necesitan una cuenta de verificado por correo electrónico que se cree no pueden realizar autoservicio arriba):

    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true

El siguiente diagrama explica todas las distintas combinaciones de estos parámetros y las condiciones para el directorio y autoservicio resultantes hacia arriba.

![][1]

Para obtener más información y ejemplos de cómo usar estos parámetros, consulte [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx).

## <a name="see-also"></a>Vea también

-  [Cómo instalar y configurar PowerShell de Azure](../powershell-install-configure.md)

-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)

-  [Referencia de Cmdlet de Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx)

-  [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
