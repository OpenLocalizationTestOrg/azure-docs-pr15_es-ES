<properties
    pageTitle="Proteger la información general de HDInsight | Microsoft Azure"
    description="Más información sobre..."
    services="hdinsight"
    documentationCenter=""
    authors="saurinsh"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/24/2016"
    ms.author="saurinsh"/>

# <a name="introduce-domain-joined-hdinsight-clusters-preview"></a>Introducir dominio une HDInsight clústeres (vista preliminar)

HDInsight de Azure hasta hoy compatibles con solo un único usuario local Administrador. Esto funcionado más pequeños equipos de aplicación o departamentos de. Hadoop según las cargas de trabajo adquiridas más popularidad en el sector de la empresa, la necesidad de capacidades de calificación de enterprise como basado en active directory autenticación, soporte de varios usuarios y control de acceso basado en roles se convirtió en cada vez más importante. Dominio une HDInsight clústeres puede crear un clúster de HDInsight unido a un dominio de Active Directory, configurar una lista de empleados de la empresa que se puede autenticar a través de Azure Active Directory para iniciar sesión en el clúster de HDInsight. Cualquier persona fuera de la empresa no puede iniciar sesión ni tener acceso al clúster HDInsight. El Administrador de empresa puede configurar el control de acceso basado en roles de seguridad de subárbol utilizando [Apache Cazador](http://hortonworks.com/apache/ranger/), por lo tanto restringir el acceso a datos sólo como sea necesario para. Por último, el administrador puede auditar el acceso a los datos por los empleados y los cambios realizados a las directivas de control de acceso, lo que se consigue un alto grado de gobernanza de recursos de la empresa.

[AZURE.NOTE]> Las nuevas características descritas en esta vista previa sólo están disponibles en clústeres basados en Linux HDInsight de carga de trabajo de sección. Otras las cargas de trabajo, como HBase, motor, tormenta y Kafka, se activará en futuras versiones. 

## <a name="benefits"></a>Ventajas

Seguridad de la empresa contiene cuatro pilares grandes: seguridad de perímetro, autenticación, autorización y cifrado.

![HDInsight de dominio unido clústeres pilares ventajas](./media/hdinsight-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

### <a name="perimeter-security"></a>Seguridad de perímetro

Seguridad de perímetro en HDInsight se logra utilizando redes virtuales y servicio de puerta de enlace. En la actualidad, un administrador de empresa puede crear un clúster de HDInsight dentro de una red virtual y usar grupos de seguridad de la red (reglas de firewall de entrada o salida) para restringir el acceso a la red virtual. Solo las direcciones IP que se definen en las reglas de firewall de entrada podrán comunicarse con el clúster HDInsight, lo que proporciona la seguridad del perímetro. Otro nivel de seguridad de perímetro se logra utilizando el servicio de puerta de enlace. La puerta de enlace es el servicio que actúa como la primera línea de defensa para cualquier solicitud entrante al clúster HDInsight. Acepta la solicitud, valida y solo permite la solicitud pasar a los otros nodos de clúster, lo que proporciona la seguridad del perímetro a otros nodos de datos y el nombre del clúster.

### <a name="authentication"></a>Autenticación

Con esta versión preliminar pública, un administrador de empresa puede aprovisionar un clúster de HDInsight de dominio, en una [red virtual](https://azure.microsoft.com/services/virtual-network/). Los nodos del clúster HDInsight se unirán al dominio administrado por la empresa. Esto se logra mediante el uso de [Servicios de dominio de Azure Active Directory](https://technet.microsoft.com/library/cc770946.aspx). Todos los nodos en el clúster se unen a un dominio que administra la empresa. Con esta configuración, pueden iniciar sesión en los empleados de la empresa a los nodos de clúster con sus credenciales de dominio. También pueden usar sus credenciales de dominio para autenticar con otros extremos aprobados como matiz, Ambari vistas, ODBC, JDBC, PowerShell y las API de REST para interactuar con el clúster. El administrador tiene control total sobre limitar el número de usuarios interactuar con el clúster a través de los extremos.

### <a name="authorization"></a>Autorización

Un procedimiento recomendado seguido por la mayoría de las empresas es que no todos los empleados tienen acceso a todos los recursos de empresa. Del mismo modo, con esta versión, el administrador puede definir directivas de control de acceso basado en roles para los recursos de clúster. Por ejemplo, el administrador puede configurar [Cazador Apache](http://hortonworks.com/apache/ranger/) para establecer directivas de control de acceso de sección. Esta funcionalidad garantiza que los empleados podrán acceder sólo tantos datos como que necesitan para tener éxito en sus tareas. Acceso SSH al clúster también está restringido sólo para el administrador.


### <a name="auditing"></a>Auditoría

Además de proteger los recursos de clúster HDInsight de usuarios no autorizados y protección de los datos, auditoría de todo el acceso a los recursos de clúster y los datos es necesaria realizar un seguimiento de acceso no autorizado o no intencionado de los recursos. Con esta vista previa, el administrador puede ver e informar de todo el acceso a los recursos de clúster de HDInsight y los datos. El administrador también puede ver e informar de todos los cambios a las directivas de control de acceso en extremos de Cazador Apache compatibles. Un clúster de dominio une HDInsight utiliza la interfaz de usuario de Cazador Apache familiares para buscar los registros de auditoría. En el back-end, Cazador utiliza [Apache Solr]( http://hortonworks.com/apache/solr/) para almacenar y buscar los registros.

### <a name="encryption"></a>Cifrado

Proteger los datos es importante para los requisitos de cumplimiento y seguridad de la organización reunión y, a continuación, además de restringir el acceso a los datos de empleados no autorizados, también debe protegerse cifra. La almacena de datos para clústeres, blobs de Windows Azure almacenamiento y el almacenamiento de lago de datos de Azure HDInsight admite transparente servidor [cifrado de datos](../storage/storage-service-encryption.md) en el resto. Proteger HDInsight clústeres sin problemas funcionará con este cifrado en el servidor de datos en función del resto.

## <a name="next-steps"></a>Pasos siguientes

- Para configurar un clúster de dominio HDInsight, consulte [Configurar dominio HDInsight clústeres](hdinsight-domain-joined-configure.md).
- Para administrar clústeres de HDInsight de dominio, vea [Administrar dominio HDInsight clústeres](hdinsight-domain-joined-manage.md).
- Para configurar las directivas de la sección y ejecutar consultas de sección, vea [Configurar subárbol directivas para clústeres HDInsight unido al dominio](hdinsight-domain-joined-run-hive.md).
- Para ejecutar consultas de subárbol con SSH en clústeres HDInsight de dominio, vea [Usar SSH con basados en Linux Hadoop en HDInsight de Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-domain-joined-hdinsight-cluster).