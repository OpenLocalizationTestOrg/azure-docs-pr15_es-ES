<properties
    pageTitle="Configurar dominio une HDInsight clústeres | Microsoft Azure"
    description="Obtenga información sobre cómo instalar y configurar clústeres HDInsight de dominio"
    services="hdinsight"
    documentationCenter=""
    authors="saurinsh"
    manager="jhubbard"
    editor="cgronlun"
    tags=""/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/26/2016"
    ms.author="saurinsh"/>

# <a name="configure-domain-joined-hdinsight-clusters-preview"></a>Configurar dominio une HDInsight clústeres (vista preliminar)

Obtenga información sobre cómo configurar un clúster de Azure HDInsight con Azure Active Directory (AD Azure) y [Apache Cazador](http://hortonworks.com/apache/ranger/) sacar partido de autenticación segura y directivas de enriquecido acceso basado en roles (RBAC) del control.  HDInsight de dominio sólo se puede configurar en clústeres basados en Linux. Para obtener más información, consulte [clústeres de dominio presente HDInsight](hdinsight-domain-joined-introduction.md).

Este artículo es el primer tutorial de una serie:

- Crear un clúster de HDInsight conectado a Azure AD (a través de la capacidad de servicios de dominio de directorio de Azure) con Cazador Apache habilitado.
- Crear y aplicar directivas de sección a través de Cazador Apache y permitir que los usuarios (por ejemplo, científicos de datos) para conectarse a la sección mediante herramientas basadas en ODBC, por ejemplo, Excel, etcetera de una plantilla. Microsoft está trabajando en agregar otras cargas de trabajo, como HBase, motor y tormenta, al dominio HDInsight pronto.

Un ejemplo de la topología final tiene el siguiente aspecto:

![Topología de HDInsight de dominio](.\media\hdinsight-domain-joined-configure\hdinsight-domain-joined-topology.png)

Dado que Azure AD actualmente solo admite redes virtuales clásicas (VNets) y HDInsight de Linux clústeres sólo admiten Administrador de recursos de Azure había basado VNets, HDInsight de Azure AD integración requiere dos VNets y una interconexión entre ellas. Para obtener información de comparación entre los modelos de implementación de dos, vea [Administrador de recursos de Azure frente a la implementación clásica: comprender los modelos de implementación y el estado de los recursos](../resource-manager-deployment-model.md). Los dos VNets debe estar en la misma región como Azure AD DS.

Nombres de servicio Azure deben ser únicos. En este tutorial, se usan los siguientes nombres. Contoso es un nombre ficticio. *Contoso* debe reemplazarse con un nombre diferente al pasar por el tutorial. 
    
**Nombres:**

|(Propiedad)|Valor|
|--------|-----|
| VNet de Azure AD|contosoaadvnet|
| Máquina Virtual de Azure AD (VM)|contosoaadadmin. Este VM se usa para configurar la unidad de organización y la zona DNS inversa.|
| Directorio de Azure AD|contosoaaddirectory|
| Nombre de dominio de Azure AD|Contoso (contoso.onmicrosoft.com)|
| HDInsight VNet|contosohdivnet|
| Grupo de recursos de HDInsight VNet|contosohdirg|
| Clúster de HDInsight|contosohdicluster|

Este tutorial proporciona los pasos para configurar un clúster de HDInsight unido al dominio. Cada sección contiene vínculos a otros artículos con información adicional.

## <a name="prerequisite"></a>Requisito previo:

- Familiarizarse con [los servicios de dominio de Active Directory de Azure](https://azure.microsoft.com/services/active-directory-ds/) su estructura de [precios](https://azure.microsoft.com/pricing/details/active-directory-ds/) .
- Asegúrese de que la suscripción está en la lista blanca para esta vista previa pública. Puede hacerlo enviando un correo electrónico a hdipreview@microsoft.com con su identificador de suscripción.
- Un certificado SSL firmado por una autoridad de firma de su dominio. Se requiere el certificado configurando LDAP seguro. No se pueden usar los certificados de firma automática.

## <a name="procedures"></a>Procedimientos

1. Crear un VNet clásica Azure para su Azure AD.  
2. Crear y configurar AD Azure y Azure AD DS.
3. Agregar una máquina virtual a la VNet clásico para crear la unidad organizativa. 
4. Crear una unidad organizativa para Azure AD DS.
5. Crear un HDInsight VNet en el modo de administración de recursos de Azure.
6. Configurar zonas DNS inversa para Azure AD DS.
6. Punto los dos VNets.
7. Crear un clúster de HDInsight.

> [AZURE.NOTE] En este tutorial se supone que no tiene un anuncio de Azure. Si tiene una, puede omitir la parte en el paso 2.
    
## <a name="create-an-azure-classic-vnet"></a>Crear un VNet clásico de Azure

En esta sección, creará un VNet clásica con el portal de Azure. En la siguiente sección, habilitar Azure AD DS para su Azure AD en el VNet clásico. Para obtener más información sobre el siguiente procedimiento y el uso de otros métodos de creación de VNet, consulte [crear una red virtual (clásica) a través del portal de Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md).

**Para crear un VNet clásico**

1. Inicie sesión el [portal de Azure](https://portal.azure.com). 
2. Haga clic en **nuevo** > **redes** > **red Virtual**.
3. En **Seleccionar un modelo de implementación**, seleccione **clásico**y, a continuación, haga clic en **crear**.
4. Escriba o seleccione los valores siguientes:

    - **Nombre**: contosoaadvnet
    - **Espacio de direcciones**: 10.1.0.0/16
    - **Nombre de subred**: subred1
    - **Intervalo de direcciones de subred**: 10.1.0.0/24
    - **Suscripción**: (seleccione una suscripción que se utiliza para crear este VNet).
    - **ResourceGroup**:
    - **Ubicación**: (seleccionar una región para el clúster de HDInsight).

        > [AZURE.IMPORTANT] Debe elegir una ubicación que admita Azure AD DS. Para obtener más información, vea [productos disponibles por región](https://azure.microsoft.com/en-us/regions/services/). 
        >
        > La VNet clásica y la VNet de grupo de recursos deben estar en la misma región como Azure AD DS.

5. Haga clic en **crear** para crear la VNet.

## <a name="create-and-configure-azure-ad-ds-for-your-azure-ad"></a>Crear y configurar Azure AD DS para su Azure AD

En esta sección, podrá:

1. Crear un anuncio de Azure.
2. Crear usuarios de Azure AD. Estos usuarios son los usuarios del dominio. Use el primer usuario para configurar el clúster HDInsight con Azure AD.  Los dos usuarios son opcionales para este tutorial. Al configurar directivas de Apache Cazador se utilizará en [subárbol configurar directivas para clústeres HDInsight unido al dominio](hdinsight-domain-joined-run-hive.md) .
3. Crear el grupo de administradores de AAD DC y agregar el usuario de Azure AD al grupo. Use este usuario para crear la unidad organizativa.
4. Habilitar los servicios de dominio de Active Directory Azure (Azure AD DS) para Azure AD.
7. Configurar LDAPS para Azure AD. El protocolo ligero de acceso a directorios (LDAP) se usa para leer y escribir a Azure AD.

Si prefiere usar un anuncio de Azure existente, puede omitir los pasos 1 y 2.

**Para crear un anuncio de Azure**

1. Desde el [portal clásica Azure](https://manage.windowsazure.com), haga clic en **nuevo** > **Servicios de aplicación** > **Active Directory** > **directorio** > **Crear personalizado**. 
3. Escriba o seleccione los valores siguientes:

    - **Nombre**: contosoaaddirectory
    - **Nombre de dominio**: contoso.  Este nombre debe ser único global.
    - **País o región**: seleccione su país o región.
4. Haga clic en **Finalizar**.


**Crear un usuario de Azure AD**

1. Desde el [portal clásica Azure](https://manage.windowsazure.com), haga clic en **Active Directory** -> **contosoaaddirectory**. 
3. En el menú superior, haga clic en **usuarios** .
4. Haga clic en **Agregar usuario**.
4. Escriba el **Nombre de usuario**y, a continuación, haga clic en **siguiente**. 
5. Configurar el perfil de usuario; En **función de**, seleccione **Administrador Global**; y, a continuación, haga clic en **siguiente**.  El rol de administrador Global es necesaria para crear unidades organizativas.
6. Haga clic en **crear** para obtener una contraseña temporal.
7. Hacer una copia de la contraseña y, a continuación, haga clic en **completado**. Más adelante en este tutorial, usará este usuario de administrador global para iniciar sesión el Administrador de la máquina virtual para crear una unidad de organización y configurar DNS inversa.


Siga el mismo procedimiento para crear dos más usuarios con el rol de **usuario** , hiveuser1 y hiveuser2. Los siguientes usuarios se utilizará en la [sección Configurar directivas para clústeres HDInsight unido al dominio](hdinsight-domain-joined-run-hive.md).

**Para crear el grupo de administradores de AAD DC y agregar un usuario de Azure AD**

1. Desde el [portal clásica Azure](https://manage.windowsazure.com), haga clic en **Active Directory** > **contosoaaddirectory**. 
3. En el menú superior, haga clic en **grupos** .
4. Haga clic en **Agregar un grupo** o **Agregar grupo**.
5. Escriba o seleccione los valores siguientes:

    - **Nombre**: los administradores AAD DC.  No cambia el nombre del grupo.
    - **Tipo de grupo**: seguridad.
6. Haga clic en **Finalizar**.
7. Haga clic en **Administradores de DC AAD** para abrir el grupo.
8. Haga clic en **Agregar a miembros**.
9. Seleccione el primer usuario que creó en el paso anterior y, a continuación, haga clic en **completado**.
10. Repita los mismos pasos para crear otro grupo denominado **HiveUsers**y agregue los usuarios de la sección dos del grupo.

Para obtener más información, vea [Servicios de dominio de Active Directory de Azure (vista preliminar) - crear el grupo ' AAD DC administradores '](../active-directory-domain-services/active-directory-ds-getting-started.md).

**Habilitar Azure AD DS para su Azure AD**

1. Desde el [portal clásica Azure](https://manage.windowsazure.com), haga clic en **Active Directory** > **contosoaaddirectory**. 
3. En el menú superior, haga clic en **Configurar** .
4. Desplácese hacia abajo hasta **Servicios de dominio**y configurar los valores siguientes:

    - **Habilitar servicios de dominio para este directorio**: Sí.
    - **Nombre de dominio DNS de servicios de dominio**: muestra el nombre predeterminado DNS del directorio de Azure. Por ejemplo, contoso.onmicrosoft.com.
    - **Servicios de dominio de conectarse a esta red virtual**: seleccione la red virtual clásica que creó anteriormente, es decir, **contosoaadvnet**.
    
6. Haga clic en **Guardar** en la parte inferior de la página. Verá **pendiente...** junto a **Habilitar servicios de dominio para este directorio**.  
7. Esperar hasta **pendiente...** desaparece, y se rellena la **Dirección IP** . Obtener llenará dos direcciones IP. Estos son las direcciones IP de los controladores de dominio que se aprovisione servicios de dominio. Cada dirección IP será visible después de que el controlador de dominio correspondiente está configurado y listo. Anote las dos direcciones IP. Los necesitará más adelante.

Para obtener más información, vea [Servicios de dominio de Active Directory de Azure (vista preliminar) - servicios de dominio de Active Directory de Azure habilitar](../active-directory-domain-services/active-directory-ds-getting-started-enableaadds.md).

**Para sincronizar la contraseña**

Si utiliza su propio dominio, debe sincronizar la contraseña. Consulte [Habilitar la sincronización de contraseña a los servicios de dominio de Azure AD para un solo nube Azure directorio AD](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md).


**Configurar LDAPS para Azure AD**

1. Obtener un certificado SSL firmado por una autoridad de firma de su dominio. No se pueden usar los certificados de firma automática. Si no puede obtener un certificado SSL, por favor, póngase en contacto hdipreview@microsoft.com para una excepción.
1. Desde el [portal clásica Azure](https://manage.windowsazure.com), haga clic en **Active Directory** > **contosoaaddirectory**. 
3. En el menú superior, haga clic en **Configurar** .
4. Desplácese a los **Servicios de dominio**.
5. Haga clic en **Configurar certificado**.
6. Siga las instrucciones para especificar el archivo de certificado y la contraseña. Verá **pendiente...** junto a **Habilitar servicios de dominio para este directorio**.  
7. Esperar hasta **pendiente...** desaparece, y obtuvo rellena **Certificado LDAP seguro** .  Esto puede tardar hasta 10 minutos o más.
 
>[AZURE.NOTE] Si se ejecutan en Azure AD DS algunas tareas en segundo plano, es posible que vea un error al cargar certificado: <i>allí es una operación que se realiza de este inquilino. Vuelva a intentarlo más tarde</i>.  En caso de que experimente este error, vuelva a intentarlo más tarde. La segunda IP de controlador de dominio puede tardar hasta 3 horas estén configurados.

Para obtener más información, vea [Configurar Secure LDAP (LDAPS) para un dominio de servicios de dominio de Active Directory de Azure administrados](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md).

## <a name="configure-an-organizational-unit-and-reverse-dns"></a>Configurar una unidad organizativa y DNS inverso

En esta sección, agregar una máquina virtual a la VNet de Azure AD e instalar herramientas administrativas en esta máquina virtual para que puedan configurar una unidad organizativa e invertir DNS. Búsqueda DNS inversa es necesaria para la autenticación Kerberos.

**Para crear una máquina virtual en la red virtual**

1. Desde el [portal clásica Azure](https://manage.windowsazure.com), haga clic en **nuevo** > **calcular** > **Máquina Virtual** > **Desde la Galería**.
3. Seleccione una imagen y, a continuación, haga clic en **siguiente**.  Si no sabe qué usar uno, seleccione el valor predeterminado, **El centro de datos de Windows Server 2012 R2**.
4. Escriba o seleccione los valores siguientes:

    - Nombre de la máquina virtual: **contosoaadadmin**
    - Nivel: **básico**
    - Nuevo nombre de usuario: (Escriba un nombre de usuario)
    - Contraseña: (Escriba una contraseña)
    
    Tenga en cuenta que el nombre de usuario y la contraseña es el administrador local.
    
5. Haga clic en **siguiente**
6. En una **Red Virtual/región**, seleccione la nueva red virtual que creó en el último paso (contosoaadvnet) y, a continuación, haga clic en **siguiente**.
7. Haga clic en **Finalizar**.

**A RDP VM**

1. Desde el [portal clásica Azure](https://manage.windowsazure.com), haga clic en **máquinas virtuales** > **contosoaadadmin**.
3. En el menú superior, haga clic en **panel** .
4. Haga clic en **Conectar** desde la parte inferior de la página.
5. Siga las instrucciones y use el nombre de usuario de administrador local y la contraseña para conectarse.

**Para unirse a la máquina virtual para el dominio de Azure AD**

1. Desde la sesión de RDP, haga clic en **Inicio**y, a continuación, haga clic en **Administrador del servidor**.
2. Haga clic en el **Servidor Local** desde el menú de la izquierda.
3. En el grupo de trabajo, haga clic en **grupo de trabajo**.
4. Haga clic en **cambiar**.
5. Haga clic en **dominio**, escriba **contoso.onmicrosoft.com**y, a continuación, haga clic en **Aceptar**.
6. Escriba las credenciales de usuario de dominio y, a continuación, haga clic en **Aceptar**.
7. Haga clic en **Aceptar**.
8. Haga clic en **Aceptar** para aceptar reiniciar el equipo.
9. Haga clic en **Cerrar**.
10. Haga clic en **reiniciar ahora**.

Para obtener más información, vea [unirse a una máquina virtual de Windows Server a un dominio administrado](../active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm.md).

**Instalar herramientas de administración de Active Directory y DNS**

1. RDP en **contosoaadadmin** con la cuenta de usuario de Azure AD.
2. Haga clic en **Inicio**y, a continuación, haga clic en **Administrador del servidor**.
3. Menú de la izquierda, haga clic en **panel** .
4. Haga clic en **Administrar**y, a continuación, haga clic en **funciones y agregar**.
5. Haga clic en **siguiente**.
6. Seleccione **basado en roles o característica de instalación**y, a continuación, haga clic en **siguiente**.
7. Seleccione la máquina virtual actual desde el grupo de servidores y haga clic en **siguiente**.
8. Haga clic en **siguiente** para omitir roles.
9. Expanda **Herramientas de administración de servidor remoto**, expanda **Herramientas de administración de roles**, seleccione **Herramientas de AD LDS y AD DS** y **Herramientas del servidor DNS**y, a continuación, haga clic en **siguiente**. 
10. Haga clic en **siguiente**
10. Haga clic en **instalar**.

Para obtener más información, vea [Herramientas de administración de la instalación de Active Directory en la máquina virtual](../active-directory-domain-services/active-directory-ds-admin-guide-administer-domain.md#task-2---install-active-directory-administration-tools-on-the-virtual-machine).


**Para configurar DNS inversa**

1. RDP a contosoaadadmin con la cuenta de usuario de Azure AD.
2. Haga clic en **Inicio**, haga clic en **Herramientas administrativas**y, a continuación, haga clic en **DNS**. 
3. Haga clic en **No** para omitir agregar ContosoAADAdmin.
4. Seleccione **el siguiente equipo**, escriba la dirección IP del primer servidor DNS que configuró anteriormente y, a continuación, haga clic en **Aceptar**.  Deberá ver que el DNS de DC se agrega al panel izquierdo.
3. Expanda el servidor DNS o controlador de dominio, haga clic en **Zonas de búsqueda inversa**y, a continuación, haga clic en **Zona nueva**. Se abre el Asistente para nueva zona.
4. Haga clic en **siguiente**.
5. Seleccione la **zona principal**y, a continuación, haga clic en **siguiente**.
6. Seleccione esta opción **para todos los servidores DNS que se ejecutan en los controladores de dominio en este dominio**y, a continuación, haga clic en **siguiente**.
6. Seleccione la **Zona de búsqueda inversa IPv4**y, a continuación, haga clic en **siguiente**.
7. En **Id. de red**, escriba el prefijo para el rango de red HDInsight VNET y, a continuación, haga clic en **siguiente**. Se creará el HDInsight VNet en la sección siguiente.
8. Haga clic en **siguiente**.
9. Haga clic en **siguiente**.
10. Haga clic en **Finalizar**.



A continuación, se usará la unidad de organización que cree al crear el clúster de HDInsight. Las cuentas de equipo y los usuarios del sistema de Hadoop se colocarán en esta unidad organizativa.

**Crear una unidad organizativa (OU) en un dominio administrado de servicios de dominio de Active Directory de Azure**

1. RDP en **contosoaadadmin** con la cuenta de dominio que está en el grupo de **Administradores de AAD DC** .
2. Haga clic en **Inicio**, haga clic en **Herramientas administrativas**y, a continuación, haga clic en **Centro de administración de Active Directory**.
5. Haga clic en el nombre de dominio en el panel izquierdo. Por ejemplo, contoso.
6. Haga clic en **nuevo** en el nombre de dominio en el panel de **tareas** y, a continuación, haga clic en **Unidad organizativa**.
7. Escriba un nombre, por ejemplo **HDInsightOU**y, a continuación, haga clic en **Aceptar**. 


Para obtener más información, consulte [crear una unidad organizativa (OU) en un dominio de servicios de dominio de Active Directory de Azure administrados](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md).


## <a name="create-a-resource-manager-vnet-for-hdinsight-cluster"></a>Crear un VNet Administrador de recursos de clúster HDInsight

En esta sección, creará un VNet Administrador de recursos Azure que se utilizará para el clúster de HDInsight. Para obtener más información sobre la creación de VNET de Azure con otros métodos, vea [crear una red virtual](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)

Después de crear el VNet, va a configurar la VNet Administrador de recursos para usar los mismos servidores DNS en cuanto a la VNet de Azure AD. Si ha seguido los pasos de este tutorial para crear la VNet clásica y Azure AD, los servidores DNS son 10.1.0.4 y 10.1.0.5.

**Para crear un VNet Administrador de recursos**

1. Inicie sesión el [portal de Azure](https://portal.azure.com).
2. Haga clic en **nuevo**, **redes**y, a continuación, **red Virtual**. 
3. En **Seleccionar un modelo de implementación**, seleccione **Administrador de recursos**y, a continuación, haga clic en **crear**.
4. Escriba o seleccione los valores siguientes:

    - **Nombre**: contosohdivnet
    - **Espacio de direcciones**: 10.2.0.0/16. Asegúrese de que el rango de dirección no puede coincidir con el intervalo de direcciones IP de la VNet clásico.
    - **Nombre de subred**: subred1
    - **Intervalo de direcciones de subred**: 10.2.0.0/24
    - **Suscripción**: (seleccione la suscripción de Azure).
    - **Grupo de recursos**: contosohdirg
    - **Ubicación**: (como la Azure AD VNet, es decir, contosoaadvnet, seleccione la misma ubicación).

5. Haga clic en **crear**.


**Para configurar DNS para el Administrador de recursos de VNet**

1. Desde el [portal de Azure](https://portal.azure.com), haga clic en **más servicios** -> **redes virtuales**. Asegúrese de que no para hacer clic en **redes virtuales (clásicas)**.
2. Haga clic en **contosohdivnet**.
4. Haga clic en **los servidores DNS** desde el lado izquierdo de la hoja nueva.
6. Haga clic en **personalizado**y, a continuación, escriba los valores siguientes:

    - 10.1.0.4
    - 10.1.0.5

    Estas direcciones IP del servidor DNS deben coincidir con a los servidores DNS en el VNet de Azure AD (VNet clásico).
7. Haga clic en **Guardar**.

























## <a name="peer-the-azure-ad-vnet-and-the-hdinsight-vnet"></a>Del mismo nivel la AD VNet Azure y la HDInsight VNet

**Mirar el VNet dos**

1. Inicie sesión el [portal de Azure](https://portal.azure.com).
2. Menú de la izquierda, haga clic en **más servicios** .
3. Haga clic en **redes virtuales**. No haga clic en **redes virtuales (clásicas)**.
4. Haga clic en **contosohdivnet**.  Esta es la HDInsight VNet.
5. En el menú de la izquierda de la hoja, haga clic en **Peerings** .
6. En el menú superior, haga clic en **Agregar** . Se abre el módulo **Agregar interconexión** .
7. En el módulo **Agregar interconexión** , establezca o seleccione los valores siguientes:

    - **Nombre**: ContosoAADHDIVNetPeering
    - **Modelo de implementación de red virtual**: clásico
    - **Suscripción**: seleccione el nombre de suscripción utilizado para el vnet clásico (Azure AD).
    - **Red virtual**: contosoaadvnet.
    - **Permitir el acceso de red virtual**: (comprobar)
    - **Permitir el tráfico reenviado**: (consulte). Deje que las otras dos casillas de verificación desactivada.

8. Haga clic en **Aceptar**.



## <a name="create-hdinsight-cluster"></a>Crear clúster HDInsight


En esta sección, puede crear un clúster de Hadoop basados en Linux en HDInsight mediante el portal de Azure o el [Administrador de recursos de Azure plantilla](../resource-group-template-deploy.md). Para otros métodos de creación de clúster y la descripción de la configuración, vea [HDInsight crear clústeres](hdinsight-hadoop-provision-linux-clusters.md). Para obtener más información sobre cómo usar la plantilla de administrador de recursos para crear clústeres de Hadoop en HDInsight, consulte [clústeres de Hadoop crear en HDInsight con plantillas de administrador de recursos](hdinsight-hadoop-create-windows-clusters-arm-templates.md)


**Para crear un clúster de HDInsight de dominio con el portal de Azure**

1. Inicie sesión el [portal de Azure](https://portal.azure.com).
2. Haga clic en **nuevo**, **inteligencia + análisis**y, a continuación, **HDInsight**.
3. Desde el módulo de **clúster HDInsight de nuevo** , escriba o seleccione los valores siguientes:

    - **Nombre de clúster**: escriba un nombre de clúster nuevo para el clúster HDInsight unido al dominio.
    - **Suscripción**: seleccione una suscripción de Azure utilizada para crear este clúster.
    - **Configuración de clúster**:

        - **Tipo de clúster**: Hadoop. HDInsight de dominio está actualmente solo admite en Hadoop clústeres.
        - **Sistema operativo**: Linux.  HDInsight de dominio solo es compatible con clústeres HDInsight basados en Linux.
        - **Versión**: Hadoop 2.7.3 (HDI 3.5). HDInsight de dominio solo es compatible con la versión de clúster HDInsight 3.5.
        - **Tipo de clúster**: PREMIUM

        Haga clic en **Seleccionar** para guardar los cambios.

    - **Credenciales**: configurar las credenciales de usuario de clúster y el usuario SSH.
    - **Origen de datos**: crear una nueva cuenta de almacenamiento o usar una cuenta de almacenamiento existente como la cuenta de almacenamiento predeterminada para el clúster de HDInsight. La ubicación debe ser la misma que la dos VNets.  La ubicación también es la ubicación del clúster HDInsight.
    - **Precios**: seleccione el número de nodos de trabajo del clúster.
    - **Configuraciones avanzadas**: 

        - **Unirse a dominio & Vnet o subred**: 

            - **Configuración de dominio**: 

                - **Nombre de dominio**: contoso.onmicrosoft.com
                - **Nombre de usuario de dominio**: escriba un nombre de usuario de dominio. Este dominio debe tener los siguientes privilegios: unirse a equipos en el dominio y colocarlas en la unidad de organización que configuró anteriormente; Crear a entidades de servicio en la unidad de organización que ha configurado anteriormente. Crear entradas DNS inversas. Este usuario de dominio se convertirá en el Administrador de este clúster de HDInsight unido al dominio.
                - **Contraseña de dominio**: escriba la contraseña de usuario de dominio.
                - **Unidad organizativa**: escriba el nombre completo de la tht OU configurado previamente. Por ejemplo: OU = HDInsightOU, DC = contoso, DC = onmicrosoft, DC = com
                - **Dirección URL de LDAPS**: ldaps://contoso.onmicrosoft.com:636
                - **Grupo de acceso de usuario**: especificar cuyos usuarios que desea sincronizar con el clúster de grupo de la seguridad. Por ejemplo, HiveUsers.

                Haga clic en **Seleccionar** para guardar los cambios.

                ![Configuración de dominio de dominio une HDInsight portal](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-portal-domain-setting.png)
            - **Una red virtual**: contosohdivnet
            - **Subred**: subred1

            Haga clic en **Seleccionar** para guardar los cambios.       
        Haga clic en **Seleccionar** para guardar los cambios.
    - **Grupo de recursos**: seleccione el grupo de recursos que se usan para la HDInsight VNet (contosohdirg).

4. Haga clic en **crear**.  

Otra opción para crear el clúster de dominio HDInsight es usar la plantilla de administración de recursos de Azure. El procedimiento siguiente muestra cómo:

**Para crear un clúster de dominio une HDInsight con una plantilla de administración de recursos**

1. Haga clic en la siguiente imagen para abrir una plantilla de administrador de recursos en el portal de Azure. La plantilla de administrador de recursos se encuentra en un contenedor de blob público. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Desde el módulo de **parámetros** , escriba los valores siguientes:

    - **Suscripción**: (seleccione la suscripción de Azure).
    - **Grupo de recursos**: haga clic en **Usar existente**, y especifique el mismo grupo de recursos que ha estado utilizando.  Por ejemplo, contosohdirg. 
    - **Ubicación**: especifique una ubicación de grupo de recursos.
    - **Nombre de clúster**: escriba un nombre para el clúster de Hadoop que se creará. Por ejemplo, contosohdicluster.
    - **Tipo de clúster**: seleccione un tipo de clúster.  El valor predeterminado es **hadoop**.
    - **Ubicación**: seleccione una ubicación para el clúster.  La cuenta de almacenamiento predeterminada usa la misma ubicación.
    - **Número de nodos de trabajo de clúster**: seleccione el número de nodos de trabajo.
    - **Contraseña y nombre de inicio de sesión de clúster**: el nombre de inicio de sesión predeterminado es **admin**.
    - **SSH nombre de usuario y contraseña**: el nombre de usuario predeterminado es **sshuser**.  Puede cambiarlo. 
    - **Id. de red virtual**: /subscriptions/&lt;SubscriptionID > /resourceGroups/&lt;ResourceGroupName > /providers/Microsoft.Network/virtualNetworks/&lt;VNetName >
    - **Subred de red virtual**: /subscriptions/&lt;SubscriptionID > /resourceGroups/&lt;ResourceGroupName > /providers/Microsoft.Network/virtualNetworks/&lt;VNetName >/subredes/subred1
    - **Nombre de dominio**: contoso.onmicrosoft.com
    - **Organización unidad DN**: OU = HDInsightOU, DC = contoso, DC = onmicrosoft, DC = com
    - **Grupo de clúster usuarios D Ns**: "\"CN = HiveUsers, OU = AADDC usuarios, DC =<DomainName>, DC = onmicrosoft, DC = com\""
    - **LDAPUrls**: ["ldaps://contoso.onmicrosoft.com:636"]
    - **DomainAdminUserName**: (escriba el nombre de usuario de administración de dominios)
    - **DomainAdminPassword**: (escriba la contraseña de usuario de administración de dominios)
    - **Acepto los términos y condiciones indicadas arriba**: (comprobar)
    - **Anclar al panel**: (comprobar)

6. Haga clic en **comprar**. Verá un nuevo mosaico titulado **implementación de implementación de plantilla**. Tarda aproximadamente 20 minutos para crear un clúster. Una vez creado el clúster, puede hacer clic en el módulo de clúster en el portal para abrirlo.

Después de completar el tutorial, desea eliminar el clúster. Con HDInsight, los datos se almacenan en el almacenamiento de Azure, por lo que puede eliminar un clúster cuando no está en uso. También se cargan un clúster HDInsight, incluso cuando no está en uso. Dado que muchas veces más que los cargos para el almacenamiento de los cargos para el clúster, tiene sentido económico para eliminar clústeres cuando no están en uso. Para obtener las instrucciones de la eliminación de un clúster, vea [Administrar Hadoop clústeres en HDInsight a través del portal de Azure](hdinsight-administer-use-management-portal.md#delete-clusters).





## <a name="next-steps"></a>Pasos siguientes

- Para configurar las directivas de la sección y ejecutar consultas de sección, vea [Configurar subárbol directivas para clústeres HDInsight unido al dominio](hdinsight-domain-joined-run-hive.md).
- Para ejecutar consultas de subárbol con SSH en clústeres HDInsight de dominio, vea [Usar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-domain-joined-hdinsight-cluster).
