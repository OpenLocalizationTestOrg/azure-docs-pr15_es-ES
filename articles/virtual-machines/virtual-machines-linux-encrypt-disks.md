<properties
   pageTitle="Cifrar discos en una VM Linux | Microsoft Azure"
   description="Cómo cifrar discos en una VM Linux usando CLI Azure y el modelo de implementación de administrador de recursos"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/11/2016"
   ms.author="iainfou"/>

# <a name="encrypt-disks-on-a-linux-vm-using-the-azure-cli"></a>Cifrar discos en una VM Linux mediante la CLI de Azure
Seguridad mejorada máquina virtual () y cumplimiento, se pueden cifrar discos virtuales en Azure en el resto. Discos se cifran mediante claves de cifrado que están protegidas en un depósito de clave de Azure. Controlar estas teclas cifradas y puede auditar su uso. En este artículo se explica cómo cifrar discos virtuales en una VM Linux usando CLI Azure y el modelo de implementación de administrador de recursos.


## <a name="quick-commands"></a>Comandos rápidos
Si necesita realizar rápidamente la tarea, los detalles de la sección siguiente de la base de comandos para cifrar discos virtuales en la máquina virtual. Obtener más información y el contexto de cada paso pueden encontrarse el resto del documento, a [partir de aquí](#overview-of-disk-encryption).

Necesita la [Última CLI Azure](../xplat-cli-install.md) instalado y conectado con el modo de administrador de recursos como sigue:

```
azure config mode arm
```

En los ejemplos siguientes, reemplace los nombres de parámetro de ejemplo con sus propios valores. Incluyen nombres de parámetro de ejemplo `myResourceGroup`, `myKeyVault`, y `myVM`.

En primer lugar, habilite al proveedor de Azure clave depósito dentro de su suscripción de Azure y crear un grupo de recursos. En el ejemplo siguiente se crea un nombre de grupo de recursos `myResourceGroup` en la `WestUS` ubicación:

```bash
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Crear un depósito clave Azure. En el ejemplo siguiente se crea un depósito de clave denominado `myKeyVault`:

```bash
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Crear una clave de cifrado en su cámara clave y habilitarlo para el cifrado de disco. En el ejemplo siguiente se crea una clave denominada `myKey`:

```bash
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```

Crear un extremo con Azure Active Directory para controlar la autenticación y el intercambio de claves de cifrado de la clave de cámara. La `--home-page` y `--identifier-uris` no es necesario ser dirección enrutable real. El más alto nivel de seguridad, se debe utilizar información confidencial de cliente en lugar de contraseñas. CLI Azure actualmente no puede generar información confidencial de cliente. Solo se puede generar la información confidencial de cliente en el portal de Azure. En el ejemplo siguiente se crea un extremo de Azure Active Directory denominado `myAADApp` y utiliza una contraseña de `myPassword`. Especifique su propia contraseña como sigue:

```bash
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Nota la `applicationId` se muestra en el resultado del comando anterior. Este ID de aplicación se usa en los siguientes pasos:

```bash
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```

Agregar un disco de datos a una máquina virtual existente. En el ejemplo siguiente se agrega un disco de datos a una máquina virtual denominada `myVM`:

```bash
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Revise los detalles de su cámara clave y la clave que creó. Necesita la clave depósito ID, URI y clave de dirección URL en el paso final. En el ejemplo siguiente se revisa los detalles para un depósito de clave denominado `myKeyVault` y clave denominada `myKey`:

```bash
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Cifrar los discos de manera, escribir sus propios nombres de parámetro durante:

```bash
azure vm enable-disk-encryption --resource-group myResourceGroup --vm-name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Azure CLI no proporciona detallados errores durante el proceso de cifrado. Para obtener información adicional de solución de problemas, consulte `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log`. Como el comando anterior tiene muchas variables y no pueden obtener mucho indicación de por qué se produce un error en el proceso, un ejemplo de comando completo sería como sigue:

```bash
azure vm enable-disk-encryption -g myResourceGroup -n MyVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \ 
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Por último, revise el estado de cifrado para confirmar que los discos virtuales ahora se han cifrado. En el ejemplo siguiente se comprueba el estado de una máquina virtual denominada `myVM` en la `myResourceGroup` grupo de recursos:

```bash
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```

## <a name="overview-of-disk-encryption"></a>Información general sobre el cifrado de disco
Discos virtuales en máquinas virtuales de Linux están cifrados almacenados con [cifrado dm](https://wikipedia.org/wiki/Dm-crypt). No hay ningún cargo para cifrar discos virtuales en Azure. Claves de cifrado se almacenan en depósito de clave de Azure con protección de software, o puede importar o generar las claves en los módulos de seguridad de Hardware (HSM) certificadas para FIPS 140-2 estándares de nivel 2. Mantener el control de estas teclas cifradas y puede auditar su uso. Estas claves de cifrado se utilizan para cifrar y descifrar discos virtuales conectados a su máquina virtual. Un extremo de Azure Active Directory proporciona un mecanismo seguro para emitir estas teclas cryptographic como máquinas virtuales se tecnología activado y desactivado.

El proceso para cifrar una máquina virtual es como sigue:

1. Crear una clave de cifrado en un depósito de clave de Azure.
2. Configurar la clave de cifrado para poder utilizarlo para cifrar discos.
3. Para leer la clave de cifrado de la cámara de clave de Azure, crear un extremo con Azure Active Directory con los permisos adecuados.
4. Ejecute el comando para cifrar los discos virtuales, que especifica el extremo de Azure Active Directory y la clave de cifrado adecuada para usarse.
5. El extremo de Azure Active Directory solicita la clave de cifrado necesaria de la cámara de clave de Azure.
6. Los discos virtuales se cifran mediante la clave de cifrado proporcionada.


## <a name="supporting-services-and-encryption-process"></a>Servicios de soporte y proceso de cifrado
Cifrado de disco se basa en los componentes adicionales siguientes:

- **Depósito de clave de azure** - utilizado para proteger las claves de cifrado e información confidencial que se utiliza para el proceso de cifrado/descifrado de disco. 
  - Si existe una, puede usar una caja fuerte existente clave de Azure. No tiene que se dedicará un depósito clave para cifrar discos.
  - Para separar los límites administrativos y visibilidad clave, puede crear un depósito dedicada de clave.
- **Azure Active Directory** - controla el intercambio seguro de claves cifradas necesarias y la autenticación para acciones solicitados. 
  - Normalmente, puede usar una instancia de Azure Active Directory existente para la aplicación de la caja. 
  - La aplicación es más de un extremo para los servicios de depósito de clave y la máquina Virtual solicitar y obtener emitido las claves de cifrado adecuadas. No se desarrollar una aplicación real que se integra con Azure Active Directory.


## <a name="requirements-and-limitations"></a>Requisitos y limitaciones
Escenarios compatibles y los requisitos para el cifrado de disco:

- El siguiente servidor Linux SKU - Ubuntu, CentOS, SUSE y SUSE Linux Enterprise Server (SLES) y rojo sombrero Enterprise Linux.
- Todos los recursos (como depósito de clave, la cuenta de almacenamiento y VM) deben estar en la misma región de Azure y de suscripción.
- Serie de estándar A, D, DS, G y GS máquinas virtuales.

Cifrado de disco no es compatible actualmente en las situaciones siguientes:

- Máquinas virtuales de nivel básico.
- Máquinas virtuales creadas con el modelo de implementación de clásico.
- Deshabilitar el cifrado de disco del sistema operativo en máquinas virtuales de Linux.
- Actualizar las claves de cifrado en una VM Linux ya cifrados.


## <a name="create-the-azure-key-vault-and-keys"></a>Crear el depósito de clave de Azure y claves
Para completar el resto de esta guía, debe [CLI más reciente de Azure](../xplat-cli-install.md) instalado y conectado con el modo de administrador de recursos como sigue:

```
azure config mode arm
```

En los ejemplos de comandos, reemplace todos los parámetros de ejemplo con sus propios nombres, ubicación y valores de clave. Los ejemplos siguientes usan una convención de `myResourceGroup`, `myKeyVault`, `myAADApp`, etcetera.

El primer paso es crear un depósito de clave de Azure para almacenar las claves de cifrado. Depósito de clave Azure puede almacenar claves, el secreto o contraseñas que le permiten implementar segura de las aplicaciones y servicios. Para el cifrado de disco virtual, use clave depósito para almacenar una clave de cifrado que se utiliza para cifrar o descifrar los discos virtuales. 

Habilitar al proveedor de Azure clave depósito en su suscripción de Azure y luego crear un grupo de recursos. En el ejemplo siguiente se crea un grupo de recursos denominado `myResourceGroup` en la `WestUS` ubicación:

```bash
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

El depósito de clave de Azure que contiene las claves de cifrado y recursos de cálculo asociado como almacenamiento y la máquina virtual propio deben estar en la misma región. En el ejemplo siguiente se crea un depósito de clave de Azure denominado `myKeyVault`:

```bash
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Puede almacenar las claves de cifrado con protección de modelo de seguridad de Hardware (HMS) o de software. Utilizar un HSM requiere un depósito clave premium. Hay un coste adicional con la creación de una prima depósito de clave en lugar de estándar depósito de clave que almacena las claves de software protegido. Para crear un depósito de clave premium, en el paso anterior agregar `--sku Premium` al comando. En el ejemplo siguiente se utiliza claves de software protegido desde que se creó un depósito de clave estándar. 

Para ambos modelos de protección de la plataforma Windows Azure debe tener concedido acceso para solicitar las claves de cifrado cuando se inicia la máquina virtual descifrar los discos virtuales. Crear una clave de cifrado dentro de la cámara clave y luego activar para su uso con cifrado de disco virtual. En el ejemplo siguiente se crea una clave denominada `myKey` y, a continuación, se habilita para el cifrado de disco:

```bash
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```


## <a name="create-the-azure-active-directory-application"></a>Crear la aplicación de Azure Active Directory
Cuando los discos virtuales están cifrados o descifrar, use un punto final para controlar la autenticación y el intercambio de claves de cifrado de la clave de cámara. En este punto final de una aplicación de Azure Active Directory permite la plataforma Windows Azure solicitar las claves de cifrado correspondientes en el nombre de la máquina virtual. Una instancia de Azure Active Directory predeterminada está disponible en la suscripción, aunque muchas organizaciones han dedicado directorios de Azure Active Directory.

Como no va a crear una aplicación de Azure Active Directory completa, la `--home-page` y `--identifier-uris` parámetros en el ejemplo siguiente no es necesario ser dirección enrutable real. En el ejemplo siguiente se especifica también un secreto de contraseña en lugar de generar claves de en el portal de Azure. Como en esta ocasión, generar claves no se puede hacer desde la CLI de Azure. 

Crear su aplicación de Azure Active Directory. En el ejemplo siguiente se crea una aplicación denominada `myAADApp` y utiliza una contraseña de `myPassword`. Especifique su propia contraseña como sigue:

```bash
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Tome nota de la `applicationId` que se devuelve en el resultado del comando anterior. Este ID de aplicación se usa en algunos de los pasos restantes. A continuación, cree un nombre principal de servicio (SPN) para que la aplicación sea accesible dentro de su entorno. Para cifrar o descifrar discos virtuales correctamente, deben establecerse permisos en la clave cifrado almacenada en depósito de clave para permitir que la aplicación de Azure Active Directory para leer las claves. 

Crear el SPN y establecer los permisos adecuados como sigue:

```bash
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```


## <a name="add-a-virtual-disk-and-review-encryption-status"></a>Agregar un disco virtual y revisar el estado de cifrado
Para cifrar realmente algunos discos virtuales, le permite agregar un disco a una máquina virtual existente. Agregue un disco de datos de 5Gb a una máquina virtual existente:

```bash
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Los discos virtuales no están cifrados actualmente. Revisar el estado de cifrado actual de la máquina virtual como sigue:

```bash
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="encrypt-virtual-disks"></a>Cifrar discos virtuales
Para cifrar ahora los discos virtuales, poner juntos todos los componentes anterior:

1. Especifique la aplicación de Azure Active Directory y la contraseña.
2. Especificar el depósito clave para almacenar los metadatos de los discos cifrados.
3. Especifique las claves de cifrado que se utilizará para el cifrado y descifrado reales.
4. Especifique si desea cifrar el disco de sistema operativo, los discos de datos o todos.

Permite revisar los detalles de su depósito de clave de Azure y la clave que creó, como necesite el identificador de depósito de clave URI y a continuación, tecla de dirección URL en el paso final:

```bash
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Cifrar los discos virtuales utilizando el resultado de la `azure keyvault show` y `azure keyvault key show` comandos como sigue:

```bash
azure vm enable-disk-encryption --resource-group myResourceGroup --vm-name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Como el comando anterior tiene muchas variables, en el ejemplo siguiente se es el comando completo para referencia:

```bash
azure vm enable-disk-encryption -g myResourceGroup -n MyVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \ 
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Azure CLI no proporciona detallados errores durante el proceso de cifrado. Para obtener información de solución de problemas, revise `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log` en la máquina virtual está cifrado.

Por último, le permite revisar el estado de cifrado para confirmar que ahora se han cifrado los discos virtuales:

```bash
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="add-additional-data-disks"></a>Agregar discos de datos adicionales
Una vez haya cifrado los discos de datos, puede agregar más tarde más discos virtuales a su máquina virtual y también los cifrar. Cuando se ejecuta la `azure vm enable-disk-encryption` comando, incrementar la versión de secuencia utilizando la `--sequence-version` parámetro. Este parámetro de versión de secuencia permite realizar las operaciones repetidas en la misma máquina virtual de.

Por ejemplo, le permite agregar un segundo disco virtual a su máquina virtual como sigue:

```bash
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Vuelva a ejecutar el comando para cifrar los discos virtuales, esta vez agregando el `--sequence-version` parámetro e incrementar el valor de nuestra primera ejecución como sigue:

```bash
azure vm enable-disk-encryption --resource-group myResourceGroup --vm-name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
  --sequence-version 2
```


## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre la administración de Azure clave depósito, incluida la eliminación de las claves de cifrado y depósitos, consulte [Administrar clave depósito con CLI](../key-vault/key-vault-manage-with-cli.md).
- Para obtener más información sobre el cifrado de disco, como la preparación de una máquina virtual personalizada de cifrado para cargar en Azure, vea [Cifrado de disco de Azure](../security/azure-security-disk-encryption.md).