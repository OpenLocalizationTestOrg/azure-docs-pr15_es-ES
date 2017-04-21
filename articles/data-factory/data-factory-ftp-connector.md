<properties 
    pageTitle="Mover los datos de servidor FTP | Microsoft Azure" 
    description="Obtenga información sobre cómo mover los datos de un servidor FTP mediante el generador de datos de Azure." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/20/2016" 
    ms.author="spelluru"/>

# <a name="move-data-from-an-ftp-server-using-azure-data-factory"></a>Mover los datos de un servidor FTP mediante el generador de datos de Azure
Este artículo describe cómo usar la actividad de copia en el generador de datos de Azure para mover datos de un servidor FTP a un almacén de datos compatibles receptor. En este artículo se basa en el artículo de [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una visión general de movimiento de datos con la actividad de copia y la lista de almacenamiento de datos compatibles como orígenes y receptores. 

Generador de datos admite actualmente solo mover los datos de un servidor FTP a otro almacena datos, pero no para mover datos desde otro almacena datos en un servidor FTP. Es compatible con ambos local y la nube de servidores FTP. 

Si está moviendo datos de un servidor **local** FTP a un almacén de datos de nube (ejemplo: el almacenamiento de blobs de Windows Azure), instalar y usar Data Management Gateway. Data Management Gateway es un agente de cliente que esté instalado en su equipo local, que permite a los servicios de nube para conectarse a los recursos locales. Vea [Data Management Gateway](data-factory-data-management-gateway.md) para obtener detalles sobre la puerta de enlace. Vea el artículo de [mover datos entre ubicaciones local y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener instrucciones detalladas sobre cómo configurar la puerta de enlace y usarlo. Usar la puerta de enlace para conectarse a un servidor FTP incluso si el servidor no está en una máquina virtual de Azure IaaS (VM). 

Puede instalar la puerta de enlace en el mismo equipo local o en la máquina virtual IaaS de Azure como el servidor FTP. Sin embargo, le recomendamos que instale la puerta de enlace en un equipo distinto o una máquina virtual independiente IaaS de Azure para evitar conflictos de recursos y para mejorar el rendimiento. Cuando se instala la puerta de enlace en otro equipo, el equipo podrá tener acceso al servidor FTP. 

## <a name="copy-data-wizard"></a>Asistente de datos de copia
La manera más sencilla de crear una canalización que copie los datos de un servidor FTP es usar al Asistente de datos de copia. Consulte [Tutorial: crear una tubería mediante el Asistente para copiar](data-factory-copy-data-wizard-tutorial.md) para obtener un tutorial rápido sobre la creación de una canalización mediante el Asistente de datos de copia. 

Los ejemplos siguientes proporcionan definiciones de JSON de ejemplo que puede usar para crear una canalización mediante el [portal de Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [PowerShell de Azure](data-factory-copy-activity-tutorial-using-powershell.md). 

## <a name="sample-copy-data-from-ftp-server-to-azure-blob"></a>Ejemplo: Copiar datos de servidor FTP a blobs de Windows Azure

En este ejemplo se muestra cómo copiar datos desde un servidor FTP a un almacenamiento de blobs de Windows Azure. Sin embargo, los datos pueden ser copiado **directamente** a cualquiera de los receptores indicada [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en el generador de datos de Azure.  
 
El ejemplo tiene las siguientes entidades de fábrica de datos:

- Servicio de tipo [FtpServer](#ftp-linked-service-properties)vinculado.
- Servicio de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)vinculado.
- Un [conjunto de datos](data-factory-create-datasets.md) a la entrada del tipo de [recurso compartido de archivos](#fileshare-dataset-type-properties).
- Un resultado [conjunto de datos](data-factory-create-datasets.md) de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- [Canalización](data-factory-create-pipelines.md) con actividad copia que utiliza [FileSystemSource](#ftp-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

El ejemplo copia datos de un servidor FTP a un blobs de Windows Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en secciones siguiendo los ejemplos. 

**FTP vinculado servicio** Este ejemplo usa la autenticación básica con el nombre de usuario y contraseña en texto sin formato. También puede usar una de las siguientes maneras: 

- Autenticación anónima 
- Autenticación básica con credenciales cifradas
- FTP sobre SSL/TLS (FTP)

Vea la sección [FTP vinculado servicio](#ftp-linked-service-properties) para distintos tipos de autenticación que se puede usar. 

    {
        "name": "FTPLinkedService",
        "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",          
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
      }
    }

**Servicio de almacenamiento vinculado de Azure**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Conjunto de datos de entrada de FTP** Este conjunto de datos hace referencia a la carpeta FTP `mysharedfolder` y archivo `test.csv`. La canalización de copia del archivo al destino. 

Establecer "externo": "true" informa de servicio del generador de datos que el conjunto de datos, el generador de datos externo y no se produce en una actividad en el generador de datos.
    
    {
      "name": "FTPFileInput",
      "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
          "folderPath": "mysharedfolder",
          "fileName": "test.csv",
          "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }


**Conjunto de datos de salida de blobs de Windows Azure**

Se escriben datos en un nuevo blob cada hora (frecuencia: horas, intervalo: 1). La ruta de acceso para el blob se evalúa dinámicamente en función de la hora de inicio de la segmentación de datos que se está procesando. La ruta de acceso de la carpeta usa el año, mes, día y partes de horas de la hora de inicio.

    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
                "partitionedBy": [
                    {
                        "name": "Year",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy"
                        }
                    },
                    {
                        "name": "Month",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "MM"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "dd"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**Canalización con actividad de copia**

La canalización contiene una actividad de la copia que está configurado para usar los conjuntos de datos de entrada y salida y se ha programado para que se ejecute cada hora. En la canalización de definición de JSON, el tipo de **origen** se establece en **FileSystemSource** y tipo de **receptor** se establece en **BlobSink**. 
    
    {
        "name": "pipeline",
        "properties": {
            "activities": [{
                "name": "FTPToBlobCopy",
                "inputs": [{
                    "name": "FtpFileInput"
                }],
                "outputs": [{
                    "name": "AzureBlobOutput"
                }],
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }],
            "start": "2016-08-24T18:00:00Z",
            "end": "2016-08-24T19:00:00Z"
        }
    }

## <a name="ftp-linked-service-properties"></a>Propiedades del sitio vinculado FTP

La tabla siguiente proporciona la descripción de los elementos de JSON específicos FTP servicios vinculados.

| (Propiedad) | Descripción | Obligatorio | Predeterminado |
| -------- | ----------- | -------- | ------- | 
| tipo | La propiedad tipo debe estar establecida en FtpServer | Sí | &nbsp;
| host | Nombre o dirección IP del servidor FTP | Sí | &nbsp;
| authenticationType | Especifique el tipo de autenticación | Sí | Básico, anónimo |
| nombre de usuario | Usuario que tiene acceso al servidor FTP | No | &nbsp;
| contraseña | Contraseña del usuario (nombre de usuario) | No | &nbsp;
| encryptedCredential | Cifrado credenciales para acceder al servidor FTP | No | &nbsp;
| gatewayName | Nombre de la puerta de enlace de Data Management Gateway para conectarse a un servidor FTP local | No    | &nbsp;
| puerto | Puerto en el que está escuchando el servidor FTP | No | 21 |
| enableSsl | Especifique si desea utilizar FTP sobre el canal SSL/TLS | No | Verdadero | 
| enableServerCertificateValidation | Especifique si desea habilitar la validación de certificado SSL de servidor cuando se usa FTP sobre el canal SSL/TLS | No | Verdadero | 

### <a name="using-anonymous-authentication"></a>Uso de autenticación anónima

    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {     
                "authenticationType": "Anonymous",
                "host": "myftpserver.com"
            }
        }
    }

### <a name="using-username-and-password-in-plain-text-for-basic-authentication"></a>Usar nombre de usuario y contraseña en texto sin formato para la autenticación básica
    
    {
        "name": "FTPLinkedService",
        "properties": {
        "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",
                "username": "Admin",
                "password": "123456"
            }
        }
    }


### <a name="using-port-enablessl-enableservercertificatevalidation"></a>Usar el puerto, enableSsl, enableServerCertificateValidation

    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",  
                "username": "Admin",
                "password": "123456",
                "port": "21",
                "enableSsl": true,
                "enableServerCertificateValidation": true
            }
        }
    }

### <a name="using-encryptedcredential-for-authentication-and-gateway"></a>Usar encryptedCredential para la autenticación y la puerta de enlace

    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",
                "encryptedCredential": "xxxxxxxxxxxxxxxxx",
                "gatewayName": "mygateway"
            }
        }
    }

