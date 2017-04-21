<properties
   pageTitle="Entornos de prueba y desarrollo | Microsoft Azure"
   description="Obtenga información sobre cómo usar las plantillas de administrador de recursos de Azure rápidamente y coherente crear y eliminar desarrollo y entornos de prueba."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/22/2016"
   ms.author="tomfitz"/>

# <a name="development-and-test-environments-in-microsoft-azure"></a>Entornos de prueba y de desarrollo de Microsoft Azure

Normalmente se implementan aplicaciones personalizadas para varios entornos de desarrollo y pruebas antes de la implementación de producción. Cuando se crean entornos local, recursos informáticos adquiridos o asignados para cada entorno para cada aplicación. Los entornos suelen incluyen varias máquinas físicas o virtuales con configuraciones específicas que se implementan manualmente o con las secuencias de comandos de automatización complejos. Implementaciones a menudo tardar horas y generar configuraciones incoherentes en entornos.

## <a name="scenario"></a>Escenario ##

Cuando se aprovisiona desarrollo y entornos de prueba en Microsoft Azure, solo pagar para los recursos que utiliza.  En este artículo se explica cómo rápidamente y siempre puede crear, mantener y eliminar desarrollo y probar entornos mediante el Administrador de recursos de Azure plantillas y archivos de parámetro, tal como se muestra a continuación.

![Escenario](./media/solution-dev-test-environments/scenario.png)

Se muestran tres entornos de desarrollo y pruebas por encima.  Cada una tiene la aplicación web y los recursos de base de datos SQL, que se especifican en un archivo de plantilla.  Los nombres de la aplicación y la base de datos cada entorno son diferentes y se especifican en archivos de parámetro único para cada entorno.  

Si no está familiarizado con los conceptos de administrador de recursos de Azure, se recomienda que lea el artículo [Información general del Administrador de recursos de Azure](azure-resource-manager/resource-group-overview.md) antes de leer este artículo.

Desea ir primero los pasos de este artículo, tal y como aparece sin leer cualquiera de los artículos mencionados obtener rápidamente algunos experiencia de uso de plantillas de administrador de recursos de Azure. Después de que ha sido una vez a través de los pasos, podrá obtener respuestas a la mayoría de las preguntas que surgieron la primera hora a través de experimentando más con los pasos y lea los artículos mencionados.

## <a name="plan-azure-resource-use"></a>Planear el uso de recursos de Azure
Una vez que tenga un diseño de alto nivel de la aplicación, puede definir:

- Los recursos que Azure incluirá la aplicación. Puede crear su aplicación e implementar como una aplicación Web de Azure con una base de datos de SQL Azure.  Puede crear la aplicación en máquinas virtuales usando PHP y MySQL IIS y SQL Server u otros componentes. El artículo de la [aplicación de servicio de Azure, servicios de nube y comparación de máquinas virtuales]( app-service-web/choose-web-site-cloud-service-vm.md) le ayuda a decidir qué recursos Azure que desea utilizar para su aplicación.
- ¿Qué requisitos de nivel de servicio como disponibilidad, seguridad y escala que la aplicación cumplirá.

## <a name="download-an-existing-template"></a>Descargar una plantilla existente
Una plantilla de administrador de recursos de Azure define todos los recursos de Azure que utiliza la aplicación. Varias plantillas ya existen que puede implementar directamente en el portal de Azure, o descargar, modificar y guardar en un sistema de control de código fuente con código de la aplicación.  Complete los pasos siguientes para descargar una plantilla existente.

