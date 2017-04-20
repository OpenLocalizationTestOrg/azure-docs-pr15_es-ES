<properties
    pageTitle="Azure Active B2C de directorio: Herramienta de auxiliar de personalización UI Page | Microsoft Azure"
    description="Herramienta auxiliar utilizada para demostrar la característica de personalización de la interfaz de usuario de página en Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/22/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-a-helper-tool-used-to-demonstrate-the-page-user-interface-ui-customization-feature"></a>Azure Active B2C de directorio: Herramienta de auxiliar utilizada para demostrar la característica de personalización de interfaz de usuario de página

En este artículo es un complemento para el [artículo de personalización de interfaz de usuario principal](active-directory-b2c-reference-ui-customization.md) de Azure Active Directory (AD Azure) B2C. Los pasos siguientes describen cómo ejercicio la característica de personalización de la interfaz de usuario de página con contenido HTML y CSS de ejemplo que hemos incluido.

## <a name="get-an-azure-ad-b2c-tenant"></a>Obtener a un inquilino de Azure AD B2C

Antes de que puede personalizar cualquier, debe [obtener a un inquilino de Azure AD B2C](active-directory-b2c-get-started.md) si aún no tiene una.

## <a name="create-a-sign-up-or-sign-in-policy"></a>Crear una directiva de inicio de sesión o inicio de sesión

El contenido de ejemplo que proporcionamos puede usarse para menú personalizar dos páginas en una [Directiva de inicio de sesión o inicio de sesión](active-directory-b2c-reference-policies.md): la [página de inicio de sesión unificada](active-directory-b2c-reference-ui-customization.md) y la [propia confirmado página atributos](active-directory-b2c-reference-ui-customization.md). Al [crear la directiva de inicio de sesión o inicio de sesión](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy), agregue la cuenta Local (dirección de correo electrónico), Facebook, Google y Microsoft como **proveedores de identidades**. Estos son los IDPs solo que acepte nuestro contenido HTML de ejemplo.  También puede agregar un subconjunto de estos IDPs si lo desea.

## <a name="register-an-application"></a>Registrar una aplicación

Debe [registrar una aplicación](active-directory-b2c-app-registration.md) de su inquilino B2C que se pueden usar para ejecutar la directiva. Después de registrar la aplicación, tiene varias opciones que puede usar para ejecutar la directiva de suscripción:

