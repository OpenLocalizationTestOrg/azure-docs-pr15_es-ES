<properties 
    pageTitle="Directivas de administración de la API de Azure | Microsoft Azure" 
    description="Aprenda a crear, editar y configurar las directivas de administración de la API." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>


#<a name="policies-in-azure-api-management"></a>Directivas de administración de la API de Azure

En administración de la API de Azure, las directivas son una capacidad eficaz del sistema que permiten la publisher cambiar el comportamiento de la API a través de la configuración. Las directivas son un conjunto de instrucciones que se ejecutan secuencialmente en la solicitud o respuesta de una API. Instrucciones populares incluyen conversiones del formato XML a JSON y limitación para restringir la cantidad de llamadas entrantes de un desarrollador de velocidad de llamadas. Muchas más directivas están disponibles fuera del cuadro.

Vea la [Referencia de la directiva][] para una lista completa de instrucciones de directiva y su configuración.

Las directivas se aplican dentro de la puerta de enlace que se encuentra entre los consumidores de API y la API administrada. La puerta de enlace recibe todas las solicitudes y reenvía normalmente inalterado a la API subyacente. Sin embargo una directiva puede aplicar cambios a la solicitud entrante y la respuesta saliente.

Las expresiones de directiva se pueden utilizar como valores de atributo o valores de texto en cualquiera de las directivas de administración de la API, a menos que la directiva especifica lo contrario. Algunas directivas, como las directivas de [flujo de Control][] y [establezca variable][] se basan en las expresiones de directiva. Para obtener más información, vea [expresiones de directiva][]y [Directivas avanzadas][] .

## <a name="scopes"> </a>Cómo configurar directivas
Se pueden configurar directivas globalmente o en el ámbito de un [producto][], la [API][] o la [operación][]. Para configurar una directiva, desplácese hasta el editor de directivas en el portal de publisher.

![Menú directivas][policies-menu]

El editor de directivas consta de tres secciones principales: el ámbito de la directiva (arriba), la definición de directiva donde se modifican las directivas de (izquierda) y las instrucciones de la lista (derecha):

![Editor de directivas][policies-editor]

Para empezar a configurar una directiva primero debe seleccionar el ámbito en el que se debe aplicar la directiva. En la captura de pantalla debajo de la **Starter** está seleccionado el producto. Observe que el símbolo cuadrado junto al nombre de la directiva indica que ya se ha aplicado una directiva en este nivel.

![Ámbito][policies-scope]

Puesto que ya se ha aplicado una directiva, se muestra la configuración de la vista de definición.

![Configurar][policies-configure]

Se muestra la directiva de solo lectura en primer lugar. Para editar la definición, haga clic en la acción de **Configuración de la directiva** .

![Editar][policies-edit]

La definición de directiva es un documento XML simple que describe una secuencia de instrucciones entrantes y salientes. El código XML puede editarse directamente en la ventana de definición. Se proporciona una lista de instrucciones a la derecha e instrucciones aplicables al ámbito actual están habilitados y resaltados; como se muestra en la instrucción de la **Tasa de límite de llamadas** en la captura de pantalla anterior.

Haga clic en una instrucción habilitada agregará el XML adecuado en la ubicación del cursor en la vista de definición. 

>[AZURE.NOTE] Si no está habilitada la directiva que desea agregar, asegúrese de que está en el ámbito correcto para esa directiva. Cada declaración de directiva está diseñada para su uso en determinadas secciones de directiva y ámbitos. Para revisar las secciones de directiva y los ámbitos de una directiva, consulte la sección **uso** de esa directiva en la [Directiva de referencia][].

Una lista completa de instrucciones de directiva y su configuración está disponibles en la [Referencia de la directiva][].

Por ejemplo, para agregar una nueva instrucción para restringir las solicitudes entrantes a direcciones IP especificadas, coloque el cursor justo dentro del contenido de la `inbound` elemento XML y haga clic en la instrucción de **llamada de restringir direcciones IP** .

![Directivas de restricción][policies-restrict]

Esto agregará un fragmento de código XML para la `inbound` elemento que proporciona instrucciones sobre cómo configurar la declaración.

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address"/>
    </ip-filter>

