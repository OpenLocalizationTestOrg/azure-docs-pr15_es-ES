
<properties
    pageTitle="Solución de problemas de pertenencia dinámica para grupos | Microsoft Azure"
    description="Sugerencias para la solución dinámica pertenencia a grupos de Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""
    />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="curtand"/>


# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Solución de problemas dinámicos pertenencias a grupos

**He configurado una regla en un grupo pero no pertenencias se actualizan en el grupo**<br/>Compruebe que la opción **Habilitar había delegado la administración de grupo** se establece en **Sí** en la ficha **Configurar** . Verá esta opción únicamente si ha iniciado sesión como un usuario que tiene asignada una licencia de Azure Active Directory Premium. Compruebe los valores para los atributos de usuario de la regla: ¿hay usuarios que cumplen la regla?

**He configurado una regla, pero ahora se quitan los miembros existentes de la regla**<br/>Este es el comportamiento esperado. Los miembros existentes del grupo se quitan cuando se habilita o cambiar una regla. Los usuarios devueltos de la evaluación de la regla se agregan como miembros al grupo.     

**No veo cambie la pertenencia al instante al agregar o cambiar una regla, ¿por qué no?**<br/>Evaluación de pertenencia dedicado se realiza periódicamente en un proceso de fondo asincrónica. ¿Cuánto tiempo tarda viene determinada por el número de usuarios en el directorio y el tamaño del grupo creado como resultado de la regla. Normalmente, directorios con pequeños números de los usuarios verán los cambios de la pertenencia a un grupo en menos de unos minutos. Directorios con un gran número de usuarios pueden tardar 30 minutos o más para rellenar.

Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Administrar el acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)
* [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
* [¿Qué es Azure Active Directory?](active-directory-whatis.md)
* [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
