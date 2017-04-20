<properties
    pageTitle="Aplicación basada en el atributo de aprovisionamiento con filtros de ámbito | Microsoft Azure"
    description="Aprenda a usar filtros de ámbito para evitar que los objetos en las aplicaciones que son compatibles con aprovisionamiento automatizado usuario desde que se aprovisiona realmente si un objeto no satisface sus necesidades empresariales."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi"/>


# <a name="attribute-based-app-provisioning-with-scoping-filters"></a>Aplicación basada en el atributo de aprovisionamiento con filtros de ámbito

El objetivo de esta sección es explicar cómo usar filtros de ámbito para definir reglas de atributo que puede determinar qué usuarios se aprovisionará con la aplicación.





## <a name="clauses-and-scope-groups"></a>Cláusulas y grupos de ámbito


![Especificar el ámbito de filtro][1] 




Filtros de ámbito están definidos por uno o varios **grupos de ámbito**, cada una de las cuales mantenga una o más **cláusulas**. Para ver las cláusulas para un grupo de ámbito particular, expanda haciendo clic en la flecha situada a la izquierda del nombre del grupo.

Una **cláusula** determina qué usuarios pueden pasar a través del filtro de ámbito evaluando los atributos de cada usuario. Por ejemplo, es posible que tenga una cláusula que requiere que el atributo de 'estado' de un usuario sea igual a Nueva York, lo que significa que solo los usuarios de Nueva York se aprovisionará en la aplicación.

![Especificar el ámbito de nombre de grupo][2] 



Cada **grupo ámbito** comienza con obligatorio de una **cláusula**, como se muestra en la captura de pantalla anterior. Esta cláusula indica que el usuario primero debe tener asignado a la aplicación antes de que se evalúa por los filtros de ámbito. Esta cláusula no se pueden eliminar ni modificar.

Puede agregar cláusulas nuevas o nuevos grupos de ámbito pulsando el botón correspondiente. Puede dar un nombre de cada grupo ámbito editando su propiedad del **Nombre del grupo de ámbito** .





## <a name="how-scoping-filters-are-evaluated"></a>Cómo se evalúan los filtros de ámbito

Durante el aprovisionamiento, se prueba cada usuario con los filtros de ámbito asignado para determinar si ese usuario merece acceso a la aplicación. Puede considerar que cada cláusula como una prueba que se debe pasar para el usuario evitar la introducción filtran. 

Si tiene varios grupos de ámbito definido, cada usuario debe pasar al menos uno de ellos para tener acceso a la aplicación. Dentro de cada grupo ámbito, sin embargo, el usuario debe pasar cada cláusula única para pasar a ese grupo ámbito específico. 

En otras palabras, puede considerar que los grupos de ámbito como o juntos y puede considerar que las cláusulas dentro de ellos como y unir. Por ejemplo, considere el ámbito de filtro a continuación:


![Especificar el ámbito de nombre de grupo][2]  


Función este filtro ámbito, los usuarios deben cumplir los criterios siguientes, para aprovisionar:

1. Se debe asignar a la aplicación.

2. Debe trabajan en el departamento de ingeniería

3. Deben ser trabajo en San Francisco o Canadá.


##<a name="related-articles"></a>Artículos relacionados

- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
- [Automatizar usuario de aprovisionamiento y baja para las aplicaciones de SaaS](active-directory-saas-app-provisioning.md)
- [Personalizar las asignaciones de atributo para el aprovisionamiento de usuario](active-directory-saas-customizing-attribute-mappings.md)
- [Escritura de expresiones para asignaciones de atributos](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Notificaciones de aprovisionamiento de la cuenta](active-directory-saas-account-provisioning-notifications.md)
- [Con SCIM para habilitar el aprovisionamiento automático de usuarios y grupos de Azure Active Directory para las aplicaciones](active-directory-scim-provisioning.md)
- [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-scoping-filters/ic782811.png
[2]: ./media/active-directory-saas-scoping-filters/ic782812.png
[3]: ./active-directory-saas-scoping-filters/ic782813.png
