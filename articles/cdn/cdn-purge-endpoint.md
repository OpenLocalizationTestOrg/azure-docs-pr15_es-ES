<properties
    pageTitle="Purgar un extremo CDN de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo purgar todo el contenido en caché de un extremo CDN."
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

# <a name="purge-an-azure-cdn-endpoint"></a>Purgar un extremo CDN de Azure

## <a name="overview"></a>Información general

Los nodos de borde de Azure CDN caché activos hasta que expira time to live (TTL) del activo.  Después de que caduque TTL del activo, cuando un cliente solicita el activo desde el nodo del borde, el nodo del borde recupera una nueva copia actualizada del activo para atender la solicitud de cliente y almacén de actualizar la memoria caché.

A veces puede purgar el contenido de la caché de todos los nodos de borde y forzar todos ellos para recuperar nuevos activos actualizados.  Esto puede deberse a las actualizaciones a la aplicación web o a rápidamente los activos de actualización que contienen información incorrecta.

> [AZURE.TIP] Tenga en cuenta que solo Purgar borra el contenido de la caché en los servidores perimetrales CDN.  Cualquier caché descendentes, como servidores proxy y caché del explorador local, todavía pueden retener una copia del archivo en caché.  Es importante recordar esto cuando se establece un archivo time to live.  Puede hacer que un cliente descendente para solicitar la última versión del archivo asigne un nombre único cada vez la actualización o aprovechando de [almacenamiento en caché de cadena de consulta](cdn-query-string.md).  

Este tutorial le guiará a través de depuración de activos de todos los nodos de borde de un extremo.

## <a name="walkthrough"></a>Tutorial

1. En el [Portal de Azure](https://portal.azure.com), busque el perfil CDN que contiene el extremo que desee purgar.

2. En el módulo de perfil CDN, haga clic en el botón de purga.

    ![Módulo de perfil CDN](./media/cdn-purge-endpoint/cdn-profile-blade.png)

    Se abre el módulo de purga.

    ![Módulo de purga CDN](./media/cdn-purge-endpoint/cdn-purge-blade.png)

3. En el módulo de purga, seleccione la dirección que desee purgar en la lista desplegable dirección URL del servicio.

    ![Formulario de purga](./media/cdn-purge-endpoint/cdn-purge-form.png)

    > [AZURE.NOTE] También puede acceder a la hoja de purga haciendo clic en el botón **Purgar** en el módulo de extremo CDN.  En ese caso, el campo **dirección URL** se rellenará previamente con la dirección del servicio de ese extremo específico.

4. Seleccione los activos que desee purgar desde los nodos de borde.  Si desea borrar todos los activos, haga clic en la casilla de verificación **Purgar todo** .  En caso contrario, escriba la ruta completa de cada activo que desee purgar (por ejemplo, `/pictures/kitten.png`) en el cuadro de texto de la **ruta de acceso** .

    > [AZURE.TIP] Más cuadros de texto de **ruta de acceso** aparecerá después de escribir texto para que pueda crear una lista de varios activos.  Puede eliminar los activos de la lista haciendo clic en el botón de puntos suspensivos (...).
    >
    > Rutas de acceso deben ser una dirección URL relativa que se ajustan a la siguiente [expresión regular](https://msdn.microsoft.com/library/az24scfc.aspx): `^\/(?:[a-zA-Z0-9-_.\u0020]+\/)*\*$";`.  Para **Azure CDN de Verizon** (Standard y Premium), asterisco (\*) se puede utilizar como un carácter comodín (por ejemplo, `/music/*`).  No se permiten caracteres comodín y **Purgar todo** con **Azure CDN de Akamai**.
    
5. Haga clic en el botón **Purgar** .

    ![Botón Depurar](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [AZURE.IMPORTANT] Las solicitudes de purga tardar aproximadamente 2 a 3 minutos para procesar con **Azure CDN de Verizon** (Standard y Premium) y aproximadamente 7 minutos con **Azure CDN de Akamai**.  CDN Azure tiene un límite de 50 simultáneas purgar solicitudes en cualquier momento. 

## <a name="see-also"></a>Vea también
- [Cargar previamente activos en un extremo de CDN de Azure](cdn-preload-endpoint.md)
- [Referencia de la API de REST de CDN Azure - purgar o cargar previamente un punto final](https://msdn.microsoft.com/library/mt634451.aspx)
