Azure determinará que la aplicación utiliza Python **cuando se cumplen las siguientes condiciones**:

- archivo de Requirements.txt en la carpeta raíz
- cualquier archivo .py en la carpeta raíz o un runtime.txt que especifica python

Cuando es el caso, usará un script de implementación específico de Python, que realiza la sincronización de archivos, así como otras operaciones de Python como estándar:

- Administración automática de entorno virtual
- Instalación de paquetes enumerados en requirements.txt con puntos
- Creación de web.config adecuados basados en la versión de Python seleccionada.
- Recopilar archivos estáticos para aplicaciones Django

Puede controlar ciertos aspectos de los pasos de implementación predeterminado sin necesidad de personalizar la secuencia de comandos.

Si desea omitir todos los pasos de implementación concretos de Python, puede crear este archivo vacío:

    \.skipPythonDeployment

Para tener más control sobre la implementación, puede reemplazar el script de implementación predeterminado mediante la creación de los archivos siguientes:

    \.deployment
    \deploy.cmd

Puede usar la [interfaz de Azure de línea de comandos][] para crear los archivos.  Use este comando de la carpeta de proyecto:

    azure site deploymentscript --python

Cuando estos archivos no existen, Azure crea un script de implementación temporal y ejecuta.  Es idéntico al que crear con el comando anterior.

[Interfaz de línea de comandos de Azure]: http://azure.microsoft.com/downloads/
