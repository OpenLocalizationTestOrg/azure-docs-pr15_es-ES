<properties
    pageTitle="Cargar previamente activos en un extremo de Azure CDN | Microsoft Azure"
    description="Obtenga información sobre cómo cargar previamente el contenido almacenado en un extremo CDN."
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

# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Cargar previamente activos en un extremo de CDN de Azure

[AZURE.INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

De forma predeterminada, los activos en primer lugar se almacenan en medida que se solicitan. Esto significa que la primera solicitud de cada región puede tardar más, ya que los servidores perimetrales no tendrá el contenido en caché y necesitará reenviar la solicitud al servidor de origen. Cargando previamente contenido evita esta primera latencia visita.

Además de proporcionar una mejor experiencia de cliente, Cargando previamente los activos en caché también puede reducir el tráfico de red en el servidor de origen.

> [AZURE.NOTE] Cargando previamente activos es útil para grandes eventos o contenido que esté disponible simultáneamente a un gran número de usuarios, como una nueva versión de película o una actualización de software.

Este tutorial le guiará por Cargando previamente el contenido de la caché en todos los nodos de borde de CDN de Azure.

## <a name="walkthrough"></a>Tutorial

1. En el [Portal de Azure](https://portal.azure.com), busque el perfil CDN que contiene el extremo que desee cargar previamente.  Se abre el módulo de perfil.

2. Haga clic en el punto final de la lista.  Se abre el módulo de extremo.

3. En el módulo de extremo CDN, haga clic en el botón cargar.

    ![Módulo de extremo CDN](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)

    Se abre el módulo de carga.

    ![Módulo de carga CDN](./media/cdn-preload-endpoint/cdn-load-blade.png)

4. Escriba la ruta completa de cada activo que desea cargar (por ejemplo, `/pictures/kitten.png`) en el cuadro de texto de la **ruta de acceso** .

    > [AZURE.TIP] Más cuadros de texto de **ruta de acceso** aparecerá después de escribir texto para que pueda crear una lista de varios activos.  Puede eliminar los activos de la lista haciendo clic en el botón de puntos suspensivos (...).
    >
    > Rutas de acceso deben ser una dirección URL relativa que se ajuste a la siguiente [expresión regular](https://msdn.microsoft.com/library/az24scfc.aspx): `^(?:\/[a-zA-Z0-9-_.\u0020]+)+$`.  Cada activo debe tener su propia ruta de acceso.  No hay ninguna funcionalidad de caracteres comodín para cargar previamente activos.

    ![Botón Cargar](./media/cdn-preload-endpoint/cdn-load-paths.png)

5. Haga clic en el botón **cargar** .

    ![Botón Cargar](./media/cdn-preload-endpoint/cdn-load-button.png)

> [AZURE.NOTE] Hay un límite de 10 carga solicitudes por minuto por perfil CDN.

## <a name="see-also"></a>Vea también
- [Purgar un extremo CDN de Azure](cdn-purge-endpoint.md)
- [Referencia de la API de REST de CDN Azure - purgar o cargar previamente un punto final](https://msdn.microsoft.com/library/mt634451.aspx)
