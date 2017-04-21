<properties 
   pageTitle="Solucionar problemas de grupos de seguridad de red - Portal | Microsoft Azure"
   description="Obtenga información sobre cómo solucionar problemas de los grupos de seguridad de red en el modelo de implementación de Azure Administrador de recursos con el Portal de Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="AnithaAdusumilli"
   manager="narayan"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="anithaa" />

# <a name="troubleshoot-network-security-groups-using-the-azure-portal"></a>Solucionar problemas de los grupos de seguridad de red con el Portal de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](virtual-network-nsg-troubleshoot-portal.md)
- [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)

Si configura grupos de seguridad de red (NSGs) en la máquina virtual (VM) y está experimentando problemas de conectividad VM, este artículo proporciona una descripción general de las capacidades de diagnóstico para NSGs para ayudar a solucionar aún más.

NSGs le permiten controlar los tipos de tráfico que fluyen dentro y fuera de sus máquinas virtuales (VM). NSGs se puede aplicar a subredes en una red Virtual de Azure (VNet), interfaces de red (NIC) o ambas. Las reglas efectivo aplicadas a una NIC son una agregación de las reglas que existen en el NSGs aplicados a una NIC y la subred a que está conectado. Reglas en estos NSGs a veces pueden entran en conflicto con otros y afectar a la conectividad de red de la VM.  

Puede ver todas las reglas de seguridad eficaz desde su NSGs, tal como se aplica en NIC de la máquina virtual. Este artículo le muestra cómo solucionar problemas de conectividad VM con estas reglas en el modelo de implementación de administrador de recursos de Azure. Si no está familiarizado con los conceptos de VNet y GSN, lea los artículos de información general [de red Virtual](virtual-networks-overview.md) y [los grupos de seguridad de red](virtual-networks-nsg.md) .

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Uso de reglas de seguridad eficaces para solucionar problemas de flujo de tráfico VM

El siguiente escenario es un ejemplo de un problema de conexión comunes:

Una máquina virtual denominada *VM1* forma parte de una subred denominada *subred1* dentro de un VNet denominado *WestUS VNet1*. Se produce un error en un intento de conectar con la máquina virtual con RDP sobre el puerto TCP 3389. NSGs se aplican a la NIC *VM1 NIC1* y la subred *subred1*. El tráfico al puerto TCP 3389 está permitido en la GSN asociado a la interfaz de red *VM1 NIC1*, pero TCP ping a correctamente de puerto 3389 de VM1.

Mientras este ejemplo usa el puerto TCP 3389, los siguientes pasos pueden usarse para determinar errores de conexión entrante y saliente sobre cualquier puerto.

### <a name="view-effective-security-rules-for-a-virtual-machine"></a>Ver las reglas de seguridad eficaz para una máquina virtual

Complete los pasos siguientes para solucionar problemas de una máquina virtual NSGs:

Puede ver una lista completa de las reglas de seguridad eficaz en una tarjeta de NIC, de la máquina virtual propiamente dicho. También puede agregar, modificar y eliminar reglas NIC y subred GSN desde el módulo de reglas efectiva, si tiene permisos para realizar estas operaciones.

1. Inicie sesión en el portal de Azure en https://portal.azure.com.
2. Haga clic en **más servicios**, haga clic en **máquinas virtuales de Windows** en la lista que aparece.
3. Seleccione una máquina virtual para solucionar problemas de la lista que aparece y se muestra un módulo VM con opciones.
4. Haga clic en **diagnosticar & solucionar los problemas de** y, a continuación, seleccione un problema común. En este ejemplo, **no puedo conectarme a mi VM de Windows** está seleccionada. 

    ![](./media/virtual-network-nsg-troubleshoot-portal/image1.png)

5. Pasos aparecen en el problema, tal como se muestra en la siguiente imagen: 

    ![](./media/virtual-network-nsg-troubleshoot-portal/image2.png)

    Haga clic en *reglas de grupo de seguridad eficaz* en la lista de pasos recomendados.

