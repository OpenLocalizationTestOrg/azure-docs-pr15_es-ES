<properties
   pageTitle="Habilitar la directiva de SSL y llevar a cabo SSL de puerta de enlace de aplicación | Microsoft Azure"
   description="Esta página proporciona una descripción general de la puerta de enlace de aplicación SSL de principio a fin de soporte técnico."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="amsriva"/>

# <a name="enabling-ssl-policy-and-end-to-end-ssl-on-application-gateway"></a>Habilitar la directiva de SSL y llevar a cabo SSL de puerta de enlace de aplicación

## <a name="overview"></a>Información general

Puerta de enlace de aplicación admite terminación SSL en la puerta de enlace, después de que el tráfico fluye normalmente sin cifrar a los servidores back-end. Permite a los servidores web se unburdened aérea de cifrado/descifrado costosas. Sin embargo para algunos clientes sin cifrar comunicaciones a los servidores back-end no es una opción aceptable. Esto puede deberse a requisitos de seguridad y cumplimiento o la aplicación solo podrá Aceptar conexión segura. Para estas aplicaciones, puerta de enlace de aplicación ahora admite el cifrado de SSL de llevar a cabo.

Fin a fin SSL le permite transmitir de forma segura datos confidenciales en el back-end cifrada fija aprovechar las ventajas de las características de equilibrio de carga de nivel 7 qué puerta de enlace de aplicación proporciona, como afinidad cookies, basado en la dirección URL del enrutamiento, compatibilidad con enrutamiento en función de la capacidad de insertar X o sitios-reenviados-* encabezados.

Cuando se configura con el modo de comunicación de SSL de llevar a cabo, puerta de enlace de aplicación termina las sesiones SSL de usuario en la puerta de enlace y descifra el tráfico de usuario. A continuación, aplica las reglas configuradas para seleccionar una instancia de grupo de back-end adecuados para enrutar el tráfico a. Puerta de enlace de aplicación, a continuación, inicia una nueva conexión SSL en el servidor back-end y vuelve a cifra datos mediante el certificado de clave pública del servidor back-end antes de transmitir solicitud del back-end. Icono Finalizar para finalizar SSL está habilitada estableciendo la configuración de protocolo en BackendHTTPSetting a Https, que se aplica a un grupo de back-end. Cada servidor back-end del grupo de back-end con SSL de llevar a cabo habilitado debe estar configurado con un certificado para permitir la comunicación segura.

![escenario de llevar a cabo ssl][1]

En este ejemplo, las solicitudes mediante TLS1.2 se enrutan a servidores de back-end en grupo 1 con SSL de llevar a cabo.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Fin a fin de SSL y lista blanca de certificados

Puerta de enlace de aplicación sólo se comunica con instancias de conocidos back-end que tienen en la lista blanca su certificado con la puerta de enlace de aplicación. Para habilitar la lista blanca de certificados, debe cargar la clave pública de certificados de servidor back-end a la puerta de enlace a la aplicación (no en el certificado raíz). A continuación, se permiten solo conexiones en back-ends de conocidos y en la lista blanca. Los resultados de back-ends restantes en un error de puerta de enlace. Certificados autofirmados son fines de prueba que y no se recomienda para cargas de trabajo de producción. Estos certificados también deben estar en la lista blanca con la puerta de enlace de aplicación como se describe en los pasos anteriores antes de poder usar.

## <a name="application-gateway-ssl-policy"></a>Directiva de SSL de puerta de enlace de aplicación

Puerta de enlace de aplicación admite configurable SSL negociación directivas de usuario, que permiten a los clientes más control sobre las conexiones SSL en la puerta de enlace de aplicación.

1. SSL 2.0 y 3.0 deshabilitadas de forma predeterminada para todas las puertas de enlace de aplicación. No son configurables en absoluto.
2. Opción para deshabilitar cualquiera de los siguientes 3 protocolos - TLSv1 le proporciona de definición de la directiva SSL\_0, TLSv1\_1, TLSv1\_2.
3. Si no se ha definido ninguna directiva SSL tres (TLSv1\_0, TLSv1\_1, TLSv1_2) están habilitadas.

## <a name="next-steps"></a>Pasos siguientes

Después de aprendizaje sobre final a final SSL y la directiva SSL, vaya a [Habilitar SSL de principio a fin de puerta de enlace de aplicación](application-gateway-end-to-end-ssl-powershell.md) crear una puerta de enlace de la aplicación con la capacidad para enviar tráfico back-ends en formato cifrado.

<!--Image references-->

[1]: ./media/application-gateway-backend-ssl/scenario.png