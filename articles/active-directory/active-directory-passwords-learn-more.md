<properties
    pageTitle="Más información: La administración de contraseñas de Azure AD | Microsoft Azure"
    description="Temas avanzados en la administración de Azure AD contraseña, incluido cómo funciona la reescritura de contraseña, seguridad de reescritura de contraseña, cómo restablecer la contraseña works portal y qué datos se utilizan por el restablecimiento de contraseña."
    services="active-directory"
    documentationCenter=""
    authors="asteen"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="asteen"/>

# <a name="learn-more-about-password-management"></a>Obtenga más información sobre la administración de contraseñas

> [AZURE.IMPORTANT] **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [He aquí cómo puede cambiar y restablecer su propia contraseña](active-directory-passwords-update-your-own-password.md).

Si ya se ha implementado la administración de contraseñas o están buscando solo para obtener más información sobre la revisemos que prácticos de cómo funciona antes de implementar, esta sección le proporcionará información general completa de los conceptos técnicos el servicio técnico. Trataremos los siguientes:

* [**Información general de reescritura de contraseña**](#password-writeback-overview)
  - [Cómo funciona la reescritura de contraseña](#how-password-writeback-works)
  - [Escenarios compatibles para reescritura de contraseña](#scenarios-supported-for-password-writeback)
  - [Modelo de seguridad de reescritura de contraseña](#password-writeback-security-model)
* [**¿Cómo restablecer la contraseña portal trabajo?**](#how-does-the-password-reset-portal-work)
  - [¿Qué datos se utilizan por el restablecimiento de contraseña?](#what-data-is-used-by-password-reset)
  - [Cómo obtener acceso a la contraseña restablece datos para los usuarios](#how-to-access-password-reset-data-for-your-users)

## <a name="password-writeback-overview"></a>Información general de reescritura de contraseña
Reescritura de contraseña es un componente de [Azure Active Directory Connect](active-directory-aadconnect.md) que se puede habilitar y usado por los suscriptores actuales de Azure Active Directory Premium. Para obtener más información, vea [Ediciones de Azure Active Directory](active-directory-editions.md).

Reescritura de contraseña le permite configurar el inquilino de nube para escribir las contraseñas a Active Directory local.  Lo obvia de tener que configurar y administrar una solución de restablecimiento de contraseña de autoservicio de complicada local y proporciona una forma conveniente en la nube para sus usuarios restablecer su contraseña local dondequiera que estén.  Siga leyendo para ver algunas de las características clave de reescritura de contraseña:

- **Cero comentarios de retraso.**  Reescritura de contraseña es una operación sincrónica.  Los usuarios se notificará inmediatamente si su contraseña no cumple directivas o no pudo restablecer o cambiar por cualquier motivo.
- **Es compatible con el restablecimiento de contraseñas para los usuarios con AD FS u otras tecnologías de federación.**  Con reescritura de contraseña, como las cuentas de usuario federado se sincronizan en su inquilino de Azure AD, puedan administrar sus instalaciones contraseñas de AD de la nube.
- **Es compatible con el restablecimiento de contraseñas para los usuarios mediante sincronización de hash de la contraseña.** Cuando el servicio de restablecimiento de contraseña detecta que una cuenta de usuario sincronizado está habilitada para la sincronización de hash de contraseña, restablecer contraseña de la nube y local de la cuenta al mismo tiempo.
- **Permite cambiar las contraseñas del panel de acceso y Office 365.**  Cuando federado o contraseña sincronizar usuarios llegan a cambiar sus contraseñas expirados o no ha expirado, pasaremos a crear las contraseñas volver a su entorno de AD local.
- **Admite la escritura cuando un administrador de restablece las contraseñas de la** [**Portal de administración de azure**](https://manage.windowsazure.com).  Siempre restablecimiento de administración ' d de la contraseña de un usuario en el [Portal de administración de Azure](https://manage.windowsazure.com), si ese usuario federado o la sincronización de la contraseña, que se configure la contraseña que el administrador selecciona en el anuncio local, también.  Esto no es compatible actualmente en el Portal de administración de Office.
- **Aplica el local directivas de contraseña de AD.**  Cuando un usuario restablece su contraseña, nos Asegúrese de que cumple con su local directiva AD antes de enviarlo a ese directorio.  Esto incluye historial, complejidad, edad, filtros de contraseña y otras restricciones de contraseña que ha definido en el anuncio local.
- **No requiere las reglas de firewall de entrada.**  Reescritura de contraseña usa una retransmisión de Bus de servicio de Azure como un canal de comunicación subyacentes, lo que significa que no tiene que abrir los puertos de entrada en el firewall para que funcione esta característica.
- **No se admite cuentas de usuario que hay dentro de los grupos protegidos en Active Directory local.** Para obtener más información acerca de los grupos protegidos, vea [las cuentas protegidas y grupos de Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

### <a name="how-password-writeback-works"></a>Cómo funciona la reescritura de contraseña
Reescritura de contraseña tiene tres componentes principales:

- Servicio de nube de restablecimiento de contraseña (este es también se integrado en páginas de cambio de contraseña de Azure AD)
- Retransmisión de Bus de servicio de Azure inquilino específica
- Extremo de restablecimiento de contraseña de local

Se ajustan como se describe en el diagrama siguiente:

  ![][001]

Al sincronizar un hash federado o contraseña ¿usuario llegue a restablecer o cambiar su contraseña en la nube, ocurre lo siguiente:

1.  Podemos comprobar qué tipo de contraseña el usuario tiene.  Si se ve que la contraseña es administrada local, a continuación, garantizamos que el servicio de reescritura está en funcionamiento.  Si es así, le permiten al usuario continuar, si no lo está, es decir al usuario que no puede restablecer su contraseña aquí.
2.  A continuación, el usuario pasa las puertas de autenticación apropiado y llega a la pantalla de restablecer la contraseña.
3.  El usuario selecciona una nueva contraseña y lo confirma.
4.  Al hacer clic en enviar, nos cifrar la contraseña legible con una clave simétrica que se creó durante el proceso de configuración de reescritura.
5.  Después de cifrar la contraseña, se incluyen en una carga que se envía a través de un canal HTTPS a la retransmisión de bus de servicio específico de inquilino (que es también configurados para durante el proceso de configuración de reescritura).  Este retransmisión está protegida mediante contraseña generada aleatoriamente que sabe solo la instalación local.
6.  Una vez que el mensaje llegue bus de servicio, el extremo de restablecimiento de contraseña se activa automáticamente y se observa que tiene pendiente una petición de restablecimiento.
7.  El servicio, a continuación, busca el usuario en cuestión mediante el atributo de anclaje de la nube.  Para esta búsqueda correctamente, el objeto de usuario debe estar en el espacio de conector de AD, deben estar vinculado al objeto MV correspondiente y deben estar vinculado en el objeto de conector AAD correspondiente. Por último, en orden de sincronización buscar esta cuenta de usuario, el vínculo del objeto de conector de AD a MV debe tener la regla de sincronización `Microsoft.InfromADUserAccountEnabled.xxx` en el vínculo.  Esto es necesario porque cuando se recibe la llamada de la nube, el motor de sincronización utiliza el atributo cloudAnchor para buscar el objeto de espacio de conector AAD, a continuación, siga el vínculo al objeto MV y, a continuación, sigue el vínculo al objeto de AD. Dado que podría haber varios objetos de Active Directory (bosque múltiple) para el mismo usuario, el motor de sincronización se basa en el `Microsoft.InfromADUserAccountEnabled.xxx` vínculo Elegir correcto.
8.  Una vez que se encuentra la cuenta de usuario, intentar restablecer la contraseña directamente en el bosque de AD correspondiente.
9.  Si la operación de establecimiento de contraseña es correcta, es decir al usuario se ha modificado su contraseña y que pueden ir en su camino feliz.
10. Si la contraseña establecida operación produce un error, se devolverá el error al usuario y hacerle inténtelo de nuevo.  La operación puede producir un error porque el servicio no estaba disponible, porque la contraseña que seleccionado no cumple las directivas de la organización, ya que no se encuentra el usuario en Active Directory local, o cualquier número de razones.  Disponemos de un mensaje específico para muchos de estos casos y decir al usuario qué puede hacer para resolver el problema.

### <a name="scenarios-supported-for-password-writeback"></a>Escenarios compatibles para reescritura de contraseña
La siguiente tabla describe qué escenarios son compatibles con las versiones de nuestras capacidades de sincronización.  En general, se recomienda que instale la última versión de [Azure AD Connect](active-directory-aadconnect.md#install-azure-ad-connect) si desea usar reescritura de contraseña.

  ![][002]

### <a name="password-writeback-security-model"></a>Modelo de seguridad de reescritura de contraseña
Reescritura de contraseña es un servicio muy robusto y seguro.  Para garantizar que su información está protegida, se habilita un modelo de seguridad en niveles de 4 que se describe a continuación.

- **Retransmisión de bus de servicio específico de inquilinos** : al configurar el servicio, se configura una retransmisión de bus de servicio específico del inquilino está protegida por una contraseña segura generada aleatoriamente que Microsoft no tiene acceso a.
- **Bloqueados, cifrado fuerte, clave de cifrado de contraseña** : una vez creada la retransmisión de bus de servicio, creamos una clave simétrica segura que usamos para cifrar la contraseña que empiece por la red.  Esta clave reside sólo en almacén de secreto de la compañía en la nube, que intensamente se bloquea y auditoría, igual que cualquier contraseña en el directorio.
- Se produce **TLS estándares de la industria** : cuando la operación de cambio o restablecer una contraseña en la nube, tomar la contraseña legible y cifrar con la clave pública.  Luego, se parece a un mensaje HTTPS que se envía a través de un canal cifrado con los certificados SSL de Microsoft para la retransmisión de bus de servicio.  Después de ese mensaje llegue en Bus de servicio, el agente local se activa, autentica a Bus de servicio mediante la contraseña segura que había sido generada previamente, toma el mensaje cifrado, descifra con la clave privada que se genera y, a continuación, los intentos para establecer la contraseña a través de la API de SetPassword de AD DS.  Este paso es lo que nos permite exigir la directiva de contraseñas de AD local (complejidad, edad, historial, filtros, etcetera) en la nube.
- **Directivas de expiración de mensaje** : por último, si por algún motivo el mensaje se encuentra en Bus de servicio porque el servicio local está cerrado, se agotado y se quitan después de varios minutos para aumentar aún más la seguridad.

## <a name="how-does-the-password-reset-portal-work"></a>¿Cómo restablecer la contraseña portal trabajo?
Cuando un usuario se desplaza a portal para restablecer la contraseña, se inicia un flujo de trabajo para determinar si esa cuenta de usuario es válida, ¿qué organización que pertenece usuarios, dónde se administra la contraseña de usuario y el usuario tiene licencia para usar la característica o no.  Lea los pasos siguientes para obtener información sobre la lógica detrás de la página de restablecimiento de contraseña.

1.  Usuario hace clic en el no puede acceder a un vínculo de cuenta o va directamente a [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com).
2.  Usuario escribe un identificador de usuario y pasa un captcha.
3.  Azure AD comprueba si el usuario tiene permiso usar esta característica haciendo lo siguiente:
    - Comprueba que el usuario tiene esta característica está habilitada y una licencia de Azure AD asignada.
        - Si el usuario no tiene una licencia asignada o esta característica está habilitada, se pregunta al usuario ponerse en contacto con su administrador para restablecer su contraseña.
    - Comprueba que el usuario tiene derecho desafío datos definidos en su cuenta de acuerdo con la directiva de administrador.
        - Si directiva requiere solo un reto, se garantiza que el usuario tiene los datos adecuados definidos para al menos uno de los desafíos habilitados por la directiva de administrador.
          - Si el usuario no está configurado, el usuario se recomienda ponerse en contacto con su administrador para restablecer su contraseña.
        - Si la directiva requiere dos desafíos, se garantiza que el usuario tiene los datos adecuados definidos para al menos dos de los retos habilitados por la directiva de administrador.
          - Si el usuario no está configurado, entonces se es el usuario es aconsejable ponerse en contacto con su administrador para restablecer su contraseña.
    - Comprueba si no se administra la contraseña del usuario local (federado o tenía de sincronización de contraseña hash).
       - Si se implementa la reescritura y la contraseña del usuario se administra de forma local, el usuario puede continuar para autenticar y restablecer su contraseña.
       - Si no se ha implementado reescritura y la contraseña del usuario se administra de forma local, el usuario se pide ponerse en contacto con su administrador para restablecer su contraseña.
4.  Si se determina que el usuario es capaz de correctamente restablecer su contraseña, el usuario se guiará por el proceso de restablecimiento.

Obtenga más información sobre cómo implementar reescritura de contraseña en [Introducción: administración de contraseñas de Azure AD](active-directory-passwords-getting-started.md).

### <a name="what-data-is-used-by-password-reset"></a>¿Qué datos se utilizan por el restablecimiento de contraseña?
La siguiente tabla muestra dónde y cómo estos datos se utilizan durante el restablecimiento de contraseña y está diseñados para ayudarle a decidir qué opciones de autenticación son adecuadas para su organización. Esta tabla también muestra los requisitos de formato para los casos donde va a proporcionar datos en nombre de los usuarios desde rutas de entrada que no validan estos datos.

> [AZURE.NOTE] Teléfono de Office no aparece en el portal de registro de los usuarios actualmente no se permite modificar esta propiedad en el directorio.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Nombre del método de contacto</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Elemento de datos de Azure Active Directory</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>¿Usados / configurable donde?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Requisitos de formato</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Teléfono de la oficina</p>
            </td>
            <td>
              <p>NúmeroTeléfono</p>
              <p>Por ejemplo, establecer MsolUser - UserPrincipalName JWarner@contoso.com - NúmeroTeléfono "1234567890 + 1 x 1234"</p>
            </td>
            <td>
              <p>Se usa en:</p>
              <p>Portal de restablecimiento de contraseña</p>
              <p>Settable desde:</p>
              <p>NúmeroTeléfono es configurable de PowerShell, DirSync, el Portal de administración de Azure y el Portal de administración de Office</p>
            </td>
            <td>
              <p>+ ccc xxxyyyzzzz (por ejemplo, + 1 1234567890)</p>
              <ul>
                <li class="unordered">
Debe proporcionar un código de país<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Debe proporcionar un código de área (donde sea aplicable)<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Ha proporcione una + delante del país código<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Debe tener un espacio entre el código de país y el resto del número<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
No se admiten extensiones, si tiene cualquier extensiones especificadas, se quitará lo desde el número antes de enviar la llamada de teléfono.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Teléfono móvil</p>
            </td>
            <td>
              <p>AuthenticationPhone</p>
              <p>OR</p>
              <p>TeléfonoMóvil</p>
              <p>(Autenticación de que teléfono se utiliza si hay datos presentar, en caso contrario, esto utiliza el campo teléfono móvil).</p>
              <p>Por ejemplo, establecer MsolUser - UserPrincipalName JWarner@contoso.com - TeléfonoMóvil "+ 1 1234567890 x 1234"</p>
            </td>
            <td>
              <p>Se usa en:</p>
              <p>Portal de restablecimiento de contraseña</p>
              <p>Portal de registro</p>
              <p>Settable desde: </p>
              <p>AuthenticationPhone es configurable desde el portal de registro de restablecimiento de contraseña o el portal de registro MFA.</p>
              <p>TeléfonoMóvil es configurable de PowerShell, DirSync, el Portal de administración de Azure y el Portal de administración de Office</p>
            </td>
            <td>
              <p>+ ccc xxxyyyzzzz (por ejemplo, + 1 1234567890)</p>
              <ul>
                <li class="unordered">
Debe proporcionar un código de país.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Debe proporcionar un código de área (donde sea aplicable).<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Ha proporcione código un + delante del país.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Debe tener un espacio entre el código de país y el resto del número.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
No se admiten extensiones, si tiene cualquier extensiones especificadas, nos omitir al realizar la llamada de teléfono.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Correo electrónico alternativa</p>
            </td>
            <td>
              <p>AuthenticationEmail</p>
              <p>OR</p>
              <p>AlternateEmailAddresses [0] </p>
              <p>(Autenticación de que correo electrónico se usa si no hay datos presentes, en caso contrario, se utiliza el campo de correo electrónico alternativa).</p>
              <p>Nota: el campo de correo electrónico alternativa se especifica como una matriz de cadenas en el directorio.  Usamos la primera entrada de esta matriz.</p>
              <p>Por ejemplo, establecer MsolUser - UserPrincipalName JWarner@contoso.com - AlternateEmailAddresses"email@live.com"</p>
            </td>
            <td>
              <p>Se usa en:</p>
              <p>Portal de restablecimiento de contraseña</p>
              <p>Portal de registro</p>
              <p>Settable desde: </p>
              <p>AuthenticationEmail es configurable desde el portal de registro de restablecimiento de contraseña o el portal de registro MFA.</p>
              <p>AlternateEmail es configurable de PowerShell, el Portal de administración de Azure y el Portal de administración de Office</p>
            </td>
            <td>
              <p>
                <a href="mailto:user@domain.com">user@domain.com</a>o甲斐@黒川.日本</p>
              <ul>
                <li class="unordered">
Mensajes de correo electrónico deberían seguir un formato estándar como por.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Correos electrónicos de Unicode son compatibles.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Preguntas y respuestas acerca de seguridad</p>
            </td>
            <td>
              <p>No está disponible para modificar directamente en el directorio.</p>
            </td>
            <td>
              <p>Se usa en:</p>
              <p>Portal de restablecimiento de contraseña</p>
              <p>Portal de registro </p>
              <p>Settable desde: </p>
              <p>La única manera de establecer preguntas de seguridad es a través del Portal de administración de Azure.</p>
              <p>La única manera de establecer respuestas a preguntas de seguridad de un usuario es a través del Portal de registro.</p>
            </td>
            <td>
              <p>Preguntas de seguridad tienen un máximo de 200 caracteres y un mínimo de 3 caracteres</p>
              <p>Respuestas tienen un máximo de 40 caracteres y un mínimo de 3 caracteres</p>
            </td>
          </tr>
        </tbody></table>

###<a name="how-to-access-password-reset-data-for-your-users"></a>Cómo obtener acceso a la contraseña restablece datos para los usuarios
####<a name="data-settable-via-synchronization"></a>Puede establecer mediante la sincronización de datos
Se pueden sincronizar los siguientes campos locales:

* Teléfono móvil
* Teléfono de la oficina

####<a name="data-settable-with-azure-ad-powershell"></a>Puede establecer con Azure AD PowerShell de datos
Los siguientes campos están accesibles con Azure AD PowerShell y la API de gráfico:

* Correo electrónico alternativa
* Teléfono móvil
* Teléfono de la oficina
* Teléfono de autenticación
* Correo electrónico de autenticación

####<a name="data-settable-with-registration-ui-only"></a>Puede establecer con el registro IU de datos
Los siguientes campos solo están disponibles a través del registro SSPR (https://aka.ms/ssprsetup) de la interfaz de usuario:

* Preguntas y respuestas acerca de seguridad

####<a name="what-happens-when-a-user-registers"></a>¿Qué ocurre cuando un usuario se registra?
Cuando un usuario se registra, la página de registro podrá establecer **siempre** los siguientes campos:

* Teléfono de autenticación
* Correo electrónico de autenticación
* Preguntas y respuestas acerca de seguridad

Si dispone de un valor para el **teléfono móvil** o **Correo electrónico alternativa**, los usuarios pueden utilizar inmediatamente los para restablecer las contraseñas, incluso si todavía no lo ha registrados para el servicio.  Además, los usuarios ver dichos valores cuando se registra por primera vez y modificarlos si lo desean.  Sin embargo, una vez que se registren correctamente, estos valores se conservarán en los campos de **Teléfono de autenticación** y **Correo electrónico de autenticación** , respectivamente.

Esto puede ser una manera útil para desbloquear grandes cantidades de usuarios para usar SSPR sin permitiendo a los usuarios validar esta información a través del proceso de registro.

####<a name="setting-password-reset-data-with-powershell"></a>Datos con PowerShell de restablecimiento de contraseña de configuración
Puede establecer los valores de los siguientes campos con Azure AD PowerShell.

* Correo electrónico alternativa
* Teléfono móvil
* Teléfono de la oficina

Para empezar, primero deberá [Descargar](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)e instalar el módulo de PowerShell de Azure AD.  Una vez instalada, puede seguir estos pasos para configurar cada campo.

#####<a name="alternate-email"></a>Correo electrónico alternativa
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
```

#####<a name="mobile-phone"></a>Teléfono móvil
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
```

#####<a name="office-phone"></a>Teléfono de la oficina
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"
```

####<a name="reading-password-reset-data-with-powershell"></a>Datos con PowerShell de restablecimiento de contraseña de lectura
Puede leer los valores de los siguientes campos con Azure AD PowerShell.

* Correo electrónico alternativa
* Teléfono móvil
* Teléfono de la oficina
* Teléfono de autenticación
* Correo electrónico de autenticación

Para empezar, primero deberá [Descargar](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)e instalar el módulo de PowerShell de Azure AD.  Una vez instalada, puede seguir estos pasos para configurar cada campo.

#####<a name="alternate-email"></a>Correo electrónico alternativa
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
```

#####<a name="mobile-phone"></a>Teléfono móvil
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
```

#####<a name="office-phone"></a>Teléfono de la oficina
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber
```

#####<a name="authentication-phone"></a>Teléfono de autenticación
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
```

#####<a name="authentication-email"></a>Correo electrónico de autenticación
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

## <a name="links-to-password-reset-documentation"></a>Vínculos a contraseña restablecer documentación
A continuación se muestran vínculos a todas las páginas de documentación de restablecimiento de contraseña de Azure AD:

* **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [He aquí cómo puede cambiar y restablecer su propia contraseña](active-directory-passwords-update-your-own-password.md).
* [**Cómo funciona**](active-directory-passwords-how-it-works.md) - obtener información sobre los distintos seis componentes del servicio y lo que hace cada uno
* [**Introducción**](active-directory-passwords-getting-started.md) : Obtenga información sobre cómo permitir que los usuarios restablecer y cambiar sus contraseñas de nube o local
* [**Personalizar**](active-directory-passwords-customize.md) - obtener información sobre cómo personalizar el aspecto y apariencia y comportamiento del servicio a las necesidades de su organización
* [**Procedimientos recomendados**](active-directory-passwords-best-practices.md) : Obtenga información sobre cómo implementar rápidamente y eficaz administrar contraseñas en su organización
* [**Obtener recomendaciones**](active-directory-passwords-get-insights.md) - Obtenga más información sobre nuestras capacidades integradas de reporting
* [**Preguntas más frecuentes**](active-directory-passwords-faq.md) : Obtenga respuestas a las preguntas más frecuentes
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md) - Obtenga información sobre cómo solucionar problemas con el servicio de rápidamente



[001]: ./media/active-directory-passwords-learn-more/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-learn-more/002.jpg "Image_002.jpg"
