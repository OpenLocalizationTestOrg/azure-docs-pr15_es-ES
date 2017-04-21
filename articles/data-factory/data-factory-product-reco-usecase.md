<properties 
    pageTitle="Caso de uso de datos Factory - recomendaciones de producto" 
    description="Obtenga información sobre un caso de uso que se implementan mediante el generador de datos de Azure junto con otros servicios." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/01/2016" 
    ms.author="shlo"/>

# <a name="use-case---product-recommendations"></a>Utilizar caso - recomendaciones de producto 

Generador de datos de Azure es uno de los muchos servicios que se utiliza para implementar el conjunto de inteligencia Cortana de aceleradores de soluciones.  Consulte la página de [Conjunto de aplicaciones de inteligencia de Cortana](http://www.microsoft.com/cortanaanalytics) para obtener más información acerca de este conjunto de aplicaciones. En este documento, describimos un caso de uso común que usuarios Azure ya han resuelto e implementa mediante el generador de datos de Azure y otros servicios de componentes de inteligencia de Cortana.

## <a name="scenario"></a>Escenario

Los distribuidores en línea con frecuencia desea atraer sus clientes comprar productos presentando con productos son probable que le interese y, por tanto, probablemente comprar. Para ello, necesitan personalizar la experiencia de los usuarios en línea mediante recomendaciones de productos personalizados para ese usuario específico comercios en línea. Estas recomendaciones personalizadas deben realizarse basándose en sus actuales e históricos de compras datos comportamiento, información de producto, presentadas recientemente marcas y los datos de segmentación de cliente y soporte técnico.  Además, pueden proporcionar las recomendaciones de producto de usuario en función de análisis de comportamiento de uso general de todos sus usuarios combinados.

El objetivo de estos distribuidores es optimizar para conversiones de usuario haga clic en a la venta y obtener ingresos de ventas superiores.  Esta conversión consiga ofreciendo recomendaciones de producto contextual basada en comportamiento basadas en intereses de los clientes y acciones. En este caso de uso, usamos comercios en línea como un ejemplo de empresas que desean optimizar para sus clientes. Sin embargo, estos principios se aplican a cualquier empresa que desee contratar a sus clientes alrededor de sus productos y servicios y mejorar la experiencia de compra de sus clientes con recomendaciones de productos personalizados.

## <a name="challenges"></a>Desafíos

Hay muchos desafíos que nominal de comercios en línea cuando intenta implementar este tipo de caso de uso. 

En primer lugar, se deben ingestión los datos de diferentes tamaños y formas de varios orígenes de datos, ambos local y en la nube. Estos datos incluyen datos de productos, datos de comportamiento de cliente histórica y datos de usuario mientras el usuario explora el sitio de minorista en línea. 

Recomendaciones personalizadas, segundo producto deben ser razonablemente y precisa calcula y pronosticadas. Además de producto, marca y datos del cliente de comportamiento y el explorador, los distribuidores online también debe incluir comentarios de los clientes en compras anteriores en la determinación de los productos recomendadas para el usuario. 

Por último, las recomendaciones deben ser entregas inmediatamente al usuario para que proporcione una exploración perfecta y adquirir experiencia y proporcionar las recomendaciones más recientes y relevantes. 

Por último, los distribuidores necesitan medir la eficacia de su enfoque mediante el seguimiento de venta general y cruzadas éxitos de ventas de conversión de hacer clic y ajustar sus recomendaciones futuras.

## <a name="solution-overview"></a>Información general sobre soluciones

En este caso de uso de ejemplo se ha resuelto e implementada por usuarios real Azure mediante el generador de datos de Azure y otros servicios de componentes Cortana inteligencia, incluidos [HDInsight](https://azure.microsoft.com/services/hdinsight/) y [Power BI](https://powerbi.microsoft.com/).

El distribuidor online utiliza un almacenamiento de blobs de Windows Azure, un servidor SQL en local, DB de SQL Azure y un puesto de datos relacionales como sus opciones de almacenamiento de datos a lo largo del flujo de trabajo.  El almacenamiento de blobs contiene información del cliente, los datos de comportamiento de cliente y datos de la información de producto. Los datos de información del producto incluyen información de la marca de producto y un producto del catálogo almacenan localmente en el almacén de datos SQL. 

Todos los datos se combinan e introducida en un sistema de recomendación de producto para ofrecer recomendaciones personalizadas según los intereses de cliente y acciones, mientras el usuario explora productos en el catálogo en el sitio Web. Los clientes también ver productos relacionados con el producto que están viendo en función de los patrones de uso general de sitio Web que no están relacionados de un usuario.

![diagrama de casos](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabytes de archivos de registro bruto web se generan diariamente desde el sitio Web del distribuidor en línea como archivos semiestructurados. Los archivos de registro bruto web y la información de catálogo de cliente y el producto se ingiere regularmente en un almacenamiento de blobs de Windows Azure mediante el movimiento de datos implementada globalmente del generador de datos como un servicio. Los archivos de registro bruto del día se dividen (por año y mes) en el almacenamiento de blobs de almacenamiento a largo plazo.  [HDInsight de Azure](https://azure.microsoft.com/services/hdinsight/) se usa para dividir los archivos de registro bruto en el almacenamiento de blobs y procesar los registros integrados a escala mediante secuencias de comandos de la sección y cerdo. Datos de registros de la web dividida se procesa para extraer las entradas necesarias para un equipo sistema de recomendación para generar las recomendaciones de productos personalizados de aprendizaje.

El sistema de recomendación utilizado para el equipo de aprendizaje en este ejemplo es una máquina de Abrir origen plataforma de recomendación de [Apache Mahout](http://mahout.apache.org/)de aprendizaje.  Cualquier [Azure aprendizaje](https://azure.microsoft.com/services/machine-learning/) o modelo personalizado se puede aplicar al escenario.  El modelo de Mahout se usa para predecir las similitudes entre los elementos en el sitio Web basándose en los patrones de uso general y para generar las recomendaciones personalizadas basadas en el usuario individual.

Por último, el conjunto de resultados de recomendaciones de productos personalizados se mueve a un puesto de datos relacionales de consumo por el sitio Web de un distribuidor.  El conjunto de resultados podría también se accede directamente desde el almacenamiento de blobs por otra aplicación o mueve a stores adicionales de otros casos de uso y los consumidores.

## <a name="benefits"></a>Ventajas

Al optimizar su estrategia de producto de recomendación y alinear con los objetivos empresariales, la solución cumple elaborado del distribuidor en línea y los objetivos de marketing. Además, podían operativa y administrar el flujo de trabajo de recomendación de producto de manera eficiente, confiable y rentable. El enfoque más fácil para actualizar su modelo y ajustar su eficacia según las medidas de éxitos de conversión de haga clic en ventas. Al usar el generador de datos de Azure, podían abandonar su administración de recursos de tiempo y consume nube manual y mover a la administración de recursos de la nube a petición. Por lo tanto, pudieron ahorrar tiempo, dinero y reducir el tiempo de implementación de soluciones. Vistas de linaje de datos y el estado del servicio operativas estuvo fáciles de visualizar y solucionar problemas con la supervisión de fábrica datos intuitiva y administración disponible desde el portal de Azure de la interfaz de usuario. Su solución ahora se puede programar y administrar para que termine de datos se produce confiable y se entrega a los usuarios y datos y dependencias de procesamiento se administran automáticamente sin intervención humana.

Al proporcionar esta experiencia personalizada de compra, el distribuidor online creado un cliente más competitivo y atractivo experiencia y, por tanto, aumente la satisfacción del cliente de ventas y en general.



  