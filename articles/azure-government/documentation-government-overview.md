<properties
    pageTitle="Documentación de gobierno Azure | Microsoft Azure"
    description="Proporciona una comparación de características e instrucciones sobre cómo desarrollar aplicaciones para la administración pública de Azure"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="ryansoc"
    manager="zakramer"
    editor=""/>

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="08/25/2016"
    ms.author="ryansoc"/>


#  <a name="azure-government-documentation-overview"></a>Introducción a la documentación administración pública de Azure

##  <a name="introduction-to-azure-government-documentation"></a>Introducción a la documentación de la administración pública de Azure

Este sitio, describe las capacidades de [Administración pública de Microsoft Azure](https://azure.microsoft.com/features/gov/) services y proporciona directrices generales aplicables a todos los clientes. Antes de incluir datos regulados específicamente en la suscripción de la administración pública de Azure, debe familiarizarse con las capacidades de administración pública de Azure y consulte a su equipo de cuenta si tiene alguna pregunta.

Debe consultar a la [Página de cumplimiento de centro de confianza de Microsoft Azure](http://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx) para obtener información actual sobre los servicios de administración pública de Azure cubierto por normativas y acreditaciones específicos. Servicios adicionales de Microsoft pueden estar disponibles, pero no están en el ámbito de los servicios de administración pública de Azure cubierto y no se tratan en este documento. Servicios de administración pública de Azure también podrían permitir utilizar una variedad de recursos adicionales, aplicaciones o servicios proporcionados por terceros: o Microsoft en términos independientes de directivas de uso y la declaración de privacidad de, que no se incluyen en el ámbito de este documento. Usted es responsable de revisar los términos de todas las tales ofertas de "complemento", como ofertas de Marketplace, para asegurarse de que cumplen sus necesidades respecto al cumplimiento.

Gobierno Azure está disponible para entidades que controlan los datos que están sujetos a ciertas regulaciones gubernamentales y los requisitos (como NIST 800.171 (DIB), ITAR, Tributaria 1075, DoD L4 y CJIS) donde se requiere el uso de la administración pública de Azure cumplir reglamentaciones. Azure clientes del Gobierno están sujetos a validación de elegibilidad.

Entidades con preguntas sobre elegibilidad para la administración pública de Azure deben consultar con su equipo de la cuenta.

##  <a name="principles-for-securing-customer-data-in-azure-government"></a>Principios para proteger los datos del cliente en la administración pública de Azure

Gobierno Azure proporciona una gama de características y servicios que puede usar para crear soluciones de nube para satisfacer sus necesidades de datos regulados de controlados. Una solución compatible con cliente es nada más que la implementación eficaz de las capacidades de administración pública de Azure del cuadro, junto con una práctica de seguridad sólidos de datos.
Cuando se hospeda una solución en la administración pública de Azure, Microsoft controla muchos de estos requisitos en el nivel de la infraestructura de nube.

En el diagrama siguiente muestra el modelo de defensa en profundidad Azure. Por ejemplo, Microsoft proporciona la infraestructura de nube básicas DDOS, junto con las capacidades de cliente, como los dispositivos de seguridad de aplicación específica del cliente que debe DDOS.

![texto alternativo](./media/azure-government-Defenseindepth.png)

Esta página describe los principios básicos para proteger los servicios y aplicaciones, que proporciona instrucciones y recomendaciones sobre cómo aplicar estos principios; en otras palabras, cómo clientes deben hacer uso inteligente de administración pública de Azure para cumplir con las obligaciones y responsabilidades necesarios para una solución que controla información ITAR.

Los principios general para proteger los datos del cliente son:
* Protección de datos mediante el cifrado
* Administración de información confidencial
* Aislamiento para restringir el acceso a datos

##  <a name="protecting-customer-data-using-encryption"></a>Proteger los datos del cliente con cifrado

Mitigar el riesgo y cumplimiento de reglamentaciones obligaciones va en coche el foco y la importancia de cifrado de datos creciente. Utilice una implementación de cifrado eficaces para mejorar las medidas de seguridad de red y las aplicaciones actuales y reducir el riesgo general de su entorno de nube.

### <a name="Overview"></a>Cifrado al resto
El cifrado de los datos almacenados se aplica a la protección de contenido del cliente de almacenamiento en disco. Hay varias maneras de que esto puede suceder:

### <a name="Overview"></a>Cifrado de servicio de almacenamiento

Cifrado de servicio de almacenamiento de Azure está habilitado en el nivel de cuenta de almacenamiento de blobs de bloque y se cifran automáticamente cuando escribe en Azure almacenamiento de blobs de página. Al leer los datos de almacenamiento de Azure, se descifra por el servicio de almacenamiento antes de que se devuelven. Use esta opción para proteger los datos sin tener que modificar o agregar código a todas las aplicaciones.

### <a name="Overview"></a>Cifrado de disco de Azure
Uso del cifrado de disco de Azure para cifrar la discos de sistemas operativos y los datos usados por una máquina Virtual de Azure. Integración con Azure clave depósito proporciona control y le ayuda a administrar las claves de cifrado de disco.

### <a name="Overview"></a>Cifrado de cliente
Cifrado de cliente integrado en Java y las bibliotecas de cliente de almacenamiento. NET, que pueden utilizar las API de depósito de clave de Azure, realizar este sencillo implementar. Use depósito de Azure clave para obtener acceso a la información confidencial en depósito de clave de Azure concretas con Azure Active Directory.

### <a name="Overview"></a>Cifrado en tránsito

El cifrado básico disponible para la conectividad de la administración pública de Azure es compatible con el protocolo de seguridad de nivel de transporte (TLS) 1.2 y certificados X.509. Federal procesamiento estándar información (FIPS) 140-2 algoritmos de cifrado de nivel 1 también se usan para conexiones de red de infraestructura entre los centros de datos de administración pública de Azure.  Windows 8-plus VM y recursos compartidos de archivos de Azure y Windows Server 2012 R2 puede usar SMB 3.0 para el cifrado entre la máquina virtual y el recurso compartido de archivos. Uso del cifrado del cliente para cifrar los datos antes de que se transfieran en almacenamiento en una aplicación de cliente y descifrar los datos después de se transfiere sin espacio de almacenamiento.

### <a name="Overview"></a>Prácticas recomendadas para el cifrado

* Máquinas virtuales de IaaS: Uso del cifrado de disco Azure. Activar el cifrado de servicio de almacenamiento para cifrar los archivos de disco duro virtual que se utilizan para realizar copias de seguridad de los discos de almacenamiento de Azure, pero sólo cifra los datos recién escritos. Esto significa que, si crea una máquina virtual y, a continuación, habilitar cifrado de servicio de almacenamiento en la cuenta de almacenamiento que contiene el archivo de disco duro virtual, se cifran únicamente los cambios, no el archivo original del disco duro virtual.
* Cifrado de cliente: Este es el método más seguro para cifrar los datos, ya que cifra antes de tránsito y cifra los datos en el resto. Sin embargo, es necesario agregar código a sus aplicaciones de almacenamiento, que no es recomendable hacer. En estos casos, puede usar HTTPs para sus datos en tránsito y cifrado de servicio de almacenamiento para cifrar los datos en el resto. Cifrado de cliente también implica más carga en el cliente, tendrá que tiene en cuenta en los planes de escalabilidad, especialmente si está cifrado y transferir una gran cantidad de datos.

Para obtener más información acerca de las opciones de cifrado en Azure, consulte la [Guía de seguridad de almacenamiento](/storage-security-guide).

##  <a name="protecting-customer-data-by-managing-secrets"></a>Proteger los datos del cliente con la administración de información confidencial

Administración de claves segura es fundamental para proteger los datos en la nube. Los clientes procure simplificar la administración de claves y mantener el control de claves usado por aplicaciones de nube y servicios para cifrar datos.

### <a name="Overview"></a>Prácticas recomendadas para administrar la información confidencial

* Usar clave de cámara para minimizar los riesgos de información confidencial que se expone a través de los archivos de configuración modificable, secuencias de comandos, o en el código fuente. Depósito de Azure clave cifra teclas (por ejemplo, las claves de cifrado para el cifrado de disco de Azure) y la información confidencial (como contraseñas), almacenarlos en FIPS 140-2 nivel 2 validar módulos de seguridad de hardware (HSM). Para obtener seguridad adicional, puede importar o generar claves en estos HSM.
* Plantillas y código de la aplicación sólo deben contener referencias URI a la información confidencial (lo que significa que la información confidencial real no está en el código, configuración o código fuente repositorios). Esto evita ataques de suplantación de identidad clave en repos internas o externas, como la recopilación robots en GitHub.
* Utilizar controles RBAC fuerte en depósito de clave. Si un operador de confianza deja la empresa o las transferencias a un nuevo grupo dentro de la compañía, debe impedir que puedan tener acceso a la información confidencial.  

Para obtener información adicional, vea [Depósito de clave para la administración pública de Azure](/azure-government/azure-government-tech-keyvault)

##  <a name="isolation-to-restrict-data-access"></a>Aislamiento para restringir el acceso a datos

Aislamiento consiste en usar los límites, segmentación y contenedores para limitar el acceso de datos a solo los usuarios autorizados, servicios y aplicaciones. Por ejemplo, la separación entre los inquilinos es un mecanismo de seguridad esenciales para plataformas de nube multiempresa como Microsoft Azure. Aislamiento lógico ayuda a evitar que a un inquilino interferir con las operaciones de cualquier otro inquilino.

### <a name="Overview"></a>Aislamiento del entorno
El entorno de administración pública de Azure es una instancia física es independiente del resto de la red de Microsoft. Esto se logra a través de una serie de controles físicos y lógicos que se incluyen las siguientes: seguridad de obstáculos físicos con dispositivos biométricos y cámaras.  Uso de credenciales específicas y la autenticación por personal de Microsoft que requieren acceso lógico al entorno de producción.  Toda la infraestructura de servicio para la administración pública de Azure se encuentra en los Estados Unidos.

#### <a name="Overview"></a>Aislamiento por cliente
Control de acceso de red de Azure implementa y separación a través de aislamiento de VLAN, ACL, cargan equilibradores y filtros IP

Los clientes pueden aislar aún más los recursos en suscripciones, grupos de recursos, redes virtuales y subredes.

Para obtener más información sobre aislamiento en Microsoft Azure, consulte la [sección de aislamiento de la Guía de seguridad de Azure](/azure-security-getting-started/#isolation).

Para información adicional y actualizaciones suscripción a la <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog de Microsoft Azure gobierno.</a>
