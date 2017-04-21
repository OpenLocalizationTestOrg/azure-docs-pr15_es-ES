

Este artículo le muestra cómo implementar una escala de máquina Virtual Azure establecer mediante una implementación de grupo de recursos de Visual Studio.


[Conjuntos de escala de máquina Virtual de Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) son un recurso de Azure calcular a implementar y administrar una colección de similar máquinas virtuales con opciones fácilmente integrados para ajustar y equilibrio de carga. Puede aprovisionar e implementar conjuntos de escala de VM con [plantillas de Azure Resource Manager (ARM)](https://github.com/Azure/azure-quickstart-templates). Puede implementar plantillas ARM con el resto de Azure CLI, PowerShell y también directamente desde Visual Studio. Visual Studio proporciona un conjunto de plantillas que se pueden implementar como parte de un proyecto de implementación de grupo de recursos de Azure de ejemplo.

Implementaciones de grupo de recursos de Azure son una forma de agrupar y publicar un conjunto de recursos relacionados de Azure en una operación de implementación única. Puede obtener más información sobre ellos aquí: [crear e implementar grupos de recursos de Azure a través de Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy/).

## <a name="pre-requisites"></a>Requisitos previos

Para empezar a implementar conjuntos de escala de VM en Visual Studio se necesita lo siguiente:

- 2013 de Visual Studio o de 2015
- Azure SDK 2.7 o 2,8

Nota: En estas instrucciones se supone que está utilizando 2015 de Visual Studio con [Azure SDK 2,8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="creating-a-project"></a>Crear un proyecto

1. Crear un nuevo proyecto de Visual Studio de 2015 eligiendo **archivo | Nuevo | Proyecto**.

    ![Archivo nuevo][file_new]

2. En **Visual C# | Nube**, elija **Administrador de recursos de Azure** para crear un proyecto para implementar una plantilla de ARM.

    ![Crear proyecto][create_project]

3.  En la lista de plantillas, seleccione la Linux o una plantilla de conjunto de escala de máquina Virtual de Windows.

    ![Seleccione la plantilla][select_Template]

4. Una vez creado el proyecto verá secuencias de comandos de PowerShell implementación, una plantilla de administrador de recursos de Azure y un archivo de parámetros para el conjunto de escala de máquina Virtual.

    ![Explorador de soluciones][solution_explorer]

## <a name="customize-your-project"></a>Personalizar el proyecto

Ahora puede editar la plantilla para personalizar para las necesidades de la aplicación, como agregar propiedades de extensión VM o editar las reglas de equilibrio de carga. De forma predeterminada se configuran plantillas de conjunto de VM escala para implementar la extensión de AzureDiagnostics que hace que sea fácil agregar reglas de Autoescala. También se implementa un equilibrador de carga con una dirección IP pública, configurado con las reglas de entrada NAT que permiten conectarse a las instancias VM con SSH (Linux) o RDP (Windows): el intervalo de puertos front-end comienza en 50000, lo que significa en el caso de Linux, si se SSH al puerto 50000 de la dirección IP pública (o el nombre de dominio) se dirige al puerto 22 de primera VM en el conjunto de escala. Conectar al puerto 50001 redirigirá al puerto 22 de la máquina virtual de segundo y así sucesivamente.

 Es una buena forma de modificar las plantillas de Visual Studio utilizar el esquema de JSON para organizar los parámetros, variables y recursos. Con una descripción del esquema de Visual Studio puede indicar errores en la plantilla antes de implementarlo.

![Explorador de JSON][json_explorer]

## <a name="deploy-the-project"></a>Implementar el proyecto

6. Implementar la plantilla de ARM en Azure para crear el recurso conjunto de escala de máquina virtual. Haga clic con el botón secundario en el nodo del proyecto, elija **implementar | Nueva implementación**.

    ![Implementar plantilla][5deploy_Template]

7. Seleccione la suscripción en el cuadro de diálogo "Implementar al grupo de recursos".

    ![Implementar plantilla][6deploy_Template]

8. Desde aquí también puede crear un nuevo grupo de recursos de Azure para implementar la plantilla que desea.

    ![Nuevo grupo de recursos][new_resource]

9. A continuación, seleccione el botón **Editar parámetros** para especificar los parámetros que se pasan a la plantilla, determinados valores como el nombre de usuario y contraseña para el sistema operativo son necesarios para crear la implementación.

    ![Editar parámetros][edit_parameters]

10. Ahora haga clic en **implementar**. La ventana de **resultados** mostrará el progreso de la implementación. Tenga en cuenta que la la acción está ejecutando el script de **Implementación AzureResourceGroup.ps1** .

    ![Ventana de resultados][output_window]

## <a name="exploring-your-vm-scale-set"></a>Explorar el conjunto de escala VM

Una vez finalizada la implementación, puede ver el nuevo conjunto de escala de VM en el **Explorador de nube** de Visual Studio (actualización de la lista). Explorer nube le permite administrar recursos de Azure en Visual Studio al desarrollo de aplicaciones. También puede ver el conjunto de escala de VM en el Portal de Azure y el Explorador de recursos de Azure.

![Explorador de nube][cloud_explorer]

 El portal proporciona la mejor manera de administrar su infraestructura de Azure con un explorador web, mientras el Explorador de recursos de Azure proporciona una forma sencilla de explorador visualmente y depurar Azure recursos, que le da una ventana en la vista de instancia de"" y que también muestra los comandos de PowerShell para los recursos que está viendo. Aunque son conjuntos de escala de VM en vista previa, el Explorador de recursos mostrará más información para los conjuntos de escala de la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes

Una vez que ha implementado correctamente conjuntos de escala de máquina virtual a través de Visual Studio puede personalizar aún más su proyecto para adaptarla a sus requisitos de la aplicación. Por ejemplo configurando Autoescala agregando un recurso perspectivas, agregando infraestructura a su plantilla como independiente máquinas virtuales o implementación de aplicaciones mediante la extensión de scripts personalizados. Una buena fuente de plantillas de ejemplo puede encontrarse en el repositorio de GitHub de [Azure tutorial plantillas](https://github.com/Azure/azure-quickstart-templates) (busque "vmss").

[file_new]: ./media/virtual-machines-common-scale-sets-visual-studio/1-FileNew.png
[create_project]: ./media/virtual-machines-common-scale-sets-visual-studio/2-CreateProject.png
[select_Template]: ./media/virtual-machines-common-scale-sets-visual-studio/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machines-common-scale-sets-visual-studio/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machines-common-scale-sets-visual-studio/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machines-common-scale-sets-visual-studio/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machines-common-scale-sets-visual-studio/6-DeployTemplate.png
[new_resource]: ./media/virtual-machines-common-scale-sets-visual-studio/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machines-common-scale-sets-visual-studio/8-EditParameter.png
[output_window]: ./media/virtual-machines-common-scale-sets-visual-studio/9-Output.png
[cloud_explorer]: ./media/virtual-machines-common-scale-sets-visual-studio/12-CloudExplorer.png