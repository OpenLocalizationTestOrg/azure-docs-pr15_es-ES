<properties
    pageTitle="Introducción a Azure clave depósito | Microsoft Azure"
    description="Use este tutorial para ayudarle a empezar a trabajar con Azure depósito de clave para crear un contenedor consolidado en Azure, almacenar y administrar las claves de cifrado y la información confidencial en Azure."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/24/2016"
    ms.author="cabailey"/>

# <a name="get-started-with-azure-key-vault"></a>Introducción a Azure clave depósito #
Azure depósito clave está disponible en la mayoría de las regiones. Para obtener más información, consulte la [página de precios de depósito de clave](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introducción  
Use este tutorial para ayudarle a empezar a trabajar con Azure depósito de clave para crear un contenedor consolidado (un depósito) en Azure, almacenar y administrar las claves de cifrado y la información confidencial en Azure. Le guiará por el proceso de uso de PowerShell de Azure para crear un depósito que contiene una clave o contraseña que puede utilizar con una aplicación de Azure. A continuación, se muestra cómo puede usar una aplicación de dicha clave o contraseña.

**Tiempo estimado para completar:** 20 minutos

>[AZURE.NOTE]  Este tutorial no incluye instrucciones acerca de cómo escribir la aplicación de Azure que uno de los pasos incluye, es decir, cómo autorizar una aplicación para usar una clave o un secreto en depósito de clave.
>
>Este tutorial usa PowerShell de Azure. Para obtener instrucciones de la interfaz de línea de comandos de varias plataformas, consulte [este tutorial equivalente](key-vault-manage-with-cli.md).

Para obtener información general acerca de la cámara de clave de Azure, consulte [¿Qué es depósito de clave de Azure?](key-vault-whatis.md)

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, debe tener el siguiente:

- Una suscripción a Microsoft Azure. Si no tiene una, puede registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).
- PowerShell Azure, **versión mínima de 1.1.0**. Para instalar Azure PowerShell y asociar con su suscripción de Azure, consulte [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md). Si ya ha instalado Azure PowerShell y no conoce la versión, desde la consola de PowerShell de Azure, escriba `(Get-Module azure -ListAvailable).Version`. Cuando haya Azure PowerShell versión 0.9.1 a través de 0.9.8 instalado, aún puede usar este tutorial con cambios menores. Por ejemplo, debe utilizar el `Switch-AzureMode AzureResourceManager` comando y algunos de los comandos de Azure clave depósito han cambiado. Para obtener una lista de los cmdlets de depósito clave para versiones 0.9.1 a través de 0.9.8, vea [Cmdlets de depósito de clave de Azure](https://msdn.microsoft.com/library/azure/dn868052\(v=azure.98\).aspx). 
- Una aplicación que se configurarán para usar la clave o contraseña que cree en este tutorial. Una aplicación de ejemplo está disponible desde el [Centro de descarga de Microsoft](http://www.microsoft.com/en-us/download/details.aspx?id=45343). Para obtener instrucciones, vea el archivo Léame adjunto.


En este tutorial se ha diseñado para principiantes de PowerShell de Azure, pero se supone que entender los conceptos básicos, como los módulos, cmdlets y sesiones. Para obtener más información, consulte [Introducción a Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx).

Para obtener ayuda detallada para cualquier cmdlet que se observan en este tutorial, use el cmdlet **Get-Help** .

    Get-Help <cmdlet-name> -Detailed

Por ejemplo, para obtener ayuda para el cmdlet **AzureRmAccount de inicio de sesión** , escriba:

    Get-Help Login-AzureRmAccount -Detailed

También puede leer los siguientes tutoriales para familiarizarse con el Administrador de recursos de Azure en Azure PowerShell:

- [Cómo instalar y configurar PowerShell de Azure](../powershell-install-configure.md)
- [Usar PowerShell Azure con el Administrador de recursos](../powershell-azure-resource-manager.md)


## <a id="connect"></a>Conectarse a las suscripciones ##

Iniciar una sesión de Azure PowerShell e inicie sesión en su cuenta de Azure con el siguiente comando:  

    Login-AzureRmAccount 

Tenga en cuenta que, si está utilizando una instancia específica del uso de Azure, por ejemplo, administración pública de Azure el entorno parámetro - con este comando. Por ejemplo:`Login-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)`

En la ventana emergente del explorador, escriba su nombre de usuario de la cuenta de Azure y la contraseña. PowerShell Azure Obtiene todas las suscripciones que están asociados a esta cuenta y de forma predeterminada, se utiliza la primera.

Si tiene varias suscripciones y desea especificar una específica para depósito de clave de Azure, escriba lo siguiente para ver las suscripciones para su cuenta:

    Get-AzureRmSubscription

A continuación, para especificar la suscripción para que utilice, escriba:

    Set-AzureRmContext -SubscriptionId <subscription ID>

Para obtener más información acerca de cómo configurar Azure PowerShell, vea [cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).


## <a id="resource"></a>Crear un nuevo grupo de recursos ##

Al usar el Administrador de recursos de Azure, se crean todos los recursos relacionados dentro de un grupo de recursos. Se creará un nuevo grupo de recursos denominado **ContosoResourceGroup** para este tutorial:

    New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East Asia'


## <a id="vault"></a>Crear un depósito clave ##

Usar el cmdlet [AzureRmKeyVault de nuevo](https://msdn.microsoft.com/library/azure/mt603736\(v=azure.300\).aspx) para crear un depósito clave. Este cmdlet tiene tres parámetros obligatorios: un **nombre de grupo de recursos**, un **nombre de depósito clave**y la **ubicación geográfica**.

Por ejemplo, si utiliza el nombre de la cámara de **ContosoKeyVault**, el nombre del grupo de recursos de **ContosoResourceGroup**y la ubicación de **Asia oriental**, escriba:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

El resultado de este cmdlet muestra propiedades del depósito clave que acaba de crear. Las dos propiedades más importantes son:

- **Nombre de depósito**: en el ejemplo, esta es **ContosoKeyVault**. Use este nombre para otros cmdlets depósito de clave.
- **URI del depósito**: en el ejemplo, esta es https://contosokeyvault.vault.azure.net/. Las aplicaciones que utilizan la cámara a través de su API de REST deben utilizar este URI.

Su cuenta de Azure ahora está autorizado para realizar operaciones en este depósito clave. Todavía, nadie más está.

>[AZURE.NOTE]  Si ve el error de **la suscripción no está registrada para usar el espacio de nombres 'Microsoft.KeyVault'** al intentar crear su nuevo depósito clave, ejecute `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"` y, a continuación, vuelva a ejecutar el comando AzureRmKeyVault de nuevo. Para obtener más información, vea [Registrar AzureRmResourceProvider](https://msdn.microsoft.com/library/azure/mt759831\(v=azure.300\).aspx).
>

## <a id="add"></a>Agregar una clave o secreto al depósito clave ##

Si desea depósito de clave de Azure para crear una clave de software protegido por usted, use el cmdlet [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048\(v=azure.300\).aspx) y escriba lo siguiente:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'

Sin embargo, si tiene una clave de software protegido existente en una. Archivo PFX que guardó en la unidad C:\ en un archivo llamado softkey.pfx que desea cargar en depósito de clave de Azure, escriba lo siguiente para establecer la variable **securepfxpwd** una contraseña de **123** para el. Archivo PFX:

    $securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force

A continuación, escriba lo siguiente para importar la clave de la. Archivo PFX, que protege la clave por el software en el servicio de depósito de clave:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd


Ahora puede hacer referencia a esta clave que creó o cargado en depósito de clave de Azure, utilizando su URI. Utilice **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** obtener siempre la versión más reciente y **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** para obtener esta versión específica.  

Para mostrar el URI de esta clave, escriba:

    $Key.key.kid

Para agregar un secreto a la cámara, que es una contraseña denominada SQLPassword y tiene el valor de Pa$ $w0rd en depósito de clave de Azure, convierta primero el valor de Pa$ $w0rd en una cadena segura escribiendo lo siguiente:

    $secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force

A continuación, escriba lo siguiente:

    $secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue

Ahora puede hacer referencia a esta contraseña que haya agregado a Azure clave depósito, utilizando su URI. Utilice **https://ContosoVault.vault.azure.net/secrets/SQLPassword** obtener siempre la versión más reciente y **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** para obtener esta versión específica.

Para mostrar el URI para este secreto, escriba:

    $secret.Id

Vamos a ver la clave o el secreto que acaba de crear:

- Para ver la clave, escriba:`Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'`
- Para ver el secreto, escriba:`Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`

Ahora, su cámara clave y clave o secreto está listo para usar las aplicaciones. Debe autorizar aplicaciones usarlas.  

## <a id="register"></a>Registrar una aplicación con Azure Active Directory ##

Este paso normalmente se haría un desarrollador, en un equipo distinto. No es específico de Azure clave depósito, pero se incluyen aquí para integridad.


>[AZURE.IMPORTANT] Para completar el tutorial, su cuenta, la cámara y la aplicación que se registrará en este paso debe estar en el mismo directorio de Azure.

Las aplicaciones que usan un depósito clave deben autenticar mediante un símbolo de Azure Active Directory. Para ello, el propietario de la aplicación debe registrar la aplicación en su Azure Active Directory. Al final del registro, el propietario de la aplicación obtiene los siguientes valores:


- Un **Identificador de la aplicación** (también conocido como un identificador de cliente) y la **clave de autenticación** (también conocido como el secreto compartido). La aplicación debe presentar estos dos valores para Azure Active Directory para obtener un token. ¿Cómo se configura la aplicación para hacer esto depende de la aplicación. Para la aplicación de ejemplo depósito de clave, el propietario de la aplicación establece estos valores en el archivo app.config.

Para registrar la aplicación en Azure Active Directory:

1. Inicie sesión en el portal de clásico de Azure.
2. En la parte izquierda, haga clic en **Active Directory**y, a continuación, seleccione el directorio en el que se registrará la aplicación. <br> <br> **Nota:** Debe seleccionar el mismo directorio que contenga la suscripción de Azure con los que ha creado su cámara clave. Si no sabe qué directorio esto es, haga clic en **configuración**, identificar la suscripción con el que ha creado su cámara clave y anote el nombre del directorio que se muestra en la última columna.

3. Haga clic en **aplicaciones**. Si no hay aplicaciones se han agregado al directorio, esta página muestra solo el vínculo **Agregar una aplicación** . Haga clic en el vínculo o, como alternativa, puede hacer clic en **Agregar** en la barra de comandos.
4.  En el asistente **Agregar aplicación** , en la **¿Qué desea hacer?** página, haga clic en **Agregar una aplicación se desarrolla mi organización**.
5.  En la página **Díganos acerca de la aplicación** , especifique un nombre para la aplicación y, a continuación, seleccione **O de aplicaciones WEB API WEB** (opción predeterminada). Haga clic en el icono **siguiente** .
6.  En la página de **Propiedades de la aplicación** , especifique la **Dirección URL de inicio de sesión** y **URI de ID de aplicación** para la aplicación web. Si la aplicación no tiene estos valores, puede crearlos para este paso (por ejemplo, podría especificar http://test1.contoso.com para los dos cuadros). No importa si existen estos sitios. Importante es que la aplicación identificador URI para cada aplicación es diferente para cada aplicación en el directorio. El directorio usa esta cadena para identificar la aplicación.
7.  Haga clic en el icono **completado** para guardar los cambios en el asistente.
8.  En la página de **Inicio rápido** , haga clic en **Configurar**.
9.  Desplácese hasta la sección de **claves** , seleccione la duración y, a continuación, haga clic en **Guardar**. La página se actualiza y muestra un valor de clave. Debe configurar la aplicación con este valor de clave y el valor del **Identificador de cliente** . (Las instrucciones para esta configuración son específicos de la aplicación).
10. Copie el valor del identificador de cliente de esta página, que se utilizará en el paso siguiente para establecer permisos en su cámara.

## <a id="authorize"></a>Autorizar la aplicación para utilizar la clave o el secreto ##

Para autorizar la aplicación para tener acceso a la clave o el secreto de la caja fuerte, use el cmdlet  [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625\(v=azure.300\).aspx) .

Por ejemplo, si el nombre de la cámara es **ContosoKeyVault** y la aplicación que desea autorizar tiene un identificador de cliente de 8f8c4bbd 485b 45fd 98f7 ec6300b7b4ed y desea autorizar la aplicación para descifrar e inicie sesión con las teclas en la caja fuerte, ejecute lo siguiente:


    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

Si desea autorizar esa misma aplicación leer información confidencial en su cámara, ejecute lo siguiente:


    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get

## <a id="HSM"></a>Si desea usar un módulo de seguridad de hardware (HMS) ##

Para obtener seguridad adicional, puede importar o generar claves en módulos de seguridad de hardware (HSM) que nunca deje el límite HSM. El HSM son FIPS 140-2 nivel 2 validar. Si este requisito no se aplica a usted, omita esta sección y vaya a [eliminar el depósito clave y claves asociadas e información confidencial](#delete).

Para crear estas teclas protegido HSM, debe utilizar el [nivel de servicio de Azure clave depósito Premium para admitir claves protegidas con HSM](https://azure.microsoft.com/pricing/free-trial/). Además, tenga en cuenta que esta función no está disponible para China de Azure.


Cuando se crea la cámara clave, agregue el parámetro **- SKU** :


    New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -SKU 'Premium'



Puede agregar claves software protegido (como se muestra anteriormente) y protegido de HSM en este depósito clave. Para crear una clave protegida HSM, establezca el **-destino** parámetro 'HSM':

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'

Puede usar el comando siguiente para importar una clave de una. Archivo PFX en su equipo. Este comando importa la clave en HSM en el servicio de depósito de clave:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'


El comando siguiente importa "su propia clave Traer" paquete (BYOK). Este escenario le permite generar su clave en su HSM local y a continuación, transferir a HSM en el servicio de depósito de clave, sin la clave dejando el límite HSM:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'

Para obtener instrucciones detalladas sobre cómo generar este paquete BYOK, vea [cómo generar y transferencia protegido HSM claves para depósito de clave de Azure](key-vault-hsm-protected-keys.md).

## <a id="delete"></a>Eliminar el depósito clave y la claves asociadas y la información confidencial ##

Si ya no necesita la clave depósito y la clave o el secreto que contiene, puede eliminar el depósito clave mediante el cmdlet [AzureRmKeyVault quitar](https://msdn.microsoft.com/library/azure/mt619485\(v=azure.300\).aspx) :

    Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'

O bien, puede eliminar un grupo de recursos de Azure completo, que incluye la clave depósito y otros recursos que se incluyen en dicho grupo:

    Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'


## <a id="other"></a>Otros Cmdlets de PowerShell de Azure ##

Otros comandos que puede resultarle útil para administrar depósito de Azure clave:

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: Este comando obtiene una visualización tabular de todas las claves y propiedades seleccionadas.
- `$Keys[0]`: Este comando muestra una lista completa de propiedades de la clave especificada
- `Get-AzureKeyVaultSecret`: Este comando muestra una visualización tabular de todos los nombres secretos y propiedades seleccionadas.
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: Ejemplo cómo quitar una clave específica.
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: Ejemplo cómo quitar un secreto específico.


## <a id="next"></a>Pasos siguientes ##

Para obtener un tutorial de seguimiento que utiliza depósito de clave de Azure en una aplicación web, vea [Usar Azure clave depósito desde una aplicación Web](key-vault-use-from-web-application.md).

Para ver cómo se utiliza la cámara clave, vea [El registro de depósito de la clave de Azure](key-vault-logging.md).

Para obtener una lista de los cmdlets de PowerShell de Azure más recientes para Azure clave depósito, vea [Cmdlets de depósito de clave de Azure](https://msdn.microsoft.com/library/azure/dn868052\(v=azure.300\).aspx). 
 

Para programar referencias, vea [Guía del desarrollador de Azure clave depósito](key-vault-developers-guide.md).
