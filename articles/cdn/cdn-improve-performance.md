<properties
    pageTitle="Mejorar el rendimiento al comprimir los archivos en Azure CDN | Microsoft Azure"
    description="Obtenga información sobre cómo mejorar la velocidad de transferencia de archivos y aumenta el rendimiento de carga de página con la compresión de archivos en Azure CDN."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>

# <a name="improve-performance-by-compressing-files"></a>Mejorar el rendimiento con la compresión de archivos

La compresión es un método sencillo y eficaz para mejorar la velocidad de transferencia de archivos y mejorar el rendimiento de carga de página reduciendo el tamaño de archivo antes de enviarlo desde el servidor. Reduce el ancho de banda y ofrece una experiencia de mayor capacidad de respuesta para los usuarios.

Hay dos formas de habilitar la compresión:

- Puede habilitar la compresión en el servidor de origen, en cuyo caso la CDN se pase a través de los archivos comprimidos y entregar archivos comprimidos a los clientes que soliciten.
- Puede habilitar la compresión directamente en los servidores perimetrales CDN, en cuyo caso la CDN se comprimir los archivos y servir a los usuarios finales, incluso si el servidor de origen no los comprime.

> [AZURE.IMPORTANT] Cambios de configuración de CDN tardan algún tiempo en propagarse a través de la red.  Perfiles de <b>Azure CDN de Akamai</b> propagación normalmente se completa en menos de un minuto.  Perfiles de <b>Azure CDN de Verizon</b> , normalmente verá los cambios aplicar en 90 minutos.  Si esta es la primera vez que se ha configurado la compresión para el extremo CDN, considere la posibilidad de esperando horas 1 y 2 para asegurarse la compresión configuración se propaga a los POP antes de solución de problemas

## <a name="enabling-compression"></a>Habilitar la compresión

> [AZURE.NOTE] Los niveles estándar y Premium CDN proporcionan la misma funcionalidad de compresión, pero difiere de la interfaz de usuario.  Para obtener más información acerca de las diferencias entre niveles estándar y Premium CDN, vea [Información general de CDN de Azure](cdn-overview.md).

### <a name="standard-tier"></a>Nivel estándar

> [AZURE.NOTE] En esta sección se aplica a los perfiles de ** **Azure CDN de Verizon Azure CDN estándar** y de Akamai** .

1. En el módulo de perfil CDN, haga clic en el extremo CDN que desea administrar.

    ![Extremos de módulos del perfil CDN](./media/cdn-file-compression/cdn-endpoints.png)

    Se abre el módulo de extremo CDN.

2. Haga clic en el botón **Configurar** .

    ![Módulo de perfil CDN botón Administrar](./media/cdn-file-compression/cdn-config-btn.png)

    Se abre el módulo de configuración de CDN.

3. Activar la **compresión**.

    ![Opciones de compresión de CDN](./media/cdn-file-compression/cdn-compress-standard.png)

4. Utilizar los tipos de forma predeterminada, o modificar la lista quitando o agregando tipos de archivo.
    
    > [AZURE.TIP] Mientras sea posible, no se recomienda aplicar compresión a comprimido formatos, como ZIP, MP3, MP4, JPG, etcetera.
    
5. Después de realizar los cambios, haga clic en el botón **Guardar** .

### <a name="premium-tier"></a>Nivel de Premium

> [AZURE.NOTE] En esta sección se aplica a los perfiles de **Azure CDN Premium de Verizon** .

1. En el módulo de perfil CDN, haga clic en el botón **Administrar** .

    ![Módulo de perfil CDN botón Administrar](./media/cdn-file-compression/cdn-manage-btn.png)

    Se abre el portal de administración de CDN.

2. Desplace el puntero sobre la pestaña **HTTP grandes** , a continuación, mantenga el mouse sobre el elemento de **Configuración de la caché** .  Haga clic en la **compresión**.

    Se muestran las opciones de compresión.

    ![Compresión de archivos](./media/cdn-file-compression/cdn-compress-files.png)

