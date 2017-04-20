<properties 
    pageTitle="Información general sobre las cuentas de integración y el paquete de integración de empresa | Servicio de aplicaciones de Microsoft Azure | Microsoft Azure" 
    description="Conozca todo acerca de las cuentas de integración, el paquete de integración de Enterprise y aplicaciones de lógica" 
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
    ms.date="07/08/2016" 
    ms.author="deonhe"/>

# <a name="overview-of-integration-accounts"></a>Información general sobre las cuentas de integración

## <a name="what-is-an-integration-account"></a>¿Qué es una cuenta de integración?
Una cuenta de integración es una cuenta de Azure que permite la integración de Enterprise aplicaciones administrar artefactos, incluidos los esquemas, mapas, certificados, socios y contratos. Crear una aplicación de integración tendrá que utilizar una cuenta de integración con el fin de obtener acceso a un esquema, mapa o certificado, por ejemplo.

## <a name="create-an-integration-account"></a>Crear una cuenta de integración 
1. Seleccione **Examinar**   
![](./media/app-service-logic-enterprise-integration-accounts/account-1.png)  
2. Escriba la **integración** en el cuadro de búsqueda de filtro y seleccione **Cuentas de integración** de la lista de resultados     
 ![](./media/app-service-logic-enterprise-integration-accounts/account-2.png)  
3. Seleccione el botón *Agregar* en el menú en la parte superior de la página      
![](./media/app-service-logic-enterprise-integration-accounts/account-3.png)  
4. Escriba el **nombre**, seleccione la **suscripción** que desea usar, bien cree un nuevo **grupo de recursos** o seleccione un grupo de recursos existente, seleccione una **ubicación** donde la cuenta de integración se alojarán, seleccione un **nivel de precios**y luego seleccione el botón **crear** .   

  En este momento, la cuenta de integración se aprovisionará en la ubicación seleccionada. Esto se debe completar dentro de 1 minuto.    
![](./media/app-service-logic-enterprise-integration-accounts/account-4.png)  
5. Actualice la página. Verá la nueva cuenta de integración enumerada. ¡Felicidades!  
![](./media/app-service-logic-enterprise-integration-accounts/account-5.png) 

## <a name="how-to-link-an-integration-account-to-a-logic-app"></a>Cómo vincular una cuenta de integración a una aplicación de lógica
En el orden de las aplicaciones de lógica tener acceso a los mapas, esquemas, contratos y otros artefactos que se encuentran en su cuenta de integración, primero debe vincular la cuenta de integración a su aplicación lógica.

### <a name="here-are-the-steps-to-link-an-integration-account-to-a-logic-app"></a>Estos son los pasos para vincular una cuenta de integración a una aplicación de lógica 

#### <a name="prerequisites"></a>Requisitos previos
- Una cuenta de integración
- Una aplicación de lógica

>[AZURE.NOTE]Asegúrese de que su cuenta de integración y la aplicación de la lógica están en la **misma ubicación de Azure** antes de empezar

1. Seleccione el vínculo **configuración** en el menú de la aplicación de lógica  
![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-1.png)   
2. Seleccione el elemento de la **Cuenta de integración** de módulo de configuración  
![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-2.png)   
3. Seleccione la cuenta de integración que desea establecer un vínculo a la lógica de la aplicación, **Seleccione una cuenta de integración** desplegable del cuadro de lista  
![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-3.png)   
4. Guarde su trabajo  
![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-4.png)   
5. Verá una notificación que indica que su cuenta de integración se ha vinculado a su aplicación lógica y que todos los defectos en su cuenta de integración están disponibles para su aplicación lógica.  
![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-5.png)   

Ahora que su cuenta de integración está vinculada a su aplicación lógica, puede ir a su aplicación lógica y usar conectores B2B como la validación de XML, archivo plano codificar o descodificar o transformación crear aplicaciones con características de B2B.  
    
## <a name="how-to-delete-an-integration-account"></a>¿Cómo eliminar una cuenta de integración?
1. Seleccione **Examinar**  
![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. Escriba la **integración** en el cuadro de búsqueda de filtro y seleccione **Cuentas de integración** de la lista de resultados     
 ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
3. Seleccione la **cuenta de integración** que desea eliminar  
![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4. Seleccione el vínculo **Eliminar** que se encuentra en el menú   
![](./media/app-service-logic-enterprise-integration-accounts/delete.png)  
5. Confirmar su elección    

## <a name="how-to-move-an-integration-account"></a>¿Cómo mover una cuenta de integración?
Puede mover fácilmente una cuenta de integración a una nueva suscripción y un nuevo grupo de recursos. Si necesita mover su cuenta de integración, siga estos pasos:

>[AZURE.IMPORTANT] Debe actualizar todas las secuencias de comandos para usar los nuevos identificadores de recursos después de mover una cuenta de integración.

1. Seleccione **Examinar**  
![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. Escriba la **integración** en el cuadro de búsqueda de filtro y seleccione **Cuentas de integración** de la lista de resultados     
 ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
3. Seleccione la **cuenta de integración** que desea eliminar  
![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4. Seleccione el vínculo **mover** que se encuentra en el menú   
![](./media/app-service-logic-enterprise-integration-accounts/move.png)  
5. Confirmar su elección    

## <a name="next-steps"></a>Pasos siguientes
- [Más información acerca de contratos] (./app-service-logic-enterprise-integration-agreements.md "Obtenga más información acerca de los contratos de integración de enterprise")  


 