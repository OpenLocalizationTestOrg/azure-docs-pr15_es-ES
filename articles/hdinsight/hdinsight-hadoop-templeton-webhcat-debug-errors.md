<properties
 pageTitle="Entender y resolver los errores de WebHCat en HDInsight"
 description="Aprenda cómo a información sobre los errores comunes de devuelven WebHCat en HDInsight y resolverlos."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
 tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="09/27/2016"
 ms.author="larryfr"/>

#<a name="understand-and-resolve-errors-received-from-webhcat-templeton-on-hdinsight"></a>Entender y resolver los errores recibidos de WebHCat (Templeton), en HDInsight

Al usar WebHCat (anteriormente conocida como Templeton,) para trabajar con HDInsight, recibirá errores. Este documento proporciona instrucciones sobre errores comunes: ¿por qué se producen y qué puede hacer para resolverlos.

##<a name="what-is-webhcat"></a>¿Qué es WebHCat?

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) es una API de REST de capa de administración de [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), una tabla y el almacenamiento de Hadoop. WebHCat está habilitado de forma predeterminada en HDInsight clústeres y varias herramientas sirve para enviar trabajos, obtener el estado del trabajo, etc. sin iniciar sesión en el clúster.

##<a name="modifying-configuration"></a>Modificar la configuración

> [AZURE.IMPORTANT] Algunos de los errores mencionados en este documento se producen porque se ha superado el máximo configurado. Cuando el paso de resolución menciones que puede cambiar un valor, debe usar uno de estos procedimientos para realizar el cambio:

* Para clústeres de **Windows** : usar una acción de secuencia de comandos para configurar el valor durante la creación de un clúster. Para obtener más información, vea [realizar acciones de script](hdinsight-hadoop-script-actions.md).

* Para clústeres **Linux** : usar Ambari (web o API de REST) para modificar el valor. Para obtener más información, vea [Administrar HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md)

###<a name="default-configuration"></a>Configuración predeterminada

Los siguientes son valores de configuración que pueden afectar al rendimiento WebHCat o provocar errores si se sobrepasan estos valores:

| Configuración | Qué hace | Valor predeterminado |
| ------- | ------------ | ------------- |
| [aplicaciones de yarn.Scheduler.Capacity.Maximum][maximum-applications] | El número máximo de tareas que pueden estar activas simultáneamente (pendiente o se está ejecutando) | 10.000 |
| [procedimientos templeton.Exec.max][max-procs] | El número máximo de solicitudes que puede servir de forma simultánea | 20 |
| [edad de MapReduce.jobhistory.Max de ms][max-age-ms] | El número de días que se conservarán el historial de trabajos | 7 días |

##<a name="too-many-requests"></a>Demasiadas solicitudes

**Código de estado HTTP**: 429

| Causa | Resolución |
| ----- | ---------- |
| Se han superado el máximo de solicitudes simultáneas servidas por WebHCat por minuto (el valor predeterminado es 20) | Reducir la carga de trabajo para asegurarse de que no se envíe más que el número máximo de solicitudes simultáneas o aumentar el límite de solicitudes simultáneas modificando `templeton.exec.max-procs`. Obtener más información, consulte [configuración de modificación](#modifying-configuration) |

##<a name="server-unavailable"></a>Servidor no está disponible

**Código de estado HTTP**: 503

| Causa | Resolución |
| ---------------- | ------------------- |
| Esto suele ocurrir durante la migración tras error entre la HeadNode primario y secundario para el clúster | Espere dos minutos y vuelva a intentar la operación |

##<a name="bad-request-content-could-not-find-job"></a>Solicitud incorrecta contenido: no se encontró trabajo

**Código de estado HTTP**: 400

| Causa | Resolución |
| ---------------- | ------------------- |
| Detalles de la tarea se han corregido por el historial de trabajos limpio | El período de retención predeterminado para el historial de trabajo es de 7 días. Esto se puede cambiar modificando `mapreduce.jobhistory.max-age-ms`. Obtener más información, consulte [configuración de modificación](#modifying-configuration) |
| Trabajo se cerró debido a un error | Vuelva a intentar el envío de trabajos de dos minutos |
| Se ha usado un identificador de trabajo no válido | Compruebe si el id de trabajo es correcto |

##<a name="bad-gateway"></a>Puerta de enlace incorrecta

**Código de estado HTTP**: 502

| Causa | Resolución |
| ---------------- | ------------------- |
| Recolección interno se produce dentro del proceso de WebHCat | Esperar recolección finalizar o reiniciar el servicio de WebHCat |
| Tiempo de espera de respuesta del servicio de ResourceManager. Esto puede ocurrir cuando el número de aplicaciones activas pasa el máximo configurado (el valor predeterminado es 10.000) | Espere a que se está ejecutando trabajos para completar o aumentar el límite de trabajo simultáneas modificando `yarn.scheduler.capacity.maximum-applications`. Obtener más información, consulte [configuración de modificación](#modifying-configuration)  |
| Al intentar recuperar todas las tareas a través de la llamada [obtener /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) mientras `Fields` se establece en`*` | No recuperar *todos los* detalles del trabajo, en su lugar use `jobid` para recuperar los detalles de tareas solo mayores que determinadas id de trabajo. O bien, utilice`Fields` |
| El servicio WebHCat está inactivo durante la migración tras error de HeadNode | Espere dos minutos y vuelva a intentar la operación |
| Hay más de 500 tareas pendientes enviadas a través de WebHCat | Espere a que trabajos pendientes se completan antes de enviar más trabajos |

[maximum-applications]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://hive.apache.org/javadocs/hcat-r0.5.0/configuration.html
[max-age-ms]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
 
