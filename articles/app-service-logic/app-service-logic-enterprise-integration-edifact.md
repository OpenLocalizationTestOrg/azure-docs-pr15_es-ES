<properties 
    pageTitle="Integración de Enterprise con EDIFACT | Microsoft Azure" 
    description="Obtenga información sobre cómo usar agreements EDIFACT para crear aplicaciones de lógica" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="jeffhollan" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="app-service-logic" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/26/2016" 
    ms.author="jonfan"/>

# <a name="enterprise-integration-with-edifact"></a>Integración de Enterprise con EDIFACT 

> [AZURE.NOTE] Esta página describe las características EDIFACT de lógica de aplicaciones. Para obtener información sobre X12, haga clic en [aquí](app-service-logic-enterprise-integration-x12.md).

## <a name="create-an-edifact-agreement"></a>Crear un acuerdo EDIFACT 
Antes de que puede intercambiar mensajes EDIFACT, debe crear un acuerdo EDIFACT y guardarlo en su cuenta de integración. Los siguientes pasos le guiará a través del proceso de creación de un acuerdo EDIFACT.

### <a name="heres-what-you-need-before-you-get-started"></a>Esto es lo necesita antes de empezar
- Una [cuenta de integración](./app-service-logic-enterprise-integration-accounts.md) definido en su suscripción de Azure  
- Al menos dos [socios](./app-service-logic-enterprise-integration-partners.md) ya definido en su cuenta de integración  

>[AZURE.NOTE]Al crear un acuerdo, el contenido de los mensajes que se enviar y que recibir a y desde el partner debe coincidir con el tipo de acuerdo.    


Después de haber [creado una cuenta de integración](./app-service-logic-enterprise-integration-accounts.md) y [asociados](./app-service-logic-enterprise-integration-partners.md), puede crear un acuerdo EDIFACT siguiendo estos pasos:  

### <a name="from-the-azure-portal-home-page"></a>Desde la página principal del portal Azure

Después de iniciar sesión en el [portal de Azure](http://portal.azure.com "Azure portal"):  
1. En el menú de la izquierda, seleccione **Examinar** .  

>[AZURE.TIP]Si no ve el vínculo **Examinar** , debe expandir el menú en primer lugar. Haga esto seleccionando el vínculo **Mostrar menú** que se encuentra en la parte superior izquierda del menú contraído.  

![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-0.png)    
2. Escriba la *integración* en el cuadro de búsqueda de filtro, a continuación, seleccione **Cuentas de integración** de la lista de resultados.       
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-3.png)    
3. En el módulo de **Integración de cuentas** que se abre, seleccione la cuenta de integración en el que creará el contrato. Si no ve la integración de cuentas de listas, [crear una primera](./app-service-logic-enterprise-integration-accounts.md "All about integration accounts").  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-4.png)  
4.  Seleccione el icono de **contratos** . Si no ve los contratos de mosaico, agregue en primer lugar.   
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-5.png)     
5. En el módulo de contratos que se abre, seleccione el botón **Agregar** .  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-agreement-2.png)  
6. Escriba un **nombre** para el acuerdo, a continuación, seleccione el **tipo de acuerdo** para EDIFACT, **Partner de Host**, **Identidad de Host**, **Invitado asociado**, **Identidad de invitado**en el módulo de contratos que se abre.  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1.png)  
7. Después de establecer las propiedades de contrato, seleccione **Configuración de recepción** para configurar cómo mensajes recibidos a través de este acuerdo deben corregirse.  
8. El control de la configuración de recepción se divide en las siguientes secciones, incluidos los identificadores, confirmación, esquemas, números de Control, validación, valores internos y proceso por lotes. Configurar estas propiedades basándose en el acuerdo con el socio con que se intercambio de mensajes. Aquí es una vista de estos controles, configurarlas en función de cómo desea que este contrato para identificar y controlar los mensajes entrantes:  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-2.png)  
9. Seleccione el botón **Aceptar** para guardar la configuración.  

### <a name="identifiers"></a>Identificadores

|(Propiedad)|Descripción |
|---|---|
|UNB6.1 (contraseña de destinatarios referencia)|Escriba un valor alfanumérico comprendido entre 1 y 14 caracteres.|
|UNB6.2 (referencia destinatarios calificador)|Escriba un valor con un mínimo de un carácter y un máximo de dos caracteres alfanumérico.|

