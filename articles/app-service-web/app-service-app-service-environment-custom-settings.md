<properties
    pageTitle="Configuración personalizada para entornos de aplicación de servicio"
    description="Valores de configuración personalizada para entornos de aplicación de servicio"
    services="app-service"
    documentationCenter=""
    authors="stefsch"
    manager="nirma"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="stefsch"/>

# <a name="custom-configuration-settings-for-app-service-environments"></a>Valores de configuración personalizada para entornos de aplicación de servicio

## <a name="overview"></a>Información general ##
Porque los entornos de aplicación de servicio están aislados en un solo cliente, hay ciertas opciones de configuración que se pueden aplicar exclusivamente a entornos de aplicación de servicio. En este artículo se documenta la distintos personalizaciones específicas que están disponibles para los entornos de aplicación de servicio.

Si no tiene un entorno de servicio de aplicación, vea [cómo crear un entorno de servicio de aplicación](app-service-web-how-to-create-an-app-service-environment.md).

Puede almacenar personalizaciones del entorno de servicio de aplicación mediante una matriz en el atributo **clusterSettings** nuevo. Este atributo se encuentra en el diccionario de "propiedades de" de la entidad del Administrador de recursos de Azure *hostingEnvironments* .

El siguiente fragmento de código de la plantilla Administrador de recursos abreviada muestra el atributo **clusterSettings** :


    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

El atributo **clusterSettings** puede incluirse en una plantilla de administrador de recursos para actualizar el entorno de servicios de aplicación.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Utilice el Explorador de recursos de Azure para actualizar un entorno de servicio de aplicación
Como alternativa, puede actualizar el entorno de servicios de aplicación mediante el [Explorador de recursos de Azure](https://resources.azure.com).  

1. En el Explorador de recursos, vaya al nodo para el entorno de aplicación de servicio (**suscripciones** > **resourceGroups** > **proveedores** > **Micrososft.Web** > **hostingEnvironments**). A continuación, haga clic en el entorno de servicio de aplicación específico que desea actualizar.

2. En el panel derecho, haga clic en **Lectura y escritura** en la barra de herramientas superior para permitir interactiva de edición en el Explorador de recursos.  

3. Haga clic en azul **Editar** botón para poder modificar la plantilla de administrador de recursos.

4. Desplácese hasta la parte inferior del panel derecho. El atributo **clusterSettings** está en la parte inferior, donde puede especificar o actualizar su valor.

5. Escriba (o copie y pegue) la matriz de valores de configuración que desee en el atributo **clusterSettings** .  

6. Haga clic en el color verde **poner** botón que se encuentra en la parte superior del panel derecho para confirmar el cambio en el entorno de servicios de aplicación.

Sin embargo, enviar el cambio, tarda aproximadamente 30 minutos multiplicados por el número de front-end en el entorno de servicios de aplicación para que el cambio surta efecto.
Por ejemplo, si un entorno de servicio de aplicación tiene cuatro front-end, esta acción tardará aproximadamente dos horas para que finalice la actualización de la configuración. Mientras se está ejecutando el cambio de configuración, ningún otro operaciones de escala o las operaciones de cambio de configuración se pueden realizar en el entorno de servicios de aplicación.

## <a name="disable-tls-10"></a>Deshabilitar TLS 1.0 ##
Una pregunta periódica de los clientes, especialmente los clientes que están trabajando con el cumplimiento de PCI auditorías, es cómo deshabilitar explícitamente TLS 1.0 para sus aplicaciones.

TLS 1.0 puede deshabilitarse a través de la entrada de **clusterSettings** siguiente:

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

## <a name="change-tls-cipher-suite-order"></a>Cambiar orden de conjunto de cifrado TLS ##
Otra pregunta de los clientes es si puede modificar la lista de cifrados negociada por su servidor y ello modificando la **clusterSettings** tal como se muestra a continuación. Se puede recuperar la lista de conjuntos de cifrado disponibles desde [en este artículo MSDN] (https://msdn.microsoft.com/library/windows/desktop/aa374757(v=vs.85\).aspx).

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [AZURE.WARNING]  Si se establecen valores incorrectos para el conjunto de cifrado que no comprende SChannel, todas las comunicaciones de TLS en su servidor podrían deje de funcionar. En este caso, debe quitar la entrada de *FrontEndSSLCipherSuiteOrder* de **clusterSettings** y envíe la plantilla actualizada del Administrador de recursos para volver a la configuración predeterminada del conjunto de aplicaciones de cifrado.  Utilice esta funcionalidad con cuidado.

## <a name="get-started"></a>Introducción
La plantilla de sitio de administrador de recursos de Azure tutorial incluye una plantilla con la definición base para [crear un entorno de servicio de aplicación](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).


<!-- LINKS -->

<!-- IMAGES -->
