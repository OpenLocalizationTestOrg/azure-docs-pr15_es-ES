El script de implementación omitirá la creación del entorno virtual en Azure si detecta que ya existe un entorno virtual compatible.  Esto puede acelerar implementación considerablemente.  Se omitirá paquetes que ya están instalados por puntos.

En algunos casos, es aconsejable forzar eliminar ese entorno virtual.  Desea hacerlo si decide incluir un entorno virtual como parte del repositorio.  También puede hacerlo si necesita deshacerse de determinados paquetes o probar cambios a requirements.txt.

Hay varias opciones para administrar el entorno virtual existente en Azure:

### <a name="option-1-use-ftp"></a>Opción 1: Usar FTP

Con un cliente FTP conectarse al servidor y podrá eliminar la carpeta ent.  Observe que algunos clientes FTP (por ejemplo, los exploradores web) sean de sólo lectura y no permiten eliminar carpetas, por lo que deberá asegurarse de que sea un cliente FTP con esa capacidad.  El nombre de host FTP y el usuario se muestran en el módulo de la aplicación web en el [Portal de Azure](https://portal.azure.com).

### <a name="option-2-toggle-runtime"></a>Opción 2: Activar o desactivar ejecución

Aquí es una alternativa que aprovecha el hecho de que el script de implementación eliminará la carpeta ent al no coincide con la versión deseada de Python.  Esto eficaz eliminar el entorno existente y crear uno nuevo.

1. Cambiar a una versión diferente de Python (a través de runtime.txt o la **Configuración de la aplicación** en el Portal de Azure)
1. GIT aplicar algunos cambios (omitir los errores de instalación de puntos si hay alguna)
1. Vuelva a la versión inicial de Python
1. algunos cambios de inserción GIT nuevo

### <a name="option-3-customize-deployment-script"></a>Opción 3: Personalizar el script de implementación

Si ha personalizado la secuencia de comandos de implementación, puede cambiar el código de deploy.cmd para exigir que elimine la carpeta ent.
