<properties
    pageTitle="Solución de análisis de registro de seguimiento de cambios | Microsoft Azure"
    description="Puede usar la solución de seguimiento de cambios de configuración de análisis de registro para ayudarle a identificar fácilmente el software y los cambios de los servicios de Windows que se producen en su entorno: identificar estos cambios de configuración puede ayudarle a identificar problemas de funcionamiento."
    services="operations-management-suite"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="operations-management-suite"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="change-tracking-solution-in-log-analytics"></a>Cambiar la solución de seguimiento de análisis de registro


Puede usar la solución de seguimiento de cambios de configuración de análisis de registro para ayudarle a identificar fácilmente el software y los cambios de los servicios de Windows y los cambios de daemon de Linux que se producen en su entorno: identificar estos cambios de configuración puede ayudarle a identificar problemas de funcionamiento.

Instale la solución para actualizar el tipo de agente que ha instalado. Cambios para instalar software y servicios de Windows en los servidores supervisados se leen y, a continuación, los datos se envían al servicio de análisis de registro en la nube para procesamiento. Lógica se aplica a los datos recibidos y el servicio de nube registra los datos. Cuando se encuentran cambios, servidores con los cambios se muestran en el panel de control de cambios. Con la información en el panel de control de cambios, puede ver fácilmente los cambios realizados en la infraestructura de servidor.

## <a name="installing-and-configuring-the-solution"></a>Instalar y configurar la solución
Use la información siguiente para instalar y configurar la solución.

- Operations Manager se requiere para la solución de seguimiento de cambios.
- Debe tener un agente de Windows o Operations Manager en cada equipo donde desea supervisar los cambios.
- Agregar la solución de seguimiento de cambios en el área de trabajo OMS mediante el proceso descrito en [soluciones de agregar el registro de análisis de la Galería de soluciones](log-analytics-add-solutions.md).  No es necesaria realizar ninguna configuración adicional.


## <a name="change-tracking-data-collection-details"></a>Cambiar detalles de conjunto de datos de seguimiento

Seguimiento de cambios recopila el inventario de software y metadatos de servicio de Windows mediante los agentes que ha habilitado.

La siguiente tabla muestra los métodos de conjunto de datos y otros detalles acerca de cómo se recopilan los datos de seguimiento de cambios.

| plataforma | Agente directo | Agente SCOM | Almacenamiento de Azure | ¿SCOM obligatorio? | Datos de agente SCOM enviadas por grupo de administración | frecuencia de la colección |
|---|---|---|---|---|---|---|
|Windows|![Sí](./media/log-analytics-change-tracking/oms-bullet-green.png)|![Sí](./media/log-analytics-change-tracking/oms-bullet-green.png)|![No](./media/log-analytics-change-tracking/oms-bullet-red.png)|            ![No](./media/log-analytics-change-tracking/oms-bullet-red.png)|![Sí](./media/log-analytics-change-tracking/oms-bullet-green.png)| cada hora|

## <a name="use-change-tracking"></a>Usar el seguimiento de cambios

Después de instala la solución, puede ver el resumen de los cambios para los servidores supervisados mediante el mosaico de **Seguimiento de cambios** en la página de **información general** de OMS.

![imagen del icono de seguimiento de cambios](./media/log-analytics-change-tracking/oms-changetracking-tile.png)

Puede ver los cambios en la infraestructura y, a continuación, en ejercicio de los detalles de las siguientes categorías:

- Cambios de tipo de configuración de software y servicios de Windows
- Cambios de software para las aplicaciones y actualizaciones de los servidores individuales
- Número total de cambios de software para cada aplicación
- Cambios de servicio de Windows para servidores individuales

![imagen del panel de control de cambios](./media/log-analytics-change-tracking/oms-changetracking01.png)

![imagen del panel de control de cambios](./media/log-analytics-change-tracking/oms-changetracking02.png)

### <a name="to-view-changes-for-any-change-type"></a>Para ver los cambios en las cambiar tipo

1. En la página **Introducción** , haga clic en el icono de **Seguimiento de cambios** .
2. En el panel **Cambiar seguimiento** , revise la información de resumen en uno de los módulos de tipo de cambio y, a continuación, haga clic en para ver información detallada sobre él en la página de **búsqueda de registros** .
3. En cualquiera de las páginas de búsqueda de registro, puede ver los resultados de tiempo, resultados detallados y el historial de búsqueda de registro. También puede filtrar por facetas y limitar los resultados.

## <a name="next-steps"></a>Pasos siguientes

- Usar [búsquedas de registro de análisis de registro](log-analytics-log-searches.md) para ver los datos de seguimiento de cambios detallado.
