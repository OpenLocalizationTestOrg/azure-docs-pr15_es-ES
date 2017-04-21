<properties
  pageTitle="Almacenamiento de máquina virtual Linux y Azure | Microsoft Azure"
  description="Describe Azure estándar y almacenamiento Premium con máquinas virtuales de Linux."
  services="virtual-machines-linux"
  documentationCenter="virtual-machines-linux"
  authors="vlivech"
  manager="timlt"
  editor=""/>

<tags
  ms.service="virtual-machines-linux"
  ms.devlang="NA"
  ms.topic="article"
  ms.tgt_pltfrm="vm-linux"
  ms.workload="infrastructure"
  ms.date="10/04/2016"
  ms.author="v-livech"/>

# <a name="azure-and-linux-vm-storage"></a>Almacenamiento de Azure y Linux VM

Almacenamiento de Azure es la solución de almacenamiento de la nube para aplicaciones modernas que se basan en la duración, disponibilidad y escalabilidad para satisfacer las necesidades de sus clientes.  Además de lo que permite a los desarrolladores generar aplicaciones a gran escala para admitir escenarios de nuevos, el almacenamiento de Azure proporciona la base de almacenamiento de Azure máquinas virtuales de Windows.

## <a name="azure-storage-standard-and-premium"></a>Almacenamiento de Azure: Estándar y Premium

Azure máquina virtual puede crearse en discos de almacenamiento estándar o discos de almacenamiento de premium.  Al usar el Portal para elegir la máquina virtual debe activar o desactivar una lista desplegable en la pantalla de conceptos básicos para ver los discos estándar y premium.  Captura de pantalla siguiente resalta ese menú de botón de alternancia.  Cuando cambia a SSD, almacenamiento de premium solo se muestran las VM habilitadas, todo apoyado por SSD unidades.  Cuando cambia el disco duro, almacenamiento estándar habilitado máquinas virtuales que se muestran las unidades de disco de copia de giro, junto con el almacenamiento de premium máquinas virtuales copia por SSD.

  ![Screen1](../virtual-machines/media/virtual-machines-linux-azure-vm-storage-overview/screen1.png)

Al crear una máquina virtual de la `azure-cli` puede elegir entre estándar y premium al elegir el tamaño de la memoria virtual a través de la `-z` o `--vm-size` indicador de cli.

### <a name="create-a-vm-with-standard-storage-vm-on-the-cli"></a>Crear una máquina virtual con almacenamiento estándar VM en cli

El indicador de cli `-z` elige Standard_A1 con A1 un almacenamiento estándar basarse VM Linux.

```bash
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_A1
```

### <a name="create-a-vm-with-premium-storage-on-the-cli"></a>Crear una máquina virtual con almacenamiento premium en cli

El indicador de cli `-z` elige Standard_DS1 con DS1 un almacenamiento premium basarse VM Linux.

```bash
azure vm quick-create -g rbg \
exampleVMname \
-l westus \
-y Linux \
-Q Debian \
-u exampleAdminUser \
-M ~/.ssh/id_rsa.pub
-z Standard_DS1
```

## <a name="standard-storage"></a>Almacenamiento estándar

Azure almacenamiento estándar es el tipo predeterminado de almacenamiento.  Almacenamiento estándar es rentable mientras aún se está eficaz.  

## <a name="premium-storage"></a>Almacenamiento de Premium

Almacenamiento de Azure Premium ofrece soporte de disco de alto rendimiento, baja latencia para máquinas virtuales que ejecutan cargas de trabajo O-intensiva. Discos de máquina virtual (VM) que utilizan almacenamiento Premium almacenan los datos en unidades de estado sólido (SSD). Puede migrar discos de VM de la aplicación a almacenamiento de Azure Premium para aprovechar las ventajas de la velocidad y rendimiento de estos discos.

Características de almacenamiento de Premium:

- Discos de almacenamiento de Premium: Almacenamiento de Azure Premium admite discos de VM pueden estar asociados a la serie DS, DSv2 o GS máquinas virtuales de Azure.

- Blob de página Premium: El almacenamiento de Premium admite Blobs de página de Azure, que se utilizan para mantener los discos persistentes para Azure máquinas virtuales de Windows (VM).

- Almacenamiento redundante local Premium: Una cuenta de almacenamiento Premium solo admite localmente redundantes almacenamiento (LRS) como la opción de replicación y mantiene tres copias de los datos dentro de una única región.

- [Almacenamiento de Premium](../storage/storage-premium-storage.md)

