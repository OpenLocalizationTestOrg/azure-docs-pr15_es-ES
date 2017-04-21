<properties 
    pageTitle="¿Qué tipo de colección debe para RemoteApp de Azure? | Microsoft Azure" 
    description="Obtenga información sobre los tipos de colecciones disponibles con RemoteApp de Azure." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="what-kind-of-collection-do-you-need-for-azure-remoteapp"></a>¿Qué tipo de colección debe para RemoteApp de Azure?

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

RemoteApp Azure le permite compartir aplicaciones y recursos con usuarios en cualquier dispositivo. Hacerlo mediante la creación de colecciones para mantener las aplicaciones y los recursos y, a continuación, compartir las colecciones con los usuarios. ¿Hay 2 opciones de otra colección, con otra red y opciones de autenticación: que es adecuado para usted?

Examinemos las consideraciones diferentes y las opciones que debe realizar obtener el máximo provecho de la colección de RemoteApp de Azure. 


## <a name="quick-differences-between-the-collection-types"></a>Rápido diferencias entre los tipos de colecciones

|           | Nube | Híbrido |
|-----------|-------|--------|
|Usar un VNET existente| Sí| Sí|
|Requiere cuentas conectadas AD (DirSync)| No| Sí|
|Requiere unirse a un dominio| No| Sí|
|Requiere el controlador de dominio accesible para VNET| No| Sí|

## <a name="cloud-collections"></a>Colecciones de nube
- Rápido crear - se aprovisiona rápidamente la colección, lo que significa que las aplicaciones de obtener acceso a los usuarios más rápidas.
- Traer sus propias aplicaciones o compartir nuestro. Puede usar una imagen personalizada (generado a partir de una máquina virtual de Azure) o una de las imágenes que se incluye con la suscripción.
- No es necesario configurar una conexión entre la colección y su dominio local.
- Pero, si lo desea puede usar su propio VNET Azure para proporcionar acceso en su entorno local para compartir datos o usar autenticación de Windows no en recursos como SQL Server (mediante la autenticación de base de datos).


Bien, ¿cómo crear una?

- ¿Solo nube? Crear con la opción **Crear rápido** en el portal.
- ¿Nube + VNET? Crear con la opción de **crear con VNET** pero no elige unirse a un dominio.

## <a name="hybrid-collections"></a>Colecciones híbrido
- Proporcionan acceso completo a la red local + VNET Azure.
- Incluye acceso de unirse al dominio para aplicaciones y los datos. Aplicaciones remotas pueden autenticación de Active Directory local: a continuación, pueden acceder a recursos de su dominio.
- Habilitar la supervisión avanzada y administración con las soluciones de System Center existentes y las directivas de grupo de Windows (a través de una imagen personalizada integrada en Windows Server 2012 R2)
- Soporte técnico [ExpressRoute](https://azure.microsoft.com/services/expressroute/) para conectar su VNET de Azure a su VNET local.

Crear con la opción de **crear con VNET** y elija unirse a un dominio.

## <a name="authentication-options"></a>Opciones de autenticación
RemoteApp Azure admite cuentas de Microsoft y cuentas de Azure Active Directory, pero no todas las colecciones de todos los métodos de soporte técnico. 

| Tipo de cuenta                      |                                                             | Nube | Nube + VNET | Híbrido |
|-----------------------------------|-------------------------------------------------------------|-------|--------------|--------|
| Cuenta de Microsoft                 |                                                             | Sí   | Sí          | No     |
| Azure Active Directory (AD Azure) |                                                             |       |              |        |
|                                   | Azure AD solo                                               | Sí   | Sí          | No     |
|                                   | AD Connect con la sincronización de contraseña                               | Sí   | Sí          | Sí    |
|                                   | AD Connect sin sincronización de la contraseña                            | Sí   | Sí          | No     |
|                                   | AD Connect con AD FS                                       | Sí   | Sí          | Sí    |
|                                   | 3 º compatibles de Azure proveedores de identidades (como Ping) | Sí   | Sí          | Sí    |
| Autenticación multifactor       |                                                             | Sí   | Sí          | Sí    |



### <a name="cloud-and-cloud--vnet"></a>Nube y nube + VNET 
Con las colecciones de la nube, puede usar cuentas de Microsoft, cuentas de Azure AD o una combinación de ambos. Use las cuentas que funcionan mejor para sus usuarios.

No existen requisitos específicos para el uso de cuentas de Microsoft. 

Si desea usar cuentas de Azure AD, debe asegurarse de que el inquilino de Azure AD coincide con el asociado con su suscripción. Cuando crea su suscripción de RemoteApp de Azure, el inquilino de Azure AD que estaba utilizando estaba asociado automáticamente con la suscripción. Cualquier usuario de Azure AD que dar permiso debe estar ese mismo inquilino. Si es necesario, puede [cambiar al inquilino de Azure AD](remoteapp-changetenant.md) asociada a su suscripción.
 
### <a name="hybrid-or-cloud--azure-ad--ad"></a>Híbrido (o nube + Azure AD + AD)

Uso de Azure AD + Active Directory es un requisito previo para una colección de híbrido en local. Debe utilizar AD Connect para integrar los dos directorios. Pero hay algunas opciones cuando se trata de cómo configurar AD Connect. 

Hay 2 AD Connect escenarios - mediante la sincronización de contraseñas o la federación de AD. Consulte la [información de conexión de AD](../active-directory/active-directory-aadconnect.md) para averiguar qué estos funciona mejor para usted.

También puede usar Azure AD + AD con una colección de la nube. Asegúrese de que seguir los mismos pasos de configuración.

Desproteger [Azure AD + requisitos de Active Directory para Azure RemoteApp](remoteapp-ad.md) para ver los pasos necesarios para configurar Azure AD y Active Directory.

## <a name="go-create-your-collection"></a>¡Vaya a crear la colección!
Aceptar, creo que nos hemos descubrió ahora, por lo que hay una cosa izquierda para hacer: crear la primera colección de RemoteApp de Azure.

[Crear una colección de nube](remoteapp-create-cloud-deployment.md) o [crear una colección de híbrido](remoteapp-create-hybrid-deployment.md) - obtener crear.