1. Examinar plantillas existentes en el repositorio de GitHub [Plantillas de Azure tutorial rápido](https://github.com/Azure/azure-quickstart-templates/) . En la lista, verá una carpeta "[201-web-aplicación-base de datos sql](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)". Puesto que muchas aplicaciones personalizadas incluyen una aplicación web y base de datos SQL, esta plantilla se usa como ejemplo en el resto de este artículo para ayudarle a entender cómo usar las plantillas. Está fuera del ámbito de este artículo para explicar todo esta plantilla crea y configura, pero si piensa usarlo para crear entornos reales de su organización, deseará comprender, lea el artículo [aprovisionar una aplicación web con una base de datos de SQL](app-service-web/app-service-web-arm-with-sql-database-provision.md) .
Nota: este artículo se ha escrito para la versión de diciembre de 2015 de la plantilla de [201-web-aplicación-base de datos sql](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database) . Los vínculos que señalan a plantilla y archivos de parámetro para que la versión de la plantilla.
2. Haga clic en el archivo [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json) en la carpeta 201-web-aplicación-base de datos sql para ver su contenido. Este es el archivo de plantilla de administrador de recursos de Azure. 
3. En el modo de vista, haga clic en el botón "[materia](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json)". 
4. Con el mouse, seleccionar el contenido de este archivo y guardarlos en su equipo como un archivo denominado "Template.json TestApp1." 
5. Examinar el contenido de la plantilla y observe lo siguiente:
 - Sección de **recursos** : esta sección define los tipos de recursos de Azure creados con esta plantilla. Entre otros tipos de recursos, esta plantilla crea recursos [Azure Web App](app-service-web/app-service-web-overview.md) y [Base de datos de SQL Azure](sql-database/sql-database-technical-overview.md) . Si prefiere ejecutar y administrar web y los servidores de SQL Server en máquinas virtuales de Windows, puede usar las plantillas de "[iis-2vm-sql-1vm](https://github.com/Azure/azure-quickstart-templates/tree/master/iis-2vm-sql-1vm)" o "[lámpara de aplicación](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app)", pero las instrucciones de este artículo se basan en la plantilla de [201-web-aplicación-base de datos sql](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database) .
 - Sección **parámetros** : esta sección definen los parámetros que se puede configurar con cada recurso. Algunos de los parámetros especificados en la plantilla tienen propiedades de "defaultValue", mientras que otras personas no. Al implementar recursos Azure con una plantilla, debe proporcionar los valores para todos los parámetros que no tienen propiedades defaultValue especificadas en la plantilla.  Si no se proporcionan valores para los parámetros con las propiedades de defaultValue, se usa el valor especificado para el parámetro defaultValue en la plantilla.

Una plantilla define los recursos que Azure se crean y los parámetros de cada recurso puede configurarse con. Puede obtener más información sobre cómo diseñar su propia lea el artículo de [los procedimientos recomendados para diseñar plantillas de administrador de recursos de Azure](best-practices-resource-manager-design-templates.md) y plantillas.

## <a name="download-and-customize-an-existing-parameter-file"></a>Descargar y personalizar un archivo de parámetro existente

Aunque es probable que prefiera el *mismo* Azure recursos creados en cada entorno, probablemente querrá la configuración de los recursos para ser *diferentes* en cada entorno.  Esta es la procedencia de los archivos de parámetros. Crear archivos de parámetro que contienen valores únicos en cada entorno complete los pasos siguientes.   

1. Ver el contenido del archivo [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json) en la carpeta 201 web app sql database. Este es el archivo de parámetros para el archivo de plantilla que guardó en la sección anterior. 
2. En el modo de vista, haga clic en el botón "[materia](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json)". 
3. Con el mouse, seleccionar el contenido de este archivo y guardarlos en tres archivos independientes en el equipo con los siguientes nombres:
 - Parámetros de TestApp1 de Development.json
 - Parámetros de TestApp1 de Test.json
 - TestApp1-parámetros-Pre-Production.json

3. Con cualquier texto o un editor de JSON, editar el archivo de parámetros de entorno de desarrollo que creó en el paso 3, reemplazar los valores que aparecen a la derecha de los valores de parámetro en el archivo con los *valores* que aparecen a la derecha de los **parámetros** a continuación: 
 - **nombre del sitio**: *TestApp1DevApp*
 - **hostingPlanName**: *TestApp1DevPlan*
 - **siteLocation**: *Central de EE.*
 - **nombre de servidor**: *testapp1devsrv*
 - **serverLocation**: *Central de EE.*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *Reemplazar con la contraseña*
 - **databaseName**: *testapp1devdb*

4. Con cualquier texto o un editor de JSON, edite el archivo de parámetros de entorno de prueba que creó en el paso 3, reemplazando la lista de los valores que aparecen a la derecha de los valores de parámetro en el archivo con los *valores* a la derecha de los **parámetros** siguientes:
 - **nombre del sitio**: *TestApp1TestApp*
 - **hostingPlanName**: *TestApp1TestPlan*
 - **siteLocation**: *Central de EE.*
 - **nombre de servidor**: *testapp1testsrv*
 - **serverLocation**: *Central de EE.*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**: *Reemplazar con la contraseña*
 - **databaseName**: *testapp1testdb*

5. Con cualquier texto o un editor de JSON, editar el archivo de parámetros de producción anterior que creó en el paso 3.  Reemplazar todo el contenido del archivo con lo que hay debajo de:

        {
          "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
          "contentVersion" : "1.0.0.0",
          "parameters" : {
        "administratorLogin" : {
          "value" : "testApp1Admin"
        },
        "administratorLoginPassword" : {
          "value" : "replace with your password"
        },
        "databaseName" : {
          "value" : "testapp1preproddb"
        },
        "hostingPlanName" : {
          "value" : "TestApp1PreProdPlan"
        },
        "serverLocation" : {
          "value" : "Central US"
        },
        "serverName" : {
          "value" : "testapp1preprodsrv"
        },
        "siteLocation" : {
          "value" : "Central US"
        },
        "siteName" : {
          "value" : "TestApp1PreProdApp"
        },
        "sku" : {
          "value" : "Standard"
        },
            "requestedServiceObjectiveName" : {
              "value" : "S1"
        }
          }
        }

En el archivo de parámetros de producción preliminar anterior, los parámetros **sku** y **requestedServiceObjectiveName** fueron *agregado*, mientras que los que no se han agregado en los archivos de parámetros de prueba y desarrollo. Esto es porque hay valores predeterminados especificados para estos parámetros en la plantilla y en los entornos de prueba y desarrollo, se usan los valores predeterminados, pero en la producción anterior se usan los valores de entorno no predeterminada para estos parámetros.

El motivo por valores predeterminados no se usan para estos parámetros en el entorno de producción previa es probar los valores de estos parámetros que es posible que prefiera para su entorno de producción para que también puede comprobar.  Estos todos los parámetros relacionan a la Azure [planes de hospedaje Web App](https://azure.microsoft.com/pricing/details/app-service/), o **sku** y Azure [Base de datos de SQL](https://azure.microsoft.com/pricing/details/sql-database/)o **requestedServiceObjectiveName** que usa la aplicación.  Diferentes SKU y nombres de servicio objetivo tengan costes diferentes y características y soporte métrica del nivel de servicio diferente.

La siguiente tabla enumera los valores predeterminados de estos parámetros especificados en la plantilla y los valores que se utilizan en lugar de los valores predeterminados en el archivo de parámetros de producción anterior.

| Parámetro | Valor predeterminado | Valor del parámetro archivo |
|---|---|---|
| **SKU** | Liberar | Estándar |
| **requestedServiceObjectiveName** | S0 | S1 |

## <a name="create-environments"></a>Crear entornos
Todos los recursos de Azure deben crearse dentro de un [Grupo de recursos de Azure](azure-resource-manager/resource-group-overview.md). Grupos de recursos le permiten agrupar recursos Azure para que puedan administrarse colectivamente.  Pueden asignarse [permisos](./active-directory/role-based-access-built-in-roles.md) a grupos de recursos que personas específicas dentro de su organización pueden crear, modificar, eliminar o ver ellos y los recursos en ellos.  Alertas e información de facturación para los recursos del grupo de recursos se pueden ver en el [Portal de Azure](https://portal.azure.com). Grupos de recursos se crean en una [región](https://azure.microsoft.com/regions/)de Azure.  En este artículo, se crean todos los recursos en la región de Estados Unidos Central. Al empezar a crear entornos reales, deberá elegir la región que mejor se adapte a sus necesidades. 

Crear grupos de recursos para cada entorno con cualquiera de los métodos siguientes.  Todos los métodos conseguir el mismo resultado.

###<a name="azure-command-line-interface-cli"></a>Interfaz de línea de comandos (CLI) de Azure

Asegúrese de que tiene la CLI [instalado](xplat-cli-install.md) en ya sea un Windows, OS X, o equipo Linux y que usted haber [conectado](xplat-cli-connect.md) su [cuenta de Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (también llamado una cuenta profesional o educativa) su suscripción de Azure. Desde la línea de comandos CLI, escriba el comando siguiente para crear el grupo de recursos para el entorno de desarrollo.

    azure group create "TestApp1-Development" "Central US"

Si se realiza correctamente, devolverá el siguiente el comando:

    info:    Executing command group create
    + Getting resource group TestApp1-Development
    + Creating resource group TestApp1-Development
    info:    Created resource group TestApp1-Development
    data:    Id:                  /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development
    data:    Name:                TestApp1-Development
    data:    Location:            centralus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Para crear el grupo de recursos para el entorno de prueba, escriba el siguiente comando:

    azure group create "TestApp1-Test" "Central US"

Para crear el grupo de recursos para el entorno de producción anterior, escriba el siguiente comando:

    azure group create "TestApp1-Pre-Production" "Central US"

###<a name="powershell"></a>PowerShell

Asegúrese de que tiene Azure PowerShell 1.01 o superior instalado en un equipo de Windows y ha conectado su [cuenta de Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (también llamado una cuenta profesional o educativa) a su suscripción como se describe en el artículo [cómo instalar y configurar Azure PowerShell](powershell-install-configure.md) . Desde el símbolo del sistema de PowerShell, escriba el comando siguiente para crear el grupo de recursos para el entorno de desarrollo.

    New-AzureRmResourceGroup -Name TestApp1-Development -Location "Central US"

Si se realiza correctamente, devolverá el siguiente el comando:

    ResourceGroupName : TestApp1-Development
    Location          : centralus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

Para crear el grupo de recursos para el entorno de prueba, escriba el siguiente comando:

    New-AzureRmResourceGroup -Name TestApp1-Test -Location "Central US"

Para crear el grupo de recursos para el entorno de producción anterior, escriba el siguiente comando:

    New-AzureRmResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

###<a name="azure-portal"></a>Portal de Azure

1. Inicie sesión en el [portal de Azure](https://portal.azure.com) con una cuenta de [Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) (también llamado trabajo o escuela). Haga clic en nuevo--Administración--> grupo de recursos y escriba "Desarrollo de TestApp1" en el cuadro Nombre de grupo de recursos, seleccione la suscripción y seleccione "Central nos" en el cuadro de ubicación de grupo de recursos, como se muestra en la siguiente imagen.
   ![Portal](./media/solution-dev-test-environments/rgcreate.png)
2. Haga clic en el botón Crear para crear el grupo de recursos.
3. Haga clic en Examinar, desplácese hacia abajo de la lista de grupos de recursos y haga clic en grupos de recursos, como se muestra a continuación.
   ![Portal](./media/solution-dev-test-environments/rgbrowse.png) 
4. Después de hacer clic en grupos de recursos, verá el módulo de grupos de recursos con su nuevo grupo de recursos.
   ![Portal](./media/solution-dev-test-environments/rgview.png)
5. Crear la prueba de TestApp1 y recursos TestApp1 Pre-producción grupos de la misma manera que creó el grupo de recursos de desarrollo de TestApp1 anterior.

##<a name="deploy-resources-to-environments"></a>Implementar recursos en entornos

Implementar recursos Azure a los grupos de recursos para cada entorno utilizando el archivo de plantilla para la solución y los archivos de parámetro para cada entorno con cualquiera de los métodos siguientes.  Ambos métodos conseguir el mismo resultado.

###<a name="azure-command-line-interface-cli"></a>Interfaz de línea de comandos (CLI) de Azure

Desde la línea de comandos CLI, escriba el comando siguiente para implementar recursos al grupo de recursos que ha creado para el entorno de desarrollo, reemplazar [path] con la ruta de acceso a los archivos que guardó en los pasos anteriores.

    azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

Después de unos minutos, ve un mensaje "Esperando implementación completar", el comando devolverá el siguiente si se realiza correctamente:

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "Deployment1"
    + Waiting for deployment to complete
    data:    DeploymentName     : Deployment1
    data:    ResourceGroupName  : TestApp1-Development
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : XXXX-XX-XXT20:20:23.5202316Z
    data:    Mode               : Incremental
    data:    Name                           Type          Value
    data:    -----------------------------  ------------  ----------------------------
    data:    siteName                       String        TestApp1DevApp
    data:    hostingPlanName                String        TestApp1DevPlan
    data:    siteLocation                   String        Central US
    data:    sku                            String        Free
    data:    workerSize                     String        0
    data:    serverName                     String        testapp1devsrv
    data:    serverLocation                 String        Central US
    data:    administratorLogin             String        testapp1Admin
    data:    administratorLoginPassword     SecureString  undefined
    data:    databaseName                   String        testapp1devdb
    data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
    data:    edition                        String        Standard
    data:    maxSizeBytes                   String        1073741824
    data:    requestedServiceObjectiveName  String        S0
    info:    group deployment create command OKx

Si el comando no se realiza correctamente, resolver los mensajes de error y vuelva a intentarlo.  Los problemas más comunes son con valores de parámetro que cumple las restricciones de nomenclatura de recursos de Azure. Otras sugerencias para solucionar problemas pueden encontrarse en el artículo de [implementaciones de grupo de recursos de solución de problemas en Azure](./resource-manager-troubleshoot-deployments-cli.md) .

Desde la línea de comandos CLI, escriba el comando siguiente para implementar recursos al grupo de recursos que ha creado para el entorno de prueba, reemplazar [path] con la ruta de acceso a los archivos que guardó en los pasos anteriores.

    azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

Desde la línea de comandos CLI, escriba el comando siguiente para implementar recursos al grupo de recursos que ha creado para el entorno de producción anterior, reemplazar [path] con la ruta de acceso a los archivos que guardó en los pasos anteriores.

    azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json
  
###<a name="powershell"></a>PowerShell

Desde un símbolo del sistema de Azure PowerShell (versión 1.01 o superior), escriba el comando siguiente para implementar recursos al grupo de recursos que ha creado para el entorno de desarrollo, reemplazar [path] con la ruta de acceso a los archivos que guardó en los pasos anteriores.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

Después de ver un cursor intermitente durante unos minutos, el comando devolverá el siguiente si se realiza correctamente:

    DeploymentName    : Deployment1
    ResourceGroupName : TestApp1-Development
    ProvisioningState : Succeeded
    Timestamp         : XX/XX/XXXX 2:44:48 PM
    Mode              : Incremental
    TemplateLink      : 
    Parameters        : 
                        Name             Type                       Value     
                        ===============  =========================  ==========
                        siteName         String                     TestApp1DevApp
                        hostingPlanName  String                     TestApp1DevPlan
                        siteLocation     String                     Central US
                        sku              String                     Free      
                        workerSize       String                     0         
                        serverName       String                     testapp1devsrv
                        serverLocation   String                     Central US
                        administratorLogin  String                     testapp1Admin
                        administratorLoginPassword  SecureString                         
                        databaseName     String                     testapp1devdb
                        collation        String                     SQL_Latin1_General_CP1_CI_AS
                        edition          String                     Standard  
                        maxSizeBytes     String                     1073741824
                        requestedServiceObjectiveName  String                     S0        
                        
    Outputs           :

  Si el comando no se realiza correctamente, resolver los mensajes de error y vuelva a intentarlo.  Los problemas más comunes son con valores de parámetro que cumple las restricciones de nomenclatura de recursos de Azure. Otras sugerencias para solucionar problemas pueden encontrarse en el artículo de [implementaciones de grupo de recursos de solución de problemas en Azure](./resource-manager-troubleshoot-deployments-powershell.md) .

  Desde el símbolo del sistema de PowerShell, escriba el comando siguiente para implementar recursos al grupo de recursos que ha creado para el entorno de prueba, reemplazar [path] con la ruta de acceso a los archivos que guardó en los pasos anteriores.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  Desde el símbolo del sistema de PowerShell, escriba el comando siguiente para implementar recursos al grupo de recursos que ha creado para el entorno de producción anterior, reemplazar [path] con la ruta de acceso a los archivos que guardó en los pasos anteriores.

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

Los archivos de plantilla y un parámetro pueden ser una versión y mantener con el código de la aplicación en un sistema de control de código fuente.  También puede guardar los comandos anteriores para archivos de comandos y guardarlos con el código.

> [AZURE.NOTE] Puede implementar la plantilla en Azure directamente haciendo clic en el botón "Implementar a Azure" en el artículo [aprovisionar una aplicación Web con una base de datos de SQL](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/) .  Esto puede resultar útil para obtener información sobre las plantillas, pero hacerlo no le permiten editar, versión y guardar los valores de plantilla y un parámetro con el código de la aplicación, por lo que en este artículo no se tratan los detalles adicionales sobre este método.

## <a name="maintain-environments"></a>Mantener entornos
Durante el desarrollo, la configuración de los recursos de Azure en diferentes entornos puede incoherente cambiarse deliberadamente o por accidente.  Esto puede provocar problemas innecesarios y resolución de problemas durante el ciclo de desarrollo de aplicaciones.

1. Cambiar los entornos, abra el [portal de Azure](https://portal.azure.com). 
2. Inicie sesión en él con la misma cuenta que utilizó para completar los pasos anteriores. 
3. Como se muestra en la imagen siguiente, haga clic en Examinar--grupos de recursos (que puede que deba desplazarse hacia abajo para ver los grupos de recursos).
   ![Portal](./media/solution-dev-test-environments/rgbrowse.png)
4. Después de hacer clic en grupos de recursos en la imagen anterior, verá el módulo de grupos de recursos y los grupos de tres recursos que creó en un paso anterior, como se muestra en la imagen siguiente. Haga clic en el grupo de recursos de desarrollo de TestApp1 y verá el módulo que muestra los recursos creados por la plantilla de la implementación de grupo de recursos de desarrollo de TestApp1 completado en un paso anterior.  Elimine el recurso TestApp1DevApp Online haciendo clic TestApp1DevApp en el módulo de grupo de recursos de desarrollo de TestApp1 y, a continuación, haga clic en Eliminar en el módulo de aplicación Web de TestApp1DevApp.
   ![Portal](./media/solution-dev-test-environments/portal2.png)
5. Haga clic en "Sí" cuando el portal le preguntará si está seguro de que desea eliminar el recurso. Cierre el módulo de grupo de recursos de desarrollo de TestApp1 y volver a abrirlo ahora se mostrarán sin la aplicación Web que acaba de eliminar.  El contenido del grupo de recursos ahora es diferente de lo que deberían. Aún más, puede experimentar eliminando varios recursos desde varios grupos de recursos o incluso cambiar la configuración de algunos de los recursos. En lugar de usar el portal de Azure para eliminar un recurso de un grupo de recursos, puede utilizar el comando PowerShell [Quitar AzureResource](https://msdn.microsoft.com/library/azure/dn757676.aspx) o el comando "eliminar recursos azure" desde la CLI para llevar a cabo la misma tarea o.
6. Para obtener todos los recursos y la configuración que se supone que la casilla de verificación de los grupos de recursos al estado deben estar en, volver a implementar los entornos a los grupos de recursos con los mismos comandos que usó en la sección de [recursos de implementación para entornos](#deploy-resources-to-environments) , pero reemplazar "Deployment1" con "Sensores2."
7.  Como se muestra en la sección de resumen del módulo de desarrollo de TestApp1 en la imagen que se muestra en el paso 4, verá que la aplicación Web eliminados en el portal en el paso anterior persiste, igual que cualquier otro recurso puede eliminar. Si cambia la configuración de cualquiera de los recursos, también puede comprobar que se han configurado volver a los valores en los archivos de parámetro demasiado. Una de las ventajas de implementar los entornos con las plantillas de administrador de recursos de Azure es que puede volver a implementar fácilmente los entornos volver a un estado conocido en cualquier momento.
8. Si hace clic en el texto situado debajo de "Última implementación" en la siguiente imagen, verá un módulo que muestra el historial de implementación del grupo de recursos.  Dado que ha usado el nombre "Deployment1" para la primera implementación y "Sensores2" para la segunda implementación, tendrá dos entradas.  Al hacer clic en una implementación mostrará un módulo que muestra los resultados de cada implementación.
  ![Portal](./media/solution-dev-test-environments/portal3.png)



## <a name="delete-environments"></a>Eliminar entornos
Una vez que haya terminado con un entorno, desee eliminarlo por lo que no tenga cargos de uso de recursos de Azure que ya no está usando.  Eliminar entornos es más fácil que crearlos.  En los pasos anteriores, se crearon grupos de recursos de Azure individuales para cada entorno y, a continuación, se implementaron los recursos en los grupos de recursos. 

Eliminar los entornos con cualquiera de los métodos siguientes.  Todos los métodos conseguir el mismo resultado.

### <a name="azure-cli"></a>CLI de Azure

Desde un indicador CLI, escriba lo siguiente:

    azure group delete "TestApp1-Development"

Cuando se le solicite, escriba y y, a continuación, presione ENTRAR para quitar el entorno de desarrollo y todos sus recursos. Después de unos minutos, el comando devolverá lo siguiente:

    info:    group delete command OK

Desde un indicador CLI, escriba lo siguiente para eliminar los entornos restantes:

    azure group delete "TestApp1-Test"
    azure group delete "TestApp1-Pre-Production"
  
### <a name="powershell"></a>PowerShell

Desde un símbolo del sistema de Azure PowerShell (versión 1.01 o superior), escriba el comando siguiente para eliminar el grupo de recursos y todo su contenido.    

    Remove-AzureRmResourceGroup -Name TestApp1-Development

Cuando se le solicite si está seguro de que desea quitar el grupo de recursos, escriba y, seguido de la tecla ENTRAR.

Escriba lo siguiente para eliminar los entornos restantes:

    Remove-AzureRmResourceGroup -Name TestApp1-Test
    Remove-AzureRmResourceGroup -Name TestApp1-Pre-Production

### <a name="azure-portal"></a>Portal de Azure

1. En el portal de Azure, vaya a grupos de recursos como hizo en los pasos anteriores. 
2. Seleccione el grupo de recursos de desarrollo de TestApp1 y, a continuación, haga clic en Eliminar en el módulo de grupo de recursos de desarrollo TestApp1. Aparecerá un nuevo módulo. Escriba el nombre del grupo de recursos y haga clic en el botón Eliminar.
![Portal](./media/solution-dev-test-environments/rgdelete.png)
3. Del mismo modo que se eliminó el grupo de recursos de desarrollo de TestApp1 de grupos de recursos de TestApp1-Pre-producción y eliminar la prueba de TestApp1.

Independientemente del método que utilice, los grupos de recursos y todos los recursos que contenían dejarán de existir y ya no tendrás que pagar gastos de facturación para los recursos.  

Para reducir la Azure gastos de utilización de recursos se produce durante el desarrollo de aplicaciones, puede usar [automatización Azure](automation/automation-intro.md) para programar trabajos que:

- Detener máquinas virtuales al final de cada día y reinicie ellos al comienzo de cada día.
- Eliminar todos entornos al final de cada día y volver a crearlos al comienzo de cada día.
 
Ahora que ha experimentado lo fácil que es crear, mantener y eliminar desarrollo y probar entornos, puede obtener más sobre lo que solo se produjo más experimentar con los pasos anteriores y leer las referencias de este artículo.

## <a name="next-steps"></a>Pasos siguientes

- [Delegar control administrativo](./active-directory/role-based-access-control-configure.md) para recursos diferentes en cada entorno asignando Microsoft Azure AD grupos o usuarios a funciones específicas que tienen la capacidad de realizar un subconjunto de las operaciones de recursos de Azure.
- [Asignar etiquetas](resource-group-using-tags.md) a los grupos de recursos para cada entorno o de los recursos individuales. Puede agregar una etiqueta de "Entorno" a los grupos de recursos y establezca su valor en corresponden a los nombres de su entorno. Las etiquetas puede resultar útiles cuando necesite organizar los recursos de facturación o de administración.
- Supervisar las alertas y facturación para los recursos del grupo de recursos en el [portal de Azure](https://portal.azure.com).
