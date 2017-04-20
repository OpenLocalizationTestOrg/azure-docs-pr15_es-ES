<properties
    pageTitle="Azure Active Directory híbrido identidad consideraciones de diseño - determinar los requisitos de sincronización de directorios | Microsoft Azure"
    description="Identificar qué requisitos son necesarios para sincronizar todos los usuarios entre on = local y la nube para la empresa."
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

# <a name="determine-directory-synchronization-requirements"></a>Determinar los requisitos de sincronización de directorios
La sincronización es proporcionar a los usuarios una identidad en la nube que se basa en su identidad local. Si no cuenta sincronizado usan para autenticación o federados, los usuarios aún tendrán que tener una identidad en la nube.  Esta identidad tendrán que se mantienen y se actualiza periódicamente.  Las actualizaciones pueden adoptar muchas formas, de título cambia a los cambios de contraseña.  

Comience por evaluar los requisitos de usuario y la solución del identidad de local de las organizaciones. Esta evaluación es importante para definir los requisitos técnicos de cómo se crean y mantienen en la nube identidades de usuario.  Para la mayoría de organizaciones, Active Directory es local y el directorio local que los usuarios se sincronizarán de, pero en algunos casos no será el caso.  

Asegúrese de que responda a las preguntas siguientes:


- ¿Tiene un bosque de AD, varios o ninguno?
 - ¿Cuántos directorios de Azure AD se sincroniza a?
 
    1. ¿Está usando filtrado?
    2. ¿Tiene varios servidores de Azure AD Connect planificados?
  
- ¿Tiene actualmente una sincronización herramienta local?
  - ¿Si es así, no los usuarios si los usuarios tienen una directorio virtual/integración de identidades?
- ¿Tiene cualquier otro directorio local que desea sincronizar (por ejemplo, directorio LDAP, base de datos de recursos humanos, etcetera)?
  - ¿Va a realizar cualquier GALSync?
  - ¿Qué es el estado actual de UPN de su organización? 
  - ¿Tiene un directorio diferente que los usuarios para autenticarse?
  - ¿Su organización utiliza Microsoft Exchange?
    - ¿Van de disponer de una implementación híbrida de exchange?

Ahora que ya tiene una idea sobre los requisitos de sincronización, debe determinar qué herramienta es la correcta para satisfacer estos requisitos.  Microsoft proporciona varias herramientas para realizar la sincronización y la integración de directorios.  Consulte la [tabla de comparación de herramientas de integración de identidad híbrido directorio](active-directory-hybrid-identity-design-considerations-tools-comparison.md) para obtener más información. 
   
Ahora que ya tiene los requisitos de sincronización y la herramienta que realizará para su empresa, debe evaluar las aplicaciones que usan los servicios de directorio. Esta evaluación es importante para definir los requisitos técnicos integrar estas aplicaciones en la nube. Asegúrese de que responda a las preguntas siguientes:

- ¿Se moverán estas aplicaciones en la nube y usar el directorio?
- ¿Hay atributos especiales que necesitan para sincronizarse con la nube, por lo que pueden usar estas aplicaciones correctamente?
- ¿Estas aplicaciones será necesario volver a escribirse sacar partido de nube auth?
- ¿Seguirán estas aplicaciones live local mientras los usuarios acceso a ellos mediante la identidad de la nube?

También debe determinar la sincronización de directorios de requisitos y restricciones de seguridad. Esta evaluación es importante para obtener una lista de los requisitos necesarios para crear y mantener las identidades de usuario en la nube. Asegúrese de que responda a las preguntas siguientes:

- ¿Dónde se encuentran el servidor de sincronización?
- ¿Será la pertenencia a un dominio?
- ¿Se ubicará el servidor en una red restringida detrás de un firewall, por ejemplo, una DMZ?
  - ¿Podrán abrir los puertos del firewall necesarias para admitir la sincronización?
- ¿Tiene un plan de recuperación para el servidor de sincronización?
- ¿Tiene una cuenta con los permisos correctos para todos los bosques que desea sincronizar con?
 - Si su empresa no conoce la respuesta para esta pregunta, revise la sección "Permisos para la sincronización de contraseña" en el artículo de [instalar el servicio de sincronización de Azure Active Directory](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) y determinar si ya tiene una cuenta con estos permisos o si tiene que crear uno.
- ¿Si tiene sincronización bosque múltiple es capaz de obtener acceso a cada bosque el servidor de sincronización?
 
>[AZURE.NOTE]
Asegúrese de tomar notas de cada respuesta y entender la razón para la respuesta. [Requisitos de respuesta a incidencias de determinar](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) irán sobre las opciones disponibles. Al tener respondidas estas preguntas que seleccione la opción que mejor se adapte a su empresa necesita.

## <a name="next-steps"></a>Pasos siguientes
[Determinar los requisitos de autenticación multifactor](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Vea también
[Información general de las consideraciones de diseño](active-directory-hybrid-identity-design-considerations-overview.md)
