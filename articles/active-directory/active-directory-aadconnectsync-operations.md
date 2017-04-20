<properties
   pageTitle="Sincronización de Azure AD Connect: tareas operativas y consideraciones | Microsoft Azure"
   description="Este tema describe las tareas operativas de sincronización de Azure AD Connect y para preparar operativo este componente."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/01/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-operational-tasks-and-consideration"></a>Sincronización de Azure AD Connect: tareas operativas y cuenta
El objetivo de este tema es describir las tareas operativas para la sincronización de Azure AD Connect.

## <a name="staging-mode"></a>Modo de ensayo
Modo de ensayo puede utilizarse para varios escenarios, incluidos:

-   Alta disponibilidad.
-   Probar e implementar nuevos cambios de configuración.
-   Introducir a un servidor nuevo y retirar al antiguo.

Con un servidor en modo de ensayo, puede realizar cambios en la configuración y obtener una vista previa de los cambios antes de realizar el servidor activo. También permite ejecutar importación completa y sincronización completa para comprobar que todos los cambios se esperan antes de realizar estos cambios en su entorno de producción.

Durante la instalación, puede seleccionar el servidor que esté en **modo de ensayo**. Esta acción activa el servidor para importar y sincronización, pero no se ejecuta las exportaciones. Un servidor en modo de ensayo no está ejecutando sincronización de la contraseña o reescritura de contraseña, incluso si ha seleccionado estas características durante la instalación. Al deshabilitar el modo de ensayo, el servidor inicia la exportación, habilita la sincronización de la contraseña y permite reescritura de contraseña.

Aún puede forzar una exportación mediante el Administrador de servicios de sincronización.

Un servidor en modo de ensayo sigue recibiendo cambios de Active Directory y Azure AD. Siempre dispone de una copia de los cambios más recientes y que pueden tomar muy rápida sobre las responsabilidades de otro servidor. Si realiza cambios en la configuración en el servidor principal, es su responsabilidad para realizar los mismos cambios en el servidor en modo de ensayo.

Para aquellos con conocimiento de las tecnologías de sincronización anteriores, el modo provisional es diferente, ya que el servidor tiene su propia base de datos SQL. Esta arquitectura permite el servido de modo que se encuentra en un centro de datos diferente.

### <a name="verify-the-configuration-of-a-server"></a>Compruebe la configuración de un servidor
Para aplicar este método, siga estos pasos:

