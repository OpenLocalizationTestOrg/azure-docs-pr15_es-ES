<properties
   pageTitle="Instalar la actualización 2 en el dispositivo StorSimple | Microsoft Azure"
   description="Se explica cómo instalar StorSimple 8000 Series 2 en el dispositivo de la serie 8000 StorSimple."
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
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="install-update-2-on-your-storsimple-device"></a>Instalar la actualización 2 en el dispositivo StorSimple

## <a name="overview"></a>Información general

En este tutorial se explica cómo instalar la actualización 2 en un dispositivo StorSimple ejecutando una versión anterior de software a través del portal clásico Azure. El tutorial también trata sobre los pasos necesarios para la actualización cuando se configura una puerta de enlace en una interfaz de red que no sean datos 0 del dispositivo StorSimple y está intentando actualizar desde una versión del software 1 anteriores a la actualización.

Actualización 2 incluye las actualizaciones de software de dispositivo, actualizaciones del controlador LSI y actualizaciones de firmware de disco. El software de dispositivo y actualizaciones de LSI actualizaciones sin interrupciones y se pueden aplicar a través del portal clásico Azure. Las actualizaciones de firmware de disco interrupciones actualizaciones y solo se pueden aplicar a través de la interfaz de Windows PowerShell del dispositivo.

> [AZURE.IMPORTANT]

> -  Es posible que no vea actualización 2 inmediatamente porque haremos fases de las actualizaciones. Buscar actualizaciones en unos días nuevamente como esta actualización estará disponible próximamente.
> - Un conjunto de comprobaciones previas manuales o automáticas termine antes de la instalación para determinar el estado de dispositivo en términos de conectividad de red y estado de hardware. Estas comprobaciones previas se realizan solo si aplicar las actualizaciones desde el portal de clásico de Azure.
> - Le recomendamos que instale las actualizaciones de software y el controlador a través del portal clásica Azure. Solo deben ir a la interfaz de Windows PowerShell del dispositivo (para instalar actualizaciones) si falla la comprobación de la puerta de enlace anteriores a la actualización en el portal. Las actualizaciones pueden tardar 7 de 4 horas para instalar (incluidas las actualizaciones de Windows). Las actualizaciones de modo de mantenimiento deben estar instaladas mediante la interfaz de Windows PowerShell del dispositivo. Como actualizaciones de modo de mantenimiento son interrupciones, estos se producen un tiempo de inactividad de su dispositivo.
> - Si ejecuta el Administrador de instantáneas StorSimple opcional, asegúrese de que ha actualizado su versión del Administrador de instantáneas a actualización 2 antes de actualizar el dispositivo.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-2-via-the-azure-classic-portal"></a>Instalar la actualización 2 a través del portal de clásico de Azure

Realice los pasos siguientes para actualizar el dispositivo para [Actualizar 2](storsimple-update2-release-notes.md).


> [AZURE.NOTE]
Actualización 2 permite a Microsoft obtener información de diagnóstico adicional desde el dispositivo. Como resultado, cuando nuestro equipo de operaciones identifica los dispositivos que están teniendo problemas, estamos mejor preparados para recopilar información desde el dispositivo y diagnóstico de problemas. Si se acepta la actualización 2, usted nos permite proporcionar este soporte proactivo.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Compruebe que el dispositivo está ejecutando **StorSimple 8000 Series Update 2 (6.3.9600.17673)**. También se debe modificar la **última actualización de fecha** . También verá que están disponibles las actualizaciones de modo de mantenimiento (puede seguir este mensaje para que se muestre hasta 24 horas después de instalar las actualizaciones).

    Actualizaciones de modo de mantenimiento son interrupciones actualizaciones que provocan el tiempo de inactividad del dispositivo y solo se pueden aplicar a través de la interfaz de Windows PowerShell de su dispositivo. En algunos casos cuando se están ejecutando 1.2 de actualización, el firmware disco ya posible actualizado, en cuyo caso no es necesario instalar las actualizaciones de modo de mantenimiento.

13. Descargue las actualizaciones de modo de mantenimiento con los pasos enumerados en [a descargar las revisiones](#to-download-hotfixes) para buscar y descargar KB3121899, las actualizaciones de firmware de disco instalaciones (el resto de actualizaciones ya debe estar instalado ahora).

13. Siga los pasos indicados en [instalar y comprobar las revisiones de modo de mantenimiento](#to-install-and-verify-maintenance-mode-hotfixes) para instalar las actualizaciones de modo de mantenimiento.


## <a name="install-update-2-as-a-hotfix"></a>Instalar la actualización 2 como una revisión

Use este procedimiento si falla la comprobación de la puerta de enlace al intentar instalar las actualizaciones a través del portal clásica Azure. Se produce un error en la comprobación disponga de una puerta de enlace asignada a una interfaz de red 0 sin datos y el dispositivo está ejecutando una versión del software antes del 1 de actualización.

Las versiones de software que se pueden actualizar mediante el método de revisión están actualización 0,1, actualización 0,2 y actualizar 0,3, 1 de actualización, actualización 1.1 y 1.2 de actualización. El método de revisión implica los siguientes pasos:

- Descargar las revisiones desde el catálogo de Microsoft Update.
- Instalar y comprobar las revisiones de modo normal.
- Instale y compruebe la revisión del modo de mantenimiento.

Para instalar la actualización 2 como una revisión, debe descargar e instalar las siguientes revisiones:

| Orden  | KB        | Descripción                    | Tipo de actualización  |
|--------|-----------|-------------------------|------------- |
| 1      | KB3121901 | Actualización de software         |  Normal     |
| 2      | KB3121900 | Controlador LSI              |  Normal     |
| 3      | KB3080728 | Corrección de Storport </br> Windows Server 2012 R2 |  Normal     |
| 4      | KB3090322 | Corregir spaceport </br> Windows Server 2012 R2 |  Normal     |
| 5      | KB3121899 | Firmware del disco           | Mantenimiento  |


> [AZURE.IMPORTANT]
>
> - Si el dispositivo está ejecutando una versión de lanzamiento (GA), póngase en contacto con el [Soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para ayudarle con la actualización.
> - Este procedimiento debe realizarse solo una vez para aplicar la actualización 2. Puede usar el portal de clásico Azure para aplicar actualizaciones posteriores.
> - Instalación de la revisión puede tardar aproximadamente 20 minutos en completarse. Tiempo total de instalación es casi 2 horas.
> - Antes de utilizar este procedimiento para aplicar la actualización, asegúrese de que ambos controladores del dispositivo están en línea y todos los componentes de hardware están correcto.

Realice los pasos siguientes para aplicar esta actualización como una revisión.

[AZURE.INCLUDE [storsimple-install-update2-hotfix](../../includes/storsimple-install-update2-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]



## <a name="next-steps"></a>Pasos siguientes

Más información sobre la [versión 2 de actualización](storsimple-update2-release-notes.md).