### <a name="acknowledgments"></a>Confirmaciones 

|(Propiedad)|Descripción |
|----|----|
|Recibo del mensaje (CONTRL)|Seleccione esta casilla para devolver una confirmación (CONTRL) técnica al remitente intercambio. Se envía la confirmación al remitente de intercambio en función de la configuración de envío para el acuerdo.|
|Confirmación (CONTRL)|Seleccione esta casilla para devolver una confirmación (CONTRL) funcional al remitente intercambio la confirmación se envía al remitente de intercambio en función de la configuración de envío para el acuerdo.|

### <a name="schemas"></a>Esquemas

|(Propiedad)|Descripción |
|----|----|
|UNH2.1 (TIPO)|Seleccione un tipo de conjunto de transacciones.|
|UNH2.2 (VERSIÓN)|Escriba el número de versión de mensaje. (Como mínimo, un carácter; máximo, tres caracteres).|
|UNH2.3 (LANZAMIENTO)|Escriba el número de versión de mensaje. (Como mínimo, un carácter; máximo, tres caracteres).|
|UNH2.5 (CÓDIGO ASIGNADO ASOCIADO)|Escriba el código asignado. (Máximo, seis caracteres. Debe ser alfanumérico).|
|UNG2.1 (APLICACIÓN DE ID. DE REMITENTE)|Escriba un valor con un mínimo de un carácter y un máximo de 35 caracteres alfanumérico.|
|UNG2.2 (CALIFICADOR DEL CÓDIGO DE APLICACIÓN REMITENTE)|Escriba un valor alfanumérico con un máximo de cuatro caracteres.|
|ESQUEMA|Seleccione el esquema cargado previamente que desee usar en su cuenta de integración asociada.|

### <a name="control-numbers"></a>Números de control

|(Propiedad)|Descripción |
|----|----|
|No permitir duplicados de intercambio de número de Control|Seleccione esta casilla para bloquear intercambios duplicados. Si se selecciona, la acción de descodificar EDIFACT comprueba que el número de control de intercambio (UNB5) para el intercambio recibido no coincide con un número de control de intercambio procesados anteriormente. Si se detecta una coincidencia, a continuación, no se procesa el intercambio.
|Buscar duplicados UNB5 cada (días)|Si decide para no permitir números de control de intercambio duplicados, puede especificar el número de días en el que la comprobación se realiza asignando el valor adecuado para la opción de **Buscar duplicados UNB5 cada (días)** .|
|No permitir duplicados de número de control de grupo|Seleccione esta casilla para bloquear intercambios con números de control de grupo duplicado (UNG5).|
|No permitir transacción conjunto control números duplicados|Seleccione esta casilla para bloquear intercambios con números de control del conjunto de transacción duplicada (UNH1).|
|Número de Control de confirmación de EDIFACT|Para designar los números de referencia de conjunto de transacciones que se utilizará en una confirmación, escriba un valor para el prefijo, un rango de números de referencia y un sufijo.|

### <a name="validations"></a>Validaciones

|(Propiedad)|Descripción |
|----|----|
|Tipo de mensaje|Especifique el tipo de mensaje. Tal como se haya completado cada fila de validación, otro se agregará automáticamente. Si no se especifican ningún reglas, la fila marcada como predeterminada se usa para la validación.|
|Validación de EDI|Seleccione esta casilla para realizar la validación de EDI en los tipos de datos definidos por las propiedades EDI del esquema, las restricciones de longitud, elementos de datos vacía y separadores al final.|
|Validación extendida|Seleccione esta casilla para habilitar validación (XSD) ampliada de intercambios recibida del remitente intercambio. Esto incluye la validación de longitud del campo, opcionalidad y número de repeticiones además validación de tipo de datos XSD.|
|Permitir ceros líder o finales|Seleccione **Permitir** para permitir líder o ceros a la derecha; Ceros **NotAllowed** para no permitir el interlineado y ceros finales o **Recortar** para recortar el interlineado y finales.|
|Recortar ceros líder o finales|Active esta casilla para recortar los ceros iniciales o finales|
|Directiva de al final separador|Si no desea permitir finales delimitadores y separadores de un intercambio recibido del remitente intercambio, seleccione **No permitido** . Si el intercambio contiene finales delimitadores y separadores, se declare no válidos. Seleccione **opcional** para aceptar intercambios con o sin separadores y delimitadores finales. Seleccione **obligatorio** si debe contener el intercambio recibido finales delimitadores y separadores.|

