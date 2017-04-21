<properties
    pageTitle="Implementar una a través de Internet equilibrio de carga solución con IPv6 usando una plantilla | Microsoft Azure"
    description="Cómo implementar la compatibilidad con IPv6 equilibrador de carga de Azure y máquinas virtuales de equilibrio de carga."
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    tags="azure-resource-manager"
    keywords="IPv6, equilibrador de carga de azure, pila dual, ip pública, ipv6 nativa, mobile, iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Implementar una solución de equilibrador de carga a través de Internet con IPv6 usando una plantilla

> [AZURE.SELECTOR]
- [PowerShell](./load-balancer-ipv6-internet-ps.md)
- [CLI de Azure](./load-balancer-ipv6-internet-cli.md)
- [Plantilla](./load-balancer-ipv6-internet-template.md)

Un equilibrador de carga de Azure es un equilibrador de carga de nivel 4 (TCP, UDP). El equilibrador de carga proporciona alta disponibilidad al distribuir el tráfico entrante entre instancias del servicio correcto en servicios de nube o máquinas virtuales en un conjunto de equilibrador de carga. Azure equilibrador de carga también puede presentar estos servicios en varios puertos, varias direcciones IP o ambos.

## <a name="example-deployment-scenario"></a>Escenario de implementación de ejemplo

El diagrama siguiente muestra la solución de equilibrio de carga se implementa con la plantilla de ejemplo descrita en este artículo.

