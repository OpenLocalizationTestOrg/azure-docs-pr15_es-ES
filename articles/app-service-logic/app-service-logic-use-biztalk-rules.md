<properties
   pageTitle="Obtenga más información sobre y crear una aplicación de API de reglas de BizTalk en la aplicación lógica | Microsoft Azure"
   description="Este tema trata sobre las características de conector de reglas de BizTalk y proporciona instrucciones sobre su uso"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="04/20/2016"
   ms.author="andalmia"/>

#<a name="biztalk-rules"></a>Reglas de BizTalk

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Reglas de negocios encapsula las directivas y decisiones que controlan los procesos empresariales. Estas directivas pueden definirse formal en manuales de procedimiento, contratos o agreements, o que exista como conocimiento o experiencia incorporados a los empleados. Estas directivas son dinámicas y están sujetos a cambios a lo largo de tiempo debidas a los cambios en los planes empresariales, reglamentaciones o cualquier otro motivo.

Implementar estas directivas en lenguajes de programación tradicionales requiere junto y mucho tiempo y no permite que no son programadores participar en la creación y el mantenimiento de directivas de empresa. Reglas de negocio de BizTalk ofrece una manera rápida de implementar estas directivas y desacoplar el resto de los procesos empresariales. Esto permite realizar los cambios necesarios a las directivas de empresa sin afectar el resto de los procesos empresariales.

##<a name="why-rules"></a>¿Por qué reglas

Hay 3 principales razones para usar las reglas de negocio de BizTalk en los procesos empresariales:

* Desacoplar la lógica empresarial de código de la aplicación
- Permitir que los analistas de negocios para tener más control sobre la administración de la lógica de negocios
+ Cambios en la lógica empresarial vaya a producción más rápido

##<a name="rules-concepts"></a>Conceptos de reglas

##<a name="vocabulary"></a>Vocabulario

Un _vocabulario_ es una colección de definiciones que consta de nombres descriptivos de los objetos informáticos utilizados en regla condiciones y acciones. Las definiciones de vocabulario facilitan las reglas de leer, comprender y compartir los usuarios en un dominio empresarial concreto.

Vocabularios están diseñados para salvar la diferencia entre semántica de negocio e implementación. Por ejemplo, un enlace de datos para el estado de aprobación puede señalar a una determinada columna en una determinada fila en una base de datos, representado como una consulta SQL. En lugar de insertar a este tipo de representación complejos en una regla, en su lugar puede crear una definición de vocabulario asociada que enlace de datos con un nombre descriptivo de "Estado". Posteriormente puede incluir "Status" en cualquier número de reglas y el motor de reglas puede recuperar los datos correspondientes de la tabla.

##<a name="rule"></a>Regla

Reglas de negocios son instrucciones descriptiva que rigen el desarrollo de procesos empresariales. Una regla consta de una condición y las acciones. Se evalúa la condición y, si se evalúa como verdadero, el motor de reglas inicia una o varias acciones.
Reglas en el marco de trabajo de reglas de empresa se definen mediante el siguiente formato:

_IF_ _condición_ _A continuación_ _acción_

Considere el ejemplo siguiente:

*Cantidad de IF es menor o igual a fondos disponibles*  
*Llevar a cabo transacciones y confirmación de impresión*

Esta regla determina si se realizará una transacción mediante la aplicación de la lógica empresarial, en el formulario de una comparación de dos valores monetarios, en el formulario de un importe de la transacción y fondos disponibles.
Puede usar la regla de negocio para crear, modificar e implementar reglas de negocios. Como alternativa, puede realizar las tareas anteriores mediante programación.

###<a name="conditions"></a>Condiciones

Una condición es verdadero o falso (Boolean) expresión que consta de uno o más predicados.
En nuestro ejemplo, el predicado menor o igual que se aplica a la cantidad y los fondos disponibles. Esta condición se evaluará siempre true o false.
Predicados se pueden combinar con los operadores lógicos AND, OR y no para formar una expresión lógica que es potencialmente bastante grande, pero siempre se evalúan como verdadero o falso.

###<a name="actions"></a>Acciones

Acciones son las consecuencias funcionales de evaluación de la condición. Si se cumple una condición de regla, se inician una acción correspondiente o acciones.
En nuestro ejemplo, "conducta transacción" y "Imprimir recibo" es acciones que se llevan a cabo cuando y solo cuando la condición (en este caso, "si cantidad es menor o igual a fondos disponibles") es true.
Acciones están representadas en el marco de trabajo de reglas de negocios al realizar operaciones de conjunto en documentos XML.

