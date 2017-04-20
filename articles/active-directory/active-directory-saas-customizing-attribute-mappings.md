<properties
    pageTitle="Personalizar las asignaciones de atributo | Microsoft Azure"
    description="Averigüe qué asignaciones de atributos para las aplicaciones SaaS de Azure Active Directory cómo se puede modificar para satisfacer sus necesidades empresariales."
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


# <a name="customizing-attribute-mappings"></a>Personalizar las asignaciones de atributo


Microsoft Azure AD proporciona soporte para el aprovisionamiento de usuario para las aplicaciones de terceros SaaS como Salesforce, Google Apps y otros. Si tiene el aprovisionamiento de usuario de un tercero aplicación SaaS habilitado, el Portal de administración de Azure controla los valores de atributo en forma de una configuración que se denomina "atributo asignación".

Hay un conjunto de asignaciones de atributo entre objetos de usuario de Azure AD y objetos de usuario de cada aplicación SaaS preconfigurado. Algunas aplicaciones administración otros tipos de objetos, como contactos o grupos. <br> 
Puede personalizar las asignaciones de atributo predeterminadas según sus necesidades empresariales. Esto significa, puede cambiar o eliminar asignaciones de atributo existentes o crear nuevas asignaciones de atributo.

En el portal de Azure AD, puede obtener acceso a esta característica haciendo clic en atributos en la barra de herramientas de una aplicación de SaaS.

> [AZURE.NOTE] El vínculo de **atributos** solo está disponible si tiene el aprovisionamiento de usuarios habilitados para una aplicación de SaaS. 


![Salesforce][1] 


Al hacer clic en atributos en la barra de herramientas, la lista de asignaciones actuales que están configuradas para una aplicación de SaaS.

La siguiente captura de pantalla muestra un ejemplo de esto:



![Salesforce][2]  


En el ejemplo anterior, puede ver que el atributo de **nombre** de un objeto de Salesforce se rellena con el valor de **givenName** de vinculado objeto de Azure AD.

Si desea personalizar las asignaciones de atributo o si desea revertir Personalizar configuración volver a la configuración predeterminada, puede hacerlo haciendo clic en el botón en la barra de herramientas en la parte inferior de una aplicación.


![Salesforce][3]  


Hay asignaciones de atributos que requieren una aplicación de SaaS funcione correctamente. En la tabla de atributos, las asignaciones de atributo relacionado con **Sí** como valor del atributo **obligatorio** . Si se requiere una asignación de atributos, no puede eliminarlo. En este caso, la característica **Eliminar** no está disponible.

Para modificar una asignación de atributo existente, seleccione la asignación y, a continuación, haga clic en **Editar**. Esto incorpora una página de diálogo que le permite modificar la asignación de atributo seleccionado.


![Editar la asignación de atributos][4]  



## <a name="understanding-attribute-mapping-types"></a>Descripción de los tipos de asignación de atributo


Con las asignaciones de atributo, puede controlar cómo se rellenan los atributos de una tercera aplicación de SaaS de terceros. Hay cuatro tipos de asignación diferentes compatibles:

- **Directo** : el atributo de destino se rellena con el valor de un atributo del objeto vinculado en Azure AD.


- **Constante** : el atributo de destino se rellena con una cadena específica que se ha especificado.


- **Expresión** - el atributo de destino se rellena basándose en el resultado de una expresión de secuencia de comandos. Para obtener más detalles, vea [Expresiones de escritura para asignaciones de atributos de Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md).


- **Ninguno** : el atributo de destino es izquierda sin modificar. Sin embargo, si el atributo de destino está vacío nunca, se rellena con el valor predeterminado que se especifique.



Además de estos cuatro tipos de asignación de atributo básica, asignaciones de atributos personalizados admiten el concepto de una asignación de valor **predeterminado** . La asignación de valor predeterminado garantiza que un atributo de destino se rellena con un valor si no hay ninguna un valor en Azure AD ni en el objeto de destino.

Microsoft Azure AD proporciona una implementación muy eficaz de un proceso de sincronización. En un entorno inicializado, solo los objetos que requieren actualizaciones se procesan durante un ciclo de sincronización. Actualizar asignaciones de atributo tiene un gran impacto en el rendimiento de un ciclo de sincronización. Esto es porque una actualización de la configuración de la asignación de atributo requiere que se vuelva a evaluar todos los objetos administrados. Por este motivo, es una práctica recomendada para mantener el número de cambios consecutivos a las asignaciones de atributo como mínimo.


##<a name="related-articles"></a>Artículos relacionados

- [Índice de artículos de administración de aplicaciones de Azure Active Directory](active-directory-apps-index.md)
- [Automatizar usuario aprovisionamiento y baja a aplicaciones de SaaS](active-directory-saas-app-provisioning.md)
- [Escritura de expresiones para asignaciones de atributos](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Especificar el ámbito de filtros para el aprovisionamiento de usuario](active-directory-saas-scoping-filters.md)
- [Con SCIM para habilitar el aprovisionamiento automático de usuarios y grupos de Azure Active Directory para las aplicaciones](active-directory-scim-provisioning.md)
- [Notificaciones de aprovisionamiento de la cuenta](active-directory-saas-account-provisioning-notifications.md)
- [Lista de tutoriales sobre cómo integrar aplicaciones de SaaS](active-directory-saas-tutorial-list.md)


<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png
