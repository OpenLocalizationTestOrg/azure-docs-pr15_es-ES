<properties 
   pageTitle="Azure compromiso móvil guía - servicio de solución de problemas" 
   description="Solución de problemas de guías para Azure compromiso móvil" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-service-issues"></a>Guía para solucionar problemas de servicio

Los siguientes son posibles problemas que puede encontrarse con la ejecución de Azure Mobile compromiso.

## <a name="service-outages"></a>Interrupciones del servicio

### <a name="issue"></a>Problema
- Problemas que parecen estar causado por interrupciones del servicio móvil compromiso de Azure.

### <a name="causes"></a>Causas
- Problemas que parecen estar causado por interrupciones del servicio de contratación de Azure Mobile pueden estar causados por varios problemas diferentes:
    - Problemas aislados que originalmente aparecen sistemáticas a todos de contratación Mobile de Azure
    - Problemas conocidos causados por las interrupciones del servidor (no siempre se muestra en el estado del servidor):
    - Programación retrasos, errores de destinatarios, problemas de actualización de distintivo, detener estadísticas recopilar, deja de inserción funcionar, Detener trabajo API, nuevas aplicaciones o los usuarios no se puede crear, errores DNS y los errores de tiempo de espera en la interfaz de usuario, API o aplicaciones en un dispositivo.
    - Nube dependencia interrupciones [estado del servicio de Azure](http://status.azure.com/)
    - Interrupciones de dependencia de servicios (PNS) de notificación de inserción
    - Interrupciones de la tienda de aplicaciones

1) Para comprobar si el problema es sistemático puede probar la misma función desde otro
   
   - Aplicación de contratación Mobile integrados de Azure
   - Dispositivo de prueba
   - Versión del sistema operativo del dispositivo de prueba
   - Campaña
   - Cuenta de usuario administrador
   - Explorador (es Decir, Firefox, Chrome, etcetera)
   - Equipo

2) Para comprobar si el problema sólo afecta a la interfaz de usuario o la API:

   - Probar la misma función de la interfaz de usuario de compromiso de Azure móvil y la API compromiso de Azure y Mobile.

3) Para comprobar si el problema está en su red de telefonía móvil:

   - Probar mientras está conectado a Internet a través de Wi-Fi y mientras está conectado a través de la red de telefonía móvil 3G.
   - Confirme que el firewall no está bloqueando cualquiera de las direcciones IP de Azure Mobile contratación o puertos.

4) Para comprobar si el problema con el dispositivo:

   - Comprobar si el dispositivo es capaz de conectarse a Azure Mobile compromiso con otra aplicación integrada de Azure Mobile compromiso.
   - Para comprobar que puede generar eventos, tareas y bloqueos desde el teléfono que puede verse en la interfaz de usuario de compromiso de Azure Mobile. 
   - Comprobar si puede enviar notificaciones de inserción de la interfaz de usuario de compromiso de Azure Mobile en el dispositivo en función de su identificador de dispositivo. 

5) Para comprobar si el problema con la aplicación:

   - Instalar y probar la aplicación desde un emulador en lugar de hacerlo desde un dispositivo físico:
   
6) Para probar si con actualizaciones de sistema operativo para el usuario final dispositivos, que es necesario actualizar SDK para resolver el problema:

   - Probar la aplicación en varios dispositivos con diferentes versiones del sistema operativo.
   - Confirme que está usando la versión más reciente del SDK.
 
## <a name="connectivity-and-incorrect-information-issues"></a>Conectividad y problemas de información incorrecta

### <a name="issue"></a>Problema
- Problemas de inicio de sesión en la interfaz de usuario de compromiso de Azure Mobile.
- Errores de la conexión con la API compromiso de Azure y Mobile.
- Problemas para cargar las etiquetas de información de aplicación a través de la API del dispositivo.
- Problemas de descarga de registros o datos exportados de Azure Mobile compromiso.
- Se muestra en la interfaz de usuario de Azure Mobile compromiso de información incorrecta.
- Se muestra en los registros de Azure Mobile compromiso de información incorrecta.

### <a name="causes"></a>Causas
* Compruebe que su cuenta de usuario tiene permisos suficientes para realizar la tarea.
* Confirme que el problema no está limitado a un equipo o su red local.
* Confirme que la que el servicio de Azure Mobile compromiso no tiene ningún informado interrupciones.
* Confirme que los archivos de etiqueta de la información de aplicación sigan todas estas reglas:
    - Use únicamente el juego de caracteres UTF8 (no se admite el conjunto de caracteres ANSI).
    - Use una coma "," como carácter separador (puede abrir un servicio para solicitar para cambiar el carácter separador de .csv desde una coma "," a otro carácter como un punto y coma ";").
    - Usar minúsculas para valores booleanos "true" y "false".
    - Utilizar un archivo que es menor que el tamaño de archivo máximo de 35MB.
 
