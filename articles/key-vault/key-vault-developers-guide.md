<properties
   pageTitle="Clave de la Guía del desarrollador de depósito | Microsoft Azure"
   description="Los desarrolladores pueden utilizar el depósito de Azure clave para administrar claves de cifrado en el entorno de Microsoft Azure. "
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/03/2016"
   ms.author="bruceper" />

# <a name="azure-key-vault-developers-guide"></a>Guía del desarrollador de Azure depósito clave
Utilizar la caja fuerte de clave, podrá para acceso seguro a la información confidencial dentro de las aplicaciones que:

- Las claves y los secretos se protegerán sin tener que escribir el código manualmente y podrá fácilmente utilizarlos en sus aplicaciones.
- Podrá tener sus propios clientes y administrar sus propias claves para que pueda centrarse en proporcionar las principales características del software. De este modo las aplicaciones no poseerá la responsabilidad ni obligación posibles claves de inquilinos de sus clientes y secretos.
- La aplicación puede utilizar las claves de firma y cifrado aún mantiene la administración de claves externa desde la aplicación que la solución es adecuada para una aplicación que se distribuye geográficamente.

- Con la versión de septiembre de 2016 de depósito de clave, las aplicaciones pueden ahora hacen uso de depósito de clave de certificados. Para obtener más información, consulte **acerca de las claves, secretos y certificados** el artículo en la [referencia del resto](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Para obtener información más general sobre Azure clave depósito, consulte [¿Cuál es la clave de bóveda](key-vault-whatis.md).

## <a name="videos"></a>Vídeos
Este vídeo muestra cómo crear su propio depósito clave y cómo usarlo desde la aplicación de ejemplo 'Hello clave caja fuerte'.

[AZURE.VIDEO azure-key-vault-developer-quick-start]

Vínculos a los recursos mencionados en el vídeo:
- [PowerShell de Azure](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Código de ejemplo de Azure depósito clave](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

Para obtener más puede seguir el [Blog de depósito clave](http://aka.ms/kvblog) y participe en el [Foro de depósito de clave](http://aka.ms/kvforum).

## <a name="creating-and-managing-key-vaults"></a>Creación y administración de depósitos de clave

Antes de trabajar con Azure clave Vault en el código, puede crear y administrar depósitos a través del resto, Administrador de recursos plantillas, PowerShell o CLI, tal como se describe en los siguientes artículos:

- [Crear y administrar depósitos clave con el resto](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [Crear y administrar claves depósitos con PowerShell](key-vault-get-started.md)
- [Crear y administrar claves depósitos con CLI](key-vault-manage-with-cli.md)
- [Crear un depósito clave y agregar un secreto a través de una plantilla de administrador de recursos de Azure](../resource-manager-template-keyvault.md)

>[AZURE.NOTE] Operaciones de depósitos clave se autentican a través de DAA y autorizadas a través de la directiva de acceso propias del depósito de clave, definidas por depósito.

## <a name="coding-with-key-vault"></a>Codificación con clave Vault

El sistema de administración de caja fuerte de clave para los programadores consiste en varias interfaces, con el resto como base, [Referencia de la API de resto de clave de bóveda](https://msdn.microsoft.com/library/azure/dn903609.aspx).

Se puede, sujetos a la autorización correcta, siga este procedimiento:

- Administrar claves de cifrado mediante [crear](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Importar](https://msdn.microsoft.com/library/azure/dn903626.aspx), [Actualizar](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Eliminar](https://msdn.microsoft.com/library/azure/dn903611.aspx) y otras operaciones

- Administrar secretos mediante [Get](https://msdn.microsoft.com/library/azure/dn903633.aspx), [Update](https://msdn.microsoft.com/library/azure/dn986818.aspx), [Delete](https://msdn.microsoft.com/library/azure/dn903613.aspx) y otras operaciones

- Utilizar claves de cifrado con [signo](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Comprobar](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) y [Encrypt](https://msdn.microsoft.com/library/azure/dn878060.aspx)/operaciones de[descifrar](https://msdn.microsoft.com/library/azure/dn878097.aspx)

Los SDKs siguientes están disponibles para trabajar con depósito de clave:

|[![.NET](./media/key-vault-developers-guide/msft.netlogo_purple.png)](https://msdn.microsoft.com/library/mt765854.aspx)|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[Documentación del SDK de .NET](https://msdn.microsoft.com/library/mt765854.aspx)|[Documentación del SDK de Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[Paquete SDK de .NET en Nuget](http://www.nuget.org/packages/Microsoft.Azure.KeyVault)|[Paquete SDK de Node.js](https://www.npmjs.com/package/azure-keyvault)|

Para obtener más información sobre la versión 2.x de .NET SDK, vea las [notas de la versión](key-vault-dotnet2api-release-notes.md).

## <a name="example-code"></a>Código de ejemplo
Para obtener ejemplos completos con clave de bóveda con las aplicaciones, consulte:

- Aplicación de muestra .NET *HelloKeyVault* y un ejemplo de servicio web de Azure. [Ejemplos de código de tecla Vault Azure](http://www.microsoft.com/download/details.aspx?id=45343)
- Tutorial para ayudarle a aprender a utilizar el depósito de Azure clave desde una aplicación web en Azure. [Utilizar Azure depósito clave desde una aplicación Web](key-vault-use-from-web-application.md)

## <a name="how-tos"></a>Procedimientos cómo

Los siguientes artículos y escenarios proporcionan orientación específica de tareas para trabajar con Azure clave Vault:

- [Cambiar el ID de inquilinos depósito clave después de mover la suscripción](key-vault-subscription-move-fix.md) - cuando mueve su suscripción de Azure de inquilinos A B de inquilinos, los depósitos de claves existentes son inaccesibles las identidades (usuarios y aplicaciones) de inquilinos revisión B. esta usando a esta guía.
- [Acceso a depósito de clave detrás de firewall](key-vault-access-behind-firewall.md) - para tener acceso a un depósito de claves que necesita la aplicación de cliente de la caja fuerte de clave para poder tener acceso a varios extremos para varias funcionalidades.

- [Cómo generar y las claves de Transfer HSM-Protected de Azure clave Vault](key-vault-hsm-protected-keys.md) - Esto le ayudará a planear, generar y, a continuación, transferir sus propias teclas protegidas con HSM para utilizar con Azure clave de bóveda.
- [Cómo pasar valores seguros (como contraseñas) durante la implementación](../resource-manager-keyvault-parameter.md) : cuando se necesita pasar un valor seguro (como una contraseña) como un parámetro durante la implementación, puede almacenar ese valor como un secreto en un depósito de Azure clave y el valor en otras plantillas de administrador de recursos de referencia.
- [Cómo utilizar la caja fuerte de clave para la administración de claves extensible con SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) - el conector de SQL Server para Azure clave de depósito permite a SQL Server y SQL en VM para aprovechar el servicio de depósito de Azure clave como proveedor de administración de claves Extensible (EKM) para proteger sus claves de cifrado para el enlace de aplicaciones; Cifrado de datos transparente, cifrado de copia de seguridad y cifrado a nivel de columna.
- [Cómo implementar certificados para las VMs desde bóveda clave](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) : una aplicación de nube que se ejecuta en una máquina virtual en Azure se necesita un certificado. ¿Cómo se consigue este certificado en esta máquina virtual en la actualidad?
- [Cómo configurar la clave de bóveda con la rotación de clave de extremo a extremo y auditoría](key-vault-key-rotation-log-monitoring.md) - recorre en este describe cómo configurar la auditoría con Azure clave Vault y la rotación de clave.

Para más orientación de tareas específicas en la integración y uso de depósitos de clave con Azure, vea [ejemplos de plantillas de Azure Resource Manager Ryan Jones para depósito de clave](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

## <a name="integrated-with-key-vault"></a>Integrado con la caja fuerte de la clave

Estos artículos están sobre otros escenarios y los servicios que nos hacen de o integran con depósito de clave.

- [Cifrado de disco Azure](../security/azure-security-disk-encryption.md) aprovecha la característica [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) de sector estándar de Windows y la característica de [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) de Linux para proporcionar cifrado de volumen para el sistema operativo y los discos de datos. La solución se integra con Azure caja fuerte de clave para ayudarle a controlar y administrar las claves de cifrado de disco y secretos en su suscripción de depósito clave, velando por que todos los datos en los discos de la máquina virtual se cifran en reposo en el almacenamiento de Azure.


## <a name="supporting-libraries"></a>Bibliotecas de soporte

- [Biblioteca básica de Microsoft Azure clave Vault](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) ofrece `IKey` y `IKeyResolver` interfaces para localizar claves de identificadores y realizar operaciones con claves.

- [Las extensiones de Microsoft Azure clave Vault](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) ofrece capacidades extendidas de Azure clave Vault.

## <a name="other-key-vault-resources"></a>Otros recursos de la caja fuerte de la clave
- [Blog de depósito clave](http://aka.ms/kvblog)
- [Foro de depósito clave](http://aka.ms/kvforum)
