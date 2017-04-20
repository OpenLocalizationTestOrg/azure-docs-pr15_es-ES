
<properties
    pageTitle="Azure Active Directory híbrido identidad consideraciones de diseño - determinar los requisitos de control de acceso | Microsoft Azure"
    description="Trata sobre los pilares de identidad y la identificación de los requisitos de acceso para los recursos para los usuarios en un entorno híbrido."
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>Determinar los requisitos de control de acceso para la solución de identidad híbrido
Cuando una organización está diseñando su solución de identidad híbrida también puede utilizar esta oportunidad para revisar los requisitos de acceso para los recursos que tiene previsto para ponerlo a disposición de los usuarios. El acceso a datos entre todos los cuatro pilares de identidad, que son:

- Administración
- Autenticación
- Autorización
- Auditoría

Las secciones que sigue cubrirá autenticación y la autorización de obtener más detalles, administración y auditoría forman parte del ciclo de vida de identidad híbrido. Leer [tareas de administración de identidades de determinar híbrido](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) para obtener más información sobre estas funciones.

>[AZURE.NOTE]
Lea [Los cuatro pilares de identidad - la administración de identidades en la edad de TI híbrido](http://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) para obtener más información acerca de cada uno de los pilares.

## <a name="authentication-and-authorization"></a>Autenticación y la autorización
Existen diferentes escenarios para la autenticación y la autorización, estos escenarios tendrá requisitos específicos que deben cumplirse por la solución de identidad híbrida que la compañía se va a adoptar. Escenarios que incluyen la comunicación entre empresas (B2B) pueden agregar un desafío adicional para administradores de TI ya será necesario para asegurarse de que el método de autenticación y la autorización usado por la organización puede comunicarse con sus socios empresariales. Durante el proceso de diseño para los requisitos de autenticación y la autorización, asegúrese de que se responden las preguntas siguientes:

- ¿Su organización autenticar y autorizar únicamente los usuarios que se encuentra en su sistema de administración de identidad?
 - ¿Existen planes para escenarios B2B?
 - Si es así, ¿ya sabe qué protocolos (SAML, OAuth, Kerberos, Tokens o certificados) se usará para conectar ambas empresas?
- ¿Es la solución de identidad híbrida que va a adoptar soporte técnico los protocolos?

Otro punto importante para tener en cuenta es donde se encuentran el repositorio de autenticación que se usará por usuarios y socios y el modelo administrativo para usarse. Tenga en cuenta las siguientes opciones de dos principales:
- Centralizada: en este modelo las credenciales del usuario, directivas y administración pueden ser centralizada local o en la nube.
- Híbrido: en este modelo las credenciales del usuario, directivas y administración será centralizada local y un replicada en la nube.

Qué modelo de su organización adoptará varían según sus requisitos empresariales que desea responder a las preguntas siguientes para identificar donde reside el sistema de administración de identidad y el modo administrativo para:

- ¿Su organización tiene actualmente la administración de identidades local?
 - Si es así, ¿van a mantenerlo?
 - ¿Hay los requisitos de cumplimiento o Reglamento que su organización debe seguir que indica debe residen en el sistema de administración de identidad?
- ¿Su organización utiliza el inicio de sesión único para aplicaciones encuentra local o en la nube?
 - ¿En caso afirmativo, la adopción de un modelo de identidad híbrido afecta este proceso?

## <a name="access-control"></a>Control de acceso
Aunque la autenticación y la autorización son elementos básicos para habilitar el acceso a los datos corporativos a través de validación de usuario, también es importante controlar el nivel de acceso que tendrán los usuarios y el nivel de administradores de acceso tendrán sobre los recursos que se están administrando. La solución de identidad híbrido puedan proporcionar acceso granular para los recursos, delegación y control de acceso de base de la función. Asegúrese de que la siguiente pregunta se responden relacionada con el control de acceso:

- ¿Su empresa tiene más de un usuario con privilegios elevados para administrar el sistema de identidad?
 - ¿En caso afirmativo, cada usuario tiene el mismo nivel de acceso?
- ¿Su empresa necesita delegar el acceso a los usuarios administrar recursos específicos?
 - Si es así, ¿con qué frecuencia esto sucede?
- ¿Su compañía necesarias para integrar las capacidades de control de acceso entre local y la nube recursos?
- ¿Su compañía necesarias para limitar el acceso a los recursos según algunas condiciones?
- ¿Su empresa tiene cualquier aplicación que necesita personalizado controlar el acceso a algunos recursos?
 - ¿En caso afirmativo, donde se encuentran las aplicaciones (local o en la nube)?
 - ¿En caso afirmativo, donde están los destinar recursos ubicados (local o en la nube)?

>[AZURE.NOTE]
Asegúrese de tomar notas de cada respuesta y entender la razón para la respuesta. [Definir la estrategia de protección de datos](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) irán sobre las opciones disponibles y ventajas y desventajas de cada opción.  Respondiendo a las preguntas que seleccione la opción que mejor se adapte a sus necesidades empresariales.

## <a name="next-steps"></a>Pasos siguientes

[Determinar los requisitos de respuesta a incidencias](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Vea también
[Información general de las consideraciones de diseño](active-directory-hybrid-identity-design-considerations-overview.md)
