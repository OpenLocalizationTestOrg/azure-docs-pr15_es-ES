<properties
    pageTitle="Administrar depósito clave mediante CLI | Microsoft Azure"
    description="Use este tutorial para automatizar tareas comunes en tecla depósito con CLI"
    services="key-vault"
    documentationCenter=""
    authors="BrucePerlerMS"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/26/2016"
    ms.author="bruceper"/>

# <a name="manage-key-vault-using-cli"></a>Administrar depósito clave mediante CLI #
Azure depósito clave está disponible en la mayoría de las regiones. Para obtener más información, consulte la [página de precios de depósito de clave](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introducción  
Use este tutorial para ayudarle a empezar a trabajar con Azure depósito de clave para crear un contenedor consolidado (un depósito) en Azure, almacenar y administrar las claves de cifrado y la información confidencial en Azure. Le guiará por el proceso de usar la interfaz de Azure entre plataformas de línea de comandos para crear un depósito que contiene una clave o contraseña que puede utilizar con una aplicación de Azure. A continuación, muestra cómo una aplicación, a continuación, puede utilizar dicha clave o contraseña.

**Tiempo estimado para completar:** 20 minutos

>[AZURE.NOTE]  Este tutorial no incluye instrucciones sobre cómo escribir la aplicación de Azure que uno de los pasos incluye, que muestra cómo autorizar una aplicación para usar una clave o un secreto en depósito de clave.
>
>Actualmente, no puede configurar depósito de clave de Azure en el portal de Azure. En su lugar, siga estas instrucciones de la interfaz de línea de comandos de entre plataformas. O bien, para obtener instrucciones de PowerShell de Azure, vea [este tutorial equivalente](key-vault-get-started.md).

Para obtener información general acerca de la cámara de clave de Azure, consulte [¿Qué es depósito de clave de Azure?](key-vault-whatis.md)

## <a name="prerequisites"></a>Requisitos previos
Para completar este tutorial, debe tener el siguiente:

- Una suscripción a Microsoft Azure. Si no tiene una, puede registrarse para una [prueba gratuita](https://azure.microsoft.com/pricing/free-trial).
- Interfaz de línea de comandos versión 0.9.1 o posterior. Para instalar la última versión y conectarse a su suscripción de Azure, vea [instalar y configurar la interfaz de línea de comandos de Azure entre plataformas](../xplat-cli-install.md).
- Una aplicación que se configurarán para usar la clave o contraseña que cree en este tutorial. Una aplicación de ejemplo está disponible desde el [Centro de descarga de Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Para obtener instrucciones, vea el archivo Léame adjunto.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Obtener ayuda con el interfaz de línea de comandos de Azure entre plataformas

En este tutorial se supone que está familiarizado con la interfaz de línea de comandos (fiesta, Terminal, símbolo del sistema)

La--Ayuda o -h parámetro puede usarse para ver la Ayuda de comandos específicos. Como alternativa, la azure help [comando] [opciones] formato también puede utilizarse para devolver la misma información. Por ejemplo, los comandos siguientes todos devuelven la misma información:

    azure account set --help

    azure account set -h

    azure help account set

En caso de duda sobre los parámetros que se necesita un comando, consulte la Ayuda usando--ayuda, -h o azure [comando].

También puede leer los siguientes tutoriales para familiarizarse con el Administrador de recursos de Azure interfaz de línea de comandos de Azure entre plataformas:

- [Cómo instalar y configurar la interfaz de línea de comandos de Azure entre plataformas](../xplat-cli-install.md)
- [Usar interfaz de Azure entre plataformas de línea de comandos con el Administrador de recursos Azure](../xplat-cli-azure-resource-manager.md)


## <a name="connect-to-your-subscriptions"></a>Conectarse a las suscripciones

Para iniciar sesión con una cuenta profesional, utilice el siguiente comando:

    azure login -u username -p password

o si desea iniciar una sesión escribiendo de forma interactiva

    azure login

>[AZURE.NOTE]  El método de inicio de sesión solo funciona con la cuenta de la organización. Una cuenta de la organización es un usuario que es administrado por su organización y definidas por el inquilino de Azure Active Directory de su organización.


Si no se dispone de una cuenta y está utilizando una cuenta de Microsoft para iniciar sesión en su suscripción de Azure, puede crear fácilmente una mediante los siguientes pasos.

1.  Inicie sesión en el inicio de sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/)y haga clic en Active Directory.
2.  Si no existe ningún directorio, seleccione crear su directorio y proporcione la información solicitada.
3.  Seleccione el directorio y agregar un nuevo usuario. Este nuevo usuario es una cuenta de la organización. Durante la creación del usuario, se proporcionará con una dirección de correo para el usuario y una contraseña temporal. Guardar esta información como se utiliza en otro paso.
4.  Desde el portal, seleccione Configuración y, a continuación, los administradores. Seleccione Agregar y agregar el nuevo usuario como administrador de compañeros. Esto permite la cuenta profesional administrar su suscripción de Azure.
5.  Por último, inicie sesión en el portal de Azure y volver a iniciarla con la nueva cuenta organizativa. Si esta es la primera vez que inicia sesión con esta cuenta, se le pedirá que cambie la contraseña.

Para obtener más información sobre el uso de una cuenta profesional con Microsoft Azure, consulte [registrarse para Microsoft Azure como una organización](../active-directory/sign-up-organization.md).

Si tiene varias suscripciones y desea especificar una específica para depósito de clave de Azure, escriba lo siguiente para ver las suscripciones para su cuenta:

    azure account list

A continuación, para especificar la suscripción para que utilice, escriba:

    azure account set <subscription name>

Para obtener más información acerca de cómo configurar la interfaz de línea de comandos de Azure entre plataformas, vea [cómo instalar y configurar la interfaz de línea de comandos de Azure entre plataformas](../xplat-cli-install.md).


## <a name="switch-to-using-azure-resource-manager"></a>Utilizar el Administrador de recursos de Azure

Depósito de clave requiere el Administrador de recursos de Azure, escriba lo siguiente para cambiar al modo de administrador de recursos de Azure:

    azure config mode arm

## <a name="create-a-new-resource-group"></a>Crear un nuevo grupo de recursos

Al utilizar el Administrador de recursos de Azure, se crean todos los recursos relacionados dentro de un grupo de recursos. En este tutorial se creará un nuevo grupo de recursos 'ContosoResourceGroup'.

    azure group create 'ContosoResourceGroup' 'East Asia'

El primer parámetro es el nombre del grupo de recursos y el segundo parámetro es la ubicación. En la ubicación, utilice el comando `azure location list` para identificar cómo especificar una ubicación alternativa a la que se muestra en este ejemplo. Si necesita más información, escriba:`azure help location`

## <a name="register-the-key-vault-resource-provider"></a>Registrar el proveedor de recursos de la cámara de clave
Asegúrese de que proveedor de recursos de clave depósito está registrado en la suscripción:

`azure provider register Microsoft.KeyVault`

Solo debe hacerse una vez por suscripción.


## <a name="create-a-key-vault"></a>Crear un depósito clave

Usar el `azure keyvault create` comando para crear un depósito clave. Esta secuencia de comandos tiene tres parámetros obligatorios: un nombre de grupo de recursos, un nombre de depósito clave y la ubicación geográfica.

Por ejemplo, si utiliza el nombre de la cámara de ContosoKeyVault, el nombre del grupo de recursos de ContosoResourceGroup y la ubicación de Asia oriental, escriba:

    azure keyvault create --vault-name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'

El resultado de este comando muestra las propiedades del depósito clave que acaba de crear. Las dos propiedades más importantes son:

- **Nombre**: en el ejemplo siguiente es ContosoKeyVault. Use este nombre para otros cmdlets depósito de clave.
- **vaultUri**: en el ejemplo siguiente es https://contosokeyvault.vault.azure.net. Las aplicaciones que utilizan la cámara a través de su API de REST deben utilizar este URI.

Su cuenta de Azure ahora está autorizado para realizar operaciones en este depósito clave. Todavía, nadie más está.


## <a name="add-a-key-or-secret-to-the-key-vault"></a>Agregar una clave o secreto al depósito clave

Si desea depósito de clave de Azure para crear una clave de software protegido por usted, use la `azure key create` comando y a continuación, escriba lo siguiente:

    azure keyvault key create --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --destination software

Sin embargo, si tiene una clave existente en un archivo de .pem guardado como archivo local en un archivo llamado softkey.pem que desea cargar en depósito de clave de Azure, escriba lo siguiente para importar la clave de la. Archivo PEM, que protege la clave por el software en el servicio de depósito de clave:

    azure keyvault key import --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --pem-file './softkey.pem' --password 'PaSSWORD' --destination software

Ahora puede hacer referencia a la clave que ha creado o cargado en depósito de clave de Azure, utilizando su URI. Utilice **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** obtener siempre la versión más reciente y **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** para obtener esta versión específica.

Para agregar un secreto a la cámara, que es una contraseña denominada SQLPassword y que tienen el valor de Pa$ $w0rd en depósito de clave de Azure, escriba lo siguiente:

    azure keyvault secret set --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword' --value 'Pa$$w0rd'

Ahora puede hacer referencia a esta contraseña que haya agregado a Azure clave depósito, utilizando su URI. Utilice **https://ContosoVault.vault.azure.net/secrets/SQLPassword** obtener siempre la versión más reciente y **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** para obtener esta versión específica.

Vamos a ver la clave o el secreto que acaba de crear:

- Para ver la clave, escriba:`azure keyvault key list --vault-name 'ContosoKeyVault'`
- Para ver el secreto, escriba:`azure keyvault secret list --vault-name 'ContosoKeyVault'`


## <a name="register-an-application-with-azure-active-directory"></a>Registrar una aplicación con Azure Active Directory

Este paso normalmente se haría un desarrollador, en un equipo distinto. No es específico de Azure clave depósito pero se incluye aquí para integridad.


>[AZURE.IMPORTANT] Para completar el tutorial, su cuenta, la cámara y la aplicación que se registrará en este paso debe estar en el mismo directorio de Azure.

Las aplicaciones que usan un depósito clave deben autenticar mediante un símbolo de Azure Active Directory. Para ello, el propietario de la aplicación debe registrar la aplicación en su Azure Active Directory. Al final del registro, el propietario de la aplicación obtiene los siguientes valores:


- Un **Identificador de la aplicación** (también conocido como un identificador de cliente) y la **clave de autenticación** (también conocido como el secreto compartido). La aplicación debe presentar ambos valores a Azure Active Directory para obtener un token. ¿Cómo se configura la aplicación para hacer esto depende de la aplicación. Para la aplicación de ejemplo depósito de clave, el propietario de la aplicación establece estos valores en el archivo app.config.



Para registrar la aplicación en Azure Active Directory:

1. Inicie sesión en el portal de Azure.
2. En la parte izquierda, haga clic en **Active Directory**y, a continuación, seleccione el directorio en el que se registrará la aplicación. <br> <br> Nota: Debe seleccionar el mismo directorio que contenga la suscripción de Azure con los que ha creado su cámara clave. Si no sabe qué directorio esto es, haga clic en **configuración**, identificar la suscripción con el que ha creado su cámara clave y anote el nombre del directorio que se muestra en la última columna.

3. Haga clic en **aplicaciones**. Si no hay aplicaciones se han agregado al directorio, esta página muestra solo el vínculo **Agregar una aplicación** . Haga clic en el vínculo o, como alternativa, puede hacer clic en **Agregar** en la barra de comandos.
4.  En el asistente **Agregar aplicación** , en la **¿Qué desea hacer?** página, haga clic en **Agregar una aplicación se desarrolla mi organización**.
5.  En la página **Díganos acerca de la aplicación** , especifique un nombre para la aplicación y seleccione **O de aplicaciones WEB API WEB** (opción predeterminada). Haga clic en el icono siguiente.
6.  En la página de **Propiedades de la aplicación** , especifique la **Dirección URL de inicio de sesión** y **URI de ID de aplicación** para la aplicación web. Si la aplicación no tiene estos valores, puede crearlos para este paso (por ejemplo, podría especificar http://test1.contoso.com para los dos cuadros). No importa si existen estos sitios. importante es que la aplicación identificador URI para cada aplicación es diferente para cada aplicación en el directorio. El directorio usa esta cadena para identificar la aplicación.
7.  Haga clic en el icono completo para guardar los cambios en el asistente.
8.  En la página de inicio rápido, haga clic en **Configurar**.
9.  Desplácese hasta la sección de **claves** , seleccione la duración y, a continuación, haga clic en **Guardar**. La página se actualiza y muestra un valor de clave. Debe configurar la aplicación con este valor de clave y el valor del **Identificador de cliente** . (Las instrucciones para esta configuración será específica de la aplicación).
10. Copie el valor del identificador de cliente de esta página, que se utilizará en el paso siguiente para establecer permisos en su cámara.




## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorizar la aplicación para utilizar la clave o el secreto

Para autorizar la aplicación para tener acceso a la clave o el secreto de la caja fuerte, use la `azure keyvault set-policy` comando.

Por ejemplo, si el nombre de la cámara es ContosoKeyVault y la aplicación que desea autorizar tiene un identificador de cliente de 8f8c4bbd 485b 45fd 98f7 ec6300b7b4ed y desea autorizar la aplicación para descifrar e inicie sesión con las teclas en la caja fuerte, a continuación, ejecute lo siguiente:

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-keys '["decrypt","sign"]'

>[AZURE.NOTE] Si se están ejecutando en el símbolo del sistema de Windows, debe reemplazar las comillas simples con comillas dobles y también escape las comillas dobles internas. Por ejemplo: "[\"descifrar\",\"inicio de sesión\"]".

Si desea autorizar esa misma aplicación leer información confidencial en su cámara, ejecute lo siguiente:

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-secrets '["get"]'

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Si desea usar un módulo de seguridad de hardware (HMS) ##

Para obtener seguridad adicional, puede importar o generar claves en módulos de seguridad de hardware (HSM) que nunca deje el límite HSM. El HSM son FIPS 140-2 nivel 2 validar. Si este requisito no se aplica a usted, omita esta sección y vaya a [eliminar el depósito clave y claves asociadas e información confidencial](#delete-the-key-vault-and-associated-keys-and-secrets).

Para crear estas teclas protegido HSM, debe tener una suscripción de cámara que admite claves protegidas con HSM.

Cuando se crea la keyvault, agregue el parámetro 'UA':

    azure azure keyvault create --vault-name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'

Puede agregar claves software protegido (como se muestra anteriormente) y protegido de HSM en este depósito. Para crear una clave protegida HSM, establezca el parámetro de destino 'HSM':

    azure keyvault key create --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --destination 'HSM'

Puede usar el comando siguiente para importar una clave de un archivo de .pem en el equipo. Este comando importa la clave en HSM en el servicio de depósito de clave:

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --destination 'HSM' --password 'PaSSWORD'

El comando siguiente importa "su propia clave Traer" paquete (BYOK). Esto le permite generar su clave en su HSM local y a continuación, transferir a HSM en el servicio de depósito de clave, sin la clave dejando el límite HSM:

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --destination 'HSM'

Para obtener instrucciones detalladas sobre cómo generar este paquete BYOK, vea [cómo usar teclas HSM-Protected con depósito de clave de Azure](key-vault-hsm-protected-keys.md).


## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Eliminar el depósito clave y la claves asociadas y la información confidencial

Si ya no necesita la clave depósito y la clave o el secreto que contiene, puede eliminar el depósito clave mediante el comando Eliminar de azure keyvault:

    azure keyvault delete --vault-name 'ContosoKeyVault'

O bien, puede eliminar un grupo de recursos de Azure completo, que incluye la clave depósito y otros recursos que se incluyen en dicho grupo:

    azure group delete --name 'ContosoResourceGroup'


## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Otros comandos de la interfaz de Azure entre plataformas de línea de comandos

Otros comandos que pueden útil para administrar depósito de clave de Azure.

Este comando muestra una visualización tabular de todas las claves y propiedades seleccionadas:

    azure keyvault key list --vault-name 'ContosoKeyVault'

Este comando muestra una lista completa de propiedades de la clave especificada:

    azure keyvault key show --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

Este comando muestra una visualización tabular de todos los nombres secretos y propiedades seleccionadas:

    azure keyvault secret list --vault-name 'ContosoKeyVault'

Aquí es un ejemplo de cómo quitar una clave específica:

    azure keyvault key delete --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

Aquí es un ejemplo de cómo quitar un secreto específico:

    azure keyvault secret delete --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword'


## <a name="next-steps"></a>Pasos siguientes

Para programar referencias, vea [Guía del desarrollador de Azure clave depósito](key-vault-developers-guide.md).
