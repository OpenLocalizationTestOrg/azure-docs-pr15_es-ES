<properties
    pageTitle="Sincronización de Azure AD Connect: configurar el filtrado | Microsoft Azure"
    description="Se explica cómo configurar el filtrado de sincronización de Azure AD Connect."
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
    ms.date="09/13/2016"
    ms.author="andkjell;markvi"/>


# <a name="azure-ad-connect-sync-configure-filtering"></a>Sincronización de Azure AD Connect: configurar el filtrado
Con el filtrado, puede controlar los objetos que deben aparecer en Azure AD desde el directorio local. La configuración predeterminada tiene todos los objetos en todos los dominios en los bosques configurados. En general, esta es la configuración recomendada. Los usuarios finales con cargas de trabajo de Office 365, como Exchange Online y Skype empresarial, beneficiarse de una lista Global de direcciones completa para que puedan enviar correo electrónico y llamar a todos los usuarios. Con la configuración predeterminada, tendrán la misma experiencia que lo harían con una implementación local de Exchange o Lync.

En algunos casos, es necesario realizar algunos cambios a la configuración predeterminada. Estos son algunos ejemplos:

- Plan usar la [topología de múltiples Azure AD-directorio](active-directory-aadconnect-topologies.md#each-object-only-once-in-an-azure-ad-directory). A continuación, deberá aplicar un filtro para controlar qué objeto debe sincronizarse con un determinado directorio de Azure AD.
- Ejecutar a un programa piloto para Azure u Office 365 y desea que solo un subconjunto de los usuarios en Azure AD. En la fase piloto pequeña, no es importante tener una lista Global de direcciones completa para mostrar la funcionalidad.
- Tiene varias cuentas de servicio y otras cuentas no personales que no desea en Azure AD.
- Por motivos de cumplimiento no elimine cualquier usuario cuentas locales. Deshabilitar solo. Pero en Azure AD desea sólo cuentas activas está presente.

Este artículo explica cómo configurar los diferentes métodos de filtrado.

> [AZURE.IMPORTANT]Microsoft no admite la operación de la sincronización de Azure AD Connect fuera de esas acciones formal documentado o modificación. Cualquiera de estas acciones puede traducirse en un estado incoherente o no compatible de sincronización de Azure AD Connect y como resultado, Microsoft no ofrece soporte técnico para estas implementaciones.

## <a name="basics-and-important-notes"></a>Conceptos básicos y notas importantes
En la sincronización de Azure AD Connect, puede habilitar el filtrado en cualquier momento. Si empieza con una configuración predeterminada de sincronización de directorios y, a continuación, configurar el filtrado, los objetos que se filtran ya no se sincronizarán con Azure AD. Como resultado de este cambio, se eliminan los objetos en Azure AD que se hayan sincronizado anteriormente, pero se han filtrado, a continuación, en Azure AD.

Antes de empezar a realizar cambios para filtrado, asegúrese de que se [deshabilita la tarea programada](#disable-scheduled-task) para no se exportan accidentalmente los cambios que todavía no se ha comprobado para ser correcta.

Dado que el filtrado puede quitar varios objetos al mismo tiempo, desea asegurarse de que los nuevos filtros son correctos antes de empezar a exportar los cambios a Azure AD. Después de completar los pasos de configuración, se recomienda que siga los [pasos de comprobación](#apply-and-verify-changes) antes de exportar y realizar cambios en Azure AD.

Para impedir que se eliminen muchos objetos por accidente, la característica para [evitar eliminaciones accidentales](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) predeterminada está activada. Si elimina muchos objetos debido a filtrado (500 de forma predeterminada), debe seguir los pasos de este artículo para permitir que las eliminaciones llegue a Azure AD.

Si usa una versión antes de noviembre de 2015 ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)), realice un cambio en la configuración de filtro y usar la sincronización de contraseñas, debe desencadenar una sincronización completa de todas las contraseñas después de completar la configuración. Para conocer los pasos sobre cómo desencadenar una sincronización completa de contraseña vea [desencadenar una sincronización completa de todas las contraseñas](active-directory-aadconnectsync-implement-password-synchronization.md#trigger-a-full-sync-of-all-passwords). Si se encuentra en 1.0.9125 o posterior, a continuación, la acción regular **sincronización completa** también calcula si se sincronizarán contraseñas y ya no es necesario este paso adicional.

Si los objetos de **usuario** se elimina accidentalmente en Azure AD debido a un error filtrado, puede volver a los objetos de usuario en Azure AD eliminando las configuraciones de filtrado y, a continuación, sincronizar los directorios de nuevo. Esta acción restaura los usuarios de la Papelera de reciclaje en Azure AD. Sin embargo, no se puede recuperar otros tipos de objeto. Por ejemplo, si se elimina accidentalmente un grupo de seguridad y que se ha usado para ACL no pueden recuperar un recurso, el grupo y su ACL.

Azure AD Connect solo elimina objetos que se considera una vez en el ámbito. Si hay objetos en Azure AD creadas por otro motor de sincronización y estos objetos no están en el ámbito, agregar filtrado no se quitan. Por ejemplo, si iniciar con un servidor de sincronización de directorios y que creó una copia completa de todo el directorio de Azure AD e instalar a un servidor de sincronización de Azure AD Connect nuevo en paralelo con el filtrado habilitado desde el principio, no quita los objetos adicionales creados por la sincronización de directorios.

La configuración filtrada se conservará al instalar o actualizar a una versión más reciente de Azure AD Connect. Siempre es recomendable comprobar que la configuración no se accidentalmente cambió después de una actualización a una versión más reciente antes de ejecutar la primera sincronización ciclo.

Si tiene más de un bosque, las configuraciones filtradas descritas en este tema deben aplicarse a cada bosque (suponiendo que desee de la misma configuración para todos ellos).

### <a name="disable-scheduled-task"></a>Deshabilitar la tarea programada
Para deshabilitar al programador integrado que activa un ciclo de sincronización de cada 30 minutos, siga estos pasos:

1. Vaya a un PowerShell símbolo.
2. Ejecutar `Set-ADSyncScheduler -SyncCycleEnabled $False` para deshabilitar el programador.
3. Realice los cambios, tal como se describe en este tema.
4. Ejecutar `Set-ADSyncScheduler -SyncCycleEnabled $True` volver a habilitar el programador.

**Si usa una versión de Azure AD Connect antes de 1.1.105.0**  
Para deshabilitar la tarea programada que activa un ciclo de sincronización de cada tres horas, siga estos pasos:

1. Iniciar **Programador de tareas** desde el menú Inicio.
2. Directamente en la **Biblioteca del programador de tareas**, busque la tarea llamada **Programador de Azure AD Sync**, con el botón secundario y seleccione **Deshabilitar**.  
![Programador de tareas](./media/active-directory-aadconnectsync-configure-filtering/taskscheduler.png)  
3. Ahora puede realizar cambios de configuración y ejecutar el motor de sincronización manualmente desde la consola del **Administrador de servicios de sincronización** .

Después de completar todos los cambios filtrados, no olvide vienen atrás y **Habilitar** la tarea de nuevo.

## <a name="filtering-options"></a>Opciones de filtrado
Los siguientes tipos de configuración de filtrado se pueden aplicar a la herramienta de sincronización de directorio:

- [**En función de grupo**](active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups): filtrado basado en un solo grupo sólo se puede configurar en la instalación inicial mediante el Asistente para instalación. No está cubierto más en este tema.

- [**Dominio**](#domain-based-filtering): esta opción permite seleccionar los dominios que se sincronizan a Azure AD. También permite agregar y quitar dominios de la configuración del motor de sincronización si realiza cambios en la infraestructura local después de instalar sincronización de Azure AD Connect.

- [**Basado en la unidad organizativa**](#organizational-unitbased-filtering): esta opción de filtrado le permite seleccionar las unidades organizativas sincronizan a Azure AD. Esta opción es para todos los tipos de objeto en OU seleccionada.

- [**Basada en el atributo**](#attribute-based-filtering): esta opción permite filtrar objetos según los valores de atributo en los objetos. También puede tener distintos filtros para distintos tipos de objeto.

Puede usar varias opciones de filtrado al mismo tiempo. Por ejemplo, puede usar el filtrado basado en OU para incluir solo los objetos en una unidad organizativa y a la misma hora basada en atributos filtrado para filtrar aún más los objetos. Al usar varios métodos de filtrado, los filtros utilizan un AND lógico entre los filtros.

## <a name="domain-based-filtering"></a>Filtrado basado en el dominio
Esta sección proporciona los pasos para configurar el filtro de dominio. Si ha agregado o eliminado dominios del bosque después de haber instalado Azure AD Connect, debe actualizar la configuración filtrada.

La mejor manera de cambiar el filtrado basado en dominio está ejecutando la instalación asistente y cambie el [dominio y unidades organizativas filtrado](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). El Asistente para instalación es automatizar todas las tareas documentadas en este tema.

Solo debe seguir estos pasos si por algún motivo no puede ejecutar el Asistente para la instalación.

Configuración filtrado basado en dominio consta de estos pasos:

- [Seleccione los dominios](#select-domains-to-be-synchronized) que se deben incluir en la sincronización.
- Para cada dominio agregado y quitaron, ajuste el [Ejecutar perfiles](#update-run-profiles).
- [Aplicar y comprobar los cambios](#apply-and-verify-changes).

### <a name="select-domains-to-be-synchronized"></a>Seleccione los dominios que sincronizar
**Para establecer el filtro de dominio, siga los pasos siguientes:**

1. Inicie sesión en el servidor que se ejecuta la sincronización de Azure AD Connect con una cuenta que sea miembro del grupo de seguridad **ADSyncAdmins** .
2. Iniciar el **Servicio de sincronización** en el menú Inicio.
3. Seleccione los **conectores** y en la lista de **conectores** , seleccione el conector con el tipo de **Servicios de dominio de Active Directory**. En **acciones**, seleccione **Propiedades**.  
![Propiedades de conector](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Haga clic en **configurar las particiones del directorio**.
5. En la lista **Seleccionar particiones de directorio** , seleccionar y anular la selección de los dominios según sea necesario. Compruebe que solo las particiones que desea sincronizar están seleccionadas.  
![Particiones](./media/active-directory-aadconnectsync-configure-filtering/connectorpartitions.png)  
Si ha cambiado su local infraestructura de AD y dominios agregados o eliminados del bosque, a continuación, haga clic en el botón **Actualizar** para obtener una lista actualizada. Al actualizar, se le pedirá las credenciales. Proporcionar las credenciales con acceso de lectura a Active Directory local. No tiene que el usuario que se rellena previamente en el cuadro de diálogo.  
![Actualización necesaria](./media/active-directory-aadconnectsync-configure-filtering/refreshneeded.png)  
6. Cuando haya terminado, cierre el cuadro de diálogo de **Propiedades** , haga clic en **Aceptar**. Si ha eliminado dominios del bosque, una ventana emergente de mensaje que indica que se ha quitado un dominio y que la configuración se se limpiar.
7. Continuar ajustar la [Ejecutar perfiles](#update-run-profiles).

### <a name="update-run-profiles"></a>Actualizar los perfiles de ejecución
Si ha actualizado el filtro de dominio, debe actualizar los perfiles de ejecución.

1. En la lista de **conectores** , asegúrese de que está seleccionado el conector que modificó en el paso anterior. En **acciones**, seleccione **Configurar perfiles de ejecutar**.  
![Conector ejecutar perfiles](./media/active-directory-aadconnectsync-configure-filtering/connectorrunprofiles1.png)  

Es necesario ajustar los siguientes perfiles:

- Importación completa
- Sincronización completa
- Importación de Delta
- Sincronización de Delta
- Exportar

Para cada uno de los perfiles de cinco, siga estos pasos para cada dominio **agregado** :

1. Seleccione el perfil y haga clic en **Nuevo paso**.
2. En la página **Configurar paso** , en la lista desplegable **tipo** , seleccione el tipo de paso con el mismo nombre que el perfil que está configurando. A continuación, haga clic en **siguiente**.  
![Conector ejecutar perfiles](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep1.png)  
3. En la página **Configuración del conector** , en la lista desplegable **partición** , seleccione el nombre del dominio que haya agregado a su filtro de dominio.  
![Conector ejecutar perfiles](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep2.png)  
4. Para cerrar el cuadro de diálogo **Configurar perfil ejecutar** , haga clic en **Finalizar**.

Para cada uno de los perfiles de cinco, siga estos pasos para cada dominio **quitado** :

1. Seleccione el perfil de ejecución.
2. Si el **valor** del atributo **partición** es un GUID, seleccione el paso ejecutar y haga clic en **Eliminar paso**.  
![Conector ejecutar perfiles](./media/active-directory-aadconnectsync-configure-filtering/runprofilesdeletestep.png)  

El resultado debería que cada dominio que desea sincronizar debe figurar como un paso en cada perfil de ejecución.

Para cerrar el cuadro de diálogo **Configurar perfiles de ejecutar** , haga clic en **Aceptar**.

- Para completar la configuración, [Aplicar y comprobar los cambios](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Filtrado basado en unidad organizativa
La mejor manera de cambiar el filtrado basado en la unidad organizativa está ejecutando la instalación asistente y cambie el [dominio y unidades organizativas filtrado](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). El Asistente para instalación es automatizar todas las tareas documentadas en este tema.

Solo debe seguir estos pasos si por algún motivo no puede ejecutar el Asistente para la instalación.

**Para configurar el filtrado basado en unidad organizativa, realice los pasos siguientes:**

1. Inicie sesión en el servidor que se ejecuta la sincronización de Azure AD Connect con una cuenta que sea miembro del grupo de seguridad **ADSyncAdmins** .
2. Iniciar el **Servicio de sincronización** en el menú Inicio.
3. Seleccione los **conectores** y en la lista de **conectores** , seleccione el conector con el tipo de **Servicios de dominio de Active Directory**. En **acciones**, seleccione **Propiedades**.  
![Propiedades de conector](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Haga clic en **Configurar particiones de directorio**, seleccione el dominio que desea configurar y, a continuación, haga clic en **contenedores**.
5. Cuando se le solicite, proporcione credenciales con acceso de lectura a su Active Directory local. No tiene que el usuario que se rellena previamente en el cuadro de diálogo.
6. En el cuadro de diálogo **Seleccionar contenedores** , desactive las unidades organizativas que no desea sincronizar con el directorio de la nube y, a continuación, haga clic en **Aceptar**.  
![UNIDAD ORGANIZATIVA](./media/active-directory-aadconnectsync-configure-filtering/ou.png)  
  - El contenedor **equipos** debe estar seleccionado para que los equipos de Windows 10 para que se sincronice correctamente a Azure AD. Si su dominio unido equipos se encuentran en otras unidades organizativas, asegúrese de que los que están seleccionados.
  - El contenedor **ForeignSecurityPrincipals** debe estar seleccionado si tiene múltiples bosques de confianza. Este contenedor permite la pertenencia a grupos de seguridad entre bosques se deben resolver.
  - Debe estar seleccionada la OU **RegisteredDevices** si ha habilitado la característica de reescritura de dispositivo. Si usa otra característica de reescritura, como reescritura de grupo, asegúrese de que se seleccionan estas ubicaciones.
  - Seleccione cualquier unidad organizativa donde se encuentran los usuarios, InetOrgPerson, grupos, contactos y equipos. En la imagen, todos ellos se encuentran en la OU ManagedObjects.
7. Cuando haya terminado, cierre el cuadro de diálogo de **Propiedades** , haga clic en **Aceptar**.
8. Para completar la configuración, [Aplicar y comprobar los cambios](#apply-and-verify-changes).

## <a name="attribute-based-filtering"></a>Filtrado basado en el atributo
Asegúrese de que se encuentran en noviembre de 2015 ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)) o posterior generar para que realizar estos pasos para que funcione.

Filtrado en función del atributo es la manera más flexible para filtrar objetos. Puede usar la potencia de [aprovisionamiento descriptiva](active-directory-aadconnectsync-understanding-declarative-provisioning.md) para controlar casi cualquier aspecto de cuándo se debe sincronizar un objeto a Azure AD.

Filtrado se puede aplicar tanto en la [entrada](#inbound-filtering) de Active Directory para el metaverso y [salida](#outbound-filtering) de metaverse a Azure AD. Se recomienda para aplicar los filtros en entrante, ya que es más fácil de mantener. El filtrado de salida sólo se debe utilizar si es necesario para unirse a objetos de más de un bosque antes de poder realizar la evaluación.

### <a name="inbound-filtering"></a>Filtrado de mensajes entrantes
Filtrado basado en entrante usa la configuración predeterminada donde objetos van a Azure AD deben tener el cloudFiltered de atributo de metaverse no ha establecido un valor para sincronizar. Si el valor de este atributo se establece en **True**, el objeto no está sincronizado. No se debe establecer en **False** por diseño. Para asegurarse de que otras reglas tienen la capacidad para aportar un valor, este atributo se supone que solo los valores **True** o **nulo** (ausente).

En el filtrado de entrada, se utiliza la potencia de **ámbito** para determinar qué objetos deben o no se sincronizarán. Esto es el lugar donde ajustes para satisfacer los requisitos de su organización. El módulo de ámbito tiene **grupo** y **cláusula** para determinar si debe ser una regla de sincronización en el ámbito. Un **grupo** contiene uno o varios **cláusula**. Hay un AND lógico entre varias cláusulas y lógico o entre varios grupos.

Veamos un ejemplo:  
![Ámbito](./media/active-directory-aadconnectsync-configure-filtering/scope.png) Esto se debe leer como **(Departamento = TI) o (departamento = ventas y c = US)**.

En los ejemplos y los pasos siguientes, utilice el objeto de usuario como ejemplo, pero puede usar para todos los tipos de objeto.

En los ejemplos siguientes, el valor de prioridad empezar con 500. Este valor garantiza que estas reglas se evalúan después de las reglas de fuera de la caja (prioridad más baja, mayor valor numérico).

#### <a name="negative-filtering-do-not-sync-these"></a>Negativo filtrado, "no se sincronizan estos"
En el ejemplo siguiente, se filtran (no se sincronizan) todos los usuarios donde **extensionAttribute15** tienen el valor **NoSync**.

1. Inicie sesión en el servidor que se ejecuta la sincronización de Azure AD Connect con una cuenta que sea miembro del grupo de seguridad **ADSyncAdmins** .
2. Inicie el **Editor de reglas de sincronización** en el menú Inicio.
3. Asegúrese de que está seleccionada la **entrada** y haga clic en **Agregar nueva regla**.
4. Dar a la regla un nombre descriptivo, como por ejemplo "*en de AD: usuario DoNotSyncFilter*". Seleccione bosque correcto, el **usuario** como el **tipo de objeto de CS**y **persona** como el **tipo de objeto de MV**. Como el **Tipo de vínculo**, seleccione **unirse** en prioridad, escriba un valor no utilizado actualmente por otra regla de sincronización (por ejemplo 500) y, a continuación, haga clic en **siguiente**.  
![1 Descripción de entrada](./media/active-directory-aadconnectsync-configure-filtering/inbound1.png)  
5. En **filtro de evaluación**, haga clic en **Agregar grupo**, haga clic en **Agregar cláusula**y, en el atributo, seleccione **ExtensionAttribute15**. Asegúrese de que el operador se establece en **igual** y escriba el valor **NoSync** en el cuadro valor. Haga clic en **siguiente**.  
![2 ámbito de entrada](./media/active-directory-aadconnectsync-configure-filtering/inbound2.png)  
6. Deje en blanco las reglas de **combinación** y, a continuación, haga clic en **siguiente**.
7. Haga clic en **Agregar transformación**, seleccione el **flujo** **constante**, seleccione el atributo de destino **cloudFiltered** y en el cuadro de texto de origen, escriba **True**. Haga clic en **Agregar** para guardar la regla.  
![Entrada de transformación 3](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
8. Para completar la configuración, [Aplicar y comprobar los cambios](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Positivo filtrado, "Sincronizar solo estos"
Expresar filtrado positivo puede ser más difícil, ya que debe tener en cuenta también los objetos que no son obvios sincronizada, como salas de conferencias.

La opción de filtrado positiva requiere dos reglas de sincronización. Uno (o varios) con el ámbito correcto de objetos para sincronizar y un segundo comodín sincronización regla filtro todos los objetos que no se han identificado como un objeto que desea sincronizar.

En el siguiente ejemplo, sólo se sincronizan objetos de usuario en el atributo de departamento tiene el valor **ventas**.

1. Inicie sesión en el servidor que se ejecuta la sincronización de Azure AD Connect con una cuenta que sea miembro del grupo de seguridad **ADSyncAdmins** .
2. Inicie el **Editor de reglas de sincronización** en el menú Inicio.
3. Asegúrese de que está seleccionada la **entrada** y haga clic en **Agregar nueva regla**.
4. Asignar un nombre descriptivo, como "*en de AD: ventas de usuario sincronizar*" a la regla. Seleccione bosque correcto, el **usuario** como el **tipo de objeto de CS**y **persona** como el **tipo de objeto de MV**. Como el **Tipo de vínculo**, seleccione **unirse** en prioridad, escriba un valor no utilizado actualmente por otra regla de sincronización (por ejemplo 501) y, a continuación, haga clic en **siguiente**.  
![Descripción de 4 de entrada](./media/active-directory-aadconnectsync-configure-filtering/inbound4.png)  
5. En **filtro de evaluación**, haga clic en **Agregar grupo**, haga clic en **Agregar cláusula**y, en el atributo seleccione **departamento**. Asegúrese de que el operador se establece en **igual** y escriba el valor de **ventas** en el cuadro valor. Haga clic en **siguiente**.  
![5 ámbito de entrada](./media/active-directory-aadconnectsync-configure-filtering/inbound5.png)  
6. Deje en blanco las reglas de **combinación** y, a continuación, haga clic en **siguiente**.
7. Haga clic en **Agregar transformación**, seleccione el **flujo** **constante**, seleccione el atributo de destino **cloudFiltered** y en el cuadro de texto de origen, escriba **Falso**. Haga clic en **Agregar** para guardar la regla.  
![Entrada de transformación de 6](./media/active-directory-aadconnectsync-configure-filtering/inbound6.png)  
Se trata de un caso especial donde establezca cloudFiltered explícitamente en falso.

    Ahora tenemos que crear la regla de sincronización de comodín.

8. Dar a la regla un nombre descriptivo, como por ejemplo "*en de AD: filtro del usuario comodín*". Seleccione bosque correcto, el **usuario** como el **tipo de objeto de CS**y **persona** como el **tipo de objeto de MV**. Como el **Tipo de vínculo**, seleccione **unirse a** y de mayor prioridad, escriba un valor no utilizado actualmente por otra regla de sincronización (por ejemplo 600). Ha seleccionado una prioridad valor superior (prioridad inferior) a la regla de sincronización anterior pero también dejó espacio podemos agregar más reglas de filtrado de sincronización más adelante cuando desee iniciar la sincronización de departamentos adicionales. Haga clic en **siguiente**.  
![Descripción de 7 de entrada](./media/active-directory-aadconnectsync-configure-filtering/inbound7.png)  
9. Deje vacío **el filtro de evaluación** y haga clic en **siguiente**. Un filtro vacío indica que la regla debe aplicarse a todos los objetos.
10. Deje en blanco las reglas de **combinación** y, a continuación, haga clic en **siguiente**.
11. Haga clic en **Agregar transformación**, seleccione el **flujo** **constante**, seleccione el atributo de destino **cloudFiltered** y en el cuadro de texto de origen, escriba **True**. Haga clic en **Agregar** para guardar la regla.  
![Entrada de transformación 3](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)  
12. Para completar la configuración, [Aplicar y comprobar los cambios](#apply-and-verify-changes).

Si necesita, puede crear más reglas del primer tipo donde incluir más objetos en la sincronización.

### <a name="outbound-filtering"></a>El filtrado de salida
En algunos casos, es necesario realizar el filtrado solo después de que se han unido los objetos en el metaverso. Por ejemplo, se podría necesario para ver el atributo de correo desde el bosque de recursos y el atributo userPrincipalName del bosque de cuentas para determinar si se debe sincronizar un objeto. En estos casos, se crea el filtrado de la regla de salida.

En este ejemplo, cambiar los usuarios para que sólo filtrados dónde correo y userPrincipalName fin con @contoso.com están sincronizados:

1. Inicie sesión en el servidor que se ejecuta la sincronización de Azure AD Connect con una cuenta que sea miembro del grupo de seguridad **ADSyncAdmins** .
2. Inicie el **Editor de reglas de sincronización** en el menú Inicio.
3. En **Tipo de reglas**, haga clic en **saliente**.
4. Busque la regla denominada **Salí a AAD – usuario unirse a SOAInAD**. Haga clic en **Editar**.
5. En el menú emergente, responda **Sí** para crear una copia de la regla.
6. En la página **Descripción** , cambie prioridad a un valor sin usar, por ejemplo 50.
7. En el panel de navegación izquierdo, haga clic en **filtro de evaluación** . Haga clic en **Agregar cláusula**, atributo, seleccione **correo**, operador select **ENDSWITH**y tipo de valor **@contoso.com**. Haga clic en **Agregar cláusula**, seleccione de atributo **userPrincipalName**, operador select **ENDSWITH**y tipo de valor **@contoso.com**.
8. Haga clic en **Guardar**.
9. Para completar la configuración, [Aplicar y comprobar los cambios](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Aplicar y comprobar los cambios
Una vez haya realizado los cambios de configuración, estos se deben aplicar a los objetos que ya están en el sistema. También es posible que deben procesarse objetos no está en el motor de sincronización y el motor de sincronización necesita leer el sistema de origen para comprobar su contenido.

Si ha cambiado la configuración con el **dominio** o **unidad organizativa** filtrado, debe realizar **importación completa** seguido de **sincronización de Delta**.

Si cambia la configuración utilizando el **atributo** filtrado, debe realizar una **sincronización completa**.

Realice los pasos siguientes:

1. Iniciar el **Servicio de sincronización** en el menú Inicio.
2. Seleccione los **conectores** y en la lista de **conectores** , seleccione el conector donde ha realizado una configuración de cambiar de una versión anterior. En **acciones**, seleccione **Ejecutar**.  
![Ejecutar el conector](./media/active-directory-aadconnectsync-configure-filtering/connectorrun.png)  
3. En el cuadro **Ejecutar perfiles**, seleccione la operación que se mencionan en la sección anterior. Si necesita realizar dos acciones, ejecute el segundo una vez completada la primera que (la columna **estado** es **inactivo** durante el conector seleccionado).

Después de la sincronización, todos los cambios se almacenan provisionalmente para exportar. Antes de implementar los cambios en Azure AD, que desea comprobar que todos estos cambios son correctos.

1. Inicie un símbolo del sistema y vaya a`%Program Files%\Microsoft Azure AD Sync\bin`
2. Ejecutar:`csexport "Name of Connector" %temp%\export.xml /f:x`  
El nombre del conector puede encontrarse en el servicio de sincronización. Tiene un nombre parecido a "contoso.com: AAD" para Azure AD.
3. Ejecutar:`CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. Ahora tiene un archivo en % temp % denominado export.csv que se puede examinar en Microsoft Excel. Este archivo contiene todos los cambios que se exportarán.
5. Realice los cambios necesarios a los datos o la configuración y ejecutar estos pasos nuevo (importar, sincronizar y comprobar) hasta que se esperan que los cambios que van a exportar.

Cuando haya terminado, exporte los cambios a Azure AD.

1. Seleccione los **conectores** y en la lista de **conectores** , seleccione el conector de Azure AD. En **acciones**, seleccione **Ejecutar**.
2. En el cuadro **Ejecutar perfiles**, seleccione **Exportar**.
3. Si los cambios de configuración eliminar muchos objetos, a continuación, verá un error en la exportación cuando el número es mayor que el umbral configurado (de forma predeterminada 500). Si ve este error, debe desactivar temporalmente la característica [evitar eliminaciones accidentales](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md).

Ahora es el momento de volver a habilitar al programador.

1. Iniciar **Programador de tareas** desde el menú Inicio.
2. Directamente en la **Biblioteca del programador de tareas**, busque la tarea llamada **Programador de Azure AD Sync**, con el botón secundario y seleccione **Habilitar**.

## <a name="next-steps"></a>Pasos siguientes
Más información sobre la configuración de [sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Más información acerca de cómo [integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md).
