### <a name="compression-support"></a>Compatibilidad con la compresión  
Procesamiento de grandes conjuntos de datos puede provocar botella de red e i/OS. Por lo tanto, los datos comprimidos en stores pueden no sólo acelerar la transferencia de datos a través de la red y ahorrar espacio en disco, pero también Traer importantes mejoras de rendimiento en el procesamiento de datos grandes. Actualmente, compresión es compatible con datos de archivo almacena como blobs de Windows Azure o sistema de archivos local.  

> [AZURE.NOTE] Configuración de compresión no es compatibles para los datos de la **AvroFormat**, **OrcFormat**o **ParquetFormat**. 

Para especificar la compresión para un conjunto de datos, utilice la propiedad de **compresión** en el conjunto de datos JSON como en el ejemplo siguiente:   

    {  
        "name": "AzureBlobDataSet",  
        "properties": {  
            "availability": {  
                "frequency": "Day",  
                "interval": 1  
            },  
            "type": "AzureBlob",  
            "linkedServiceName": "StorageLinkedService",  
            "typeProperties": {  
                "fileName": "pagecounts.csv.gz",  
                "folderPath": "compression/file/",  
                "compression": {  
                    "type": "GZip",  
                    "level": "Optimal"  
                }  
            }  
        }  
    }  
 
La sección **compresión** tiene dos propiedades:  
  
- **Tipo:** el códec de compresión, que puede ser **GZIP**, **Deflate** o **BZIP2**.  
- **Nivel:** la razón de compresión, que puede ser **óptima** o **más rápido**. 
    - **Más rápida:** La operación de compresión debe completar lo antes posible, incluso si el archivo resultante no se comprime de manera óptima. 
    - **Óptima**: la operación de compresión se debe óptima comprimir, incluso si la operación tarda más tiempo en completarse. 
    
    Para obtener más información, vea el tema de [Nivel de compresión](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . 

Supongamos que el conjunto de datos de ejemplo anterior se usa como el resultado de una actividad de copia, la actividad de copia comprime los datos de salida con códec GZIP con relación óptima y, a continuación, escribir los datos comprimidos en un archivo llamado pagecounts.csv.gz en el almacenamiento de blobs de Windows Azure.   

Cuando se especifica la propiedad de compresión en un conjunto de datos de entrada JSON, la canalización puede leer los datos comprimidos desde el origen y cuando se especifica la propiedad de un conjunto de datos de salida JSON, la actividad de copia puede escribir los datos comprimidos al destino. Estos son algunos escenarios de ejemplo: 

- Datos de GZIP lectura comprimido de blobs de Azure, descomprimir el archivo y escribir datos de resultado en una base de datos de SQL Azure. Definir el conjunto de datos de entrada blobs de Windows Azure con la compresión de propiedad JSON en este caso. 
- Leer datos desde un archivo de texto sin formato del sistema de archivos local, comprimir con formato GZip y escribir los datos comprimidos en un blobs de Windows Azure. Definir un conjunto de datos de blobs de Windows Azure de salida con la compresión de propiedad JSON en este caso.  
- Leer datos comprimidos GZIP desde un blobs de Windows Azure, descomprimir el archivo, comprimirlo mediante BZIP2 y escribir datos de resultado en una blobs de Windows Azure. Definir el conjunto de datos de blobs de Windows Azure entrada con el tipo de compresión establecido en GZIP y el conjunto de datos de salida con el tipo de compresión establecido BZIP2 en este caso.   
