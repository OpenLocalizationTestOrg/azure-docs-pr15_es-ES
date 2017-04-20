<properties
    pageTitle="Introducción de pila clave depósito Azure | Microsoft Azure"
    description="Obtenga información sobre cómo Azure pila clave depósito administra claves y la información confidencial"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="introduction-to-key-vault-in-azure-stack"></a>Introducción a depósito clave en pila de Azure #

## <a name="before-you-start"></a>Antes de empezar

En este artículo se supone lo siguiente:

- El lector tiene acceso a una suscripción que tenga Azure clave depósito habilitado.
- El SDK de PowerShell de Azure está configurado y listo.
- La versión TP2, todas las operaciones de orientación de inquilinos pueden realizarse únicamente de PowerShell.

## <a name="key-vault-basics"></a>Conceptos básicos de la cámara de clave

Depósito de clave en pila de Azure le ayuda a proteger las claves de cifrado y usar información confidencial que las aplicaciones y servicios en la nube. Mediante depósito de clave, puede cifrar las claves y la información confidencial (como claves de autenticación, las claves de cuenta de almacenamiento, las claves de cifrado de datos, archivos .pfx y contraseñas).

Tecla depósito simplifica el proceso de administración de claves y le permite mantener el control de las teclas de acceso y cifrar los datos. Los desarrolladores pueden crear claves para desarrollo y pruebas en minutos y sin problemas migrarlas a las claves de producción. Los administradores de seguridad pueden conceder (y revocar) permiso para claves, según sea necesario.

Cualquier persona que tenga una suscripción de pila de Azure puede crear y usar depósitos claves. Aunque clave depósito ventajas a los desarrolladores y administradores de seguridad, puede implementar y administrada por un administrador que administra otros servicios de pila de Azure para una organización. Por ejemplo, el administrador puede iniciar sesión con una suscripción de pila de Azure, cree un depósito para la organización en la que desea almacenar claves y es responsable de estas tareas operativas:

- Crear o importar una clave o un secreto
- Revocar o eliminar una clave o secreto
- Autorizar usuarios o aplicaciones para tener acceso a la cámara clave, por lo que, a continuación, pueden administrar o usar sus claves y la información confidencial
- Configurar el uso de la clave (por ejemplo, firmar o cifrar)

El administrador puede ofrecer a los desarrolladores URI para llamar desde sus aplicaciones y proporcionar un administrador de seguridad con información de registro de uso de la clave.

Los desarrolladores también pueden administrar las teclas directamente, mediante las API de. Para obtener más información, vea a Guía del desarrollador de depósito de clave.

## <a name="scenarios"></a>Escenarios

La siguiente tabla muestra algunos de los escenarios donde clave depósito pueden ayudar a satisfacer las necesidades de los programadores y administradores de seguridad:


### <a name="developer-for-an-azure-stack-application"></a>Programador de una aplicación de la pila de Azure

**Problema**: quiero escribir una aplicación para la pila de Azure que usa claves para la firma y cifrado, pero quiero que sean externas de mi aplicación para que la solución es adecuada para una aplicación que se distribuye geográfico.

**Instrucción**: claves se almacenan en un depósito e invoca URI cuando sea necesario.


### <a name="developer-for-software-as-a-service-saas"></a>Programador de software como servicio (SaaS)

**Problema:** No quiero la responsabilidad o responsabilidad potencial para claves de inquilinos de Mis clientes y la información confidencial.

**Instrucción:** Los clientes pueden importar sus propias claves en pila de Azure y administrarlos. Quiero que los clientes propietario y administrar sus claves para que puedo concentrarse en hacer ¿qué puedo hacer mejor, que proporciona las principales características de software.


### <a name="chief-security-officer-cso"></a>Director de seguridad (CSO)

**Problema:** Deseo para asegurarse de que la organización tiene el control del ciclo de vida clave y puede supervisar el uso de la clave.

**Instrucción** Depósito de clave está diseñada para que no vea ni extraer las claves de Microsoft.  Cuando una aplicación debe llevar a cabo operaciones de cifrado mediante las teclas de los clientes, depósito clave lo hace en nombre de la aplicación. La aplicación no ve las claves de los clientes.  Aunque utilizamos varios servicios de pila de Azure y recursos, deseo administrar las claves desde una única ubicación en la pila de Azure. La cámara ofrece una interfaz única, independientemente de cuántos depósitos tiene en pila de Azure, qué regiones soporte técnico y usan en las aplicaciones.

## <a name="next-steps"></a>Pasos siguientes

[Introducción a depósito clave](azure-stack-kv-getting-started.md)
