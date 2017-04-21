<properties
    pageTitle="¿Qué es el Administrador de tráfico | Microsoft Azure"
    description="En este artículo le ayudará a comprender qué es el Administrador de tráfico, y si se trata de la opción de enrutamiento de tráfico adecuada para su aplicación"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="overview-of-traffic-manager"></a>Información general del Administrador de tráfico

Administrador de tráfico de Microsoft Azure le permite controlar la distribución de tráfico de usuario para los extremos de servicio en los centros de datos distintos. Extremos de servicio compatibles con el Administrador de tráfico incluyen máquinas virtuales de Azure, aplicaciones Web y servicios de nube. También puede usar el Administrador de tráfico con extremos externos, no Azure.

Administrador de tráfico usa el sistema de nombres de dominio (DNS) para dirigir las solicitudes de cliente al extremo más adecuada basándose en un [método de enrutamiento de tráfico](traffic-manager-routing-methods.md) y la salud de los extremos. Administrador de tráfico proporciona una variedad de métodos de enrutamiento de tráfico según las necesidades de otra aplicación, extremo del estado [de supervisión](traffic-manager-monitoring.md)y migración tras error automática. Administrador de tráfico es y son resistente a error, incluido el error de un área de Azure todo.

## <a name="traffic-manager-benefits"></a>Ventajas del Administrador de tráfico

Administrador de tráfico puede ayudarle:

- **Mejorar la disponibilidad de aplicaciones críticas**

    Administrador de tráfico ofrece alta disponibilidad de las aplicaciones de los extremos de supervisión y proporcionando migración tras error automática cuando un extremo deja de funcionar.

- **Mejorar la capacidad de respuesta para las aplicaciones de alto rendimiento**

    Azure le permite iniciar servicios en la nube o sitios Web en todo el mundo los centros de datos. Administrador de tráfico mejora la capacidad de respuesta de la aplicación dirigiendo el tráfico al extremo con la latencia de red más baja para el cliente.

- **Realizar el mantenimiento de servicio sin el tiempo de inactividad**

    Puede realizar operaciones de mantenimiento planeado en sus aplicaciones sin el tiempo de inactividad. Administrador de tráfico dirige el tráfico a extremos alternativos mientras el mantenimiento está en curso.

- **Combinar en local y aplicaciones basadas en la nube**

    Administrador de tráfico admite externo, en la nube y local implementaciones, incluida la "ráfaga-al-nube," "migrar-to-cloud," y "migración tras error a la nube" escenarios de extremos de Azure no es lo que se pueden utilizar con híbrido.

- **Distribuir el tráfico para implementaciones complejas, grandes**

    Con [los perfiles de administrador de tráfico anidados](traffic-manager-nested-profiles.md), métodos de enrutamiento de tráfico pueden combinarse para crear reglas sofisticadas y flexibles para satisfacer las necesidades de implementaciones más grandes y complejas.

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre el [funcionamiento del Administrador de tráfico](traffic-manager-how-traffic-manager-works.md).

- Obtenga información sobre cómo desarrollar aplicaciones de alta disponibilidad mediante la [supervisión de extremo de administrador de tráfico](traffic-manager-monitoring.md).

- Más información sobre los [métodos de enrutamiento de tráfico](traffic-manager-routing-methods.md) admitidos por el Administrador de tráfico.

- [Crear un perfil de administrador de tráfico](traffic-manager-manage-profiles.md).

