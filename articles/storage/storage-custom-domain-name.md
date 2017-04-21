<properties
    pageTitle="Configurar un nombre de dominio para el extremo de almacenamiento de blobs | Microsoft Azure"
    description="Obtenga información sobre cómo asignar un dominio de usuario personalizada para el extremo de almacenamiento de blobs para una cuenta de almacenamiento de Azure en el Portal de clásico de Azure."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="configure-a-custom-domain-name-for-your-blob-storage-endpoint"></a>Configurar un nombre de dominio personalizado para el extremo de almacenamiento de blobs

## <a name="overview"></a>Información general

Puede configurar un dominio personalizado para obtener acceso a datos blob en su cuenta de almacenamiento de Azure. El extremo predeterminado para el almacenamiento de blobs es `<storage-account-name>.blob.core.windows.net`. Si asigna un dominio personalizado y un subdominio como **www.contoso.com** al extremo blob para su cuenta de almacenamiento y, a continuación, los usuarios puede también acceso a los datos blob en su cuenta de almacenamiento que utiliza ese dominio.

>[AZURE.IMPORTANT] Almacenamiento de Azure aún no admite HTTPS con dominios personalizados. Somos conscientes de que los clientes están interesados en esta característica y estará disponible en una versión futura.

Hay dos formas para que apunten a su dominio personalizado para el extremo de blobs de su cuenta de almacenamiento. La manera más sencilla es crear un registro CNAME asignación de su dominio personalizado y un subdominio al extremo blob. Un registro CNAME es una característica DNS que asigna un dominio de origen a un dominio de destino. En este caso, el dominio de origen es personalizado dominio y subdominio, observe que el subdominio siempre es necesario. El dominio de destino es el extremo de servicio de blobs de Windows.