##<a name="policy"></a>Directiva

Una directiva es una agrupación lógica de reglas. Redacte una directiva, guarde él, probarlo y, cuando esté satisfecho con los resultados, se puede usar en un entorno de producción.

###<a name="policy-composition"></a>Composición de directiva

Puede crear directivas en el portal de reglas. Una directiva puede contener un conjunto arbitrario grande de reglas, pero normalmente Redacte una directiva de las reglas que pertenecen a un dominio de negocio específicas en el contexto de la aplicación que va a utilizar la directiva.

###<a name="policy-testing"></a>Prueba de directiva
Un modo eficaz puede realizar la ejecución de una prueba de la directiva antes de que se utiliza en un entorno de producción. El Portal de reglas le permite proporcionar entradas a una directiva, ejecute la directiva y ver sus resultados. El resultado incluye registros, la ejecución de la regla, la evaluación de condición y salidas resultantes.

##<a name="sample-scenario---insurance-claims"></a>Escenario de ejemplo - reclamaciones de seguros
Demos un escenario de ejemplo y recorra, según se redacta la lógica empresarial para el mismo.

![Texto alternativo][1]

En un escenario de reclamaciones de seguros really simple, demandante envía su reclamación de seguros (a través de cualquier cliente como el sitio Web, la aplicación de teléfono, etcetera). Esta solicitud de notificación obtiene enviada a unidad de proceso de notificación de la empresa y basándose en el resultado del procesamiento, la notificación puede ser cualquier aprobado, rechazado o enviado a lo largo de más procesamiento manual.
La unidad de procesamiento de reclamaciones en nuestro escenario sería lo que abarca la lógica empresarial del sistema. Tomar notas más detalles sobre esta unidad, podemos ver lo siguiente:

![Texto alternativo][2]

Deje que nosotros usar reglas de negocios para implementar la lógica empresarial.


##<a name="creation-of-rules-api-app"></a>Creación de la aplicación de la Api de reglas


1. Inicie sesión en el Portal de Azure
2. Seleccione Nuevo -> Marketplace, a continuación, en Buscar *Reglas de BizTalk*
3. Seleccione BizTalk reglas de la lista de resultados. Se abre el módulo de reglas de BizTalk
4. Seleccione el botón *crear* ![texto alternativo][3]
1. En el nuevo módulo que se abre, escriba la información siguiente:  
    1. Nombre: dé un nombre para la aplicación de la API de reglas
    1. Plan de servicio de aplicaciones: seleccione o cree un nuevo Plan de servicio de aplicación
    1. Elegir el nivel de precio que desea que se encuentran en esta aplicación de precios de nivel:
    1. Grupo de recursos: seleccione o cree que la aplicación debería residen en el grupo de recursos
    2. Suscripción: seleccione la suscripción que desea usar
    1. Ubicación: elija la ubicación geográfica donde desea que la aplicación para su implementación.
4.  Seleccione *crear*. La aplicación de API de reglas de BizTalk se crearán dentro de unos minutos.

##<a name="vocabulary-creation"></a>Creación de vocabulario
Después de crear una aplicación de API de reglas de BizTalk, el siguiente paso sería crear vocabularios. Las expectativas es que el programador es la persona más comunes para realizar este ejercicio. Aquí le mostramos cómo obtenerlo terminado:


1. Su BizTalk reglas API App desde el portal, vaya a examinar de Inicio -> aplicaciones API - ><Your Rules API App>. Esto le ayudarán a Panel de aplicación de reglas API similar a continuación:

   ![Texto alternativo][4]

2.haga seleccione "Definiciones de vocabulario". ¿Mostrar el 3.en vocabulario creación pantalla Seleccione "Agregar" para comenzar a agregar nuevas definiciones de vocabulario.
Hay 2 tipos de definición de vocabulario en estos momentos: Literal y XML.