Para limitar las solicitudes entrantes y Aceptar que sólo aquellos desde una dirección IP 1.2.3.4 modificar el XML como sigue:

    <ip-filter action="allow">
        <address>1.2.3.4</address>
    </ip-filter>

![Guardar][policies-save]

Cuando complete la configuración de las instrucciones para la directiva, haga clic en **Guardar** y los cambios se propagarán a la puerta de enlace API administración inmediatamente.

##<a name="sections"> </a>Configuración de la directiva de descripción

Una directiva es una serie de instrucciones que se ejecutan en el orden de una solicitud y una respuesta. La configuración se divide adecuadamente `inbound`, `backend`, `outbound`, y `on-error` secciones como se muestra en la siguiente configuración.

    <policies>
      <inbound>
        <!-- statements to be applied to the request go here -->
      </inbound>
      <backend>
        <!-- statements to be applied before the request is forwarded to 
             the backend service go here -->
      </backend>
      <outbound>
        <!-- statements to be applied to the response go here -->
      </outbound>
      <on-error>
        <!-- statements to be applied if there is an error condition go here -->
      </on-error>
    </policies> 

Si se produce un error durante el procesamiento de una solicitud, los restantes pasos de la `inbound`, `backend`, o `outbound` secciones se omiten y ejecución salta a las instrucciones de la `on-error` sección. Colocando las instrucciones de directiva en la `on-error` sección puede revisar el error mediante la `context.LastError` propiedad, inspeccionar y personalizar la respuesta de error utilizando la `set-body` directiva y configurar ¿qué ocurre si se produce un error. Existen códigos de error para conocer los pasos integrados y errores que pueden producirse durante el procesamiento de instrucciones de la directiva. Para obtener más información, vea [control de errores en las directivas de administración de la API](https://msdn.microsoft.com/library/azure/mt629506.aspx).

Puesto que se pueden especificar directivas en distintos niveles (global, producto, api y operación) la configuración proporciona una manera para especificar el orden en que las instrucciones de la definición directiva ejecutar con respecto a la directiva principal. 

Ámbitos de directiva se evalúan en el orden siguiente.

1. Ámbito global
2. Ámbito del producto
3. Ámbito de la API
4. Ámbito de la operación

Las instrucciones en ellos se evalúan según la ubicación de la `base` elemento, si está presente.

Por ejemplo, si tiene una directiva en el nivel global y una directiva configurada para una API, a continuación, siempre que se utilice esa API determinada ambas directivas se aplicará. Administración de la API permite para pedir determinista de instrucciones de directiva combinada a través del elemento base. 

    <policies>
        <inbound>
            <cross-domain />
            <base />
            <find-and-replace from="xyz" to="abc" />
        </inbound>
    </policies>

En la definición de la directiva de ejemplo anterior, la `cross-domain` instrucción podría ejecutar antes de que se seguido de las directivas de mayor en activar, la `find-and-replace` directiva.

Si la misma directiva aparece dos veces en la declaración de directiva, se aplica la directiva evaluada más recientemente. Puede utilizarla para reemplazar las directivas que se definen en un ámbito superior. Para ver las directivas en el ámbito actual en el editor de directivas, haga clic en **volver a calcular la directiva efectiva para el ámbito seleccionado**.

No tenga en cuenta que directiva global directiva principal y el uso del `<base>` elemento en el no tiene ningún efecto. 

## <a name="next-steps"></a>Pasos siguientes

Consulte siguiendo el vídeo en las expresiones de directiva.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Referencia de directiva]: api-management-policy-reference.md
[Producto]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md#add-apis 
[Operación]: api-management-howto-add-operations.md

[Directivas avanzadas]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Flujo de control]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Establecer variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Expresiones de directiva]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-menu]: ./media/api-management-howto-policies/api-management-policies-menu.png
[policies-editor]: ./media/api-management-howto-policies/api-management-policies-editor.png
[policies-scope]: ./media/api-management-howto-policies/api-management-policies-scope.png
[policies-configure]: ./media/api-management-howto-policies/api-management-policies-configure.png
[policies-edit]: ./media/api-management-howto-policies/api-management-policies-edit.png
[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
[policies-save]: ./media/api-management-howto-policies/api-management-policies-save.png
