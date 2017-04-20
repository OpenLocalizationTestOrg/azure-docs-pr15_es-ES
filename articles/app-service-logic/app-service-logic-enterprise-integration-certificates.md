
<properties
    pageTitle="Uso de certificados con paquete de integración de Enterprise | Microsoft Azure"
    description="Obtenga información sobre cómo usar certificados con el paquete de integración de empresa y aplicaciones de lógica"
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
    ms.date="09/06/2016"
    ms.author="deonhe"/>

# <a name="learn-about-certificates-and-enterprise-integration-pack"></a>Obtenga más información sobre los certificados y paquete de integración de Enterprise

## <a name="overview"></a>Información general
Integración de Enterprise utiliza certificados para B2B comunicaciones seguras. Puede usar dos tipos de certificados en las aplicaciones de integración de enterprise:

- Certificados públicos, que se deben comprar una entidad de certificación (CA).
- Certificados privados, puede emitir usted mismo. Estos certificados también se conoce como certificados firmados.


## <a name="what-are-certificates"></a>¿Qué son los certificados?
Los certificados son documentos digitales que comprobar la identidad de los participantes de comunicaciones electrónicas y que también proteger comunicaciones electrónicas.

## <a name="why-use-certificates"></a>¿Por qué usar certificados?
A veces B2B comunicaciones deben mantenerse confidenciales. Integración de Enterprise utiliza certificados para proteger estas comunicaciones de dos maneras:

- Al cifrar el contenido de los mensajes
- Por firmar digitalmente los mensajes  

## <a name="how-do-you-upload-certificates"></a>¿Cómo se cargan certificados?

### <a name="public-certificates"></a>Certificados públicos
Para usar un *certificado público* en las aplicaciones de lógica con capacidades de B2B, debe cargar el certificado en su cuenta de integración. Para usar un *certificado autofirmado*, por otro lado, primero debe cargarlo [Depósito de clave de Azure](../key-vault/key-vault-get-started.md "Obtenga más información sobre la clave de cámara").

Después de cargar un certificado, está disponible para ayudar a proteger los mensajes de B2B al definir sus propiedades en los [contratos](./app-service-logic-enterprise-integration-agreements.md) que cree.  

Estos son los pasos detallados para cargar los certificados públicos en su cuenta de integración tras iniciar sesión el portal de Azure:

1. Seleccione **Examinar**.  
    ![Seleccione Examinar](./media/app-service-logic-enterprise-integration-overview/overview-1.png)  

2. Escriba **integración** en el cuadro de búsqueda de filtro y, a continuación, seleccione **Cuentas de integración** de la lista de resultados.     
    ![Seleccione cuentas de integración](./media/app-service-logic-enterprise-integration-overview/overview-2.png)

3. Seleccione la cuenta de integración a la que desea agregar el certificado.  
    ![Seleccione la cuenta de integración a la que desea agregar el certificado](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  

4.  Seleccione el icono de **certificados** .  
    ![Seleccione el icono de certificados](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)

5. En el módulo de **certificados** que se abre, seleccione el botón **Agregar** .
    ![Seleccione el botón Agregar](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)

6. Escriba un **nombre** para el certificado y, a continuación, seleccione el tipo de certificado. (En este ejemplo, hemos usado el tipo de certificado público). A continuación, seleccione el icono de carpeta en el lado derecho del cuadro de texto de **certificado** .

7. Cuando se abre el selector de archivos, busque y seleccione el archivo de certificado que desee cargar en su cuenta de integración.

8. Seleccione el certificado y, a continuación, seleccione **Aceptar** en el selector de archivo. Esto valida y carga el certificado para su cuenta de integración.

8. Por último, en el módulo de **certificado de agregar** , seleccione el botón **Aceptar** .  
    ![Seleccione el botón Aceptar](./media/app-service-logic-enterprise-integration-certificates/certificate-3.png)  

9. En aproximadamente un minuto, verá una notificación que indica que ha finalizado la carga de certificado.

10. Seleccione el icono de **certificados** . Debe ver el certificado recién agregado.  
    ![Ver el nuevo certificado](./media/app-service-logic-enterprise-integration-certificates/certificate-4.png)  

### <a name="private-certificates"></a>Certificados privados
Puede cargar certificados privados en su cuenta de integración realizando los siguientes pasos:  

1. [Cargar su clave privada en depósito de clave] (../key-vault/key-vault-get-started.md "Obtenga más información sobre depósito clave").  

    > [AZURE.TIP] Debe autorizar la característica lógica aplicaciones de servicio de aplicación de Azure para realizar operaciones en depósito de clave. Puede conceder acceso a la entidad de seguridad de servicio de aplicaciones de lógica mediante el siguiente comando PowerShell:`Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`  

2. Crear un certificado privado.  

3. Cargar el certificado privado a su cuenta de integración.

Una vez que haya tomado los pasos anteriores, puede usar el certificado privado para crear contratos.

Siguientes son los pasos detallados para cargar los certificados privados en su cuenta de integración tras iniciar sesión el portal de Azure:  

1. Seleccione **Examinar**.  
    ![Cargar sus certificados privados en su cuenta de integración](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    

2. Escriba **integración** en el cuadro de búsqueda de filtro y, a continuación, seleccione **Cuentas de integración** de la lista de resultados.     
    ![Seleccione cuentas de integración](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  

3. Seleccione la cuenta de integración a la que desea agregar el certificado.  
    ![Seleccione la cuenta de integración a la que desea agregar el certificado](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  

4. Seleccione el icono de **certificados** .  
    ![Seleccione el icono de certificados](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)  

5. En el módulo de **certificados** que se abre, seleccione el botón **Agregar** .
    ![Seleccione el botón Agregar](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)

6. Escriba un **nombre** para el certificado y, a continuación, seleccione el tipo de certificado. (En este ejemplo, hemos usado el tipo de certificado público). A continuación, seleccione el icono de carpeta en el lado derecho del cuadro de texto de **certificado** .

7. Cuando se abre el selector de archivos, busque y seleccione el archivo de certificado que desee cargar en su cuenta de integración.

8. Una vez que ha seleccionado el certificado, seleccione **Aceptar** en el selector de archivo. Esta acción Valida el certificado y cargas a su cuenta de integración.

9. Por último, en el módulo de **certificado de agregar** , seleccione el botón **Aceptar** .  
    ![Agregar el certificado](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-1.png)  

10. En aproximadamente un minuto, verá una notificación que indica que ha finalizado la carga de certificado.

11. Seleccione el icono de **certificados** . Debe ver el certificado recién agregado.
    ![Ver el nuevo certificado](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-2.png)  

Después de cargar un certificado, está disponible para ayudar a proteger los mensajes de B2B al definir sus propiedades en [contratos](./app-service-logic-enterprise-integration-agreements.md).  

## <a name="next-steps"></a>Pasos siguientes
- [Crear una aplicación de la lógica que usa las características de B2B](./app-service-logic-enterprise-integration-b2b.md)  
- [Crear un acuerdo de B2B](./app-service-logic-enterprise-integration-agreements.md)  
- [Más información sobre la clave de cámara] (../key-vault/key-vault-get-started.md "Obtenga más información sobre depósito clave")  
