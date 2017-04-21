<properties
    pageTitle="Asignar almacenamiento en Azure recuperación de sitios para la replicación de la máquina virtual de Hyper-V entre los centros de datos local | Microsoft Azure"
    description="Preparar la asignación de almacenamiento para la replicación de la máquina virtual de Hyper-V entre dos centros de datos locales con Azure sitio de recuperación."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="07/06/2016"
    ms.author="raynew"/>


# <a name="prepare-storage-mapping-for-hyper-v-virtual-machine-replication-between-two-on-premises-datacenters-with-azure-site-recovery"></a>Preparar la asignación de almacenamiento para la replicación de la máquina virtual de Hyper-V entre dos centros de datos locales con Azure sitio recuperación


Recuperación de sitio de Azure contribuye a su estrategia de recuperación (BCDR) de desastres y continuidad empresarial por coordinar replicación, migración tras error y recuperación de máquinas virtuales y servidores físicos. Este artículo describe la asignación de almacenamiento, lo que ayuda a realizar óptimo usar de almacenamiento cuando usa recuperación de sitios para replicar máquinas virtuales Hyper-V entre los centros de datos de dos local VMM.

Publicar comentarios o preguntas en la parte inferior de este artículo o en el [Foro de servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Información general

Asignación de almacenamiento solo es relevante cuando está replicar máquinas virtuales de Hyper-V que provienen de nubes de VMM desde un centro de datos principal a un centro de datos secundario con replicación de réplica de Hyper-V o SAN, como sigue:


- **Local réplica local con la réplica de Hyper-V)** : Configura almacenamiento asignación por asignación clasificaciones de almacenamiento en un origen y destino en servidores VMM para hacer lo siguiente:

    - **Identificar almacenamiento de destino para máquinas virtuales de réplica**: se reflejarán máquinas virtuales de Windows a un destino de almacenamiento (SMB compartir o clúster volúmenes compartidos (CSVs)) que elija.
    - **Ubicación de la máquina virtual de réplica**: asignación de almacenamiento se utiliza para colocar de manera óptima máquinas virtuales de réplica en servidores de host de Hyper-V. Máquinas virtuales de réplica se pondrán en hosts que pueden tener acceso a la clasificación de almacenamiento asignada.
    - **Ninguna asignación de almacenamiento**: si no configurar asignación de almacenamiento, máquinas virtuales de Windows se reflejarán en la ubicación de almacenamiento predeterminada especificada en el servidor de host de Hyper-V asociado a la máquina virtual de réplica.

- **Réplica local con SAN local**: configurar almacenamiento asignación por asignación agrupaciones de almacenamiento de matrices en un origen y destino en servidores VMM.
    - **Especificar grupo**: especifica qué bloque de almacenamiento secundaria recibe datos de replicación desde el grupo principal.
    - **Identificar grupos de almacenamiento de destino**: garantiza que LUN en un grupo de replicación de origen se copian en el grupo de almacenamiento de destino asignados de su elección.

## <a name="set-up-storage-classifications-for-hyper-v-replication"></a>Configurar clasificaciones de almacenamiento para la replicación de Hyper-V

Cuando usa réplica Hyper-V para replicar con el sitio de recuperación, asigne entre clasificaciones de almacenamiento en servidores de origen y destino VMM o en un único servidor VMM si dos sitios administrados por el mismo servidor VMM. Tenga en cuenta que:

- Cuando está correctamente configurada asignación y la replicación está habilitada, virtual en el disco duro de la máquina virtual en la ubicación primaria se reflejarán al almacenamiento en la ubicación de destino asignados.
- Clasificaciones de almacenamiento deben estar disponibles para los grupos host de nubes de origen y destino.
- Clasificaciones no es necesario tener el mismo tipo de almacenamiento. Por ejemplo, puede asignar una clasificación de origen que contiene los recursos compartidos SMB a una clasificación de destino que contiene CSVs.
- Más en [cómo crear clasificaciones de almacenamiento en VMM](https://technet.microsoft.com/library/gg610685.aspx).

## <a name="example"></a>Ejemplo

Si las clasificaciones están configuradas correctamente en VMM cuando seleccione el origen y destino servidor VMM durante la asignación de almacenamiento, se mostrarán las clasificaciones de origen y destino. Aquí es un ejemplo de recursos compartidos de archivos de almacenamiento y las clasificaciones para una organización con dos ubicaciones de Nueva York y Chicago.

**Ubicación** | **Servidor VMM** | **Uso compartido de archivos (origen)** | **Clasificación (origen)** | **Asignado a** | **Uso compartido de archivos (destino)**
---|---|--- |---|---|---
Nueva York | VMM_Source| SourceShare1 | ORO | GOLD_TARGET | TargetShare1
 |  | SourceShare2 | PLATA | SILVER_TARGET | TargetShare2
 | | SourceShare3 | BRONCE | BRONZE_TARGET | TargetShare3
Chicago | VMM_Target |  | GOLD_TARGET | No asignado |
| | | SILVER_TARGET | No asignado |
 | | | BRONZE_TARGET | No asignado

En la pestaña de **Almacenamiento del servidor** debe configurar en la página de **recursos** del portal de recuperación del sitio.

![Configurar la asignación de almacenamiento](./media/site-recovery-storage-mapping/storage-mapping1.png)

Con este ejemplo:
- Cuando un se crea una máquina virtual de réplica para cualquier máquina virtual en almacenamiento de oro (SourceShare1), se reflejarán en un almacenamiento GOLD_TARGET (TargetShare1).
- Cuando se crea una máquina virtual de réplica para cualquier máquina virtual en almacenamiento plata (SourceShare2), se reflejarán en un almacenamiento SILVER_TARGET (TargetShare2) y así sucesivamente.

Las acciones de archivo real y sus clasificaciones asignadas en VMM aparecen en la siguiente captura de pantalla.

![Clasificaciones de almacenamiento en VMM](./media/site-recovery-storage-mapping/storage-mapping2.png)

## <a name="multiple-storage-locations"></a>Varias ubicaciones de almacenamiento

Si la clasificación de destino está asignada a varios recursos compartidos de SMB o CSVs, la ubicación de almacenamiento óptima se selecciona automáticamente cuando la máquina virtual está protegida. Si ningún almacenamiento adecuado de destino está disponible con la clasificación especificada, se utiliza la ubicación de almacenamiento predeterminada especificada en el host de Hyper-V para colocar el disco duro virtual de réplica.

En la tabla siguiente se muestran cómo la clasificación de almacenamiento y volúmenes compartidos de clúster están configurados en nuestro ejemplo.

**Ubicación** | **Clasificación** | **Almacenamiento asociado**
---|---|---
Nueva York | ORO | <p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p>
 | PLATA | <p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p>
Chicago | GOLD_TARGET | <p>C:\ClusterStorage\TargetVolume1</p><p>\\FileServer\TargetShare1</p>
 | SILVER_TARGET| <p>C:\ClusterStorage\TargetVolume2</p><p>\\FileServer\TargetShare2</p>

Esta tabla resume el comportamiento al habilitar protección para máquinas virtuales de Windows (VM1 - VM5) en este entorno de ejemplo.

**Máquina virtual** | **Almacenamiento de origen** | **Clasificación de origen** | **Almacenamiento de destino asignados**
---|---|---|---
VM1 | C:\ClusterStorage\SourceVolume1 | ORO | <p>C:\ClusterStorage\SourceVolume1</p><p>\\\FileServer\SourceShare1</p><p>Ambos GOLD_TARGET</p>
VM2 | \\FileServer\SourceShare1 | ORO | <p>C:\ClusterStorage\SourceVolume1</p><p>\\FileServer\SourceShare1</p> <p>Ambos GOLD_TARGET</p>
VM3 | C:\ClusterStorage\SourceVolume2 | PLATA | <p>C:\ClusterStorage\SourceVolume2</p><p>\FileServer\SourceShare2</p>
VM4 | \FileServer\SourceShare2 | PLATA |<p>C:\ClusterStorage\SourceVolume2</p><p>\\FileServer\SourceShare2</p><p>Ambos SILVER_TARGET</p>
VM5 | C:\ClusterStorage\SourceVolume3 | N/A. | Ninguna asignación, por lo que se usa la ubicación de almacenamiento predeterminada del host Hyper-V

## <a name="next-steps"></a>Pasos siguientes

Ahora que ya tiene una mejor comprensión de asignación de almacenamiento, [Prepare implementar la recuperación de sitios de Azure](site-recovery-best-practices.md).
