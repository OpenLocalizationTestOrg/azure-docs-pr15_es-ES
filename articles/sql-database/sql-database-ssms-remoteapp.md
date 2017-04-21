<properties
    pageTitle="Conectarse a la base de datos de SQL con SQL Server Management Studio RemoteApp de Azure | Microsoft Azure"
    description="Use este tutorial para aprender a usar SQL Server Management Studio en Azure RemoteApp de seguridad y el rendimiento al conectarse a la base de datos SQL"
    services="sql-database"
    documentationCenter=""
    authors="adhurwit"
    manager="jhubbard"/>

<tags
    ms.service="sql-database"
    ms.workload="data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="adhurwit"/>

# <a name="use-sql-server-management-studio-in-azure-remoteapp-to-connect-to-sql-database"></a>Usar SQL Server Management Studio en Azure RemoteApp para conectarse a la base de datos SQL

## <a name="introduction"></a>Introducción  
En este tutorial se muestra cómo usar SQL Server Management Studio (SSMS) en Azure RemoteApp para conectarse a la base de datos de SQL. Le guiará por el proceso de configuración de SQL Server Management Studio en Azure RemoteApp, explica las ventajas y muestra las características de seguridad que puede usar en Azure Active Directory.

**Tiempo estimado para completar:** 45 minutos

## <a name="ssms-in-azure-remoteapp"></a>SSMS en Azure RemoteApp

RemoteApp Azure es un servicio RDS en Azure que ofrece aplicaciones. Puede obtener más información sobre él aquí: [¿Qué es RemoteApp?](../remoteapp/remoteapp-whatis.md)

SSMS ejecuta en Azure RemoteApp proporciona la misma experiencia que ejecuta SSMS localmente.

![Captura de pantalla que muestra SSMS ejecuta en Azure RemoteApp][1]



## <a name="benefits"></a>Ventajas

Hay muchas ventajas de usar SSMS en Azure RemoteApp, incluidos:

- No tiene el puerto 1433 en el servidor de SQL Azure exponer externamente (fuera de Azure).
- No es necesario mantener agregar y quitar direcciones IP en el servidor de seguridad de servidor de SQL Azure.
- Todas las conexiones de RemoteApp de Azure se producen a través de HTTPS en puerto 443 con cifrado de protocolo de escritorio remoto
- Es multiusuario y puede escalar.
- Hay un aumento del rendimiento de tener SSMS en la misma región como la base de datos de SQL.
- Puede auditar el uso de Azure RemoteApp con la edición Premium de Azure Active Directory que tiene los informes de actividad de usuario.
- Puede habilitar la autenticación multifactor (AMF).
- Access SSMS en cualquier lugar al utilizar cualquiera de los clientes de Azure RemoteApp compatibles que incluye iOS, Android, Mac, Windows Phone y Windows PC.


## <a name="create-the-azure-remoteapp-collection"></a>Crear la colección de RemoteApp de Azure

Estos son los pasos para crear la colección de Azure RemoteApp con SSMS:


### <a name="1-create-a-new-windows-vm-from-image"></a>1. crear una nueva máquina virtual de Windows de la imagen
Usar la imagen de "Windows Server Remote Desktop sesión Host Windows Server 2012 R2" de la Galería para hacer su nueva máquina virtual.


### <a name="2-install-ssms-from-sql-express"></a>2. instalar SSMS desde SQL Express