## <a name="premium-storage-supported-vms"></a>Almacenamiento de Premium compatibles VM

Almacenamiento de Premium es compatible con la serie DS, DSv2-series, GS serie y máquinas virtuales de Azure Fs serie (VM). Puede usar los discos de almacenamiento estándar y Premium con almacenamiento Premium compatibles de máquinas virtuales. Pero no puede utilizar discos de almacenamiento de Premium con una serie VM, que no es compatibles de almacenamiento de Premium.

Los siguientes son las distribuciones de Linux se valida con almacenamiento Premium.

| Distribución. | Versión                 | Núcleo compatible    |
|--------------|-------------------------|---------------------|
| Ubuntu       | 12.04                   | 3.2.0-75.110+       |
| Ubuntu       | 14.04                   | 3.13.0-44.73+       |
| Debian       | 7.x, 8.x                | 3.16.7-ckt4-1+      |
| SLES         | SLES 12                 | 3.12.36-38.1+       |
| SLES         | SLES 11 SERVICE PACK 4             | 3.0.101-0.63.1+     |
| CoreOS       | 584.0.0+                | 3.18.4+             |
| CentOS       | 6.5, 6.6, 6,7, 7.0, 7.1 | 3.10.0-229.1.2.el7+ |
| RHEL         | 6.8 +, 7.2 +              |                     |


## <a name="file-storage"></a>Almacenamiento de archivos

Almacenamiento de archivos Azure ofrece recursos compartidos de archivos en la nube mediante el protocolo SMB estándar. Con archivos de Azure, puede migrar las aplicaciones empresariales que se basan en servidores de archivos en Azure. Aplicaciones que se ejecutan en Azure pueden montaje fácilmente recursos compartidos de archivos de Azure máquinas virtuales de Windows con Linux. Y con la versión más reciente de almacenamiento de archivos, puede montaje un archivo compartido desde una aplicación local que admita SMB 3.0.  Como los recursos compartidos de archivos son recursos compartidos SMB, puede tener acceso a través de la API del sistema de archivo estándar.

Almacenamiento de archivos se basa en la misma tecnología de almacenamiento de blobs, tabla y cola, por lo que el almacenamiento de archivos que ofrece la disponibilidad, la duración, escalabilidad y redundancia geo integrado en la plataforma de almacenamiento de Azure. Para más información sobre los objetivos de rendimiento de almacenamiento de archivos y los límites, vea destinos de rendimiento y escalabilidad de almacenamiento de Azure.

- [Cómo usar el almacenamiento de archivos de Azure con Linux](../storage/storage-how-to-use-files-linux.md)

## <a name="hot-storage"></a>Almacenamiento de acceso rápido

El nivel de almacenamiento de acceso rápido Azure está optimizado para almacenar los datos que se tiene acceso con frecuencia.  Almacenamiento de acceso rápido es el tipo de almacenamiento predeterminada para BLOBs stores.

## <a name="cool-storage"></a>Almacenamiento de información interesante

El nivel de almacenamiento interesantes Azure está optimizado para almacenar los datos con poca frecuencia acceder y larga duración. Casos de uso de ejemplo para el almacenamiento de interesantes que se pueden incluyen las copias de seguridad, contenido multimedia, datos científicos, cumplimiento y archivados datos. En general, cualquier dato que apenas se obtiene acceso es un candidato ideal para el almacenamiento de interesante.

|                             | Nivel de almacenamiento de acceso rápido      | Nivel de almacenamiento interesante     |
|:----------------------------|:---------------------:|:---------------------:|
| Disponibilidad                | 99,9%                 | 99%                   |
| Disponibilidad (lecturas GRS RA) | 99,99%                | 99,9%                 |
| Cargos de uso               | Costos más altos de almacenamiento  | Reducir los costos de almacenamiento   |
|                             | Access inferior          | Mayor acceso         |
|                             | y los costes de transacción | y los costes de transacción |


## <a name="redundancy"></a>Redundancia

Los datos de su cuenta de Microsoft Azure almacenamiento siempre se duplica para garantizar la duración y alta disponibilidad, el SLA de almacenamiento de Azure incluso en el caso de errores de hardware transitorias de la reunión.

Cuando se crea una cuenta de almacenamiento, debe seleccionar una de las siguientes opciones de replicación:

- Almacenamiento redundante local (LRS)
- Almacenamiento de zona redundantes (ZRS)
- Almacenamiento geo redundantes (GRS)
- Almacenamiento de acceso de lectura geo redundantes (RA GRS)

