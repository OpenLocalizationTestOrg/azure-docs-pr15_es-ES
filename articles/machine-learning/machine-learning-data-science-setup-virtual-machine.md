<properties
    pageTitle="Configurar una máquina virtual como un servidor de Bloc de notas de IPython | Microsoft Azure"
    description="Establecer el una Azure Máquina Virtual para su uso en un entorno de ciencias de datos con el servidor de IPython para análisis avanzado."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="xibingao;bradsev" />

# <a name="set-up-an-azure-virtual-machine-as-an-ipython-notebook-server-for-advanced-analytics"></a>Configurar una máquina virtual Azure como un servidor de Bloc de notas de IPython para análisis avanzado

Este tema muestra cómo crear y configurar una máquina virtual Azure para análisis avanzado que se puede para utilizar como parte de un entorno de ciencias de datos. La máquina virtual de Windows está configurada con herramientas como como el Bloc de notas de IPython, Explorador de almacenamiento de Azure, AzCopy, así como otras utilidades que son útiles para proyectos de análisis avanzadas de soporte. Explorador de almacenamiento de Azure y AzCopy, por ejemplo, proporcionan formas adecuado para cargar los datos con el almacenamiento de blobs de Windows Azure desde el equipo local o descargar en el equipo local de almacenamiento de blobs de Windows.

## <a name="create-vm"></a>Paso 1: Crear un máquina virtual de Azure de propósito general

