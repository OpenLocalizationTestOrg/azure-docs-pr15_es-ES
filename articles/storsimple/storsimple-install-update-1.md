<properties
   pageTitle="Instalar la actualización 1.2 en su dispositivo StorSimple | Microsoft Azure"
   description="Se explica cómo instalar StorSimple 8000 Series actualización 1.2 en su dispositivo de la serie 8000 StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/22/2016"
   ms.author="alkohli" />

# <a name="install-update-12-on-your-storsimple-device"></a>Instalar la actualización 1.2 en el dispositivo StorSimple

## <a name="overview"></a>Información general

En este tutorial se explica cómo instalar actualización 1.2 en un dispositivo de StorSimple que se está ejecutando una versión del software antes del 1 de actualización. El tutorial también trata sobre los pasos adicionales necesarios para la actualización cuando se configura una puerta de enlace en una interfaz de red que no sean datos 0 del dispositivo StorSimple.

Actualización 1.2 incluye las actualizaciones de software de dispositivo, LSI controlador actualizaciones y actualizaciones de firmware de disco. El software y las actualizaciones del controlador LSI actualizaciones sin interrupciones y se pueden aplicar a través del portal clásico Azure. Las actualizaciones de firmware de disco interrupciones actualizaciones y solo se pueden aplicar a través de la interfaz de Windows PowerShell del dispositivo.

Dependiendo de qué versión el dispositivo se está ejecutando, puede determinar si se aplicará la actualización 1.2. Puede comprobar la versión del software del dispositivo, vaya a la sección **vistazo rápido** de su dispositivo de **paneles**.

</br>

