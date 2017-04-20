<properties
    pageTitle="Sincronización de Azure AD Connect: procedimientos recomendados para cambiar la configuración predeterminada | Microsoft Azure"
    description="Proporciona los procedimientos recomendados para cambiar la configuración predeterminada de sincronización de Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="markvi;andkjell"/>


# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Sincronización de Azure AD Connect: procedimientos recomendados para cambiar la configuración predeterminada
El propósito de este tema es describir cambios compatibles y no compatibles para la sincronización de Azure AD Connect.

La configuración creada por Azure AD Connect funciona "es" para la mayoría de los entornos que se sincronizan en local de Active Directory con Azure AD. Sin embargo, en algunos casos, es necesario aplicar algunos cambios a una configuración para satisfacer una necesidad particular o requisito.

## <a name="changes-to-the-service-account"></a>Cambios en la cuenta de servicio
Sincronización de Azure AD Connect se está ejecutando en una cuenta de servicio creada por el Asistente para instalación. Esta cuenta de servicio contiene las claves de cifrado a la base de datos usado por la sincronización. Se crea con una contraseña de 127 caracteres y la contraseña está establecida en no.

- Es **no admitidas** para cambiar o restablecer la contraseña de la cuenta de servicio. Hacerlo destruye las claves de cifrado y el servicio no puede tener acceso a la base de datos y no es capaz de inicio.

## <a name="changes-to-the-scheduler"></a>Cambios en el programador
Comenzando con las versiones de compilación 1.1 (febrero de 2016) puede configurar el [Programador](active-directory-aadconnectsync-feature-scheduler.md) tener un ciclo de sincronización distinto del predeterminado de 30 minutos.

## <a name="changes-to-synchronization-rules"></a>Cambios en las reglas de sincronización
El Asistente para instalación proporciona una configuración que se supone que funciona para los escenarios más comunes. En caso de que necesite realizar cambios en la configuración, debe seguir estas reglas para aún tiene una configuración compatible.

- Si los flujos de atributos directa predeterminado no son adecuados para su organización, puede [cambiar flujos de atributos](active-directory-aadconnectsync-change-the-configuration.md#other-common-attribute-flow-changes) .
- Si desea [no flujo un atributo](active-directory-aadconnectsync-change-the-configuration.md#do-not-flow-an-attribute) y quitar los valores de atributo existentes en Azure AD, debe crear una regla para este escenario.
- [Desactivar una regla de sincronización no deseados](#disable-an-unwanted-sync-rule) en lugar de eliminarla. Se vuelve a crear una regla eliminada durante la actualización.
- Para [cambiar una regla de salida del cuadro](#change-an-out-of-box-rule), debe hacer una copia de la regla original y deshabilite la regla de cuadro de. El Editor de reglas de sincronización le pide y ayuda.
- Exportar las reglas de sincronización personalizados mediante el Editor de reglas de sincronización. El editor le proporciona una secuencia de comandos de PowerShell que puede usar para fácilmente créelas de nuevo en un escenario de recuperación ante.

>[AZURE.WARNING] Las reglas de cuadro de sincronización tienen una huella digital. Si realiza un cambio en estas reglas, la huella digital ya no coincidentes. Es posible que tenga problemas en el futuro cuando intenta aplicar una nueva versión de Azure AD Connect. Solo realice cambios la manera en que se describe en este artículo.

### <a name="disable-an-unwanted-sync-rule"></a>Desactivar una regla de sincronización no deseados
No elimine una regla de cuadro de sincronización. Se vuelve a crear durante la siguiente actualización.

En algunos casos, el Asistente para instalación ha creado una configuración que no funciona para la topología. Por ejemplo, si tiene una topología de bosque de recursos de la cuenta pero ha ampliado el esquema en el bosque cuenta con el esquema de Exchange, se crean reglas para Exchange para bosques de cuenta y recursos. En este caso, debe desactivar la regla de sincronización de Exchange.

![Regla de sincronización deshabilitado](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

En la imagen anterior, el Asistente para instalación ha encontrado un esquema de Exchange 2003 antiguo del bosque de cuenta. Se agregó a esta extensión de esquema antes de que el bosque de recursos se introdujo en el entorno de Fabrikam. Para asegurarse de que no se sincronizan atributos de la implementación de Exchange anterior, debe deshabilitar la regla de sincronización como se muestra.

### <a name="change-an-out-of-box-rule"></a>Cambiar una regla de salida de cuadro
Si necesita realizar cambios en una regla de salida del cuadro, debe hacer una copia de la regla de salida del cuadro y deshabilite la regla original. A continuación, realice los cambios a la regla duplicada. El Editor de reglas de sincronización es ayudarle con estos pasos. Cuando abre una regla de salida del cuadro, se presentan con este cuadro de diálogo:  
![Advertencia de regla de cuadro](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Seleccione **Sí** para crear una copia de la regla. A continuación, se abre la regla duplicada.  
![Regla duplicado](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

En esta regla duplicada, realice los cambios necesarios para el ámbito, unirse y transformaciones.

## <a name="next-steps"></a>Pasos siguientes

**Temas de información general**

- [Sincronización de Azure AD Connect: entender y personalizar la sincronización](active-directory-aadconnectsync-whatis.md)
- [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
