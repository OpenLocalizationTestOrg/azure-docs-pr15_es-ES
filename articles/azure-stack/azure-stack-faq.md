<properties
    pageTitle="Preguntas más frecuentes sobre la pila de Azure | Microsoft Azure"
    description="Pila de Azure preguntas más frecuentes."
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="helaw"/>

# <a name="frequently-asked-questions-for-azure-stack"></a>Preguntas más frecuentes sobre la pila de Azure

## <a name="deployment"></a>Implementación

### <a name="do-i-need-to-format-my-data-disks-before-starting-or-restarting-an-installation"></a>¿Debo aplicar formato a los discos de datos antes de iniciar o reiniciar una instalación?

Discos deben estar en formato. Si reinstalar el sistema operativo, debe comprobar si está presente el bloque de almacenamiento antiguos y eliminar con los pasos siguientes:

1. Abra el administrador del servidor.
2. Seleccione los grupos de almacenamiento.
3. Ver si se muestra un grupo de almacenamiento.
4. Haga clic en **grupo de almacenamiento** si enumerados y habilitar la lectura y escritura.
5. Haga clic en el **disco duro Virtual** (esquina inferior izquierda) y seleccione Eliminar.
6. Haga clic en **Grupo de almacenamiento** y haga clic en eliminar.
7. Vuelva a iniciar el script de pila de Azure y compruebe que pasa la comprobación de disco.

Opcionalmente, se puede utilizar la siguiente secuencia de comandos:

```PowerShell
$pools = Get-StoragePool -IsPrimordial $False -ErrorVariable Err -ErrorAction SilentlyContinue
if ($pools -ne $null) {
  $pools| Set-StoragePool -IsReadOnly $False -ErrorVariable Err -ErrorAction SilentlyContinue
  $pools = Get-StoragePool -IsPrimordial $False -ErrorVariable Err -ErrorAction SilentlyContinue
  $pools | Get-VirtualDisk | Remove-VirtualDisk -Confirm:$False
  $pools | Remove-StoragePool -Confirm:$False
}
```

### <a name="can-i-use-all-ssd-disks-for-the-storage-pool-in-the-poc-installation"></a>¿Puedo usar todos los discos SSD para el bloque de almacenamiento en la instalación de prueba de concepto?

Esta configuración no es compatible con esta versión.  Para obtener más información, consulte la [Guía de requisitos](azure-stack-deploy.md) para obtener más información.

### <a name="can-i-use-nvme-data-disks-for-the-microsoft-azure-stack-poc"></a>¿Puedo usar discos de datos de NVMe para la prueba de concepto de pila de Microsoft Azure?

Mientras directa de espacios de almacenamiento admite discos de NVMe, pila de Azure solo es compatible con un subconjunto de los tipos de unidad posibles y combinaciones posibles para espacios de almacenamiento directo. 

### <a name="how-can-i-reinstall-azure-stack"></a>¿Cómo puedo volver a instalar la pila de Azure?
Puede seguir los pasos de la [Guía de redistribución](azure-stack-redeploy.md).  

## <a name="tenant"></a>Inquilino

### <a name="can-i-deploy-my-own-images-as-a-tenant"></a>¿Puedo implementar Mis propia imágenes como un inquilino?

Sí, al igual que en Azure, un inquilino puede cargar imágenes en pila de Azure, además de usar las imágenes del administrador del servicio. Para obtener información general, vea [Agregar una imagen de máquina virtual](azure-stack-add-vm-image.md). 

## <a name="testing"></a>Realizar pruebas

### <a name="can-i-use-nested-virtualization-to-test-the-microsoft-azure-stack-poc"></a>¿Usar virtualización anidadas para probar la prueba de concepto de pila de Microsoft Azure?

Virtualización anidada no es compatible ni probada con Azure pila Technical Preview 2.

## <a name="virtual-machines"></a>Máquinas virtuales de Windows

### <a name="does-azure-stack-support-dynamic-disks-for-virtual-machines"></a>¿Pila de Azure admite discos dinámicos para máquinas virtuales?

Pila de Azure no admite discos dinámicos.

## <a name="next-steps"></a>Pasos siguientes

[Solución de problemas](azure-stack-troubleshooting.md)
