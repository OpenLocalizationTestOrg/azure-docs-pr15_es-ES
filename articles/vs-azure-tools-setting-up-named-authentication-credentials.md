<properties
   pageTitle="Configurar las credenciales de autenticación con el nombre | Microsoft Azure"
   description="Aprenda cómo a proporcionar las credenciales de Visual Studio puede usar para autenticar solicitudes de Azure para publicar una aplicación en Azure de Visual Studio o para supervisar un servicio de nube existente... "
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="setting-up-named-authentication-credentials"></a>Configurar las credenciales de autenticación con nombre

Para publicar una aplicación en Azure de Visual Studio o para supervisar un servicio de nube existente, debe proporcionar las credenciales que se puede usar Visual Studio para autenticar las solicitudes de Azure. Hay varios lugares en Visual Studio, donde puede iniciar sesión para proporcionar estas credenciales. Por ejemplo, desde el Explorador de servidores, puede abrir el menú contextual para el nodo de **Azure** y elija **Conectar a Azure**. Cuando inicie sesión, la información de suscripción asociada a su cuenta de Azure está disponible en Visual Studio y no hay nada más que tiene que hacer.

Herramientas de Azure también admite una manera de proporcionar credenciales, antigua con la suscripción (archivo .publishsettings). Este tema describe este método, que aún se admite en Azure SDK 2.2.

Los siguientes elementos son necesarios para la autenticación de Azure.

- El identificador de la suscripción

- Un certificado X.509 v3 válido

>[AZURE.NOTE] La longitud de clave de certificado X.509 v3 debe tener al menos 2048 bits. Azure rechaza cualquier certificado que no cumple este requisito o que no es válida.

Visual Studio utiliza el identificador de suscripción junto con los datos del certificado como credenciales. Se hace referencia a las credenciales adecuadas en el archivo de suscripción (archivo .publishsettings), que contiene una clave pública para el certificado. El archivo de suscripción puede contener credenciales de más de una suscripción.

Puede editar la información de la suscripción desde el cuadro de diálogo **Crear o Editar suscripción** , como se explica más adelante en este tema.

Si desea crear un certificado usted mismo, puede hacer referencia a las instrucciones en [crear y cargar un certificado de administración de Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) y manualmente cargar el certificado en el [portal de clásico de Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

>[AZURE.NOTE] Estas credenciales que requiere Visual Studio para administrar los servicios de nube no son las mismas credenciales necesarias para autenticar una solicitud respecto a los servicios de almacenamiento de Azure.

## <a name="modify-or-export-authentication-credentials-in-visual-studio"></a>Modificar o exportar las credenciales de autenticación en Visual Studio

También puede configurar, modificar o exportar sus credenciales de autenticación en el cuadro de diálogo **Nueva suscripción** , que aparece si realiza alguna de las siguientes acciones:

- En el **Explorador de servidores**, abra el menú contextual para el nodo de **Azure** , elija **Administrar suscripciones**, elija la ficha **certificados** y elija el botón **nuevo** o **Editar** .

- Cuando se publica un servicio de nube Azure desde el Asistente para **Publicar aplicaciones de Azure** , elija **Administrar** en la lista **Elegir la suscripción** , a continuación, elija la ficha certificados y, a continuación, elija el botón **nuevo** o **Editar** .

El procedimiento siguiente asume que está abierto el cuadro de diálogo **Nueva suscripción** .

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>Para configurar las credenciales de autenticación en Visual Studio

1. En **Seleccionar un certificado existente** para la lista de autenticación, seleccione un certificado.

1. Elija el botón **copiar la ruta de acceso completa** . La ruta de acceso para el certificado (archivo .cer) se copia en el Portapapeles.

    >[AZURE.IMPORTANT] Para publicar la aplicación de Azure desde Visual Studio, debe cargar este certificado en el [portal clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

1. Para cargar el certificado en el [portal de clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885):

    1. Elija el vínculo de Azure Portal.

         Se abre el [portal clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885) .

    1. Inicie sesión en el [portal de clásica Azure](http://go.microsoft.com/fwlink/?LinkID=213885)y, a continuación, elija el botón de **Servicios en la nube** .

    1. Elija el servicio de nube que le interese.

        Se abrirá la página para el servicio.

    1. En la ficha **certificados** , elija el botón **cargar** .

    1. Pegue la ruta de acceso completa del archivo .cer que acaba de crear y, a continuación, escriba la contraseña que ha especificado.
