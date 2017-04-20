<properties
    pageTitle="Azure AD Connect: Preguntas más frecuentes sobre | Microsoft Azure"
    description="Esta página ha preguntas más frecuentes acerca de Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-faq"></a>Azure AD Connect preguntas más frecuentes

## <a name="general-installation"></a>Instalación general
**P: ¿instalación funcionará si el administrador Global de AD Azure tiene 2FA habilitado?**  
Con las versiones de febrero de 2016, esto es compatible.

**P: ¿existe una manera de instalar Azure AD Connect desatendida?**  
Solo es compatible para instalar Azure AD Connect utilizando al Asistente para instalación. Una instalación desatendida y silenciosa no es compatible.

**P: ¿puedo tener un bosque donde no se pueden conectar con un dominio. ¿Cómo se puede instalar Azure AD Connect?**  
Con las versiones de febrero de 2016, esto es compatible.

**¿P: el agente de mantenimiento de AD DS funciona en server core?**  
Sí. Después de instalar al agente, puede completar el proceso de registro con el siguiente cmdlet de PowerShell: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

## <a name="network"></a>Red
**P: ¿puedo tener un dispositivo de red, firewall o algo más que limita las conexiones de tiempo máximo puede estar abierto en mi red. ¿Cuánto tiempo mi umbral de tiempo de espera del lado cliente debería al usar Azure AD Connect?**  
Todo el software de red, dispositivos físicos o cualquier otra descripción que limita el tiempo máximo conexiones pueden permanecer abiertas debe usar un umbral de al menos 5 minutos (300 segundos) para la conectividad entre el servidor donde está instalado el cliente de Azure AD Connect y Azure Active Directory. Esto también se aplica a todas las herramientas de sincronización de Microsoft Identity publicadas anteriormente.

**P: ¿son dominios de segundo nivel (única etiqueta dominios) compatibles?**  
No, Azure AD Connect no admite local bosques/dominios con los dominios de segundo nivel.

**¿P: son "puntos" NetBios denominado compatibles?**  
No, Azure AD Connect no admite local bosques/dominios donde el nombre NetBios contiene un punto "." en el nombre.

## <a name="federation"></a>Federación
**P: ¿qué debo hacer si aparece un mensaje de correo que me pide que renovar mi certificado de Office 365**  
Utilice las instrucciones que se describen en el tema [renovarlos](active-directory-aadconnect-o365-certs.md) sobre cómo renovar el certificado.

**P: ¿puedo hacer "Actualice automáticamente usuario de confianza" para el usuario de confianza de O365. ¿Tengo que realice ninguna acción cuando mi token automáticamente, el certificado de firma se renueva?**  
Utilice las instrucciones que se describe en el artículo [renovarlos](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Entorno
**P: ¿se admite cambiar el nombre del servidor después de haber instalado Azure AD Connect?**  
No. Cambiar el nombre del servidor hará que el motor de sincronización no puedan conectarse a la base de datos SQL y el servicio no podrá comenzar.

## <a name="identity-data"></a>Datos de identidad
**P: el atributo UPN (userPrincipalName) en Azure AD no coinciden con el UPN local - ¿por qué?**  
Vea estos artículos:

- [Nombres de usuario en Office 365, Azure o Intune no coincide con el UPN local o el identificador de inicio de sesión alternativo](https://support.microsoft.com/en-us/kb/2523192)
- [Cambios no están sincronizados en la herramienta de sincronización de Azure Active Directory después de cambiar el UPN de una cuenta de usuario para usar un dominio federado diferente](https://support.microsoft.com/en-us/kb/2669550)

También puede configurar Azure AD para permitir que el motor de sincronización actualizar userPrincipalName tal como se describe en las [características de servicio de sincronización de Azure AD Connect](active-directory-aadconnectsyncservice-features.md).

## <a name="custom-configuration"></a>Configuración personalizada
**P: ¿dónde se describen los cmdlets de PowerShell para Azure AD Connect?**  
Con la excepción de los cmdlets documentados en este sitio, otros cmdlets de PowerShell que se encuentra en Azure AD Connect no son compatibles para uso del cliente.

* *P: ¿pueden usar "cliente-servidor exportar o importar" que se encuentra en *el Administrador de servicios de sincronización* para mover la configuración entre servers? **  
No. Esta opción no recuperará todas las opciones de configuración y no debe usarse. En su lugar, debe usar al Asistente para crear la configuración básica en el segundo servidor y utilizar el editor de reglas de sincronización para generar scripts de PowerShell para mover cualquier regla personalizada entre servidores. Consulte [mover configuración personalizada de activo a servidor de ensayo](active-directory-aadconnect-upgrade-previous-version.md#move-custom-configuration-from-active-to-staging-server).

## <a name="troubleshooting"></a>Solución de problemas
**P: ¿Cómo puedo obtener ayuda con Azure AD Connect?**

[Buscar Microsoft Knowledge Base (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

- Buscar Microsoft Knowledge Base (KB) técnica soluciones para problemas comunes de reparación sobre la compatibilidad con Azure AD Connect.

[Foros de Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

- Puede buscar y examinar para técnicos preguntas y respuestas de la Comunidad o en su propia pregunta haciendo clic en [aquí](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Azure AD Connect de atención al cliente](https://manage.windowsazure.com/?getsupport=true)

- Use este vínculo para obtener soporte técnico a través del portal de Azure.
