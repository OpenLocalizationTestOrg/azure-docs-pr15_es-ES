Algunos paquetes no pueden instalarse con puntos cuando se ejecuta en Azure.  Simplemente puede ser que el paquete no está disponible en el índice de paquete Python.  Es posible que no se requiere un compilador (un compilador no está disponible en el equipo que ejecuta la aplicación web en la aplicación de servicio de Azure).

En esta sección, veremos formas de tratar este problema.

### <a name="request-wheels"></a>Solicitud de ruedas

Si la instalación del paquete requiere un compilador, intente ponerse en contacto con el propietario del paquete para solicitar que ruedas esté disponible para el paquete.

Con la disponibilidad reciente de [Compilador de Microsoft Visual C++ para Python 2.7][], ahora es más fácil crear paquetes que tienen código nativo para Python 2.7.

### <a name="build-wheels-requires-windows"></a>Generar ruedas (requiere Windows)

Nota: Cuando se usa esta opción, asegúrese de que compilar el paquete mediante un entorno de Python que coincida con la plataforma/arquitectura o versión que se usa en la aplicación web de servicio de la aplicación de Azure (Windows/32-bit/2.7 o 3.4).

Si el paquete no se instala porque requiere un compilador, puede instalar el compilador en su equipo local y generar una rueda para el paquete, que se van a incluir, a continuación, en el repositorio.

Los usuarios de Mac o Linux: Si no tiene acceso a un equipo Windows, vea [crear una cuenta de máquina Virtual ejecuta Windows][] para crear una máquina virtual en Azure.  Puede usarlo para crear las ruedas, agregarlas al repositorio y descartar la máquina virtual si lo desea. 

Para Python 2.7, puede instalar el [Compilador de Microsoft Visual C++ para Python 2.7][].

Para Python 3.4, puede instalar [Microsoft Visual C++ 2010 Express][].

Para generar ruedas, necesitará el paquete de rueda:

    env\scripts\pip install wheel

Deberá usar `pip wheel` para compilar una dependencia:

    env\scripts\pip wheel azure==0.8.4

Esto crea un archivo .whl en la carpeta \wheelhouse.  Agregar los archivos de la carpeta y la rueda de \wheelhouse al repositorio.

Editar su requirements.txt para agregar la `--find-links` opción en la parte superior. Esto indica a puntos para buscar una coincidencia exacta en la carpeta local antes de pasar al índice de paquete python.

    --find-links wheelhouse
    azure==0.8.4

Si desea incluir todas las dependencias en la carpeta \wheelhouse y no utilice el índice de paquete python en absoluto, puede forzar puntos para omitir el índice de paquete agregando `--no-index` a la parte superior de la requirements.txt.

    --no-index

### <a name="customize-installation"></a>Personalizar la instalación

Puede personalizar el script de implementación para instalar un paquete en el entorno virtual con un instalador alternativo, como fácil\_instalar.  Vea deploy.cmd para obtener un ejemplo que se convirtió en comentario.  Asegúrese de que dichos paquetes no aparecen en requirements.txt, para evitar que los puntos instalarlas.

Agregue lo siguiente a la secuencia de comandos de implementación:

    env\scripts\easy_install somepackage

También puede usar fácil\_a instalar desde un instalador exe (algunos son zip compatible, es muy fácil\_instalar es compatible con ellos).  Agregar el programa de instalación al repositorio y fáciles invocar\_instalar pasando la ruta de acceso al archivo ejecutable.

Agregue lo siguiente a la secuencia de comandos de implementación:

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### <a name="include-the-virtual-environment-in-the-repository-requires-windows"></a>Incluir el entorno virtual en el repositorio (requiere Windows)

Nota: Cuando se usa esta opción, asegúrese de usar un entorno virtual que coincida con la plataforma y arquitectura o versión que se usa en la aplicación web de servicio de la aplicación de Azure (Windows/32-bit/2.7 o 3.4).

Si incluye el entorno virtual en el repositorio, puede impedir que el script de implementación realizar gestiones de entorno virtual en Azure mediante la creación de un archivo vacío:

    .skipPythonDeployment

Se recomienda que elimine el entorno virtual existente en la aplicación, para evitar que los archivos sobrantes cuando el entorno virtual se administra automáticamente.


[Crear una máquina Virtual con Windows]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-hero-tutorial/
[Compilador de Microsoft Visual C++ para Python 2.7]: http://aka.ms/vcpython27
[Microsoft Visual C++ 2010 Express]: http://go.microsoft.com/?linkid=9709949
