<properties
    pageTitle="Empezar a trabajar con el SDK de puerta de enlace de concentrador IoT | Microsoft Azure"
    description="Usar Windows para ilustrar los conceptos clave que debería entender cuando se utiliza el SDK de puerta de enlace de Azure IoT Azure tutorial de IoT SDK de puerta de enlace."
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>


# <a name="azure-iot-gateway-sdk-beta---get-started-using-windows"></a>SDK de puerta de enlace de Azure IoT (beta) - Introducción al uso de Windows

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Cómo crear los datos de ejemplo

Antes de empezar, debe [Configurar el entorno de desarrollo] [ lnk-setupdevbox] para trabajar con el SDK de Windows.

1. Abra un símbolo del sistema de **símbolo del programador para VS2015** .
2. Vaya a la carpeta raíz de su copia local del repositorio de **puerta de enlace sdk azure-iot** .
3. Ejecutar la **herramientas\\build.cmd** secuencia de comandos. Esta secuencia de comandos crea un archivo de solución de Visual Studio, la solución, crea y ejecuta las pruebas. Puede encontrar la solución de Visual Studio en la carpeta de **compilación** de su copia local del repositorio de **puerta de enlace sdk azure-iot** .

## <a name="how-to-run-the-sample"></a>Cómo ejecutar el ejemplo

1. La secuencia de comandos **build.cmd** crea una carpeta denominada **crear** en su copia local del repositorio. Esta carpeta contiene los dos módulos utilizados en este ejemplo.

    La secuencia de comandos de compilación coloca **logger_hl.dll** en la **crear\\módulos\\registrador\\depurar** carpeta y **hello_world_hl.dll** en la **crear\\módulos\\hello_world\\depurar** carpeta. Use estas rutas de acceso para el valor de la **ruta de acceso del módulo** tal como se muestra en el archivo de configuración de JSON siguiente.

2. El archivo **hello_world_win.json** en la **ejemplos\\hello_world\\src** carpeta es un archivo de configuración de JSON de ejemplo para Windows que puede usar para ejecutar el ejemplo. La configuración de JSON de ejemplo que se muestra a continuación, se supone que clonar el repositorio IoT SDK de puerta de enlace en la raíz de la unidad **C:** . Si se ha descargado a otra ubicación, debe ajustar los valores de la **ruta de acceso del módulo** en el **ejemplos\\hello_world\\src\\hello_world_win.json** archivo según corresponda.

3. En el módulo **logger_hl** , en la sección **argumentos** , establezca el valor de **nombre de archivo** para el nombre y la ruta de acceso del archivo que contiene los datos del registro.

    Este es un ejemplo de un archivo de configuración de JSON para Windows que escriba en el archivo **log.txt** en la raíz de la unidad **C:** .

    ```
    {
      "modules" :
      [
        {
          "module name" : "logger_hl",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\logger\\Debug\\logger_hl.dll",
          "args" : 
          {
            "filename":"C:\\log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\\\modules\\hello_world\\Debug\\hello_world_hl.dll",
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

3. En el símbolo del sistema, vaya a la carpeta raíz de su copia local del repositorio de **puerta de enlace sdk azure-iot** .
4. Ejecute el siguiente comando:
  
  ```
  build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json
  ```

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md