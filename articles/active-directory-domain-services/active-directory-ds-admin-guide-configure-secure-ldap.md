<properties
    pageTitle="Configurar LDAP seguro (LDAPS) en servicios de dominio de Active Directory Azure | Microsoft Azure"
    description="Configurar LDAP seguro (LDAPS) para un dominio administrado de servicios de dominio de Active Directory de Azure"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="maheshu"/>

# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Configurar LDAP seguro (LDAPS) para un dominio administrado de servicios de dominio de Active Directory de Azure
Este artículo le muestra cómo se puede habilitar seguro ligero Directory Access Protocol (LDAPS) de su dominio de servicios de dominio de Active Directory de Azure administrado. LDAP seguro es también conocida como ' protocolo de acceso a directorios de ligero (LDAP) sobre Secure Sockets Layer (SSL) y seguridad de capa de transporte (TLS)'.

## <a name="before-you-begin"></a>Antes de empezar
Para realizar las tareas que se enumeran en este artículo, debe:

1. Una **suscripción de Azure**válida.

2. Un **directorio de Azure AD** - ya sea sincronizado con un directorio local o solo de la nube.

3. **Servicios de dominio de Active Directory de Azure** debe estar habilitada para el directorio de Azure AD. Si todavía no lo ha hecho, siga todas las tareas descritas en la [Guía de introducción](./active-directory-ds-getting-started.md).

