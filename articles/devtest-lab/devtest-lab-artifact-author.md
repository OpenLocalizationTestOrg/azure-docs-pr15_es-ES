<properties 
    pageTitle="Crear artefactos personalizados para su VM de prácticas DevTest | Microsoft Azure"
    description="Aprender a crear sus propio artefactos para su uso con DevTest prácticas"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="tarcher"/>

#<a name="create-custom-artifacts-for-your-devtest-labs-vm"></a>Crear artefactos personalizados para su máquina virtual de prácticas de DevTest

> [AZURE.VIDEO how-to-author-custom-artifacts] 

## <a name="overview"></a>Información general
**Artefactos** se usan para implementar y configurar la aplicación después de que se aprovisiona una máquina virtual. Muestra consta de un archivo de definición de muestra y otros archivos de secuencia de comandos que se almacenan en una carpeta en un repositorio git. Archivos de definición de muestra constan de JSON y expresiones que puede utilizar para especificar que desea instalar en una máquina virtual. Por ejemplo, puede definir el nombre de la muestra, comando ejecutar y parámetros que están disponibles cuando se ejecuta el comando. Puede hacer referencia a otros archivos de secuencias de comandos en el archivo de definición de muestra por su nombre.

##<a name="artifact-definition-file-format"></a>Formato de archivo de definición de muestra
En el ejemplo siguiente se muestra las secciones que conforman la estructura básica de un archivo de definición.

    {
      "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2015-01-01/dtlArtifacts.json",
      "title": "",
      "description": "",
      "iconUri": "",
      "targetOsType": "",
      "parameters": {
        "<parameterName>": {
          "type": "",
          "displayName": "",
          "description": ""
        }
      },
      "runCommand": {
        "commandToExecute": ""
      }
    }

| Nombre del elemento | ¿Obligatorio? | Descripción
| ------------ | --------- | -----------
| $schema      | No        | Ubicación del archivo de esquema JSON que le ayuda a probar la validez del archivo de definición.
| título        | Sí       | Nombre de la muestra que se muestran en la práctica.
| Descripción  | Sí       | Descripción de la muestra que se muestran en la práctica.
| iconUri      | No        | URI del icono que se muestra en la práctica.
| targetOsType | Sí       | Sistema operativo de la máquina virtual donde se instalará la muestra. Opciones admitidas son: Windows y Linux.
| parámetros   | No        | Valores que se proporcionan cuando se ejecuta el comando de instalación de muestra en un equipo. Esto le permite personalizar la muestra.
| EjecutarComando   | Sí       | Muestra el comando que se ejecuta en una máquina virtual de instalación.

###<a name="artifact-parameters"></a>Parámetros de muestra

En la sección parámetros del archivo de definición, especifique los valores de un usuario puede introducir al instalar un efecto. Puede hacer referencia a estos valores en el comando de instalación de muestra.

Definir parámetros tendrá la estructura siguiente.

    "parameters": {
        "<parameterName>": {
          "type": "<type-of-parameter-value>",
          "displayName": "<display-name-of-parameter>",
          "description": "<description-of-parameter>"
        }
      }

| Nombre del elemento | ¿Obligatorio? | Descripción
| ------------ | --------- | -----------
| tipo         | Sí       | Tipo de valor del parámetro. Vea la siguiente lista de los tipos permitidos:
| displayName sí       | Nombre del parámetro que se muestra a un usuario de la práctica.
| Descripción  | Sí       | Descripción del parámetro que se muestra en la práctica.

Los tipos permitidos son:

- cadena: cualquier cadena JSON válida.
- int: un entero JSON válido
- booleano: cualquier JSON booleana válida
- matriz: cualquier matriz JSON válida

##<a name="artifact-expressions-and-functions"></a>Funciones y expresiones de muestra

Puede usar expresiones y funciones para crear la muestra el comando de instalación.
Se incluyen las expresiones entre corchetes ([y]) y se evaluará cuando se instala la muestra. Expresiones pueden aparecer en cualquier lugar de un valor de cadena JSON y siempre devuelven otro valor JSON. Si necesita usar una cadena literal que comienza con un corchete [, debe usar dos corchetes [[.
Normalmente, se utilizar las expresiones con funciones para construir un valor. Al igual que en JavaScript, llamadas de función tienen el formato functionName(arg1,arg2,arg3)

La siguiente lista muestra funciones comunes.

- Parameters(ParameterName) - devuelve un valor de parámetro que se proporciona cuando se ejecuta el comando de muestra.
- concatenación (arg1, arg2, arg3,...) combina varios valores de cadena. Esta función puede tener cualquier número de argumentos.

En el ejemplo siguiente se muestra cómo usar expresiones y funciones para crear un valor.

    runCommand": {
         "commandToExecute": "[concat('powershell.exe -File startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

##<a name="create-a-custom-artifact"></a>Crear una muestra personalizada

Crear su muestra personalizado siguiendo estos pasos:

1. Instalar a un editor de JSON: se necesita un editor de JSON para trabajar con archivos de definición de muestra. Se recomienda utilizar [Código de Visual Studio](https://code.visualstudio.com/), que está disponible para Windows, Linux y OS X.

1. Obtener un artifactfile.json de ejemplo: consulte los artefactos creado por el equipo de prácticas de DevTest de Azure en nuestro [GitHub repositorio](https://github.com/Azure/azure-devtestlab) donde hemos creado una amplia biblioteca de artefactos que le ayudará a crear sus propio artefactos. Descargar un archivo de definición de muestra y realizar cambios en él para crear sus propio artefactos.

1. Asegúrese de que el uso de IntelliSense: aproveche IntelliSense para ver elementos válidos que se pueden usar para crear un archivo de definición de muestra. También puede ver las distintas opciones para los valores de un elemento. Por ejemplo, IntelliSense muestran las dos opciones de Windows o Linux al editar el elemento **targetOsType** .

1. Almacenar la muestra en un repositorio de git
    1. Cree un directorio independiente para cada muestra donde el nombre del directorio es el mismo que el nombre de la muestra.
    1. Almacenar el archivo de definición de muestra (artifactfile.json) en el directorio que ha creado.
    1. Almacenar las secuencias de comandos que se hace referencia en el comando de instalación de muestra.

    Aquí tiene un ejemplo del aspecto de una carpeta de muestra:

    ![Ejemplo de repo git de muestra](./media/devtest-lab-artifact-author/git-repo.png)

1. Agregar el repositorio de artefactos a la práctica -, consulte el artículo, [Agregar un repositorio de muestra Git a un laboratorio](devtest-lab-add-artifact-repo.md).

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Entradas de blog relacionadas
- [Cómo solucionar errores defectos en AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)
- [Unirse a una máquina virtual para el dominio de Active Directory existente con la plantilla ARM en laboratorio de pruebas de desarrollo de Azure](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [Agregar un repositorio Git muestra un laboratorio](devtest-lab-add-artifact-repo.md).
