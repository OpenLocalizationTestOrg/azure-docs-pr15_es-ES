<properties
   pageTitle="Clasificación de datos de Azure | Microsoft Azure"
   description="Este artículo proporciona una introducción a los fundamentos de la clasificación de datos y los resalta su valor, específicamente en el contexto de la nube informática y el uso de Microsoft Azure"
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="yurid"/>

# <a name="data-classification-for-azure"></a>Clasificación de datos de Azure

En este artículo se proporciona una introducción a los fundamentos de la clasificación de datos y se resalta su valor, específicamente en el contexto de la nube informática y el uso de Microsoft Azure. 

## <a name="data-classification-fundamentals"></a>Aspectos básicos de la clasificación de datos

Clasificación correcta de los datos en una organización requiere conocimiento amplio de las necesidades de su organización y comprender donde residen los activos de datos.  
 
Hay datos en uno de estos tres estados básicos: 

- En el resto 
- En proceso 
- En tránsito 
 
Los tres estados requieren soluciones técnicas únicas para la clasificación de datos, pero los principios aplicados de clasificación de datos deben ser el mismo para cada uno. Datos que se clasifican como confidencial deben mantenerse confidencial en el resto, en proceso y en tránsito. 
 
También pueden ser datos estructurados o no estructurados. Procesos de clasificación típico para los datos estructurados que se encuentra en hojas de cálculo y bases de datos son menos compleja y lenta de administrar que los datos no estructurados como documentos, código fuente y correo electrónico. 

> [AZURE.TIP] Para obtener más información acerca de los procedimientos recomendados para datos y capacidades de Azure cifrado lea [Prácticas recomendadas de cifrado de datos de Azure](azure-security-data-encryption-best-practices.md)

En general, las organizaciones tienen más datos no estructurados de datos estructurados. Independientemente de si los datos están estructurados o, es importante para administrar confidencialidad de datos. Cuando se implementa correctamente, clasificación de datos ayuda a garantizar que se administran los activos con mayor supervisión de activos de datos que se consideren público o libre para distribuir los datos confidenciales o importantes. 

### <a name="controlling-access-to-data"></a>Controlar el acceso a datos 

Autenticación y la autorización se suelen confundir entre sí y sus funciones interpretarse incorrectamente. En realidad son muy distintas, como se muestra en la siguiente ilustración.  

![Control y acceso a datos](./media/azure-security-data-classification/azure-security-data-classification-fig1.png)

### <a name="authentication"></a>Autenticación 

Autenticación normalmente consta de al menos dos partes: un identificador de usuario o el nombre de usuario para identificar un usuario y un símbolo, como una contraseña para confirmar que la credencial de nombre de usuario es válida. El proceso no proporciona el usuario autenticado con acceso a los elementos o servicios. comprueba que el usuario es quien dicen que son.   

> [AZURE.TIP] [Azure Active Directory](../active-directory/active-directory-whatis.md) proporciona servicios de identidad basada en nube que permiten autenticar y autorizar a los usuarios. 

### <a name="authorization"></a>Autorización
 
Autorización es el proceso de ofrecer la posibilidad de obtener acceso a una aplicación, conjunto de datos, archivo de datos o algún otro objeto de un usuario autenticado. Asignar los usuarios autenticados los derechos para usar, modificar o eliminar los elementos que tengan acceso requiere atención a la clasificación de datos. 

Autorización correcta requiere la implementación de un mecanismo para validar las necesidades de los usuarios individuales para tener acceso a archivos e información que basa en una combinación de funciones, la directiva de seguridad y directiva de riesgo consideraciones. Por ejemplo, no es necesario tener acceso a todos los empleados datos de determinadas aplicaciones de línea de negocio (LOB) y sólo un pequeño subconjunto de los empleados probablemente tendrá acceso a los archivos de recursos humanos (HR). Pero, para las organizaciones controlar quién tiene acceso a datos, así como cuándo y cómo, debe ser un sistema eficaz de autenticación de usuarios en su lugar. 

