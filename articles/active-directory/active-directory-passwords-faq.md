<properties
    pageTitle="Preguntas más frecuentes: Administración de contraseña de Azure AD | Microsoft Azure"
    description="Preguntas más frecuentes (P+F) sobre la administración de contraseñas en Azure AD, incluido de restablecimiento de contraseña, registro, informes y reescritura a local de Active Directory."
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

# <a name="password-management-frequently-asked-questions"></a>Preguntas más frecuentes sobre la administración de contraseñas

> [AZURE.IMPORTANT] **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [He aquí cómo puede cambiar y restablecer su propia contraseña](active-directory-passwords-update-your-own-password.md).

Las siguientes son algunas preguntas frecuentes para todos los elementos relacionados con la administración de la contraseña.

Si se encuentra con una pregunta que no conoce la respuesta a o busca ayuda con un problema en particular que se opuestas, puede leer en a continuación para ver si nos hemos tratado ya.  Si no lo hemos hecho ya, no se preocupe! No dude en cualquier pregunta tiene que no se incluye aquí en los [foros de Azure AD](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD) y se deberá volver a tan pronto como lo posible.

Estas preguntas más frecuentes se dividen en las secciones siguientes:

- [**Preguntas acerca del registro de restablecimiento de contraseña**](#password-reset-registration)
- [**Preguntas sobre el restablecimiento de contraseña**](#password-reset)
- [**Preguntas sobre los informes de administración de contraseña**](#password-management-reports)
- [**Preguntas sobre reescritura de contraseña**](#password-writeback)

## <a name="password-reset-registration"></a>Registro de restablecimiento de contraseña
 - **P: ¿pueden los usuarios registrar sus propios datos de restablecimiento de contraseña?**

 > **A:** Sí, siempre y cuando se habilita el restablecimiento de contraseña y tienen licencia, puede ir al portal de registro de restablecimiento de contraseña en http://aka.ms/ssprsetup para registrar su información de autenticación para su uso con el restablecimiento de contraseña. Los usuarios también pueden registrar ir al panel de acceso en http://myapps.microsoft.com, haga clic en la ficha perfil y haciendo clic en el registro para la opción de restablecimiento de contraseña. Más información sobre cómo los usuarios configurados para restablecer Lea cómo obtener usuarios configurados para restablecer contraseña contraseñas.

 - **P: ¿definir datos de restablecimiento de contraseña en el nombre de Mis usuarios?**

 > **A:** Sí, puede hacerlo con DirSync PowerShell, o a través del portal de [Portal de administración de Azure](https://manage.windowsazure.com) o administrador de Office. Más información sobre esta característica en la entrada de blog privacidad mejorada para Azure AD AMF y números de teléfono de restablecimiento de contraseña y leyendo, obtenga información sobre cómo se utilizan los datos por contraseña restablecen.

 - **P: ¿puedo sincronizar los datos de preguntas de seguridad de local?**

 > **A:** No, no es posible hoy, pero se habla.

 - **P: ¿pueden mis usuarios registrar datos de forma que otros usuarios no pueden ver estos datos?**

 > **A:** Sí, cuando los usuarios registran datos con el Portal de registro de restablecimiento de contraseña se guarda en los campos de autenticación privado que sólo son visibles por los administradores globales y el usuario sí mismo. Más información sobre esta característica en la entrada de blog privacidad mejorada para Azure AD AMF y números de teléfono de restablecimiento de contraseña y leyendo, obtenga información sobre cómo se utilizan los datos por contraseña restablecen.

 - **P: ¿Mis usuarios tienen que registrarse para que puedan usar el restablecimiento de contraseña?**

 > **A:** No, si define suficiente información de autenticación en su nombre, los usuarios no tendrá que registrar. Restablecer contraseña funcionará correctamente como correctamente haya dado formato a los datos almacenados en los campos adecuados en el directorio. Obtenga más información sobre leyendo Obtenga información sobre cómo se utilizan los datos por el restablecimiento de contraseña.

 - **P: ¿puedo sincronizar o establecer los campos de correo electrónico de autenticación o teléfono alternativo de autenticación, el teléfono de autenticación en nombre de Mis usuarios?**

 > **A:** No actualmente, pero estamos considerando habilitar esta función.

 - **P: ¿cómo sabe el portal de registro a las opciones de mostrar mis usuarios?**

 > **A:** El portal de registro de restablecimiento de contraseña solo muestra las opciones que ha habilitado para los usuarios en la sección de la directiva de restablecimiento de contraseña de usuario de la ficha de configurar el directorio. Esto significa que si no habilita, diga preguntas de seguridad, a continuación, los usuarios no podrán registrarse para dicha opción.

 - **P: ¿cuándo se considera un usuario registrado?**

 > **A:** Se considera un usuario registrado cuando tiene al menos definido segmentos de N de información de autenticación, donde N es el número de métodos requiere autenticación que haya establecido en el [Portal de administración de Azure](https://manage.windowsazure.com). Para obtener más información, consulte Personalización de usuario restablecer la directiva de contraseña.


## <a name="password-reset"></a>Restablecimiento de contraseña

 - **P: ¿cuánto tiempo debo esperar para recibir un correo electrónico, SMS o llamada telefónica de restablecimiento de contraseña?**

 > **A:** Correo electrónico, mensajes SMS y llamadas telefónicas llegarán en menos de 1 minuto, con el caso normal 5-20 segundos. Si no recibe la notificación en este intervalo de tiempo, compruebe la carpeta correo no deseada, que el número de contacto de correo electrónico es el que espera, y que los datos de autenticación en el directorio tiene el formato correcto. Para obtener más información sobre el formato de números de teléfono y correo electrónico direcciones para su uso con el restablecimiento de contraseña consulte Descubra cómo se utilizan los datos por el restablecimiento de contraseña.

 - **P: ¿qué idiomas son compatibles con el restablecimiento de contraseña?**

 > **A:** Interfaz de usuario para restablecer la contraseña mensajes SMS y llamadas de voz están localizados en la misma 40 idiomas admitidos en Office 365. Estos son: árabe, búlgaro, chino simplificado, chino tradicional, croata, checo, danés, neerlandés, inglés, estonio, finlandés, francés, alemán, griego, hebreo, Hindi, húngaro, indonesio, italiano, japonés, kazajo, coreano, letón, lituano, malayo (Malaysia), noruego (Bokmål), polaco, portugués (Brasil), portugués (Portugal), rumano, ruso, serbio (latino), eslovaco, esloveno, español, sueco, tailandés, turco, ucraniano y vietnamita.

 - **P: ¿qué partes de la experiencia de restablecimiento de contraseña obtengan marca cuando establezco organizativa marca en mi directorio de configurar pestaña?**

 > **A:** El portal de restablecimiento de contraseña mostrará el logotipo de su organización y también le permitirá configurar el contacto el vínculo de administrador para que apunten a un correo electrónico personalizado o la dirección URL. Cualquier correo electrónico que se envía por el restablecimiento de contraseña incluirá el logotipo de su organización, colores (en este caso rojos), nombre en el cuerpo del correo electrónico y personalizar de nombre. Vea un ejemplo con todos los elementos con su marca siguiente. Para obtener más información, lea el restablecimiento de contraseña personalizar apariencia.

  ![][001]

 - **P: ¿Cómo puedo informar a los usuarios sobre dónde ir para restablecer su contraseña?**

 > **A:** Puede enviar los usuarios directamente a https://passwordreset.microsoftonline.com, o bien puede indicar que haga clic en el no puede acceder a su vínculo de cuenta se encuentra en cualquier escuela o trabajo ID pantalla iniciar sesión. Puede dude publicar estos vínculos (o crear redireccionamientos de URL a ellos) en cualquier lugar que esté disponible para los usuarios.

 - **P: ¿puedo usar esta página desde un dispositivo móvil?**

 > **A:** Sí, esta página funciona en dispositivos móviles.

 - **P: ¿tiene compatibilidad con cuentas de active directory local desbloquear cuando los usuarios restablezcan sus contraseñas?**

 > **A:** Sí, cuando un usuario restablece su contraseña y reescritura de contraseña se ha implementado con todas las versiones de Azure AD Connect o con versiones de sincronización de Azure AD 1.0.0485.0222 o posterior, esa cuenta de usuario será desbloqueada automáticamente cuando ese usuario restablece su contraseña.

 - **P: ¿Cómo puedo integrar contraseña restablecer directamente en la experiencia de inicio de inicio de sesión de escritorio de los usuarios?**

 > **A:** Esto no es posible hoy. Sin embargo, si estrictamente necesita esta capacidad y es un cliente de Azure AD Premium, puede instalar el Administrador de identidad de Microsoft sin costo adicional e implementar la solución de restablecimiento de contraseña local se encuentra en él para solucionar este requisito.

 - **P: ¿puedo establecer preguntas de seguridad diferentes para diferentes configuraciones regionales?**

 > **A:** No, no es posible hoy, pero se habla.

 - **P: ¿cuántas preguntas podemos configurar la opción de autenticación de preguntas de seguridad?**

 > **A:** Puede configurar hasta 20 preguntas de seguridad personalizado en el [Portal de administración de Azure](https://manage.windowsazure.com).

 - **P: ¿cuánto tiempo puede preguntas de seguridad?**

 > **A:** Preguntas de seguridad pueden entre 3 y 200 caracteres.

 - **P: ¿cuánto tiempo puede respuestas a preguntas de seguridad?**

 > **A:** Respuestas pueden tener 3 a 40 caracteres.

 - **P: ¿son duplicadas respuestas a preguntas de seguridad rechazados?**

 > **A:** Sí, nos rechazar duplicadas respuestas a preguntas de seguridad.

 - **P: ¿puede un usuario registrar más de uno de la misma pregunta de seguridad?**

 > **A:** No, una vez que un usuario registra una pregunta concreta, que no puede registrar para esa pregunta una segunda vez.

 - **P: ¿es posible definir un límite mínimo de preguntas de seguridad para el registro y restablecer?**

 > **A:** Sí, puede establecerse un límite para el registro y otro para restablecer. 3-5 preguntas de seguridad podrían ser necesarios para el registro y 3-5 podrían ser necesarios para restablecer.

 - **P: si un usuario se ha registrado más que el número máximo de preguntas necesarios para restablecer, ¿cómo se seleccionan las preguntas de seguridad durante el restablecimiento de?**

 > **A:** Preguntas de seguridad N se seleccionan al azar del número total de preguntas que se ha registrado un usuario, donde N es el número mínimo de preguntas necesarios para restablecer contraseña. Por ejemplo, si un usuario tiene 5 preguntas de seguridad registrados, pero solo 3 necesarios para restablecer, 3 de los 5 se selecciona aleatoriamente y presenta al usuario en el momento de restablecer. Si el usuario obtiene las respuestas a las preguntas sobre un error, el proceso de selección se produce de nuevo para evitar la pregunta hammering.

 - **P: ¿impedir que los usuarios intentar restablecer muchas veces en un período de tiempo corto contraseñas?**

 > **A:** Sí, hay varias características de seguridad integradas en Restablecer contraseña. Los usuarios solo pueden intentar 5 intentos de restablecimiento de contraseña dentro de una hora antes de que se bloquee 24 horas. Los usuarios solo pueden intentar validar un número de teléfono 5 veces dentro de una hora antes de que se bloquee 24 horas. Los usuarios solo pueden intentar un método de autenticación solo 5 veces dentro de una hora antes de que se bloquee 24 horas.

 - **P: ¿durante cuánto tiempo son el correo electrónico y el código de acceso de plantilla de SMS válidos?**

 > **A:** La duración de la sesión de restablecimiento de contraseña es 105 minutos. Esto significa que la operación de restablecimiento de desde el principio de la contraseña, el usuario tiene 105 minutos para restablecer su contraseña. El correo electrónico y el código de acceso de plantilla de SMS no son válidos después de este período de tiempo.


## <a name="password-management-reports"></a>Informes de administración de contraseñas

 - **P: ¿cuánto tiempo tarda para que los datos que se muestran en los informes de administración de la contraseña?**

 > **A:** Datos deberían aparecer en los informes de administración de contraseña en 5-10 minutos. Algunos casos pueden tardar hasta una hora que aparezca.

 - **P: ¿Cómo puedo filtrar los informes de administración de contraseña?**

 > **A:** Puede filtrar los informes de administración de contraseña haciendo clic en la lupa pequeña al extremo derecho de las etiquetas de columna, en la parte superior del informe (consulte la captura de pantalla). Si desea hacer más rica filtrado, puede descargar el informe de excel y cree una tabla dinámica.

  ![][002]

 - **P: ¿cuál es el número máximo de eventos se almacenan en los informes de administración de la contraseña?**

 > **A:** Hasta 1.000 contraseña se almacenan los eventos de registro de restablecimiento de contraseña o restablecer en los informes de administración de la contraseña.  Estamos trabajando para expandir este número para incluir más eventos.

 - **P: ¿cuánto vaya a los informes de administración de la contraseña?**

 > **A:** Los informes de administración de la contraseña muestran las operaciones que se producen dentro de los últimos 30 días. Estamos investigando cómo hacer esto en un período de tiempo más largo. Por ahora, si necesita archivar estos datos, puede descargar los informes periódicamente y guardarlos en una ubicación diferente.

 - **P: ¿hay un número máximo de filas que pueden aparecer en los informes de administración de la contraseña?**

 > **A:** Sí, puede aparecer un máximo de 1.000 filas en cualquiera de los informes de administración de contraseñas, si se muestran en la interfaz de usuario o que se ha descargado. Estamos investigando cómo aumentar este límite.

 - **P: ¿existe una API para tener acceso al restablecimiento de contraseñas o datos de los informes de registro?**

 > **A:** Sí, vea la siguiente documentación para obtener información sobre cómo tener acceso a lo informes de flujo de datos de restablecimiento de contraseña.  [Descubra cómo tener acceso a la contraseña restablece informar eventos mediante programación](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).

## <a name="password-writeback"></a>Reescritura de contraseña
 - **P: ¿cómo funciona la reescritura de contraseña en segundo plano?**

 > **A:** Para obtener una explicación detallada de lo que sucede cuando se habilita la reescritura de contraseña, así como la flujos de datos a través del sistema en su entorno local, consulte [que cómo contraseña reescritura funciona](active-directory-passwords-learn-more.md#how-password-writeback-works) . Ver que el [modelo de seguridad de reescritura de contraseña](active-directory-passwords-learn-more.md#password-writeback-security-model) en cómo reescritura de contraseña funciona para obtener información sobre cómo garantizamos que reescritura de contraseña es un servicio de alta seguridad.

 - **P: ¿cuánto reescritura de contraseña tarda trabajar?  ¿Hay un retraso de sincronización como con la sincronización de hash de contraseña?**

 > **A:** Reescritura de contraseña es instantánea. Es una canalización sincrónica que funciona esencialmente diferente de sincronización de contraseñas hash. Reescritura de contraseña permite a los usuarios obtener comentarios en tiempo real sobre el éxito su de restablecimiento de contraseña o cambiar la operación. El tiempo medio de una reescritura de la contraseña correcta es en 500 ms.

 - **P: ¿qué tipos de cuentas funciona reescritura de contraseña para?**

 > **A:** Reescritura de contraseña funciona con federado y contraseña Hash sincronizados los usuarios.

 - **P: ¿contraseña reescritura exigir directivas de contraseña de mi dominio?**

 > **A:** Sí, reescritura de contraseña exige la duración de la contraseña, historial, complejidad, filtros y cualquier otra restricción que se puede colocar en lugar de contraseñas en el dominio local.

 - **P: ¿es reescritura de contraseña segura?  ¿Cómo puedo asegurarme de que no obtenga entrado en mi sistema?**

 > **A:** Sí, reescritura de contraseña es muy seguro. Para obtener más información acerca de las 4 capas de seguridad implementación por el servicio de reescritura de contraseña, desproteja el [modelo de seguridad de contraseña reescritura](active-directory-passwords-learn-more.md#password-writeback-security-model) de reescritura de contraseña cómo funciona.




## <a name="links-to-password-reset-documentation"></a>Vínculos a contraseña restablecer documentación
A continuación se muestran vínculos a todas las páginas de documentación de restablecimiento de contraseña de Azure AD:

* **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [He aquí cómo puede cambiar y restablecer su propia contraseña](active-directory-passwords-update-your-own-password.md).
* [**Cómo funciona**](active-directory-passwords-how-it-works.md) - obtener información sobre los distintos seis componentes del servicio y lo que hace cada uno
* [**Introducción**](active-directory-passwords-getting-started.md) : Obtenga información sobre cómo permitir que los usuarios restablecer y cambiar sus contraseñas de nube o local
* [**Personalizar**](active-directory-passwords-customize.md) - obtener información sobre cómo personalizar el aspecto y apariencia y comportamiento del servicio a las necesidades de su organización
* [**Procedimientos recomendados**](active-directory-passwords-best-practices.md) : Obtenga información sobre cómo implementar rápidamente y eficaz administrar contraseñas en su organización
* [**Obtener recomendaciones**](active-directory-passwords-get-insights.md) - Obtenga más información sobre nuestras capacidades integradas de reporting
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md) - Obtenga información sobre cómo solucionar problemas con el servicio de rápidamente
* [**Más información**](active-directory-passwords-learn-more.md) - vaya profundidad los detalles técnicos de cómo funciona el servicio


[001]: ./media/active-directory-passwords-faq/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-faq/002.jpg "Image_002.jpg"
