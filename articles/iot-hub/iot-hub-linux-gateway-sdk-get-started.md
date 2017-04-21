<properties
    pageTitle="Empezar a trabajar con el SDK de puerta de enlace de concentrador IoT | Microsoft Azure"
    description="Este tutorial SDK de puerta de enlace de Azure IoT usa Linux para ilustrar los conceptos clave que debería entender cuando se usa el SDK de Azure IoT puerta de enlace."
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>


# <a name="azure-iot-gateway-sdk-beta---get-started-using-linux"></a>SDK de puerta de enlace de Azure IoT (beta) - empezar a usar Linux

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Cómo crear los datos de ejemplo

Antes de empezar, debe [Configurar el entorno de desarrollo] [ lnk-setupdevbox] para trabajar con el SDK en Linux.

1. Abra un shell.
2. Vaya a la carpeta raíz de su copia local del repositorio de **puerta de enlace sdk azure-iot** .
3. Ejecute el script **tools/build.sh** . Esta secuencia de comandos utiliza la utilidad **cmake** para crear una carpeta denominada **Generar** en la carpeta raíz de su copia local del repositorio de **puerta de enlace sdk azure-iot** y generar un archivo MAKE. A continuación, la secuencia de comandos genera la solución y ejecuta las pruebas.

> [AZURE.NOTE]  Cada vez que se ejecuta la secuencia de comandos de **build.sh** , elimina y, a continuación, vuelve a crear la carpeta en la carpeta raíz de su copia local del repositorio de **puerta de enlace sdk azure-iot** **Generar** .

## <a name="how-to-run-the-sample"></a>Cómo ejecutar el ejemplo

1. La secuencia de comandos de **build.sh** genera su resultado en la carpeta de **compilación** de su copia local del repositorio de **puerta de enlace sdk azure-iot** . Esto incluye los dos módulos utilizados en este ejemplo.

    La secuencia de comandos de compilación coloca **liblogger_hl.so** en la **módulos de compilación/registrador/** carpeta y **libhello_world_hl.so** en la **módulos de compilación/hello_world/** carpeta. Use estas rutas de acceso para el valor de la **ruta de acceso del módulo** tal como se muestra en el archivo de configuración de JSON siguiente.

2. El archivo **hello_world_lin.json** en la carpeta de **ejemplos, hello_world/src** es un archivo de configuración de ejemplo JSON para Linux que puede usar para ejecutar el ejemplo. La configuración de JSON de ejemplo que se muestra a continuación, se supone que se ejecutará la muestra desde la raíz de su copia local del repositorio de **puerta de enlace sdk azure-iot** .

3. En el módulo **logger_hl** , en la sección **argumentos** , establezca el valor de **nombre de archivo** para el nombre y la ruta de acceso del archivo que contiene los datos del registro.

    Este es un ejemplo de un archivo de configuración de JSON para Linux que escribirá la **log.txt** a la carpeta donde se ejecuta la muestra.

    ```
    {
      "modules" :
      [ 
        {
          "module name" : "logger_hl",
          "module path" : "./build/modules/logger/liblogger_hl.so",
          "args" : 
          {
            "filename":"./log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "./build/modules/hello_world/libhello_world_hl.so",
          "args" : null
        }
      ],
      "links" :
      [
        {
          "source": "hello_world",
          "sink": "logger_hl"
        }
      ]
    }
    ```

3. En el shell, vaya a la carpeta de la **puerta de enlace sdk azure-iot** .
4. Ejecute el siguiente comando:
  
  ```
  ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
  ``` 

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
