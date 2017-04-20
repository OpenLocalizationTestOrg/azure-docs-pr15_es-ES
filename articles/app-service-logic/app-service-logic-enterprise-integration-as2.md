<properties 
    pageTitle="Aprenda a crear un acuerdo AS2 para el paquete de integración de Enterprise" 
    description="Aprenda a crear un acuerdo AS2 para el paquete de integración de empresa | Servicio de aplicaciones de Microsoft Azure" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="msftman" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="deonhe"/>

# <a name="enterprise-integration-with-as2"></a>Integración de Enterprise con AS2

## <a name="create-an-as2-agreement"></a>Crear un acuerdo AS2
Para utilizar las funciones de empresa en aplicaciones de lógica, primero debe crear contratos. 

### <a name="heres-what-you-need-before-you-get-started"></a>Esto es lo necesita antes de empezar
- Una [cuenta de integración](./app-service-logic-enterprise-integration-accounts.md) definido en su suscripción de Azure  
- Al menos dos [socios](./app-service-logic-enterprise-integration-partners.md) ya definido en su cuenta de integración  

>[AZURE.NOTE]Al crear un acuerdo, el contenido del archivo de contrato debe coincidir con el tipo de acuerdo.    


Después de haber [creado una cuenta de integración](./app-service-logic-enterprise-integration-accounts.md) y [asociados](./app-service-logic-enterprise-integration-partners.md), puede crear un acuerdo siguiendo estos pasos:  

### <a name="from-the-azure-portal-home-page"></a>Desde la página principal del portal Azure

