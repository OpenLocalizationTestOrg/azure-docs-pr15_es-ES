<properties
    pageTitle="Azure Active B2C de directorio: Personalización de interfaz de usuario | Microsoft Azure"
    description="Un tema de las características de personalización de la interfaz de usuario de Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-customize-the-azure-ad-b2c-user-interface-ui"></a>Azure Active B2C de directorio: Personalizar la interfaz de usuario (IU) de Azure AD B2C

Experiencia de usuario es fundamental en una aplicación de consumidor orientación. Es la diferencia entre una buena aplicación y una excelente y entre los consumidores simplemente activos y que consiguen embragado. B2C de Azure Active Directory (AD Azure) le permite personalizar consumidor suscripción, inicio de sesión (*vea la nota siguiente*), edición, el perfil y páginas con control píxel perfecto de restablecimiento de contraseña.

> [AZURE.NOTE]
Actualmente, cuenta local inicio de sesión de páginas, su contraseña accompaning restablecer las páginas y correos electrónicos de verificación pueden personalizarse mediante la [característica de personalización de marca de empresa](../active-directory/active-directory-add-company-branding.md) y no los mecanismos descritos en este artículo.

En este artículo, se describen:

- Característica de personalización de interfaz de usuario de página.
- Una herramienta que le ayudarán a probar la característica de personalización de interfaz de usuario de página con el contenido de ejemplo.
- Los elementos de interfaz de usuario principales en cada tipo de página.
- Prácticas recomendadas para el ejercicio de esta característica.

## <a name="the-page-ui-customization-feature"></a>La característica de personalización de la interfaz de usuario de página

Con la característica de personalización de interfaz de usuario de página, puede personalizar la apariencia del consumidor suscripción, inicio de sesión, páginas de restablecimiento de contraseña y edición de perfil (mediante la configuración de [directivas](active-directory-b2c-reference-policies.md)). Los consumidores tendrán experiencias transparente al navegar entre la aplicación y las páginas servidas por Azure AD B2C.

A diferencia de otros servicios en Opciones de la interfaz de usuario están limitados o solo están disponibles a través de las API de Azure AD B2C utiliza un enfoque moderno (y más sencillo) a la personalización de la interfaz de usuario de página.