| Si ejecuta la versión del software...   | ¿Qué ocurre en el portal?                              |
|---------------------------------|--------------------------------------------------------------|
| Versión - GA                    | Si está ejecutando la versión de lanzamiento (GA), no se aplican esta actualización. Póngase [en contacto con soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para actualizar el dispositivo.|
| Actualización de 0,1                      | Portal aplica la actualización 1.2.                                |
| Actualizar 0,2                      | Portal aplica la actualización 1.2.                                |
| Actualizar 0,3                      | Portal aplica la actualización 1.2.                                |
| Actualización 1                        | Esta actualización no estará disponible.                           |
| Actualizar 1.1                      | Esta actualización no estará disponible.                           |

</br>

> [AZURE.IMPORTANT]

> -  Es posible que no vea actualización 1.2 inmediatamente porque haremos fases de las actualizaciones. Buscar actualizaciones en unos días nuevamente como esta actualización estará disponible próximamente.
> - Esta actualización incluye un conjunto de comprobaciones previas manuales o automáticas para determinar el estado de dispositivo en términos de conectividad de red y estado de hardware. Estas comprobaciones previas se realizan solo si aplicar las actualizaciones desde el portal de clásico de Azure.
> - Le recomendamos que instale las actualizaciones de software y el controlador a través del portal clásica Azure. Solo deben ir a la interfaz de Windows PowerShell del dispositivo (para instalar actualizaciones) si falla la comprobación de la puerta de enlace anteriores a la actualización en el portal. Las actualizaciones pueden tardar 10-5 horas para instalar (incluidas las actualizaciones de Windows). Las actualizaciones de modo de mantenimiento deben estar instaladas mediante la interfaz de Windows PowerShell del dispositivo. Como actualizaciones de modo de mantenimiento son interrupciones, estos se producen un tiempo de inactividad de su dispositivo.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-12-via-the-azure-classic-portal"></a>Instalar la actualización 1.2 a través del portal de clásico de Azure

Realice los pasos siguientes para actualizar el dispositivo para [actualizar 1.2](storsimple-update1-release-notes.md). Use este procedimiento únicamente si dispone de una puerta de enlace configurado en la interfaz de red 0 de datos en el dispositivo.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Compruebe que el dispositivo está ejecutando **StorSimple 8000 Series actualización 1.2 (6.3.9600.17584)**. También se debe modificar la **última actualización de fecha** . También verá que están disponibles las actualizaciones de modo de mantenimiento (puede seguir este mensaje para que se muestre hasta 24 horas después de instalar las actualizaciones).

    Actualizaciones de modo de mantenimiento son interrupciones actualizaciones que provocan el tiempo de inactividad del dispositivo y solo se pueden aplicar a través de la interfaz de Windows PowerShell de su dispositivo.

    ![Página de mantenimiento] (./media/storsimple-install-update-1/InstallUpdate12_10M.png "Página de mantenimiento")

13. Descargue las actualizaciones de modo de mantenimiento con los pasos enumerados en [a descargar las revisiones]( #to-download-hotfixes) para buscar y descargar KB3063416, las actualizaciones de firmware de disco instalaciones (el resto de actualizaciones ya debe estar instalado ahora).

13. Siga los pasos indicados en [instalar y comprobar las revisiones de modo de mantenimiento](#to-install-and-verify-maintenance-mode-hotfixes) para instalar las actualizaciones de modo de mantenimiento.

14. En el portal de clásico Azure, vaya a la página de **Mantenimiento** y, en la parte inferior de la página, haga clic en **Examinar actualizaciones** para comprobar si las actualizaciones de Windows y, a continuación, haga clic en **Instalar actualizaciones**. Termine después de todas las actualizaciones se instalan correctamente.



## <a name="install-update-12-on-a-device-that-has-a-gateway-configured-for-a-non-data-0-network-interface"></a>Instalar la actualización 1.2 en un dispositivo que tenga una puerta de enlace configurada para una interfaz de red 0 sin datos

Debe usar este procedimiento solo si no la comprobación de la puerta de enlace al intentar instalar las actualizaciones a través del portal clásica Azure. Se produce un error en la comprobación disponga de una puerta de enlace asignada a una interfaz de red 0 sin datos y el dispositivo está ejecutando una versión del software antes del 1 de actualización. Si el dispositivo no tiene una puerta de enlace en una interfaz de red 0 sin datos, puede actualizar el dispositivo directamente desde el portal de clásico de Azure. Vea [instalar actualizar 1.2 a través del portal clásico Azure](#install-update-1.2-via-the-azure-classic-portal).

Las versiones de software que pueden actualizarse con este método son actualización 0,1, 0,2 y actualización 0,3.


> [AZURE.IMPORTANT]
>
> - Si el dispositivo está ejecutando una versión de lanzamiento (GA), póngase en contacto con el [Soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para ayudarle con la actualización.
> - Este procedimiento debe realizarse solo una vez para aplicar la actualización 1.2. Puede usar el portal de clásico Azure para aplicar actualizaciones posteriores.

Si el dispositivo está ejecutando 1 anteriores a la actualización de software y tiene una puerta de enlace para una interfaz de red que no sean de 0, puede aplicar actualización 1.2 de las dos maneras siguientes:

- **Opción 1**: Descargar la actualización y aplicar mediante la `Start-HcsHotfix` cmdlet de la interfaz de Windows PowerShell del dispositivo. Este es el método recomendado. **No use este método para aplicar la actualización 1.2 si el dispositivo está ejecutando Update 1.0 o 1.1 de actualización.**

- **Opción 2**: quitar la configuración de puerta de enlace e instalar la actualización directamente desde el portal de clásico de Azure.


En las siguientes secciones se proporcionan instrucciones detalladas para cada uno de estos.

## <a name="option-1-use-windows-powershell-for-storsimple-to-apply-update-12-as-a-hotfix"></a>Opción 1: Usar Windows PowerShell para StorSimple aplicar la actualización 1.2 como una revisión

Debe usar este procedimiento solo si está ejecutando actualización 0,1, 0,2, 0,3 y si la comprobación de la puerta de enlace ha fallado al intentar instalar actualizaciones desde el portal de clásico de Azure. Si está ejecutando software de versión (GA), [Soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para actualizar el dispositivo.

Para instalar la actualización 1.2 como una revisión, debe descargar e instalar las siguientes revisiones:

| Orden  | KB        | Descripción             | Tipo de actualización  |
|--------|-----------|-------------------------|------------- |
| 1      | KB3063418 | Actualización de software         |  Normal     |
| 2      | KB3043005 | Actualización del controlador LSI SA |  Normal     |
| 3      | KB3063416 | Firmware del disco           | Mantenimiento  |

Antes de utilizar este procedimiento para aplicar la actualización, asegúrese de:

- Ambos controladores del dispositivo están en línea.

Siga estos pasos para aplicar la actualización 1.2. **Las actualizaciones podrían tardar alrededor de 2 horas para completar (unos 30 minutos para el software, 30 minutos para controlador, 45 minutos para firmware del disco).**

[AZURE.INCLUDE [storsimple-install-update-option1](../../includes/storsimple-install-update-option1.md)]


## <a name="option-2-use-the-azure-classic-portal-to-apply-update-12-after-removing-the-gateway-configuration"></a>Opción 2: Usar el portal de clásico Azure para aplicar actualización 1.2 después de quitar la configuración de puerta de enlace

Este procedimiento se aplica únicamente a los dispositivos de StorSimple que se están ejecutando una versión del software antes del 1 de actualización y establecer una puerta de enlace en una interfaz de red que no sean datos 0. Debe desactivar la configuración de puerta de enlace antes de aplicar la actualización.

La actualización puede tardar unas horas en completarse. Quitar la configuración de puerta de enlace en las interfaces iSCSI podría provocar si sus hosts están en subredes diferentes, el tiempo de inactividad. Le recomendamos que configure datos 0 para el tráfico iSCSI para reducir el tiempo de inactividad.

Realice los pasos siguientes para deshabilitar la interfaz de red con la puerta de enlace y, a continuación, aplique la actualización.

[AZURE.INCLUDE [storsimple-install-update-option2](../../includes/storsimple-install-update-option2.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]


## <a name="next-steps"></a>Pasos siguientes

Más información sobre la [versión 1.2 de actualización](storsimple-update1-release-notes.md).
