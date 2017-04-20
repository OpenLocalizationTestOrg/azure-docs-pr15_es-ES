<properties 
    pageTitle="Información general sobre X12 y el módulo de integración de empresa | Servicio de aplicaciones de Microsoft Azure | Microsoft Azure" 
    description="Aprenda a usar X12 agreements para crear aplicaciones de lógica" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="msftman" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="app-service-logic" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/08/2016" 
    ms.author="deonhe"/>

# <a name="enterprise-integration-with-x12"></a>Integración de Enterprise con X12 

>[AZURE.NOTE]Esta página abarca la X12 las características de lógica de aplicaciones. Para obtener información sobre EDIFACT, haga clic en [aquí](app-service-logic-enterprise-integration-edifact.md).

## <a name="create-an-x12-agreement"></a>Crear un X12 contrato 
Antes de que puede intercambiar X12 mensajes, deberá crear una X12 contrato y guardarlo en su cuenta de integración. Los siguientes pasos le guiará a través del proceso de creación de un X12 contrato.

### <a name="heres-what-you-need-before-you-get-started"></a>Esto es lo necesita antes de empezar
- Una [cuenta de integración](./app-service-logic-enterprise-integration-accounts.md) definido en su suscripción de Azure  
- Al menos dos [socios](./app-service-logic-enterprise-integration-partners.md) ya definido en su cuenta de integración  

>[AZURE.NOTE]Al crear un acuerdo, el contenido del archivo de contrato debe coincidir con el tipo de acuerdo.    


Después de haber [creado una cuenta de integración](./app-service-logic-enterprise-integration-accounts.md) y [asociados](./app-service-logic-enterprise-integration-partners.md), puede crear un X12 contrato siguiendo estos pasos:  

### <a name="from-the-azure-portal-home-page"></a>Desde la página principal del portal Azure