### <a name="locally-redundant-storage"></a>Almacenamiento redundante local

Almacenamiento redundante local (LRS) aplica los datos dentro de la región en la que se creó su cuenta de almacenamiento. Para maximizar la duración, cada solicitud realizada en los datos en su cuenta de almacenamiento se duplica tres veces. Estas tres réplicas residen en los dominios fault independiente y actualización.  Una solicitud de correctamente devuelve solo una vez que se ha escrito en las tres réplicas.

### <a name="zone-redundant-storage"></a>Almacenamiento de zona redundantes

Almacenamiento de zona redundantes (ZRS) aplica los datos en dos o tres instalaciones, dentro de una única región o a través de dos regiones, lo que proporciona mayor duración que LRS. Si su cuenta de almacenamiento tiene ZRS habilitado, sus datos son resistentes incluso en el caso de un error en una de las instalaciones.

### <a name="geo-redundant-storage"></a>Almacenamiento geo redundantes

Almacenamiento geo redundantes (GRS) aplica los datos a una zona secundaria cientos de millas fuera de la región principal. Si su cuenta de almacenamiento tiene GRS habilitada, los datos son resistentes incluso en el caso de una interrupción regional completa o un desastre en el que el área principal no es recuperable.

### <a name="read-access-geo-redundant-storage"></a>Almacenamiento de acceso de lectura geo redundantes

Almacenamiento de acceso de lectura geo redundantes (RA GRS) aumenta al máximo la disponibilidad de su cuenta de almacenamiento, proporcionando acceso de solo lectura a los datos en la ubicación secundaria, además de la replicación entre dos regiones proporcionados por GRS. En caso de que los datos no está disponibles en el área principal, la aplicación puede leer datos de la región secundaria.

Para un análisis detallado vea de redundancia de almacenamiento de Azure:

- [Replicación de almacenamiento de Azure](../storage/storage-redundancy.md)

## <a name="scalability"></a>Escalabilidad

Almacenamiento de Azure es masivamente scalable, por lo que puede almacenar y proceso cientos de terabytes de datos para admitir los escenarios grande de datos necesarios para análisis científico, financieros y aplicaciones multimedia. O bien, puede almacenar las pequeñas cantidades de datos necesarios para un sitio Web de pequeña empresa. Donde se encuentran sus necesidades, puede pagar sólo los datos que está almacenando. Almacenamiento de Azure actualmente almacena decenas de trillones de objetos de cliente único y controla millones de solicitudes por segundo en promedio.

Para cuentas de almacenamiento estándar: una cuenta de almacenamiento estándar tiene una tasa de solicitud total máximo de 20 000 IOPS. El total IOPS a través de todos los discos de máquina virtual en una cuenta de almacenamiento estándar no debe superar este límite.

Para cuentas de almacenamiento premium: una cuenta de almacenamiento premium tiene una tasa de rendimiento total máximo de 50 GB/s. El rendimiento total a través de todos los discos VM no debe superar este límite.

## <a name="availability"></a>Disponibilidad

Garantizamos que al menos 99,99% (99,9% de nivel de acceso a interesante) del tiempo, se procesará correctamente las solicitudes de lectura de datos de cuentas de almacenamiento de acceso de lectura Geo redundantes (RA GRS), siempre que se vuelve a intentar intentos para leer datos de la región principal en la región secundaria.

Garantizamos que al menos 99,9% (99% de nivel de acceso a interesante) del tiempo, se procesará correctamente las solicitudes de lectura de datos de almacenamiento redundantes localmente (LRS), almacenamiento redundantes de zona (ZRS) y cuentas de almacenamiento redundantes de Geo (GRS).

Garantizamos que al menos 99,9% (99% de nivel de acceso a interesante) del tiempo, se procesará correctamente las solicitudes de escritura de datos en almacenamiento redundantes localmente (LRS), zona redundantes almacenamiento (ZRS) y las cuentas de almacenamiento redundantes de Geo (GRS) y almacenamiento de acceso de lectura Geo redundantes (RA GRS).