4. Un **certificado que se utilizará para habilitar LDAP seguro**.
    - **Se recomienda** - obtener un certificado de la entidad emisora de certificados o entidad de certificación pública. Esta opción de configuración es más segura.
    - Como alternativa, también puede crear [un certificado autofirmado](#task-1---obtain-a-certificate-for-secure-ldap) tal como se muestra más adelante en este artículo.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>Requisitos del certificado LDAP seguro
Adquirir un certificado válido por lo siguiente antes de habilitar LDAP seguro. Encuentre errores si intenta habilitar LDAP seguro de su dominio administrado con un certificado incorrecto o no válido.

1. **Emisor de confianza** - el certificado debe ser emitido por una entidad de confianza para equipos que necesitan conectarse al dominio mediante LDAP seguro. Esta entidad puede ser entidad de certificación de empresa de su organización o una entidad emisora de confianza para estos equipos.

2. **Duración** : el certificado debe ser válido para los próximos meses 3 a 6. Acceso seguro de LDAP a su dominio administrado se interrumpe cuando expira el certificado.

3. **Nombre de asunto** : el nombre del asunto en el certificado debe ser un carácter comodín para su dominio administrado. Por ejemplo, si el dominio se denomina 'contoso100.com', debe ser el nombre del certificado asunto ' *. contoso100.com'. Establecer el nombre DNS (nombre de asunto alternativo) para este nombre comodín.

3. **Uso de claves** - el certificado debe estar configurado para el siguiente usa - firmas digitales y cifrado de clave.

4. **Propósito del certificado** - el certificado debe ser válido para la autenticación de servidor SSL.

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>Tarea 1: obtener un certificado para LDAP seguro
La primera tarea consiste en obtener un certificado utilizado para el acceso LDAP seguro en el dominio administrado. Tiene dos opciones:

- Obtener un certificado de una entidad emisora de certificados. Puede ser la autoridad CA de empresa de su organización o una entidad emisora de certificados.

- Crear un certificado autofirmado.


### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>Opción un (recomendado) - obtener un certificado LDAP seguro desde una entidad emisora de certificados
Si su organización implementa una empresa pública clave infraestructura, debe obtener un certificado de la entidad de certificación (CA) enterprise para su organización. Si su organización obtiene sus certificados de una entidad emisora de certificados, debe obtener el certificado LDAP seguro desde esa entidad emisora de certificados.

Cuando se solicita un certificado, asegúrese de seguir los requisitos descritos en [requisito para el certificado LDAP seguro](#requirements-for-the-secure-ldap-certificate).

> [AZURE.NOTE] Los equipos cliente que necesitan para conectar con el dominio administrado usando LDAP seguro deben confiar en el emisor del certificado LDAPS.


### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>Opción B - crear un certificado autofirmado para LDAP seguro
Para crear un certificado autofirmado para LDAP seguro, puede elegir si:

- certificados de su organización no están emitidos por una entidad de certificación de empresa o
- No espere a usar un certificado de una entidad emisora de certificados.

**Crear un certificado autofirmado con PowerShell**

En el equipo de Windows, abra una nueva ventana de PowerShell como **Administrador** y escriba los siguientes comandos para crear un nuevo certificado autofirmado.

    $lifetime=Get-Date

    New-SelfSignedCertificate -Subject *.contoso100.com -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment -Type SSLServerAuthentication -DnsName *.contoso100.com

En el ejemplo anterior, reemplace 'contoso100.com' con el nombre de dominio DNS de su dominio de servicios de dominio de Active Directory de Azure administrado.

![Seleccionar directorio de Azure AD](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

El certificado autofirmado recién creado se coloca en el almacén de certificados del equipo local.


## <a name="task-2---export-the-secure-ldap-certificate-to-a-pfx-file"></a>Tarea 2: exportar la LDAP seguro de certificados a una. Archivo PFX
Antes de comenzar esta tarea, asegúrese de que ha obtenido el certificado LDAP seguro de la entidad de certificación de empresa o una entidad de certificación pública o ha creado un certificado autofirmado.

Realice los pasos siguientes para exportar el certificado LDAPS a una. Archivo PFX.

1. Presione el botón **Inicio** y escriba **R**. En el cuadro de diálogo **Ejecutar** , escriba **mmc** y haga clic en **Aceptar**.

    ![Iniciar la consola MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)

2. En el símbolo del sistema de **Control de cuentas de usuario** , haga clic en **Sí** para iniciar MMC (consola de administración de Microsoft) como administrador.

3. En el menú **archivo** , haga clic en **Agregar o quitar complemento-en...**.

    ![Agregar complemento de consola MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)

4. En el cuadro de diálogo **Agregar o quitar complementos** , seleccione el complemento de **certificados** y a continuación, haga clic en el **Agregar >** botón.

    ![Agregar complemento de certificados a consola MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)

5. En el asistente **complemento certificados** , seleccione la **cuenta de equipo** y haga clic en **siguiente**.

    ![Agregar el complemento certificados para la cuenta de equipo](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)

6. En la página **Seleccionar el PC** , seleccione **equipo Local: (el equipo que se está ejecutando esta consola)** y haga clic en **Finalizar**.

    ![Agregar complemento certificados - seleccione PC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)

7. En el cuadro de diálogo **Agregar o quitar complementos** , haga clic en **Aceptar** para agregar el complemento certificados a MMC.

    ![Agregar complemento certificados a MMC - terminado](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)

8. En la ventana MMC, haga clic para expandir la **Raíz de la consola**. Verá el complemento certificados cargado. Haga clic en **certificados (equipo Local)** para expandir. Haga clic para expandir el nodo **Personal** , seguido por el nodo **certificados** .

    ![Almacén de certificados personales abierto](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)

9. Debe ver el certificado autofirmado que hemos creado. Puede examinar las propiedades del certificado para asegurarse de que coincida con la huella digital que informa de windows PowerShell cuando creó el certificado.

10. Seleccione el certificado autofirmado y **haga clic en**. En el menú contextual, seleccione **Todas las tareas** y seleccione **Exportar**.

    ![Exportar certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)

11. En el **Asistente para exportación de certificados**, haga clic en **siguiente**.

    ![Asistente para exportación de certificados](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)

12. En la página **Exportar la clave privada** , seleccione **exportar la clave privada**y haga clic en **siguiente**.

    ![Exportar clave privada de certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [AZURE.WARNING] DEBE exportar la clave privada junto con el certificado. Si desea proporcionar una PFX que no contiene la clave privada para el certificado, habilitar LDAP seguro de su dominio administrado se produce un error.

13. En la página de **Formato de archivo de exportación** , seleccione **intercambio de información Personal: PKCS #12 (. PFX)** como el formato de archivo para el certificado exportado.

    ![Formato de archivo de certificado de exportación](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [AZURE.NOTE] Solo la. Formato de archivo PFX es compatible. No exportar el certificado para la. Formato de archivo CER.

14. En la página de **seguridad** , seleccione la opción de **contraseña** y escriba una contraseña para proteger la. Archivo PFX. Recordar esta contraseña, ya que se necesitará en la siguiente tarea. Haga clic en **siguiente** para continuar.

    ![Contraseña para la exportación de certificado ](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [AZURE.NOTE] Anote esta contraseña. Necesita al habilitar LDAP seguro para este dominio administrado en la [tarea 3 - habilitar LDAP seguro del dominio administrado](#task-3---enable-secure-ldap-for-the-managed-domain)

15. En la página **archivo para exportar** , especifique el nombre de archivo y la ubicación donde desea exportar el certificado.

    ![Ruta de acceso para exportar certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)

16. En la página siguiente, haga clic en **Finalizar** para exportar el certificado a un archivo PFX. Verá el cuadro de diálogo de confirmación cuando se ha exportado el certificado.

    ![Exportar certificado finalizado](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)


## <a name="task-3---enable-secure-ldap-for-the-managed-domain"></a>Tarea 3: habilitar LDAP seguro del dominio administrado
Para habilitar LDAP seguro, realice los siguientes pasos de configuración:

1. Desplácese hasta el **[portal clásica Azure](https://manage.windowsazure.com)**.

2. Seleccione el nodo de **Active Directory** en el panel izquierdo.

3. Seleccione el directorio de Azure AD (también denominado 'inquilino'), que se han habilitado Servicios de dominio de Active Directory de Azure.

    ![Seleccionar directorio de Azure AD](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Haga clic en la ficha **Configurar** .

    ![Configurar la ficha de directorio](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. Desplácese hacia abajo hasta la sección de **Servicios de dominio**. Verá una opción titulada **LDAP seguro (LDAPS)** como se muestra en la siguiente captura de pantalla:

    ![Sección de configuración de servicios de dominio](./media/active-directory-domain-services-admin-guide/secure-ldap-start.png)

6. Haga clic en el botón **Configurar certificado...** para que aparezca el cuadro de diálogo **Configurar certificado para LDAP seguro** .

    ![Configurar certificado para LDAP seguro](./media/active-directory-domain-services-admin-guide/secure-ldap-configure-cert-page.png)

7. Haga clic en el icono de carpeta debajo del **Archivo PFX con certificado** para especificar el archivo PFX, que contiene el certificado que desea utilizar para el acceso LDAP seguro en el dominio administrado. También, escriba la contraseña especificada al exportar el certificado para el archivo PFX. A continuación, haga clic en el botón listo en la parte inferior.

    ![Especificar la contraseña y archivos PFX LDAP seguro](./media/active-directory-domain-services-admin-guide/secure-ldap-specify-pfx.png)

8. La sección de la ficha **Configurar** **Servicios de dominio** debe obtener atenuada y se encuentra en la **pendiente...** estado durante unos minutos. Durante este período, se verificará el certificado LDAPS para determinar la precisión y LDAP seguro está configurado para su dominio administrado.

    ![Proteger LDAP - estado pendiente](./media/active-directory-domain-services-admin-guide/secure-ldap-pending-state.png)

    > [AZURE.NOTE] Tarda aproximadamente 10 o 15 minutos para habilitar LDAP seguro de su dominio administrado. Si el certificado LDAP seguro proporcionado no coincide con los criterios requeridos, LDAP seguro no está habilitada para el directorio y verá un error. Por ejemplo, el nombre de dominio es incorrecto, el certificado ha caducado o caduca pronto etcetera.

9. Cuando LDAP seguro correctamente está habilitado para su dominio administrado, la **pendiente...** debería desaparecer el mensaje. Debe ver la huella digital del certificado que se muestra.

    ![LDAP seguro habilitado](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled.png)

<br>


## <a name="task-4---enable-secure-ldap-access-over-the-internet"></a>Tarea 4: habilitar el acceso LDAP seguro en internet
**Tarea opcional** - si no va a tener acceso al dominio administrado mediante LDAPS a través de internet, omitir esta tarea de configuración.

Antes de comenzar esta tarea, asegúrese de que haya completado los pasos indicados en la [tarea 3](#task-3---enable-secure-ldap-for-the-managed-domain).

1. Verá una opción para **Habilitar SECURE LDAP acceso en INTERNET** en la sección de la página **Configurar** **Servicios de dominio** . De forma predeterminada, esta opción está establecida en **NO** como acceso a internet para el dominio administrado sobre LDAP seguro está deshabilitado de forma predeterminada.

    ![LDAP seguro habilitado](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled2.png)

2. Alternar **Habilitar el acceso de LDAP seguro a través de INTERNET de la** en **Sí**. Haga clic en el botón **Guardar** en el panel inferior.
    ![LDAP seguro habilitado](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access.png)

3. La sección de la ficha **Configurar** **Servicios de dominio** debe obtener atenuada y se encuentra en la **pendiente...** estado durante unos minutos. Después de algún tiempo, acceso de internet a su dominio administrado sobre LDAP seguro está habilitado.

    ![Proteger LDAP - estado pendiente](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access-pending-state.png)

    > [AZURE.NOTE] Tardará unos 10 minutos en Habilitar acceso a internet a través de LDAP seguro de su dominio administrado.

4. Cuando un acceso seguro a su dominio administrado por internet LDAP correctamente está habilitado, el **pendiente...** debería desaparecer el mensaje. Debe ver la dirección IP externa que se pueden usar para tener acceso a su directorio sobre LDAPS en el campo **IP dirección para LDAPS acceso externo**.

    ![LDAP seguro habilitado](./media/active-directory-domain-services-admin-guide/secure-ldap-internet-access-enabled.png)

<br>

## <a name="task-5---configure-dns-to-access-the-managed-domain-from-the-internet"></a>Tarea 5: configurar DNS para el acceso al dominio administrado de internet
**Tarea opcional** - si no va a tener acceso al dominio administrado mediante LDAPS a través de internet, omitir esta tarea de configuración.

Antes de comenzar esta tarea, asegúrese de que haya completado los pasos indicados en la [tarea 4](#task-4---enable-secure-ldap-access-over-the-internet).

Una vez que se ha habilitado el acceso LDAP seguro en internet para su dominio administrado, deberá actualizar el DNS para que los equipos cliente pueden encontrar este dominio administrado. Al final de la tarea 4, una dirección IP externa se muestra en la ficha **Configurar** de **IP dirección para LDAPS acceso externo**.

Configure el proveedor DNS externo para que el nombre DNS del dominio (por ejemplo, ' contoso100.com') administrado apunta a esta dirección IP externa. En nuestro ejemplo, es necesario crear la entrada DNS siguiente:

    contoso100.com  -> 52.165.38.113

Eso es todo: ya está listo para conectar con el dominio administrado mediante LDAP seguro a través de internet.

> [AZURE.WARNING] Recuerde que los equipos cliente deben confiar en el emisor del certificado LDAPS para que pueda conectarse correctamente al dominio administrado mediante LDAPS. Si está utilizando una entidad de certificación de empresa o una entidad de certificación públicamente, no es necesario hacer nada, ya que estos proveedores de certificados aceptados confían en los equipos cliente. Si está utilizando un certificado autofirmado, debe instalar la parte pública del certificado autofirmado en el almacén de certificados de confianza en el equipo cliente.

<br>

## <a name="related-content"></a>Contenido relacionado

- [Administrar un dominio administrado de servicios de dominio de Active Directory de Azure](active-directory-ds-admin-guide-administer-domain.md)