Para obtener más información sobre cómo establecer credenciales para un origen de datos FTP local, vea [establecer credenciales y seguridad](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .

[AZURE.INCLUDE [data-factory-file-share-dataset](../../includes/data-factory-file-share-dataset.md)]   
[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]   
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="ftp-copy-activity-type-properties"></a>Propiedades de tipo de actividad de copia de FTP

Para obtener una lista completa de secciones y propiedades disponibles para definir actividades, vea el artículo de la [Creación de canalizaciones](data-factory-create-pipelines.md) . Propiedades como nombre, descripción, entrada y salida tablas y directivas están disponibles para todos los tipos de actividades. 

Propiedades disponibles en la sección typeProperties de la actividad por otro lado varían en función de cada tipo de actividad. Actividad de copiar las propiedades de tipo varían según los tipos de orígenes y receptores.

[AZURE.INCLUDE [data-factory-file-system-source](../../includes/data-factory-file-system-source.md)]   

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Rendimiento y optimización  
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para obtener información acerca de los factores clave que afectan al rendimiento de movimiento de datos (copiar actividad) en el generador de datos de Azure y distintas formas de optimizar.

## <a name="next-steps"></a>Pasos siguientes
Consulte los artículos siguientes: 

- [Tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para crear una canalización con una actividad de copia. 