##<a name="literal-definition"></a>Definición literal
1.  Después de hacer clic en "Agregar", se abre un nuevo módulo de "Agregar definición" hacia arriba. Escriba los valores siguientes
  1.    Nombre: se esperan sólo caracteres alfanuméricos sin caracteres especiales. También debe ser único en la lista de definición de vocabulario existente.
  2.    Descripción: campo opcional.
  3.    Tipo de definición: hay 2 tipos compatibles. Elija Literal en este ejemplo
  4.    Tipo de datos: permite a los usuarios seleccionar el tipo de datos de la definición. Actualmente se admiten tipos de 4 datos:.  Cadena: estos valores se deben especificar en comillas dobles ("cadena de ejemplo")  
    II. Booleana: puede ser verdadero o falso  
    III.    Número: puede ser cualquier número decimal  
    IV. Fecha y hora: Esto significa que la definición de tipo fecha. Datos deben escribirse con este formato: mm/dd/aaaa hh AM\PM  
  5. Entrada: este es donde se escribe el valor de la definición. Los valores especificados aquí deben cumplir el tipo de datos elegido. Puede introducir un valor único, un conjunto de valores separados por comas o un rango de valores mediante la palabra clave *a*. Por ejemplo, puede especificar valor único 1; un conjunto de 1, 2, 3; o un intervalo de 1 a 5. Tenga en cuenta que solo admite el rango de números.
  6. Seleccione *Aceptar*.

![Texto alternativo][5]
##<a name="xml-definition"></a>Definición de XML
Si se elige el tipo de vocabulario como XML, debe especificar las siguientes entradas  
  una.    Esquema: hacer clic en esta abrirá un nuevo usuario Permitir placa para elegir en una lista de esquemas ya cargados o permitir cargar uno nuevo.
b.    XPATH: esta entrada obtiene desbloqueados solo después de elegir un esquema en el paso anterior. Haciendo clic en esta se mostrará el esquema que se ha seleccionado y permite al usuario seleccionar el nodo para el que se debe crear una definición de vocabulario.  
  c.    HECHO: esta entrada identifica el objeto de datos ' d apto para el motor de reglas de procesamiento. Se trata de una propiedad avanzada y de forma predeterminada se establece en el elemento primario de la expresión XPATH seleccionado. FACT resulta especialmente importante para escenarios encadenar y colección.

![Texto alternativo][6]

### <a name="add-bulk"></a>Agregar en masa
Los pasos anteriores han capturado la experiencia para crear las definiciones de vocabulario. Una vez creada, las definiciones de vocabulario creado aparecerá en el formulario de lista. Existen requisitos para poder generar varias definiciones del mismo esquema en lugar de repetir los pasos anteriores cada vez. Esto es donde capacidad agregar masiva es muy útil.
Seleccionar "Masa agregar" le llevará a un nuevo módulo. El primer paso es seleccionar el esquema que están creados varias definiciones. Al seleccionar esta se abrirá un nuevo módulo permitir: elegir de una lista de esquemas ya cargados o cargar uno nuevo.
Ahora la propiedad XPath obtiene desbloqueada. Al seleccionar esta se abrirá el Visor de esquema que se pueden seleccionar varios nodos.
Los nombres de las definiciones de varios creados predeterminado será el nombre del nodo seleccionado. Siempre se pueden modificar tras la creación.

![Texto alternativo][7]

##<a name="policy-creation"></a>Creación de directivas
Una vez que el programador ha creado vocabularios necesarios, las expectativas es que el analista de negocios sería las directivas de empresa crear uno a través del Portal de Azure.  
    1. en la aplicación de reglas que se creó, hay una lente de directiva haciendo clic en el que el usuario se vaya a la página de creación de la directiva.  
    2. esta página mostrará la lista de directivas de esta aplicación de reglas concreta. Los analistas pueden agregar una nueva directiva simplemente escribiendo un nombre de directiva y presionar la tecla TABULADOR dos veces. Varias directivas pueden residen en una sola aplicación API de reglas.  
    3. selección de la directiva creada tendrá el usuario a la página de detalles de directiva donde se pueden ver las reglas que están en la directiva.  
    ![Texto alternativo][8]
 4.  Seleccione "Agregar" para agregar una nueva regla. Esto le llevará a un nuevo módulo.

