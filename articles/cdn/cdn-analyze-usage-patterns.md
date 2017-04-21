<properties
    pageTitle="Analizar patrones de uso de Azure CDN | Microsoft Azure"
    description="Puede ver los patrones de uso de la CDN mediante los siguientes informes: ancho de banda, transferir datos, visitas, Estados de caché, proporción de visitas de caché, transferir datos de IPV4 o IPV6."
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

# <a name="analyze-azure-cdn-usage-patterns"></a>Analizar patrones de uso de CDN de Azure

[AZURE.INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Puede ver los patrones de uso para su CDN mediante los siguientes informes:

- Ancho de banda
- Datos que se transfieren
- Visitas
- Estados de caché
- Tasa de visitas de caché
- Datos de IPv4 o IPV6 transferidos

## <a name="accessing-advanced-http-reports"></a>Acceso a informes HTTP avanzados

1. En el módulo de perfil CDN, haga clic en el botón **Administrar** .

    ![Módulo de perfil CDN botón Administrar](./media/cdn-reports/cdn-manage-btn.png)

    Se abre el portal de administración de CDN.

2. Desplace el puntero sobre la pestaña **análisis** , a continuación, desplace el puntero sobre el flotante **Informes principales** .  Haga clic en el informe que desee en el menú.

    ![Portal de administración de CDN - menú informes principales](./media/cdn-reports/cdn-core-reports.png)


## <a name="bandwidth"></a>Ancho de banda

El informe de ancho de banda consta de una tabla de datos y el gráfico que indica el uso de ancho de banda para HTTP y HTTPS durante un período de tiempo determinado. Puede ver el uso de ancho de banda a través de POP determinado o todos los CDN POP. Esto le permite ver los picos de tráfico y la distribución de CDN POP en Mbps.

- Seleccionar todos los nodos de borde para ver el tráfico de todos los nodos o elija un región específico o nodo de la lista desplegable.
- Seleccione el intervalo de fechas para ver datos de hoy o esta semana o este mes, etc. o especifique fechas personalizadas y luego haga clic en "Ir" para asegurarse de que se actualice la selección.
- Puede exportar y descargar los datos haciendo clic en el icono de la hoja de excel situado junto a "Ir".

El informe se actualiza cada 5 minutos.

![Informe de ancho de banda](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Datos que se transfieren

Este informe se compone de una tabla de datos y el gráfico que indica el uso de tráfico de HTTP y HTTPS durante un período de tiempo determinado. Puede ver el uso de tráfico a través de POP determinado o todos los CDN POP. Esto le permite ver los picos de tráfico y la distribución de CDN POP en GB.

- Seleccionar todos los nodos de borde para ver el tráfico de todas las notas o elija un región específico o nodo de la lista desplegable.
- Seleccione el intervalo de fechas para ver datos de hoy o esta semana o este mes, etc. o especifique fechas personalizadas y luego haga clic en "Ir" para asegurarse de que se actualice la selección.
- Puede exportar y descargar los datos haciendo clic en el icono de la hoja de excel situado junto a "Ir".

El informe se actualiza cada 5 minutos.

![Informe de transferir datos](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Visitas (códigos de estado)

Este informe describe la distribución de códigos de estado de solicitud para el contenido. Todas las solicitudes de contenido, generarán un código de estado HTTP. El código de estado describe cómo borde POP controla la solicitud. Por ejemplo, los códigos de estado de 2xx indican que la solicitud se ha servido correctamente a un cliente mientras se produjo un error que indica un código de estado 4xx. Para obtener más detalles sobre el código de estado HTTP, consulte [códigos de estado](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

- Seleccione el intervalo de fechas para ver datos de hoy o esta semana o este mes, etc. o especifique fechas personalizadas y luego haga clic en "Ir" para asegurarse de que se actualice la selección.
- Puede exportar y descargar los datos haciendo clic en la hoja de excel que se encuentra junto a "Ir".

![Informe visitas](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Estados de caché

Este informe describe la distribución de caché y de caché de solicitud de cliente. Dado que el mejor rendimiento procede de caché, puede optimizar la velocidad de entrega datos por minimizar errores de caché y expiración de la caché. Errores de caché se pueden reducir mediante la configuración de su servidor de origen para evitar la asignación de encabezados de respuesta "no caché", evitando la cadena de consulta caché excepto donde se necesita estrictamente y evitando códigos de respuesta no caché. Caché expirados visitas se pueden evitar que un activo está max edad tanto como sea posible para reducir el número de solicitudes al servidor de origen.

![Informe de Estados de caché](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Estados de caché principal incluyen:

- TCP_HIT: Servir de borde. El objeto en caché y no había superado su antigüedad de max.
- TCP_MISS: Servir de origen. El objeto no estaba en caché y la respuesta fue al origen.
- TCP_EXPIRED _MISS: servida de origen después de validación con el origen. El objeto en caché pero había excedido su antigüedad de max. Se ha generado una validación con origen en el objeto de caché quedado reemplazado por una nueva respuesta de origen.
- TCP_EXPIRED _HIT: servida desde borde después de validación con el origen. El objeto en caché pero había excedido su antigüedad de max. Se ha generado una validación con el servidor de origen en el objeto de caché que se ha modificado.

- Seleccione el intervalo de fechas para ver datos de hoy o esta semana o este mes, etc. o especifique fechas personalizadas y luego haga clic en "Ir" para asegurarse de que se actualice la selección.
- Puede exportar y descargar los datos haciendo clic en el icono de la hoja de excel situado junto a "Ir".

### <a name="full-list-of-cache-statuses"></a>Lista completa de los Estados de caché

- TCP_HIT - se notifica este estado cuando una solicitud se sirve directamente desde el POP al cliente. Un activo inmediatamente se sirve desde un POP cuando se almacena en caché en el POP más cercano al cliente y tiene una válida time to live o TTL. TTL depende de los encabezados de respuesta siguientes:

    - Control de caché: s-maxage
    - Control de caché: max-edad
    - Caduca

- TCP_MISS - este estado indica que no se encontró una versión en caché del activo solicitado en POP más cercano al cliente. Se solicitará activo de un servidor de origen o de un servidor de la pantalla de origen. Si el servidor de origen o el servidor de protección de origen devuelve un activo, se entregan al cliente y caché en el cliente y el servidor perimetral. En caso contrario, un código de estado no es 200 (por ejemplo, 403 Prohibido, 404 no encontrado, etc.) se devolverán.

- TCP_EXPIRED _HIT - este estado se indica cuando una solicitud de que un activo con un TTL caducado, por ejemplo, cuando ha expirado edad max del activo, el destino se sirve directamente desde el POP al cliente.

    Una solicitud de expirada normalmente el resultado de una solicitud de renovación con el servidor de origen. Fin de un _HIT TCP_EXPIRED que se produzca, el servidor de origen debe indicar que no existe una versión más reciente del activo. Este tipo de situación normalmente actualizará los encabezados de caduca y Control de caché de ese activo.

- TCP_EXPIRED _MISS - se notifica este estado cuando una versión más reciente de un activo en caché expirado se sirve desde el POP al cliente. Esto ocurre cuando ha caducado el período de vida de un activo en caché (por ejemplo, caducado edad max) y el servidor de origen devuelve una versión más reciente de ese activo. Esta nueva versión del activo se servirá para el cliente en lugar de la versión en caché. Además, se se almacenarán en caché en el servidor perimetral y el cliente.

- CONFIG_NOCACHE - este estado indica que una configuración específica del cliente en nuestra borde POP evita el activo en la caché.

- Ninguno: de este estado indica que no se ha realizado una comprobación de actualización del contenido de la memoria caché.

- TCP_ CLIENT_REFRESH _MISS - este estado se indica cuando un cliente HTTP (por ejemplo, explorador) fuerza un borde POP para recuperar una versión nueva de un activo obsoleto desde el servidor de origen.

    De forma predeterminada, nuestros servidores impiden que a un cliente HTTP forzar nuestros servidores perimetrales para recuperar una versión nueva del activo desde el servidor de origen.

- TCP_ PARTIAL_HIT - se notifica este estado cuando una solicitud de intervalo de bytes como resultado un posicionamiento de un activo parcialmente en caché. El intervalo de bytes solicitado inmediatamente sirve de POP para el cliente.

- ALMACENABLE - se notifica este estado cuando los encabezados de Control de caché y caduca de un activo indican que no se debe guardar en un POP o el cliente de HTTP. Estos tipos de solicitudes atendidos desde el servidor de origen

## <a name="cache-hit-ratio"></a>Tasa de visitas de caché

Este informe indica el porcentaje de solicitudes de caché que se sirve directamente desde la memoria caché.

El informe proporciona los siguientes detalles:

- El contenido solicitado estaba en caché en la más cercana a la mando POP.
- La solicitud se sirve directamente desde el borde de la red.
- La solicitud no requiere una validación con el servidor de origen.

El informe no incluye:

- Solicita que se denegado por país opciones de filtrado.
- Solicitudes de activos cuyos encabezados indican que no deberían caché. Por ejemplo, Control de la caché: privado, Control de la caché: no caché o Pragma: encabezados de caché no evitará un activo en la caché.
- Solicitudes de intervalo de bytes de contenido parcialmente en caché.

La fórmula es: (TCP_ posicionamiento / (TCP_ posicionamiento + TCP_MISS)) * 100

- Seleccione el intervalo de fechas para ver datos de hoy o esta semana o este mes, etc. o especifique fechas personalizadas y luego haga clic en "Ir" para asegurarse de que se actualice la selección.
- Puede exportar y descargar los datos haciendo clic en el icono de la hoja de excel situado junto a "Ir".


![Informe de relación de visitas de caché](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>Transferir datos de IPv4 o IPV6

Este informe muestra la distribución de uso de tráfico de IPV4 vs IPV6.

![Transferir datos de IPv4 o IPV6](./media/cdn-reports/cdn-ipv4-ipv6.png)

- Seleccione el intervalo de fechas para ver datos de hoy o esta semana o este mes, etc., o especifique fechas personalizadas.
- A continuación, haga clic en "Ir" para asegurarse de que se actualice la selección.


## <a name="considerations"></a>Consideraciones

Solo se pueden generar informes dentro de los últimos 18 meses.
