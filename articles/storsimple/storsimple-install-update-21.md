<properties
   pageTitle="Instalar 2.2 de actualización en el dispositivo StorSimple | Microsoft Azure"
   description="Se explica cómo instalar StorSimple 8000 Series actualización 2.2 en su dispositivo de la serie 8000 StorSimple."
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
   ms.date="08/02/2016"
   ms.author="alkohli" />

# <a name="install-update-22-on-your-storsimple-device"></a>Instalar 2.2 de actualización en el dispositivo StorSimple

## <a name="overview"></a>Información general

En este tutorial se explica cómo instalar 2.2 de actualización en un dispositivo StorSimple ejecutando una versión anterior de software a través del portal clásico Azure y utilizando el método de revisión. Cuando se configura una puerta de enlace en una interfaz de red que no sean datos 0 del dispositivo StorSimple y está intentando actualizar desde una versión del software 1 anteriores a la actualización, se utiliza el método de revisión.

Actualización 2.2 incluye software del dispositivo, WMI y actualizaciones de iSCSI. Si actualiza desde la versión 2.1, solo la actualización de software de dispositivo tendrá que se aplique. Si actualiza desde una versión de 2 anteriores a la actualización, también deberán aplicar controlador LSI, Spaceport, Storport y actualizaciones de firmware de disco. El software del dispositivo, WMI, iSCSI, controlador LSI, Spaceport y Storport correcciones actualizaciones sin interrupciones y se pueden aplicar a través del portal clásico Azure. Las actualizaciones de firmware de disco interrupciones actualizaciones y solo se pueden aplicar a través de la interfaz de Windows PowerShell del dispositivo. 

> [AZURE.IMPORTANT]

> - Un conjunto de comprobaciones previas manuales o automáticas termine antes de la instalación para determinar el estado de dispositivo en términos de conectividad de red y estado de hardware. Estas comprobaciones previas se realizan solo si aplicar las actualizaciones desde el portal de clásico de Azure.
> - Le recomendamos que instale las actualizaciones de software y el controlador a través del portal clásica Azure. Solo deben ir a la interfaz de Windows PowerShell del dispositivo (para instalar actualizaciones) si falla la comprobación de la puerta de enlace anteriores a la actualización en el portal. Dependiendo de la versión que está actualizando, las actualizaciones pueden tardar 1,5 2,5 horas para instalar. Las actualizaciones de modo de mantenimiento deben estar instaladas mediante la interfaz de Windows PowerShell del dispositivo. Como actualizaciones de modo de mantenimiento son interrupciones, estos se producen un tiempo de inactividad de su dispositivo.
> - Si ejecuta el Administrador de instantáneas StorSimple opcional, asegúrese de que ha actualizado su versión del Administrador de instantáneas a 2.2 actualizar antes de actualizar el dispositivo.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-22-via-the-azure-classic-portal"></a>Instalar actualización 2.2 a través del portal de clásico de Azure

Realice los pasos siguientes para actualizar el dispositivo para [Actualizar 2.2](storsimple-update21-release-notes.md).


> [AZURE.NOTE]
Si desea aplicar la actualización 2 o posterior (incluidas actualización 2.1), Microsoft podrán extraer información de diagnóstico adicional desde el dispositivo. Como resultado, cuando nuestro equipo de operaciones identifica los dispositivos que están teniendo problemas, estamos mejor preparados para recopilar información desde el dispositivo y diagnóstico de problemas. Aceptando actualización 2 o posterior, usted nos permite proporcionar este soporte proactivo.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Compruebe que el dispositivo está ejecutando **StorSimple 8000 Series actualización 2.2 (6.3.9600.17708)**. También se debe modificar la **última actualización de fecha** . 

    Si está actualizando desde una versión anterior de la actualización 2, también verá que están disponibles las actualizaciones de modo de mantenimiento (puede seguir este mensaje para que se muestre hasta 24 horas después de instalar las actualizaciones).

    Actualizaciones de modo de mantenimiento son interrupciones actualizaciones que provocan el tiempo de inactividad del dispositivo y solo se pueden aplicar a través de la interfaz de Windows PowerShell de su dispositivo. En algunos casos cuando se están ejecutando 1.2 de actualización, el firmware disco ya posible actualizado, en cuyo caso no es necesario instalar las actualizaciones de modo de mantenimiento.

    Si está actualizando desde 2 de actualización, el dispositivo ahora debería ser actualizado. Puede omitir los pasos restantes.

