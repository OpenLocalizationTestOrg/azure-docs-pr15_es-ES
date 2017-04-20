<properties
    pageTitle="Servicios de dominio de Active Directory Azure: Habilitar servicios de dominio de Active Directory Azure | Microsoft Azure"
    description="Introducción a servicios de dominio de Azure Active Directory"
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
    ms.topic="get-started-article"
    ms.date="10/19/2016"
    ms.author="maheshu"/>

# <a name="enable-azure-ad-domain-services"></a>Habilitar los servicios de dominio de Active Directory de Azure

## <a name="task-3-enable-azure-ad-domain-services"></a>Tarea 3: Habilitar servicios de dominio de Active Directory de Azure
En esta tarea, habilitar servicios de dominio de Active Directory de Azure para el directorio. Realice los siguientes pasos de configuración para habilitar los servicios de dominio de Active Directory de Azure para el directorio.

1. Desplácese hasta el **portal clásica Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Seleccione el nodo de **Active Directory** en el panel izquierdo.

3. Seleccione al inquilino de Azure AD (directorio) para el que desea habilitar servicios de dominio de Active Directory de Azure.

    ![Seleccionar directorio de Azure AD](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Haga clic en la ficha **Configurar** .

    ![Configurar la ficha de directorio](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. Desplácese a una sección de **Servicios de dominio**.

    ![Sección de configuración de servicios de dominio](./media/active-directory-domain-services-getting-started/domain-services-configuration.png)

6. Activar o desactivar la opción titulada **Habilitar servicios de dominio para este directorio** en **Sí**. Observe algunos más opciones de configuración para los servicios de dominio de Active Directory de Azure aparecen en la página.

    ![Habilitar servicios de dominio](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

    > [AZURE.NOTE] Al habilitar servicios de dominio de Active Directory de Azure para su inquilino, Azure AD genera y almacena los hash de credenciales de Kerberos y NTLM necesarios para autenticar a los usuarios.

7. Especifique el **nombre de dominio DNS de servicios de dominio**.

   - El nombre de dominio predeterminado del directorio (es decir, que terminan con la **. onmicrosoft.com** sufijo de dominio) está seleccionado de manera predeterminada.

   - La lista contiene todos los dominios que se han configurado para el directorio de Azure AD-incluidos comprobado así como no verificados dominios que se configura en la ficha 'Dominios'.

   - Además, también puede escribir un nombre de dominio personalizado. En este ejemplo, nos hemos introducido en un nombre de dominio personalizado 'contoso100.com'.

     > [AZURE.WARNING] Asegúrese de que el prefijo del dominio del nombre de dominio que especifique (por ejemplo, 'contoso100' en el nombre de dominio 'contoso100.com') es menos de 15 caracteres. No puede crear un dominio de servicios de dominio de Active Directory de Azure con un prefijo de dominio más de 15 caracteres.

8. Asegúrese de que el nombre de dominio DNS que ha elegido para el dominio administrado no existe en la red virtual. Más concretamente, compruebe si:

   - ya tiene un dominio con el mismo nombre de dominio DNS de la red virtual.

   - la red virtual que ha seleccionado tiene una conexión VPN con su red local y tiene un dominio con el mismo nombre de dominio DNS en su red local.

   - tener un servicio de nube existente con ese nombre en la red virtual.

9. El siguiente paso es seleccionar una red virtual en el que desea que los servicios de dominio de Active Directory de Azure esté disponible. Seleccione la red virtual y subred dedicada que creó en la lista desplegable titulada **Servicios de dominio de conectarse a esta red virtual**.

   - Asegúrese de que la ha especificado una red virtual pertenece a un área de Azure compatible con servicios de dominio de Active Directory de Azure. Consulte la página de [Servicios de Azure por región](https://azure.microsoft.com/regions/#services/) saber las regiones de Azure en la que los servicios de dominio de Azure AD está disponible.

   - Redes virtuales que pertenecen a una región donde no se admite servicios de dominio de Active Directory de Azure no se muestran en la lista desplegable.
   
   - Use una subred dedicada dentro de la red virtual para los servicios de dominio de Active Directory de Azure. Asegúrese de que no seleccione la subred de puerta de enlace. Consulte [Consideraciones de redes](active-directory-ds-networking.md). 

   - Asimismo, redes virtuales que se crearon con el Administrador de recursos de Azure no aparecen en la lista desplegable. Redes virtuales basadas en el Administrador de recursos no son compatibles actualmente con los servicios de dominio de Active Directory de Azure.

10. Para habilitar los servicios de dominio de Active Directory de Azure, haga clic en **Guardar** desde el panel de tareas en la parte inferior de la página.

11. La página muestra un ' pendiente...' estado, mientras que los servicios de dominio de Azure AD se está habilitado para el directorio.

    ![Habilitar servicios de dominio - estado pendiente](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

    > [AZURE.NOTE] Servicios de dominio de Active Directory Azure proporciona alta disponibilidad de su dominio administrado. Después de habilitar servicios de dominio de Active Directory de Azure, observe las direcciones IP a la que están disponibles en la presentación de una red virtual de uno a uno los servicios de dominio. La segunda dirección IP se muestra breve, como pronto el servicio permite alta disponibilidad para su dominio. Cuando alta disponibilidad esté configurado y activo para su dominio, verá dos direcciones IP en la sección de la ficha **Configurar** **Servicios de dominio** .

12. Después de aproximadamente 20-30 minutos, consulte la primera dirección IP a la que está disponible en su red virtual en el campo **dirección IP** en la página **Configurar** servicios de dominio.

    ![Servicios de dominio habilitados - IP primera aprovisionado](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)

13. Cuando esté en funcionamiento para su dominio alta disponibilidad, verá dos direcciones IP que se muestra en la página. Su dominio administrado está disponible en su red virtual seleccionado en estos dos direcciones IP. Tenga en cuenta las direcciones IP para que pueda actualizar la configuración de DNS para su red virtual. Este paso permite máquinas virtuales en una red virtual para conectar con el dominio para operaciones como unirse a un dominio.

    ![Servicios de dominio activado: se aprovisione ambas direcciones IP](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [AZURE.NOTE] Dependiendo del tamaño de su inquilino de Azure AD (número de usuarios, grupos etc.), sincronización a su dominio administrado tiene algún tiempo. Este proceso de sincronización sucede en segundo plano. Para grandes inquilinos con miles de objetos, puede tardar un día o dos para todos los usuarios, pertenencias a grupos y las credenciales se sincronicen.

<br>

## <a name="task-4---update-dns-settings-for-the-azure-virtual-network"></a>Tarea 4: actualizar la configuración de DNS de la red virtual de Azure
La siguiente tarea de configuración es actualizar [la configuración de DNS de la red virtual Azure](active-directory-ds-getting-started-dns.md).
