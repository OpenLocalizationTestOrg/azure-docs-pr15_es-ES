<properties 
   pageTitle="Crear un acuerdo con el socio comercial en el servicio de aplicación de Azure | Microsoft Azure" 
   description="Crear cotización Agreements Partner" 
   services="logic-apps" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
    ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="08/23/2016"
   ms.author="rajram"/>

# <a name="creating-a-trading-partner-agreement"></a>Crear un acuerdo con el socio comercial   

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Socios son las entidades implicados en B2B comunicaciones (para empresas). Cuando dos asociados de establecer una relación, esto se conoce como un *contrato*. El contrato definido se basa en la comunicación los dos socios desea lograr y protocolo o transporte específico. Los distintos protocolos B2B y transportes admitidos por el servicio de aplicación de Azure incluyen:

- AS2 (aplicación instrucción 2)
- EDIFACT (las Naciones Unidas o electrónicos intercambio de datos para la administración, el comercio y transporte (UN/EDIFACT))
- X12 (ASC X12)

### <a name="biztalk-api-apps-that-support-b2b-scenarios"></a>Aplicaciones de la API de BizTalk que admiten escenarios B2B
Las siguientes aplicaciones de API habilitar estas capacidades con una experiencia intuitiva y enriquecida en el Portal de Azure:


## <a name="biztalk-trading-partner-management-tpm"></a>BizTalk (TPM) de administración de socios comerciales
- Creación y administración de Partners, perfiles e identidades
- Almacenamiento y administración de esquemas EDI
- Almacenamiento y administración de certificados (que se usa en AS2 protocolo)
- Creación y administración de contratos de AS2
- Creación y administración de contratos de EDIFACT (incluye procesamiento por lotes en el lado de envío)
- Creación y administración de X12 Agreements (incluye procesamiento por lotes en el lado de envío)

![][1]


## <a name="as2-connector"></a>Conector AS2
- Ejecuta AS2 Agreements según se define en la instancia de aplicación de la API de TPM relacionada
- Expone información de procesamiento y seguimiento de AS2 para solucionar este problema


## <a name="biztalk-edifact"></a>BizTalk EDIFACT
- Ejecuta EDIFACT Agreements según se define en la instancia de aplicación de la API de TPM relacionada
- Expone información de procesamiento y seguimiento de EDIFACT para solucionar este problema
- Proporciona administración de estado de lotes (iniciar y detener) según se define en EDIFACT contratos de la instancia de aplicación de la API de TPM relacionada


## <a name="biztalk-x12"></a>BizTalk X12
- Ejecuta X12 Agreements según se define en la instancia de aplicación de la API de TPM relacionada 
- Surge X12 información para solucionar problemas de procesamiento y seguimiento
- Proporciona administración de estado de lotes (iniciar y detener) según se define en X12 contratos de la instancia de aplicación de la API de TPM relacionada

Como se ha dicho AS2, X 12 y EDIFACT API aplicaciones requieren una aplicación de la API de TPM a función según lo esperado.


## <a name="getting-started"></a>Introducción
Para crear los contratos comerciales de partner:

1. Crear una instancia del conector de **Administración de socios comerciales de BizTalk** . Esto requiere una base de datos de SQL en blanco a función. Antes de iniciar Asegúrese de tener una base de datos en blanco disponible y listo para su uso.
2. Cargar esquemas y certificados según sea necesario por los contratos. Para ello, la instancia TPM creadas y entrar en el elemento 'Esquemas' o 'Certificados' de exploración
3. Vaya a la instancia TPM creada y recorrer todo el elemento de **socios**
4. Crear a socios como desee. También editar los perfiles según corresponda y agregue las identidades necesarias
5. Ahora puede usar el elemento de **contratos** para crear contratos. Cuando se crea un acuerdo, debe seleccionar el protocolo que se usará. El resto de opciones de configuración se basa en el protocolo que ha seleccionado.

![][2]

![][3]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-trading-partner-agreement/TPMResourceView.png
[2]: ./media/app-service-logic-create-a-trading-partner-agreement/ProtocolSelection.png
[3]: ./media/app-service-logic-create-a-trading-partner-agreement/X12AgreementCreation.png
 