1. [Preparar](#prepare)
2. [Importar y sincronizar](#import-and-synchronize)
3. [Comprobar](#verify)
4. [Servidor de conmutador activo](#switch-active-server)

#### <a name="prepare"></a>Preparar

1. Instalar Azure AD Connect, seleccione **el modo de ensayo**y a continuación, anule la selección de **iniciar la sincronización** en la última página del Asistente de instalación. Este modo permite ejecutar el motor de sincronización de forma manual.
![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. Desactiva o inicio de sesión de inicio de sesión y desde el menú Inicio, seleccione **El servicio de sincronización**.

#### <a name="import-and-synchronize"></a>Importar y sincronizar

1. Seleccione **los conectores**y seleccione el primer conector con el tipo de **Servicios de dominio de Active Directory**. Haga clic en **Ejecutar**, seleccione **importación completa**y en **Aceptar**. Siga estos pasos para todos los conectores de este tipo.
2. Seleccione el conector con el tipo de **Azure Active Directory (Microsoft)**. Haga clic en **Ejecutar**, seleccione **importación completa**y en **Aceptar**.
3. Asegúrese de que la ficha conectores todavía está seleccionada. Para cada conector con el tipo de **Servicios de dominio de Active Directory**, haga clic en **Ejecutar**, seleccione **Sincronización Delta**y en **Aceptar**.
4. Seleccione el conector con el tipo de **Azure Active Directory (Microsoft)**. Haga clic en **Ejecutar**, seleccione **Sincronización Delta**y en **Aceptar**.

Ahora ha organizado exportar cambia a Azure AD y local AD (si está utilizando la implementación híbrida de Exchange). Los siguientes pasos permiten inspeccionar lo que va a cambiar antes de empezar la exportación a los directorios.

#### <a name="verify"></a>Comprobar

1. Inicie un símbolo del sistema y vaya a`%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Ejecutar:`csexport "Name of Connector" %temp%\export.xml /f:x`  
El nombre del conector puede encontrarse en el servicio de sincronización. Tiene un nombre parecido a "contoso.com: AAD" para Azure AD.
3. Ejecutar:`CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. Tiene un archivo en % temp % denominado export.csv que se puede examinar en Microsoft Excel. Este archivo contiene todos los cambios que se exportarán.
5. Realice los cambios necesarios a los datos o la configuración y ejecutar estos pasos nuevo (importar y sincronizar y comprobar) hasta que se esperan que los cambios que van a exportar.

**Comprender el archivo de export.csv**

La mayoría del archivo es fácil de entender. Algunos abreviaturas a entender el contenido:

- OMODT: tipo de objeto de modificación. Indica si la operación en un nivel de objeto es un agregar, actualizar o eliminar.
- AMODT: tipo de modificación de atributo. Indica si la operación en un nivel de atributo es un agregar, actualizar o eliminar.

Si el valor del atributo es multivalor, se muestran no todos los cambios. Solo el número de valores, agregar y quitar está visible.

#### <a name="switch-active-server"></a>Servidor de conmutador activo

1. En el servidor actualmente activo, desactive el servidor (DirSync, FIM/Azure AD Sync) no se exporta a Azure AD o establecer para la realización de modo (Azure AD Connect).
2. Ejecutar al Asistente para instalación en el servidor en **modo de ensayo** y deshabilitar **el modo de ensayo**.
![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## <a name="disaster-recovery"></a>Recuperación
Parte del diseño de implementación es planear para que saber qué hacer si hay un desastre donde se pierde el servidor de sincronización. Existen diferentes modelos de uso y utilizar depende de varios factores como:

-   ¿Qué es la tolerancia para no que pueda realizar cambios en objetos en Azure AD durante el tiempo de inactividad?
-   ¿Si utiliza la sincronización de contraseñas, los usuarios aceptan que debe usar la contraseña antigua en Azure AD en caso de cambian local?
-   ¿Tiene una dependencia en operaciones en tiempo real, como reescritura de contraseña?

Dependiendo de las respuestas a estas preguntas y la directiva de su organización, puede implementar una de las siguientes estrategias:

-   Volver a generar cuando sea necesario.
-   Tiene un servidor de reserva libre, conocido como **modo de ensayo**.
-   Use máquinas virtuales de Windows.

Si no utiliza la base de datos SQL Express integrado, también debería revisar la sección de [Alta disponibilidad de SQL](#sql-high-availability) .

### <a name="rebuild-when-needed"></a>Volver a generar cuando sea necesario
Una estrategia viable es planear una regeneración del servidor cuando sea necesario. Normalmente, instalar el motor de sincronización y que pueden completar la importación inicial y la sincronización con unas pocas horas. Si no hay un servidor de reserva, es posible usar temporalmente un controlador de dominio para hospedar el motor de sincronización.

El servidor de motor de sincronización no almacenar cualquier estado acerca de los objetos para que se puede reconstruir la base de datos de los datos de Active Directory y Azure AD. El atributo **sourceAnchor** se usa para unirse a los objetos de la nube y local. Si reconstruir el servidor con existente objetos local y la nube, a continuación, el motor de sincronización coincide con los objetos juntos nuevamente en reinstalación. Todo lo que debe de documentos y guardar es los cambios de configuración realizados en el servidor, como las reglas de filtrado y la sincronización. Deben volver a aplicar estas configuraciones personalizadas antes de iniciar la sincronización.

### <a name="have-a-spare-standby-server---staging-mode"></a>Tiene un servidor de reserva reserva - modo de ensayo
Si tiene un entorno más complejo, a continuación, se recomienda tener uno o más servidores en espera. Durante la instalación, puede habilitar un servidor que esté en **modo de ensayo**.

Para obtener más detalles, vea [modo de ensayo](#staging-mode).

### <a name="use-virtual-machines"></a>Usar máquinas virtuales de Windows
Es un método comunes y compatible ejecutar el motor de sincronización en una máquina virtual. En caso de que el host tiene un problema, la imagen con el servidor de motor de sincronización se puede migrar a otro servidor.

### <a name="sql-high-availability"></a>Alta disponibilidad SQL
Si no está utilizando SQL Server Express que viene con Azure AD Connect, alta disponibilidad para SQL Server también debe considerarse. La solución de disponibilidad alta solo admitida es clústeres de SQL. Las soluciones no admitidas incluyen reflejo y siempre en.

## <a name="next-steps"></a>Pasos siguientes

**Temas de información general**  

- [Sincronización de Azure AD Connect: entender y personalizar la sincronización](active-directory-aadconnectsync-whatis.md)  
- [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)  
