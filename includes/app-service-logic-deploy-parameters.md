Con el Administrador de recursos de Azure definir parámetros para los valores que desea especificar cuando se implementa la plantilla. La plantilla incluye una sección denominada parámetros que contiene todos los valores de parámetro.
Debe definir un parámetro para los valores que varían en función del proyecto que está implementando o basado en el entorno de que implementación. Debe definir parámetros para los valores que se mantienen siempre el mismo. Cada valor de parámetro se utiliza en la plantilla para definir los recursos que se implementan. 

Al definir parámetros, use el campo **allowedValues** para especificar los valores que un usuario puede proporcionar durante la implementación. Use el campo **defaultValue** para asignar el valor para el parámetro, si no se proporciona ningún valor durante la implementación.

Describiremos cada parámetro de la plantilla.

### <a name="logicappname"></a>logicAppName

El nombre de la aplicación de la lógica para crear.

    "logicAppName": {
        "type": "string"
    }