3. Habilitar la compresión haciendo clic en el botón de opción **Habilita la compresión** .  Escriba los tipos MIME que desea comprimir como una lista delimitada por comas (sin espacios) en el cuadro de texto de **Los tipos de archivo** .
        
    > [AZURE.TIP] Mientras sea posible, no se recomienda aplicar compresión a comprimido formatos, como ZIP, MP3, MP4, JPG, etcetera. 

4. Después de realizar los cambios, haga clic en el botón **Actualizar** .


## <a name="compression-rules"></a>Reglas de compresión

Estas tablas describen el comportamiento de la compresión de Azure CDN para cada caso.

> [AZURE.IMPORTANT] Para **Azure CDN de Verizon** (Standard y Premium), solo elegibles archivos se comprimen.  Para ser elegible para la compresión, un archivo debe:
>
> - Ser mayor de 128 bytes.
> - Ser menor que 1 MB.
> 
> Para **Azure CDN de Akamai**, todos los archivos son elegibles para la compresión.
>
> Para todos los productos de CDN de Azure, un archivo debe ser un tipo MIME que se ha [configurado para la compresión](#enabling-compression).
>
> Perfiles de **Azure CDN de Verizon** (Standard y Premium) admiten **gzip**, **disminuir**o **bzip2** codificación.  Perfiles de **Azure CDN de Akamai** solo admiten codificación **gzip** .
>
> Extremos de **Azure CDN de Akamai** solicitan siempre archivos **gzip** codificada desde el origen, independientemente de la solicitud de cliente.

### <a name="compression-disabled-or-file-is-ineligible-for-compression"></a>Compresión deshabilitada o el archivo está no válido para la compresión

|Formato solicitado de cliente (a través de encabezado de codificación de Aceptar)|Formato de archivo en caché|Respuesta CDN al cliente|Notas|
|----------------|-----------|------------|-----|
|Comprimido|Comprimido|Comprimido|   |
|Comprimido|Sin comprimir|Sin comprimir|    | 
|Comprimido|No en caché|Comprimido o sin comprimir|Depende de la respuesta de origen|
|Sin comprimir|Comprimido|Sin comprimir|    |
|Sin comprimir|Sin comprimir|Sin comprimir|    |   
|Sin comprimir|No en caché|Sin comprimir|     |

### <a name="compression-enabled-and-file-is-eligible-for-compression"></a>Habilitar la compresión y el archivo es elegible para la compresión

|Formato solicitado de cliente (a través de encabezado de codificación de Aceptar)|Formato de archivo en caché|Respuesta CDN al cliente|Notas|
|----------------|-----------|------------|-----|
|Comprimido|Comprimido|Comprimido|CDN transcodifica entre los formatos admitidos|
|Comprimido|Sin comprimir|Comprimido|CDN realiza compresión|
|Comprimido|No en caché|Comprimido|CDN realiza compresión si origin devuelve sin comprimir.  **CDN de Azure desde Verizon** pase el archivo sin comprimir en la primera solicitud y, a continuación, comprimir y caché el archivo para las solicitudes subsiguientes.  Archivos con `Cache-Control: no-cache` encabezado nunca se comprimen. 
|Sin comprimir|Comprimido|Sin comprimir|CDN realiza descompresión|
|Sin comprimir|Sin comprimir|Sin comprimir|     |  
|Sin comprimir|No en caché|Sin comprimir|     |

## <a name="media-services-cdn-compression"></a>Servicios multimedia de compresión de CDN

Media Services CDN habilitada extremos streaming, la compresión está habilitada de forma predeterminada para los siguientes tipos de contenido: aplicación/vnd.ms-sstr + xml, application/dash+xml,application/vnd.apple.mpegurl, aplicación/f4m + xml. No se puede activar o desactivar la compresión para los tipos de mencionada con el portal de Azure.  

## <a name="see-also"></a>Vea también
- [Solución de problemas de compresión de archivos CDN](cdn-troubleshoot-compression.md)    
