<properties
    pageTitle="Sincronización de Azure AD Connect: cómo hacer un cambio en la configuración predeterminada | Microsoft Azure"
    description="Describe cómo hacer un cambio en la configuración de sincronización de Azure AD Connect."
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
    ms.date="08/31/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-how-to-make-a-change-to-the-default-configuration"></a>Sincronización de Azure AD Connect: cómo hacer un cambio en la configuración predeterminada
El propósito de este tema es que le guíe durante el proceso realizar cambios en la configuración predeterminada de la sincronización de Azure AD Connect. Proporciona los pasos para algunos escenarios comunes. Con esta información, debe realizar algunos cambios simples en su propia configuración en función de sus propias reglas de empresa.

## <a name="synchronization-rules-editor"></a>Editor de reglas de sincronización
El Editor de reglas de sincronización se usa para ver y cambiar la configuración predeterminada. Puede encontrarlo en el menú Inicio, en el grupo de **Azure AD Connect** .  
![Menú Inicio con el Editor de reglas de sincronización](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

Al abrirlo, consulte las reglas de fuera de la caja de forma predeterminada.

![Editor de reglas de sincronización](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navegar en el editor
Las listas desplegables en la parte superior del editor de permiten encontrar rápidamente una regla en particular. Por ejemplo, si desea ver las reglas donde el atributo proxyAddresses se incluye, podría cambiar listas desplegables a la siguiente:  
![Filtrado de SRE](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png)  
Para restablecer el filtrado y cargar una nueva configuración, presione **F5** en el teclado.

En la parte superior derecha, hay un botón **Agregar nueva regla**. Este botón se usa para crear su propia regla personalizada.

En la parte inferior, tiene botones para actuar en una regla de sincronización seleccionado. **Editar** y **Eliminar** hacen lo esperado. **Exportar** genera un script de PowerShell para volver a crear la regla de sincronización. Este procedimiento le permite mover una regla de sincronización de un servidor a otro.

## <a name="create-your-first-custom-rule"></a>Crear la primera regla personalizada
El cambio más comunes es los cambios a los flujos de atributo. Los datos en el directorio de origen no sea como en Azure AD. En el ejemplo de esta sección, desea asegurarse de que el nombre de un usuario esté siempre en **mayúsculas o minúsculas**.

### <a name="disable-the-scheduler"></a>Deshabilitar el programador
El [Programador](active-directory-aadconnectsync-feature-scheduler.md) ejecuta cada 30 minutos de forma predeterminada. Desea asegurarse de que no se inicia mientras se realizan cambios y solucionar problemas de las reglas nuevas. Para deshabilitar temporalmente el programador, iniciar PowerShell y ejecutar`Set-ADSyncScheduler -SyncCycleEnabled $false`

![Deshabilitar el programador](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Crear la regla

1. Haga clic en **Agregar nueva regla**.
2. En la página **Descripción** , escriba lo siguiente:  
![Regla de filtrado de entrada](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)  
    - Nombre: Escriba un nombre descriptivo de la regla.
    - Descripción: Algunas aclaraciones para que otra persona puede comprender cuál es la regla para.
    - Conectado system: el sistema almacenado en el objeto. En este caso, seleccionamos el conector de Active Directory.
    - Tipo de objeto de sistema/Metaverse conectado: Seleccione **usuario** y **persona** respectivamente.
    - Tipo de vínculo: Cambie este valor a **unirse**.
    - Prioridad: Proporcionar un valor único en el sistema. Un valor numérico menor indica una prioridad más alta.
    - Etiqueta: Deje en blanco. Solo el cuadro de reglas de Microsoft deberían tener este cuadro rellena con un valor.
3. En la página de **filtro de evaluación** , escriba **givenName ISNOTNULL**.  
![Regla de filtro de ámbito de entrada](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png)  
En esta sección se usa para definir qué objetos que debe aplicar la regla a. Si deja en blanco, la regla se aplicará a todos los objetos de usuario. Pero incluiría salas de conferencias, cuentas de servicio y otros objetos de usuario no personas.
4. En **unirse a las reglas**, deje vacío.
5. En la página de **las transformaciones** , cambie la tasa a la **expresión**. Seleccione el atributo de destino **givenName**y a continuación, en fuente, escriba `PCase([givenName])`.
![Regla de entrada transformaciones](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png)  
El motor de sincronización distingue mayúsculas de minúsculas en el nombre de la función y el nombre del atributo. Si escribe algún error, se muestra una advertencia cuando se agrega la regla. El editor le permite guardar y continuar, por lo que tendrá que volver a abrir la regla y corrija la regla.
6. Haga clic en **Agregar** para guardar la regla.

La nueva regla personalizada debe estar visible con otras reglas de sincronización en el sistema.

### <a name="verify-the-change"></a>Compruebe el cambio
Con este cambio nuevo, desea asegurarse de que funciona como se esperaba y no está produciendo los errores. Dependiendo del número de objetos que tiene, hay dos maneras de realizar este paso.

1. Ejecutar una sincronización completa en todos los objetos
2. Ejecutar una vista previa y una sincronización completa en un único objeto

Iniciar el **Servicio de sincronización** en el menú Inicio. Los pasos descritos en esta sección están todos en esta herramienta.

1. **Sincronización completa de todos los objetos**  
Seleccione los **conectores** en la parte superior. Identificar el conector que realiza un cambio en la sección anterior, en este caso, los servicios de dominio de Active Directory, y selecciónelo. Seleccione **Ejecutar** en acciones y seleccione **Sincronización completa** y en **Aceptar**.
![Sincronización completa](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)  
Los objetos ahora se actualizan en el metaverso. Desea ver el objeto en el metaverso.

2. **Vista previa y una sincronización completa en un único objeto**  
Seleccione los **conectores** en la parte superior. Identificar el conector que realiza un cambio en la sección anterior, en este caso, los servicios de dominio de Active Directory, y selecciónelo. Seleccione **el espacio del conector de búsqueda**. Use el ámbito para buscar un objeto que desea usar para probar el cambio. Seleccione el objeto y haga clic en **vista previa**. En la pantalla nuevo, seleccione **Vista previa de confirmación**.
![Confirmar la vista previa](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png)  
Ahora, el cambio se compromete a metaverse.

**Ver el objeto en el metaverso**  
Desea seleccionar algunos objetos de ejemplo para asegurarse de que el valor se espera y que se aplica la regla. Seleccione **Búsqueda en Metaverse** desde la parte superior. Agregue cualquier filtro que se debe buscar los objetos pertinentes. Resultado de la búsqueda, abra un objeto. Consulte los valores de atributo y compruebe también en la columna de **Reglas de sincronización de** la regla aplicada como se esperaba.  
![Búsqueda en Metaverse](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)  
### <a name="enable-the-scheduler"></a>Activar el programador
Si todo es según lo esperado, puede habilitar al programador de nuevo. De PowerShell, ejecute `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## <a name="other-common-attribute-flow-changes"></a>Otros cambios de flujo de atributos comunes
La sección anterior describe cómo realizar cambios en un flujo de atributos. En esta sección se proporcionan algunos ejemplos adicionales. Los pasos para crear la regla de sincronización es abreviados, pero puede encontrar los pasos completos en la sección anterior.

### <a name="use-another-attribute-than-the-default"></a>Utilizar otro atributo de la predeterminada
En Fabrikam, hay un bosque donde se usa el alfabeto local para nombre, apellido y nombre para mostrar. La representación de caracteres latinos de estos atributos puede encontrarse en los atributos de extensión. Al crear la lista global de direcciones en Azure AD y Office 365, la organización quiere estos atributos que se utilizan en su lugar.

Con una configuración predeterminada, un objeto del bosque local es similar a esta:  
![Flujo de atributos 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

Para crear una regla con otros flujos de atributo, haga lo siguiente:

- Inicie el **Editor de regla de sincronización** en el menú Inicio.
- Con **entrante** seleccionado a la izquierda, haga clic en el botón **Agregar nueva regla**.
- Asignar un nombre y una descripción a la regla. Seleccione el Active Directory local y los tipos de objeto pertinente.  En **Tipo de vínculo**, seleccione **unirse**. Para prioridad, seleccione un número que no se utiliza por otra regla. Las reglas de cuadro de inicio con 100, por lo que el valor 50 puede usarse en este ejemplo.
![Flujo de atributos 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
- Deje vacío ámbito (es decir, debe aplicarse a todos los objetos de usuario del bosque).
- Deje en blanco las reglas de combinación (es decir, deje que la regla de cuadro de controlar las combinaciones).
- En las transformaciones, cree los flujos siguientes:  
![Flujo de atributos 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
- Haga clic en **Agregar** para guardar la regla.
- Vaya a **Administrador de servicios de sincronización**. En **conectores**, seleccione el conector donde se agregó la regla. Seleccione **Ejecutar**y **sincronización completa**. Una sincronización completa vuelve a calcular todos los objetos mediante las reglas actuales.

Esto es el resultado para el mismo objeto con esta regla personalizada:  
![Flujo de atributos 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Longitud de atributos
Atributos de cadena predeterminada son establecido indizar y la longitud máxima es 448 caracteres. Si está trabajando con los atributos de cadena que pueden contener más, asegúrese de incluir las siguientes acciones en el flujo de atributos:  
`attributeName` <- `Left([attributeName],448)`

### <a name="changing-the-userprincipalsuffix"></a>Cambiar el userPrincipalSuffix
El atributo userPrincipalName de Active Directory no siempre se conoce los usuarios y no pueden servir como el ID de inicio de sesión. La Azure AD Connect Asistente de instalación de sincronización permite elegir un atributo diferente, por ejemplo correo. Pero en algunos casos se debe calcular el atributo. Por ejemplo, la empresa Contoso tiene dos directorios de Azure AD, uno para producción y otro para realizar pruebas. Desean que los usuarios de su inquilino de prueba para usar otro sufijo en el ID de inicio de sesión.  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

En esta expresión, tomar todo el contenido a la izquierda de la primera @-sign (Word) y concatenar con una cadena fija.

### <a name="convert-a-multi-value-to-a-single-value"></a>Convertir valor múltiple en un valor único
Algunos atributos de Active Directory son multivalor en el esquema aunque su aspecto único con valores en equipos y usuarios de Active Directory. Un ejemplo es el atributo de descripción.  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

En esta expresión en caso de que el atributo tiene un valor, se tomar el primer elemento () en el atributo, quitar espacios (Trim) iniciales y finales y, a continuación, conservar los caracteres primero 448 (izquierda) en la cadena.

### <a name="do-not-flow-an-attribute"></a>¿Fluyen un atributo
Para información sobre el escenario de esta sección, vea [controlar el proceso de flujo de atributo](active-directory-aadconnectsync-understanding-declarative-provisioning.md#control-the-attribute-flow-process).

Hay dos formas de flujo no un atributo. La primera está disponible en el Asistente para instalación y le permite quitar [atributos seleccionados](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Esta opción funciona si nunca ha sincronizado el atributo antes. Sin embargo, si ha iniciado sincronizar este atributo y eliminarlo más tarde con esta característica, el motor de sincronización se detiene administrar el atributo y los valores existentes quedan en Azure AD.

Si desea quitar el valor de un atributo y asegúrese de que no fluye en el futuro, tiene que crear una regla personalizada en su lugar.

En Fabrikam, que hemos obtenido que algunos de los atributos que se sincronizan con la nube no deben estar ahí. Queremos para asegurarse de que estos atributos se quitan de Azure AD.  
![Atributos de extensión incorrecta](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

- Crear una nueva regla de sincronización entrante y rellenar la descripción ![descripciones](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
- Crear flujos de atributos del tipo de **expresión** y con el origen de **AuthoritativeNull**. El literal **AuthoritativeNull** indica que el valor debe ser vacío en la MV, incluso si se trata de una regla de sincronización de prioridad inferior rellenar el valor.
![Transformación de atributos de extensión](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
- Guardar la regla de sincronización. Iniciar el **Servicio de sincronización**, busque el conector, seleccione **Ejecutar**y **Sincronización completa**. Este paso vuelve a calcular todos los flujos de atributo.
- Compruebe que los cambios deseados a exportarse de búsqueda en el espacio del conector.
![Eliminar preconfigurada](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre el modelo de configuración de [Aprovisionamiento descriptiva de descripción](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
- Más información sobre el lenguaje de expresiones en [Expresiones de aprovisionamiento descriptiva en Descripción](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).

**Temas de información general**

- [Sincronización de Azure AD Connect: entender y personalizar la sincronización](active-directory-aadconnectsync-whatis.md)
- [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
