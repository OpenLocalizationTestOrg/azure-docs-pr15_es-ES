<properties
   pageTitle="Tutorial: Procesar facturas EDIFACT mediante servicios de Azure BizTalk | Servicios de Microsoft Azure BizTalk"
   description="Cómo crear y configurar la aplicación de conector de la caja o API y usarla en una aplicación de la lógica de servicio de la aplicación de Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="msftman"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="deonhe"/>

# <a name="tutorial-process-edifact-invoices-using-azure-biztalk-services"></a>Tutorial: Proceso EDIFACT facturas con servicios de BizTalk de Azure
Puede usar el Portal de servicios de BizTalk para configurar e implementar X12 y contratos de EDIFACT. En este tutorial, veremos cómo crear un acuerdo EDIFACT para intercambiar facturas entre socios comerciales. En este tutorial se escribe alrededor de una solución empresarial de llevar a cabo con dos socios comerciales, Northwind y Contoso que intercambiar mensajes EDIFACT.  

## <a name="sample-based-on-this-tutorial"></a>En función de este tutorial de ejemplo
En este tutorial se escribe alrededor de una muestra, **Enviar facturas de EDIFACT mediante servicios de BizTalk**, puede descargarse desde la [Galería de código de MSDN](http://go.microsoft.com/fwlink/?LinkId=401005). Puede utilizar los datos de ejemplo y vaya a través de este tutorial para comprender cómo se creó la muestra. O bien, puede utilizar este tutorial para crear su propia solución de principio a. En este tutorial va dirigido hacia el segundo enfoque para que comprender cómo se creó esta solución. Además, tanto como sea posible, el tutorial es coherente con los datos de ejemplo y usa los mismos nombres para artefactos (por ejemplo, los esquemas, transformaciones) que se utiliza en el ejemplo.  

>[AZURE.NOTE] Dado que esta solución implica envía un mensaje de un puente EAI a un puente EDI, reutiliza [puente de servicios de BizTalk encadenar muestra](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) los datos de ejemplo.  

## <a name="what-does-the-solution-do"></a>¿Qué hace la solución?

En esta solución, Northwind recibe facturas EDIFACT de Contoso. Estas facturas no están en un formato EDI estándar. Por lo tanto, antes de enviar la factura Northwind, se deben transformar a un documento de factura (también denominado INVOIC) EDIFACT. En la recepción, Northwind debe procesar la factura EDIFACT y devolver un mensaje de control (también denominado CONTRL) a Contoso.

![][1]  

Para lograr este escenario de empresa, Contoso usa las características proporcionadas con Microsoft Azure BizTalk Services.

*   Contoso utiliza puentes EAI para transformar la factura original EDIFACT INVOIC.

*   Puente EAI, a continuación, envía el mensaje a un puente de envío EDI implementado como parte de un acuerdo configurado en el Portal de servicios de BizTalk.

*   El puente de envío EDI procesa la INVOIC EDIFACT y redirige a Northwind.

*   Después de recibir la factura, Northwind devuelve un CONTRL del mensaje a EDI recibe puente implementado como parte del acuerdo.  

> [AZURE.NOTE] Opcionalmente, esta solución también muestra cómo usar el procesamiento por lotes para enviar las facturas por lotes, en lugar de enviar cada factura por separado.  

Para completar el escenario, usamos colas de Bus de servicio para enviar la factura de Contoso Northwind o recibir confirmación de Northwind. Estas colas pueden crearse con una aplicación de cliente, que está disponible como descarga y se incluye en el paquete de ejemplo que está disponible como parte de este tutorial.  

## <a name="prerequisites"></a>Requisitos previos

*   Debe tener un espacio de nombres de Bus de servicio. Para obtener instrucciones sobre cómo crear un espacio de nombres, vea [Cómo: crear o modificar un Namespace de servicio de Bus de servicio](https://msdn.microsoft.com/library/azure/hh674478.aspx). Supongamos que ya tiene un espacio de nombres de Bus de servicio que se aprovisione, denominado **edifactbts**.

*   Debe tener una suscripción de servicios de BizTalk. Para obtener instrucciones, vea [crear un servicio de BizTalk con Azure portal clásico](http://go.microsoft.com/fwlink/?LinkID=302280). En este tutorial, supongamos que tiene una suscripción de servicios de BizTalk, denominada **contosowabs**.

*   Registrar su suscripción de servicios de BizTalk en el Portal de servicios de BizTalk. Para obtener instrucciones, vea [registrar una implementación de servicio de BizTalk en el Portal de servicios de BizTalk](https://msdn.microsoft.com/library/hh689837.aspx)

*   Debe tener instalado Visual Studio.

*   Debe tener instalado el SDK de servicios de BizTalk. Puede descargar el SDK de [http://go.microsoft.com/fwlink/?LinkId=235057](http://go.microsoft.com/fwlink/?LinkId=235057)  

## <a name="step-1-create-the-service-bus-queues"></a>Paso 1: Crear las colas de Bus de servicio  
Esta solución utiliza colas de Bus de servicio para intercambiar mensajes entre socios comerciales. Contoso y Northwind envían mensajes a las colas desde donde puentes EAI y EDI consumen. Para esta solución, debe tres colas de Bus de servicio:

*   **northwindreceive** – Northwind recibe la factura de Contoso sobre esta cola.

*   **contosoreceive** : Contoso recibe la confirmación de Northwind sobre esta cola.

*   **suspendido** : suspensión todos los mensajes se enrutan a esta cola. Los mensajes se suspenden si se produce un error durante el proceso.

Puede crear estas colas Bus de servicio mediante una aplicación de cliente que se incluye en el paquete de ejemplo.  

1.  Desde la ubicación donde haya descargado la muestra, abra **Tutorial enviar facturas usando BizTalk servicios EDI Bridges.sln**.

2.  Presione **F5** para generar e iniciar la aplicación **Cliente de Tutorial** .

3.  En la pantalla, escriba el espacio de nombres de ACS de Bus de servicio, el nombre del emisor y la clave de emisor.

    ![][2]  
4.  Un cuadro de mensaje le pide que tres colas se creará en el espacio de nombres de Bus de servicio. Haga clic en **Aceptar**.

5.  Deje al cliente de Tutorial ejecuta. Abrir, haga clic en **Servicio de Bus** > **_el espacio de nombres de servicio Bus_** > **colas**y comprobar que se crean tres colas.  

## <a name="step-2-create-and-deploy-trading-partner-agreement"></a>Paso 2: Crear e implementar de acuerdo con el socio comercial
Crear un acuerdo de partner comerciales entre Contoso y Northwind. Un acuerdo con el socio comercial define un contrato de comercio entre los dos colaboradores, como qué esquema de mensajes para usar el protocolo de mensajería que se desea usar, etcetera. Un acuerdo con el socio comercial incluye dos puentes EDI, uno para enviar mensajes a socios (denominados el **puente enviar EDI**) y otro para recibir mensajes de socios (denominados del **puente de recepción EDI**).

En el contexto de esta solución, el puente de envío EDI corresponde a la parte de envío del acuerdo y se usa para enviar la factura EDIFACT de Contoso a Northwind. Del mismo modo, el puente de recepción EDI corresponde a la parte de recepción del acuerdo y se utiliza para recibir confirmaciones de Northwind.  

### <a name="create-the-trading-partners"></a>Crear a los socios comerciales

Para empezar a crear a socios de Contoso y Northwind.  

1.  En el Portal de servicios de BizTalk, en la pestaña **asociados** , haga clic en **Agregar**.

2.  En la nueva página de partner, escriba **Contoso** como el nombre de un compañero y, a continuación, haga clic en **Guardar**.

3.  Repita el paso para crear al segundo asociado, **Northwind**.  

### <a name="create-the-agreement"></a>Crear el contrato
Cotización asociado se crean entre perfiles comerciales de socios comerciales. Esta solución utiliza los perfiles de partner predeterminados que se crean automáticamente cuando se crean los socios.  

1.  En el Portal de servicios de BizTalk, haga clic en **Agreements** > **Agregar**.

2.  En la página de **Configuración General** del nuevo acuerdo, especifique los valores tal como se muestra en la imagen siguiente y, a continuación, haga clic en **continuar**.

    ![][3]  

    Después de hacer clic en **continuar**, dos pestañas, **Configuración de recibir** y **Enviar** estarán disponibles.

3.  Crear el acuerdo de envío entre Contoso y Northwind. Este acuerdo rige cómo Contoso envía la factura EDIFACT Northwind.

    1.  Haga clic en **configuración de envío**.

    2.  Conservar los valores predeterminados de las pestañas de la **Dirección URL de entrada**, **transformar**y **procesamiento por lotes** .

    3.  En la ficha **protocolo** , en la sección de **esquemas** , cargue el esquema **EFACT_D93A_INVOIC.xsd** . Este esquema está disponible con el paquete de ejemplo.

        ![][4]  
    4.  En la pestaña de **transporte** , especifique los detalles de las colas de Bus de servicio. Para el acuerdo de envío, usamos el **northwindreceive** para enviar la factura EDIFACT Northwind y la cola **suspendida** para enrutar todos los mensajes que se producirá un error durante el proceso y se ha suspendido. Crea estas colas en **paso 1: crear las colas de Bus de servicio** (en este tema).

        ![][5]  

        En **transporte configuración > tipo de transporte** y **mensaje suspensión configuración > tipo de transporte**, seleccione Bus de servicio de Azure y proporcionar los valores tal como se muestra en la imagen.

4.  Crear el contrato de recepción entre Contoso y Northwind. Este acuerdo rige cómo Contoso recibe la confirmación de Northwind.

    1.  Haga clic en **configuración de recepción**.

    2.  Conservar los valores predeterminados de las pestañas de **transporte** y **transformar** .

    3.  En la ficha **protocolo** , en la sección de **esquemas** , cargue el esquema **EFACT_4.1_CONTRL.xsd** . Este esquema está disponible con el paquete de ejemplo.

    4.  En la pestaña de la **ruta** , crear un filtro para asegurarse de que solo confirmaciones de Northwind se enrutan a Contoso. En **Configuración de la ruta**, haga clic en **Agregar** para crear el filtro de enrutamiento.

        ![][6]  
        1.  Proporcionar valores para el **Nombre de la regla**, la **regla de ruta**y la **ruta destino** tal como se muestra en la imagen.

        2.  Haga clic en **Guardar**.

    5.  En la pestaña de la **ruta** de nuevo, especifique donde confirmaciones suspendidas (confirmaciones errores durante el proceso) se enrutan a. Establecer el tipo de transporte en Bus de servicio de Azure, tipo de destino de la ruta a **cola**, tipo de autenticación a la **Firma de acceso compartido** (SA), proporcionar la cadena de conexión SA para el espacio de nombres de Bus de servicio y, a continuación, escriba el nombre de cola como **suspendido**.

5.  Por último, haga clic en **implementar** para implementar el contrato. Tenga en cuenta los extremos donde el enviar y recibir se desarrollan agreements.

    *   En la pestaña **Configuración de envío** , en la **Dirección URL de entrada**, tenga en cuenta el punto final. Para enviar un mensaje de Contoso Northwind mediante el puente de envío EDI, debe enviar un mensaje a este extremo.

    *   En la pestaña **Configuración de recepción** , bajo **transporte**, tenga en cuenta el punto final. Para enviar un mensaje de Northwind a Contoso usando EDI recibir puente, debe enviar un mensaje a este extremo.  

## <a name="step-3-create-and-deploy-the-biztalk-services-project"></a>Paso 3: Crear e implementar el proyecto de servicios de BizTalk

En el paso anterior, implementa el envío EDI y recibir agreements para procesar facturas EDIFACT y confirmaciones. Estos contratos pueden procesar mensajes que cumplen el esquema de mensaje EDIFACT estándar. Sin embargo, por el escenario para esta solución, Contoso envía una factura Northwind en un esquema propio interno. Por lo tanto, antes de que el mensaje se ha enviado al puente de envío EDI, se deben transformar del esquema interno para el esquema de factura EDIFACT estándar. El proyecto de EAI de servicios de BizTalk lo hace.

El proyecto de servicios de BizTalk, **InvoiceProcessingBridge**, que transforma el mensaje también se incluye como parte de la muestra que ha descargado. El proyecto incluye los siguientes artefactos:

*   **INHOUSEINVOICE. XSD** : esquema de la factura interna que se envía a Northwind.

*   **EFACT_D93A_INVOIC. XSD** : esquema de la factura EDIFACT estándar.

*   **EFACT_4.1_CONTRL. XSD** : esquema de la confirmación de EDIFACT Northwind envía a Contoso.

*   **INHOUSEINVOICE_to_D93AINVOIC. TRFM** – la transformación que asigna el esquema de factura internas para el esquema de factura EDIFACT estándar.  

### <a name="create-the-biztalk-services-project"></a>Crear el proyecto de servicios de BizTalk
1.  En la solución de Visual Studio, expanda el proyecto InvoiceProcessingBridge y, a continuación, abra el archivo **MessageFlowItinerary.bcs** .

2.  Haga clic en cualquier lugar en el lienzo y establecer la **Dirección URL del servicio de BizTalk** en el cuadro de propiedad para especificar el nombre de la suscripción de servicios de BizTalk. Por ejemplo, `https://contosowabs.biztalk.windows.net`.

    ![][7]  
3.  En el cuadro de herramientas, arrastre un **Puente de Xml unidireccional** al lienzo. Establecer las propiedades de **Nombre de entidad** y la **Dirección relativa** del puente a **ProcessInvoiceBridge**. Haga doble clic en **ProcessInvoiceBridge** para abrir la superficie de configuración del puente.

4.  En el cuadro **Tipos de mensaje** , haga clic en el signo más (**+**) botón para especificar el esquema de mensaje entrante. Dado que el mensaje entrante para el puente EAI siempre es la factura interna, establezca **INHOUSEINVOICE**.

    ![][8]  
5.  Haga clic en la forma de **Transformación de Xml** y, en el cuadro de propiedad para la propiedad de **mapas** , haga clic en el botón de puntos suspensivos (**...**). En el cuadro de diálogo de **Selección de mapas** , seleccione el archivo de transformación **INHOUSEINVOICE_to_D93AINVOIC** y, a continuación, haga clic en **Aceptar**.

    ![][9]  
6.  Volver al **MessageFlowItinerary.bcs**y, en el cuadro de herramientas, arrastre un **Extremo de servicio externo bidireccional** a la derecha de la **ProcessInvoiceBridge**. Establezca su propiedad **Nombre de la entidad** **EDIBridge**.

7.  En el Explorador de soluciones, expanda la **MessageFlowItinerary.bcs** y haga doble clic en el archivo **EDIBridge.config** . Reemplace el contenido de la **EDIBridge.config** con el siguiente.

    > [AZURE.NOTE] ¿Por qué necesito para editar el archivo config? El extremo de servicio externo que se agregó al lienzo diseñador puente representa el puentes EDI que hemos implementado una versión anterior. Puentes EDI son bidireccionales puentes, con un envío y recepción lado. Sin embargo, el puente EAI que se agregó al diseñador de puente es un puente unidireccional. Por lo tanto, para controlar los patrones de intercambio de mensaje diferente de los dos puentes, usamos un comportamiento personalizado puente incluyendo su configuración en el archivo config. Además, el comportamiento personalizado también controla la autenticación al extremo del puente de envío EDI. Este comportamiento personalizado está disponible como un ejemplo independiente en [puente de servicios de BizTalk encadenar muestra - EAI EDI](http://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104). Esta solución reutiliza la muestra.  
    
    ```
<?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <extensions>
          <behaviorExtensions>
            <add name="BridgeAuthentication" 
                  type="Microsoft.BizTalk.Bridge.Behaviour.BridgeBehaviorElement, Microsoft.BizTalk.Bridge.Behaviour, Version=1.0.0.0, Culture=neutral, PublicKeyToken=ae58f69b69495c05" />
          </behaviorExtensions>
        </extensions>
        <behaviors>
          <endpointBehaviors>
            <behavior name="BridgeAuthenticationConfiguration">
              <!-- Enter the ACS namespace, issuer name and issuer secret of the BizTalk Services deployment -->
              <BridgeAuthentication acsnamespace="[YOUR ACS NAMESPACE]" 
                                    issuername="owner" 
                                    issuersecret="[YOUR ACS SECRET]" />
              <webHttp />
            </behavior>
          </endpointBehaviors>
        </behaviors>
        <bindings>
          <webHttpBinding>
            <binding name="BridgeBindingConfiguration">
              <security mode="Transport" />
            </binding>
          </webHttpBinding>
        </bindings>
        <client>
          <clear />
          <!--
            Go BizTalk Portal > Agreement > Send Settings > Inbound URL
            Copy the Endpoint URL and paste it in the below address field
          -->
          <endpoint name="TwoWayExternalServiceEndpointReference1" 
                    address="[YOUR EDI BRIDGE SEND URI]" 
                    behaviorConfiguration="BridgeAuthenticationConfiguration" 
                    binding="webHttpBinding" 
                    bindingConfiguration="BridgeBindingConfiguration" 
                    contract="System.ServiceModel.Routing.IRequestReplyRouter" />
        </client>
      </system.serviceModel>
    </configuration>

    ```
8.  Actualizar el archivo EDIBridge.config para incluir detalles de configuración

    *   En _<behaviors>_, proporcionar el espacio de nombres de ACS y la clave asociada a la suscripción a servicios de BizTalk.

    *   En _<client>_, proporcione el extremo donde se implementa el contrato de envío EDI.

    Guardar los cambios y cierre el archivo de configuración.

9.  En el cuadro de herramientas, haga clic en el **conector** y unirse a los componentes **ProcessInvoiceBridge** y **EDIBridge** . Seleccione el conector y, en el cuadro de propiedades, establezca la **Condición de filtro** en **Coincidir todos los**. Así se garantiza que todos los mensajes procesados por el puente EAI se enrutan a puente EDI.

    ![][10]  
10.  Guardar los cambios en la solución.  

### <a name="deploy-the-project"></a>Implementar el proyecto

1.  En el equipo donde ha creado el proyecto de servicios de BizTalk, descargue e instale el certificado SSL para su suscripción de servicios de BizTalk. Desde, en servicios de BizTalk, haga clic en **panel**y, a continuación, haga clic en **Descargar certificado de SSL**. Haga doble clic en el certificado y siga el símbolo del sistema para completar la instalación. Asegúrese de que instalar el certificado en el almacén de certificados de **Entidades emisoras de certificados raíz de confianza** .

2.  En el Explorador de soluciones de Visual Studio, haga clic en el proyecto **InvoiceProcessingBridge** y, a continuación, haga clic en **implementar**.

3.  Proporcionar los valores tal como se muestra en la imagen y, a continuación, haga clic en **implementar**. Puede obtener las credenciales de ACS para los servicios de BizTalk haciendo clic en **Información de conexión** en el panel de servicios de BizTalk.

    ![][11]  

    Desde el panel de resultados, copie el extremo donde se implementa el puente EAI, por ejemplo, `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`. Necesitará esta dirección URL del extremo más adelante.  

## <a name="step-4-test-the-solution"></a>Paso 4: Probar la solución


En este tema, veremos cómo probar la solución mediante la aplicación **Cliente de Tutorial** proporcionada como parte de la muestra.  

1.  En Visual Studio, presione F5 para iniciar el **Tutorial de cliente**.

2.  La pantalla debe tener los valores que se rellena automáticamente en el paso donde creamos colas de Bus de servicio. Haga clic en **siguiente**.

3.  En la ventana siguiente, proporcione credenciales de ACS de suscripción de servicios de BizTalk y los extremos donde EAI y EDI (recibir) se implementan puentes.

    El extremo del puente EAI se había copiado en el paso anterior. Para EDI recibir puente extremo, en el Portal de servicios de BizTalk, vaya al contrato > configuración de recepción > transporte > extremo.

    ![][12]  
4.  En la ventana siguiente, en Contoso, haga clic en el botón **Enviar factura internas** . En el archivo Abrir cuadro de diálogo, abra el archivo INHOUSEINVOICE.txt. Examinar el contenido del archivo y, a continuación, haga clic en **Aceptar** para enviar la factura.

    ![][13]  
5.  En unos segundos se recibe la factura de Northwind. Haga clic en el vínculo **Ver mensaje** para ver la factura recibida por Northwind. Observe cómo la factura recibida por Northwind en esquema EDIFACT estándar mientras la enviados por Contoso era un esquema interno.

    ![][14]  
6.  Seleccione la factura y, a continuación, haga clic en **Enviar confirmación**. En el cuadro de diálogo que aparece, observe que el identificador de intercambio es el mismo en la factura recibida y la confirmación de que se envían. Haga clic en Aceptar en el cuadro de diálogo de **Confirmación de enviar** .

    ![][15]  
7.  En unos segundos, se recibe correctamente la confirmación de Contoso.

    ![][16]  

## <a name="step-5-optional-send-edifact-invoice-in-batches"></a>Paso 5 (opcional): factura enviar EDIFACT por lotes 
Puentes EDI de servicios de BizTalk también es compatible con procesamiento por lotes de los mensajes salientes. Esta característica es útil para recibir socios que prefieren recibir un lote de mensajes (reunión determinado criterio) en lugar de mensajes individuales.

El aspecto más importante cuando se trabaja con lotes es la versión real del lote, también denominado los criterios de lanzamiento. Los criterios de versión se pueden basar en cómo el socio destinatario desea recibir mensajes. Si se habilita el procesamiento por lotes, el puente EDI no enviar el mensaje saliente en el asociado receptor hasta que se cumpla los criterios de lanzamiento. Por ejemplo, un criterio por lotes en función de envíos de tamaño del mensaje un lote sólo cuando ' n ' se procesan por lotes de mensajes. Un criterio lote también pueden basada en tiempo que un lote se envía a una hora fija cada día. En esta solución, tratamos los criterios en función del tamaño del mensaje.

1.  En el Portal de servicios de BizTalk, haga clic en el acuerdo que creó anteriormente. Haga clic en configuración de envío > Lotes > Agregar lote.

2.  Para nombre de sección, escriba **InvoiceBatch**, proporcione una descripción y, a continuación, haga clic en **siguiente**.

3.  Especificar criterios por lotes, que define los mensajes que deben ser por lotes. En esta solución, hemos por lotes todos los mensajes. Por lo tanto, seleccione el uso de definiciones opción avanzada y escriba **1 = 1**. Esta es una condición que siempre será true y, por tanto, se procesarse todos los mensajes. Haga clic en **siguiente**.

    ![][17]  
4.  Especificar criterios por lotes lanzamiento. En el cuadro desplegable, seleccione **MessageCountBased**y para **contar**, especifique **3**. Esto significa que un lote de tres mensajes que se enviarán a Northwind. Haga clic en **siguiente**.

    ![][18]  
5.  Revise el resumen y, a continuación, haga clic en **Guardar**. Haga clic en **implementar** para implementar el contrato.

6.  Volver al **Cliente Tutorial**, haga clic en **Enviar factura internas**y siga las indicaciones para enviar la factura. Observe que no se ha recibido ninguna factura de Northwind porque no se cumple el tamaño del lote. Repita este paso dos veces más, por lo que tiene tres factura mensajes enviados a Northwind. Esto cumple los criterios de lanzamiento de lote de los 3 mensajes y ahora debe ver una factura de Northwind.


<!--Image references-->
[1]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-1.PNG  
[2]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-2.PNG  
[3]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-3.PNG
[4]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-4.PNG  
[5]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-5.PNG  
[6]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-6.PNG  
[7]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-7.PNG  
[8]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-8.PNG
[9]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-9.PNG  
[10]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-10.PNG  
[11]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-11.PNG  
[12]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-12.PNG  
[13]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-13.PNG
[14]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-14.PNG  
[15]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-15.PNG  
[16]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-16.PNG  
[17]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-17.PNG  
[18]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-18.PNG

