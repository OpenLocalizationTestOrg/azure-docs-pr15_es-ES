Con el Administrador de recursos de Azure definir parámetros para los valores que desea especificar cuando se implementa la plantilla. La plantilla incluye una sección denominada parámetros que contiene todos los valores de parámetro.
Debe definir un parámetro para los valores que varían en función del proyecto que está implementando o basado en el entorno de que implementación. Debe definir parámetros para los valores que se mantienen siempre el mismo. Cada valor de parámetro se utiliza en la plantilla para definir los recursos que se implementan. 

Al definir parámetros, use el campo **allowedValues** para especificar los valores que un usuario puede proporcionar durante la implementación. Use el campo **defaultValue** para asignar el valor para el parámetro, si no se proporciona ningún valor durante la implementación.

Describiremos cada parámetro de la plantilla.

### <a name="sitename"></a>nombre del sitio

El nombre de la aplicación web que desea crear.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName

El nombre del plan de servicio de aplicación para la aplicación web de hospedaje.
    
    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>SKU

Nivel de precios para el plan de hospedaje.

    "sku": {
      "type": "string",
      "allowedValues": [
        "F1",
        "D1",
        "B1",
        "B2",
        "B3",
        "S1",
        "S2",
        "S3",
        "P1",
        "P2",
        "P3",
        "P4"
      ],
      "defaultValue": "S1",
      "metadata": {
        "description": "The pricing tier for the hosting plan."
      }
    }

La plantilla define los valores que están permitidos para este parámetro y asigna un valor predeterminado (S1) si no se especifica ningún valor.

### <a name="workersize"></a>workerSize

El tamaño de la instancia del plan de hospedaje (pequeño, mediano o grande).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }
    
La plantilla define los valores que están permitidos para este parámetro (0, 1 o 2) y asigna un valor predeterminado (0) si no se especifica ningún valor. Los valores corresponden a pequeña, medianos y grandes.
