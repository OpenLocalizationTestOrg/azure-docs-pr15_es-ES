<properties
   pageTitle="Actualización de la aplicación: temas avanzados | Microsoft Azure"
   description="En este artículo se trata algunos temas avanzados relacionados con la actualización de una aplicación de servicio tela."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>

# <a name="service-fabric-application-upgrade-advanced-topics"></a>Actualización de la aplicación de servicio tela: temas avanzados

## <a name="adding-or-removing-services-during-an-application-upgrade"></a>Agregar o quitar servicios durante la actualización de una aplicación

Si se agrega un nuevo servicio para una aplicación que ya está implementada y publicada como una actualización, el nuevo servicio se agrega a la aplicación implementada.  Para realizar dicha actualización no afecta a cualquiera de los servicios que ya estuvieran parte de la aplicación. Sin embargo, se debe iniciar una instancia del servicio que se agregó para que el nuevo servicio esté activa (mediante el `New-ServiceFabricService` cmdlet).

También se pueden quitar servicios de una aplicación como parte de una actualización. Sin embargo, se deben detener todas las instancias actuales del servicio se va a eliminar antes de continuar con la actualización (con la `Remove-ServiceFabricService` cmdlet). 

## <a name="manual-upgrade-mode"></a>Modo de actualización manual

> [AZURE.NOTE]  El modo manual se debe considerar solo para una actualización de error o suspendida. El modo supervisado es el modo de actualización recomendado para las aplicaciones de servicio tela.

Azure tela de servicio proporciona varios modos de actualización para admitir clústeres de desarrollo y producción. Opciones de implementación elegidos pueden ser diferentes para diferentes entornos.

La actualización de aplicación supervisados sucesiva es la actualización más habitual para usar en producción. Cuando se especifica la directiva de actualización, servicio tela garantiza que la aplicación es correcto antes de la actualización se realiza.

 Administrador de la aplicación puede usar el modo de actualización aplicación graduales manual para tiene control total sobre el progreso de la actualización a través de los distintos dominios de actualización. Este modo es útil cuando se requiere una directiva de evaluación de estado personalizados o complejas, o qué está ocurriendo una actualización no convencional (por ejemplo, la aplicación ya está en la pérdida de datos).

Por último, la actualización automática de aplicación sucesiva es útil para desarrollo o entornos de pruebas para proporcionar un ciclo de iteración rápido durante el desarrollo del servicio.

## <a name="change-to-manual-upgrade-mode"></a>Cambiar a modo de actualización manual
**Manual**: detener la actualización de la aplicación en la Ud. actual y cambiar el modo de actualización manual no supervisado. El administrador debe llamar manualmente **MoveNextApplicationUpgradeDomainAsync** para continuar con la actualización o desencadenar una operación de deshacer iniciando una actualización nueva. Una vez que la actualización se especifica en el modo Manual, permanece en el modo Manual hasta que se inicie una nueva actualización. El comando **GetApplicationUpgradeProgressAsync** devuelve tela\_aplicación\_actualizar\_estado\_rodante\_reenviar\_pendiente.

## <a name="upgrade-with-a-diff-package"></a>Actualizar un paquete de diferencias

Puede actualizar una aplicación de servicio tela aprovisionamiento con un paquete de aplicación completo y autocontenido. También se actualiza una aplicación mediante un paquete de diferencias que contiene los archivos de la aplicación actualizada, el manifiesto de aplicación actualizado y los archivos de manifiestos de servicio.

Un paquete de aplicación completa contiene todos los archivos necesarios para iniciar y ejecutar una aplicación de servicio tela. Un paquete de diferencias contiene solo los archivos que ha cambiado entre la última disposición y la actualización actual, además de archivos de manifiestos el manifiesto completa de la aplicación y el servicio. Cualquier referencia en el manifiesto de aplicación o servicio que no se encuentra en el diseño de compilación se busca en el almacén de imágenes.

Paquetes de aplicación completa son necesarios para la primera instalación de una aplicación para el clúster. Las actualizaciones posteriores pueden ser un paquete de aplicación completa o un paquete de diferencias.

Ocasiones cuando usa un paquete de diferencias puede ser una buena opción:

* Un paquete de diferencias es preferido cuando tiene un paquete de aplicación de gran tamaño que hace referencia a varios archivos de manifiestos de servicio o varios paquetes de código, config paquetes o paquetes de datos.

* Un paquete de diferencias es preferido cuando tiene un sistema de implementación que genera el diseño de compilación directamente desde el proceso de generación de la aplicación. En este caso, incluso si no ha cambiado el código, ensamblados recién creados Obtén una suma de comprobación diferente. Con un paquete de aplicación completa, se requeriría que actualice la versión en todos los paquetes de código. Usa un paquete de diferencias, solo proporciona los archivos que ha cambiado y los archivos de manifiestos donde ha cambiado la versión.

Cuando se actualiza una aplicación mediante Visual Studio, el paquete de diferencias se publica automáticamente. Para crear un paquete de diferencias manualmente, se deben actualizar el manifiesto de aplicación y los manifiestos de servicio, pero solo los paquetes modificados se incluirá en el paquete de aplicación final. 

Por ejemplo, vamos a iniciar con la siguiente aplicación (números de versión proporcionados para facilitar la descripción):

```text
app1        1.0.0
  service1  1.0.0
    code    1.0.0
    config  1.0.0
  service2  1.0.0
    code    1.0.0
    config  1.0.0
```

Ahora, supongamos que desea actualizar solo el paquete de código de service1 utilizando un paquete de diferencias con PowerShell. Ahora, la aplicación actualizada tiene la estructura de carpetas siguiente:

```text
app1        2.0.0      <-- new version
  service1  2.0.0      <-- new version
    code    2.0.0      <-- new version
    config  1.0.0
  service2  1.0.0
    code    1.0.0
    config  1.0.0
```

En este caso, actualice el manifiesto de aplicación 2.0.0 y el manifiesto del servicio de service1 reflejar la actualización del paquete de código. La carpeta para el paquete de aplicación tendría la estructura siguiente:

```text
app1/
  service1/
    code/
```

## <a name="next-steps"></a>Pasos siguientes

[Actualizar la aplicación usando Visual Studio](service-fabric-application-upgrade-tutorial.md) le guiará a través de la actualización de una aplicación mediante Visual Studio.

[Actualizar su Powershell de uso de aplicación](service-fabric-application-upgrade-tutorial-powershell.md) le guiará a través de una actualización de la aplicación con PowerShell.

Controlar cómo se actualiza la aplicación con [Parámetros de actualización](service-fabric-application-upgrade-parameters.md).

Hacer que las actualizaciones de aplicación compatibles por aprender a usar la [Serialización de datos](service-fabric-application-upgrade-data-serialization.md).

Solucionar problemas comunes en las actualizaciones de la aplicación puede hacer referencia a los pasos de [Solución de problemas de actualizaciones de las aplicaciones](service-fabric-application-upgrade-troubleshooting.md).
 