Vaya a la nueva máquina virtual y vaya a esta página de descarga: [2014® de Microsoft® SQL Server Express](https://www.microsoft.com/en-us/download/details.aspx?id=42299)

Hay una opción para descargar solo SSMS. Después de la descarga, vaya al directorio de instalación y ejecute el programa de instalación para instalar SSMS.

También debe instalar SQL Server 2014 Service Pack 1. Puede descargar aquí: [Service Pack 1 (SP1) de Microsoft SQL Server de 2014](https://www.microsoft.com/en-us/download/details.aspx?id=46694)

SQL Server 2014 Service Pack 1 incluye funciones esenciales para trabajar con bases de datos de SQL Azure.


### <a name="3-run-validate-script-and-sysprep"></a>3. ejecute validar script y Sysprep

En el escritorio de la máquina virtual se denomina una secuencia de comandos de PowerShell validar. Ejecute esta haciendo doble clic en. Se compruebe que la máquina virtual está lista para usarse con alojamiento remoto de aplicaciones. Cuando finalice la comprobación, le pedirá que ejecute sysprep - elegir ejecutarlo.

Cuando finalice sysprep, se cerrará la máquina virtual.

Para obtener más información sobre la creación de una imagen de RemoteApp de Azure, consulte: [cómo crear una imagen de la plantilla de RemoteApp en Azure](http://blogs.msdn.com/b/rds/archive/2015/03/17/how-to-create-a-remoteapp-template-image-in-azure.aspx)


### <a name="4-capture-image"></a>4. capturar la imagen

Cuando la máquina virtual dejó de funcionar, busque en el portal actual y capturarlo.

Para obtener más información acerca de cómo capturar una imagen, vea [capturar una imagen de una máquina virtual de Windows Azure creada con el modelo de implementación clásica](../virtual-machines/virtual-machines-windows-classic-capture-image.md)


### <a name="5-add-to-azure-remoteapp-template-images"></a>5. agregar imágenes de plantilla de RemoteApp de Azure

En la sección de Azure RemoteApp del portal actual, vaya a la pestaña imágenes de plantilla y haga clic en Agregar. En el cuadro emergente, seleccione "Importar una imagen de la biblioteca de máquinas virtuales" y, a continuación, elija la imagen que acaba de crear.



### <a name="6-create-cloud-collection"></a>6. Crear colección de nube

En el portal actual, cree una nueva colección de nube de RemoteApp de Azure. Elija la imagen de la plantilla que acaba de importar con SSMS instalado en él.

![Crear nueva colección de nube][2]


### <a name="7-publish-ssms"></a>7. publicar SSMS

La publicación de la ficha de la nueva colección de nube, seleccione Publicar una aplicación en el menú Inicio y elija SSMS de la lista.

![Publicar la aplicación][5]

### <a name="8-add-users"></a>8. agregar usuarios

En la ficha acceso de usuario puede seleccionar los usuarios que tendrán acceso a esta colección de RemoteApp de Azure que solo incluye SSMS.

![Agregar usuario][6]


### <a name="9-install-the-azure-remoteapp-client-application"></a>9. instalar la aplicación de cliente de RemoteApp de Azure

Puede descargar e instalar un cliente de Azure RemoteApp aquí: [Descargar | Azure RemoteApp](https://www.remoteapp.windowsazure.com/en/clients.aspx)



## <a name="configure-azure-sql-server"></a>Configurar el servidor SQL Azure

Es la única configuración necesaria para asegurarse de que los servicios de Azure está habilitado para el firewall. Si utiliza esta solución, es necesario agregar las direcciones IP para abrir el firewall. El tráfico de red que se permite a SQL Server es de otros servicios de Azure.


![Permitir de Azure][4]



## <a name="multi-factor-authentication-mfa"></a>Autenticación multifactor (AMF)

AMF puede habilitarse específicamente para esta aplicación. Vaya a la pestaña de aplicaciones de Azure Active Directory. Encontrará una entrada de RemoteApp de Microsoft Azure. Si hace clic en esa aplicación y, a continuación, configura, verá la página siguiente, donde puede habilitar AMF para esta aplicación.

![Habilitar AMF][3]



## <a name="audit-user-activity-with-azure-active-directory-premium"></a>Actividad de usuario de auditoría con Azure Active Directory Premium

Si no tiene Azure AD Premium, debe activar en la sección de licencias de su directorio. Con Premium habilitada, puede asignar a usuarios al nivel Premium.

Cuando vaya a un usuario en Azure Active Directory, a continuación, puede ir a la pestaña actividad para ver información de inicio de sesión a RemoteApp de Azure.



## <a name="next-steps"></a>Pasos siguientes

Después de completar todos los pasos anteriores, estará poder ejecutar al cliente de Azure RemoteApp e iniciar sesión con un usuario asignado. Se le presentará con SSMS como uno de sus aplicaciones y puede ejecutar como lo haría si se han instalado en su equipo con acceso al servidor de SQL Azure.

Para obtener más información sobre cómo realizar la conexión a la base de datos de SQL, vea [conectarse a la base de datos de SQL con SQL Server Management Studio y realizar una consulta de SQL de T de ejemplo](sql-database-connect-query-ssms.md).


Eso es todo por ahora. Disfrute.



<!--Image references-->
[1]: ./media/sql-database-ssms-remoteapp/ssms.png
[2]: ./media/sql-database-ssms-remoteapp/newcloudcollection.png
[3]: ./media/sql-database-ssms-remoteapp/mfa.png
[4]: ./media/sql-database-ssms-remoteapp/allowazure.png
[5]: ./media/sql-database-ssms-remoteapp/publish.png
[6]: ./media/sql-database-ssms-remoteapp/user.png