### <a name="internal-settings"></a>Configuración interna

|(Propiedad)|Descripción |
|----|----|
|Crear etiquetas XML vacías si se permiten separadores al final|Seleccione esta casilla para que el remitente de intercambio incluir etiquetas XML vacías para separadores al final.|
|Proceso por lotes de entrada|Las opciones incluyen:</br></br>**Intercambio de división como conjuntos de transacciones - suspender conjuntos de transacciones en Error**: analiza cada transacción establecer en un intercambio en otro documento XML aplicando los sobres adecuado para el conjunto de transacciones. Con esta opción si uno o varios conjuntos de transacciones en el intercambio de un error de validación, solo los conjuntos de transacciones se suspenden. Intercambio de división como conjuntos de transacciones - suspender intercambio error: analiza cada transacción establecer en un intercambio en otro documento XML aplicando los sobres adecuado. Con esta opción, si establece una o más transacciones en el intercambio no validación, a continuación, el intercambio completo será suspendido.</br></br>**Intercambio de conservar - suspender conjuntos de transacciones en Error**: permanecen intactos el intercambio, la creación de un documento XML para el intercambio por lotes completo. Con esta opción, si uno o varios conjuntos de transacciones en el intercambio de un error de validación, a continuación, solo los conjuntos de transacciones se suspenden, mientras se procesan todos los otros conjuntos de transacciones.</br></br>**Intercambio de conservar - suspender intercambio error**: permanecen intactos el intercambio, la creación de un documento XML para el intercambio por lotes completo. Con esta opción, si establece una o más transacciones en el intercambio no validación, a continuación, se suspende el intercambio completo.|

El contrato está preparado para administrar los mensajes entrantes que se ajusten a los valores seleccionados.

Para configurar las opciones que controlan los mensajes que envíe para los partners:  
10. Seleccione **Configuración de envío** para configurar cómo mensajes enviados a través de este acuerdo deben corregirse.  

El control de la configuración de envío se divide en las siguientes secciones, incluidos los identificadores, confirmación, esquemas, sobres, juegos de caracteres y separadores, números de Control y validación. 

Aquí es una vista de estos controles. Realice las selecciones en función de cómo desea controlar los mensajes que envíe para los partners a través de este acuerdo:   
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-3.png)    
11. Seleccione el botón **Aceptar** para guardar la configuración.  

### <a name="identifiers"></a>Identificadores
|(Propiedad)|Descripción |
|----|----|
|UNB1.2 (versión de sintaxis)|Seleccione un valor entre **1** y **4**.|
|UNB2.3 (dirección enrutamiento inverso de remitente)|Escriba un valor alfanumérico con un mínimo de un carácter y un máximo de 14 caracteres.|
|UNB3.3 (dirección de enrutamiento inverso destinatarios)|Escriba un valor alfanumérico con un mínimo de un carácter y un máximo de 14 caracteres.|
|UNB6.1 (contraseña de destinatarios referencia)|Escriba un valor alfanumérico con un mínimo de una y un máximo de 14 caracteres.|
|UNB6.2 (referencia destinatarios calificador)|Escriba un valor con un mínimo de un carácter y un máximo de dos caracteres alfanumérico.|
|UNB7 (Id. de referencia de aplicación)|Escriba un valor alfanumérico con un mínimo de un carácter y un máximo de 14 caracteres|

### <a name="acknowledgment"></a>Confirmación
|(Propiedad)|Descripción |
|----|----|
|Recibo del mensaje (CONTRL)|Seleccione esta casilla si el partner hospedado espera recibir para recibir una confirmación (CONTRL) técnica. Esta opción especifica que socios, que envía el mensaje, solicita confirmación el partner de invitado.|
|Confirmación (CONTRL)|Seleccione esta casilla si el partner hospedado espera recibir una confirmación funcional (CONTRL). Esta opción especifica que socios, que envía el mensaje, solicita confirmación el partner de invitado.|
|Generar SG1 y SG4 bucle para conjuntos de transacciones aceptada|Si decide solicitar una confirmación funcional, seleccione esta casilla Forzar generación de bucles SG1 y SG4 en confirmaciones CONTRL funcionales para conjuntos de transacciones aceptada.|

