<properties
    pageTitle="Reemplazar el comportamiento HTTP predeterminado en CDN de Azure con el motor de reglas | Microsoft Azure"
    description="El motor de reglas le permite personalizar cómo se administran las solicitudes HTTP por CDN de Azure, como bloquear el envío de determinados tipos de contenido, definir una directiva de almacenamiento en caché y modificar los encabezados HTTP."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>

# <a name="override-default-http-behavior-using-the-rules-engine"></a>Reemplazar el comportamiento HTTP predeterminado mediante el motor de reglas

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Información general

El motor de reglas le permite personalizar cómo se administran las solicitudes HTTP, por ejemplo, bloquear el envío de determinados tipos de contenido, definir una directiva de almacenamiento en caché y modificar encabezados HTTP.  Este tutorial mostrará cómo crear una regla que cambiará el comportamiento de la caché de activos CDN.  También hay contenido de vídeo en la sección "[Vea también](#see-also)".

## <a name="tutorial"></a>Tutorial

1. En el módulo de perfil CDN, haga clic en el botón **Administrar** .

    ![Módulo de perfil CDN botón Administrar](./media/cdn-rules-engine/cdn-manage-btn.png)

    Se abre el portal de administración de CDN.

2. Haga clic en la pestaña **HTTP grandes** , seguida de **Motor de reglas**.

    Se muestran las opciones para una nueva regla.

    ![Nuevas opciones de regla CDN](./media/cdn-rules-engine/cdn-new-rule.png)

    >[AZURE.IMPORTANT] El orden en que se enumeran varias reglas afecta a cómo se gestionan. Una regla subsiguientes puede reemplazar las acciones especificadas por una regla anterior.
    
3. Escriba un nombre en el **nombre / descripción** cuadro de texto.

4. Identificar el tipo de solicitudes que se aplicará la regla.  La condición de coincidencia **siempre** está activada de forma predeterminada.  Deberá usar **siempre** este tutorial, deje que ha seleccionado.

    ![Condición de coincidencia CDN](./media/cdn-rules-engine/cdn-request-type.png)

    >[AZURE.TIP] Hay muchos tipos de resultado de las condiciones disponibles en la lista desplegable.  Al hacer clic en el icono azul informativo a la izquierda de la condición de coincidencia explica la condición en detalle seleccionada actualmente.
    >
    >Para obtener la lista completa de condiciones de coincidencia en detalle, vea [reglas motor coincide con condición y detalles de la función](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_0).

5.  Haga clic en el **+** botón junto a las **características** para agregar una nueva característica.  En la lista desplegable de la izquierda, seleccione **Forzar interno Max-edad**.  En el cuadro de texto que aparece, escriba **300**.  Deje los demás valores predeterminados.

    ![Característica CDN](./media/cdn-rules-engine/cdn-new-feature.png)

    >[AZURE.NOTE] Como con las condiciones de coincidencia, haciendo clic en el icono azul informativo a la izquierda de la nueva característica mostrará detalles acerca de esta característica.  En el caso de **Fuerza interna Max-edad**, nos estamos reemplazar encabezados de **Control de caché** y **caduca** del activo al control cuando el nodo del borde CDN actualizará el activo desde el origen.  Nuestro ejemplo de 300 segundos significa que el nodo del borde CDN guardará caché activo durante 5 minutos antes de actualizar al activo desde su origen.
    >
    >Para obtener la lista completa de características de forma detallada, vea [condición de coincidencia de motor de reglas y detalles de la función](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_1).

6.  Haga clic en el botón **Agregar** para guardar la nueva regla.  La nueva regla ahora está esperando aprobación. Una vez que se ha aprobado, el estado cambiará de **XML pendiente** a **XML activo**.

    >[AZURE.IMPORTANT] Cambios de las reglas pueden tardar hasta 90 minutos en propagarse a través de la CDN.

## <a name="see-also"></a>Vea también
* [Viernes azure: nuevas y poderosas Premium características de Azure CDN](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (vídeo)
* [Condición de coincidencia de motor de reglas y detalles de la característica](https://msdn.microsoft.com/library/mt757336.aspx)
