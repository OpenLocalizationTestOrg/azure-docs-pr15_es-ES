<properties
    pageTitle="Descripción general de esquemas y el paquete de integración de empresa | Microsoft Azure"
    description="Aprenda a usar los esquemas con las aplicaciones empresariales Integration Pack y lógica"
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
    ms.date="07/29/2016"
    ms.author="deonhe"/>

# <a name="learn-about-schemas-and-the-enterprise-integration-pack"></a>Obtenga información sobre los esquemas y el paquete de integración de Enterprise  

## <a name="why-use-a-schema"></a>¿Por qué usar un esquema?
Utilizar esquemas para confirmar que recibe los documentos XML son válidos, con los datos esperados en un formato predefinido. Los esquemas se utilizan para validar los mensajes que se ha intercambiado en un escenario de B2B.

## <a name="add-a-schema"></a>Agregar un esquema
Desde el portal de Azure:  

1. Seleccione **más servicios**.  
![Portal de captura de pantalla de Azure, con "Más servicios" resaltado](./media/app-service-logic-enterprise-integration-overview/overview-11.png)    

2. En el cuadro de búsqueda de filtro, escriba **integración**y seleccione **Cuentas de integración** de la lista de resultados.     
![Captura de pantalla del cuadro de búsqueda de filtro](./media/app-service-logic-enterprise-integration-overview/overview-21.png)  
3. Seleccione la **cuenta de integración** al que agregar el esquema.    
![Captura de pantalla de la lista de cuentas de integración](./media/app-service-logic-enterprise-integration-overview/overview-31.png)  

4. Seleccione el icono de **esquemas** .  
![Captura de pantalla de IEP cuenta integración con "Esquemas" resaltados](./media/app-service-logic-enterprise-integration-schemas/schema-11.png)  

### <a name="add-a-schema-file-less-than-2-mb"></a>Agregar un archivo de esquema menor que 2 MB  

1. En el módulo de **esquemas** que se abre (desde los pasos anteriores), seleccione **Agregar**.  
![Módulo de captura de pantalla de esquemas, con el botón "Agregar" resaltado](./media/app-service-logic-enterprise-integration-schemas/schema-21.png)  

2. Escriba un nombre para el esquema. Para cargar el archivo de esquema, seleccione el icono de carpeta junto al cuadro de texto de **esquema** . Después de completa el proceso de carga, seleccione **Aceptar**.    
![Captura de pantalla de "Agregar esquema", "Archivo pequeña" resaltado](./media/app-service-logic-enterprise-integration-schemas/schema-31.png)  

### <a name="add-a-schema-file-larger-than-2-mb-up-to-a-maximum-of-8-mb"></a>Agregar un archivo de esquema más de 2 MB (hasta un máximo de 8 MB)  

El proceso para este depende el nivel de acceso del contenedor de blob: **público** o **sin acceso anónimo**. Para determinar este nivel de acceso, en el **Explorador de almacenamiento de Azure**en **Contenedores de Blob**, seleccione el contenedor de blob que desee. Seleccione **seguridad**y seleccione la pestaña de **Nivel de acceso** .

1. Si el nivel de acceso de seguridad de blob es **público**, siga estos pasos.  
  ![Captura de pantalla de Azure Explorador de almacenamiento, con "Blob contenedores", "Seguridad" y "Público" resaltado](./media/app-service-logic-enterprise-integration-schemas/blob-public.png)  

    una. Cargar el esquema con el almacenamiento y copie el URI.  
    ![Captura de pantalla de la cuenta de almacenamiento, con URI resaltado](./media/app-service-logic-enterprise-integration-schemas/schema-blob.png)  

    b. En **Agregar esquema**, seleccione **archivo de gran tamaño**y proporcionar el URI en el cuadro de texto de **URI del contenido** .  
    ![Captura de pantalla de esquemas, con el botón "Agregar" y "Archivo de gran tamaño" resaltado](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  

2. Si el nivel de acceso de seguridad de blob no es **acceso anónimo**, siga estos pasos.  
  ![Captura de pantalla de Azure Explorador de almacenamiento, con "Blob contenedores", "Seguridad" y "Sin acceso anónimo" resaltado](./media/app-service-logic-enterprise-integration-schemas/blob-1.png)  

    una. Cargue el esquema al almacenamiento.  
    ![Captura de pantalla de la cuenta de almacenamiento](./media/app-service-logic-enterprise-integration-schemas/blob-3.png)

    b. Generar una firma de acceso compartido para el esquema.  
    ![Captura de pantalla de sccount de almacenamiento, con acceso compartido firmas ficha resaltado](./media/app-service-logic-enterprise-integration-schemas/blob-2.png)

    c. En **Agregar esquema**, seleccione **archivo de gran tamaño**y proporcionar la firma de acceso compartido URI en el cuadro de texto de **URI del contenido** .  
    ![Captura de pantalla de esquemas, con el botón "Agregar" y "Archivo de gran tamaño" resaltado](./media/app-service-logic-enterprise-integration-schemas/schema-largefile.png)  

3. En el módulo de **esquemas** de la cuenta de integración de EIP, ahora debe ver el esquema recién agregado.  
![Captura de pantalla de EIP cuenta integración con "Esquemas" y el nuevo esquema resaltado](./media/app-service-logic-enterprise-integration-schemas/schema-41.png)
  

## <a name="edit-schemas"></a>Editar esquemas
1. Seleccione el icono de **esquemas** .  
2. Seleccione el esquema que desea editar en el módulo de **esquemas** que se abre.
3. En el módulo de **esquemas** , seleccione **Editar**.  
![Módulo de captura de pantalla de esquemas](./media/app-service-logic-enterprise-integration-schemas/edit-12.png)    
4. Seleccione el archivo de esquema que desea editar mediante el cuadro de diálogo Selector de archivo que se abre.
5. Seleccione **Abrir** en el selector de archivos.  
![Captura de pantalla de selector de archivos](./media/app-service-logic-enterprise-integration-schemas/edit-31.png)  
6. Recibir una notificación que indica que la carga se realizó correctamente.  

## <a name="delete-schemas"></a>Eliminar esquemas
1. Seleccione el icono de **esquemas** .  
2. Seleccione el esquema que desea eliminar de la hoja de **esquemas** que se abre.  
3. En el módulo de **esquemas** , seleccione **Eliminar**.
![Módulo de captura de pantalla de esquemas](./media/app-service-logic-enterprise-integration-schemas/delete-12.png)  

4. Para confirmar su elección, seleccione **Sí**.  
![Captura de pantalla de mensaje de confirmación de "Eliminar esquema"](./media/app-service-logic-enterprise-integration-schemas/delete-21.png)  
5. Por último, observe que se actualiza la lista de esquemas en el módulo de **esquemas** y el esquema eliminó ya no aparece.  
![Captura de pantalla de EIP cuenta integración con "Esquemas" resaltados](./media/app-service-logic-enterprise-integration-schemas/delete-31.png)    

## <a name="next-steps"></a>Pasos siguientes

- [Obtenga más información sobre el paquete de integración de Enterprise] (./app-service-logic-enterprise-integration-overview.md "Obtenga información sobre el paquete de integración de enterprise").  