### <a name="schemas"></a>Esquemas
|(Propiedad)|Descripción |
|----|----|
|UNH2.1 (TIPO)|Seleccione un tipo de conjunto de transacciones.|
|UNH2.2 (VERSIÓN)|Escriba el número de versión de mensaje.|
|UNH2.3 (LANZAMIENTO)|Escriba el número de versión de mensaje.|
|ESQUEMA|Seleccione el esquema para usar. Los esquemas se encuentran en su cuenta de integración. Para obtener acceso a los esquemas, vincular primero su cuenta de integración a su aplicación lógica.|

### <a name="envelopes"></a>Sobres
|(Propiedad)|Descripción |
|----|----|
|UNB8 (código de prioridad de procesamiento)|Escriba un valor por orden alfabético que no más de un carácter.|
|UNB10 (comunicación acuerdo)|Escriba un valor alfanumérico con un mínimo de un carácter y un máximo de 40 caracteres.|
|UNB11 (indicador de prueba)|Seleccione esta casilla para indicar que genera el intercambio de datos de prueba|
|Aplicar UNA segmento (consejos de cadena de servicio)|Seleccione esta casilla para generar un segmento UNA para el intercambio que se envíen.|
|Aplicar UNG segmentos (encabezado de grupo de la función)|Seleccione esta casilla para crear segmentos de agrupamiento en el encabezado de grupo funcional en los mensajes enviados al asociado de invitado. Para crear los segmentos UNG, se usan los siguientes valores:</br></br>Para **UNG1**, escriba un valor alfanumérico con un mínimo de un carácter y un máximo de seis caracteres.</br></br>Para **UNG2.1**, escriba un valor con un mínimo de un carácter y un máximo de 35 caracteres alfanumérico.</br></br>Para **UNG2.2**, escriba un valor alfanumérico con un máximo de cuatro caracteres.</br></br>Para **UNG3.1**, escriba un valor con un mínimo de un carácter y un máximo de 35 caracteres alfanumérico.</br></br>Para **UNG3.2**, escriba un valor alfanumérico con un máximo de cuatro caracteres.</br></br>Para **UNG6**, escriba un valor con un mínimo de una y un máximo de tres caracteres alfanumérico.</br></br>Para **UNG7.1**, escriba un valor con un mínimo de un carácter y un máximo de tres caracteres alfanumérico.</br></br>Para **UNG7.2**, escriba un valor con un mínimo de un carácter y un máximo de tres caracteres alfanumérico.</br></br>Para **UNG7.3**, escriba un valor con un mínimo de 1 caracteres y un máximo de 6 caracteres alfanumérico.</br></br>Para **UNG8**, escriba un valor alfanumérico con un mínimo de un carácter y un máximo de 14 caracteres.|

### <a name="character-sets-and-separators"></a>Separadores y juegos de caracteres
Que el conjunto de caracteres, puede especificar un conjunto diferente de delimitadores que se usará para cada tipo de mensaje. Si no se especifica un juego de caracteres de un esquema de mensaje especificado, se utiliza el juego de caracteres predeterminado.

|(Propiedad)|Descripción |
|----|----|
|UNB1.1 (identificador del sistema)|Seleccione el carácter EDIFACT establecido se aplicará en el intercambio de correo saliente.|
|Esquema|Seleccione un esquema de la lista desplegable. Como se ha completado cada fila se agrega una nueva fila. Para ver el esquema seleccionado, seleccione los separadores establecidos para usarse:</br></br>**Separador de elementos de componente** : especifique un único carácter para separar los elementos de datos compuesto.</br></br>**Separador de elementos de datos** : especifique un único carácter para separar los elementos de datos simple dentro de datos compuesto.</br></br></br></br>**Carácter de reemplazo** : seleccione esta casilla si la carga de datos contienen caracteres que también se usan como datos, segmento o separadores de componente. A continuación, puede escribir un carácter de reemplazo. Cuando se genera el mensaje saliente de EDIFACT, todas las instancias de caracteres separadores en los datos de carga se reemplazan por el carácter especificado.</br></br>**Terminador de segmento** : especifique un único carácter para indicar el final de un segmento EDI.</br></br>**Sufijo** : seleccione el carácter que se usa con el identificador de segmento. Si se designa un sufijo, el elemento de datos de segmento terminador puede estar vacío. Si el terminador de segmento está vacío, debe designar un sufijo.|