El proceso de asignación de su dominio personalizado a su extremo blobs, sin embargo, podría un breve período de tiempo de inactividad del dominio mientras se va a registrar el dominio en el [Portal de Azure clásico](https://manage.windowsazure.com). Si el dominio personalizado admite actualmente una aplicación con un contrato de nivel de servicio (SLA) que no requiere que haya ningún tiempo de inactividad, puede usar el subdominio Azure **asverify** proporcionar un paso intermedio de registro para que los usuarios podrán obtener acceso a su dominio mientras el DNS asignación tiene lugar.

La siguiente tabla muestra las direcciones URL de ejemplo para obtener acceso a datos blob en una cuenta de almacenamiento denominada **mystorageaccount**. El dominio personalizado registrado para la cuenta de almacenamiento es **www.contoso.com**:

Tipo de recurso|Formatos de URL
---|---
Cuenta de almacenamiento|**Dirección URL predeterminada:** http://mystorageaccount.blob.core.windows.net<p>**Dirección URL del dominio personalizado:** http://www.contoso.com</td>
BLOB|**Dirección URL predeterminada:** http://mystorageaccount.blob.core.windows.net/mycontainer/myblob<p>**Dirección URL del dominio personalizado:** http://www.contoso.com/mycontainer/myblob
Contenedor raíz|**Dirección URL predeterminada:** http://mystorageaccount.blob.core.windows.net/myblob o http://mystorageaccount.blob.core.windows.net/$ raíz/myblob<p>**Dirección URL del dominio personalizado:** http://www.contoso.com/myblob o http://www.contoso.com/$ raíz/myblob

## <a name="register-a-custom-domain-for-your-storage-account"></a>Registrar un dominio personalizado para su cuenta de almacenamiento

Use este procedimiento para registrar su dominio personalizado si no tiene dudas sobre tienen el dominio no esté disponible brevemente a los usuarios, o si el dominio personalizado actualmente no hospeda una aplicación.

Si el dominio personalizado compatibles con una aplicación que no puede tener un tiempo de inactividad, utilice el procedimiento descrito en <a href="#register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain">registrar un dominio personalizado para su cuenta de almacenamiento mediante el subdominio intermediario asverify</a>.

Para configurar un nombre de dominio personalizado, debe crear un nuevo registro CNAME con el registrador de dominios. El registro CNAME especifica un alias para un nombre de dominio; en este caso asigna la dirección de su dominio personalizado para el extremo de almacenamiento de blobs de su cuenta de almacenamiento.

Cada registrador tiene un método similar, pero ligeramente diferente para especificar un registro CNAME, pero el concepto es el mismo. Tenga en cuenta que muchos paquetes de registro de dominio básico no ofrecen la configuración de DNS, por lo que es podrán que necesite actualizar el paquete de registro de dominio antes de crear el registro CNAME.

1.  En el [Portal de Azure clásica](https://manage.windowsazure.com), vaya a la pestaña de **almacenamiento** .

2.  En la ficha **almacenamiento** , haga clic en el nombre de la cuenta de almacenamiento para la que desea asignar el dominio personalizado.

3.  Haga clic en la ficha **Configurar** .

4.  En la parte inferior de la pantalla, haga clic en **Administrar dominio** para mostrar el cuadro de diálogo **Administrar dominio de personalizado** . En el texto en la parte superior del cuadro de diálogo, verá información sobre cómo crear el registro CNAME. Para este procedimiento, omitir el texto al que hace referencia al subdominio **asverify** .

5.  Inicie sesión en el sitio Web del registrador de su DNS y vaya a la página de administración de DNS. Es posible en una sección como **Nombre de dominio**, **DNS**o **Nombre de servidor de administración**.

6.  Busque la sección de administración de CNAME. Debe ir a una página de configuración avanzada y busque las palabras **CNAME**, **Alias**o **subdominios**.

7.  Crear un nuevo registro CNAME y proporcione un alias de subdominio, como **www** o **fotos**. A continuación, proporcione un nombre de host, que es el extremo de servicio Blob, en el formato **mystorageaccount.blob.core.windows.net** (donde **mystorageaccount** es el nombre de su cuenta de almacenamiento). El nombre de host para usar está disponible para usted en el texto del cuadro de diálogo **Administrar dominio de personalizado** .

8.  Después de haber creado el registro CNAME, vuelva al cuadro de diálogo **Administrar dominio personalizado** y escriba el nombre de dominio personalizado, incluidos el subdominio, en el campo **Nombre de dominio personalizado** . Por ejemplo, si su dominio es **contoso.com** y el subdominio es **www**, escriba **www.contoso.com**; Si su subdominio es **fotos**, escriba **photos.contoso.com**. Tenga en cuenta que es necesario, el subdominio.

9. Haga clic en el botón **registrar** para registrar su dominio personalizado.

    Si el registro se realiza correctamente, verá el mensaje de **su dominio personalizado está activo**. Los usuarios pueden ahora ver blob datos en su dominio personalizado, siempre y cuando tengan los permisos adecuados.

## <a name="register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain"></a>Registrar un dominio personalizado para su cuenta de almacenamiento mediante el subdominio intermediario asverify

Use este procedimiento para registrar personalizado dominio si el dominio personalizado compatibles con una aplicación con un SLA que requiere que no exista ningún tiempo de inactividad. Mediante la creación de un CNAME que señala desde asverify. &lt;subdominio&gt;. &lt;customdomain&gt; a asverify. &lt;storageaccount&gt;. blob.core.windows.net, puede registrar su dominio con Azure previamente. A continuación, puede crear un segundo CNAME que señala desde &lt;subdominio&gt;. &lt;customdomain&gt; a &lt;storageaccount&gt;. blob.core.windows.net, momento en que el tráfico a su dominio personalizado se dirige a su extremo blob.

El subdominio asverify es un subdominio especial reconocido en Azure. Anteponiendo **asverify** a su propio subdominio permitir Azure reconocer su dominio personalizado sin modificar el registro DNS del dominio. Si modifica el registro DNS del dominio, se asignarán al extremo blob sin tiempo de inactividad.

1.  En el [Portal de Azure clásica](https://manage.windowsazure.com), vaya a la pestaña de **almacenamiento** .

2.  En la ficha **almacenamiento** , haga clic en el nombre de la cuenta de almacenamiento para la que desea asignar el dominio personalizado.

3.  Haga clic en la ficha **Configurar** .

4.  En la parte inferior de la pantalla, haga clic en **Administrar dominio** para mostrar el cuadro de diálogo **Administrar dominio de personalizado** . En el texto en la parte superior del cuadro de diálogo, verá información sobre cómo crear el registro CNAME con el subdominio **asverify** .

5.  Inicie sesión en el sitio Web del registrador de su DNS y vaya a la página de administración de DNS. Es posible en una sección como **Nombre de dominio**, **DNS**o **Nombre de servidor de administración**.

6.  Busque la sección de administración de CNAME. Debe ir a una página de configuración avanzada y busque las palabras **CNAME**, **Alias**o **subdominios**.

7.  Crear un nuevo registro CNAME y proporcione un alias de subdominio que incluye el subdominio asverify. Por ejemplo, el subdominio que especifique estarán en el formato **asverify.www** o **asverify.photos**. A continuación, proporcione un nombre de host, que es el extremo de servicio Blob, en el formato **asverify.mystorageaccount.blob.core.windows.net** (donde **mystorageaccount** es el nombre de su cuenta de almacenamiento). El nombre de host para usar está disponible para usted en el texto del cuadro de diálogo **Administrar dominio de personalizado** .

8.  Después de haber creado el registro CNAME, vuelva al cuadro de diálogo **Administrar dominio personalizado** y escriba el nombre de dominio personalizado en el campo **Nombre de dominio personalizado** . Por ejemplo, si su dominio es **contoso.com** y el subdominio es **www**, escriba **www.contoso.com**; Si su subdominio es **fotos**, escriba **photos.contoso.com**. Tenga en cuenta que es necesario, el subdominio.

9.  Haga clic en la casilla de verificación que dice **Avanzadas: Use el subdominio 'asverify' para mi dominio personalizado realiza el registro previo**.

10. Haga clic en el botón **registrar** para su dominio personalizado realiza el registro previo.

    Si el preregistration se realiza correctamente, verá el mensaje de **su dominio personalizado está activo**.

11. En este momento, se ha comprobado su dominio personalizado haciendo Azure, pero el tráfico a su dominio aún no se enruta a su cuenta de almacenamiento. Para completar el proceso, vuelva al sitio Web del registrador de su DNS y crear otro registro CNAME que asigna el subdominio a su extremo de servicio de blobs de Windows. Por ejemplo, especifique el subdominio como **www** o **fotos**y el nombre de host como **mystorageaccount.blob.core.windows.net** (donde **mystorageaccount** es el nombre de su cuenta de almacenamiento). Con este paso, el registro del dominio personalizado es completo.

12. Por último, puede eliminar el registro CNAME creado usando **asverify**, tal y como era necesaria sólo como un paso intermedio.

Los usuarios pueden ahora ver blob datos en su dominio personalizado, siempre y cuando tengan los permisos adecuados.

## <a name="verify-that-the-custom-domain-references-your-blob-service-endpoint"></a>Compruebe que el dominio personalizado hace referencia el extremo de servicio de blobs

Para comprobar que su dominio personalizado en realidad se asigna a su extremo de servicio de blobs, cree un blob en un contenedor público dentro de su cuenta de almacenamiento. A continuación, en un explorador web, utilice un URI en el siguiente formato para tener acceso a los blobs:

-   http://<*subdomain.customdomain*>/<*mycontainer*>/<*myblob*>

Por ejemplo, puede usar el siguiente URI para tener acceso a un formulario web mediante un subdominio **photos.contoso.com** personalizado que se asigna a un blob en el contenedor de **myforms** :

-   http://Photos.contoso.com/myforms/ApplicationForm.htm

## <a name="unregister-a-custom-domain-from-your-storage-account"></a>Anular el registro de un dominio personalizado de su cuenta de almacenamiento

Para eliminar del registro un dominio personalizado, siga estos pasos: 

1. Inicie sesión en el [Portal de clásico de Azure](https://manage.windowsazure.com). 

2. En el panel de navegación, haga clic en **almacenamiento**. 

3. En la página de **almacenamiento** , haga clic en el nombre de la cuenta de almacenamiento para mostrar el panel. 

5. En la cinta de opciones, haga clic en **Manage Domain**. 

6. En el cuadro de diálogo **Administrar dominio de personalizada** , haga clic en **Anular registro**. 


## <a name="additional-resources"></a>Recursos adicionales

-   [Cómo asignar dominio personalizado al extremo de la red de entrega de contenido (CDN)](../cdn/cdn-map-content-to-custom-domain.md)