##<a name="rule-creation"></a>Creación de reglas
Una regla es el conjunto de instrucciones de condición y una acción. Las acciones se ejecutan si la condición se evalúa como verdadero. En el módulo crear regla, asigne un nombre de regla único (para esa directiva) y una descripción (opcional).
El cuadro de condición (si está) puede utilizarse para crear complejas instrucciones condicionales. Los siguientes son las palabras clave admitidas:  
1.  Y -operador condicional  
2.  O bien-operador condicional  
3.  ¿es\_no\_existe  
4.  existe  
5.  falso  
6.  es\_igual\_a  
7.  es\_mayor\_que  
8.  es\_mayor\_de\_igual\_a  
9.  es\_en  
10. es\_menos\_que  
11. es\_menos\_de\_igual\_a  
12. es\_no\_en  
13. es\_no\_igual\_a  
14. Mod  
15. Verdadero  

El cuadro de Action(THEN) puede contener varias instrucciones, uno por línea, para crear acciones que se ejecutan. Los siguientes son las palabras clave admitidas:  
1.  es igual a  
2.  falso  
3.  Verdadero  
4.  detener  
5.  Mod  
6.  nulo  
7.  actualizar  

Los cuadros de condición y una acción proporcionan Intellisense para ayudarle a crear una regla rápidamente. Puede activarse presionando ctrl + espacio o simplemente empieza a escribir. Palabras clave que coincidan con los caracteres escritos automáticamente filtrado hacia abajo y se muestra. La ventana de intellisense mostrará todas las palabras clave y las definiciones de vocabulario.
![Texto alternativo][9]

##<a name="explicit-forward-chaining"></a>Reenviar explícita encadenar
Reglas de BizTalk es compatible con explícitas encadenar hacia adelante, si desea que los usuarios volver a evaluar reglas en respuesta a determinadas acciones, pueden desencadenar esto mediante determinadas palabras clave. Las siguientes son las palabras clave admitidas:  
   1.   actualizar <vocabulary definition> : esta palabra clave vuelve a evaluar todas las reglas que utilizan la definición de vocabulario especificado en su estado.  
   2.   Detener: esta palabra clave deja de todas las ejecuciones de regla

##<a name="enabledisable-rules"></a>Habilitar/deshabilitar reglas
Cada regla de la directiva se puede habilitar o deshabilitar. De forma predeterminada se habilitan todas las reglas. Reglas desactivadas no se ejecutará durante la ejecución de la directiva. Habilitar/deshabilitar reglas pueden realizarse desde el módulo de regla directamente: los comandos están disponibles en la barra de comandos en la parte superior del módulo, o de la directiva, el menú contextual (botón secundario en una regla) tiene la opción de habilitar/deshabilitar.

##<a name="rule-priority"></a>Prioridad de la regla
Todas las reglas de una directiva se ejecutan en orden. La prioridad de ejecución viene determinada por el orden en que aparecen en la directiva. Esta prioridad puede cambiarse simplemente arrastrando y soltando la regla.

##<a name="test-policy"></a>Probar la directiva
Puede probar las directivas mediante el comando de "Probar directiva" en el módulo de directivas de prueba. En este módulo puede ver una lista de las definiciones de vocabulario que se usan en la directiva que requieren una entrada del usuario. Los usuarios pueden agregar manualmente los valores de estas entradas para su escenario de prueba. Como alternativa, también pueden elegir importar usuarios probar XMLs entradas. Una vez que todas las entradas, se puede ejecutar la prueba y los resultados para cada definición de vocabulario se mostrará en la columna de salida para facilitar la comparación. Para ver registros de analista de negocios descriptivos, haga clic en "Ver registros" para ver los registros de ejecución. Para guardar los registros, la opción "Guardar resultados" está disponible para almacenar todos los datos relacionados de análisis independientes de prueba.

## <a name="using-rules-in-logic-apps"></a>Con las reglas de aplicaciones de lógica
Una vez que la directiva se ha creado y probado, ahora está listo para su uso. Puede crear una nueva aplicación lógica seleccionando lógica de aplicaciones en el lado izquierdo de la página principal del portal. Una vez que se ha creado la aplicación lógica, iniciarlo, a continuación, seleccione *desencadenadores y acciones*. A continuación, puede seleccionar la plantilla para *crear desde cero* . Siga los pasos para agregar la aplicación de API de reglas de BizTalk a la aplicación de la lógica. Una vez hecho esto, habrá una opción para elegir qué aplicación de la API de reglas (acción) al destino. Acciones incluyen la lista de directivas que se ejecutan. Elija una directiva específica después de que las entradas necesarios para la directiva debe especificarse. Los usuarios pueden usar el resultado de la aplicación de reglas API descendente más toma de decisiones.

