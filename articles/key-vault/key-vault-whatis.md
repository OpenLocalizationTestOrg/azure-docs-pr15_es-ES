<properties
    pageTitle="¿Qué es depósito de clave de Azure? | Microsoft Azure"
    description="Depósito de Azure clave ayuda a proteger las claves de cifrado y usado por aplicaciones de la nube y los servicios de información confidencial. Mediante depósito de clave de Azure, los clientes pueden cifrar claves y la información confidencial (como claves de autenticación, las claves de cuenta de almacenamiento, claves de cifrado de datos. Archivos PFX y contraseñas) mediante las teclas que están protegidas por módulos de seguridad de hardware (HSM)."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/10/2016"
    ms.author="cabailey"/>



# <a name="what-is-azure-key-vault"></a>¿Qué es depósito de clave de Azure?

Azure depósito clave está disponible en la mayoría de las regiones. Para obtener más información, consulte la [página de precios de depósito de clave](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introducción

Depósito de Azure clave ayuda a proteger las claves de cifrado y usado por aplicaciones de la nube y los servicios de información confidencial. Mediante la clave de cámara, puede cifrar las claves y la información confidencial (como claves de autenticación, las claves de cuenta de almacenamiento, claves de cifrado de datos. Archivos PFX y contraseñas) mediante las teclas que están protegidas por módulos de seguridad de hardware (HSM). Para obtener seguridad adicional, puede importar o generar claves en HSM. Si decide hacer esto, las claves de FIPS 140-2 nivel 2 HSM validados (hardware y firmware) de procesos de Microsoft.  

Tecla depósito simplifica el proceso de administración de claves y le permite mantener el control de las teclas de acceso y cifrar los datos. Los desarrolladores pueden crear claves para desarrollo y pruebas en minutos y sin problemas migrarlas a las claves de producción. Los administradores de seguridad pueden conceder (y revocar) permiso para claves, según sea necesario.

Utilice la siguiente tabla para mejor comprender cómo se puede ayudar a depósito clave para satisfacer las necesidades de los programadores y los administradores de seguridad.





| Función        | Declaración del problema           | Solución de Azure depósito clave  |
| ------------- |-------------|-----|
| Programador de una aplicación de Azure      | "Quiero escribir una aplicación para Azure que utiliza claves de firma y cifrado, pero quiero estas teclas sea externa de mi aplicación para que la solución es adecuada para una aplicación que se distribuye geográfico. <br/><br/>Quiero también estas teclas y la información confidencial que se desea proteger, sin tener que escribir el código yo. También quiero estas teclas y la información confidencial que sea fácil usar mis aplicaciones, con un rendimiento óptimo". | √ Claves se almacenan en un depósito e invoca URI cuando sea necesario.<br/><br/> Teclas √ están protegidas por Azure, mediante algoritmos estándar del sector, longitudes de clave y módulos de seguridad de hardware (HSM).<br/><br/> Teclas √ se procesan en HSM que residen en los mismos centros de datos de Azure como las aplicaciones. Esto proporciona mayor confiabilidad y latencia menor que si las teclas residen en una ubicación diferente, como local.|
| Programador de Software como servicio (SaaS)      |"No quiero la responsabilidad o responsabilidad potencial para claves de inquilinos de Mis clientes y la información confidencial. <br/><br/>Quiero los clientes y administrar sus claves para que puedo concentrarse en hacer ¿qué puedo hacer mejor, que proporciona las principales características de software." | Los clientes √ pueden importar sus propias claves en Azure y administrarlos. Cuando una aplicación de SaaS debe llevar a cabo operaciones de cifrado mediante las teclas de sus clientes, depósito de clave no estas operaciones en nombre de la aplicación. La aplicación no ve las claves de los clientes.|
| Oficial de seguridad principal (CSO) | "Deseo saber que nuestras aplicaciones cumplan FIPS 140-2 nivel 2 HSM para administración de claves segura. <br/><br/>Deseo para asegurarse de que la organización tiene el control del ciclo de vida clave y puede supervisar el uso de la clave. <br/><br/>Y aunque usamos varios servicios de Azure y recursos, quiero administrar las teclas desde una única ubicación en Azure."     |√ HSM son FIPS 140-2 nivel 2 validar.<br/><br/>√ Clave depósito está diseñada para que no vea ni extraer las claves de Microsoft.<br/><br/>√ Cerca de registro en tiempo real de uso de la clave.<br/><br/>√ La cámara ofrece una interfaz única, independientemente de cuántos depósitos tiene en Azure, qué regiones soporte técnico y usan en las aplicaciones. |


Cualquier persona que tenga una suscripción de Azure puede crear y usar depósitos claves. Aunque clave depósito ventajas a los desarrolladores y administradores de seguridad, podría implementado y administrada por el Administrador de la organización que administra otros servicios de Azure para una organización. Por ejemplo, este administrador se inicie sesión con una suscripción de Azure, cree un depósito para la organización en la que se va a almacenar claves y, a continuación, se responsable de las tareas operativas, como:

+ Crear o importar una clave o un secreto
+ Revocar o eliminar una clave o secreto
+ Autorizar usuarios o aplicaciones para tener acceso a la cámara clave, por lo que, a continuación, pueden administrar o usar sus claves y la información confidencial
+ Configurar el uso de la clave (por ejemplo, firmar o cifrar)
+ Uso de claves de Monitor

Este administrador desea proporcionar a los desarrolladores URI para llamar desde sus aplicaciones y proporcionar su administrador de seguridad con información de registro de uso de la clave. 

   ![Información general de Azure depósito clave][1]

Los desarrolladores también pueden administrar las teclas directamente, mediante las API de. Para obtener más información, vea [Guía del desarrollador de depósito de clave](key-vault-developers-guide.md).

## <a name="next-steps"></a>Pasos siguientes

Para obtener un tutorial de introducción al obtener un administrador, consulte [Introducción a Azure clave depósito](key-vault-get-started.md).

Para obtener más información sobre el uso del registro para depósito de clave, vea [Registro de Azure clave depósito](key-vault-logging.md).

Para obtener más información sobre el uso de claves y la información confidencial con Azure clave cámara, vea [acerca de las teclas, información confidencial y certificados](https://msdn.microsoft.com/library/azure/dn903623\(v=azure.1\).aspx).


<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