13. Descargue las actualizaciones de modo de mantenimiento con los pasos enumerados en [a descargar las revisiones](#to-download-hotfixes) para buscar y descargar KB3121899, las actualizaciones de firmware de disco instalaciones (el resto de actualizaciones ya debe estar instalado ahora).

13. Siga los pasos indicados en [instalar y comprobar las revisiones de modo de mantenimiento](#to-install-and-verify-maintenance-mode-hotfixes) para instalar las actualizaciones de modo de mantenimiento. 

  

## <a name="install-update-22-as-a-hotfix"></a>Instalar la actualización 2.2 como una revisión

Use este procedimiento si falla la comprobación de la puerta de enlace al intentar instalar las actualizaciones a través del portal clásica Azure. Se produce un error en la comprobación disponga de una puerta de enlace asignada a una interfaz de red 0 sin datos y el dispositivo está ejecutando una versión del software antes del 1 de actualización.

Las versiones de software que se pueden actualizar mediante el método de revisión son:

- Actualizar 0,1, 0,2, 0,3
- Actualizar 1, 1.1, 1.2
- Actualización 2, 2.1 

> [AZURE.IMPORTANT]
>
> - Si el dispositivo está ejecutando una versión de lanzamiento (GA), póngase en contacto con el [Soporte técnico de Microsoft](storsimple-contact-microsoft-support.md) para ayudarle con la actualización.

El método de revisión implica los siguientes pasos:

- Descargar las revisiones desde el catálogo de Microsoft Update.
- Instalar y comprobar las revisiones de modo normal.
- Instale y compruebe la revisión del modo de mantenimiento (solo cuando se actualiza desde anteriores a la actualización software 2).

#### <a name="download-updates-for-a-device-running-update-21-software"></a>Descargar actualizaciones de un dispositivo 2.1 de actualización de software

**Si el dispositivo está ejecutando actualizar 2.1**, debe descargar solo la actualización de software de dispositivo KB3179904. Instalar solo el archivo binario precedido 'todo hcsmdssoftwareudpate'. No instale la Cis y la actualización de agente MDS precedido `all-cismdsagentupdatebundle`. Si no lo hace se producirá un error. Se trata de una actualización sin interrupciones, no se le interrumpe IO y el dispositivo no tendrá ningún tiempo de inactividad.


#### <a name="download-updates-for-a-device-running-update-2-software"></a>Descargar actualizaciones de un dispositivo 2 de actualización de software

**Si el dispositivo ejecuta 2**, debe descargar e instalar las siguientes revisiones en el orden indicado:

| Orden  | KB        | Descripción                    | Tipo de actualización  | Tiempo de la instalación |
|--------|-----------|-------------------------|------------- |-------------|
| 1.      | KB3179904 | Actualización de software & #42;  |  Normal <br></br>Sin interrupciones     | ~ 45 minutos |
| 2.      | KB3146621 | paquete iSCSI | Normal <br></br>Sin interrupciones  | ~ 20 minutos |
| 3.      | KB3103616 | Paquete WMI |  Normal <br></br>Sin interrupciones      | ~ min 12 |


 & #42;  Actualización de *Nota, software consta de dos archivos binarios: actualización de software de dispositivo precedido `all-hcsmdssoftwareupdate` y el agente de Cis y Mds precedido `all-cismdsagentupdatebundle`. La actualización de software de dispositivo debe estar instalada antes que el agente de Cis y Mds. También debe reiniciar el controlador de active a través de la `Restart-HcsController` actualizar cmdlet después de aplicar el agente de Cis y MDS (y antes de aplicar el restante actualizaciones).* 

#### <a name="download-updates-for-a-device-running-pre-update-2-software"></a>Descargar actualizaciones de un dispositivo anteriores a la actualización de software 2

**Si el dispositivo está ejecutando versiones 0,2, 0,3, 1.0 y 1.1**, debe descargar y firmware y controlador de instalación LSI actualización además de software, iSCSI y WMI. Esta actualización ya está instalada si se están ejecutando actualizar 1.2 o 2. 
 
| Orden  | KB        | Descripción                    | Tipo de actualización  | Tiempo de la instalación |
|--------|-----------|-------------------------|------------- |-------------|
| 4.      | KB3121900 | Firmware y controlador LSI             |  Normal <br></br>Sin interrupciones      | ~ 20 minutos |


<br></br>
**Si el dispositivo está ejecutando versiones 0,2, 0,3, 1.0, 1.1 y 1.2**, debe descargar e instalar la Spaceport y la corrección Storport. Ya están instalados si se están ejecutando 2 de actualización.

| Orden  | KB        | Descripción                    | Tipo de actualización  | Tiempo de la instalación |
|--------|-----------|-------------------------|------------- |-------------|
| 5.      | KB3090322 | Corregir spaceport </br> Windows Server 2012 R2 |  Normal <br></br>Sin interrupciones      | ~ 20 minutos |
| 6.      | KB3080728 | Corrección de Storport </br> Windows Server 2012 R2 |  Normal <br></br>Sin interrupciones      | ~ 20 minutos |



<br></br>
También debe instalar las actualizaciones de firmware de disco. Puede comprobar si necesita las actualizaciones de firmware de disco ejecutando el `Get-HcsFirmwareVersion` cmdlet. Si se están ejecutando estas versiones de firmware: `XMGG`, `XGEG`, `KZ50`, `F6C2`, `VR08`, y no debe instalar estas actualizaciones.


| Orden  | KB        | Descripción                    | Tipo de actualización  | Tiempo de la instalación |
|--------|-----------|-------------------------|------------- |-------------|
| 7.      | KB3121899 | Firmware del disco              |  Mantenimiento <br></br>Interrupciones      | ~ 30 minutos |
 
<br></br>

> [AZURE.IMPORTANT]
>
> - Este procedimiento debe realizarse solo una vez para aplicar 2.2 de actualización. Puede usar el portal de clásico Azure para aplicar actualizaciones posteriores.
> - Si la actualización de la actualización 2, es el tiempo total de instalación cerca de 1,5 horas.
> - Antes de utilizar este procedimiento para aplicar la actualización, asegúrese de que tanto los controladores de dispositivo están en línea y todos los componentes de hardware están correcto.

Realice los pasos siguientes para descargar e instalar las revisiones.

[AZURE.INCLUDE [storsimple-install-update21-hotfix](../../includes/storsimple-install-update21-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Pasos siguientes

Más información sobre la [versión 2.1 de actualización](storsimple-update21-release-notes.md).
