## <a name="download-and-understand-the-arm-template"></a>Descargar y comprender la plantilla ARM

Puede descargar la plantilla ARM existente para crear un VNet y dos subredes desde github, realice los cambios que desee y volver a usarla. Para ello, siga los pasos siguientes.

1. Vaya a [la página de plantilla de ejemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
2. Haga clic en **azuredeploy.json**y, a continuación, haga clic en **materia**.
3. Guarde el archivo a un una carpeta local en el equipo.
4. Si está familiarizado con las plantillas ARM, vaya al paso 7.
5. Abra el archivo que ha guardado y ver el contenido en los **parámetros** en la línea 5. Parámetros de plantilla ARM proporcionan un marcador de posición para los valores que se pueden rellenar durante la implementación.

    | Parámetro | Descripción |
    |---|---|
    | **ubicación** | Azure región donde se creará el VNet |
    | **vnetName** | Nombre para el nuevo VNet |
    | **addressPrefix** | Espacio de direcciones para VNet, en formato CIDR |
    | **subnet1Name** | Nombre para el primer VNet |
    | **subnet1Prefix** | Bloque CIDR para la primera subred |
    | **subnet2Name** | Nombre para la segunda VNet |
    | **subnet2Prefix** | Bloque CIDR para la segunda subred |

    >[AZURE.IMPORTANT] Plantillas ARM mantenidas en github pueden cambiar con el tiempo. Asegúrese de la plantilla antes de usarla.
    
6. Compruebe el contenido en **recursos** y observe lo siguiente:

    - **tipo**. Tipo de recurso se ha creado por la plantilla. En este caso, **Microsoft.Network/virtualNetworks**, que representan una VNet.
    - **nombre**. Nombre para el recurso. Observe el uso de **[parameters('vnetName')]**, lo que significa que tendrá el nombre proporcionado como entrada por el usuario o un archivo de parámetro durante la implementación.
    - **Propiedades**. Lista de propiedades para el recurso. Esta plantilla utiliza las propiedades de subred y de espacio de direcciones durante la creación de VNet.

7. Vaya a [la página de plantilla de ejemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
8. Haga clic en **azuredeploy paremeters.json**y, a continuación, haga clic en **materia**.
9. Guarde el archivo a un una carpeta local en el equipo.
10. Abra el archivo que acaba de guardar y editar los valores de los parámetros. Use los siguientes valores para implementar el VNet descrito en este escenario.

        {
          "location": {
            "value": "Central US"
          },
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }

11. Guarde el archivo.
  