<properties
   pageTitle="Cómo conservar una dirección IP virtual constante de un servicio de nube | Microsoft Azure"
   description="Obtenga información sobre cómo asegurarse de que no cambia la dirección IP virtual (VIP) de su servicio de nube de Azure."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="how-to-retain-a-constant-virtual-ip-address-for-a-cloud-service"></a>Cómo conservar una dirección IP virtual constante de un servicio de nube

Cuando actualiza un servicio de nube que se hospeda en Azure, debe asegurarse de que no cambie la dirección IP virtual (VIP) del servicio. Muchos de los servicios de administración de dominio use el sistema de nombres de dominio (DNS) para registrar nombres de dominio. DNS sólo funciona si la dirección VIP es el mismo. Puede usar el **Asistente para publicar** en herramientas de Azure para asegurarse de que la dirección VIP de su servicio de nube no cambia cuando se actualiza. Para obtener más información sobre cómo usar la administración de dominios DNS para servicios de nube, consulte [configurar un nombre de dominio personalizado para un servicio de nube de Azure](./cloud-services/cloud-services-custom-domain-name.md).

## <a name="publishing-a-cloud-service-without-changing-its-vip"></a>Publicar un servicio de nube sin cambiar su VIP

Se asigna a la dirección VIP de un servicio de nube al primero implementarlo en Azure en un entorno determinado, como el entorno de producción. La dirección VIP no cambia a menos que se elimina la implementación explícitamente o implícitamente se elimina por el proceso de actualización de implementación. Para conservar a la dirección VIP, no debe eliminar la implementación, y también debe asegurarse de que Visual Studio no elimina automáticamente la implementación. Puede controlar el comportamiento especificando las opciones de implementación en el **Asistente para publicación**, que es compatible con varias opciones de implementación. Puede especificar una implementación de un nuevo o una implementación de actualización, que puede ser incremental o simultánea, y conservan de ambos tipos de implementaciones de actualización de la dirección VIP. Para obtener definiciones de estos tipos de implementación, vea [Asistente para publicar aplicaciones de Azure](vs-azure-tools-publish-azure-application-wizard.md).  Además, puede controlar si se ha eliminado la implementación de un servicio de nube anterior si se produce un error. La dirección VIP inesperadamente podría cambiar si no establece esta opción correctamente.

### <a name="to-update-a-cloud-service-without-changing-its-vip"></a>Para actualizar un servicio de nube sin cambiar su VIP

1. Después de implementar el servicio de nube al menos una vez, abra el menú contextual para el nodo para el proyecto de Azure y, a continuación, elija **Publicar**. Aparece el Asistente para **Publicar aplicaciones de Azure** .

1. En la lista de suscripciones, elija el elemento al que desea distribuir y, a continuación, elija el botón **siguiente** . Aparece la página **configuración** del asistente.

1. En la pestaña **Configuración común** , compruebe que el nombre del servicio de nube en el que está implementando, el **entorno**, la **Configuración de compilación**y la **Configuración del servicio** son los correctos.

1. En la pestaña **Configuración avanzada** , compruebe que la cuenta de almacenamiento y la etiqueta de implementación están correctas, que está desactivada la casilla de verificación **Eliminar implementación en caso de error** y que está seleccionada la casilla de verificación de la actualización de **implementación** . Al seleccionar la casilla de verificación de la actualización de **implementación** , asegúrese de que no se eliminará su implementación y su VIP no se perderán cuando vuelva a publicar la aplicación. Desactivando la **implementación en la casilla de verificación Error de eliminar**, asegúrese que la dirección VIP no se pierden si se produce un error durante la implementación.

1. Para especificar cómo desea que los roles que se actualicen, elija el vínculo **configuración** junto al cuadro **de implementación de actualización** y, a continuación, elija la opción de actualización simultáneas o de actualización incremental en el cuadro de diálogo de configuración **de implementación de actualización** . Si elige actualización incremental, se actualiza cada instancia uno tras otro, para que la aplicación esté siempre disponible. Si elige actualización simultáneas, se actualizarán todas las instancias al mismo tiempo. Actualización simultánea es más rápida, pero el servicio no esté disponible durante el proceso de actualización.

1. Cuando esté satisfecho con la configuración, elija el botón **siguiente** .

1. En la página de **Resumen** del asistente, compruebe la configuración y, a continuación, elija el botón **Publicar** .

  >[AZURE.WARNING] Si se produce un error en la implementación, debe por qué se produjo un error de direcciones y volver a implementar rápidamente, para evitar que el servicio de nube en un estado dañado.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre la publicación Azure desde Visual Studio, vea [Asistente para aplicaciones de Azure publicar](vs-azure-tools-publish-azure-application-wizard.md).