![Escenario de equilibrador de carga](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

En este escenario creará los siguientes recursos de Azure:

- una interfaz de red virtual para cada VM con direcciones IPv4 y IPv6 asignadas
- un equilibrador de carga a través de Internet con un IPv4 y la dirección IP pública de IPv6
- dos cargar Equilibrio reglas para asignar al públicas VIP a los extremos privados
- Configurar disponibilidad que contiene las dos VM
- dos máquinas virtuales (VM)

## <a name="deploying-the-template-using-the-azure-portal"></a>Implementación de la plantilla con el portal de Azure

En este artículo hace referencia a una plantilla que se publica en la Galería de [Plantillas de Azure tutorial rápido](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) . Puede descargar la plantilla de la galería o iniciar la implementación de Azure directamente desde la galería. En este artículo se supone que la plantilla que ha descargado en su equipo local.

1. Abra el portal de Azure e inicie sesión con una cuenta que tenga permisos para crear máquinas virtuales y recursos de red dentro de una suscripción de Azure. Además, a menos que está usando los recursos existentes, la cuenta necesita permiso para crear un grupo de recursos y una cuenta de almacenamiento.

2. Haga clic en "+ nuevo" desde el menú después escriba "plantilla" en el cuadro de búsqueda. Seleccione "Implementación de la plantilla" en los resultados de búsqueda.

    ![paso 2 kg-ipv6-portal](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. En la todo módulo, haga clic en "Implementación de la plantilla".

    ![paso 3 kg-ipv6-portal](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Haga clic en "Crear".

    ![libras-ipv6-portal-paso4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Haga clic en "Editar plantilla". Eliminar el contenido existente y copiar y pegar en todo el contenido del archivo de plantilla (que incluye el inicio y finalización {}), a continuación, haga clic en "Guardar".

    > [AZURE.NOTE] Si usa Microsoft Internet Explorer, al pegar aparece un cuadro de diálogo que le pide que para permitir el acceso al Portapapeles de Windows. Haga clic en "Permitir acceso".

    ![libras-ipv6-portal-step5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Haga clic en "Editar parámetros". En el módulo de parámetros, especifique los valores por las instrucciones en la sección de parámetros de plantilla, haga clic en "Guardar" para cerrar el módulo de parámetros. En el módulo de implementación personalizado, seleccione la suscripción, un grupo de recursos existente o cree uno. Si va a crear un grupo de recursos, a continuación, seleccione una ubicación para el grupo de recursos. A continuación, haga clic en **condiciones legales**, haga clic en **comprar** para las condiciones legales. Azure comienza a implementar los recursos. Se necesitan unos minutos para implementar todos los recursos.

    ![libras-ipv6-portal-step6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Para obtener más información acerca de estos parámetros, consulte la sección [variables y parámetros de plantilla](#template-parameters-and-variables) más adelante en este artículo.

7. Para ver los recursos creados por la plantilla, haga clic en Examinar, desplácese por la lista hasta que vea "Grupos de recursos" y luego haga clic en él.

    ![libras-ipv6-portal-step7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. En el módulo de grupos de recursos, haga clic en el nombre del grupo de recursos especificado en el paso 6. Verá una lista de todos los recursos que se implementaron. Si todo ha ido bien, debe indicar "Correcta" en "Última implementación". Si no es así, asegúrese de que la cuenta que está usando tiene permisos para crear los recursos necesarios.

    ![libras-ipv6-portal-step8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [AZURE.NOTE] Si examinar los grupos de recursos inmediatamente después de completar el paso 6, "Última implementación" mostrará el estado de "Implementación" mientras se implementan los recursos.

9. Haga clic en "myIPv6PublicIP" en la lista de recursos. Verá que tiene una dirección IPv6 en dirección IP, y que su nombre DNS es el valor especificado para el parámetro dnsNameforIPv6LbIP en el paso 6. Este recurso es el público IPv6 dirección y nombre de host que se puede acceder a los clientes de Internet.

    ![libras-ipv6-portal-step9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Validar la conectividad

Cuando la plantilla se ha implementado correctamente, puede validar conectividad complete las tareas siguientes:

1. Inicie sesión en el portal de Azure y conectarse a cada una de las máquinas virtuales creadas por la implementación de la plantilla. Si implementa una VM de servidor de Windows, ejecute ipconfig/todo desde un símbolo del sistema. Verá que las máquinas virtuales tienen direcciones IPv4 e IPv6. Si implementa máquinas virtuales de Linux, debe configurar el sistema operativo Linux para recibir direcciones IPv6 dinámicas con las instrucciones para la distribución de Linux.
2. Desde un cliente conectado a Internet de IPv6, iniciar una conexión a la dirección IPv6 pública del equilibrador de carga. Para confirmar que es el equilibrio entre las dos VM equilibrador de carga, puede instalar a un servidor web como Microsoft Internet Information Services (IIS) en cada una de las máquinas virtuales. La página web de forma predeterminada en cada servidor podría contener el texto "Server0" o "servidor 1" para identificarlo. A continuación, abra un explorador de Internet en un cliente conectado a Internet de IPv6 y busque el nombre de host especificado para el parámetro dnsNameforIPv6LbIP del equilibrador de carga para confirmar la conectividad IPv6 de llevar a cabo cada VM. Si solo se ve la página web de un solo servidor, debe borrar la caché de su explorador. Abrir varias sesiones de exploración privadas. Debería ver una respuesta de cada servidor.
3. Desde un cliente conectado a Internet IPv4, iniciar una conexión a la dirección IPv4 pública del equilibrador de carga. Para confirmar que el equilibrador de carga de las dos máquinas virtuales de equilibrio de carga, puede probar con IIS, tal como se describe en el paso 2.
4. En cada VM inicia una conexión saliente a un dispositivo IPv6 o IPv4 conectado a Internet. En ambos casos, la dirección IP de origen ver por el dispositivo de destino es la dirección pública de IPv4 o IPv6 del equilibrador de carga.

>[AZURE.NOTE]
ICMP IPv4 y IPv6 está bloqueado en la red de Azure. Como resultado, las herramientas ICMP como ping siempre un error. Para probar la conectividad, utilice una alternativa TCP como TCPing o el cmdlet de PowerShell prueba-NetConnection. Tenga en cuenta que las direcciones IP que se muestra en el diagrama se muestran ejemplos de valores que es posible que vea. Dado que las direcciones IPv6 se asignan dinámicamente, las direcciones que recibe variarán y pueden variar según la región. También es común para el pública dirección IPv6 en el equilibrador de carga para empezar con un prefijo diferente que las direcciones IPv6 privadas en el grupo de back-end.

## <a name="template-parameters-and-variables"></a>Variables y parámetros de plantilla

Una plantilla de administrador de recursos de Azure contiene varias variables y parámetros que puede personalizar según sus necesidades. Las variables se usan para los valores fijos que no desea que un usuario cambie. Los parámetros se usan para los valores que desea que un usuario proporcionar al implementar la plantilla. Ejemplo de plantilla está configurada para el escenario descrito en este artículo. Se puede personalizar para las necesidades de su entorno.

La plantilla de ejemplo usada en este artículo incluye las variables y los parámetros siguientes:

| Parámetro / Variable | Notas |
|-----------|-------|
| adminUsername | Especifique el nombre de la cuenta de administrador utilizada para iniciar sesión en los equipos virtuales con. |
| adminPassword | Especifique la contraseña de la cuenta de administrador utilizada para iniciar sesión en los equipos virtuales con. |
| dnsNameforIPv4LbIP | Especifique el nombre de host DNS que desea asignar como el nombre público del equilibrador de carga. Este nombre se resuelve direcciones IPv4 públicas del equilibrador de carga. El nombre debe ser minúscula y coincide con el regex: ^ [a-z][a-z0-9-]{1,61}[a-z0-9]$. |
| dnsNameforIPv6LbIP | Especifique el nombre de host DNS que desea asignar como el nombre público del equilibrador de carga. Este nombre se resuelve en dirección IPv6 pública del equilibrador de carga. El nombre debe ser minúscula y coincide con el regex: ^ [a-z][a-z0-9-]{1,61}[a-z0-9]$. Puede ser el mismo nombre que la dirección IPv4. Cuando un cliente envía una consulta DNS para este nombre de Azure devolverá A y AAAA registros cuando se comparte el nombre. |
| vmNamePrefix | Especificar el prefijo de nombre de la máquina virtual. La plantilla anexa un número (0, 1, etc.) en el nombre cuando se crean las máquinas virtuales. |
| nicNamePrefix | Especifique el prefijo de nombre de la interfaz de red. La plantilla anexa un número (0, 1, etc.) en el nombre cuando se crean las interfaces de red. |
| storageAccountName | Escriba el nombre de una cuenta de almacenamiento existente o especifique el nombre de una nueva que se cree la plantilla. |
| availabilitySetName | A continuación, escriba el nombre de la disponibilidad establece para usarse con las máquinas virtuales |
| addressPrefix | El prefijo de dirección que se utiliza para definir el intervalo de direcciones de la red Virtual |
| subnetName | El nombre de la subred en creado para la VNet |
| subnetPrefix | El prefijo de dirección que se utiliza para definir el intervalo de direcciones de la subred |
| vnetName | Especificar el nombre de la VNet usado por las máquinas virtuales. |
| ipv4PrivateIPAddressType | El método de asignación que se usa para la dirección IP privada (estática o dinámica) |
| ipv6PrivateIPAddressType | El método de asignación que se usa para la dirección IP privada (dinámico). IPv6 solo es compatible con la asignación dinámica. |
| numberOfInstances | El número de instancias de equilibrio de carga implementado por la plantilla |
| ipv4PublicIPAddressName | Especifique el nombre DNS que desea usar para comunicarse con la dirección IPv4 pública del equilibrador de carga. |
| ipv4PublicIPAddressType | El método de asignación que se usa para la dirección IP pública (estática o dinámica) |
| Ipv6PublicIPAddressName | Especifique el nombre DNS que desea usar para comunicarse con la dirección IPv6 pública del equilibrador de carga. |
| ipv6PublicIPAddressType | El método de asignación que se usa para la dirección IP pública (dinámico). IPv6 solo es compatible con la asignación dinámica. |
| lbName | Especifique el nombre del equilibrador de carga. Este nombre se muestra en el portal o utiliza al hacer referencia a él con un comando CLI o PowerShell. |

El resto de variables en la plantilla contiene valores derivados cuando Azure crea los recursos asignados. No cambie las variables.