6. Aparece el módulo de **obtener las reglas de seguridad eficaz** , tal como se muestra en la siguiente imagen:

    ![](./media/virtual-network-nsg-troubleshoot-portal/image3.png)

    Tenga en cuenta las siguientes secciones de la imagen:

    - **Ámbito:** Establecer a *VM1*, la máquina virtual seleccionada en el paso 3.
    - **Interfaz de red:** *VM1 NIC1* está seleccionada. Una máquina virtual puede tener varias interfaces de red (NIC). Cada NIC puede tener reglas de seguridad eficaces único. Para solucionar el problema, tendrá que ver las reglas de seguridad efectivo para cada NIC.
    - **Asociada NSGs:** NSGs se puede aplicar a la NIC y la subred a que la NIC está conectada. En la imagen, un GSN se ha aplicado a la NIC y la subred a que está conectado. Puede hacer clic en los nombres de GSN para modificar directamente las reglas en la NSGs.
    - **Ficha VM1 GSN:** La lista de reglas que se muestran en la imagen es para el GSN aplicado a la NIC. Azure crea varias reglas predeterminadas siempre que se crea una GSN. No se puede quitar las reglas de forma predeterminada, pero se puede reemplazar con reglas de mayor prioridad. Para obtener más información acerca de las reglas de forma predeterminada, lea el artículo [Introducción a GSN](virtual-networks-nsg.md#default-rules) .
    - **Columna de destino:** Algunas de las reglas tienen texto en la columna, mientras que otros usuarios tienen prefijos de direcciones. El texto es el nombre de las etiquetas predeterminadas que aplica la regla de seguridad cuando se ha creado. Las etiquetas son identificadores proporcionados por el sistema que representan varios prefijos. Seleccionar una regla con una etiqueta, como *AllowInternetOutBound*, enumera los prefijos en el módulo de **prefijos de direcciones** .
    - **Descargar:** La lista de reglas puede ser larga. Puede descargar un archivo .csv de las reglas de análisis sin conexión haciendo clic en **Descargar** y guardar el archivo.
    - **AllowRDP** Regla de entrada: esta regla permite conexiones RDP a la máquina virtual.
7. Haga clic en la pestaña **Subred1 GSN** para ver las reglas eficaces desde el GSN aplicado a la subred, tal como se muestra en la siguiente imagen: 

    ![](./media/virtual-network-nsg-troubleshoot-portal/image4.png)

    Observe la regla de **entrada** de *denyRDP* . Las reglas de entrada aplicadas a la subred se evalúan antes de las reglas aplicadas a la interfaz de red. Puesto que se aplica la regla de denegar a la subred, falla la solicitud para conectarse a TCP 3389, porque nunca se evalúa la regla de permitir en la NIC. 

    La regla de *denyRDP* es la razón por qué falla la conexión RDP. Quitarlo debería resolver el problema.

    >[AZURE.NOTE]Si la máquina virtual asociada a la NIC no está en un estado de ejecución o NSGs no se han aplicado a la subred o NIC, no se muestra ninguna regla.

8. Para editar las reglas de GSN, haga clic en *Subred1 GSN* en la sección **NSGs asociada** .
   Se abrirá el módulo **Subred1 GSN** . Puede editar directamente las reglas haciendo clic en **reglas de seguridad entrante**.

    ![](./media/virtual-network-nsg-troubleshoot-portal/image7.png)

9. Después de quitar la regla de entrada de *denyRDP* en la **Subred1 GSN** y agregar una regla de *allowRDP* , la lista de reglas eficaz es similar a la siguiente imagen:

    ![](./media/virtual-network-nsg-troubleshoot-portal/image8.png)

    Confirme que el puerto TCP 3389 está abierto al abrir una conexión RDP con la máquina virtual o con la herramienta de PsPing. Puede obtener más información sobre PsPing lea la [página de descarga de PsPing](https://technet.microsoft.com/sysinternals/psping.aspx).

### <a name="view-effective-security-rules-for-a-network-interface"></a>Ver las reglas de seguridad eficaz para una interfaz de red

Si el flujo del tráfico VM se ve afectado para una NIC específica, puede ver una lista completa de las reglas eficaces para la NIC desde el contexto de interfaces de red complete los pasos siguientes:

1. Inicie sesión en el portal de Azure en https://portal.azure.com.
2. Haga clic en **más servicios**, haga clic en **interfaces de red** en la lista que aparece.
3. Seleccione una interfaz de red. En la siguiente imagen, se selecciona una NIC denominada *VM1 NIC1* .

    ![](./media/virtual-network-nsg-troubleshoot-portal/image5.png)

    Observe que el **ámbito** se establece en la interfaz de red seleccionada. Para obtener más información acerca de la información adicional que se muestra, lea el paso 6 de la sección de **Solución de problemas NSGs para una máquina virtual** de este artículo.

    >[AZURE.NOTE] Si se quita un GSN desde una interfaz de red, la subred GSN todavía es eficaz en la NIC determinado. En este caso, el resultado sólo muestra las reglas de la subred GSN. Reglas solo aparecen si la NIC está conectada a una máquina virtual.

4. Puede editar directamente las reglas de NSGs asociadas a una NIC y una subred. Para obtener información sobre cómo hacerlo, vea el paso 8 de la sección **Ver las reglas de seguridad eficaz para una máquina virtual** de este artículo.

## <a name="view-effective-security-rules-for-a-network-security-group-nsg"></a>Ver las reglas de seguridad eficaz para un grupo de seguridad de la red (NSG)

Cuando se modifica reglas GSN, desea revisar el impacto de las reglas que se agregan en una máquina virtual determinada. Puede ver una lista completa de las reglas de seguridad eficaces para todas las NIC que se aplica un GSN determinado, sin tener que cambiar de contexto desde el módulo GSN determinado. Para solucionar eficaces reglas dentro de un GSN, realice los pasos siguientes:

1. Inicie sesión en el portal de Azure en https://portal.azure.com.
2. Haga clic en **más servicios**, haga clic en **grupos de seguridad de red** en la lista que aparece.
3. Seleccione un GSN. En la siguiente imagen, se ha seleccionado un GSN denominado VM1 GSN.

    ![](./media/virtual-network-nsg-troubleshoot-portal/image6.png)

    Tenga en cuenta las siguientes secciones de la imagen anterior:

    - **Ámbito:** Establecer la GSN seleccionado.
    - **Máquina Virtual:** Cuando se aplica un GSN a una subred, se aplica a todas las interfaces de red conectados a todas las máquinas virtuales conectadas a la subred. Esta lista muestra todas las máquinas virtuales de este GSN se aplica a. Puede seleccionar cualquier máquina virtual de la lista.

    >[AZURE.NOTE] Si se aplica un GSN con solo una subred vacía, no se mostrarán máquinas virtuales. Si un GSN se aplica a una NIC que no está asociada a una máquina virtual, también se mostrarán no esas NIC. 
    - **Interfaz de red:** Una máquina virtual puede tener varias interfaces de red. Puede seleccionar una interfaz de red conectada a la máquina virtual seleccionada.
    - **AssociatedNSGs:** En cualquier momento una NIC puede tener hasta dos NSGs eficaces, uno aplicado a la NIC y el otro a la subred. Aunque el ámbito está seleccionado como VM1-GSN, si la NIC tiene una subred eficaces GSN, el resultado mostrará ambos NSGs.
4. Puede editar directamente las reglas de NSGs asociadas a una NIC o subred. Para obtener información sobre cómo hacerlo, vea el paso 8 de la sección **Ver las reglas de seguridad eficaz para una máquina virtual** de este artículo.

Para obtener más información acerca de la información adicional que se muestra, lea el paso 6 de la sección **Ver las reglas de seguridad eficaz para una máquina virtual** de este artículo.

>[AZURE.NOTE] Aunque una NIC y subred cada sólo pueden tener una que GSN aplicado, un GSN se puede asociar a varias NIC y varias subredes.

## <a name="considerations"></a>Consideraciones

Solución de problemas de conectividad, tenga en cuenta los siguientes puntos:

- Reglas de GSN predeterminadas bloqueará acceso entrante desde internet y permitir únicamente VNet tráfico entrante. Deben agregar explícitamente reglas para permitir el acceso entrante desde Internet, según sea necesario.
- Si no hay ninguna regla de seguridad GSN causando la conectividad de red de la VM no se realice correctamente, el problema puede ser debido a:
    - Software de firewall que se ejecuta en el sistema operativo de la VM
    - Rutas configuradas para equipos virtuales o tráfico local. Tráfico de Internet puede redirigirá a local mediante túnel forzado. Una conexión RDP/SSH desde Internet a su máquina virtual no funcionen con esta configuración, dependiendo de cómo el hardware de red local controla este tráfico. Lea el artículo de [Solución de problemas de rutas](virtual-network-routes-troubleshoot-powershell.md) para aprender a diagnosticar los problemas de ruta que pueden impedir la el flujo del tráfico dentro y fuera de la máquina virtual. 
- Si ha peered VNets, de forma predeterminada, la etiqueta VIRTUAL_NETWORK se expande automáticamente para incluir prefijos para VNets peered. Puede ver estos prefijos en la lista de **ExpandedAddressPrefix** para solucionar problemas relacionados con la conectividad de interconexión VNet. 
- Reglas de seguridad eficaz solo se muestran si hay un GSN asociado a la VM NIC y o subred. 
- Si no hay ningún NSGs asociadas a la NIC o subred y tiene una dirección IP pública asignada a su máquina virtual, todos los puertos estará abiertos para acceso entrante y saliente. Si la máquina virtual tiene una dirección IP pública, aplicar NSGs a la subred o NIC es muy recomendable.