¿Cómo funciona: Azure AD B2C se ejecute código en el explorador del consumidor y usa un enfoque moderno denominado [Uso compartido de recursos entre origen (CORS)](http://www.w3.org/TR/cors/) para cargar contenido desde una dirección URL que especifique en una directiva. Puede especificar diferentes direcciones URL para diferentes páginas. El código de elementos de interfaz de usuario de Azure AD B2C combina con el contenido cargado de la dirección URL y muestra la página a los consumidores. Todo lo que debe hacer es:

1. Crear contenido con HTML5 correcto una `<div id="api"></div>` elemento (debe ser un elemento vacío) ubicado en algún lugar en el `<body>`. Marcas de este elemento donde se inserta el contenido de Azure AD B2C.
2. Hospedar el contenido de un extremo HTTPS (con CORS permitido).
3. Estilo de los elementos de interfaz de usuario que inserta Azure AD B2C en.

## <a name="test-out-the-ui-customization-feature"></a>Probar la característica de personalización de la interfaz de usuario

Si desea probar la característica de personalización de la interfaz de usuario con nuestro ejemplo HTML y el contenido CSS, le proporcionamos [una herramienta auxiliar sencillo](active-directory-b2c-reference-ui-customization-helper-tool.md) que carga y configura el contenido de ejemplo en el almacenamiento de blobs de Windows Azure.

> [AZURE.NOTE]
Puede hospedar el contenido de la interfaz de usuario en cualquier lugar: en servidores web, CDN, AWS S3, sistemas de uso compartido de archivos, etcetera. Siempre que el contenido está alojado en un extremo HTTPS disponible públicamente (con CORS permitido), ya está listo. Estamos usando almacenamiento de blobs de Windows Azure solo con fines ilustrativos.

### <a name="the-most-basic-html-content-for-testing"></a>El contenido HTML más básico para realizar pruebas

Se muestra a continuación es el código HTML más básico contenido que puede usar para probar esta función. Use la misma herramienta auxiliar proporcionada anteriormente para cargar y configurar este contenido en el almacenamiento de blobs de Windows Azure. A continuación, puede comprobar que los botones básicos, no estilizado y campos de formulario en cada página se muestran y funcionales.

```HTML

<!DOCTYPE html>
<html>
    <head>
        <title>!Add your title here!</title>
    </head>
    <body>
        <div id="api"></div>    <!-- IMP: This element is intentionally empty; don't enter anything here -->
    </body>
</html>

```

## <a name="the-core-ui-elements-in-each-type-of-page"></a>Los elementos de interfaz de usuario principales en cada tipo de página

En las secciones siguientes, encontrará ejemplos de fragmentos de HTML5 Azure AD B2C combina en la `<div id="api"></div>` elemento se encuentra en el contenido. **No se pueden insertar estos fragmentos en el contenido HTML 5.** El servicio de Azure AD B2C en inserta en tiempo de ejecución. Use estos ejemplos para diseñar sus propias hojas de estilo.

### <a name="azure-ad-b2c-content-inserted-into-the-identity-provider-selection-page"></a>Contenido de AD B2C Azure insertado en la "página de selección de proveedor de identidades"

Esta página contiene una lista de proveedores de identidades que el usuario puede elegir durante el inicio de sesión o inicio de sesión. Estos son los proveedores de identidades social como Facebook y Google +, o bien cuentas locales (según el nombre de usuario o dirección de correo electrónico).

```HTML

<div id="api" data-name="IdpSelections">
    <div class="intro">
         <p>Sign up</p>
    </div>

    <div>
        <ul>
            <li>
                <button class="accountButton" id="FacebookExchange">Facebook</button>
            </li>
            <li>
                <button class="accountButton" id="GoogleExchange">Google+</button>
            </li>
            <li>
                <button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
            </li>
        </ul>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-local-account-sign-up-page"></a>Contenido de AD B2C Azure insertado en la "página de registro de cuenta Local"

Esta página contiene un formulario de inicio de sesión que el usuario tiene que rellenar al suscribirse a una cuenta local que se basa en una dirección de correo electrónico o un nombre de usuario. El formulario puede contener distintos controles de entrada como cuadro de entrada de texto, cuadro de entrada de contraseña, botón de radio, cuadros de lista desplegable de selección simple y casillas de verificación de selección múltiple.

```HTML

<div id="api" data-name="SelfAsserted">
    <div class="intro">
        <p>Create your account by providing the following details</p>
    </div>

    <div id="attributeVerification">
        <div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
        <div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
        <div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
        <div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
        <div class="attr" id="attributeList">
            <ul>
                <li>
                    <div class="attrEntry validate">
                        <div>
                            <div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
                            <div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
                            <div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
                            <div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
                            <div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
                            <div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
                            <div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
                        </div>

                    <div class="helpText show">This information is required</div>
                        <label>Email</label>
                        <input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

                    <div class="buttons verify" claim_id="email">
                        <div id="email_ver_wait" class="working" style="display: none;"></div>
                            <label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label>
                            <input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
                            <button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
                            <button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
                            <button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
                            <button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
                            <button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button>
                        </div>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
                        <label>Enter password</label>
                        <input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ &quot; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"> This information is required</div>
                        <label>Reenter password</label>
                        <input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Name</label>
                        <input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Gender</label>
                        <input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
                        <label for="extension_Gender_F">Female</label>
                        <input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
                        <label for="extension_Gender_M">Male</label>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Loyalty number</label>
                        <input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>State</label>
                        <select class="dropdown_single" id="state">
                            <option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
                            <option value="WA">Washington</option>
                            <option value="NY">New York</option>
                            <option value="CA">California</option>
                        </select>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Zip code</label>
                        <input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
                    </div>
                </li>
            </ul>
        </div>
        <div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
    </div>
    <div class="verifying-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="verifying_blurb"></div>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-social-account-sign-up-page"></a>Azure AD B2C contenido que se inserte en la "cuenta Social suscripción"página"

Esta página contiene un formulario de inicio de sesión que el usuario tiene que rellenar al iniciar sesión con una cuenta existente desde un proveedor de identidades social como Facebook o Google +. Esta página es similar a la página Inicio de sesión de la cuenta local (que se muestra en la sección anterior) con la excepción de los campos de entrada de contraseña.

### <a name="azure-ad-b2c-content-inserted-into-the-unified-sign-up-or-sign-in-page"></a>Contenido de AD B2C Azure insertado en "unificado suscripción o inicio de sesión en la página"

Esta página controla tanto suscripción & inicio de sesión de los consumidores, que pueden usar proveedores de identidades social como Facebook o Google + o cuentas locales.

```HTML

<div id="api" data-name="Unified">
        <div class="social" role="form">
               <div class="intro">
                       <h2>Sign in with your social account</h2>
               </div>
               <div class="options">
                       <div><button class="accountButton firstButton" id="MicrosoftAccountExchange" tabindex="1">msa</button></div>
                       <div><button class="accountButton" id="FacebookExchange" tabindex="1">fb</button></div>
               </div>
        </div>
        <div class="divider">
               <h2>OR</h2>
        </div>
        <div class="localAccount" role="form">
               <div class="intro">
                       <h2>Sign in with your existing account</h2>
               </div>
               <div class="error pageLevel" aria-hidden="true" style="display: none;">
                       <p role="alert"></p>
               </div>
               <div class="entry">
                       <div class="entry-item">
                               <label for="logonIdentifier">Email Address</label> 
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="email" id="logonIdentifier" name="LogonIdentifier" pattern="^[a-zA-Z0-9.!#$%&amp;’*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" placeholder="LogonIdentifier" value="" tabindex="1">
                       </div>
                       <div class="entry-item">
                               <div class="password-label"> <label for="password">Password</label><a id="forgotPassword" tabindex="2">Forgot your password?</a></div>
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="password" id="password" name="Password" placeholder="Password" tabindex="1">
                       </div>
                       <div class="working"></div>
                       <div class="buttons"> <button id="next" tabindex="1">Sign in</button> </div>
               </div>
               <div class="divider">
                       <h2>OR</h2>
               </div>
               <div class="create">
                       <p>Don't have an account?<a id="createAccount" tabindex="1">Sign up now</a> </p>
               </div>
        </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-multi-factor-authentication-page"></a>Contenido de AD B2C Azure insertada en la página de autenticación multifactor de""

En esta página, los usuarios pueden comprobar sus números de teléfono (con el texto o voz) durante la suscripción o inicio de sesión.

```HTML

<div id="api" data-name="Phonefactor">
    <div id="phonefactor_initial">
        <div class="intro">
            <p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
        </div>
        <div class="errorText" id="errorMessage" style="display:none"></div>
        <div class="phoneEntry" id="phoneEntry">
            <div class="phoneNumber">
                <select id="countryCode" style="display:inline-block">
                    <option value="+93">Afghanistan (+93)</option>
                    <!-- Not all country codes listed -->
                    <option value="+44">United Kingdom (+44)</option>
                    <option value="+1" selected="">United States (+1)</option>
                    <!-- Not all country codes listed -->
                </select>
            </div>
            <div class="phoneNumber">
                <input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
            </div>
        </div>
        <div id="codeVerification" style="display:none">
            <div>
                <p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
                <input type="text" id="verificationCode" placeholder="Verification code">
            </div>
        </div>
        <div class="buttons">
            <button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
            <button id="verifyPhone" style="display:inline-block">Call Me</button>
            <button id="cancel" style="display:inline-block">Cancel</button>
        </div>
    </div>
    <div class="dialing-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="dialing_blurb"></div><div id="dialing_number"></div>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-error-page"></a>Contenido de AD B2C Azure insertado en la "Error"página"

```HTML

<div id="api" class="error-page-content" data-name="GlobalException">
    <h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
    <div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
    <div class="error-page-messagedetails">
        <div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
        <div>Timestamp:2015-09-14 23:22:35Z</div>
        <div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
    </div>
</div>

```

## <a name="things-to-remember-when-building-your-own-content"></a>Cosas que debe recordar al crear su propio contenido

Si va a usar la característica de personalización de la interfaz de usuario de página, revise las siguientes prácticas recomendadas:

- No copiar el contenido de forma predeterminada del B2C de Azure AD e intenten modificarlo. Es mejor para generar el contenido de HTML5 desde cero y usar contenido predeterminado como referencia.
- En todas las páginas (excepto las páginas de Error) servidas por el inicio de sesión, inicio de sesión y directivas de perfil de edición, hojas de estilo que proporciona tendrán que sustituir las hojas de estilo predeterminado que se agregan en estas páginas en el <head> fragmentos. En todas las páginas servidas por la suscripción o inicio de sesión y directivas de restablecimiento de contraseña y las páginas de Error en todas las directivas, tendrá que proporcionar todos los estilos usted mismo.
- Por motivos de seguridad, no podrá incluir cualquier JavaScript en su contenido. La mayoría de lo que necesita debe estar disponible fuera del cuadro. Si no es así, utilice la [Voz de usuario](http://feedback.azure.com/forums/169401-azure-active-directory) para solicitar nueva funcionalidad.
- Versiones de explorador compatibles:
    - Internet Explorer 11
    - Internet Explorer 10
    - Internet Explorer 9 (limitado)
    - Internet Explorer 8 (limitado)
    - Google Chrome 43.0
    - Google Chrome 42.0
    - Mozilla Firefox 38.0
    - Mozilla Firefox 37.0