## <a name="using-rules-via-apis"></a>Uso de reglas a través de la API
La aplicación de la API de reglas también se puede invocar mediante un amplio conjunto de API. Esta forma, los usuarios no están restringido a solo usando lógica de aplicaciones y pueden usar las reglas en cualquier aplicación mediante llamadas del resto. Las API de REST exacta disponibles se pueden visualizar haciendo clic en la lente de "Definición de la API" en el panel de reglas.

![Texto alternativo][10]

A continuación se muestra un ejemplo de cómo uno podría usar esta API en C#

            // Constructing the JSON message to use in API call to Rules API App

            // xmlInstance is the XML message instance to be passed as input to our Policy
            string xmlInstance = "<ns0:Patient xmlns:ns0=\"http://tempuri.org/XMLSchema.xsd\">  <ns0:Name>Name_0</ns0:Name>  <ns0:Email>Email_0</ns0:Email>  <ns0:PatientID>PatientID_0</ns0:PatientID>  <ns0:Age>10.4</ns0:Age>  <ns0:Claim>    <ns0:ClaimDate>2012-05-31T13:20:00.000-05:00</ns0:ClaimDate>    <ns0:ClaimID>10</ns0:ClaimID>    <ns0:TreatmentID>12</ns0:TreatmentID>    <ns0:ClaimAmount>10000.0</ns0:ClaimAmount>    <ns0:ClaimStatus>ClaimStatus_0</ns0:ClaimStatus>    <ns0:ClaimStatusReason>ClaimStatusReason_0</ns0:ClaimStatusReason>  </ns0:Claim></ns0:Patient>";

            JObject input = new JObject();

            // The JSON object is to be of form {"<XMLSchemName>_<RootNodeName>":"<XML Instance String>".
            // In the below case, we are using XML Schema - "insruanceclaimsschema" and the root node is "Patient".
            // This is CASE SENSITIVE.
            input.Add("insuranceclaimschema_Patient", xmlInstance);
            string stringContent = JsonConvert.SerializeObject(input);


            // Making REST call to Rules API App
            HttpClient httpClient = new HttpClient();

            // The url is the Host URL of the Rules API App
            httpClient.BaseAddress = new Uri("https://rulesservice77492755b7b54c3f9e1df8ba0b065dc6.azurewebsites.net/");
            HttpContent httpContent = new StringContent(stringContent);
            httpContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json");

            // Invoking API "Execute" on policy "InsruranceClaimPolicy" and getting response JSON object. The url can be gotten from the API Definition Lens
            var postReponse = httpClient.PostAsync("api/Policies/InsuranceClaimPolicy?comp=Execute", httpContent).Result;

Observe que la aplicación de API de reglas anteriores tiene su configuración de seguridad establecida en "Anon público". Se puede establecer desde dentro de la API de App usando: todas las opciones -> configuración de la aplicación -> nivel de acceso.

![Texto alternativo][11]

## <a name="editing-vocabulary-and-policy"></a>Vocabulario y directiva de edición
Una de las principales ventajas del uso de reglas de negocios es que los cambios a la lógica empresarial pueden enviarse a producción mucho más rápido. Cualquier cambio realizado en vocabulario y directivas se aplica inmediatamente en producción. Usuario necesita simplemente vaya a la definición de vocabulario respectivos o directiva y realice los cambios que entre en vigor.

<!--Image references-->
[1]: ./media/app-service-logic-use-biztalk-rules/InsuranceScenario.PNG
[2]: ./media/app-service-logic-use-biztalk-rules/InsuranceBusinessLogic.png
[3]: ./media/app-service-logic-use-biztalk-rules/CreateRuleApiApp.png
[4]: ./media/app-service-logic-use-biztalk-rules/RulesDashboard.png
[5]: ./media/app-service-logic-use-biztalk-rules/LiteralVocab.PNG
[6]: ./media/app-service-logic-use-biztalk-rules/XMLVocab.PNG
[7]: ./media/app-service-logic-use-biztalk-rules/BulkAdd.PNG
[8]: ./media/app-service-logic-use-biztalk-rules/PolicyList.PNG
[9]: ./media/app-service-logic-use-biztalk-rules/RuleCreate.PNG
[10]: ./media/app-service-logic-use-biztalk-rules/APIDef.PNG
[11]: ./media/app-service-logic-use-biztalk-rules/PublicAnon.PNG