### <a name="control-numbers"></a>Números de control
|(Propiedad)|Descripción |
|----|----|
|UNB5 (número de Control de intercambio)|Escriba un prefijo, un intervalo de valores para el número de control de intercambio y un sufijo. Estos valores se utilizan para generar un intercambio saliente. El prefijo y sufijo son opcionales; se requiere el número de control. Se incrementa el número de control para cada mensaje nuevo; el prefijo y sufijo son las mismas.|
|UNG5 (número de Control de grupo)|Escriba un prefijo, un intervalo de valores para el número de control de intercambio y un sufijo. Estos valores se utilizan para generar el número de control de grupo. El prefijo y sufijo son opcionales; se requiere el número de control. Se incrementa el número de control para cada mensaje nuevo hasta que se alcance el valor máximo; el prefijo y sufijo son las mismas.|
|UNH1 (número de referencia de encabezado de mensaje)|Escriba un prefijo, un intervalo de valores para el número de control de intercambio y un sufijo. Estos valores se utilizan para generar el número de referencia de encabezado de mensaje. El prefijo y sufijo son opcionales; se requiere el número de referencia. Se incrementa el número de referencia para cada mensaje nuevo; el prefijo y sufijo son las mismas.|

### <a name="validations"></a>Validaciones
|(Propiedad)|Descripción |
|----|----|
|Tipo de mensaje|Al seleccionar esta opción habilita la validación en el receptor de intercambio. Esta validación realiza la validación de EDI en elementos de datos de conjunto de transacciones, validar los tipos de datos, las restricciones de longitud y elementos de datos vacía y separadores de aprendizaje.|
|Validación de EDI|Seleccione esta casilla para realizar la validación de EDI en los tipos de datos definidos por las propiedades EDI del esquema, las restricciones de longitud, elementos de datos vacía y separadores al final.|
|Validación extendida|Al seleccionar esta opción habilita la validación ampliada de intercambios recibida del remitente intercambio. Esto incluye la validación de longitud del campo, opcionalidad y número de repeticiones además validación de tipo de datos XSD. Puede habilitar la validación de extensión sin habilitar validación EDI, o viceversa.|
|Permitir líder o finales ceros|Si selecciona esta opción especifica que un intercambio EDI recibido de la parte no validación si un elemento de datos en un intercambio EDI no se ajusta a sus requisitos de longitud debido o espacios finales, pero que se ajusten a sus requisitos de longitud cuando se eliminan.|
|Recortar ceros líder o finales|Si selecciona esta opción se recortan los ceros iniciales y finales.|
|Al final separador|Si selecciona esta opción especifica un intercambio EDI recibido de la parte no falla la validación si un elemento de datos en un intercambio EDI no se ajusta a sus requisitos de longitud debido a ceros iniciales (o finales) o espacios finales, pero que se ajusten a sus requisitos de longitud cuando se eliminan.</br></br>Si no desea permitir finales delimitadores y separadores de un intercambio recibido del remitente intercambio, seleccione **No permitido** . Si el intercambio contiene finales delimitadores y separadores, se declare no válidos.</br></br>Seleccione **opcional** para aceptar intercambios con o sin separadores y delimitadores finales.</br></br>Seleccione **obligatorio** si debe contener el intercambio recibido finales delimitadores y separadores.|

Después de seleccionar **Aceptar** en el módulo abierto:  
12. Seleccione el mosaico de **contratos** en el módulo de integración cuenta y verá el contrato recién agregado aparece.  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-4.png)   

## <a name="learn-more"></a>Aprende más
- [Obtenga más información sobre el paquete de integración de Enterprise] (./app-service-logic-enterprise-integration-overview.md "Obtenga más información sobre el paquete de integración de Enterprise")  
