<properties
    pageTitle="Controlar Azure CDN Premium desde Verizon comportamiento de las solicitudes de cadenas de consulta de almacenamiento en caché | Microsoft Azure"
    description="Cadena de consulta CDN Azure almacenamiento en caché controles cómo son los archivos en caché que contengan cadenas de consulta."
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

#<a name="controlling-caching-behavior-of-cdn-requests-with-query-strings---premium"></a>Controlar el comportamiento de la caché de solicitudes CDN cadenas de consulta - Premium

> [AZURE.SELECTOR]
- [Estándar](cdn-query-string.md)
- [CDN Azure Premium desde Verizon](cdn-query-string-premium.md)

##<a name="overview"></a>Información general

Los controles de almacenamiento en caché de cadena de consulta cómo son los archivos en caché que contengan cadenas de consulta.

> [AZURE.IMPORTANT] Los productos estándar y Premium CDN proporcionan la misma cadena de consulta funcionalidad de caché, pero difiere de la interfaz de usuario.  Este documento describe la interfaz de **Azure CDN Premium de Verizon**.  Cadena de consulta almacenamiento en caché con ** **Azure CDN de Akamai Azure CDN estándar**y de Verizon** , vea [comportamiento de almacenamiento en caché de control de solicitudes CDN cadenas de consulta](cdn-query-string.md).

Existen tres modos:

- **caché de estándar**: este es el modo predeterminado.  El nodo del borde CDN pasará la cadena de consulta del solicitante al origen en la primera solicitud y la caché del activo.  Todas las solicitudes subsiguientes para ese activo atendidas desde el nodo del borde omitirá la cadena de consulta hasta que expire el activo en caché.
- **caché sin**: en este modo, las solicitudes de cadenas de consulta no se en caché en el nodo del borde CDN.  El nodo del borde recupera activo directamente desde el origen y pasa al solicitante con cada solicitud.
- **caché única**: este modo trata cada solicitud con una cadena de consulta como un único activo con su propia memoria caché.  Por ejemplo, la respuesta desde el origen de una solicitud de *foo.ashx?q=bar* desea en caché en el nodo del borde y se devuelve para la caché subsiguientes con esa misma cadena de consulta.  Una solicitud de *foo.ashx?q=somethingelse* se podría caché como un activo independiente con su propia hora Live.

##<a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Cambiar la configuración de premium CDN perfiles de caché de cadena de consulta

1. En el módulo de perfil CDN, haga clic en el botón **Administrar** .

    ![Módulo de perfil CDN botón Administrar](./media/cdn-query-string-premium/cdn-manage-btn.png)

    Se abre el portal de administración de CDN.

2. Desplace el puntero sobre la pestaña **HTTP grandes** , a continuación, mantenga el mouse sobre el elemento de **Configuración de la caché** .  Haga clic en **almacenamiento en caché de cadena de consulta**.

    Se muestran las opciones de almacenamiento en caché de cadena de consulta.

    ![Cadena de consulta CDN opciones de caché](./media/cdn-query-string-premium/cdn-query-string.png)

3. Después de realizar la selección, haga clic en el botón **Actualizar** .


> [AZURE.IMPORTANT] Los cambios de configuración no pueden ser inmediatamente visibles, como el tiempo para el registro en propagarse a través de la CDN.  Perfiles de <b>Azure CDN de Verizon</b> , propagación normalmente se completará en 90 minutos, pero en algunos casos puede tardar más.
