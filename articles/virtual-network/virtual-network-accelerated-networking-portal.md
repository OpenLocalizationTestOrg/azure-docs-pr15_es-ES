<properties 
   pageTitle="Acelerado de la red para una máquina virtual - Portal | Microsoft Azure"
   description="Obtenga información sobre cómo configurar redes acelera para una máquina virtual Azure con el Portal de Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="jdial" />

# <a name="accelerated-networking-for-a-virtual-machine"></a>Redes acelerada para una máquina virtual

> [AZURE.SELECTOR]
- [Portal de Azure](virtual-network-accelerated-networking-portal.md)
- [PowerShell](virtual-network-accelerated-networking-powershell.md)

Redes acelerada permite la única raíz i/OS virtualización (SR IOV) a una máquina virtual (VM), mejorando considerablemente el rendimiento de red. Esta ruta de acceso de alto rendimiento que omita el host de la datapath reducción de latencia, vibración y el uso de CPU para su uso con las cargas de trabajo de red más exigentes en tipos VM compatibles. En este artículo se explica cómo usar el Portal de Azure para configurar redes acelerado en el modelo de implementación de administrador de recursos de Azure. También puede crear una máquina virtual con acelerado de red con PowerShell de Azure. Para obtener información sobre cómo hacerlo, haga clic en el cuadro de PowerShell en la parte superior de este artículo.

La siguiente imagen muestra la comunicación entre dos máquinas virtuales (VM) con y sin acelerado a redes:

![Comparación](./media/virtual-network-accelerated-networking-portal/image1.png)

Sin acelerado redes, todo el tráfico de red dentro y fuera de la máquina virtual debe recorrer el host y el modificador virtual. El modificador virtual proporciona todas las aplicación de directivas, por ejemplo, los grupos de seguridad de red, las listas de control de acceso, aislamiento y otros servicios de red virtualizado para el tráfico de red. Para obtener más información, lea el artículo [virtualización de red Hyper-V y Switch Virtual](https://technet.microsoft.com/library/jj945275.aspx) .

Con acelerado de red, el tráfico de red llega a la tarjeta de red (NIC) y, a continuación, se reenviará a la máquina virtual. Todas las directivas de red que se aplica el modificador virtual sin acelerado redes se descarga y se aplica en hardware. Aplicar la directiva de hardware permite la NIC reenvíe el tráfico de red directamente a la máquina virtual, omitiendo el host y el modificador virtual, manteniendo todas la directiva que se aplica en el host.

Las ventajas de redes acelerado solo se aplican a la máquina virtual que está activada. Para obtener los mejores resultados, es ideal para habilitar esta característica en al menos dos máquinas virtuales conectado a la misma VNet. Cuando se comunica a través de VNets o conexión local, esta característica tiene un impacto mínimo a latencia general.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

##<a name="benefits"></a>Ventajas

- **Menor latencia / superior paquetes por segundo (pps):** Quitando el modificador virtual la datapath quita el tiempo que dedican a paquetes en el host de procesamiento de directiva y aumenta el número de paquetes que puede procesar dentro de la máquina virtual.
- **Reducido vibración:** Procesamiento de switch virtual depende de la cantidad de directiva que debe aplicarse y la carga de trabajo de la CPU que está realizando el procesamiento. Descarga la aplicación de directivas para el hardware quita los cambios en esa proporcionando paquetes directamente a la máquina virtual, quitando el host de comunicación VM y todas las interrupciones de software y los cambios de contexto.
- **Disminuye la CPU:** Omitir el modificador virtual en el host lleva a menos la CPU para el procesamiento de tráfico de red.

## <a name="limitations"></a>Limitaciones

Al usar esta función, existen las siguientes limitaciones:
 
- **Creación de la interfaz de red:** Redes acelerada sólo se puede habilitar para una nueva interfaz de red.  No se pueden habilitar en una interfaz de red existente.
- **Creación de VM:** Una interfaz de red con las redes acelerada habilitada solo puede estar asociada a una máquina virtual cuando se crea la máquina virtual. La interfaz de red no se puede adjuntar a una máquina virtual existente.
- **Regiones:** Disponible en las regiones oeste Central de nosotros y Oeste Europe Azure solo. Expandir el conjunto de regiones en el futuro.
- **Sistema operativo de compatibles:** Microsoft Windows Server 2012 R2 y Windows Server 2016 Technical Preview 5. Compatibilidad con Linux y Windows Server 2012 se agregará pronto.
- **Tamaño de memoria virtual:** Standard_D15_v2 y Standard_DS15_v2 son que solo admite tamaños de instancia de máquina virtual. Para obtener más información, vea el artículo de [tamaños de máquina virtual de Windows](../virtual-machines/virtual-machines-windows-sizes.md) . El conjunto de tamaños de instancia VM admitidos se expandirá en el futuro.

Cambios en estas limitaciones se anunciará a través de la página de [actualizaciones de red Virtual de Azure](https://azure.microsoft.com/updates/accelerated-networking-in-preview) .

## <a name="create-a-windows-vm-with-accelerated-networking"></a>Crear una máquina virtual de Windows con las redes acelerada

1. Registrarse en la vista previa enviando un correo electrónico a [Acelerado suscripciones a redes](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) con su identificador de suscripción y el uso. No lleve a cabo los pasos restantes hasta después de recibir un correo electrónico para notificarle que ha aceptado en la vista previa.
2. Inicie sesión en el Portal de Azure en http://portal.azure.com.
3. Crear una máquina virtual complete los pasos en el artículo [crear una máquina virtual de Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) seleccionando las opciones siguientes:
    - Seleccione un sistema operativo que se muestran en la sección de las limitaciones de este artículo.
    - Seleccione una ubicación (región) aparece en la sección de las limitaciones de este artículo.
    - Seleccione un tamaño VM enumerado en la sección de las limitaciones de este artículo. Si uno de los tamaños admitidos no aparece, haga clic en **Ver todo** en el módulo de **Elegir un tamaño** para seleccionar un tamaño de una lista expandida.
    - En el módulo de **configuración** , haga clic en *habilitado* para **las redes acelerado**, tal como se muestra en la siguiente imagen:

        ![Configuración](./media/virtual-network-accelerated-networking-portal/image3.png)

    >[AZURE.NOTE] La opción acelerado redes sólo estará visible si ha:
    >
    >- Se han aceptado en la vista previa
    >- Seleccionar tamaños VM mencionados en la sección limitaciones de este artículo, la ubicación y el sistema operativo compatible.

5. Una vez creada la máquina virtual, descargue el [controlador acelerado redes](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84), conectar e iniciar sesión en la máquina virtual y ejecutar el programa de instalación de controlador dentro de la máquina virtual.
6. Haga clic en el botón de Windows y haga clic en **Administrador de dispositivos**. Compruebe que el **Adaptador de Ethernet de función Virtual 3 Mellanox ConnectX** aparece en la opción de **red** cuando se expande, tal como se muestra en la siguiente imagen:

    ![Administrador de dispositivos](./media/virtual-network-accelerated-networking-portal/image2.png)