> [AZURE.TIP] en Microsoft Azure, asegúrese de aprovechar el Control de acceso de Azure Role-Based (RBAC) para conceder solo la cantidad de acceso que necesitan los usuarios para realizar su trabajo. Para obtener más información, lea [las asignaciones de roles de uso para administrar el acceso a los recursos de Azure Active Directory](../active-directory/role-based-access-control-configure.md) . 

### <a name="roles-and-responsibilities-in-cloud-computing"></a>Roles y responsabilidades de la informática en nube 

Aunque los proveedores de nube pueden ayudar a administrar los riesgos, clientes tenga que asegurarse de que administración de clasificación de datos y cumplimiento correctamente se implementa para proporcionar el nivel adecuado de servicios de administración de datos.  
 
Responsabilidades de clasificación de datos varían en función de qué modelo de servicio de nube está en su lugar, tal como se muestra en la siguiente ilustración. Los tres modelos de servicio de nube principal son infraestructura como servicio (IaaS), plataforma como servicio (PaaS) y software como servicio (SaaS). Implementación de mecanismos de clasificación de datos también varían según la dependencia y las expectativas del proveedor de nube. 

![Roles](./media/azure-security-data-classification/azure-security-data-classification-fig2.png)

Aunque es responsable de clasificar los datos, proveedores de nube deben hacer compromisos escritos acerca de cómo se seguro y mantener la privacidad de los datos del cliente almacenados en la nube.  

- Requisitos de **los proveedores de IaaS** están limitados a garantizar que el entorno virtual puede incluir capacidades de clasificación de datos y los requisitos de cumplimiento de cliente. Proveedores de IaaS tienen una función más pequeña de clasificación de datos porque solo necesitan para asegurarse de que los datos del cliente direcciones requisitos de cumplimiento. Sin embargo, proveedores deben garantizar que sus entornos virtuales dirección requisitos de clasificación de datos además de proteger sus centros de datos.
- **Proveedores de PaaS** responsabilidades pueden combinarse porque la plataforma se podría usar en un enfoque por capas para proporcionar seguridad para una herramienta de clasificación. Proveedores de PaaS pueden ser responsables de autenticación y posiblemente algunas reglas de autorización y deben proporcionar seguridad y capacidades de clasificación de datos a su nivel de aplicación. Mucho como proveedores de IaaS, PaaS proveedores necesitan asegurarse de que su plataforma cumple con los requisitos de clasificación de datos pertinentes.
- **Proveedores de SaaS** con frecuencia se considerarse como parte de una cadena de autorización y se necesita para asegurarse de que los datos almacenados en la SaaS aplicación puede controlar el tipo de clasificación. Aplicaciones SaaS pueden utilizarse para aplicaciones LOB y por su necesidad de naturaleza para proporcionar los medios para autenticar y autorizar los datos que se utiliza y almacena. 

## <a name="classification-process"></a>Proceso de clasificación 

¿Muchas organizaciones que comprender la necesidad de clasificación de datos y desean implementarlo boca un reto básico: dónde comenzar?

