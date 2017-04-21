<properties
 pageTitle="Enviar trabajos de un paquete de HPC clúster en Azure | Microsoft Azure"
 description="Obtenga información sobre cómo configurar un equipo local para enviar trabajos a un clúster de HPC Pack de Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="10/14/2016"
 ms.author="danlep"/>

# <a name="submit-hpc-jobs-from-an-on-premises-computer-to-an-hpc-pack-cluster-deployed-in-azure"></a>Enviar trabajos HPC desde un equipo local a un clúster de HPC Pack implementado en Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Configurar un equipo de cliente local para enviar trabajos a un clúster de [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) en Azure. Este artículo le muestra cómo configurar un equipo local con las herramientas cliente para enviar el trabajo a través de HTTPS al clúster en Azure. De esta forma, varios usuarios de clúster pueden enviar trabajos a un clúster de HPC Pack basado en la nube, pero sin conexión directamente con el nodo principal VM u obtener acceso a una suscripción de Azure.

![Enviar un trabajo a un clúster de Azure][jobsubmit]

## <a name="prerequisites"></a>Requisitos previos

* **Nodo principal de HPC Pack implementado en una máquina virtual de Azure** - le recomendamos que use herramientas automatizadas como una [plantilla de Azure tutorial](https://azure.microsoft.com/documentation/templates/) o un [script de PowerShell de Azure](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) para implementar el nodo principal y un clúster. Necesita el nombre DNS del nodo principal y las credenciales de un administrador de clúster para completar los pasos de este artículo.

* **Equipo cliente** - necesita un equipo cliente de Windows o Windows Server que se puede ejecutar utilidades de cliente de HPC Pack (consulte [requisitos del sistema](https://technet.microsoft.com/library/dn535781.aspx)). Si desea usar el portal de HPC paquete web o la API de REST para enviar trabajos, puede usar cualquier equipo cliente de su elección.

* **Medio de instalación de paquete de HPC** : instalar las herramientas de cliente de HPC Pack, el paquete de instalación libre de la versión más reciente de HPC Pack (HPC Pack 2012 R2) está disponible desde el [Centro de descarga de Microsoft](http://go.microsoft.com/fwlink/?LinkId=328024). Asegúrese de que descargar la misma versión de paquete HPC que esté instalada en el nodo principal VM.

## <a name="step-1-install-and-configure-the-web-components-on-the-head-node"></a>Paso 1: Instalar y configurar los componentes de web en el nodo principal

Para habilitar una interfaz REST enviar trabajos al clúster a través de HTTPS, asegúrese de que los componentes de web HPC Pack están configurados en el nodo principal de HPC Pack. Si aún no están instaladas, instalar primero los componentes de web, ejecute el archivo de instalación HpcWebComponents.msi. A continuación, configure los componentes ejecutando el script de PowerShell HPC **Conjunto HPCWebComponents.ps1**.

Para obtener información detallada, vea [instalar Microsoft HPC Pack Web Components](http://technet.microsoft.com/library/hh314627.aspx).

>[AZURE.TIP] Algunas plantillas de Azure tutorial para HPC Pack instalación y configuración los componentes web automáticamente. Si usa el [script de implementación de HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) para crear el clúster, puede instalar y configurar los componentes web como parte de la implementación.

**Para instalar los componentes web**

1. Conectarse al nodo principal VM mediante las credenciales de un administrador de clúster.

2. Desde la carpeta de instalación del paquete HPC, ejecute HpcWebComponents.msi en el nodo principal.

3. Siga los pasos del Asistente para instalar los componentes web

**Para configurar los componentes web**

1. En el nodo principal, inicie HPC PowerShell como administrador.

2. Para cambiar el directorio a la ubicación de la secuencia de comandos de configuración, escriba el siguiente comando:

    ```powershell
    cd $env:CCP_HOME\bin
    ```
3. Para configurar la interfaz REST e iniciar el servicio Web de HPC, escriba el siguiente comando:

    ```powershell
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```

4. Cuando se le pida que seleccione un certificado, elija el certificado que corresponde al nombre DNS público del nodo principal. Por ejemplo, si implementa el nodo principal VM utiliza el modelo de implementación clásica, el nombre del certificado es similar a CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net. Si utiliza el modelo de implementación de administrador de recursos, el nombre del certificado es similar a CN =&lt;*HeadNodeDnsName*&gt;. &lt; *región*&gt;. cloudapp.azure.com.

    >[AZURE.NOTE] Seleccione este certificado más adelante al enviar trabajos al nodo principal desde un equipo local. No seleccione o configurar un certificado que corresponda al nombre del equipo del nodo principal en el dominio de Active Directory (por ejemplo, CN =*MyHPCHeadNode.HpcAzure.local*).

5. Para configurar el portal web para el envío de trabajos, escriba el siguiente comando:

    ```powershell
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
6. Una vez completada la secuencia de comandos, detenga y reinicie el servicio del programador de trabajos de HPC escribiendo los comandos siguientes:

    ```powershell
    net stop hpcscheduler
    net start hpcscheduler
    ```

## <a name="step-2-install-the-hpc-pack-client-utilities-on-an-on-premises-computer"></a>Paso 2: Instalar las herramientas de cliente de HPC Pack en un equipo local

Si desea instalar las herramientas de cliente de HPC Pack en su equipo, descargar los archivos de instalación de paquete de HPC (instalación completa) desde el [Centro de descarga de Microsoft](http://go.microsoft.com/fwlink/?LinkId=328024). Al comenzar la instalación, elija la opción de configuración de las **Herramientas de cliente de HPC Pack**.

Para usar las herramientas de cliente de HPC Pack enviar trabajos al nodo principal VM, también debe exportar un certificado desde el nodo principal e instálelo en el equipo cliente. El certificado debe estar en. Formato CER.

**Para exportar el certificado desde el nodo principal**

1. En el nodo principal, agregue el complemento de certificados a una consola de administración de Microsoft para la cuenta de equipo Local. Para que conocer los pasos agregar el complemento, vea [Agregar el complemento certificados a MMC](https://technet.microsoft.com/library/cc754431.aspx).

2. En el árbol de consola, expanda **certificados – equipo Local** > **Personal**y, a continuación, haga clic en **certificados**.

3. Localice el certificado que ha configurado para los componentes web HPC Pack [paso 1: instalar y configurar los componentes de web en el nodo principal](#step-1:-install-and-configure-the-web-components-on-the-head-node) (por ejemplo, CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net).

4. Haga clic en el certificado y a continuación, haga clic en **Todas las tareas** > **Exportar**.

5. En el Asistente para exportación de certificados, haga clic en **siguiente**y asegúrese de que está seleccionada la opción **No, no exportar la clave privada** .

6. Siga los pasos del Asistente para exportar el certificado en DER binario codificado X.509 (. Formato CER).


**Para importar el certificado en el equipo cliente**


1. Copie el certificado que ha exportado desde el nodo principal a una carpeta en el equipo cliente.

2. En el equipo cliente, ejecute certmgr.msc.

3. En el Administrador de certificados, expanda **certificados: usuario actual** > **Entidades emisoras de certificados raíz de confianza**, haga clic en **certificados**y, a continuación, haga clic en **Todas las tareas** > **Importar**.

4. En el Asistente para importación de certificado, haga clic en **siguiente** y siga los pasos para importar el certificado que ha exportado desde el nodo principal para el almacén de entidades emisoras de certificados raíz de confianza.



>[AZURE.TIP] Es posible que vea una advertencia de seguridad, porque no se reconocerá la entidad emisora de certificados en el nodo principal en el equipo cliente. Con fines de pruebas, puede omitir esta advertencia y completar la importación del certificado.

## <a name="step-3-run-test-jobs-on-the-cluster"></a>Paso 3: Ejecutar trabajos de prueba en el clúster

Para comprobar la configuración, intente ejecutar trabajos en el clúster en Azure desde el equipo local. Por ejemplo, puede usar herramientas de gráfico de paquete HPC o los comandos de línea de comandos para enviar trabajos al clúster. También puede usar un portal basado en web para enviar trabajos.


**Para ejecutar comandos de envío de trabajo en el equipo cliente**


1. En un equipo cliente donde están instaladas las herramientas de cliente de HPC Pack, inicie un símbolo del sistema.

2. Escriba un comando de ejemplo. Por ejemplo, para mostrar todos los trabajos en el clúster, escriba un comando similar a uno de estos procedimientos, dependiendo del nombre completo de DNS del nodo principal:

    ```command
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all
    ```
    
    o
    
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```

    >[AZURE.TIP] Use el nombre completo de DNS del nodo principal, no la dirección IP, en la dirección URL del programador. Si especifica la dirección IP, un error se parece "el certificado de servidor necesita que una cadena válida de confianza o se coloca en el almacén raíz de confianza".

3. Cuando se le pida, escriba el nombre de usuario (en el formulario &lt;DomainName&gt;\\&lt;nombre de usuario&gt;) y la contraseña de administrador de clústeres HPC u otro usuario de clúster que ha configurado. Puede elegir almacenar las credenciales localmente de más operaciones de trabajo.

    Aparece una lista de trabajos.


**Usar el Administrador de trabajos de HPC en el equipo cliente**

1. Si previamente no almacenar las credenciales de dominio para un usuario de clúster al enviar un trabajo, puede agregar las credenciales de administrador de credenciales.

    una. En el Panel de Control en el equipo cliente, inicie el Administrador de credenciales.

    b. Haga clic en **Credenciales de Windows** > **Agregar una credencial genérica**.

    c. Especificar la dirección de Internet (por ejemplo, https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler o https://&lt;HeadNodeDnsName&gt;.&lt; región&gt;.cloudapp.azure.com/HpcScheduler) y el nombre de usuario (&lt;DomainName&gt;\\&lt;nombre de usuario&gt;) y la contraseña de administrador de clústeres u otro usuario de clúster que ha configurado.

2. En el equipo cliente, inicie el Administrador de trabajos de HPC.

3. En el cuadro de diálogo **Seleccionar el nodo de cabeza** , escriba la dirección URL para el nodo principal en Azure (por ejemplo, https://&lt;HeadNodeDnsName&gt;. cloudapp.net o https://&lt;HeadNodeDnsName&gt;.&lt; región&gt;. cloudapp.azure.com).

    Administrador de trabajos de HPC se abre y muestra una lista de tareas en el nodo principal.

**Usar el portal de web que se ejecuta en el nodo principal**

1. Iniciar un explorador web en el equipo cliente y escriba una de las siguientes direcciones, según el nombre completo de DNS del nodo principal:

    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal
    ```
    
    o
    
    ```
    https://<HeadNodeDnsName>.<region>.cloudapp.azure.com/HpcPortal
    ```
2. En el cuadro de diálogo de seguridad que aparece, escriba las credenciales del dominio del Administrador de clúster HPC. (También puede agregar otros usuarios de clúster funciones diferentes. Consulte [Administrar usuarios de clúster](https://technet.microsoft.com/library/ff919335.aspx)).

    El portal web se abre en la vista de lista de trabajo.

3. Para enviar un trabajo de ejemplo que devuelve la cadena "Hola a todos" en el clúster, haga clic en **nueva tarea** en el panel de navegación izquierdo.

4. En la página **Nuevo trabajo** , **desde las páginas de la presentación**, haga clic en **Hola a todos**. Aparece la página de envío de trabajo.

5. Haga clic en **Enviar**. Si se le solicita, proporcione las credenciales del dominio del Administrador de clúster HPC. Se envía el trabajo y, a continuación, el identificador de trabajo aparece en la página **Mis trabajos** .

6. Para ver los resultados de la tarea que ha enviado, haga clic en el ID de trabajo y, a continuación, haga clic en **Ver tareas** para ver el resultado del comando (en **salida**).

## <a name="next-steps"></a>Pasos siguientes

* También puede enviar trabajos al clúster Azure con la [API de REST de HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx).

* Si desea enviar trabajos de clúster de un cliente Linux, consulte el ejemplo Python en [HPC Pack 2012 R2 SDK y ejemplos de código](https://www.microsoft.com/download/details.aspx?id=41633).


<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png