Después de iniciar sesión en el [portal de Azure](http://portal.azure.com "Azure portal"):  
1. En el menú de la izquierda, seleccione **Examinar** .  

>[AZURE.TIP]Si no ve el vínculo **Examinar** , debe expandir el menú en primer lugar. Haga esto seleccionando el vínculo **Mostrar menú** que se encuentra en la parte superior izquierda del menú contraído.  

![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. Escriba la *integración* en el cuadro de búsqueda de filtro, a continuación, seleccione **Cuentas de integración** de la lista de resultados.       
![](./media/app-service-logic-enterprise-integration-x12/x12-1-3.png)    
3. En el módulo de **Integración de cuentas** que se abre, seleccione la cuenta de integración en el que creará el contrato. Si no ve la integración de cuentas de listas, [crear una primera](./app-service-logic-enterprise-integration-accounts.md "All about integration accounts").  
![](./media/app-service-logic-enterprise-integration-x12/x12-1-4.png)  
4.  Seleccione el icono de **contratos** . Si no ve los contratos de mosaico, agregue en primer lugar.   
![](./media/app-service-logic-enterprise-integration-x12/x12-1-5.png)     
5. En el módulo de contratos que se abre, seleccione el botón **Agregar** .  
![](./media/app-service-logic-enterprise-integration-agreements/agreement-2.png)  
6. Escriba un **nombre** para el acuerdo, a continuación, seleccione el **tipo de acuerdo**, **Partner de Host**, **Identidad de Host**, **Invitado Partner** **Identidad de invitado**en el módulo de contratos que se abre.  
![](./media/app-service-logic-enterprise-integration-x12/x12-1.png)  
7. Una vez que ha establecido la recepción de propiedades de configuración, seleccione el botón **Aceptar**  
Vamos a seguir:  
8. Seleccione **Configuración de recepción** para configurar cómo mensajes recibidos a través de este acuerdo deben corregirse.  
9. El control de la configuración de recepción se divide en las siguientes secciones, incluidos los identificadores, confirmación, esquemas, sobres, números de Control, validaciones y valores internos. Configurar estas propiedades basándose en el acuerdo con el socio con que se intercambio de mensajes. Aquí es una vista de estos controles, configurarlas en función de cómo desea que este contrato para identificar y controlar los mensajes entrantes:  
![](./media/app-service-logic-enterprise-integration-x12/x12-2.png)  

![](./media/app-service-logic-enterprise-integration-x12/x12-3.png)  
10. Seleccione el botón **Aceptar** para guardar la configuración.  

### <a name="identifiers"></a>Identificadores

|(Propiedad)|Descripción |
|---|---|
|ISA1 (autorización calificador)|Seleccione el valor de calificador de autorización de la lista desplegable.|
|ISA2|Opcional. Escriba el valor de la información de autorización. Si el valor especificado para ISA1 sea 00, escriba un mínimo de un carácter alfanumérico y un máximo de 10.|
|ISA3 (calificador de seguridad)|Seleccione el valor de calificador de seguridad de la lista desplegable.|
|ISA4|Opcional. Escriba el valor de la información de seguridad. Si el valor especificado para ISA3 no sea 00, escriba un mínimo de un carácter alfanumérico y un máximo de 10.|

### <a name="acknowledgments"></a>Confirmaciones 

|(Propiedad)|Descripción |
|----|----|
|TA1 esperado|Seleccione esta casilla para devolver una confirmación (TA1) technical al remitente intercambio. Estas confirmaciones se envían al remitente de intercambio en función de la configuración de envío para el acuerdo.|
|FA esperada|Seleccione esta casilla para devolver una confirmación (FA) funcional al remitente intercambio. A continuación, seleccione si desea que las confirmaciones 997 o 999, basadas en las versiones de esquema que está trabajando. Estas confirmaciones se envían al remitente de intercambio en función de la configuración de envío para el acuerdo.|
|Incluir bucle AK2/IK2|Seleccione esta casilla para habilitar la generación de bucles AK2 en confirmaciones funcionales para conjuntos de transacciones aceptada. Nota: Esta casilla de verificación está habilitada sólo si seleccionó la casilla de verificación FA lo esperado.|

### <a name="schemas"></a>Esquemas

Elija un esquema para cada tipo de transacción (ST1) y la aplicación de remitente (GS2). La canalización de recepción separa el mensaje entrante haciendo coincidir los valores para ST1 y GS2 en el mensaje entrante con los valores definidos aquí y el esquema de los mensajes entrantes con el esquema que defina aquí.

|(Propiedad)|Descripción |
|----|----|
|Versión|Seleccione el X12 versión|
|Tipo de transacción (ST01)|Seleccione el tipo de transacción|
|Aplicación de remitente (GS02)|Seleccione la aplicación remitente|
|Esquema|Seleccione el archivo de esquema que desea nosotros. Archivos de esquema se encuentran en su cuenta de integración.|

### <a name="envelopes"></a>Sobres

|(Propiedad)|Descripción |
|----|----|
|Uso de ISA11|Use este campo para especificar el separador de un conjunto de transacciones:</br></br>Seleccione el identificador estándar que se utiliza la notación decimal de "." en lugar de la notación decimal del documento entrante en EDI canalización de recepción.</br></br>Seleccione el separador de repetición para especificar el separador repetidas repeticiones de un elemento de datos simple o una estructura de datos repetidos. Por ejemplo, (^) normalmente se usa como separador de repetición. Para los esquemas HIPAA, solo puede usar (^).|

### <a name="control-numbers"></a>Números de control

|(Propiedad)|Descripción |
|----|----|
|No permitir duplicados de intercambio de número de Control|Active esta opción para bloquear intercambios duplicados. Si selecciona esta opción, el Portal de servicios de BizTalk comprueba que el número de control de intercambio (ISA13) para el intercambio recibido no coincide con el número de control de intercambio. Si se detecta una coincidencia, la canalización de recepción no procesar el intercambio.<br/>Si decide para no permitir números de control de intercambio duplicados, puede especificar el número de días en el que la comprobación se realiza asignando el valor correspondiente para buscar duplicados ISA13 cada x días.|
|No permitir duplicados de número de control de grupo|Active esta opción para bloquear intercambios con números de control de grupo duplicado.|
|No permitir transacción conjunto control números duplicados|Active esta opción para bloquear intercambios con números de control del conjunto de transacción duplicada.|

### <a name="validations"></a>Validaciones

|(Propiedad)|Descripción |
|----|----|
|Tipo de mensaje|Tipo de mensaje EDI, como pedido de compra de 850 o confirmación de implementación de 999.|
|Validación de EDI|Realiza la validación de EDI en los tipos de datos definidos por las propiedades EDI del esquema, las restricciones de longitud, elementos de datos vacía y separadores al final.|
|Validación extendida|Si el tipo de datos no es EDI, validación está en el requisito de elemento de datos y permite la repetición, enumeraciones y validación de longitud de elemento de datos (min/max).|
|Permitir ceros líder o finales|Se conservan espacio adicional y cero caracteres que son iniciales o finales. No se quitan.|
|Directiva de al final separador|Genera separadores al final en el intercambio recibido. Opciones incluyen NotAllowed, opcional y obligatorio.|

### <a name="internal-settings"></a>Configuración interna

|(Propiedad)|Descripción |
|----|----|
|Convertir formato decimal implícito Nn base 10 valor numérico|Convierte a un número EDI que se especifica con el formato Nn en un valor numérico de base 10 en el código XML intermedio en el Portal de servicios de BizTalk.|
|Crear etiquetas XML vacías si se permiten separadores al final|Seleccione esta casilla para que el remitente de intercambio incluir etiquetas XML vacías para separadores al final.|
|Proceso por lotes de entrada|Intercambio de división como conjuntos de transacciones - suspender conjuntos de transacciones en error: analiza cada transacción establecer en un intercambio en otro documento XML aplicando los sobres adecuado para el conjunto de transacciones. Con esta opción, si establece una o más transacciones en el intercambio no validación, y servicios de BizTalk suspende solo los conjuntos de transacciones. </br></br>Intercambio de división como conjuntos de transacciones - suspender intercambio error: analiza cada transacción establecer en un intercambio en otro documento XML aplicando los sobres adecuado. Con esta opción, si transacción de uno o varios conjuntos en el intercambio no validación, y servicios de BizTalk suspende el intercambio completo.</br></br>Mantener el intercambio - suspender conjuntos de transacciones en error: permanecen intactos el intercambio, la creación de un documento XML para el intercambio por lotes completo. Con esta opción, si onAe o transacción más se establece en el intercambio no validación, y servicios de BizTalk suspende solo los conjuntos de transacciones, mientras sigue procesar todos los otros conjuntos de transacciones.</br></br>Mantener el intercambio - suspender intercambio error: permanecen intactos el intercambio, la creación de un documento XML para el intercambio por lotes completo. Con esta opción, si transacción de uno o varios conjuntos en el intercambio no validación, y servicios de BizTalk suspende el intercambio completo.</br></br>|

El contrato está preparado para administrar los mensajes entrantes que se ajustan al esquema seleccionado.

Para configurar las opciones que controlan los mensajes que envíe para los partners:  
11. Seleccione **Configuración de envío** para configurar cómo mensajes enviados a través de este acuerdo deben corregirse.  

El control de la configuración de envío se divide en las siguientes secciones, incluidos los identificadores, confirmación, esquemas, sobres, números de Control, juegos de caracteres y separadores y validación. 

Aquí es una vista de estos controles. Realice las selecciones en función de cómo desea controlar los mensajes que envíe para los partners a través de este acuerdo:   
![](./media/app-service-logic-enterprise-integration-x12/x12-4.png)  

![](./media/app-service-logic-enterprise-integration-x12/x12-5.png)  

![](./media/app-service-logic-enterprise-integration-x12/x12-6.png)  
12. Seleccione el botón **Aceptar** para guardar la configuración.  

### <a name="identifiers"></a>Identificadores
|(Propiedad)|Descripción |
|----|----|
|Calificador de autorización (ISA1)|Seleccione el valor de calificador de autorización de la lista desplegable.|
|ISA2|Escriba el valor de la información de autorización. Si este valor es distinto de 00, escriba un mínimo de un carácter alfanumérico y un máximo de 10.|
|Calificador de seguridad (ISA3)|Seleccione el valor de calificador de seguridad de la lista desplegable.|
|ISA4|Escriba el valor de la información de seguridad. Si este valor es distinto de 00, para el cuadro de texto valor (ISA4), escriba un mínimo de un valor alfanumérico y un máximo de 10.|

### <a name="acknowledgment"></a>Confirmación
|(Propiedad)|Descripción |
|----|----|
|TA1 esperado|Seleccione esta casilla para devolver una confirmación (TA1) technical al remitente intercambio. Esta opción especifica que el partner de host que envía el mensaje solicita confirmación el partner de invitado en el acuerdo. Estas confirmaciones se esperan por el partner de host en función de la configuración de recepción del acuerdo.|
|FA esperada|Seleccione esta casilla para devolver una confirmación (FA) funcional al remitente de intercambio y, a continuación, seleccione si desea que las confirmaciones 997 o 999, basadas en las versiones de esquema que está trabajando. Estas confirmaciones se esperan por el partner de host en función de la configuración de recepción del acuerdo.|
|Versión de FA|Seleccione la versión de FA|

### <a name="schemas"></a>Esquemas
|(Propiedad)|Descripción |
|----|----|
|Versión|Seleccione el X12 versión|
|Tipo de transacción (ST01)|Seleccione el tipo de transacción|
|ESQUEMA|Seleccione el esquema para usar. Los esquemas se encuentran en su cuenta de integración. Para obtener acceso a los esquemas, vincular primero su cuenta de integración a su aplicación lógica.|

### <a name="envelopes"></a>Sobres
|(Propiedad)|Descripción |
|----|----|
|Uso de ISA11|Use este campo para especificar el separador de un conjunto de transacciones:</br></br>Seleccione el identificador estándar que se utiliza la notación decimal de "." en lugar de la notación decimal del documento entrante en EDI canalización de recepción.</br></br>Seleccione el separador de repetición para especificar el separador repetidas repeticiones de un elemento de datos simple o una estructura de datos repetidos. Por ejemplo, (^) normalmente se usa como separador de repetición. Para los esquemas HIPAA, solo puede usar (^).</br>|
|Separador de repetición|Escriba el separador de repetición|
|Número de versión de control (ISA12)|Seleccione la versión de la X12 estándar que se utiliza el portal de servicios de BizTalk para generar un intercambio saliente.|
|Indicador de uso (ISA15)|Especifique si el contexto de un intercambio es información (I), los datos de producción (P) o (T) de datos de prueba. Recepción EDI canalización promueve esta propiedad al contexto.|
|Esquema|Puede especificar cómo el Portal de servicios de BizTalk genera los segmentos GS y ST para un intercambio codificada X12 que envía a la canalización de envío.</br></br>Puede asociar valores de la GS1, GS2, GS3, GS4, GS5, GS7 y GS8 elementos de datos con valores del tipo de transacción y elementos de datos o versión. Cuando el Portal de servicios de BizTalk determina que un mensaje XML tiene los valores definidos para el tipo de transacción y elementos de versión o de una fila de la cuadrícula, a continuación, rellena los elementos de datos GS1, GS2, GS3, GS4, GS5, GS7 y GS8 en los sobres de la intersección saliente con los valores de la misma fila de la cuadrícula. Los valores del tipo de transacción y elementos de versión o deben ser únicos.</br></br>Opcional. Para GS1, seleccione un valor para el código funcional de la lista desplegable.</br></br>Obligatorio. Para GS2, escriba un valor alfanumérico para el remitente de la aplicación con un mínimo de dos caracteres y un máximo de 15 caracteres.</br></br>Obligatorio. Para GS3, escriba un valor alfanumérico para el receptor de la aplicación con un mínimo de dos caracteres y un máximo de 15 caracteres.</br></br>Opcional. Para GS4, seleccione AAAAMMDD o AAMMDD.</br></br>Opcional. Para GS5, seleccione HHMM HHMMSS o HHMMSSdd.</br></br>Opcional. Para GS7, seleccione un valor para la Agencia responsable de la lista desplegable.</br></br>Opcional. Para GS8, escriba un valor alfanumérico para el documento que se identifica con un mínimo de un carácter y un máximo de 12 caracteres.</br></br>**Nota**: estos son los valores que el Portal de servicios de BizTalk se escribe en los campos GS del intercambio generar si el tipo de transacción y elementos de versión o en la misma fila son una coincidencia con los asociados a la intersección.|

### <a name="control-numbers"></a>Números de control
|(Propiedad)|Descripción |
|----|----|
|Intercambio de Control de número (ISA13)|Obligatorio. Especifique un rango de valores para el número de control de intercambio utilizado por el Portal de servicios de BizTalk para generar un intercambio saliente. Escriba un valor numérico con un mínimo de 1 y un máximo de 999999999.|
|Número de Control de grupo (GS06)|Obligatorio. Escriba el rango de números que debe usar el Portal de servicios de BizTalk para el número de control de grupo. Escriba un valor numérico con un mínimo de un carácter y un máximo de nueve caracteres.|
|Número de Control del conjunto de transacciones (ST02)|Número de transacciones definir Control (ST02), especifique un rango de valores numéricos para los campos obligatorios central y valores alfanuméricos para opcional prefijo y sufijo. La longitud máxima de los cuatro campos es nueve caracteres.|
|Prefijo|Para designar el rango de números de control de conjunto de transacción utilizados en una confirmación, escriba valores en los campos de número (ST02) de Control de ACK. Escriba un valor numérico para los dos campos en el medio y un valor alfanumérico (si lo desea) de los campos prefijo y sufijo. Los campos en el medio necesarios y contienen los valores mínimos y máximos para el número de control; el prefijo y sufijo son opcionales. La longitud máxima de los tres campos es nueve caracteres.|
|Sufijo|Para designar el rango de números de control de conjunto de transacción utilizados en una confirmación, escriba valores en los campos de número (ST02) de Control de ACK. Escriba un valor numérico para los dos campos en el medio y un valor alfanumérico (si lo desea) de los campos prefijo y sufijo. Los campos en el medio necesarios y contienen los valores mínimos y máximos para el número de control; el prefijo y sufijo son opcionales. La longitud máxima de los tres campos es nueve caracteres.|

### <a name="character-sets-and-separators"></a>Separadores y juegos de caracteres
Que el conjunto de caracteres, puede especificar un conjunto diferente de delimitadores que se usará para cada tipo de mensaje. Si no se especifica un juego de caracteres de un esquema de mensaje especificado, se utiliza el juego de caracteres predeterminado.

|(Propiedad)|Descripción |
|----|----|
|Juego de caracteres para usarse|Seleccione el X12 juego de caracteres para validar las propiedades que especifica para el contrato.</br></br>**Nota**: el Portal de servicios de BizTalk solo usa esta configuración para validar los valores especificados para las propiedades de contrato relacionados. La canalización de recepción o la canalización de envío pasa por alto esta propiedad juego de caracteres al realizar el procesamiento de tiempo de ejecución.|
|Esquema|Símbolo de selección (+) y seleccione un esquema de la lista desplegable. Para ver el esquema seleccionado, seleccione los separadores establecidos para usarse:</br></br>Separador de elementos de componente: especifique un único carácter para separar los elementos de datos compuesto.</br></br>Separador de elementos de datos: especifique un único carácter para separar los elementos de datos simple dentro de datos compuesto.</br></br></br></br>Carácter de reemplazo: seleccione esta casilla si la carga de datos contienen caracteres que también se usan como datos, segmento o separadores de componente. A continuación, puede escribir un carácter de reemplazo. Cuando se genera la salida mensaje X12, todas las instancias de caracteres separadores de la carga los datos se reemplazan con el carácter especificado.</br></br>Terminador de segmento: escriba un único carácter para indicar el final de un segmento EDI.</br></br>Sufijo: seleccione el carácter que se usa con el identificador de segmento. Si se designa un sufijo, el elemento de datos de segmento terminador puede estar vacío. Si el terminador de segmento está vacío, debe designar un sufijo.|

### <a name="validation"></a>Validación
|(Propiedad)|Descripción |
|----|----|
|Tipo de mensaje|Al seleccionar esta opción habilita la validación en el receptor de intercambio. Esta validación realiza la validación de EDI en elementos de datos de conjunto de transacciones, validar los tipos de datos, las restricciones de longitud y elementos de datos vacía y finales separadores.|
|Validación de EDI||
|Validación extendida|Al seleccionar esta opción habilita la validación ampliada de intercambios recibida del remitente intercambio. Esto incluye la validación de longitud del campo, opcionalidad y número de repeticiones además validación de tipo de datos XSD. Puede habilitar la validación de extensión sin habilitar validación EDI, o viceversa.|
|Permitir que los ceros iniciales o finales|Si selecciona esta opción especifica que un intercambio EDI recibido de la parte no validación si un elemento de datos en un intercambio EDI no se ajusta a sus requisitos de longitud debido o espacios finales, pero que se ajusten a sus requisitos de longitud cuando se eliminan.|
|Al final separador|Si selecciona esta opción especifica un intercambio EDI recibido de la parte no falla la validación si un elemento de datos en un intercambio EDI no se ajusta a sus requisitos de longitud debido a ceros iniciales (o finales) o espacios finales, pero que se ajusten a sus requisitos de longitud cuando se eliminan.</br></br>Si no desea permitir finales delimitadores y separadores de un intercambio recibido del remitente intercambio, seleccione no permitido. Si el intercambio contiene finales delimitadores y separadores, se declare no válidos.</br></br>Seleccione opcional para aceptar intercambios con o sin separadores y delimitadores finales.</br></br>Seleccione obligatorio si debe contener el intercambio recibido finales delimitadores y separadores.|

Después de seleccionar **Aceptar** en los módulos abiertos:  
13. Seleccione el mosaico de **contratos** en el módulo de integración cuenta y verá el contrato recién agregado aparece.  
![](./media/app-service-logic-enterprise-integration-x12/x12-7.png)   

## <a name="learn-more"></a>Aprende más
- [Obtenga más información sobre el paquete de integración de Enterprise] (./app-service-logic-enterprise-integration-overview.md "Obtenga más información sobre el paquete de integración de Enterprise")  
