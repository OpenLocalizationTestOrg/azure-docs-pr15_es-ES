<properties
    pageTitle="Azure AD Connect: Habilitar reescritura de dispositivo | Microsoft Azure"
    description="Este documento detalla cómo habilitar reescritura de dispositivo con Azure AD Connect"
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
    ms.date="08/29/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: Habilitar reescritura de dispositivo

>[AZURE.NOTE] Una suscripción a Azure AD Premium se requiere para reescritura del dispositivo.

La siguiente documentación proporciona información sobre cómo habilitar la característica de reescritura de dispositivo en Azure AD Connect. Reescritura de dispositivo se usa en las situaciones siguientes:

- Habilitar el acceso condicional basado en dispositivos para ADFS (2012 R2 o superior) protegido aplicaciones (confianza confianzas de entidades).

Esto proporciona seguridad adicional y garantiza que se concede acceso a las aplicaciones únicamente a los dispositivos de confianza. Para obtener más información sobre el acceso condicional, vea [Administración de riesgos con acceso condicional](active-directory-conditional-access.md) y [la configuración de acceso condicional locales con Azure Active Directory dispositivo de registro](https://msdn.microsoft.com/library/azure/dn788908.aspx).

>[AZURE.IMPORTANT]
<li>Dispositivos deben estar ubicados en el mismo bosque que los usuarios. Desde dispositivos deben escribirse de nuevo a un único bosque, esta característica no se admite actualmente una implementación con varios bosques de usuario.</li>
<li>Objeto de configuración de registro de un solo dispositivo se puede agregar al bosque de Active Directory local. Esta característica no es compatible con una topología donde se sincroniza Active Directory local a múltiples directorios de Azure AD.</li>

## <a name="part-1-install-azure-ad-connect"></a>Parte 1: Instalar Azure AD conectar
1. Instalar Azure AD Connect utilizando personalizada o configuración rápida. Microsoft recomienda comenzar con todos los usuarios y grupos sincronizan correctamente antes de habilitar la reescritura de dispositivo.

## <a name="part-2-prepare-active-directory"></a>Parte 2: Preparar Active Directory
Realice los pasos siguientes para prepararse para usar reescritura de dispositivo.

1.  Desde el equipo donde está instalado Azure AD Connect, inicie PowerShell en modo elevado.

2.  Si no está instalado el módulo de PowerShell de Active Directory, instalarlo mediante el siguiente comando:

    `Install-WindowsFeature –Name AD-Domain-Services –IncludeManagementTools`

3. Si no está instalado el módulo Azure Active Directory PowerShell, a continuación, descargue e instale de [Azure Active Directory Module para Windows PowerShell (versión de 64 bits)](http://go.microsoft.com/fwlink/p/?linkid=236297). Este componente tiene una dependencia en el Asistente de inicio de sesión, que se instala con Azure AD Connect.

4.  Con credenciales de administrador de empresa, ejecute los comandos siguientes y, a continuación, salga de PowerShell.

    `Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'`

    `Initialize-ADSyncDeviceWriteback {Optional:–DomainName [name] Optional:-AdConnectorAccount [account]}`

Credenciales de administrador de empresa son necesarias, ya que es necesario realizar cambios en el espacio de nombres de configuración. Un administrador de dominio no tiene permisos suficientes.

![PowerShell para habilitar la reescritura de dispositivo](./media/active-directory-aadconnect-feature-device-writeback/powershell.png)

Descripción:

- Si todavía no existe, crea y configura nuevos contenedores y objetos en CN = Configuración de registro del dispositivo, CN = Servicios, CN = Configuración, [dn del bosque].
- Si todavía no existe, crea y configura nuevos contenedores y objetos en CN = RegisteredDevices, [dominio dn]. Objetos de dispositivo se creará en este contenedor.
- Establece permisos necesarios en la cuenta de Azure AD conector para administrar dispositivos en Active Directory.
- Solo debe ejecutar en un bosque, incluso si se está instalando Azure AD Connect en varios bosques.

Parámetros:

- DomainName: Dominio de Active Directory donde se crearán objetos de dispositivo. Nota: Todos los dispositivos para un determinado bosque de Active Directory se creará en un único dominio.
- AdConnectorAccount: Cuenta de Active Directory Azure AD Connect utilizará para administrar objetos en el directorio. Esta es la cuenta utilizada por la sincronización de Azure AD Connect para conectarse a Active Directory. Si ha instalado mediante la configuración de express, es la cuenta que el prefijo MSOL_.

## <a name="part-3-enable-device-writeback-in-azure-ad-connect"></a>Parte 3: Habilitar dispositivo reescritura en Azure AD Connect
Use el siguiente procedimiento para habilitar la reescritura de dispositivo en Azure AD Connect.

1.  Ejecute al Asistente para instalación de nuevo. Seleccione **Personalizar las opciones de sincronización** de la página tareas adicionales y haga clic en **siguiente**.
![Instalación personalizada personalizar las opciones de sincronización](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback2.png)
2.  En la página características opcionales, reescritura de dispositivo ya no se resaltan en gris. Por favor, observe que si Azure AD Connect pasos de preparación no son reescritura de dispositivo completada se pueden atenuada en la página características opcionales. Active la casilla para reescritura de dispositivo y haga clic en **siguiente**. Si aún se desactiva la casilla de verificación, vea la [sección de solución de problemas](#the-writeback-checkbox-is-still-disabled).
![Características opcionales de reescritura de dispositivo de instalación personalizada](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback3.png)
3.  En la página de la reescritura, verá el nombre de dominio suministrado como del bosque de reescritura de dispositivo predeterminado.
![Bosque de destino de reescritura de dispositivo de instalación personalizada](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback4.png)
4.  Completar la instalación del asistente sin cambios de configuración adicionales. Si es necesario, consulte [instalación personalizada de Azure AD Connect.](./connect/active-directory-aadconnect-get-started-custom.md)

## <a name="enable-conditional-access"></a>Habilitar el acceso condicional
Instrucciones detalladas para habilitar este escenario están disponibles en [la configuración de acceso condicional locales con Azure Active Directory dispositivo de registro](https://msdn.microsoft.com/library/azure/dn788908.aspx).

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Compruebe que los dispositivos están sincronizados con Active Directory
Reescritura de dispositivo ahora debe funcionar correctamente. Tenga en cuenta que puede tardar hasta 3 horas para objetos de dispositivo estar atrás escrito para AD.  Para comprobar que los dispositivos que se están sincronizados correctamente, haga lo siguiente después de completar las reglas de sincronización:

1.  Inicie el centro de administración de Active Directory.
2.  Expanda RegisteredDevices, dentro del dominio que se federado.
![Centro de administración de Active Directory registrado dispositivos](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback5.png)
3.  Dispositivos registrados actuales aparecerá en la lista.
![Centro de administración de Active Directory registra la lista de dispositivos](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback6.png)

## <a name="troubleshooting"></a>Solución de problemas

### <a name="the-writeback-checkbox-is-still-disabled"></a>La casilla de verificación de reescritura todavía está deshabilitado
Si no está activada la casilla de verificación dispositivo reescritura incluso si ha seguido los pasos anteriores, los siguientes pasos le guiará a través de lo que está comprobando el Asistente para la instalación antes de que la casilla está activada.

Lo primero es lo primero:

- Asegúrese de que al menos un bosque tiene 2012R2 de Windows Server. El tipo de objeto de dispositivo debe estar presente.
- Si ya se está ejecutando el Asistente para instalación, no se detectarán los cambios. En este caso, complete el Asistente para instalación y vuelva a ejecutar.
- Asegúrese de que la cuenta que proporciona en la secuencia de comandos de inicialización es realmente el usuario correcto usado por el conector de Active Directory. Para comprobarlo, siga estos pasos:
    - En el menú Inicio, abra el **Servicio de sincronización**.
    - Abra la ficha **conectores** .
    - Busque el conector con el tipo de servicios de dominio de Active Directory y selecciónela.
    - En **acciones**, seleccione **Propiedades**.
    - Vaya a **conectarse al bosque de Active Directory**. Compruebe que el nombre de usuario y dominio especificado en esta coincidencia de pantalla la cuenta a la secuencia de comandos.
![Cuenta de conector en el Administrador de servicios de sincronización](./media/active-directory-aadconnect-feature-device-writeback/connectoraccount.png)

Comprobar la configuración de Active Directory:
- Compruebe que el servicio de registro del dispositivo se encuentra en la siguiente ubicación (CN = DeviceRegistrationService, CN = Servicios de registro de dispositivo, CN = Configuración de registro del dispositivo, CN = Servicios, CN = Configuration) en el contexto de nomenclatura configuración.

![Solución de problemas, DeviceRegistrationService en el espacio de nombres de configuración](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot1.png)

- Compruebe que hay un solo objeto de configuración de búsqueda en el espacio de nombres de configuración. Si hay más de uno, elimine el duplicado.

![Solución de problemas, busque los objetos duplicados](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot2.png)

- En el objeto de servicio de registro del dispositivo, asegúrese de que el atributo msDS-DeviceLocation está presente y tiene un valor. Buscar la ubicación y asegúrese de que está presente con el objectType msDS-DeviceContainer.

![Solución de problemas, msDS DeviceLocation](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot3.png)

![Solución de problemas, clase de objeto RegisteredDevices](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot4.png)

- Compruebe la cuenta utilizada por el conector de Active Directory tiene los permisos necesarios en el contenedor de dispositivos registrados encontrado por el paso anterior. Se trata de los permisos en este contenedor esperados:

![Solucionar problemas, compruebe los permisos en el contenedor](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot5.png)

- Compruebe la cuenta de Active Directory tiene permisos en CN = Configuración de registro del dispositivo, CN = Servicios, CN = objeto de configuración.

![Solucionar problemas, compruebe los permisos de configuración del dispositivo de registro](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Información adicional
- [Administración de riesgos con acceso condicional](active-directory-conditional-access.md)
- [Configurar el acceso condicional locales con Azure Active Directory dispositivo de registro](https://msdn.microsoft.com/library/azure/dn788908.aspx)

## <a name="next-steps"></a>Pasos siguientes
Más información acerca de cómo [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
