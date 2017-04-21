<properties 
   pageTitle="Notas de la versión de lanzamiento de StorSimple 8000 de versión | Microsoft Azure"
   description="Describe las nuevas características, problemas abiertos y alternativas disponibles para julio de 2014 versión de Microsoft Azure StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
 <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="storsimple-8000-series-release-version-release-notes---july-2014"></a>Notas de la versión de StorSimple 8000 Series versión - julio de 2014 

## <a name="overview"></a>Información general

Notas de la versión de seguir identifican los problemas de abrir críticos para la serie de 8000 StorSimple lanzamiento de julio de 2014 disponibilidad general (GA) de Microsoft Azure StorSimple. Esta versión corresponde a la versión 6.3.9600.17215.  

A menos que se indique lo contrario, estas notas se aplican a todos los modelos del dispositivo StorSimple. Las notas se actualizan continuamente; como se detectan problemas importantes que requieren una solución, que se hayan agregado. Antes de implementar la solución Microsoft Azure StorSimple, tenga en cuenta la siguiente información.  

## <a name="known-issues-in-this-release"></a>Problemas conocidos en esta versión
La tabla siguiente proporciona un resumen de los problemas conocidos en esta versión.  
 
| No. | Característica | Problema | Comentarios o solución | Se aplica a dispositivo físico | Se aplica al dispositivo virtual |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Restablecimiento de fábrica | En algunos casos, cuando realiza un restablecimiento de fábrica, el dispositivo StorSimple pueden bloquearse y mostrar este mensaje: **Restablecer a fábrica está en curso (fase 8)**. Esto ocurre si presiona CTRL + C mientras el cmdlet está en curso. | Presione CTRL + C después de iniciar un restablecimiento de fábrica. Si ya están en este estado, póngase en contacto con Microsoft Support para los pasos siguientes. | Sí | No |
| 2 | Quórum de disco | En algunas ocasiones, si se desconecta la mayoría de los discos en la caja EBOD de un dispositivo 8600 de ningún quórum de disco, a continuación, el bloque de almacenamiento estará sin conexión. Permanecerá sin conexión incluso si los discos se vuelven a conectar. | Debe reiniciar el dispositivo. Si el problema continúa, póngase en contacto con Microsoft Support para los pasos siguientes. | Sí | No |
| 3 | Errores de nube instantánea | En algunas ocasiones, una instantánea de nube puede fallar con el error **se alcanzó el límite máximo copia de seguridad**. Esto ocurre si supera 255 clones en línea en el mismo dispositivo, desde el mismo volumen original que se ha eliminado. | | Sí | Sí |
| 4 | ID de controlador incorrecto | Cuando se realiza un reemplazo de controlador, controlador 0 puede mostrarse como controlador 1. Durante el reemplazo de controlador, cuando se cargue la imagen desde el nodo del mismo nivel, el identificador de controlador puede mostrarse inicialmente como el punto de Id. En algunas ocasiones, este comportamiento también pueden aparecer después de reiniciar el sistema. | No se requiere ninguna acción del usuario. Se ocupará esta situación una vez completada la sustitución de controlador. | Sí | No |
| 5 | Dispositivo supervisión gráficos | En el servicio de administrador de StorSimple, los gráficos de supervisión de dispositivo no funcionan cuando básica o la autenticación NTLM está habilitada en la configuración del servidor proxy para el dispositivo. | Modificar la configuración de proxy web para el dispositivo registrado con el servicio de administrador de StorSimple para que la autenticación se establece en ninguno. Para ello, ejecute el cmdlet Set-HcsWebProxy StorSimple Windows PowerShell. | Sí | Sí |
| 6 | Cuentas de almacenamiento | Usar el servicio de almacenamiento para eliminar la cuenta de almacenamiento es un escenario no compatible. Esto llevará a una situación en la que no se puede recuperar datos de usuario. | | Sí | Sí |
| 7 | Recuperación | No se admite una recuperación dentro de 24 horas de recuperación (DR). | | Sí | No |
| 8 | Migración tras error de dispositivo | No se admite varias migraciones tras error de un contenedor de volumen desde el mismo dispositivo de origen a dispositivos de destino diferente. Migración tras error de un único dispositivo inactivo en varios dispositivos hará que los contenedores de volumen en el primer error en dispositivo de perder la propiedad de los datos. Después de la este tipo de migración, estos contenedores de volumen aparecerán o tienen un comportamiento diferente cuando se ve en el portal de clásico de Azure. | | Sí | No |
| 9 | Instalación | Durante StorSimple adaptador de instalación de SharePoint, debe proporcionar una dirección IP de dispositivo finalizar correctamente la instalación. | | Sí | No |
| 10 | Interfaces de red | Interfaces de red DATA 2 y 3 de datos se han cambiado en el software. | Póngase en contacto con Microsoft Support si necesita configurar estas interfaces. | Sí | No |


 
