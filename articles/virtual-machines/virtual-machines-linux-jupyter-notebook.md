<properties
    pageTitle="Crear un bloc de notas de Jupyter/IPython | Microsoft Azure"
    description="Aprenda a implementar el Bloc de notas de Jupyter/IPython en una máquina virtual de Linux creada con el modelo de implementación de administrador de recursos en Azure."
    services="virtual-machines-linux"
    documentationCenter="python"
    authors="crwilcox"
    manager="wpickett"
    editor=""
    tags="azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="python"
    ms.topic="article"
    ms.date="11/10/2015"
    ms.author="crwilcox"/>

# <a name="jupyter-notebook-on-azure"></a>Bloc de notas de Jupyter de Azure

El [proyecto Jupyter](http://jupyter.org), anteriormente en el [proyecto IPython](http://ipython.org), proporciona un conjunto de herramientas para informática científica con potentes depósitos interactivos que combinan la ejecución de código con la creación de un documento de cálculo activa. Estos archivos de Bloc de notas pueden contener texto arbitrario, fórmulas matemáticas, códigos de entrada, resultados, gráficos, vídeos y cualquier otro tipo de archivos multimedia que sea capaz de mostrar un explorador web moderna. Si está completamente nuevo en Python y desea obtener en un entorno interactivo divertido o hacer algunos sistemas de paralelo técnicos graves, el Bloc de notas de Jupyter es una fantástica elección.

![Captura de pantalla](./media/virtual-machines-linux-jupyter-notebook/ipy-notebook-spectral.png) paquetes usando SciPy y Matplotlib para analizar la estructura de una grabación de sonido.


## <a name="jupyter-two-ways-azure-notebooks-or-custom-deployment"></a>Jupyter dos formas: Blocs de notas de Azure o implementación personalizado
Azure proporciona un servicio que puede usar para [comenzar a usar Jupyter rápidamente ](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).  Mediante el servicio de Bloc de notas de Azure, puede obtener fácilmente acceso a la interfaz de web accesible del Jupyter a scalable recursos de cálculo con toda la potencia de Python y sus bibliotecas de muchos.  Puesto que la instalación se controla por el servicio, los usuarios pueden acceder a estos recursos sin necesidad de administración y configuración por el usuario.

Si no funciona el servicio de Bloc de notas para su situación pase a lea este artículo que se le mostrará cómo implementar el Bloc de notas de Jupyter en Microsoft Azure, usando máquinas virtuales de Linux (VM).

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="create-and-configure-a-vm-on-azure"></a>Crear y configurar una máquina virtual de Azure

El primer paso es crear una máquina virtual (VM) que se ejecuta en Azure.
Esta VM es un sistema operativo completo en la nube y se usará para ejecutar el Bloc de notas de Jupyter. Es capaz de ejecutar máquinas virtuales Linux y Windows Azure y trataremos la configuración de Jupyter en ambos tipos de máquinas virtuales de Windows.

### <a name="create-a-linux-vm-and-open-a-port-for-jupyter"></a>Crear una VM Linux y abra un puerto de Jupyter

Siga las instrucciones dada [aquí] [ portal-vm-linux] para crear una máquina virtual de la distribución de *Ubuntu* . Este tutorial usa Ubuntu Server 14.04 LTADOS. Se supondrá el nombre de usuario *azureuser*.

Después de la máquina virtual implementa es necesario abrir una regla de seguridad en el grupo de seguridad de la red.  Desde el portal de Azure, vaya a **Grupos de seguridad de la red** y abrir la pestaña grupo de seguridad correspondiente a su máquina virtual. Debe agregar una regla de seguridad de entrada con los siguientes valores: **TCP** para el protocolo **\*** para el puerto de origen (público) y **9999** para el puerto de destino (privada).

![Captura de pantalla](./media/virtual-machines-linux-jupyter-notebook/azure-add-endpoint.png)

Mientras está en el grupo de seguridad de red, haga clic en **Interfaces de red** y anote la **Dirección IP pública** como se necesitará para conectarse a su máquina virtual en el siguiente paso.

## <a name="install-required-software-on-the-vm"></a>Instalar software necesario en la máquina virtual

Para ejecutar el Bloc de notas de Jupyter en nuestra VM, nos debemos instalar Jupyter y sus dependencias. Conectar con su linux vm mediante ssh y el nombre de usuario y la contraseña de par eligió al crear la máquina virtual. En este tutorial se utilizará PuTTY y conectar desde Windows.

### <a name="installing-jupyter-on-ubuntu"></a>Instalar Jupyter en Ubuntu
Instale Anaconda una distribución de python ciencias de datos más conocidos, usando uno de los vínculos de [Análisis continuo](https://www.continuum.io/downloads).  A partir de la escritura de este documento, los vínculos de abajo son más hasta versiones de fecha.

#### <a name="anaconda-installs-for-linux"></a>Instalaciones de anaconda para Linux
<table>
  <th>3.4 Python</th>
  <th>Python 2.7</th>
  <tr>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh'>64 bits</href>
    </td>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86_64.sh'>64 bits</href>
    </td>
  </tr>
  <tr>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86.sh'>32 bits</href>
    </td>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86.sh'>32 bits</href>
    </td>  
  </tr>
</table>


#### <a name="installing-anaconda3-230-64-bit-on-ubuntu"></a>Instalar Anaconda3 2.3.0 64 bits en Ubuntu
Por ejemplo, esto es cómo puede instalar Anaconda en Ubuntu

    # install anaconda
    cd ~
    mkdir -p anaconda
    cd anaconda/
    curl -O https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh
    sudo bash Anaconda3-2.3.0-Linux-x86_64.sh -b -f -p /anaconda3

    # clean up home directory
    cd ..
    rm -rf anaconda/

    # Update Jupyter to the latest install and generate its config file
    sudo /anaconda3/bin/conda install jupyter -y
    /anaconda3/bin/jupyter-notebook --generate-config


![Captura de pantalla](./media/virtual-machines-linux-jupyter-notebook/anaconda-install.png)


### <a name="configuring-jupyter-and-using-ssl"></a>Configurar Jupyter y utilizar SSL
Después de instalar debemos dedique un momento a los archivos de configuración de instalación de Jupyter. Si experimenta problemas con la configuración de Jupyter puede ser útil ver la [Documentación de Jupyter para ejecutar un servidor de Bloc de notas](http://jupyter-notebook.readthedocs.org/en/latest/public_server.html).

Siguiente se `cd` en el directorio de perfil para crear nuestro certificado SSL y editar el archivo de configuración de perfiles.

En Linux, utilice el siguiente comando.

    cd ~/.jupyter

Use el comando siguiente para crear el certificado SSL (Linux y Windows).

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Tenga en cuenta que estamos creando un certificado SSL autofirmado, al conectarse al Bloc de notas en el explorador dará una advertencia de seguridad.  Uso de producción a largo plazo, desea utilizar un certificado debidamente firmado asociado a su organización.  Puesto que la administración de certificados está fuera del alcance de esta demostración, se le limitarse a un certificado autofirmado por ahora.

Además de usar un certificado, también debe proporcionar una contraseña para proteger su bloc de notas de uso no autorizado.  Por motivos de seguridad Jupyter utiliza contraseñas cifradas en su archivo de configuración, así que deberá cifrar la contraseña en primer lugar.  IPython proporciona una utilidad para ello; en un símbolo del sistema, ejecute el siguiente comando.

    /anaconda3/bin/python -c "import IPython;print(IPython.lib.passwd())"

Esto le pedirá que asigne una contraseña y la confirmación y, a continuación, imprimirá la contraseña. Nota: para el siguiente paso.

    Enter password:
    Verify password:
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

A continuación, se modificará el archivo de configuración del perfil, que es el `jupyter_notebook_config.py` archivo en el directorio que esté usando.  Nota que este archivo no exista--crearlo solo si es el caso.  Este archivo contiene un número de campos y de forma predeterminada todos están comentados.  Puede abrir este archivo con cualquier editor de texto de su gusto y debe asegurarse de que tiene al menos el siguiente contenido. **Asegúrese de reemplazar el c.NotebookApp.password en la configuración con sha1 desde el paso anterior**.

    c = get_config()

    # You must give the path to the certificate file.
    c.NotebookApp.certfile = u'/home/azureuser/.jupyter/mycert.pem'

    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '

    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed traffic on that port
    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### <a name="run-the-jupyter-notebook"></a>Ejecutar el Bloc de notas de Jupyter

En este momento estamos listos para iniciar el Bloc de notas de Jupyter. Para ello, vaya al directorio que desea almacenar los blocs de notas en e inicie el servidor de Bloc de notas de Jupyter con el siguiente comando.

    /anaconda3/bin/jupyter-notebook

Ahora debería poder tener acceso a su bloc de notas de Jupyter en la dirección `https://[PUBLIC-IP-ADDRESS]:9999`.

Cuando se abre primero el Bloc de notas, la página de inicio de sesión le pide su contraseña. Y una vez que inicie sesión, verá el panel"Jupyter Bloc de notas," ¿Cuál es el centro de ontrol para todas las operaciones de Bloc de notas.  Desde esta página puede crear blocs de notas y abrir las existentes.

![Captura de pantalla](./media/virtual-machines-linux-jupyter-notebook/jupyter-tree-view.png)

### <a name="using-the-jupyter-notebook"></a>Usar el Bloc de notas de Jupyter

Si hace clic en el botón **nuevo** , verá la siguiente página de apertura.

![Captura de pantalla](./media/virtual-machines-linux-jupyter-notebook/jupyter-untitled-notebook.png)

El área marcada con un `In []:` mensaje es el área de entrada y a continuación puede escribir cualquier código Python válido y ejecutar al presionar `Shift-Enter` o haga clic en el icono de "Reproducir" (el triángulo hacia la derecha en la barra de herramientas).

## <a name="a-powerful-paradigm-live-computational-documents-with-rich-media"></a>Un paradigma eficaces: live cálculo documentos con medios enriquecidos

El Bloc de notas debe sentirse muy natural a cualquier persona que ha utilizado Python y procesador de word, porque está en algunas formas en una combinación de ambos: puede ejecutar bloques de código Python, pero también puede mantener las notas y otro texto cambiando el estilo de una celda de "Código" a "Descuento" usar el menú desplegable en la barra de herramientas.

Jupyter es mucho más que un procesador de texto, ya que permite la mezcla de cálculo y medios enriquecidos (texto, gráficos, vídeo y prácticamente cualquier elemento que puede mostrar un explorador web moderna). Puede combinar, texto, código, vídeos y mucho más!

![Captura de pantalla](./media/virtual-machines-linux-jupyter-notebook/jupyter-editing-experience.png)

Con la potencia de muchas bibliotecas de excelencia de Python científicos y técnicos informática y, en la siguiente captura de pantalla, que puede realizar un cálculo simple con la misma facilidad que un análisis de red complejas, todo en un entorno.

Este paradigma de combinación de la potencia de la web moderna cálculo directo ofrece muchas posibilidades y es ideal para la nube. Puede utilizar el Bloc de notas:

* Como un bloc de notas de cálculo grabar exploración trabajar en un problema.

* Para compartir los resultados con compañeros, en el formulario cálculo 'live' o en formatos de copias impresas (HTML, PDF).

* Para distribuir y presentar los materiales de formación directo que implican cálculo, por lo que los estudiantes inmediatamente pueden experimentar con el código real, modificar y volver a ejecutar interactiva.

* Para proporcionar "papeles ejecutables" que presentan los resultados de la investigación de manera que se puede inmediatamente reproducir, validar y extendida por otros usuarios.

* Como una plataforma para informática de colaboración: varios usuarios pueden iniciar sesión el mismo servidor de Bloc de notas para compartir una sesión de cálculo activa.


Si va a la de código fuente IPython [repositorio][], encontrará todo un directorio con ejemplos de Bloc de notas que puede descargar y, a continuación, experimentar con en su propia máquina virtual Jupyter de Azure.  Basta con descargar el `.ipynb` archivos desde el sitio y cargarlos en el panel del Bloc de notas Azure VM (o descárguelos directamente en la máquina virtual).

## <a name="conclusion"></a>Conclusión

El Bloc de notas de Jupyter proporciona una interfaz eficaz para tener acceso a la potencia del ecosistema Python en Azure de forma interactiva.  Cubra una amplia gama de casos de uso incluidos exploración simple y aprendizaje Python, análisis de datos y visualización, simulación e informática en paralelo. Los documentos de Bloc de notas resultantes contienen un registro completo de los cálculos que se realizan y se puede compartir con otros usuarios Jupyter.  Puede utilizar el Bloc de notas de Jupyter como una aplicación local, pero es ideal para implementaciones de nube de Azure

Las características básicas de Jupyter también están disponibles en Visual Studio a través de [Python Tools para Visual Studio][] (PTVS). PTVS es una gratuita e integración de la informática de complemento de Microsoft Visual Studio se convierte en un entorno de desarrollo de Python avanzado que incluye un editor avanzado con IntelliSense, depuración paralelo y generación de perfiles de código abierto.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de Python](/develop/python/).

[portal-vm-linux]: https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-tutorial-portal-rm/
[repositorio]: https://github.com/ipython/ipython
[Python Tools para Visual Studio]: http://aka.ms/ptvs
