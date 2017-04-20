<properties
    pageTitle="Con Azure CDN CORS | Microsoft Azure"
    description="Obtenga información sobre cómo usar la red de entrega de contenido Azure (CDN) a con origen entre recursos de uso compartido (CORS)."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="casoper"/>
    
# <a name="using-azure-cdn-with-cors"></a>Usar CDN Azure con CORS     

## <a name="what-is-cors"></a>¿Qué es CORS?

CORS (cruzado Origin uso compartido de recursos) es una característica HTTP que permite a una aplicación de web que se ejecuta en un dominio tener acceso a recursos de otro dominio. Para reducir la posibilidad de ataques de secuencias de comandos entre sitios, todos los exploradores web modernos implementan una restricción de seguridad que se conoce como [Directiva de mismo origen](http://www.w3.org/Security/wiki/Same_Origin_Policy).  Esto impide que una página web desde las API de llamada en un dominio diferente.  CORS ofrece una manera segura para permitir que un dominio (el dominio de origen) llamar a las API de otro dominio.
 
## <a name="how-it-works"></a>Cómo funciona
1.  El explorador envía la solicitud de opciones con un encabezado de **origen** HTTP. El valor de este encabezado es el dominio que sirve la página principal. Cuando una página de https://www.contoso.com intenta obtener acceso a datos de un usuario en el dominio fabrikam.com, el siguiente encabezado de la solicitud se enviará a fabrikam.com: 
    
    `Origin: https://www.contoso.com`
 
2.  El servidor puede responder con la siguiente:
    - Un encabezado de **Access Control-permitir-origen** en su respuesta que indica que se permiten los sitios de origen. Por ejemplo:
        
        `Access-Control-Allow-Origin: https://www.contoso.com`
        
    - Una página de error si el servidor no admite la solicitud de origen de cruz
    - Un encabezado de **Access Control-permitir-origen** con un carácter comodín que permite a todos los dominios:
        
        `Access-Control-Allow-Origin: *`
 
Para las solicitudes HTTP complejas, hay una solicitud de "comprobaciones" terminada primero para determinar si tiene permisos antes de enviar la solicitud completa.
 
## <a name="wildcard-or-single-origin-scenarios"></a>Escenarios de origen único o comodín

CORS en Azure CDN funcionarán automáticamente con ninguna configuración adicional cuando el encabezado de **Access Control-permitir-origen** es comodín (*) o un solo origen.  La CDN almacenará en caché la primera respuesta y las solicitudes subsiguientes usará el mismo encabezado.
 
Si ya se han realizado las solicitudes a la CDN antes de CORS se establece en el su origen, debe purgar contenido en el contenido de punto final para volver a cargar el contenido con el encabezado de **Access Control-permitir-origen** .
 
## <a name="multiple-origin-scenarios"></a>Varios escenarios de origen

Si necesita permitir una lista específica de orígenes para poder para CORS, puede ser un poco más complicado. El problema se produce cuando la CDN almacena el encabezado de **Access Control-permitir-origen** para el origen de la primera CORS.  Cuando un origen diferente de CORS realiza una solicitud posterior, se mostrará la CDN servida el encabezado de **Access Control-permitir-origen** en caché, que no coinciden.  Existen varias formas de corregir este problema.
 
### <a name="azure-cdn-premium-from-verizon"></a>CDN Azure Premium desde Verizon

La mejor manera de habilitar esta opción es usar **Azure CDN Premium desde Verizon**, que expone algunas funcionalidades avanzadas. 
 
Deberá crear [una regla](cdn-rules-engine.md) comprobar el encabezado de **origen** de la solicitud.  Si se trata de un origen válido, la regla establecerá el encabezado de **Access Control-permitir-origen** con el origen proporcionado en la solicitud.  Si no se permite el origen especificado en el encabezado de **origen** , la regla debe omitir el encabezado de **Access Control-permitir-origen** que hará que el explorador rechazar la solicitud. 
 
Hay dos formas de hacerlo con el motor de reglas.  En ambos casos, el encabezado de **Access Control-permitir-origen** del servidor de origen del archivo se omite completamente, motor de reglas de CDN completamente administra los orígenes CORS permitidos.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Una expresión regular con todos los orígenes válidos
 
En este caso, debe crear una expresión regular que incluye todos los orígenes que desea permitir: 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$
 
> [AZURE.TIP] **CDN de Azure desde Verizon** utiliza [Expresiones regulares de Perl Compatible](http://pcre.org/) como motor de expresiones regulares.  Puede usar una herramienta como [101 de expresiones regulares](https://regex101.com/) para validar la expresión regular.  Observe que el carácter "/" es válido en expresiones regulares y no necesita un carácter de escape, sin embargo, ese carácter de escape se considera práctica recomendada y esperamos algunos controles de validación de regex.

Si coincida con la expresión regular, la regla reemplazará el encabezado de **Access Control-permitir-origen** (si existe) desde el origen con el origen que envió la solicitud.  También puede agregar encabezados CORS adicionales, como **Control-permitir-métodos de acceso**.

![Ejemplo de reglas con expresión regular](./media/cdn-cors/cdn-cors-regex.png)
 
#### <a name="request-header-rule-for-each-origin"></a>Regla de encabezado de solicitud para cada origen.

En lugar de expresiones regulares, en su lugar puede crear una regla independiente para cada origen que desea permitir el uso de la **Solicitud comodín de encabezado** [coincide con la condición](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_1). Al igual que con el método de expresión regular, el motor de reglas solo establece los encabezados CORS. 
  
![Ejemplo de reglas sin expresión regular](./media/cdn-cors/cdn-cors-no-regex.png)

> [AZURE.TIP] En el ejemplo anterior, el uso del carácter comodín * indica el motor de reglas para que coincida con HTTP y HTTPS.
 
### <a name="azure-cdn-standard"></a>CDN Azure estándar

En perfiles de Azure CDN estándar, el único mecanismo para permitir que varios orígenes sin necesidad de utilizar el origen de comodín es usar el [almacenamiento en caché de cadena de consulta](cdn-query-string.md).  Debe habilitar la configuración de la cadena de consulta del extremo de CDN y, a continuación, utilice una cadena de consulta únicas para las solicitudes de cada dominio permitido. Esto da la CDN de almacenamiento en caché de un objeto independiente para cada cadena de consulta único. Este enfoque no es ideal, sin embargo, como producirán varias copias del mismo archivo en caché en la CDN.  

