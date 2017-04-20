<properties
    pageTitle="Identidad de Azure Active Directory híbrido consideraciones de diseño: determinar las tareas de administración de identidades híbrido | Microsoft Azure"
    description="Con control de acceso condicional Azure Active Directory comprueba las condiciones específicas que elegir para autenticar al usuario y antes de permitir el acceso a la aplicación. Una vez que se cumplen las condiciones, el usuario autenticado y permite el acceso a la aplicación."
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

# <a name="plan-for-hybrid-identity-lifecycle"></a>Planificación de ciclo de vida de identidad híbrido 

Identidad es una de las bases de su estrategia de acceso de movilidad y aplicación de empresa. Si se firma su dispositivo móvil o aplicación SaaS, su identidad es la clave para obtener acceso a todo. En su nivel superior, una solución de administración de identidades abarca unificar y sincronización entre los repositorios de identidades que incluye automatización y centralizar el proceso de aprovisionamiento de recursos. La solución de identidad debe ser una identidad centralizada en local y la nube y también usar algún tipo de federación de identidades para mantener la autenticación centralizada y segura de compartir y colaborar con usuarios externos y empresas. Recursos de rango de sistemas operativos y aplicaciones a las personas de o relacionado con una organización. Estructura organizativa puede modificarse para adaptarse a las directivas y procedimientos de aprovisionamiento.

También es importante tener una solución de identidad orientada a ofrecerle a los usuarios les ofrece experiencias de autoservicio para mantenerlas a la productividad. La solución de identidad es más eficaz si habilita un inicio de sesión único para los usuarios en todos los recursos que necesitan tener acceso a los administradores en todos los niveles pueden utilizar procedimientos estándar para administrar las credenciales de usuario. Algunos niveles de administración pueden ser reducidos o eliminados, dependiendo del alcance de la solución de administración de aprovisionamiento. Además, puede segura distribuir capacidades de administración manual o automáticamente, entre varias organizaciones. Por ejemplo, un administrador de dominio puede servir solo las personas y recursos de ese dominio. Este usuario puede realizar las tareas administrativas y aprovisionamiento, pero no está autorizado para realizar tareas de configuración, como la creación de flujos de trabajo.


## <a name="determine-hybrid-identity-management-tasks"></a>Determinar las tareas de administración de identidades híbrido
Distribuir las tareas administrativas en su organización mejora la precisión y la eficacia de la administración y mejora el equilibrio de carga de trabajo de una organización. Los siguientes son los cambios que definen un sistema de administración de identidades sólidos.

 ![](./media/hybrid-id-design-considerations/Identity_management_considerations.png)


Para definir las tareas de administración de identidades híbrido, debe conocer algunas características esenciales de la organización que estarán adoptando la identidad híbrido. Es importante conocer los repositorios actuales que se usa para los orígenes de identidad. Al conocer esos elementos básicos, tendrá los requisitos básicos y, a continuación, en función de que debe formular preguntas más granulares que le llevará a una mejor decisión de diseño para la solución de identidad.  

Al definir dichos requisitos, asegúrese de que al menos las siguientes preguntas

- Opciones de aprovisionamiento: 
 - ¿Es compatible con la solución de identidad híbrida un sistema de aprovisionamiento y administración de cuentas sólido acceso?
 - ¿Cómo son los usuarios, grupos y contraseñas será administrado?
 - ¿Es la administración de ciclo de vida de identidad con capacidad de respuesta? 
      - ¿Cuánto tiempo tarda en suspensión de cuenta de actualizaciones de contraseña?
      
- Administración de licencias: 
 - ¿Es la administración de licencia híbrido controladores de solución de identidad?
     - Si es así, ¿qué capacidades están disponibles?
- ¿La solución encargarse de la administración de licencia basada en grupo? 
      ¿-En caso afirmativo, es posible asignarle un grupo de seguridad? 
       ¿-En caso afirmativo, el directorio de nube asignará automáticamente licencias a todos los miembros del grupo? 
        -¿Qué sucede si un usuario posteriormente se agrega o quita del grupo, se una licencia automáticamente asignar o quitar según corresponda? 

- Integración con otros proveedores de identidades de terceros:
- ¿Se puede integrar esta solución híbrida con proveedores de identidades de terceros para implementar el inicio de sesión único?
- ¿Es posible unificar todos los proveedores de identidades diferentes en un sistema de identidad consistente?
- ¿En caso afirmativo, cómo y que se y qué capacidades están disponibles?

## <a name="synchronization-management"></a>Administración de sincronización
Uno de los objetivos de un administrador de identidad, puedan poner todos los proveedores de identidades y mantenerlos sincronizados. Mantener los datos sincronizados basada en un proveedor de identidades maestra relevantes. En un escenario de identidad híbrido, con un modelo de administración sincronizados y administrar todas las identidades de usuario y del dispositivo en un servidor local y sincronizar las cuentas y, opcionalmente, las contraseñas en la nube. El usuario escribe la contraseña mismo local como él o lo hace en la nube y en el inicio de sesión, se verificará la contraseña de la solución de identidad. Este modelo utiliza una herramienta de sincronización de directorios.
 
![](./media/hybrid-id-design-considerations/Directory_synchronization.png)Para diseño adecuado de la sincronización de la solución de identidad híbrido asegurarse de que se responden las preguntas siguientes: • ¿Cuáles son las soluciones de sincronización disponibles para la solución de identidad híbrida?
• ¿Cuáles son la funciones disponibles de inicio de sesión único?
• ¿Cuáles son las opciones para la federación de identidades entre B2B y B2C?

## <a name="next-steps"></a>Pasos siguientes
[Determinar la estrategia de adopción de administración de identidades híbrido](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)


## <a name="see-also"></a>Vea también
[Información general de las consideraciones de diseño](active-directory-hybrid-identity-design-considerations-overview.md)