Si ya tiene una máquina virtual Azure y solo desea configurar un servidor de Bloc de notas de IPython en ella, puede omitir este paso y continúe con [paso 2: agregar un extremo de los blocs de notas IPython a una máquina virtual existente](#add-endpoint).

Antes de iniciar el proceso de creación de una máquina virtual en Azure, debe determinar el tamaño de la máquina que es necesaria para procesar los datos de su proyecto. Máquinas menores tienen menos memoria y menos núcleos de CPU de equipos más grandes, pero también son menos costosas. Para obtener una lista de tipos de equipos y precios, consulte la página de <a href="http://azure.microsoft.com/pricing/details/virtual-machines/" target="_blank">Precios de máquinas virtuales</a>

1. Inicie sesión en el <a href="https://manage.windowsazure.com" target="_blank">Portal de clásico de Azure</a>y haga clic en **nuevo** en la esquina inferior izquierda. Aparecerá una ventana. Seleccione **calcular** -> **máquina VIRTUAL** -> **De galería**.

    ![Crear área de trabajo][24]

2. Elija una de las imágenes siguientes:

    * Centro de datos de Windows Server 2012 R2
    * Experiencia de aspectos básicos de Windows Server (Windows Server 2012 R2)

    A continuación, haga clic en la flecha hacia la derecha en la parte inferior derecha para ir a la siguiente página de configuración.

    ![Crear área de trabajo][25]

3. Escriba un nombre para la máquina virtual que desea crear, seleccione el tamaño de la máquina (predeterminado: A3) según el tamaño de los datos que se va a procesar el equipo y cómo eficaces que desee del equipo (tamaño de la memoria y el número de núcleos de cálculo), escriba un nombre de usuario y contraseña para el equipo. A continuación, haga clic en la flecha que apunta hacia la derecha para ir a la siguiente página de configuración.

    ![Crear área de trabajo][26]

4. Seleccione la **Región o de AFINIDAD de red de grupo o VIRTUAL** que contenga la **Cuenta de almacenamiento** que va a utilizar para esta máquina virtual y, a continuación, seleccione esa cuenta de almacenamiento. Agregar un punto final en la parte inferior en el campo **EXTREMOS** escribiendo el nombre del extremo ("IPython" a continuación). Puede elegir cualquier cadena como el **nombre** del punto final y cualquier número entero entre 0 y 65536 está **disponible** como el **Puerto público**. El **Puerto A privado** tiene que ser **9999**. Los usuarios deben **evitar** el uso de puertos públicos que ya se ha asignado para los servicios de internet. <a href="http://www.chebucto.ns.ca/~rakerman/port-table.html" target="_blank">Puertos de los servicios de Internet</a> proporciona una lista de puertos que se han asignado y se debe evitar.

    ![Crear área de trabajo][27]

5. Haga clic en la marca de verificación para iniciar la máquina virtual de proceso de aprovisionamiento.

    ![Crear área de trabajo][28]


Puede tardar 25 15 minutos para completar la máquina virtual de proceso de aprovisionamiento. Después de crear la máquina virtual, debe mostrar el estado de este equipo como en **ejecución**.

![Crear área de trabajo][29]

## <a name="add-endpoint"></a>Paso 2: Agregar un extremo de los blocs de notas IPython a una máquina virtual existente

Si ha creado la máquina virtual siguiendo las instrucciones en el paso 1, ya se ha agregado el punto final para el Bloc de notas de IPython y se puede omitir este paso.

Si ya existe la máquina virtual y, a continuación, debe agregar un punto final para el Bloc de notas de IPython que va a instalar en el paso 3 siguiente, el primer registro en el Portal clásico de Azure, seleccione la máquina virtual y agregar el extremo de servidor de Bloc de notas de IPython. La figura siguiente contiene una captura de pantalla del portal después de que el punto final para el Bloc de notas de IPython se ha agregado a una máquina virtual de Windows.

![Crear área de trabajo][17]

## <a name="run-commands"></a>Paso 3: Instalar el Bloc de notas de IPython y otras herramientas de soporte

Después de crear la máquina virtual, utilice el protocolo de escritorio remoto (RDP) para iniciar sesión en la máquina virtual de Windows. Para obtener instrucciones, vea [cómo iniciar sesión en una máquina Virtual que ejecuta Windows Server](../virtual-machines/virtual-machines-windows-classic-connect-logon.md). Abra el **símbolo del sistema** (**no la ventana de comandos de Powershell**) como **Administrador** y ejecute el siguiente comando.

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Cuando finalice la instalación, el servidor de Bloc de notas de IPython se inicia automáticamente en la *C:\\usuarios\\\<nombre de usuario\>\\documentos\\blocs de notas de IPython* directorio.

Cuando se le pida, escriba una contraseña para el Bloc de notas de IPython y la contraseña de administrador del equipo. Esto permite que el Bloc de notas de IPython ejecutar como un servicio en el equipo.

## <a name="access"></a>Paso 4: Blocs de notas de IPython de acceso desde un explorador web
Para obtener acceso al servidor de IPython Bloc de notas, abra un sitio web explorador y entrada *nombre DNS del equipo https://&#60;virtual >: & #60; número de puerto público >* en el cuadro de texto de la dirección URL. En este caso, la *& #60; número de puerto público >* debe ser el número de puerto que especificó al agregar el extremo del Bloc de notas de IPython.

La *& #60; nombre DNS de la máquina virtual >* puede encontrarse en el Portal de Azure clásico. Después de inicio de sesión en el Portal clásica, haga clic en **máquinas virtuales de Windows**, seleccione el equipo que creó y, a continuación, seleccione **panel**, el nombre DNS se mostrarán como sigue:

![Crear área de trabajo][19]

Encontrará una advertencia que indica que _hay un problema con el certificado de seguridad del sitio Web_ (Internet Explorer) o _la conexión es no privada_ (Chrome), como se muestra en las siguientes figuras. Haga clic en **Continuar para este sitio Web (no recomendado)** (Internet Explorer) o **avanzado** y, a continuación * *continuar a & #60;* Nombre DNS*> (no seguras) ** (Chrome) para continuar. A continuación, escriba la contraseña especificada anteriormente para tener acceso a los blocs de notas de IPython.

**Internet Explorer:**
![Crear área de trabajo][20]

**Chrome:**
![Crear área de trabajo][21]

Después de iniciar sesión en el Bloc de notas de IPython, un directorio *DataScienceSamples* mostrar en el explorador. Este directorio contiene muestra IPython los blocs de notas compartidos por Microsoft para ayudar a los usuarios realizar las tareas de ciencias de datos. Estos blocs de notas de ejemplo IPython se ha desprotegido del [**repositorio de Github**](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) a los equipos virtuales durante el servidor de Bloc de notas de IPython configurar el proceso. Microsoft mantiene y actualiza este repositorio con frecuencia. Los usuarios pueden visitar el repositorio de Github para obtener los blocs de notas de IPython de ejemplo actualizados recientemente.
![Crear área de trabajo][18]

## <a name="upload"></a>Paso 5: Cargar una IPython Bloc de notas existente desde un equipo local en el servidor de Bloc de notas de IPython

Los blocs de notas IPython proporcionan una forma sencilla para que los usuarios cargar un bloc de notas IPython en sus equipos locales en el servidor de Bloc de notas de IPython en los equipos virtuales. Después de que los usuarios inician sesión en el Bloc de notas de IPython en un explorador web, haga clic en el **directorio** que se pueden cargar el Bloc de notas de IPython a. A continuación, seleccione un archivo de Bloc de notas de IPython .ipynb a cargar desde el equipo local en el **Explorador de archivos**y arrastre y colóquelo en el directorio IPython Bloc de notas en el explorador web. Haga clic en el botón **cargar** para cargar el archivo .ipynb en el servidor de Bloc de notas de IPython. Otros usuarios, a continuación, pueden empezar a usar en desde un explorador web.

![Crear área de trabajo][22]

![Crear área de trabajo][23]


##<a name="shutdown"></a>Cierre y anular la asignación de máquina virtual cuando no esté en uso

El precio de máquinas virtuales de Windows Azure como **pagar solo por lo que usa**. Para asegurarse de que no se facturación cuando no se utiliza la máquina virtual, tiene que estar en el estado de **detenido (Deallocated)** cuando no esté en uso.

> [AZURE.NOTE] Si cierra la máquina virtual desde dentro de la máquina virtual (mediante opciones de energía de Windows), la máquina virtual se detiene pero permanece asignada. Para asegurarse de que no continúe facturará, detener siempre máquinas virtuales desde el [Portal de Azure clásico](http://manage.windowsazure.com/). También puede dejar la máquina virtual a través de Powershell llamando **ShutdownRoleOperation** con "PostShutdownAction" igual a "StoppedDeallocated".

Para cerrar y desasignar la máquina virtual:

1. Inicie sesión en el [Portal de Azure clásica](http://manage.windowsazure.com/) con su cuenta.  

2. Seleccione **máquinas virtuales** de la barra de navegación izquierda.

3. En la lista de máquinas virtuales de Windows, haga clic en el nombre de la máquina virtual, a continuación, vaya a la página de **panel** .

4. En la parte inferior de la página, haga clic en **Cerrar**.

![Cierre VM][15]

Se cancela la asignación de la máquina virtual pero no se eliminarán. Puede reiniciar el equipo virtual en cualquier momento desde el Portal de clásico de Azure.

## <a name="your-azure-vm-is-ready-to-use-whats-next"></a>Está listo para usar su VM Azure: ¿qué es la siguiente?

La máquina virtual ahora está lista para usar en sus ejercicios de ciencias de datos. La máquina virtual también está preparada para utilizarlo como un servidor de Bloc de notas de IPython para la exploración y el procesamiento de datos y otras tareas junto con el proceso de ciencias de datos de equipo y aprendizaje de máquina de Azure.

Los siguientes pasos en el proceso de ciencias de datos de equipo se asignan en la [Ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) y pueden incluir pasos que mover los datos a HDInsight, procesarán y escuche ahí en preparación para aprender de los datos con el aprendizaje del equipo de Azure.


[15]: ./media/machine-learning-data-science-setup-virtual-machine/vmshutdown.png
[17]: ./media/machine-learning-data-science-setup-virtual-machine/add-endpoints-after-creation.png
[18]: ./media/machine-learning-data-science-setup-virtual-machine/sample-ipnbs.png
[19]: ./media/machine-learning-data-science-setup-virtual-machine/dns-name-and-host-name.png
[20]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning-ie.png
[21]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning.png
[22]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-1.png
[23]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-2.png
[24]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-1.png
[25]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-2.png
[26]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-3.png
[27]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-4.png
[28]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-5.png
[29]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-6.png