Una forma sencilla y eficaz de implementar la clasificación de datos es usar la comprobación de PLAN, DO, el funcionamiento de los modelos de [MOF](https://technet.microsoft.com/solutionaccelerators/dd320379.aspx). La siguiente ilustración gráficos las tareas necesarias para implementar correctamente la clasificación de datos en este modelo.  

1. **Planear**. Identificar los activos de datos, un custodio de datos para implementar el sistema de clasificación y desarrollar perfiles de protección. 
2. **DO**. Después de que se ha acordado políticas de clasificación de datos, implementar el programa y tecnologías de cumplimiento según sea necesario para datos confidenciales.  
3. **Comprobar**. Comprobar y validar informes para asegurarse de que las herramientas y métodos que se usa un modo eficaz direccionamiento las directivas de clasificación. 
4. **Acta**. Revisar el estado de acceso a datos y archivos de revisión y los datos que requieren revisión mediante una metodología de reclasificación y revisión para adoptar los cambios y a los nuevos riesgos de dirección.  

![Planear,, active, actuar](./media/azure-security-data-classification/azure-security-data-classification-fig3.png)
 
###<a name="select-a-terminology-model-that-addresses-your-needs"></a>Seleccionar un modelo de terminología que responde a sus necesidades
 
Existen varios tipos de procesos para la clasificación de datos, incluidos los procesos manuales, procesos basado en la ubicación que clasifican los datos según ubicación de un usuario o del sistema, basado en la aplicación procesos como la clasificación de base de datos y procesos usados por varias tecnologías, en la sección "Proteger datos confidenciales" más adelante en este artículo se describen algunas de las cuales automatizados.  
 
Este artículo presenta dos modelos terminología generalized basados en modelos bien usados y respeta la industria. Estos modelos terminología, ambos proporcionan tres niveles de sensibilidad clasificación, se muestran en la tabla siguiente.  

> [AZURE.NOTE] al clasificar un archivo o recurso que combina los datos que normalmente se clasifica en distintos niveles, el nivel superior de clasificación actual debe establecer la clasificación general. Por ejemplo, un archivo que contiene datos confidenciales y restringidos debe clasificar como restringido.  

| **Sensibilidad**   | **Modelo de terminología 1**   | **Modelo de terminología 2** |
|--------------------|---------------------------|-------------------------|
| Alta               | Confidencial              | Restringido              |
| Medio             | Sólo para uso interno     | Confidencial               |
| Bajo                | Público                    | Sin restricciones            |

#### <a name="confidential-restricted"></a>Confidencial (restringido) 

Información que se clasifica como confidencial o restringido incluye datos que pueden ser graves a uno o más personas o las organizaciones si comprometido o perdido. Dicha información se proporciona con frecuencia en una base de "necesita saber" y puede incluir: 

- Datos personales, incluida la información de identificación personal como Seguridad Social o números de identificación nacional, números de passport, números de tarjetas de crédito, del controlador números de licencia, registros médicos y números de identificación de directiva de seguros de salud.  
- Registros financieros, incluidos los números de cuenta financiera como comprobar o cuenta de inversión. 
- Material de empresa, como los documentos o los datos de propiedad intelectual o específica.  
- Datos legales, incluidos los materiales de abogados privilegios posibles. 
- Datos de autenticación, como las claves de cifrado privada, pares de contraseña de nombre de usuario u otras secuencias de identificación como archivos claves biométricos privados. 

Los datos que se clasifican como confidencial con frecuencia tienen normativas y requisitos de cumplimiento para el control de datos. 

#### <a name="for-internal-use-only-sensitive"></a>Para uso interno solo (dependientes)
 
Información que se clasifica como de confidencialidad medio incluye datos que no tenga un impacto grave en una persona o la organización si perdiera o destruyera y archivos. Dicha información se incluye: 

- Correo electrónico, la mayoría de las cuales se puede eliminar o distribuida sin que se produzca una crisis (excepto los buzones de correo y correo electrónico de personas que se identifican en la clasificación confidencial).  
- Documentos y archivos que no incluya datos confidenciales.
 
Normalmente, esta clasificación incluye cualquier cosa que no sea confidencial. Esta clasificación puede incluir la mayoría de los datos de empresa, porque la mayoría de los archivos que se administra o se utilizan diarias se pueden clasificar como confidencial. Con la excepción de los datos que se hace público o confidencial, todos los datos dentro de una organización de empresa se pueden clasificar como confidencial de forma predeterminada. 

#### <a name="public-unrestricted"></a>Público (sin restricciones)
 
Información que se clasifica como público incluye datos y archivos que no son fundamentales para necesidades empresariales u operaciones. Esta clasificación también puede incluir datos que se ha publicado deliberadamente del público para su uso, como material de marketing o presione anuncios. Además, esta clasificación puede incluir datos como mensajes de correo electrónico no deseado almacenados por un servicio de correo electrónico. 

### <a name="define-data-ownership"></a>Definir la propiedad de datos
 
Es importante establecer una cadena borrar apoyo de propiedad para todos los activos de datos. En la siguiente tabla identifica las funciones de la propiedad de datos distintos de los esfuerzos de clasificación de datos y sus respectivos derechos.  

| **Función**        | **Crear**    | **Modificar o eliminar**   | **Delegado**  | **Lectura**    | **Archivar y restaurar**   |
|-----------------|---------------|---------------------|---------------|-------------|-----------------------|
| Propietario           | X             | X                   | X             | X           | X                     |
| Custodio       |               |                     | X             |             |                       |
| Administrador   |               |                     |               |             | X                     |
| Usuario\*          |               | X                   |               | X           |                       |
**Los usuarios se le ha concedido derechos adicionales como edición y eliminar por custodia* 

> [AZURE.NOTE] Esta tabla no proporciona una lista completa de roles y derechos, pero simplemente un representante de ejemplo. 

El **propietario de activos de datos** es el creador original de los datos, que pueden delegar la propiedad y asignarle a un custodio. Cuando se crea un archivo, el propietario debería poder asignar una clasificación, lo que significa que tienen una responsabilidad de entender lo que necesita se clasifican como confidencial en función de las directivas de su organización. Todos los datos del propietario de activos de datos pueden ser clasificados automáticamente en cuanto a uso interno solo (dependientes) a menos que son responsables de disponiendo o crear tipos de datos (restringidas) confidencial. Rol del propietario se cambia con frecuencia, después de que se clasifican los datos. Por ejemplo, el propietario puede crear una base de datos de la información clasificado y ceder sus derechos a la custodia de datos.  

> [AZURE.NOTE] los propietarios de activos de datos suelen usar una combinación de servicios, dispositivos y medios, algunos de los cuales son personales y algunos de los que pertenecen a la organización. Una directiva de Borrar organización puede ayudar a garantizar que el uso de dispositivos como portátiles y dispositivos inteligentes es conforme a las directrices de clasificación de datos.  

El propietario de activos (o su delegado) para administrar el activo según agreements con el propietario de activos o de acuerdo con los requisitos de directiva aplicable asigna la **custodia de activos de datos** . Lo ideal es que, la función custodio se puede implementar en un sistema automatizado. Un custodio activo garantiza que los controles de acceso es necesario se proporcionan y es responsables de administrar y proteger los activos de delegado a su cuidado. Las responsabilidades de la custodia de activos se incluyen:  

- Proteger los activos conforme a la dirección del propietario de activos o de acuerdo con el propietario de activos 
- Garantizar el cumplan de directivas de clasificación 
- Informar a los propietarios de activos de los cambios a acordado controles y procedimientos de protección antes de esos cambios surtan efecto 
- Informa al propietario del activo sobre cambios o eliminación de responsabilidades de custodio de activos 
- Un **Administrador** representa un usuario que es responsable de garantizar que se mantiene la integridad, pero no son un propietario de activos de datos, custodio o usuario. De hecho, muchos de los roles de administrador proporcionan servicios de administración del contenedor de datos sin tener acceso a los datos. El rol de administrador incluye los activos de copia de seguridad y restauración de los datos, mantener registros de los activos y elegir, para adquirir y funcionamiento de los dispositivos y almacenamiento esa casa. 
- El usuario activo incluye todos los usuarios que se le ha concedido acceso a datos o un archivo. Asignación de acceso a menudo se delega el propietario de la custodia de activos.  

### <a name="implementation"></a>Implementación
  
Consideraciones de administración se aplican a todas las metodologías de clasificación. Estas consideraciones que necesite incluir detalles acerca de la persona, ¿qué, dónde, cuándo y por qué un activo de datos ' d se utiliza, acceso, cambiado o eliminado. La administración de activos debe realizarse con una descripción de cómo una organización ve sus riesgos, pero se puede aplicar una metodología simple según se define en el proceso de clasificación de datos. Consideraciones adicionales para la clasificación de datos incluyen la introducción de nuevas aplicaciones y herramientas y administración de cambios después de implementar un método de clasificación.  

### <a name="reclassification"></a>Reclasificación
 
Reclasificar o cambiar el estado de clasificación de un activo de datos debe hacerse cuando un usuario o sistema determina que ha cambiado el perfil de importancia o riesgo de activos de datos. Este esfuerzo es importante para garantizar que el estado de clasificación sigue siendo válido y las actuales. Puede ser clasificado automáticamente o basado en uso por un custodio de datos o el propietario de datos mayor parte del contenido que no se clasifica manualmente. 

### <a name="manual-data-reclassification"></a>Reclasificación manual de datos
 
Lo ideal es que, para ello garantizará que los detalles de un cambio se capturan y auditoría. Por motivos de confidencialidad, o para los registros que se mantiene el formato de papel o un requisito para revisar los datos que originalmente estaban mal clasificados, sería la razón más probable modificar la clasificación manual. Dado que este documento considera la clasificación de datos y mover los datos en la nube, los esfuerzos de reclasificación manual requeriría la atención en el caso por caso y una revisión de administración de riesgos sería ideal para satisfacer los requisitos de clasificación. En general, por ejemplo un esfuerzo consideraría la directiva de la organización sobre qué tiene que ser clasificados, el estado de clasificación predeterminado (todos los datos y archivos ser confidencial pero no confidencial) y tomar excepciones para los datos de alto riesgo. 

### <a name="automatic-data-reclassification"></a>Reclasificación de datos automática
 
Reclasificación de datos automática usa la misma regla general como clasificación manual. La excepción es que soluciones automatizadas pueden asegurarse de que las reglas se seguidos y aplicadas según sea necesario. Clasificación de datos puede realizarse como parte de una directiva de aplicación de clasificación de datos, que se puede aplicar cuando los datos se almacenan en uso y tránsito mediante la tecnología de autorización.

- Basado en la aplicación. Mediante algunas aplicaciones de forma predeterminada, establece un nivel de clasificación. Por ejemplo, datos de software de administración (CRM) de relación de cliente, recursos humanos y herramientas de administración de registros de salud están confidenciales de forma predeterminada. 
- Basado en la ubicación. Ubicación de datos puede ayudar a identificar confidencialidad de datos. Por ejemplo, datos que se almacenan por un HR o el departamento de finanzas son más probable que sea confidencial de naturaleza.  
 
### <a name="data-retention-recovery-and-disposal"></a>Eliminación, la recuperación y retención de datos 

Recuperación de datos y la eliminación, como reclasificación de datos, es un aspecto esencial de la administración de activos de datos. Los principios de recuperación de datos y eliminación se definida por una directiva de retención de datos y se aplican en la misma manera que reclasificación de datos; Por ejemplo un esfuerzo que realiza las funciones custodio y Administrador como una tarea de colaboración.  

Si no tiene una directiva de retención de datos puede significar una pérdida de datos o incumplimiento de los requisitos reglamentarios y legales detección. La mayoría de las organizaciones que no tienen una directiva de retención de datos definidos claramente suelen usar una directiva de retención predeterminado "mantener todo". Sin embargo, como una directiva de retención tiene riesgos adicionales en escenarios de servicios de nube. 

Por ejemplo, puede considerarse una directiva de retención de datos para proveedores de servicios de nube como "la duración de la suscripción" (siempre y cuando el servicio se paga por, se conservarán los datos). Acuerdo de pago de retención no podrá tratar directivas de retención normativos o de la empresa. Definir una directiva para datos confidenciales puede asegurarse de que los datos se almacenan y se eliminan de acuerdo con los procedimientos recomendados. Además, puede crearse una directiva de archivado para formalizar comprender qué datos deben eliminarse y cuándo. 

Directiva de retención de datos debería tratar los necesarios normativas y requisitos de cumplimiento, así como los requisitos de retención legal corporativas. Datos clasificados podrían provocar preguntas sobre la duración de retención y las excepciones de datos que se ha guardado con un proveedor. dichas preguntas están más probable que los datos que no se han clasificado correctamente. 

> [AZURE.TIP] más información acerca de las directivas de retención de datos de Azure y más leer el [Contrato de suscripción de Microsoft Online](https://azure.microsoft.com/support/legal/subscription-agreement/)

## <a name="protecting-confidential-data"></a>Proteger datos confidenciales
  
Después de que se clasifican los datos, buscar e implementar formas de proteger datos confidenciales se convierte en una parte integral de cualquier estrategia de implementación de protección de datos. Proteger datos confidenciales requiere atención adicional sobre cómo se almacenan los datos y se transmite en arquitecturas convencionales, así como en la nube. 

Esta sección proporciona información básica sobre algunas tecnologías que pueden automatizar los esfuerzos de cumplimiento para ayudar a proteger los datos que se han clasificado como confidencial. 
 
Como se muestra en la siguiente ilustración, se pueden implementar estas tecnologías como local o soluciones basadas en la nube, o en modo híbrido, con algunos de ellos implementado local y otros en la nube. (Algunas tecnologías, como el cifrado y administración de derechos, también amplían a los dispositivos de usuario).  

![Tecnologías](./media/azure-security-data-classification/azure-security-data-classification-fig4.png)

### <a name="rights-management-software"></a>Software de administración de derechos  

Una solución para evitar la pérdida de datos es software de administración de derechos. A diferencia de enfoques que intenten interrumpir el flujo de información en los puntos de salida de una organización, software de administración de derechos funciona en profundidad niveles de tecnologías de almacenamiento de datos. Se cifran los documentos y control sobre quién puede descifrar ellos usa controles de acceso que se definen en una solución de control de autenticación como un servicio de directorio.  

> [AZURE.TIP] Puede usar Azure Rights Management (RMS de Azure) como la solución de protección de la información para proteger los datos en escenarios diferentes. Lectura [¿Qué es la administración de derechos de Azure?](https://docs.microsoft.com/rights-management/understand-explore/what-is-azure-rms) para obtener más información sobre esta solución Azure.

Algunas de las ventajas del software de administración de derechos se incluyen: 

- Protegidos información confidencial. Los usuarios pueden proteger sus datos directamente mediante aplicaciones habilitadas para administración de derechos. Se necesita ningún paso adicional: creación de documentos, enviar correo electrónico y publicar datos ofrecen una experiencia de protección de datos coherentes. 
- Protección viaja con los datos. Los clientes permanecen en el control de quién tiene acceso a sus datos, ya sea en la nube, la infraestructura de TI existente, o en el escritorio del usuario. Pueden elegir las organizaciones cifrar sus datos y restringir el acceso según sus requisitos empresariales. 
- Directivas de protección de la información de forma predeterminada. Los administradores y usuarios pueden usar directivas estándares para muchos escenarios empresariales comunes, como "Empresa confidencial: de sólo lectura" y "No reenviar". Un amplio conjunto de uso se admiten derechos como leer, copiar, imprimir, guardar, editar y reenviar para permitir la flexibilidad para definir los derechos de uso personalizada. 

> [AZURE.TIP] para proteger los datos en el almacenamiento de Azure mediante el [Cifrado de servicio de almacenamiento de Azure](../storage/storage-service-encryption.md) para datos almacenados. También puede usar el [Cifrado de disco de Azure](azure-security-disk-encryption.md) para ayudar a proteger los datos que se encuentran en discos virtuales usados para Azure máquinas virtuales de Windows.

### <a name="encryption-gateways"></a>Puertas de enlace de cifrado

Trabajar con puertas de enlace de cifrado en sus propias capas para proporcionar servicios de cifrado mediante el redireccionamiento de todo el acceso a datos basados en la nube. Este enfoque no debe confundirse con la de una red privada virtual (VPN). Las puertas de enlace de cifrado están diseñados para proporcionar una capa transparente a soluciones basadas en la nube.   

Las puertas de enlace de cifrado pueden proporcionar un medio para administrar y proteger los datos que se han clasificado como confidencial mediante el cifrado de los datos al transmitirlos, así como datos almacenados.  
 
Las puertas de enlace de cifrado se colocan en el flujo de datos entre los dispositivos de usuario y datos de la aplicación centros para proporcionar servicios de cifrado/descifrado. Estas soluciones, como VPN, principalmente son soluciones locales. Están diseñados para proporcionar control sobre claves de cifrado, lo que ayuda a reduce el riesgo de colocar los datos y la clave de administración con un proveedor de terceros. Estas soluciones están diseñadas, al igual que el cifrado, para trabajar sin problemas y transparente entre los usuarios y el servicio. 

> [AZURE.TIP] Puede usar Azure ExpressRoute para ampliar sus redes local en la nube de Microsoft a través de una conexión privada dedicada. Obtenga [información general técnica de ExpressRoute](../expressroute/expressroute-introduction.md) para obtener más información acerca de esta funcionalidad. Otras opciones para cruzada local de conectividad entre su red local y [Azure es una VPN de sitio a otro](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

### <a name="data-loss-prevention"></a>Prevención de pérdida de datos 
Pérdida de datos (también se conoce como filtrado de datos) es un factor importante y la prevención de pérdida de datos externos a través de personal malintencionadas como accidentales es fundamental para muchas organizaciones.  
 
Tecnologías de (DLP) de prevención de pérdida de datos pueden ayudar a garantizar que soluciones, como servicios de correo electrónico no transmita datos que se han clasificado como confidencial. Organizaciones pueden aprovechar las ventajas de características DLP en los productos existentes para ayudar a evitar la pérdida de datos. Estas características usan directivas que se pueden crear fácilmente desde cero o mediante una plantilla proporcionada por el proveedor de software.  
 
Tecnologías DLP pueden realizar análisis detallado de contenido a través de coincidencias de palabra clave, coincidencias de diccionario, evaluación de expresiones regulares y otro examen de contenido para detectar el contenido que infringe las directivas de DLP organizativas. Por ejemplo, DLP puede ayudar a evitar la pérdida de los siguientes tipos de datos: 

- Seguridad social y números de identificación nacional 
- Información bancaria 
- Números de tarjeta de crédito  
- Direcciones IP 

Algunas tecnologías DLP también proporcionan la capacidad para invalidar la configuración de DLP (por ejemplo, si una organización necesita transmitir información de número de seguridad Social a un procesador de nóminas). Además, es posible configurar DLP para que los usuarios se les notifica antes de par intentan enviar información confidencial que no debería ser transmitida. 

> [AZURE.TIP] Puede usar las capacidades de Office 365 DLP para proteger sus documentos. Lectura [controles de cumplimiento de Office 365: prevención de pérdida de datos](https://blogs.office.com/2013/10/28/office-365-compliance-controls-data-loss-prevention/) para obtener más información.

## <a name="see-also"></a>Vea también

- [Prácticas recomendadas de cifrado de datos de Azure](azure-security-data-encryption-best-practices.md)
- [Prácticas recomendadas de seguridad de control de Azure la administración de identidades y acceso](azure-security-identity-management-best-practices.md)
- [Blog del equipo de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/)
- [Centro de respuesta de seguridad de Microsoft](https://technet.microsoft.com/library/dn440717.aspx)

