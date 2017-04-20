Azure determinará la versión de Python para su entorno virtual con la prioridad siguiente:

1. versión especificada en runtime.txt en la carpeta raíz
1. versión especificada por el valor de Python en la configuración de la aplicación web (la **configuración de** > módulo de**Configuración de la aplicación** para la aplicación web en el Portal de Azure)
1. 2.7 Python es el valor predeterminado si no se especifica ninguno de los anteriores

Valores válidos para el contenido de 

    \runtime.txt

son:

- Python 2.7
- 3.4 Python

Si la versión micro especificado (tercer dígito), se omite.
