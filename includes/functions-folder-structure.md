
El código para todas las funciones en una aplicación de la función dada reside en una carpeta raíz que contiene un archivo de configuración de host y una o más subcarpetas, cada una de las cuales contienen el código para una función independiente, como en el ejemplo siguiente

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

El archivo *host.json* contiene una configuración específica de tiempo de ejecución y se encuentra en la carpeta raíz de la aplicación de la función. Para obtener información sobre la configuración que están disponibles, vea [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) en el wiki de repositorio de WebJobs.Script.

Cada función tiene una carpeta que contiene uno o más archivos de código, la configuración de function.json y otras dependencias.