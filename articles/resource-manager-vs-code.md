<properties
   pageTitle="Utilizar código de VS con las plantillas de administrador de recursos | Microsoft Azure"
   description="Muestra cómo configurar el código de Visual Studio para crear plantillas de administrador de recursos de Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="cmatskas"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/26/2016"
   ms.author="chmatsk;tomfitz"/>

# <a name="working-with-azure-resource-manager-templates-in-visual-studio-code"></a>Trabajar con plantillas de administrador de recursos de Azure en el código de Visual Studio

Plantillas de administrador de recursos de Azure son archivos JSON que describen un recurso y las dependencias relacionadas. Estos archivos a veces pueden ser grandes y complicado para herramientas de soporte técnico es importante. Código de Visual Studio es un editor de código nuevo, Abrir origen, ligero entre plataformas. Admite la creación y edición de plantillas de administrador de recursos a través de una [extensión de nuevo](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). Código de VS ejecuta en todas partes y no requiere acceso a Internet a menos que también desee implementar las plantillas de administrador de recursos.

Si no dispone de código VS, puede instalarlo en [https://code.visualstudio.com/](https://code.visualstudio.com/).

## <a name="install-the-resource-manager-extension"></a>Instale la extensión del Administrador de recursos

Para trabajar con las plantillas JSON en código VS, debe instalar una extensión. Los siguientes pasos descargar e instalación la compatibilidad de idioma para plantillas de JSON Administrador de recursos:

1. Iniciar código VS 
2. Abrir abrir rápido (Ctrl + P) 
3. Ejecute el siguiente comando: 

        ext install azurerm-vscode-tools

4. Reinicie VS código cuando se le solicite para habilitar la extensión. 

 ¡Trabajo!

## <a name="set-up-resource-manager-snippets"></a>Configurar el Administrador de recursos fragmentos

Los pasos anteriores habían instalado la compatibilidad de herramientas, pero ahora es necesario configurar VS código para usar fragmentos de código de plantilla JSON.

1. Copie el contenido del archivo del repositorio de [azure-xplat-arm-herramientas](https://raw.githubusercontent.com/Azure/azure-xplat-arm-tooling/master/VSCode/armsnippets.json) al Portapapeles.
2. Iniciar código VS 
3. En el código de VS, puede abrir el archivo de fragmentos JSON, desplácese hasta el **archivo** -> **Preferencias** -> **Fragmentos de usuario** -> **JSON**, o bien, seleccionar **F1** y escribir **Preferencias** hasta que puede seleccionar **Preferencias: fragmentos**.

    ![fragmentos de preferencias](./media/resource-manager-vs-code/preferences-snippets.png)

    En Opciones, seleccione **JSON**.

    ![Seleccione json](./media/resource-manager-vs-code/select-json.png)

4. Pegar el contenido del archivo en el paso 1, en el archivo de fragmentos de código de usuario antes del final "}" 
5. Asegúrese de que el JSON es Aceptar y no hay ningún zigzag en cualquier lugar. 
6. Guarde y cierre el archivo de fragmentos de código de usuario.

Eso es todo lo necesario para empezar a usar los fragmentos de código del Administrador de recursos. A continuación, deberá incluimos esta configuración a la prueba.

## <a name="work-with-template-in-vs-code"></a>Trabajar con la plantilla en el código de VS

Es la manera más sencilla de empezar a trabajar con una plantilla de captar una de las plantillas de inicio, rápida, en disponibles en [Github](https://github.com/Azure/azure-quickstart-templates) o usar uno de sus propios. Puede [exportar una plantilla](resource-manager-export-template.md) para cualquiera de los grupos de recursos a través del portal. 

1. Si exporta una plantilla de un grupo de recursos, abra los archivos extraídos en código VS.

    ![Mostrar archivos](./media/resource-manager-vs-code/show-files.png)

2. Abra el archivo template.json para que puedan editarlo y agregar algunos recursos adicionales. Después de la **"recursos": [** presione ENTRAR para iniciar una nueva línea. Si escribe **arm**, aparecerá una lista de opciones. Estas opciones están los fragmentos de código de plantilla que ha instalado. Debe ser similar a esta: 

    ![Mostrar fragmentos de código](./media/resource-manager-vs-code/type-snippets.png)

3. Seleccione el fragmento que desee. En este artículo, estoy elegir **arm ip** para crear una nueva dirección IP pública. Colocar una coma después del corchete de cierre "}" del recurso recién creado para asegurarse de que la plantilla, la sintaxis es válida.

     ![Agregar una coma](./media/resource-manager-vs-code/add-comma.png)

4. Código de VS tiene IntelliSense integrado. Al editar sus plantillas, código VS sugiere valores disponibles. Por ejemplo, para agregar una sección de variables a la plantilla, agregue **""** (dos comillas dobles) y seleccione **Ctrl + espacio** entre las comillas. Se presentará con opciones de **variables**.

    ![agregar variables](./media/resource-manager-vs-code/add-variables.png)

5. También puede sugerir IntelliSense funciones o valores disponibles. Para establecer una propiedad en un valor de parámetro, crear una expresión con **"[]"** y **Ctrl + barra espaciadora**. Puede empezar a escribir el nombre de una función. Seleccione la **pestaña** cuando haya encontrado la función que desee.

    ![Agregar parámetro](./media/resource-manager-vs-code/select-parameters.png)

6. Vuelva a seleccionar **CTRL+BARRA ESPACIADORA** dentro de la función para ver una lista de los parámetros disponibles dentro de la plantilla.

    ![Agregar parámetro](./media/resource-manager-vs-code/select-avail-parameters.png)

7. Si tiene problemas de validación de esquema en la plantilla, verá el zigzag habituales en el editor. Puede ver la lista de errores y advertencias escribiendo **Ctrl + Mayús + M** o seleccionando los glifos en la barra de estado a la izquierda inferior.

    ![errores](./media/resource-manager-vs-code/errors.png)

    Validación de la plantilla puede ayudarle a detectar problemas de sintaxis; Sin embargo, también puede ver los errores que puede pasar por alto. En algunos casos, el editor compara la plantilla con un esquema que no está actualizado e informa de un error, por tanto, aunque se sabe que es correcta. Por ejemplo, supongamos que una función se ha agregado recientemente para el Administrador de recursos, pero no se ha actualizado el esquema. El editor de informes de error a pesar de que la función funciona correctamente durante la implementación.

    ![mensaje de error](./media/resource-manager-vs-code/unrecognized-function.png)

## <a name="deploy-your-new-resources"></a>Implementar los nuevos recursos

Cuando la plantilla esté lista, puede implementar los nuevos recursos siguiendo las instrucciones siguientes: 

### <a name="windows"></a>Windows

1. Abra un símbolo del sistema de PowerShell 
2. En tipo de inicio de sesión: 

        Login-AzureRmAccount 

3. Si tiene varias suscripciones, obtener una lista de las suscripciones con:

        Get-AzureRmSubscription

    Y seleccione la suscripción para que utilice.
   
        Select-AzureRmSubscription -SubscriptionId <Subscription Id>

4. Actualizar los parámetros del archivo de parameters.json
5. Ejecutar el Deploy.ps1 para implementar la plantilla de Azure

### <a name="osxlinux"></a>OSX/Linux

1. Abra una ventana de terminal 
2. En tipo de inicio de sesión:

        azure login 

3. Si tiene varias suscripciones, seleccione la suscripción con:

        azure account set <subscriptionNameOrId> 

4. Actualizar los parámetros en el archivo parameters.json.
5. Para implementar la plantilla, ejecute:

        azure group deployment create -f <PathToTemplate> 

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre las plantillas, vea [plantillas de administrador de recursos de Azure de creación](resource-group-authoring-templates.md).
- Para obtener información sobre las funciones de plantilla, vea [funciones de plantilla de administrador de recursos de Azure](resource-group-template-functions.md).
- Para obtener más ejemplos de trabajar con código de Visual Studio, vea [generar aplicaciones de nube con código de Visual Studio](https://github.com/Microsoft/HealthClinic.biz/wiki/Build-cloud-apps-with-Visual-Studio-Code) de la [demostración](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/)de conectar [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015. Para más tutoriales de la demostración HealthClinic.biz, vea [Tutoriales de herramientas de desarrollador de Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).