- Crear uno de los AD B2C de Azure aplicaciones de inicio rápido que aparece en la sección "Introducción" de [sesión arriba y los consumidores en las aplicaciones de inicio de sesión](active-directory-b2c-overview.md#getting-started).
- Use la aplicación de la [animación del Azure AD B2C](https://aadb2cplayground.azurewebsites.net) predefinida. Si decide usar la animación, debe registrar una aplicación en su inquilino de B2C con la **redirección URI** `https://aadb2cplayground.azurewebsites.net/`.
- Use el botón **Ejecutar ahora** en la directiva en el [portal de Azure](https://portal.azure.com/).

## <a name="customize-your-policy"></a>Personalizar la directiva

Para personalizar la apariencia de la directiva, debe crear archivos HTML y CSS usando las convenciones específicas de Azure AD B2C. A continuación, puede cargar el contenido estático a una ubicación disponible públicamente para que Azure AD B2C puede obtener acceso a él. Esto podría ser su propio servidor web dedicada, el almacenamiento de blobs de Windows Azure, red de entrega de contenido de Azure o cualquier otro estático recursos proveedor de hospedaje. Los requisitos sólo son que el contenido está disponible a través de HTTPS y se puede acceder usando CORS. Una vez que ha expuesto su contenido estático en la web, puede editar la directiva para que señale a esta ubicación y presentar contenido a sus clientes. El [artículo principal de la personalización de interfaz de usuario](active-directory-b2c-reference-ui-customization.md) se describe en detalle cómo funciona la característica de personalización de Azure AD B2C.

Para el propósito de este tutorial, ya hemos creado algunas contenido de ejemplo y alojados en el almacenamiento de blobs de Windows Azure. El contenido de muestra es una personalización básica en el tema de nuestra empresa ficticio, "Wingtip Toys". Para probar en su propia directiva, siga estos pasos:

1. Inicie sesión en su inquilino en el [portal de Azure](https://portal.azure.com/) y desplácese hasta el módulo de características B2C.
2. Haga clic en **directivas de inicio de sesión o inicio de sesión** y, a continuación, haga clic en la directiva (por ejemplo, "b2c\_1\_inicio de sesión\_arriba\_inicio de sesión\_en").
3. Haga clic en **personalización de la interfaz de usuario de página** y, a continuación, **página de inicio de sesión o inicio de sesión unificado**.
4. Activar o desactivar el modificador de **página personalizado de uso** en **Sí**. En el campo de **página personalizado URI** , escriba `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/unified.html`. Haga clic en **Aceptar**.
5. Haga clic en la **página de registro de cuenta Local**. Activar o desactivar el modificador de **utilizar una plantilla personalizada** en **Sí**. En el campo de **página personalizado URI** , escriba `https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/selfasserted.html`.
5. Repita el mismo paso de la **página de registro de cuenta Social**.
 Haga clic en **Aceptar** dos veces para cerrar los módulos de personalización de la interfaz de usuario.
6. Haga clic en **Guardar**.

Ahora puede probar la directiva personalizada. Puede usar su propia aplicación o la animación de Azure AD B2C si desea, pero también basta con hacer clic en el comando **Ejecutar ahora** en el módulo de directiva. Seleccione la aplicación en el cuadro de lista desplegable y elija la URI de redirección correspondiente. Haga clic en el botón **Ejecutar ahora** . Se abrirá una nueva pestaña del explorador y puede ejecutar a través de la experiencia del usuario de inicio de sesión de la aplicación con el contenido nuevo en su lugar.

## <a name="upload-the-sample-content-to-azure-blob-storage"></a>Cargar el contenido de ejemplo en el almacenamiento de blobs de Windows Azure

Si desea usar el almacenamiento de blobs de Windows Azure para hospedar el contenido de la página, puede crear su propia cuenta de almacenamiento y utilizar nuestra herramienta de auxiliar B2C para cargar los archivos.

### <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

1. Inicie sesión en el [portal de Azure](https://portal.azure.com/).
2. Haga clic en **+ nuevo** > **datos + almacenamiento** > **cuenta de almacenamiento**. Necesitará una suscripción de Azure para crear una cuenta de almacenamiento de blobs de Windows Azure. Puede registrarse una prueba gratuita en el [sitio Web de Azure](https://azure.microsoft.com/pricing/free-trial/).
3. Proporcione un **nombre** para la cuenta de almacenamiento (por ejemplo, "contoso") y elija las selecciones adecuadas para el **nivel de precios**, **grupo de recursos** y de **suscripción**. Asegúrese de que tiene la opción de **Pin para Startboard** activada. Haga clic en **crear**.
4. Vuelva a la Startboard y haga clic en la cuenta de almacenamiento que acaba de crear.
5. En la sección de **Resumen** , haga clic en **contenedores**y, a continuación, haga clic en **+ Add**.
6. Proporcione un **nombre** para el contenedor (por ejemplo, "b2c") y seleccione **Blob** como el **tipo de acceso**. Haga clic en **Aceptar**.
7. El contenedor que creó aparecerá en la lista en el módulo de **BLOB** . Anote la dirección URL del contenedor; Por ejemplo, debe ser similar al `https://contoso.blob.core.windows.net/b2c`. Cierre el módulo de **BLOB** .
8. En el módulo de cuenta de almacenamiento, haga clic en **las teclas** y anote los valores de los campos **Nombre de la cuenta de almacenamiento** y la **Clave principal de Access** .

1. Inicie sesión en el [portal de Azure](https://portal.azure.com/).
2. Haga clic en **+ nuevo** > **datos + almacenamiento** > **cuenta de almacenamiento**. Necesitará una suscripción de Azure para crear una cuenta de almacenamiento de blobs de Windows Azure. Puede registrarse una prueba gratuita en el [sitio Web de Azure](https://azure.microsoft.com/pricing/free-trial/).
3. Seleccione el **Almacenamiento de blobs** en **Tipo de cuenta**y deje los otros valores como predeterminado.  Puede editar el grupo de recursos y la ubicación si lo desea.  Haga clic en **crear**.
4. Vuelva a la Startboard y haga clic en la cuenta de almacenamiento que acaba de crear.
5. En la sección de **Resumen** , haga clic en **+ contenedor**.
6. Proporcione un **nombre** para el contenedor (por ejemplo, "b2c") y seleccione **Blob** como el **tipo de acceso**. Haga clic en **Aceptar**.
7. Abra el contenedor de **Propiedades**y anote la dirección URL del contenedor; Por ejemplo, debe ser similar al `https://contoso.blob.core.windows.net/b2c`. Cierre el módulo contenedor.
8. En el módulo de cuenta de almacenamiento, haga clic en el **Icono de clave** y anote los valores de los campos **Nombre de la cuenta de almacenamiento** y la **Clave principal de Access** .

> [AZURE.NOTE]
    **Tecla de acceso principal** es una credencial de seguridad importantes.

### <a name="download-the-helper-tool-and-sample-files"></a>Descargar los archivos de herramienta y muestra auxiliar

Puede descargar los [archivos de herramienta y muestra de auxiliar de almacenamiento de blobs de Windows Azure como un archivo .zip](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) o clonar desde GitHub:

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Este repositorio contiene un `sample_templates\wingtip` directorio, que contiene el ejemplo HTML, CSS y las imágenes. Estas plantillas hacer referencia a su cuenta de almacenamiento de blobs de Windows Azure, debe modificar los archivos HTML. Abrir `unified.html` y `selfasserted.html` y reemplazar todas las instancias de `https://localhost` con la dirección URL de su propio contenedor que anotó en los pasos anteriores. Debe usar la ruta de acceso absoluta de los archivos HTML porque en este caso, el código HTML se servirá por Azure AD, en el dominio `https://login.microsoftonline.com`.

### <a name="upload-the-sample-files"></a>Cargue los archivos de ejemplo

En el mismo repositorio, descomprima `B2CAzureStorageClient.zip` y ejecute la `B2CAzureStorageClient.exe` dentro de archivos. Este programa simplemente cargar todos los archivos en el directorio que especifique a su cuenta de almacenamiento y habilitar el acceso CORS para los archivos. Si ha seguido los pasos anteriores, los archivos HTML y CSS ahora apuntar a su cuenta de almacenamiento. Observe que el nombre de su cuenta de almacenamiento es el elemento que precede a `blob.core.windows.net`; Por ejemplo, `contoso`. Puede comprobar que el contenido se ha cargado correctamente al intentar obtener acceso a `https://{storage-account-name}.blob.core.windows.net/{container-name}/wingtip/unified.html` en un explorador. También puede usar [http://test-cors.org/](http://test-cors.org/) para asegurarse de que el contenido es ahora CORS habilitado. (Busque "estado XHR: 200" en el resultado.)

### <a name="customize-your-policy-again"></a>Personalizar la directiva de nuevo

Ahora que ha cargado el contenido de muestra en su cuenta de almacenamiento, debe modificar la directiva de suscripción para hacer referencia a él. Repita los pasos de la sección ["Personalizar la directiva"](#customize-your-policy) anterior, esta vez con direcciones URL de la cuenta de su propio almacenamiento. Por ejemplo, la ubicación de su `unified.html` archivo sería `<url-of-your-container>/wingtip/unified.html`.

Ahora puede usar el botón **Ejecutar ahora** o en su propia aplicación para volver a ejecutar la directiva. El resultado debe casi exactamente el mismo aspecto, que utiliza el mismo ejemplo HTML y CSS en ambos casos. Sin embargo, las directivas ahora se hace referencia a su propia instancia de almacenamiento de blobs de Windows Azure y es libre para editar y cargar los archivos a medida que tenga. Para obtener más información sobre cómo personalizar el código HTML y CSS, consulte el [artículo de personalización de interfaz de usuario principal](active-directory-b2c-reference-ui-customization.md).