- [Azure SLA de almacenamiento](https://azure.microsoft.com/support/legal/sla/storage/v1_1/)


## <a name="regions"></a>Regiones

Azure está disponible de forma general 30 regiones del mundo y anuncia planes 4 regiones adicionales. Expansión geográfica es una prioridad de Azure porque permite que nuestros clientes conseguir mejor rendimiento y admite sus requisitos y preferencias de ubicación de los datos.  Región de más reciente azures iniciar se encuentra en Alemania.

Alemania de Microsoft Cloud proporciona una opción diferenciada a los servicios de Microsoft Cloud ya están disponibles en Europa, crear oportunidades aumentadas para innovaciones y crecimiento económico para altamente regulados socios y clientes en Alemania, la Unión Europea (UE) y la Asociación Europea de libre comercio (AELC).

Se administran los datos de clientes en los centros de datos nuevos en Magdeburg y Frankfurt, bajo el control de un administrador de datos, sistemas de T internacional, una compañía alemán independiente y subsidiaria de Deutsche Telekom. Servicios de nube comercial de Microsoft en estos centros de datos observar alemán normas de control de datos y ofrecen a los clientes opciones adicionales de cómo y dónde se procesan los datos.


- [Mapa de regiones de Azure](https://azure.microsoft.com/regions/)

## <a name="security"></a>Seguridad

Almacenamiento de Azure proporciona un conjunto completo de características de seguridad que juntas permiten a los desarrolladores generar aplicaciones seguras. La cuenta de almacenamiento, sí se puede proteger mediante Control de acceso basado en roles y Azure Active Directory. Pueden proteger los datos al transmitirlos entre una aplicación y Azure mediante el cifrado de cliente, HTTPS o SMB 3.0. Pueden establecer el datos para cifrar automáticamente al escribir en el almacenamiento de Azure con cifrado de servicio de almacenamiento (SSE). Discos de sistema operativo y datos usados por máquinas virtuales se pueden establecer para ser cifrada con cifrado de disco de Azure. Se pueden conceder acceso delegado a los objetos de datos en el almacenamiento de Azure usar firmas de acceso compartido.

### <a name="management-plane-security"></a>Seguridad de plano de administración

Plano de administración consta de los recursos que se utilizan para administrar su cuenta de almacenamiento. En esta sección, hablaremos acerca del modelo de implementación de administrador de recursos de Azure y cómo usar el Control de acceso basado en roles (RBAC) para controlar el acceso a sus cuentas de almacenamiento. También hablaremos acerca de cómo administrar las claves de la cuenta de almacenamiento y cómo volver a generar ellos.

### <a name="data-plane-security"></a>Seguridad de plano de datos

En esta sección, veremos permitir el acceso a los objetos de datos reales en su cuenta de almacenamiento, como BLOB, archivos, colas y tablas, uso compartido firmas de Access y almacena las directivas de acceso. Trataremos SA de nivel de servicio y asociaciones de seguridad de nivel de la cuenta. También veremos cómo limitar el acceso a una dirección IP específica (o intervalo de direcciones IP), cómo limitar el protocolo utilizado para HTTPS y cómo revocar una firma de acceso compartido sin tener que esperar para que expire.

## <a name="encryption-in-transit"></a>Cifrado en tránsito

En esta sección se explica cómo proteger los datos al transferir dentro o fuera de almacenamiento de Azure. Hablaremos recomienda el uso de HTTPS y el cifrado usado por SMB 3.0 Azure recursos compartidos de archivos. También se echamos un vistazo a cifrado de cliente, lo que le permite cifrar los datos antes de que se transfieran en almacenamiento en una aplicación de cliente y descifrar los datos después de que se transfieran sin espacio de almacenamiento.

## <a name="encryption-at-rest"></a>Cifrado al resto

Hablaremos sobre cifrado de servicio de almacenamiento (SSE) y cómo puede habilitar para una cuenta de almacenamiento de su blobs de bloque, blobs de página y se cifran automáticamente cuando escribe en Azure almacenamiento de blobs de datos anexados. También veremos cómo puede usar el cifrado de disco de Azure y explorar las diferencias básicas y las cajas de cifrado de disco frente a SSE frente a cifrado de cliente. Veremos brevemente cumplimiento de FIPS para equipos de gobierno de Estados Unidos.

- [Guía de seguridad de almacenamiento de Azure](../storage/storage-security-guide.md)

## <a name="cost-savings"></a>Ahorro de costos

- [Costo de almacenamiento](https://azure.microsoft.com/pricing/details/storage/)

- [Calculadora de costo de almacenamiento](https://azure.microsoft.com/pricing/calculator/?service=storage)

## <a name="storage-limits"></a>Límites de almacenamiento

- [Límites de servicio de almacenamiento](../azure-subscription-service-limits.md#storage-limits)
