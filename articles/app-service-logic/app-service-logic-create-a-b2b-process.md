<properties 
   pageTitle="Creación de un proceso de B2B en servicio de la aplicación de Azure | Microsoft Azure" 
   description="Información general sobre cómo crear un proceso de negocio de negocio" 
   services="logic-apps" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>

# <a name="creating-a-b2b-process"></a>Crear un proceso de B2B

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]


## <a name="business-scenario"></a>Escenario de empresa 
Contoso y Northwind son dos socios. Contoso (el distribuidor) envía pedidos de compra a Northwind (el distribuidor) a través de un transporte de nivel de la industria como AS2. Northwind almacena todos los pedidos entrantes en su lugar de almacenamiento de la nube. Los pedidos de compra son mensajes XML entre estas dos socios. Una vez que el mensaje se almacena en el almacenamiento de nube de Northwind los procesos internos de Northwind controlen el orden de ese punto en.
 
El objetivo de este tutorial es establecer cómo Northwind puede establecer un proceso empresarial a través de la que puede recibir mensajes (órdenes de compra en XML) de su asociado de Contoso sobre AS2 y, a continuación, se mantiene en su lugar de almacenamiento de la nube.


## <a name="capabilities-demonstrated"></a>Capacidades de muestra 
Este tutorial le ayuda a presentar las siguientes capacidades: 

- **Transporte del mensaje**: el distribuidor y el proveedor pueden estar en distintas plataformas pero aún pueden lograr la comunicación entre los dos. En este tutorial se comunican a través AS2 (aplicación declaración 2). AS2 es una forma popular de transporte de datos entre socios comerciales en las comunicaciones entre empresas.
- **Persistencia de los datos**: una vez que se ha recibido el mensaje sobre AS2, a continuación, Northwind desea conservar antes de procesamiento. Puede utilizar un conector para conservar los mensajes en su lugar de almacenamiento de la nube. En este tutorial Blobs de Azure es que se usan como el almacenamiento de nube de Northwind.
- **Creación de un proceso empresarial**: un flujo de varias aplicaciones API pueden se unen para conseguir un resultado de la empresa como se muestra aquí.


## <a name="before-you-begin"></a>Antes de empezar
En este tutorial se supone que tiene un conocimiento básico de servicios de aplicación de Azure, saber cómo crear aplicaciones API y unir un flujo.


## <a name="steps-to-achieve-the-business-scenario"></a>Pasos para lograr la situación de negocio
**Crear y configurar las aplicaciones de la API necesarias**

1. Crear una instancia del **Conector de Azure almacenamiento de blobs de Windows**. Esto requiere las credenciales para una cuenta de almacenamiento de Azure. Asegúrese de que está preparado antes de empezar a crear esto.
2. Crear una instancia de la **Administración de socios comerciales de BizTalk**. Esto requiere una base de datos de SQL en blanco a función. Asegúrese de que está preparado antes de empezar a crear esto.
3. Crear una instancia del **Conector AS2**. Esto también requiere una base de datos de SQL en blanco a función. Asegúrese de que está preparado antes de empezar a crear esto. Además, si desea archivar los mensajes como parte de AS2 procesamiento, puede proporcionar credenciales para una blobs de Windows Azure durante su creación.
4. Configurar el servicio TPM (administración de socios comerciales) que se crea:  
    1. Vaya a la instancia del servicio TPM creado como parte de los pasos anteriores.
    2. Usar la opción de **socios** en *componentes* para **Agregar** un nuevo asociado de denominada **Contoso** y en su perfil de agregar la identidad AS2 necesaria.
    3. Usar la opción de **socios** en *componentes* para **Agregar** un nuevo asociado con el nombre de **Northwind** y en su perfil de agregar la identidad de AS2 necesaria.
    4. Use la opción de **contratos** en *componentes de* acuerdo de **Agregar** un nuevo AS2 entre Contoso y Northwind. Northwind será el partner hospedado aquí y Contoso será el partner de invitado. Según corresponda configurar firma, cifrado, compresión y confirmaciones durante la creación de este acuerdo. En caso de certificados sea necesitan, puede cargar a través de la opción de **certificados** al explorar el servicio TPM que se crea.


## <a name="create-a-flow--business-process"></a>Crear un flujo de proceso de negocio
1. Crear un flujo de nuevo en el que el primer paso es AS2. Arrastre y coloque el **Conector AS2** y elija la instancia que ya ha creado. Elija desencadenador como la funcionalidad:  
    ![][1]  
2. A continuación arrastre y coloque **Azure almacenamiento de blobs conector** y elija la instancia que ya ha creado. Elija la acción como la funcionalidad y dentro de ella, seleccione **Cargar Blob** como la funcionalidad deseada. Configurar según corresponda.
3. Ahora crea e implementa el flujo.


## <a name="message-processing--troubleshooting"></a>Procesamiento de mensajes y solución de problemas
1. Es el momento de probar el flujo que hemos implementado. Enviar que mensajes XML ajustadas en AS2 (según el contrato de AS2 creado anteriormente) al extremo AS2 expuesto por la instancia de AS2Connector que ha creado. Debe configurar la autenticación del extremo de modo que es accesible públicamente.
2. Información de la ejecución del flujo de es expuesta examinando el flujo y pasar luego a la instancia de flujo que obtuvo ejecuta
3. Para obtener información de procesamiento de AS2, vaya a la instancia de AS2Connector implicada y siga al entrar en el elemento de seguimiento. Puede usar los filtros que implica restringir la vista a la información que se desea.

![][2]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-b2b-process/Flow.png
[2]: ./media/app-service-logic-create-a-b2b-process/Tracking.png
 