Después de iniciar sesión en el [portal de Azure](http://portal.azure.com "Azure portal"):  
1. En el menú de la izquierda, seleccione **Examinar** .  

>[AZURE.TIP]Si no ve el vínculo **Examinar** , debe expandir el menú en primer lugar. Haga esto seleccionando el vínculo **Mostrar menú** que se encuentra en la parte superior izquierda del menú contraído.  

![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. Escriba la *integración* en el cuadro de búsqueda de filtro, a continuación, seleccione **Cuentas de integración** de la lista de resultados.       
 ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
3. En el módulo de **Integración de cuentas** que se abre, seleccione la cuenta de integración en el que creará el contrato. Si no ve la integración de cuentas de listas, [crear una primera](./app-service-logic-enterprise-integration-accounts.md "All about integration accounts").  
![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4.  Seleccione el icono de **contratos** . Si no ve los contratos de mosaico, agregue en primer lugar.   
![](./media/app-service-logic-enterprise-integration-agreements/agreement-1.png)   
5. En el módulo de contratos que se abre, seleccione el botón **Agregar** .  
![](./media/app-service-logic-enterprise-integration-agreements/agreement-2.png)  
6. Escriba un **nombre** para el acuerdo, a continuación, seleccione el **Partner de Host**, **Identidad de Host**, **Invitado asociado**, **Identidad de invitado**, en el módulo de contratos que se abre.  
![](./media/app-service-logic-enterprise-integration-agreements/agreement-3.png)  

Estos son algunos detalles que le resultará útil cuando la configuración para el acuerdo: 
  
|(Propiedad)|Descripción|
|----|----|
|Partner de host|Un contrato debe a partner host y de invitado. El partner de host representa la organización que está configurando el contrato.|
|Identidad de host|Identificador de asociado de host. |
|Socio invitado|Un contrato debe a partner host y de invitado. El partner de invitado representa la organización que está realizando la empresa con el partner de host.|
|Identidad de invitado|Identificador de asociado de invitado.|
|Configuración de recepción|Estas propiedades se aplican a todos los mensajes recibidos por un acuerdo|
|Configuración de envío|Estas propiedades se aplican a todos los mensajes enviados por un acuerdo|  
Vamos a seguir:  
7. Seleccione **Configuración de recepción** para configurar cómo mensajes recibidos a través de este acuerdo deben corregirse.  
 
 - Si lo desea, puede reemplazar las propiedades en el mensaje entrante. Para ello, seleccione la casilla de verificación **reemplazar las propiedades de mensaje** .
  - Seleccione la casilla de verificación **mensaje debe haber iniciado sesión** si le gustaría requieren todos los mensajes entrantes estén firmados. Si selecciona esta opción, también debe seleccionar el **certificado** que se usará para validar la firma en los mensajes.
  - Si lo desea, puede requerir mensajes estén cifrados también. Para ello, seleccione la casilla de verificación **se debe cifrar el mensaje** . Necesitará, a continuación, seleccione el **certificado** que se usará para descodificar los mensajes entrantes.
  - También puede requerir que los mensajes se comprime. Para ello, active la casilla **que se debe comprimir mensaje** .  
![](./media/app-service-logic-enterprise-integration-agreements/agreement-4.png)  

Si desea más información sobre la recepción de qué habilita configuración, consulte la tabla siguiente.  

|(Propiedad)|Descripción|
|----|----|
|Reemplazar las propiedades de mensaje|Seleccione esta opción para indicar que se pueden reemplazar propiedades en los mensajes recibidos |
|Debe haber iniciado sesión mensaje|Habilitar esta opción para requerir que los mensajes estén firmados digitalmente|
|Se debe cifrar el mensaje|Habilitar esta opción para requerir que los mensajes estén cifrados. Mensajes cifrados no serán rechazados.|
|Mensaje que se debe comprimir|Habilitar esta opción para requerir que los mensajes se comprime. Mensajes comprimidos no serán rechazados.|
|Texto MDN|Este es un valor predeterminado MDN que se envíen al remitente del mensaje|
|Enviar MDN|Habilitar esta opción para permitir MDNs que se envíen.|
|Enviar MDN firmado|Habilitar esta opción para requerir MDNs estén firmados.|
|Algoritmo de Micrófono||
|Enviar MDN asincrónica|Habilitar esta opción para requerir que los mensajes se envíen asincrónica.|
|DIRECCIÓN URL|Esta es la dirección URL a la que se enviarán los mensajes.|
Ahora, vamos a seguir:  
8. Seleccione **Configuración de envío** para configurar cómo mensajes enviados a través de este acuerdo deben corregirse.  
![](./media/app-service-logic-enterprise-integration-agreements/agreement-5.png)  

Si desea más información sobre el envío de qué habilita configuración, consulte la tabla siguiente.  

|(Propiedad)|Descripción|
|----|----|
|Habilitar la firma de mensaje|Seleccione esta casilla para habilitar todos los mensajes enviados desde el contrato de haber iniciado sesión.|
|Algoritmo de Micrófono|Seleccione el algoritmo que se utiliza en la firma de mensaje|
|Certificado|Seleccione el certificado de firma de mensaje|
|Habilitar el cifrado de mensajes|Seleccione esta casilla para cifrar todos los mensajes enviados desde este contrato.|
|Algoritmo de cifrado|Seleccione el algoritmo de cifrado para usar en el cifrado de mensajes|
|Unfold encabezados HTTP|Seleccione esta casilla de verificación tendrá la oportunidad de encabezado de tipo de contenido HTTP en una sola línea.|
|Solicitar MDN|Activar esta casilla solicitar un MDN para todos los mensajes enviados desde este contrato|
|Solicitar MDN firmado|Habilitar para solicitar que todos los MDNs enviados a este contrato iniciado sesión|
|Solicitar MDN asincrónica|Habilitar para solicitar MDN asincrónica que se envíen a este contrato|
|DIRECCIÓN URL|La dirección URL a la que se enviará MDNs|
|Habilitar NRR|Seleccione esta casilla para habilitar no renuncia de recepción|
¡Se han terminado casi!  
9. Seleccione el mosaico de **contratos** en el módulo de integración cuenta y verá el contrato recién agregado aparece.  
![](./media/app-service-logic-enterprise-integration-agreements/agreement-6.png)

