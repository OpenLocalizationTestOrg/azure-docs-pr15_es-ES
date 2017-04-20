<properties
   pageTitle="Obtener datos de Analysis Services de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo conectarse y obtener datos de un servidor de Analysis Services en Azure."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="owend"/>

# <a name="get-data-from-azure-analysis-services"></a>Obtener datos de Azure Analysis Services
Una vez que ha creado un servidor de Azure e implementado un modelo tabular, los usuarios de su organización están listos para conectar y empezar a examinar los datos.

Azure Analysis Services es compatible con conexiones de cliente con [Actualizar modelos de objetos](#client-libraries); TOM, AMO, Adomd.Net o MSOLAP, para conectarse a través de xmla en el servidor. Por ejemplo, Power BI, Power BI Desktop, Excel o cualquier aplicación cliente de terceros que es compatible con los modelos de objetos.

## <a name="server-name"></a>Nombre del servidor
Cuando se crea un servidor de Analysis Services en Azure, especifique un nombre único y la región donde el servidor es que debe crearse. Al especificar el nombre del servidor en una conexión, el esquema de nombres de servidor es:
```
<protocol>://<region>/<servername>
```
 Donde protocolo es cadena **asazure**, la región es el Uri de la región en la que se creó el servidor (por ejemplo, para oeste de Estados Unidos, westus.asazure.windows.net) y nombre_de_servidor es el nombre de su servidor único dentro de la región.

## <a name="get-the-server-name"></a>Obtener el nombre del servidor
Antes de conectarse, debe obtener el nombre del servidor. En **el portal de Azure** > servidor > **información general sobre** > **nombre del servidor**, copie el nombre de servidor completo. Si otros usuarios de su organización se conectan a este servidor demasiado, desea compartir este nombre de servidor con ellos. Al especificar un nombre de servidor, debe usarse la ruta de acceso completa.

![Obtener el nombre del servidor de Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)


## <a name="connect-in-power-bi-desktop"></a>Conectar en Power BI Desktop

> [AZURE.NOTE] Esta característica es la vista previa.

1. [Power BI Desktop](https://powerbi.microsoft.com/desktop/), haga clic en **Obtener datos** > **bases de datos** > **Azure Analysis Services**.

2. En **servidor**, pegue el nombre del servidor desde el Portapapeles.

3. En la **base de datos**, si conoce el nombre de la base de datos de modelo tabular o perspectiva que desea conectarse, péguela aquí. En caso contrario, puede dejar este campo en blanco. Puede seleccionar una base de datos o perspectiva en la pantalla siguiente.

4. Deje la opción predeterminada de **Conectar live** seleccionado y presione **Conectar**. Si se le pedirá que introduzca una cuenta, escriba su cuenta profesional.

5. En **navegador**, expanda el servidor, a continuación, seleccione el modelo o perspectiva que desea conectarse y haga clic en **Conectar**. Un solo clic en un modelo o perspectiva muestra todos los objetos de esa vista.


## <a name="connect-in-power-bi"></a>Conectar en Power BI
1. Crear un archivo de Power BI Desktop que tenga una conexión a un modelo en el servidor.

2. En [Power BI](https://powerbi.microsoft.com), haga clic en **Obtener datos** > **archivos**. Busque y seleccione el archivo.


## <a name="connect-in-excel"></a>Conectar en Excel
Se admite la conexión al servidor de Azure Analysis Services en Excel mediante obtener datos en Excel 2016 o Power Query en versiones anteriores. Se requiere [Msolap.7 proveedor](https://aka.ms/msolap) . No se admite la conexión mediante el Asistente para la importación de tabla en Power Pivot.

1. En Excel 2016, en la cinta de opciones de **datos** , haga clic en **Obtener datos externos** > **Desde otros orígenes** > **De Analysis Services**.

2. En el Asistente para la conexión de datos, en **nombre del servidor**, pegue el nombre del servidor desde el Portapapeles. A continuación, en **las credenciales de inicio de sesión**, seleccione **usar el siguiente nombre de usuario y contraseña**y, a continuación, escriba el nombre de usuario profesional, por ejemplo nancy@adventureworks.com, y la contraseña.

    ![Conectar en el inicio de sesión de Excel](./media/analysis-services-connect/aas-connect-excel-logon.png)

4. En **Seleccionar base de datos y tabla**, seleccione la base de datos y modelo o perspectiva y, a continuación, haga clic en **Finalizar**.

    ![Conectar en el modelo de selección de Excel](./media/analysis-services-connect/aas-connect-excel-select.png)

## <a name="connection-string"></a>Cadena de conexión
Cuando se conecte a Azure Analysis Services mediante el modelo de objetos Tabular, utilice los siguientes formatos de cadena de conexión:

###### <a name="integrated-azure-active-directory-authentication"></a>Autenticación de Azure Active Directory integrada
```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```
Autenticación integrada seleccionará la caché de credenciales de Azure Active Directory si está disponible. Si no es así, se muestra la ventana de inicio de sesión de Azure.

###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Autenticación de Azure Active Directory con el nombre de usuario y contraseña
```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

## <a name="client-libraries"></a>Bibliotecas de cliente
Cuando se conecta a Azure Analysis Services desde Excel u otras interfaces como TOM, AsCmd, ADOMD.NET, debe instalar las últimas bibliotecas de cliente de proveedor. Obtener la información más reciente:  

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>



## <a name="next-steps"></a>Pasos siguientes
[Administrar el servidor](analysis-services-manage.md)
