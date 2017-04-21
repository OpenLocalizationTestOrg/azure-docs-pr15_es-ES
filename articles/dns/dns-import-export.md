<properties
   pageTitle="Importar y exportar un archivo de zona del dominio a DNS de Azure mediante CLI | Microsoft Azure"
   description="Obtenga información sobre cómo importar y exportar un archivo de zona DNS a Azure DNS mediante CLI de Azure"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Importar y exportar un archivo de zona DNS mediante la CLI de Azure


En este artículo le guiará a través de cómo importar y exportar archivos de zona DNS para el DNS de Azure mediante la CLI de Azure.

## <a name="introduction-to-dns-zone-migration"></a>Introducción a la migración de zona DNS

Un archivo de zona DNS es un archivo de texto que contiene los detalles de cada registro de sistema de nombres de dominio (DNS) en la zona. Sigue un formato estándar, lo que es adecuado para transferir los registros DNS entre sistemas DNS. Con un archivo de zona de forma rápida, confiable y adecuado para transferir una zona DNS dentro o fuera de Azure DNS.

Azure DNS es compatible con la importación y exportación de archivos de zona mediante la interfaz de línea de comandos de Azure (CLI). CLI Azure es una herramienta de línea de comandos entre plataformas de administración de servicios de Azure. Está disponible para las plataformas Windows, Mac y Linux desde la [página de descargas de Azure](https://azure.microsoft.com/downloads/). Compatibilidad entre plataformas es especialmente importante para importar y exportar archivos de zona, porque el software de servidor de nombre más comunes, [enlazar](https://www.isc.org/downloads/bind/), normalmente se ejecuta en Linux.

## <a name="obtain-your-existing-dns-zone-file"></a>Obtener el archivo de zona DNS existente

Antes de importar un archivo de zona DNS en DNS de Azure, debe obtener una copia del archivo de zona. El origen de este archivo dependen de donde se hospeda actualmente la zona DNS.

- Si la zona DNS está alojada en un servicio de partner (como un registrador de dominios, dedicado proveedor de hospedaje DNS o el proveedor de servicios de nube alternativo), dicho servicio debe proporcionar la capacidad de descargar el archivo de zona DNS.

- Si la zona DNS se hospeda en DNS de Windows, la carpeta predeterminada para los archivos de zona es **%systemroot%\system32\dns**. La ruta de acceso completa de cada archivo de zona también se muestra en la ficha **General** de la consola de administración del servicio DNS.

- Si la zona DNS está hospedada mediante el uso de enlace, la ubicación del archivo de zona para cada zona especificada en el archivo de configuración de enlace **named.conf**.

**Trabajar con archivos de zona de GoDaddy**<BR>
Archivos de zona descargados desde GoDaddy tienen un formato no estándar ligeramente. Necesitará corregir esto antes de importar estos archivos de zona DNS de Azure. Nombres DNS en el RData de cada registro DNS se especifican como nombres completos, pero no tienen una terminación "." Esto significa que se interpretan por otros sistemas DNS como nombres relativos. Debe editar el archivo de zona para anexar el carácter final "." a sus nombres antes de importar en Azure DNS.

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Importar un archivo de zona DNS en DNS de Azure


Importar un archivo de zona creará una nueva zona en Azure DNS si no existe. Si ya existe la zona, conjuntos de registros en el archivo de zona deben combinarse con los conjuntos de registro existentes.

### <a name="merge-behavior"></a>Comportamiento de combinación

- De forma predeterminada, se combinan los conjuntos de registros nuevos y existentes. Registros idénticos dentro de un conjunto de registros combinado se lleva a cabo.

- Como alternativa, especificando la `--force` opción, el proceso de importación reemplazará existentes conjuntos de registros con conjuntos de registros nuevos. No se quitarán los conjuntos de registro existente que no tienen un registro correspondiente en el archivo de zona importado.

- Cuando se combinan conjuntos de registros, se utiliza el tiempo de vida (TTL) preexistentes de conjuntos de registros. Cuando `--force` es usado, se utiliza el período de vida del conjunto de registros nuevo.

- Inicio de los parámetros de autoridad (SOA) (excepto `host`) siempre se toman desde el archivo de zona importado, independientemente de si `--force` se utiliza. Del mismo modo, en el registro de servidor de nombres establecer en el vértice de zona, el valor de TTL siempre se toma del archivo de zona importado.

- Un registro CNAME importado no reemplaza un registro CNAME existente con el mismo nombre a menos que la `--force` parámetro se especifica.

- Cuando se produce un conflicto entre un registro CNAME y otro registro del mismo nombre pero de tipo diferente (con independencia de que se existente o nueva), se conservará el registro existente. Esto es independiente del uso de `--force`.

### <a name="additional-information-about-importing"></a>Información adicional sobre la importación

Las notas siguientes proporcionan más detalles técnicos sobre la zona de proceso de importación.

- La `$TTL` directiva es opcional y es compatible. Cuando se `$TTL` directiva se expresa, registros sin un TTL explícito será conjunto importados a un TTL predeterminado de 3.600 segundos. Cuando dos registros en el mismo conjunto de registros especifican TTLs diferentes, se utiliza el valor inferior.

- La `$ORIGIN` directiva es opcional y es compatible. Cuando se `$ORIGIN` está establecido, el valor predeterminado utilizado es el nombre de la zona tal como se especifica en la línea de comandos (más la terminación ".").

- La `$INCLUDE` y `$GENERATE` directivas no son compatibles.

- Estos tipos de registros son compatibles: A, AAAA, CNAME, MX, NS, inicio de autoridad, SRV y TXT.

- El registro de inicio de autoridad Azure DNS se crea automáticamente cuando se crea una zona. Al importar un archivo de zona, se toman todos los parámetros de inicio de autoridad de la zona archivo *excepto* la `host` parámetro. Este parámetro utiliza el valor de DNS de Azure. Esto es porque este parámetro debe hacer referencia al servidor de nombres primario proporcionado por DNS de Azure.

- Establecer en el vértice de zona del registro del servidor de nombres también se crea automáticamente Azure DNS cuando se crea la zona. Se importa únicamente el período de vida de este conjunto de registros. Estos registros contienen los nombres de servidor de nombre proporcionados por DNS de Azure. Los datos del registro no se sobrescriben con los valores contenidos en el archivo de zona importado.

- Durante la versión preliminar pública, Azure DNS admite solo registros TXT de cadena único. Multicadena registros TXT se concatena y se truncan a 255 caracteres.

### <a name="cli-format-and-values"></a>Valores y formato CLI


El formato del comando CLI Azure para importar una zona DNS es:<BR>`azure network dns zone import [options] <resource group> <zone name> <zone file name>`

Valores:

- `<resource group>`es el nombre del grupo de recursos de la zona de DNS de Azure.
- `<zone name>`es el nombre de la zona.
- `<zone file name>`es el nombre de ruta de acceso o del archivo de zona que desea importar.

Si no existe una zona con este nombre en el grupo de recursos, se creará para usted. Si ya existe la zona, los conjuntos de registros importados se combinarán con conjuntos de registros existentes. Para sobrescribir los conjuntos de registro existentes, use la `--force` opción.

Para comprobar el formato de un archivo de zona sin importarlos realmente, utilice la `--parse-only` opción.

### <a name="step-1-import-a-zone-file"></a>Paso 1. Importar un archivo de zona

Para importar un archivo de zona de la zona **contoso.com**.

1. Inicie sesión en su suscripción de Azure usando CLI Azure.

        azure login

2. Seleccione la suscripción donde desee crear la nueva zona DNS.

        azure account set <subscription name>

3. Azure DNS es un servicio de solo el Administrador de recursos Azure, por lo que debe ser cambiada Azure CLI al modo de administrador de recursos.

        azure config mode arm

4. Antes de utilizar el servicio DNS de Azure, debe registrar su suscripción para utilizar el proveedor de recursos de Microsoft.Network. (Esta es una operación de una sola vez para cada suscripción).

        azure provider register Microsoft.Network

5. Si aún no tiene una, debe crear un grupo de recursos de administrador de recursos.

        azure group create myresourcegroup westeurope

6. Para importar la zona **contoso.com** desde el archivo **contoso.com.txt** en una nueva zona DNS en el grupo de recursos **myresourcegroup**, ejecute el comando `azure network dns zone import`.<BR>Este comando cargar el archivo de zona y analizarla. El comando ejecutará una serie de comandos en el servicio DNS de Azure para crear la zona y todo el registro se establece en la zona. El comando también informa del progreso en la ventana de la consola, junto con los errores y advertencias. Como conjuntos de registros se crean en serie, puede tardar unos minutos para importar un archivo de zona de gran tamaño.

        azure network dns zone import myresourcegroup contoso.com contoso.com.txt



### <a name="step-2-verify-the-zone"></a>Paso 2. Compruebe la zona

Para comprobar la zona DNS después de importar el archivo, puede utilizar cualquiera de los métodos siguientes:

- Puede enumerar los registros mediante el siguiente comando CLI de Azure.

        azure network dns record-set list myresourcegroup contoso.com

- Lista de los registros mediante el cmdlet de PowerShell `Get-AzureRmDnsRecordSet`.

- Puede usar `nslookup` para comprobar la resolución de nombres para los registros. Porque la zona no está delegada todavía, debe especificar los servidores de nombres DNS de Azure correctos explícitamente. En el ejemplo siguiente se muestra cómo recuperar los nombres de servidor de nombre asignados a la zona. TI también muestra cómo consultar el registro "www" mediante `nslookup`.

        C:\>azure network dns record-set show myresourcegroup contoso.com @ NS
        info:Executing command network dns record-set show
        + Looking up the DNS Record Set "@" of type "NS"
        data:Id: /subscriptions/…/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
        data:Name: @
        data:Type: Microsoft.Network/dnszones/NS
        data:Location: global
        data:TTL : 3600
        data:NS records
        data:Name server domain name : ns1-01.azure-dns.com
        data:Name server domain name : ns2-01.azure-dns.net
        data:Name server domain name : ns3-01.azure-dns.org
        data:Name server domain name : ns4-01.azure-dns.info
        data:
        info:network dns record-set show command OK

        C:\> nslookup www.contoso.com ns1-01.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221

### <a name="step-3-update-dns-delegation"></a>Paso 3. Delegación de DNS de actualización

Después de haber comprobado que la zona se ha importado correctamente, debe actualizar la delegación de DNS para que apunten a los servidores de nombres DNS de Azure. Para obtener más información, vea el artículo [actualizar la delegación DNS](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Exportar un archivo de zona DNS de DNS de Azure

El formato del comando CLI Azure para importar una zona DNS es:<BR>`azure network dns zone export [options] <resource group> <zone name> <zone file name>`

Valores:

- `<resource group>`es el nombre del grupo de recursos de la zona de DNS de Azure.
- `<zone name>`es el nombre de la zona.
- `<zone file name>`es la ruta de acceso y nombre de exportar el archivo de zona.

Como con la importación de zona, primero debe iniciar sesión, elige la suscripción y configurar CLI Azure para usar el modo de administrador de recursos.

### <a name="to-export-a-zone-file"></a>Para exportar un archivo de zona


1. Inicie sesión en su suscripción de Azure usando CLI Azure.

        azure login

2. Seleccione la suscripción donde desee crear la nueva zona DNS.

        azure account set <subscription name>

3. Azure DNS es un servicio Azure solo el Administrador de recursos. CLI Azure se debe cambiar al modo de administrador de recursos.

        azure config mode arm

4. Para exportar el existente de zona DNS de Azure **contoso.com** en el grupo de recursos **myresourcegroup** para el archivo **contoso.com.txt** (en la carpeta actual), ejecute `azure network dns zone export`. Este comando llame al servicio de Azure DNS para enumerar los conjuntos de registros en la zona y exportar los resultados a un archivo de zona compatible con el enlace.

        azure network dns zone export myresourcegroup contoso.com contoso.com.txt

