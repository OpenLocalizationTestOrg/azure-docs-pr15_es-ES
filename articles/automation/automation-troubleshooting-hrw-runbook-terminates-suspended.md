<properties
   pageTitle="Híbrido Runbook trabajo: Un trabajo runbook termina con un estado de suspendido | Microsoft Azure"
   description="Causas de los síntomas y soluciones para error de finalización de tarea híbrido Runbook trabajo."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/17/2016"
   ms.author="magoedte" />

# <a name="hybrid-runbook-worker-a-runbook-job-terminates-with-a-status-of-suspended"></a>Trabajador de Runbook híbrido: Un trabajo runbook termina con un estado de suspensión

## <a name="summary"></a>Resumen

Se ha suspendido su runbook poco después de intentar ejecutar tres veces. Hay condiciones que pueden interrumpir runbook de completar correctamente y el mensaje de error relacionados no incluye información adicional que indica el motivo. Este artículo proporciona pasos para solucionar problemas para los problemas relacionados con los errores de ejecución de runbook híbrido Runbook trabajo.

Si el problema de Azure no se trata en este artículo, visite los foros de Azure en [MSDN y el desbordamiento de pila](https://azure.microsoft.com/support/forums/). Puede publicar el problema en estos foros o para [ @AzureSupport en Twitter](https://twitter.com/AzureSupport). Además, puede abrir una solicitud de soporte técnico de Azure seleccionando **obtener soporte técnico** en el sitio de [soporte técnico de Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptom"></a>Síntoma

Se produce un error de ejecución de runbook y el error devuelto es, "la acción del trabajo 'Activar' no se puede ejecutar porque el proceso se ha detenido inesperadamente. La acción del trabajo se intentó 3 veces."


## <a name="cause"></a>Causa

Hay varias causas posibles para el error: 

  1. Trabajador híbrido está detrás de un servidor proxy o firewall
  2. El equipo en el que se está ejecutando el trabajo híbrida tiene menos de los [requisitos](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-requirements) de hardware mínimos 
  3. No se puede autenticar la runbooks con recursos locales


## <a name="cause-1-hybrid-runbook-worker-is-behind-proxy-or-firewall"></a>Causa 1: Híbrido Runbook trabajo está detrás del servidor proxy o firewall

El equipo en el que se está ejecutando el trabajo Runbook híbrido es detrás de un servidor proxy o firewall y acceso a redes externas no se permite o configurado correctamente.

### <a name="solution"></a>Solución

Compruebe el equipo tiene acceso saliente a *. cloudapp.net en los puertos 443, 9354 y 30199 30000. 

## <a name="cause-2-computer-has-less-than-minimum-hardware-requirements"></a>Causa 2: El equipo tiene requisitos de hardware mínimos menor que

Equipos que ejecutan híbrido Runbook trabajador deben cumplir los requisitos de hardware mínimos antes de designar a hospedar esta característica. En caso contrario, según el uso de otros procesos en segundo plano y conflictos causados por runbooks durante la ejecución de recursos, el equipo se convierten en utilizarán más y provocar retrasos de trabajo runbook o tiempos de espera. 

### <a name="solution"></a>Solución 

En primer lugar, confirme el equipo designado para ejecutar la característica híbrida Runbook trabajo cumple los requisitos de hardware mínimos.  Si es así, supervisar el uso de CPU y la memoria para determinar cualquier correlación entre el rendimiento de procesos de trabajo de Runbook híbrido y Windows.  Si no hay memoria o presión de la CPU, esto puede indicar la necesidad de actualizar o agregar procesadores adicionales para aumentar la memoria para la botella de recursos de direcciones y resolver el error. Como alternativa, seleccione un recurso de cálculo diferente que es compatible con los requisitos mínimos y escala cuando las peticiones de carga de trabajo indican que un aumento es necesario.         

## <a name="cause-3-runbooks-cannot-authenticate-with-local-resources"></a>Causa 3: No se puede autenticar Runbooks con recursos locales

### <a name="solution"></a>Solución

Compruebe el registro de eventos de **Microsoft SMA** para un evento correspondiente con descripción *Win32 proceso terminó con el código [4294967295]*.  La causa de este error es que todavía no lo ha configurado la autenticación en sus runbooks o especificado las credenciales ejecutar como para el grupo de trabajo híbrido.  Revise [Runbook permisos](automation-hybrid-runbook-worker.md#runbook-permissions) para confirmar que ha configurado correctamente autenticación para su runbooks.  


 

