<properties
    pageTitle="Implementar pila Azure | Microsoft Azure"
    description="Volver a la pila de Azure."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="erikje"/>

# <a name="redeploy-azure-stack"></a>Implementar la pila de Azure

Para redistribuir la pila de Azure, debe empezar de nuevo desde cero como se describe a continuación.

## <a name="steps-to-redeploy-azure-stack"></a>Pasos para implementar la pila de Azure

1. Reinicie el host en el sistema operativo original (instalado a Vírgenes). Esto no es la configuración predeterminada en el menú Inicio, lo que debe usar KVM o consola local para seleccionar durante el reinicio (durante la instalación, denominado el "arranque de disco duro virtual" OS a "AzureStack TP2", esto le ayudará a identificar el sistema operativo que).

    No es necesario quitar la entrada de inicio existente (la nueva compatibilidad con secuencia "PrepareBootFromVHD.ps1" se encarga de que para usted.)

2. Si no tiene KVM o elegir el sistema operativo de inicio antes de reiniciar:
    
    1. Busque la.\BootMenuNoKVM.ps1 secuencia de comandos. Este archivo está disponible con los otros scripts de soporte técnico proporcionados junto con esta versión de compilación.
    
    2. Ejecutar la secuencia de comandos con privilegios elevados. Seleccione el nombre del sistema operativo de Host Original. Esto iniciará el host en el sistema operativo host original sin necesidad de acceso KVM.
    
    3. Una vez completada la secuencia de comandos se le solicitará que confirme el reinicio.

    - Si hay otros usuarios conectados, este comando se producirá un error.

    - Simplemente ejecute el siguiente comando: reiniciar el equipo-forzar 
 
3. Elimine el archivo CloudBuilder.vhdx que se usó como parte de la implementación anterior.

    No es necesario eliminar la agrupación de almacenamiento de la implementación de TP2 anterior. El script de implementación detecta y limpia el existente y luego crea un nuevo.

5. Volver a implementar copien una nueva copia de la CloudBuilder.vhdx, inicio para él, etcetera.

## <a name="next-steps"></a>Pasos siguientes

[Conectarse a la pila de Azure](azure-stack-connect-azure-stack.md)
