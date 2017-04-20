<properties
   pageTitle="Tutorial rápido para el gráfico de Azure AD API | Microsoft Aure"
   description="La API de gráfico de Azure Active Directory proporciona acceso mediante programación a Azure AD a través de extremos de la API de REST de OData. Aplicaciones pueden utilizar la API de gráfico para realizar crear, leer, actualizar y eliminar operaciones (CRUD) en los datos de directorio y objetos."
   services="active-directory"
   documentationCenter="n/a"
   authors="PatAltimore"
   manager="mbaldwin"
   editor=""
   tags=""/>


   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="09/16/2016"
      ms.author="patricka"/>

# <a name="quickstart-for-the-azure-ad-graph-api"></a>Tutorial rápido para el gráfico de Azure AD API

La API de gráfico de Azure Active Directory (AD) proporciona acceso mediante programación a Azure AD a través de extremos de la API de REST de OData. Aplicaciones pueden utilizar la API de gráfico para realizar crear, leer, actualizar y eliminar operaciones (CRUD) en los datos de directorio y objetos. Por ejemplo, puede usar la API de gráfico para crear un nuevo usuario, ver o actualizar las propiedades del usuario, cambiar contraseña del usuario, compruebe la pertenencia a grupos de acceso basado en roles, deshabilitar o eliminar el usuario. Para obtener más información sobre las características de la API de gráfico y los escenarios de aplicación, vea [API de Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) y [los requisitos previos de Azure AD Graph API](https://msdn.microsoft.com/library/hh974476.aspx). 

> [AZURE.IMPORTANT] Funcionalidad de Azure AD Graph API también está disponible a través de [Microsoft Graph](https://graph.microsoft.io/), una API unificada que incluye las API de otros servicios de Microsoft, como Outlook, OneDrive, OneNote, organizador y Office Graph, accesible a través de un solo extremo y con un token de acceso único.

## <a name="how-to-construct-a-graph-api-url"></a>Cómo crear una dirección URL de API de gráfico

En la API de gráfico, para tener acceso a datos de directorio y objetos (es decir, recursos o entidades) a la que desea realizar operaciones de CRUD, puede usar direcciones URL basadas en el protocolo de datos abiertos (OData). Las direcciones URL usadas en la API de Graph constan de cuatro partes principales: raíz, identificador del inquilino, ruta de acceso de recursos y opciones de cadena de consulta de servicio: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. El ejemplo de la siguiente URL: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

- **Raíz del servicio**: en la API de gráfico de Azure AD, la raíz del servicio es siempre https://graph.windows.net.
- **Identificador del inquilino**: puede ser un nombre de dominio (registrado) comprobado, en el ejemplo anterior, contoso.com. También puede ser un identificador de objeto del inquilino o "myorganiztion" o "yo" alias. Para obtener más información, consulte [direccionamiento entidades y las operaciones en la API de gráfico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview)).
- **Ruta de acceso de recursos**: en esta sección de una dirección URL identifica el recurso para que pueda interactuar con (usuarios, grupos, un usuario particular, o un grupo en particular, etcetera.) En el ejemplo anterior, es el nivel superior "grupos de" dirección del conjunto de recursos. También puede tratar una entidad concreta, por ejemplo "usuarios / {Id. de objeto}" o "usuarios/userPrincipalName".
- **¿Parámetros de consulta**:? separa la sección de ruta de acceso de recursos de la sección de parámetros de consulta. Se requiere el parámetro de consulta "versión api" en todas las solicitudes de la API de gráfico. La API de gráfico también admite las siguientes opciones de consulta de OData: **$filter**, **$orderby**, **expanda $**, **$top**y **$format**. Actualmente no se admiten las siguientes opciones de consulta: **$count** **$inlinecount**y **$skip**. Para obtener más información, vea [consultas compatibles, filtros y opciones de paginación en API de Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Las versiones API de gráfico

Especificar la versión de una solicitud de API de gráfico en el parámetro de consulta "versión api". Para 1,5 y versiones posteriores, use un valor numérico versión; versión de la API = 1,6. Para las versiones anteriores, utilice una cadena de fecha que cumple con el formato dd-MM-aaaa; Por ejemplo, versión de la api = 2013-11-08. Características de vista previa, utilice la cadena "beta"; Por ejemplo, versión de la api = beta. Para obtener más información acerca de las diferencias entre versiones de la API de gráfico, vea [versiones de la API de Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Gráfico API de metadatos

Para devolver el archivo de metadatos de la API de gráfico, agregar el segmento "$metadata" después del inquilino identificador en la dirección URL por ejemplo, la siguiente URL devuelve los metadatos para la empresa de demostración utilizada el Explorador de gráfico: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Puede escribir esta dirección URL en la barra de direcciones de un explorador web para ver los metadatos. El documento de metadatos CSDL devuelto describe las entidades y tipos complejos, sus propiedades y las funciones y acciones expuestas por la versión de la API de gráfico que ha solicitado. Si se omite el parámetro de versión de la api devolverá metadatos para la versión más reciente.

## <a name="common-queries"></a>Consultas comunes

[Consultas comunes de Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) enumera las consultas comunes que se pueden usar con el gráfico de AD Azure, incluidas las consultas que se pueden usar para tener acceso a recursos de nivel superior en su directorio y consultas para realizar operaciones en el directorio.

Por ejemplo, `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` devuelve información para contoso.com del directorio de la compañía.

O `https://graph.windows.net/contoso.com/users?api-version=1.6` muestra todos los objetos de usuario en el directorio de contoso.com.

## <a name="using-the-graph-explorer"></a>Mediante el Explorador de gráfico

Puede utilizar el Explorador de gráfico para la API de Azure AD Graph para consultar los datos del directorio al generar la aplicación.

> [AZURE.IMPORTANT] El Explorador de gráfico no admite escritura o eliminar los datos de un directorio. Solo puede realizar operaciones de lectura en el directorio de Azure AD con el Explorador de gráfico.

El siguiente es el resultado que verá si ordenara desplácese hasta el Explorador de gráfico, seleccione la empresa de demostración de uso y escriba `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` para mostrar todos los usuarios en el directorio de demostración:

![Explorador de api de Azure AD graph](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Cargar el Explorador de gráfico**: para cargar la herramienta, vaya a [https://graphexplorer.cloudapp.net/](https://graphexplorer.cloudapp.net/). Haga clic en la **Compañía de demostración de uso** para ejecutar el Explorador de gráfico con los datos de un inquilino de ejemplo. No necesita credenciales para la empresa de demostración de usar. Como alternativa, puede haga clic en **iniciar sesión** e inicie sesión con sus credenciales de cuenta de Azure AD para ejecutar el Explorador de gráfico en el inquilino. Si ejecuta Explorer gráfico en su propia inquilino, usted o su administrador necesitará consentimiento durante el inicio de sesión. Si tiene una suscripción de Office 365, tendrá automáticamente un inquilino de Azure AD. Las credenciales que use para iniciar sesión en Office 365 son, de hecho, cuentas de Azure AD, y puede usar estas credenciales con el Explorador de gráfico.

**Ejecutar una consulta**: para ejecutar una consulta, escriba la consulta en el cuadro de texto de la solicitud y haga clic en **obtener** o haga clic en la tecla **ENTRAR** . Los resultados se muestran en el cuadro respuesta. Por ejemplo, `https://graph.windows.net/graphdir1.onmicrosoft.com /groups?api-version=1.6` mostrará una lista de todos los objetos de grupo en el directorio de demostración.

Tenga en cuenta las siguientes características y limitaciones del explorador de gráfico:
- Capacidad de Autocompletar en conjuntos de recursos. Para ver esto, haga clic en **Uso de demostración de empresa** y, a continuación, haga clic en el cuadro de texto de la solicitud (donde aparece la dirección URL de la empresa). Puede seleccionar un conjunto de la lista desplegable de recursos.

- Es compatible con la "yo" y "myorganization" direccionamiento alias. Por ejemplo, puede usar `https://graph.windows.net/me?api-version=1.6` para devolver el objeto de usuario del usuario que ha iniciado sesión en o `https://graph.windows.net/myorganization/users?api-version=1.6` para devolver todos los usuarios en el directorio actual. Observe que la "yo" alias using devuelve un error para la empresa de demostración porque no hay ningún usuario ha iniciado sesión en que hace la solicitud.

- Una sección de encabezados de respuesta. Esto puede usarse para ayudar a solucionar los problemas que se producen al ejecutar consultas.

- Visor de JSON para la respuesta con las capacidades de expandir y contraer.

- No se admiten para mostrar una foto en miniatura.

## <a name="using-fiddler-to-write-to-the-directory"></a>Utilizar Fiddler para escribir en el directorio

Para el propósito de esta guía de inicio rápido, puede utilizar al depurador de Web Fiddler para practicar realizar operaciones 'escribir' contra directorio de Azure AD. Para obtener más información e instalar a Fiddler, consulte [http://www.telerik.com/fiddler](http://www.telerik.com/fiddler).

En el ejemplo siguiente, usará a Fiddler Web depurador para crear un nuevo grupo de seguridad 'MyTestGroup' en el directorio de Azure AD.

**Obtener un token de acceso**: para obtener acceso a Azure AD Graph, clientes deben autenticarse correctamente en Azure AD en primer lugar. Para obtener más información, vea [Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md).

**Redactar y ejecutar una consulta**: realice los pasos siguientes.

1. Abra Fiddler Web depurador y cambie a la pestaña **Compositor** .
2. Puesto que desea crear un nuevo grupo de seguridad, seleccione **Publicar** como método HTTP desde el menú desplegable. Para obtener más información sobre los permisos y las operaciones en un objeto de grupo, vea [grupo](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#GroupEntity) dentro de la [referencia de API de REST de Azure AD gráfico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. En el campo al lado de **entrada**, escriba lo siguiente como la dirección URL de la solicitud: `https://graph.windows.net/mytenantdomain/groups?api-version=1.6`.

    > [AZURE.NOTE] Debe sustituir mytenantdomain con el nombre de dominio de su propio directorio de Azure AD.

4. En el campo directamente debajo del menú desplegable de la entrada, escriba lo siguiente:

    ```
Host: graph.windows.net
Authorization: your access token
Content-Type: application/json
```

    > [AZURE.NOTE] Sustituir su &lt;el token de acceso&gt; con el token de acceso para el directorio de Azure AD.

5. En el campo **solicitud cuerpo** , escriba lo siguiente:

    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
```

    Para obtener más información sobre la creación de grupos, vea [Crear grupo](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Para obtener más información sobre entidades de Azure AD y tipos que se exponen Graph e información acerca de las operaciones que se pueden realizar en ellas con el gráfico, vea [referencia de API de REST de Azure AD gráfico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Pasos siguientes

- Más información acerca de la [API de Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
- Obtenga más información sobre los [ámbitos de permiso de la API de Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)
