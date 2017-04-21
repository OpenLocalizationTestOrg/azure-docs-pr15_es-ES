<properties
    pageTitle="Notas de la versión para los servicios de BizTalk Azure | Servicios de Microsoft Azure BizTalk"
    description="Enumera los problemas conocidos para los servicios de BizTalk de Azure" 
    services="biztalk-services"
    documentationCenter=""
    authors="msftman"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="deonhe"/>

# <a name="release-notes-for-azure-biztalk-services"></a>Notas de la versión para los servicios de Azure BizTalk

Las notas de los servicios de Microsoft Azure BizTalk contienen los problemas conocidos en esta versión.

## <a name="whats-new-in-the-november-update-of-biztalk-services"></a>Novedades de la actualización de noviembre de servicios de BizTalk
* Cifrado al resto puede estar habilitado en el Portal de servicios de BizTalk. Vea [Habilitar cifrado al resto de Portal de servicios de BizTalk](https://msdn.microsoft.com/library/azure/dn874052.aspx).

## <a name="update-history"></a>Historial de actualización

### <a name="october-update"></a>Actualización de octubre

* Cuentas de la organización son compatibles:  
 * **Escenario**: registrado una implementación de BizTalk Service con una cuenta Microsoft (como user@live.com). En este escenario, solo los usuarios de Microsoft Account pueden administrar el BizTalk Service con el portal de servicios de BizTalk. No se puede utilizar una cuenta de la organización.  
 * **Escenario**: registrado una implementación de BizTalk Service utilizando una cuenta de Azure Active Directory (como user@fabrikam.com o user@contoso.com). En este escenario, solo los usuarios de Azure Active Directory de la misma organización pueden administrar el BizTalk Service con el portal de servicios de BizTalk. No se puede utilizar una cuenta de Microsoft.  
* Cuando se crea un BizTalk Service en el portal de clásico Azure, se registran automáticamente en el Portal de servicios de BizTalk.
 * **Escenario**: iniciar sesión en el portal de clásico Azure, crear una BizTalk Service y, a continuación, seleccione **Administrar** por primera vez. Cuando se abra el portal de servicios de BizTalk, BizTalk Service se registra automáticamente y está listo para su implementación.  
 Vea [cómo registrar y actualizar una implementación de servicio de BizTalk en el Portal de servicios de BizTalk](https://msdn.microsoft.com/library/azure/hh689837.aspx).  

### <a name="august-14-update"></a>Actualización 14 de agosto
* Contrato y puente disociación: cotización agreements partner y puentes ahora se separan en el Portal de servicios de BizTalk. Ahora crear agreements y puentes por separado y en tiempo de ejecución puentes resuelven un acuerdo basándose en los valores en el mensaje EDI. Vea [Crear contratos en Azure BizTalk Services](https://msdn.microsoft.com/library/azure/hh689908.aspx), [crear un puente EDI con el Portal de servicios de BizTalk](https://msdn.microsoft.com/library/azure/dn793986.aspx), [crear un puente de AS2 con el Portal de servicios de BizTalk](https://msdn.microsoft.com/library/azure/dn793993.aspx), y [¿cómo resolver puentes agreements en tiempo de ejecución?](https://msdn.microsoft.com/library/azure/dn794001.aspx)  
* La opción para crear plantillas para agreements se interrumpió.  
* Para el acuerdo de envío, ahora puede especificar conjuntos de delimitador diferente para cada esquema. Esta configuración se especifica en configuración de protocolo de acuerdo de envío lado. Para obtener más información, consulte [creación de una X12 contrato en servicios de Azure BizTalk](https://msdn.microsoft.com/library/azure/hh689847.aspx) y [crear un acuerdo de EDIFACT en Azure BizTalk Services](https://msdn.microsoft.com/library/azure/dn606267.aspx). Dos nuevas entidades también se agregan a la API de OM TPM para el mismo propósito. Consulte [X12DelimiterOverrides](https://msdn.microsoft.com/library/azure/dn798749.aspx) y [EDIFACTDelimiterOverride](https://msdn.microsoft.com/library/azure/dn798748.aspx).  
* Construcciones XSD estándar, incluidos los tipos derivados, ahora son compatibles. Consulte [estructuras de XSD estándar de uso en los mapas](https://msdn.microsoft.com/library/azure/dn793987.aspx) y [Usar tipos de deriva en escenarios de asignación y ejemplos](https://msdn.microsoft.com/library/azure/dn793997.aspx).  
* AS2 admite nuevos algoritmos de Micrófono para la firma de mensaje y nuevos algoritmos de cifrado. Consulte [crear un acuerdo de AS2 en servicios de Azure BizTalk](https://msdn.microsoft.com/library/azure/hh689890.aspx).  
## <a name="know-issues"></a>Problemas conocidos

### <a name="connectivity-issues-after-biztalk-services-portal-update"></a>Problemas de conectividad después de la actualización del Portal de servicios de BizTalk

  Si tiene el Portal de servicios de BizTalk abierto mientras BizTalk Services se actualiza para deshacer cambios en el servicio, que podría enfrentarse a problemas de conectividad con el Portal de servicios de BizTalk.  
  Como solución alternativa, puede reiniciar el explorador, eliminar la memoria caché del explorador o iniciar el portal en modo privado.  

### <a name="visual-studio-ide-cannot-locate-the-artifact-if-you-click-an-error-or-warning-in-a-biztalk-services-project"></a>IDE de Visual Studio no puede encontrar la muestra si hace clic en un error o advertencia en un proyecto de servicios de BizTalk
Instale Visual Studio 2012 actualización 3 RC 1 para solucionar el problema.  

### <a name="custom-binding-project-reference"></a>Referencia de proyecto de enlace personalizado
Tenga en cuenta las siguientes situaciones con un proyecto de servicios de BizTalk en una solución de Visual Studio:  
* En la misma solución de Visual Studio, hay un proyecto de servicios de BizTalk y un enlace personalizado. El proyecto de BizTalk Service tiene una referencia a este archivo de proyecto de enlace personalizado.
* El proyecto de BizTalk Service tiene una referencia a un enlace o comportamiento personalizado DLL.

'Generar' la solución en Visual Studio correctamente. A continuación, 'Reconstruir' o 'Limpiar' la solución. Después, al volver a generar o limpiar de nuevo, se produce el siguiente error:  
  No se puede copiar el archivo <Path to DLL> a "bin\Debug\FileName.dll". El proceso no puede acceder al archivo 'bin\Debug\FileName.dll' porque se está usando otro proceso.  

#### <a name="workaround"></a>Solución alternativa
* Si está instalado [Visual Studio 2012 actualización 3](https://www.microsoft.com/download/details.aspx?id=39305) , tiene las dos opciones siguientes:

  * Reinicie Visual Studio, o

  * Reinicie la solución. A continuación, llevar a cabo sólo una versión de la solución.  

* Si no está instalado [Visual Studio 2012 actualización 3](https://www.microsoft.com/download/details.aspx?id=39305) , abra el Administrador de tareas, haga clic en la pestaña procesos, haga clic en el proceso de MSBuild.exe y, a continuación, haga clic en el botón Terminar proceso.  

### <a name="routing-to-basichttprelay-endpoints-is-not-supported-from-bridges-and-biztalk-services-portal-if-non-printable-characters-are-promoted-as-http-headers"></a>Enrutamiento a extremos de BasicHttpRelay no es compatible de puentes y Portal de servicios de BizTalk si se promueven los caracteres no imprimibles como encabezados HTTP

Si usa caracteres no imprimibles como parte de las propiedades de promoción para los mensajes, los mensajes se no se enrutan a destinos de retransmisión que utilizan el enlace BasicHttpRelay. Además, las propiedades de promoción que están disponibles como parte del seguimiento de URL codificada para blobs y anular la codificación para destinos.  

### <a name="mdn-is-sent-asynchronously-even-if-the-send-asynchronous-mdn-option-is-unchecked"></a>MDN se envía de forma asincrónica incluso si se desactiva la opción de enviar asincrónica MDN  
Tenga en cuenta este escenario: si Active la casilla de verificación **Enviar MDN asincrónica** y, especifique una dirección URL para enviar el asincrónico MDN y, a continuación, desactive la casilla de verificación **Enviar MDN asincrónica** de nuevo, la MDN aún se envía a la dirección URL especificada aunque no está seleccionada la opción para enviar MDNs asincrónico.  
Como solución alternativa, debe borrar la dirección URL especificada antes debe desactivar la casilla de verificación **Enviar MDN asincrónica** y, a continuación, implementar el contrato de AS2.  

### <a name="whitespace-characters-beyond-a-valid-interchange-cause-an-empty-message-to-be-sent-to-the-suspend-endpoint"></a>Espacios en blanco más allá de un intercambio válido producen un mensaje vacío que se envíen al extremo de suspensión  
Si hay más allá de un segmento IEA los espacios en blanco, el Desensamblador trata como final de intercambio actual y busca en el siguiente conjunto de espacios en blanco como un mensaje siguiente. Puesto que no es válida intercambio, puede observar que se envía un mensaje de éxito a la ruta de destino y se envía un mensaje vacío del extremo de suspensión.  
### <a name="tracking-in-biztalk-services-portal"></a>Realizar un seguimiento del Portal de servicios de BizTalk  
Seguimiento de eventos se capturan hasta el procesamiento del mensaje EDI y cualquier correlación. Si se produce un error en un mensaje de fuera de la fase de protocolo, seguimiento se muestra correctamente. En esta situación, consulte la sección Registro en la columna **Detalles** en el **seguimiento de** detalles del error.
La X12 configuración de enviar y recibir ([creación de una X12 contrato en servicios de Azure BizTalk](https://msdn.microsoft.com/library/azure/hh689847.aspx)) proporcionan información acerca de la fase de protocolo.  

### <a name="update-agreement"></a>Contrato de actualización  
El Portal de servicios de BizTalk le permite modificar el calificador de una identidad cuando se ha configurado un acuerdo. Esto puede provocar propiedades no es coherente. Por ejemplo, hay un acuerdo mediante ZZ:1234567 y ZZ:7654321 el calificador. En la configuración de perfil del Portal de servicios de BizTalk, cambiar ZZ:1234567 para que sea 01:ChangedValue. Abra el contrato y 01:ChangedValue se muestra en lugar de ZZ:1234567.
Para modificar el calificador de una identidad, eliminar el contrato, actualizar **identidades** en el perfil de partner y, a continuación, vuelva a crear el acuerdo.  
> AZURE. Advertencia este comportamiento afecta X12 y AS2.  

### <a name="as2-attachments"></a>Datos adjuntos de AS2  
Datos adjuntos para AS2 mensajes no se admiten en enviar o reciban. Más concretamente, se omiten en los datos adjuntos y el cuerpo del mensaje se procesa como un mensaje de AS2 normal.  
### <a name="resources-remembering-path"></a>Recursos: Recordar la ruta de acceso  
Al agregar **recursos**, es posible que la ventana de diálogo no recuerde la ruta previamente utilizada para agregar un recurso. Para recordar la ruta de acceso utilizado previamente, intente agregar el sitio web de Portal de servicios de BizTalk a **Sitios de confianza** en Internet Explorer.  
### <a name="if-you-rename-the-entity-name-of-a-bridge-and-close-the-project-without-saving-changes-opening-the-entity-again-results-in-an-error"></a>Si cambiar el nombre de la entidad de un puente y cierre el proyecto sin guardar los cambios, vuelva a abrir la entidad da como resultado un error
Considere la posibilidad de un escenario en el siguiente orden:  
* Agregar un puente (por ejemplo un puente de unidireccional XML) a un proyecto de BizTalk Service  

* Cambiar el nombre del puente especificando un valor de la propiedad del nombre de la entidad. Esto cambia el nombre del archivo .bridgeconfig asociado con el nombre especificado.  

* Cierre el archivo de .bcs (cerrando la pestaña en Visual Studio) sin guardar los cambios.  

* Abra el archivo .bcs de nuevo desde el Explorador de soluciones.  
Observará que mientras el archivo .bridgeconfig asociado con el nuevo nombre especificado, el nombre de la entidad en la superficie de diseño es el nombre del antiguo. Si intenta abrir la configuración del puente haciendo doble clic en el componente de puente, obtendrá el siguiente error:  
  '<old name>'Entidad asociados archivo'<old name>.bridgeconfig' no existe  
Para evitar este escenario, asegúrese de que guardar cambios después de cambiar el nombre de las entidades de un proyecto de BizTalk Service.  
### <a name="biztalk-service-project-builds-successfully-even-if-an-artifact-has-been-excluded-from-a-visual-studio-project"></a>Proyecto de BizTalk Service se compila correctamente aunque se ha excluido un efecto de un proyecto de Visual Studio
Considere la posibilidad de un escenario donde agregar un efecto (por ejemplo, un archivo XSD) a un proyecto de BizTalk Service, especifique que muestra en la configuración del puente (por ejemplo, como el tipo de mensaje de solicitud) y, a continuación, se excluir del proyecto de Visual Studio. En este caso, generar el proyecto no hará cualquier error como la muestra eliminada está disponible en el disco en la misma ubicación desde donde se ha incluido en el proyecto de Visual Studio.
### <a name="the-biztalk-service-project-does-not-check-for-schema-availability-while-configuring-the-bridges"></a>El proyecto de BizTalk Service no comprobar la disponibilidad de esquema de al configurar la puentes
En un proyecto de BizTalk Service, si un esquema que se agrega al proyecto importa otro esquema, el proyecto de BizTalk Service no comprueba si el esquema importado se agrega al proyecto. Si intenta crear un proyecto, no obtendrá los errores de compilación.
### <a name="the-response-message-for-a-xml-request-reply-bridge-is-always-of-charset-utf-8"></a>El mensaje de respuesta de un puente de respuesta de la solicitud de XML es siempre del juego de caracteres UTF-8
En esta versión, el juego de caracteres del mensaje de respuesta de un puente de respuesta de la solicitud de XML siempre se establece en UTF-8.
### <a name="user-defined-datatypes"></a>Tipos de datos definidos por el usuario
Los adaptadores BizTalk adaptador Pack dentro de la característica de servicio de adaptador BizTalk pueden utilizar tipos de datos definidos por el usuario para operaciones de adaptador.
Cuando se usa tipos de datos definidos por el usuario, copie los archivos (DLL) a unidad: \Program Files\Microsoft BizTalk adaptador Service\BAServiceRuntime\bin\ o a la Global caché (Ensamblados) en el servidor que hospeda el servicio adaptador BizTalk. En caso contrario, el siguiente error puede producirse en el cliente:  
```<s:Fault xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
  <faultcode>s:Client</faultcode>
  <faultstring xml:lang="en-US">The UDT with FullName "File, FileUDT, Version=Value, Culture=Value, PublicKeyToken=Value" could not be loaded. Try placing the assembly containing the UDT definition in the Global Assembly Cache.</faultstring>
  <detail>
    <AFConnectRuntimeFault xmlns="http://Microsoft.ApplicationServer.Integration.AFConnect/2011" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <ExceptionCode>ERROR_IN_SENDING_MESSAGE</ExceptionCode>
    </AFConnectRuntimeFault>
  </detail>
</s:Fault> ```  
> [AZURE.IMPORTANT] Se recomienda utilizar GACUtil.exe para instalar un archivo en la caché de ensamblado Global. GACUtil.exe se explica cómo usar esta herramienta y las opciones de línea de comandos de Visual Studio.  

### <a name="restarting-the-biztalk-adapter-service-web-site"></a>Reiniciar el sitio Web de BizTalk adaptador Service
Instalar la **Ejecución del servicio de adaptador BizTalk** * crea el * *Servicio de adaptador BizTalk* * sitio web de IIS que contiene la * *BAService* * aplicación.* *BAService** aplicación usa internamente el enlace de retransmisión para ampliar el alcance local del extremo de servicios en la nube. Un servicio hospedado en las instalaciones, el extremo de retransmisión correspondiente se registrará en el Bus de servicio solo cuando se inicia el servicio local.  

Si detiene e inicia una aplicación, no se respeta la configuración de inicio automático de una aplicación. Así que cuando se detiene **BAService** , debe siempre reiniciar el sitio web de **Servicio de adaptador BizTalk** en su lugar. No iniciar o detener la aplicación **BAService** .
### <a name="special-characters-should-not-be-used-for-address-and-entity-names-of-lob-components"></a>No usar caracteres especiales de dirección y entidad nombres de componentes LOB
No debe utilizar caracteres especiales de dirección y entidad nombres de componentes LOB. Si lo hace, obtendrá un error al implementar el proyecto de BizTalk Service. Para determinados caracteres como '%', el sitio Web de servicio de adaptador BizTalk podría ir a un estado de detención y tendrá que iniciarlo manualmente.
### <a name="test-map-with-get-context-property"></a>Mapa de prueba con la propiedad de contexto de Get
Si una transformación contiene una operación de asignación de **Obtener la propiedad de contexto** , se producirá un error en **El mapa de prueba** . Como solución alternativa temporal, reemplace la operación de asignación de **Propiedades de contexto de obtener** con una operación de asignación de concatenar de cadena que contiene datos ficticias. Esto rellenar el esquema de destino y permitir que la pruebe otras funciones de transformación.
### <a name="test-map-property-does-not-display"></a>No se muestra la propiedad del mapa de prueba
No se muestran las propiedades de **Mapa de prueba** en Visual Studio. Esto puede ocurrir si la ventana **Propiedades** y la ventana del **Explorador de soluciones** no se acoplarán al mismo tiempo. Para resolver este problema, acoplar las **Propiedades** y las ventanas del **Explorador de soluciones** .  
### <a name="datetime-reformat-drop-down-is-grayed-out"></a>Cambiar el formato DateTime desplegable está atenuado
Cuando se agrega a la superficie de diseño y se configura una operación de asignación de cambiar el formato de fecha y hora, la lista desplegable de formato puede atenuada. Esto puede suceder si se establece el equipo mostrar **mediano – 125%** o **más grande: 150%**. Para resolver, establezca la visualización a **más pequeño: 100% (predeterminado)** mediante los pasos siguientes:  
1. Abra el **Panel de Control** y haga clic en **apariencia y personalización**.
2. Haga clic en **Mostrar**.
3. Haga clic en **más pequeño: 100% (predeterminado)** y haga clic en **Aplicar**.

La lista desplegable **formato** ahora debería funcionar según lo esperado.
### <a name="duplicate-agreements-in-the-biztalk-services-portal"></a>Duplicar agreements en el Portal de servicios de BizTalk
Tenga en cuenta lo siguiente:
1. Crear un acuerdo con la API de OM de administración de partners de cotización.
2. Abra el contrato en el Portal de servicios de BizTalk en dos pestañas diferentes.
3. Implemente el contrato de las pestañas de ambos.
4. Como resultado, tanto los contratos se desarrollan de entradas duplicadas en el Portal de servicios de BizTalk

**Solución alternativa**. Abrir uno de los contratos duplicados en el Portal de servicios de BizTalk así como para anular.  

### <a name="bridges-do-not-use-updated-certificate-even-after-a-certificate-has-been-updated-in-the-artifact-store"></a>Puentes usando certificado actualizado incluso después de que se ha actualizado un certificado en el almacén de muestra
Tenga en cuenta las siguientes situaciones:  

**Escenario 1: Utilizar certificados de huella digital para proteger la transferencia de mensajes de un puente a un extremo de servicio**  
Considere la posibilidad de un escenario que utilizar certificados de huella digital en su proyecto BizTalk Service. Actualizar el certificado en el Portal de servicios de BizTalk con el mismo nombre pero una huella digital diferente, pero no se actualizan el proyecto BizTalk Service según corresponda. En este caso, el puente puede seguir procesar los mensajes porque los datos de certificado más antiguos aún pueden estar en la caché de canal. Después de eso, se produce un error de procesamiento de mensajes.  

**Solución**: actualice el certificado en el proyecto de BizTalk Service y volver a implementar el proyecto.  

**Escenario 2: Utilizar comportamientos de nombre para identificar certificados para proteger la transferencia de mensajes de un puente a un extremo de servicio**

Considere la posibilidad de un escenario que utilizar comportamientos de nombre para identificar los certificados en su proyecto BizTalk Service. Actualizar el certificado en el Portal de servicios de BizTalk, pero no se actualizan el proyecto BizTalk Service según corresponda. En este caso, el puente puede seguir procesar los mensajes porque los datos de certificado más antiguos aún pueden estar en la caché de canal. Después de eso, se produce un error de procesamiento de mensajes.  

**Solución**: actualice el certificado en el proyecto de BizTalk Service y volver a implementar el proyecto.  

### <a name="bridges-continue-to-process-messages-even-when-the-sql-database-is-offline"></a>Puentes continuarán procesar los mensajes incluso cuando la base de datos SQL está desconectado
Puentes de servicios de BizTalk continuarán procesar los mensajes de un tiempo, incluso si la Microsoft Azure base de datos SQL (que almacena la información de ejecución como artefactos implementados y canalizaciones) está sin conexión. Esto es porque los servicios de BizTalk utiliza la caché artefactos y configuración del puente.
Si no desea que los puentes de procesar los mensajes cuando la base de datos de SQL está sin conexión, puede usar los cmdlets de PowerShell de servicios de BizTalk para detener o suspender BizTalk Service. Consulte [El ejemplo de administración de servicio de Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=329019) para los cmdlets de Windows PowerShell para administrar operaciones.  
### <a name="reading-the-xml-message-within-a-bridges-custom-code-component-includes-an-extra-bom-character"></a>Leer el mensaje XML dentro de componente de código personalizado de un puente incluye un carácter BOM extra
Considere la posibilidad de un escenario donde desea leer un mensaje XML en el código personalizado de un puente. Si utiliza la System.Text.Encoding.UTF8.GetString(bytes) de la API de .NET un carácter BOM extra se incluye en los resultados al principio del mensaje. Por lo tanto, si no desea que el resultado para incluir el carácter de la lista de materiales adicional, debe usar ```System.IO.StreamReader().ReadToEnd()```.
### <a name="sending-messages-to-a-bridge-using-wcf-does-not-scale"></a>Enviar mensajes a un puente de WCF no escala
No ajustar el tamaño de los mensajes enviados a un puente de WCF. En su lugar, debe usar HttpWebRequest si desea que un cliente scalable.
### <a name="upgrade-token-provider-error-after-upgrading-from-biztalk-services-preview-to-general-availability-ga"></a>ACTUALIZACIÓN: Error proveedor de Token después de actualizar de vista previa de los servicios de BizTalk a disponibilidad General (GA)
Hay un EDI o el contrato de AS2 con lotes activos. Cuando BizTalk Service se actualiza de vista previa a disponibilidad general, puede ocurrir lo siguiente:
* Error: El proveedor de tokens no pudo proporcionar un token de seguridad. Proveedor de token devuelto mensaje: no se pudo resolver el nombre remoto.

* Tareas por lotes se cancelan.

**Solución alternativa**: después de BizTalk Service se actualiza a disponibilidad General (GA), volver a implementar el contrato.  

### <a name="upgrade-toolbox-shows-the-old-bridge-icons-after-upgrading-the-biztalk-services-sdk"></a>ACTUALIZACIÓN: Cuadro de herramientas muestra los iconos de puente anterior después de actualizar el SDK de servicios de BizTalk
Después de actualizar una versión anterior del SDK de servicios de BizTalk, que tenía antiguos iconos que representan los puentes, continúa el cuadro de herramientas mostrar los iconos de puentes de la antiguos. Sin embargo, si agrega un puente a la superficie del diseñador proyecto BizTalk Service, la superficie muestra el icono de nuevo.  

**Solución alternativa**. Puede evitar este problema eliminando los archivos .tbd en <system drive>: \Users\<usuario > \AppData\Local\Microsoft\VisualStudio\11.0.  

### <a name="upgrade-biztalk-portal-update-from-preview-to-ga-might-show-an-error-indicating-that-the-edi-capability-is-not-available"></a>ACTUALIZACIÓN: Actualización de BizTalk Portal de vista previa a disponibilidad general puede mostrar un error que indica que la capacidad EDI no está disponible
Si ha iniciado sesión en el Portal de servicios de BizTalk mientras los servicios de BizTalk se actualiza de vista previa a disponibilidad general, obtendrá el siguiente error en el portal:  

Esta función no está disponible como parte de esta versión de Microsoft Azure BizTalk Services. Para usar estas capacidades cambiar a una edición apropiada.  

**Resolución**: cierre de sesión desde el portal de cerrar y abrir el explorador y, a continuación, inicie sesión en el portal.  
### <a name="upgrade-new-tracking-data-does-not-show-up-after-biztalk-services-is-upgraded-to-ga"></a>ACTUALIZACIÓN: Nuevos datos de seguimiento no aparecen después de actualizar servicios BizTalk GA
Supongamos un escenario donde tiene un puente XML implementado en la suscripción de vista previa de los servicios de BizTalk. Enviar mensajes al puente y el seguimiento de los datos correspondiente está disponible en el Portal de servicios de BizTalk. Ahora, si se actualizan los bits de tiempo de ejecución de Portal de servicios de BizTalk y servicios de BizTalk a GA y enviar un mensaje al mismo extremo del puente implementado una versión anterior, los datos de seguimiento no mostrará los mensajes enviados después de la actualización.  

### <a name="pipelines-vs-bridges"></a>Puentes de canalizaciones v/s
En este documento, el término 'canalizaciones' y 'puentes' se utilizan indistintamente. Básicamente, ambos significan lo mismo, que es una unidad de procesamiento de mensaje implementada en servicios de BizTalk.  

### <a name="concepts"></a>Conceptos  

[Servicios de BizTalk](https://msdn.microsoft.com/library/azure/hh689864.aspx)   
