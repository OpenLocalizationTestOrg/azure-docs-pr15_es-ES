<properties
    pageTitle="Ejecutar Casandra con Linux en Azure | Microsoft Azure"
    description="Cómo ejecutar un clúster de Casandra en Linux en Azure máquinas virtuales de Windows desde una aplicación de Node.js"
    services="virtual-machines-linux"
    documentationCenter="nodejs"
    authors="hanuk"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="hanuk;robmcm"/>

# <a name="running-cassandra-with-linux-on-azure-and-accessing-it-from-nodejs"></a>Ejecutar Casandra con Linux en Azure y tener acceso a él desde Node.js

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Obtenga información sobre cómo llevar a [cabo estos pasos con el modelo de administrador de recursos](https://azure.microsoft.com/documentation/templates/datastax-on-ubuntu/).

## <a name="overview"></a>Información general
Microsoft Azure es una plataforma de nube abierta que se ejecuta Microsoft como así como no software de Microsoft que incluye sistemas operativos, servidores de aplicaciones, software intermedio mensajería, así como bases de datos SQL y NoSQL de ambos modelos comercial y de código abierto. Crear y son resistentes servicios en nubes públicas incluidos Azure requiere minucioso y arquitectura deliberadamente para los servidores de aplicaciones como capas de almacenamiento bien. Arquitectura de almacenamiento distribuido del Casandra naturalmente le ayuda a crear sistemas altamente disponibles que están tolerancia de errores de clúster. Casandra es una escala de nube NoSQL base de datos mantenido por Apache Software Foundation en cassandra.apache.org; Casandra escrito en Java y, por tanto, ejecuta en ambos en Windows, así como Linux plataformas.

El enfoque de este artículo es mostrar la implementación de Casandra en Ubuntu como un clúster de centro única y datos múltiples aprovechar máquinas virtuales de Microsoft Azure y redes virtuales. La implementación de clúster para cargas de trabajo de producción optimizada está fuera del ámbito de este artículo ya que requiere la configuración de nodo de disco múltiples, diseño de la topología de anillo apropiada y modelado para admitir la replicación necesaria, coherencia de los datos, rendimiento y requisitos de alta disponibilidad de datos.

Esto lleva artículo un enfoque fundamental para mostrar lo que implica crear el clúster Casandra compara Docker, Chef o posición libre lo que puede hacer que la implementación de infraestructura mucho más fácil.  

## <a name="the-deployment-models"></a>Los modelos de implementación
Redes de Microsoft Azure permiten la implementación de aislado clústeres privados, el acceso de los cuales se puede restringir para lograr la seguridad de la red minucioso.  Dado que este artículo trata sobre que muestra la implementación de Casandra en un nivel fundamental, no nos centraremos en el nivel de coherencia y el diseño de almacenamiento óptimo de rendimiento. Aquí encontrará la lista de requisitos de nuestro clúster hipotética de red:

- Sistemas externos no pueden acceder Casandra base de datos desde dentro o fuera de Azure
- Clúster de Casandra debe estar detrás de un equilibrador de carga para el tráfico de thrift
- Implementar nodos de Casandra en dos grupos en cada centro de datos para una disponibilidad de clúster mejorado
- Bloquear el clúster lo solo granja de servidores de aplicación tiene acceso a la base de datos directamente
- Sin extremos de redes públicas que no sean SSH
- Cada nodo de Casandra necesita una dirección IP interna fija

Casandra puede distribuirse a una sola región Azure o para varias regiones en función de la naturaleza distribuida de la carga de trabajo. Modelo de implementación de múltiples región puede aprovecharse para servir a los usuarios finales más cerca una geografía determinada a través de la misma infraestructura de Casandra. Se encarga de Casandra nodo integrado replicación de la sincronización de varios maestros escribe procedentes de varios centros de datos y ofrece una vista coherente de los datos en aplicaciones. Implementación de múltiples región también puede ayudar con la reducción de riesgos la más amplia Azure interrupción del servicio. Coherencia de ajustables de Casandra y la topología de replicación le ayudará a para satisfacer las necesidades RPO diversas de aplicaciones.

### <a name="single-region-deployment"></a>Implementación de única región
Se iniciará con una implementación única región y recoger las lecciones para crear un modelo de múltiples región. Una red virtual Azure se utilizará para crear subredes aisladas para que se pueden cumplir los requisitos de seguridad de la red mencionados arriba.  El proceso descrito en la creación de la implementación de región solo usa Ubuntu 14.04 LTADOS y Casandra 2.08; Sin embargo, el proceso se pueden adoptar fácilmente a las otras variantes de Linux. Las siguientes son algunas de las características de la implementación única región sistemáticas.  

**Alta disponibilidad:** Los nodos de Casandra que se muestra en la figura 1 se implementan en dos conjuntos de disponibilidad para que los nodos se extienden entre varios dominios de error de alta disponibilidad. Máquinas virtuales anotadas con cada conjunto de disponibilidad se asigna a los dominios de error 2.  Usos de Microsoft Azure el concepto de dominio de errores para administrar tiempo planificado hacia abajo (por ejemplo, los errores de software o hardware) mientras el concepto de dominio de actualización (por ejemplo, host o invitado revisiones y actualizaciones de sistema operativo, actualizaciones de las aplicaciones) se usa para la administración de programar tiempo de inactividad. Consulte [recuperación y alta disponibilidad para las aplicaciones de Azure](http://msdn.microsoft.com/library/dn251004.aspx) para la función de error y actualizar los dominios en lograr alta disponibilidad.

![Implementación de única región](./media/virtual-machines-linux-classic-cassandra-nodejs/cassandra-linux1.png)

Figura 1: Implementación de única región

Tenga en cuenta que en el momento de escribir este documento, Azure no permite la asignación explícita de un grupo de máquinas virtuales a un dominio de error específico; Por consiguiente, incluso con el modelo de implementación que se muestra en la figura 1, es estadística probable que todas las máquinas virtuales se pueden asignar a dos dominios de error en lugar de cuatro.

**Thrift tráfico de equilibrio de carga:** Bibliotecas de cliente de thrift dentro del servidor web que se conectan al clúster a través de un equilibrador de carga interno. Es necesario que el proceso de adición del equilibrador de carga interno a la subred "datos" (consulte la figura 1) en el contexto del servicio de nube aloja el clúster Casandra. Una vez que se define el equilibrador de carga interno, cada nodo requiere el extremo de equilibrio de carga se agregue con las anotaciones de un conjunto de equilibrio de carga con el nombre del equilibrador de carga definidas previamente. Para obtener más detalles, vea [Equilibrio de carga interna de Azure ](../load-balancer/load-balancer-internal-overview.md).

**Semillas de clúster:** Es importante seleccionar la mayoría de los nodos altamente disponible para semillas como los nuevos nodos se comunicarán con los nodos de valor de inicialización para detectar la topología de clúster. Un nodo de cada conjunto de disponibilidad está designado como nodos de valor de inicialización para evitar el punto de error.

**Nivel de coherencia y Factor de replicación:** Generación de datos y alta disponibilidad duración de Casandra se caracteriza por el Factor de replicación (RN - número de copias de cada fila almacenada en el clúster) y el nivel de coherencia (número de réplicas a ser leídos/escritos antes de devolver el resultado al llamador). Factor de replicación se especifica durante la creación de espacio de claves (similar a una base de datos relacional) mientras que se especifica el nivel de coherencia al emitir la consulta CRUD. Consulte la documentación de Casandra en [Configurar coherencia](http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) para obtener detalles de la coherencia y la fórmula de cálculo de quórum.

Casandra es compatible con dos tipos de modelos de integridad de datos: coherencia y la coherencia; el Factor de replicación y el nivel de coherencia se determinan si los datos serán coherentes tan pronto como una operación de escritura o será finalmente coherente. Por ejemplo, especificar QUÓRUM así como el nivel de coherencia siempre que asegura de datos coherencia mientras cualquier nivel de coherencia, debajo de la cantidad de réplicas que se escriban según sea necesario para obtener resultados de QUÓRUM (por ejemplo, uno) que estén finalmente coherente datos.

El clúster de 8 nodos mostrado arriba, con un factor de replicación de 3 y QUÓRUM (2 nodos leer o escritos coherencia) el nivel de coherencia de lectura y escritura, puede superar la pérdida teórica de más de 1 nodo por grupo de replicación antes de advertir el error de inicio de la aplicación. Se supone que todos los espacios claves también disponen de equilibrio lectura-escritura solicitudes.  Los siguientes son los parámetros que se utilizará para el clúster implementado:

Configuración de clúster de Casandra única región:

| Parámetros de clúster | Valor | Notas |
| ----------------- | ----- | ------- |
| Número de nodos (N) | 8   | Número total de nodos en el clúster |
| Factor de replicación (RN) | 3 | Número de réplicas de una fila determinada. |
| Nivel de coherencia (escritura) | QUORUM[(RF/2) +1) = 2] el resultado de la fórmula se redondea hacia abajo | Escribe más 2 réplicas antes de enviar la respuesta a la persona que llama; 3er réplica se escribe de forma coherente al final. |
| Nivel de coherencia (lectura) | QUÓRUM [(RF/2) = 2 + 1] el resultado de la fórmula se redondea hacia abajo | Lee 2 réplicas antes de enviar la respuesta a la persona que llama. |
| Estrategia de replicación | NetworkTopologyStrategy vea [Replicación de datos](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) en la documentación de Casandra para obtener más información | Comprenda la topología de implementación y coloca réplicas en nodos de forma que todas las réplicas no terminen en el mismo bastidor |
| Snitch | GossipingPropertyFileSnitch vea [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) en la documentación de Casandra para obtener más información | NetworkTopologyStrategy utiliza un concepto de snitch para comprender la topología. GossipingPropertyFileSnitch proporciona un mejor control en cada nodo de asignaciones para bastidor y centro de datos. A continuación, el clúster utiliza cotilleos propagar esta información. Esto es mucho más sencillo de ajuste IP dinámico en relación con PropertyFileSnitch |


**Azure consideraciones para Casandra clúster:** Capacidad de máquinas virtuales de Microsoft Azure usa almacenamiento de blobs de Windows Azure para persistencia de disco; Almacenamiento de Azure guarda 3 réplicas de cada disco para duración alta. Esto significa que cada fila de datos que se inserta en una tabla de Casandra ya está almacenada en 3 réplicas y, por tanto, coherencia de los datos está ya controlado incluso si el Factor de replicación (RN) es 1. El problema principal con Factor de replicación 1 es la aplicación experimentará inactividad incluso si se produce un error en un único nodo Casandra. Sin embargo, si un nodo hacia abajo para solucionar los problemas (por ejemplo, hardware, errores del software de sistema) reconocidos en Azure tela controlador, proporcionará un nuevo nodo en su lugar con las mismas unidades de almacenamiento. Aprovisionamiento de un nuevo nodo para reemplazar el antiguo puede tardar unos minutos.  De forma similar para las actividades de mantenimiento planeado como invitado OS cambios, actualizaciones de Casandra y cambios en la aplicación controlador de tela de Azure realiza distribuir las actualizaciones de los nodos en el clúster.  Actualización sucesiva también puede tardar unos nodos en un momento y, por tanto, el clúster puede experimentar inactividad breve para algunas particiones. Sin embargo, los datos no se perderán debido a la redundancia integrada de almacenamiento de Azure.  

Para sistemas implementados en Azure que no requiere alta disponibilidad (por ejemplo, alrededor de 99,9 que equivale a 8,76 horas por año; consulte [Alta disponibilidad](http://en.wikipedia.org/wiki/High_availability) para obtener información detallada), es posible que pueda ejecutar con RN = 1 y nivel de coherencia = uno.  Para las aplicaciones con requisitos de alta disponibilidad, RN = 3 y nivel de coherencia = QUÓRUM admiten el tiempo de inactividad de uno de los nodos de las réplicas. RN = 1 en tradicional implementaciones (por ejemplo, local) no se pueden usar debido a la pérdida de datos posible es el resultado de problemas, como los errores de disco.   

## <a name="multi-region-deployment"></a>Implementación de múltiples región
Para datos centro replicación y la coherencia modelo de Casandra descrito anteriormente ayuda con la implementación de múltiples región fuera del cuadro sin necesidad de las herramientas externas. Esto es muy diferente de las bases de datos relacionales tradicionales donde la configuración de la base de datos reflejada para escritura múltiples maestro puede ser bastante compleja. Casandra en una región múltiples configurar le ayudarán con los escenarios de uso, incluidos los siguientes:

**Proximidad según su implementación:** Aplicaciones de múltiples arrendatarios, con borrar la asignación de los usuarios del inquilino-al-región, se puede obtenido por la latencia baja del clúster múltiples región. Por ejemplo, una administración de aprendizaje para instituciones educativas sistemas pueden implementar un clúster distribuido en regiones de Estados Unidos oriental y occidental de Estados Unidos para servir el campus respectivo para transacciones, así como el análisis. Los datos pueden ser coherentes localmente en el tiempo lee y escribe y pueden ser finalmente coherentes entre las áreas de ambos. Hay otros ejemplos como media, comercio electrónico y distribución nada y todo lo que sirve geo concentrado usuario base es un buen caso de uso para este modelo de implementación.

**Alta disponibilidad:** La redundancia es un factor clave para lograr alta disponibilidad de software y hardware; Para obtener más información, consulte Creación de sistemas de nube confiable en Microsoft Azure. En Microsoft Azure, la manera solo confiable de lograr redundancia verdadero es implementar un clúster de múltiples región. Se pueden implementar aplicaciones en modo activo / activo o activo pasivo y si una de las regiones no funciona, el Administrador de tráfico de Azure puede redirigir el tráfico a la región activa.  Con la implementación única región, si la disponibilidad es 99,9, una implementación de dos región puede alcanzar una disponibilidad de 99.9999 a calcular la fórmula: (1-(1-0.999) *(1-0,999))*100); Consulte la nota anterior para obtener información detallada.

**Recuperación:** Clúster de múltiples región Casandra, si bien diseñada, puede resistir interrupciones del centro de datos grave. Si una región es hacia abajo, puede iniciar la aplicación que se implementa en otras regiones atendiendo a los usuarios finales. Al igual que cualquier otra implementación de continuidad empresarial, la aplicación debe ser tolerancia a errores de algunos perder los datos procedentes de los datos de la canalización de asincrónica. Sin embargo, Casandra hace que la recuperación mucho swifter que el tiempo necesario por los procesos de recuperación de bases de datos tradicionales. Figura 2 muestra el modelo de implementación de región múltiples típica con ocho nodos en cada región. Ambas regiones son imágenes reflejadas entre sí para la misma de simetría; diseños del mundo real dependen del tipo de carga de trabajo (por ejemplo, transacciones o analítica), RPO, RTO, coherencia de los datos y los requisitos de disponibilidad.

![Implementación de la región de múltiples](./media/virtual-machines-linux-classic-cassandra-nodejs/cassandra-linux2.png)

Figura 2: Implementación de múltiples región Casandra

### <a name="network-integration"></a>Integración de red
Conjuntos de máquinas virtuales implementados a redes privadas que se encuentra en dos regiones se comunica con otros mediante un túnel VPN. Túnel VPN conecta dos puertas de enlace de software que se aprovisione durante el proceso de implementación de red. Ambas regiones tienen una arquitectura de red similar en términos de subredes "web" y "datos"; Redes Azure permite la creación de subredes tantas según sea necesario y aplicar ACL según sea necesario por la seguridad de la red. Al diseñar la topología de clúster entre latencia de comunicación de centro de datos y el impacto económico del tráfico de red que debe considerar.

### <a name="data-consistency-for-multi-data-center-deployment"></a>Coherencia de los datos para su implementación del centro de datos múltiples
Distribuido implementaciones necesita conocer el impacto de la topología de clúster de alta disponibilidad y rendimiento. El nivel de coherencia y RN deben seleccionarse de manera que el quórum no depende de la disponibilidad de todos los centros de datos.
Para un sistema que necesita coherencia alta, un LOCAL_QUORUM para el nivel de coherencia (para lectura y escritura) se asegurarse de que la local lee y escribe se cumple de los nodos locales mientras datos es replicar asincrónica en los centros de datos remota.  Tabla 2 resume los detalles de configuración para el clúster de región múltiples descritos más adelante en la escritura hacia arriba.

**Configuración de clúster de región de dos Casandra**


| Parámetros de clúster | Valor | Notas |
| ----------------- | ----- | ------- |
| Número de nodos (N) | 8 + 8 | Número total de nodos en el clúster |
| Factor de replicación (RN) | 3 | Número de réplicas de una fila determinada. |
| Nivel de coherencia (escritura) | LOCAL_QUORUM [(sum(RF)/2) +1) = 4] el resultado de la fórmula se redondea hacia abajo | 2 nodos se escriben en el centro de datos de la primera forma sincrónica; 2 nodos adicionales necesarios para quórum escribirá asincrónica en el centro de datos 2 º. |
| Nivel de coherencia (lectura) | LOCAL_QUORUM ((RF/2) + 1) = 2, el resultado de la fórmula se redondea hacia abajo | Se cumplen las solicitudes de lectura de solo una región; 2 nodos se leen antes de enviar la respuesta al cliente. |
| Estrategia de replicación | NetworkTopologyStrategy vea [Replicación de datos](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) en la documentación de Casandra para obtener más información | Comprenda la topología de implementación y coloca réplicas en nodos de forma que todas las réplicas no terminen en el mismo bastidor |
| Snitch | GossipingPropertyFileSnitch vea [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) en la documentación de Casandra para obtener más información | NetworkTopologyStrategy utiliza un concepto de snitch para comprender la topología. GossipingPropertyFileSnitch proporciona un mejor control en cada nodo de asignaciones para bastidor y centro de datos. A continuación, el clúster utiliza cotilleos propagar esta información. Esto es mucho más sencillo de ajuste IP dinámico en relación con PropertyFileSnitch |


##<a name="the-software-configuration"></a>LA CONFIGURACIÓN DE SOFTWARE
Las siguientes versiones de software se usan durante la implementación:

<table>
<tr><th>Software</th><th>Origen</th><th>Versión</th></tr>
<tr><td>JRE </td><td>[JRE 8](http://www.oracle.com/technetwork/java/javase/downloads/server-jre8-downloads-2133154.html) </td><td>8U5</td></tr>
<tr><td>JNA </td><td>[JNA](https://github.com/twall/jna) </td><td> 3.2.7</td></tr>
<tr><td>Casandra</td><td>[Apache Casandra 2.0.8](http://www.apache.org/dist/cassandra/2.0.8/apache-cassandra-2.0.8-bin.tar.gz)</td><td> 2.0.8</td></tr>
<tr><td>Ubuntu  </td><td>[Microsoft Azure](https://azure.microsoft.com/) </td><td>14.04 LTADOS</td></tr>
</table>

Puesto que la descarga de JRE requiere aceptación manual de licencia de Oracle, para simplificar la implementación, descargar todo el software necesario en el escritorio para cargar más adelante en la imagen de la plantilla de Ubuntu que crearemos como precursores de la implementación de clúster.

Descargue este software en un directorio de descarga conocido (por ejemplo, %TEMP%/downloads en Windows o ~/Downloads en la mayoría de las distribuciones de Linux o Mac) en el equipo local.

### <a name="create-ubuntu-vm"></a>CREAR UBUNTU VM
En este paso del proceso crearemos Ubuntu imagen con el software mínimos necesario para que puedan reutilizarse para aprovisionar varios nodos de Casandra la imagen.  
####<a name="step-1-generate-ssh-key-pair"></a>PASO 1: Generar de par de claves SSH
Azure necesita una clave pública PEM o DER codificado en el momento de aprovisionamiento de X509. Generar un par de claves públicas y privadas siguiendo las instrucciones que se encuentra en cómo usar SSH con Linux en Azure. Si planea usar putty.exe como un cliente SSH en Windows o Linux, debe convertir el PEM codificado clave privada RSA al formato PPK con puttygen.exe; las instrucciones para esto pueden encontrarse en la página web anterior.

####<a name="step-2-create-ubuntu-template-vm"></a>PASO 2: Crear la plantilla de Ubuntu VM
Para crear la plantilla de máquina virtual, inicie sesión en el portal de clásico Azure y usar la siguiente secuencia: haga clic en nuevo, cálculo, máquina VIRTUAL, de galería, UBUNTU, Ubuntu Server 14.04 LTADOS y, a continuación, haga clic en la flecha derecha. Para obtener un tutorial que describe cómo crear una VM Linux, consulte Crear Máquina Virtual ejecuta Linux.

En la pantalla de "configuración de máquina Virtual" #1, escriba la información siguiente:

<table>
<tr><th>NOMBRE DE CAMPO              </td><td>       VALOR DEL CAMPO               </td><td>         NOTAS                </td><tr>
<tr><td>FECHA DE LANZAMIENTO DE VERSIÓN    </td><td> Seleccione una fecha de la drow hacia abajo</td><td></td><tr>
<tr><td>NOMBRE DE LA MÁQUINA VIRTUAL    </td><td> plantilla de Cass                </td><td> Este es el nombre de host de la máquina virtual </td><tr>
<tr><td>NIVEL                     </td><td> ESTÁNDAR                        </td><td> Deje el valor predeterminado              </td><tr>
<tr><td>TAMAÑO                     </td><td> A1                              </td><td>Seleccione la máquina virtual según las necesidades de IO; Para ello, deje el valor predeterminado </td><tr>
<tr><td> NUEVO NOMBRE DE USUARIO           </td><td> LocalAdmin                      </td><td> "administrador" es un nombre de usuario reservado en Ubuntu 12. xx y después</td><tr>
<tr><td> AUTENTICACIÓN      </td><td> Haga clic en la casilla de verificación                 </td><td>Compruebe si desea proteger con una clave SSH </td><tr>
<tr><td> CERTIFICADO             </td><td> nombre de archivo de certificado de clave pública </td><td> Usar la clave pública generada previamente</td><tr>
<tr><td> Nueva contraseña   </td><td> contraseña segura </td><td> </td><tr>
<tr><td> Confirmar contraseña   </td><td> contraseña segura </td><td></td><tr>
</table>

Escriba la información siguiente en la pantalla de "configuración de máquina Virtual" #2:

<table>
<tr><th>NOMBRE DE CAMPO             </th><th> VALOR DEL CAMPO                       </th><th> NOTAS                                 </th></tr>
<tr><td> SERVICIO DE NUBE  </td><td> Crear un nuevo servicio de nube    </td><td>Servicio de nube es un recursos de cálculo contenedor como máquinas virtuales de Windows</td></tr>
<tr><td> NOMBRE DE DNS DEL SERVICIO DE NUBE </td><td>Ubuntu template.cloudapp.net   </td><td>Asigne un nombre de equilibrador de carga independiente de equipo</td></tr>
<tr><td> REGIÓN/GRUPO AFINIDAD/RED VIRTUAL </td><td>    Estados Unidos occidental </td><td> Seleccione una región desde el que las aplicaciones web acceso al clúster de Casandra</td></tr>
<tr><td>CUENTA DE ALMACENAMIENTO </td><td>   Utilizar predeterminado </td><td>Usar la cuenta de almacenamiento predeterminada o una cuenta de almacenamiento creado previamente en una región determinada</td></tr>
<tr><td>CONJUNTO DE DISPONIBILIDAD </td><td>  Ninguno </td><td>  Deje en blanco</td></tr>
<tr><td>EXTREMOS   </td><td>Utilizar predeterminado </td><td>  Usar la configuración predeterminada de SSH </td></tr>
</table>

Haga clic en la flecha derecha, deje los valores predeterminados en la pantalla #3 y haga clic en el botón "revisar" para completar el proceso de aprovisionamiento de VM. Después de unos minutos, la máquina virtual con el nombre "ubuntu plantilla" debe estar en un estado de "ejecutar".

###<a name="install-the-necessary-software"></a>INSTALAR EL SOFTWARE NECESARIO
####<a name="step-1-upload-tarballs"></a>PASO 1: Cargar tarballs
Usa scp o pscp, copie el software descargado anteriormente al directorio de ~/downloads con el siguiente formato de comando:

#####<a name="pscp-server-jre-8u5-linux-x64targz-localadminhk-cas-templatecloudappnethomelocaladmindownloadsserver-jre-8u5-linux-x64targz"></a>pscp server-jre-8u5-linux-x64.tar.gzlocaladmin@hk-cas-template.cloudapp.net:/home/localadmin/downloads/server-jre-8u5-linux-x64.tar.gz

Repita el comando anterior para JRE, así como los bits de Casandra.

####<a name="step-2-prepare-the-directory-structure-and-extract-the-archives"></a>PASO 2: Preparar la estructura de directorio y extraer los archivos
Inicie sesión en la máquina virtual y crear la estructura de directorios y extraer software como un usuario con la siguiente secuencia de comandos de fiesta:

    #!/bin/bash
    CASS_INSTALL_DIR="/opt/cassandra"
    JRE_INSTALL_DIR="/opt/java"
    CASS_DATA_DIR="/var/lib/cassandra"
    CASS_LOG_DIR="/var/log/cassandra"
    DOWNLOADS_DIR="~/downloads"
    JRE_TARBALL="server-jre-8u5-linux-x64.tar.gz"
    CASS_TARBALL="apache-cassandra-2.0.8-bin.tar.gz"
    SVC_USER="localadmin"

    RESET_ERROR=1
    MKDIR_ERROR=2

    reset_installation ()
    {
       rm -rf $CASS_INSTALL_DIR 2> /dev/null
       rm -rf $JRE_INSTALL_DIR 2> /dev/null
       rm -rf $CASS_DATA_DIR 2> /dev/null
       rm -rf $CASS_LOG_DIR 2> /dev/null
    }
    make_dir ()
    {
       if [ -z "$1" ]
       then
          echo "make_dir: invalid directory name"
          exit $MKDIR_ERROR
       fi

       if [ -d "$1" ]
       then
          echo "make_dir: directory already exists"
          exit $MKDIR_ERROR
       fi

       mkdir $1 2>/dev/null
       if [ $? != 0 ]
       then
          echo "directory creation failed"
          exit $MKDIR_ERROR
       fi
    }

    unzip()
    {
       if [ $# == 2 ]
       then
          tar xzf $1 -C $2
       else
          echo "archive error"
       fi

    }

    if [ -n "$1" ]
    then
       SVC_USER=$1
    fi

    reset_installation
    make_dir $CASS_INSTALL_DIR
    make_dir $JRE_INSTALL_DIR
    make_dir $CASS_DATA_DIR
    make_dir $CASS_LOG_DIR

    #unzip JRE and Cassandra
    unzip $HOME/downloads/$JRE_TARBALL $JRE_INSTALL_DIR
    unzip $HOME/downloads/$CASS_TARBALL $CASS_INSTALL_DIR

    #Change the ownership to the service credentials

    chown -R $SVC_USER:$GROUP $CASS_DATA_DIR
    chown -R $SVC_USER:$GROUP $CASS_LOG_DIR
    echo "edit /etc/profile to add JRE to the PATH"
    echo "installation is complete"


Si pega esta secuencia de comandos en la ventana de vim, asegúrese de quitar el retorno de carro ('\r ") mediante el siguiente comando:

    tr -d '\r' <infile.sh >outfile.sh

####<a name="step-3-edit-etcprofile"></a>Paso 3: Editar etcetera/perfil
Anexar la siguiente al final:

    JAVA_HOME=/opt/java/jdk1.8.0_05
    CASS_HOME= /opt/cassandra/apache-cassandra-2.0.8
    PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$CASS_HOME/bin
    export JAVA_HOME
    export CASS_HOME
    export PATH

####<a name="step-4-install-jna-for-production-systems"></a>Paso 4: Instalar JNA para sistemas de producción
Usar la siguiente secuencia de comandos: el siguiente comando instalará jna 3.2.7.jar y jna-plataforma-3.2.7.jar al directorio de /usr/share.java sudo apt-get instalar java libjna

Crear vínculos simbólicos en el directorio de $CASS_HOME/biblioteca para que el script de inicio de Casandra puede encontrar estos jars:

    ln -s /usr/share/java/jna-3.2.7.jar $CASS_HOME/lib/jna.jar

    ln -s /usr/share/java/jna-platform-3.2.7.jar $CASS_HOME/lib/jna-platform.jar

####<a name="step-5-configure-cassandrayaml"></a>Paso 5: Configurar cassandra.yaml
Editar cassandra.yaml en cada máquina virtual para reflejar la configuración necesario para todas las máquinas virtuales [que se ajuste durante el aprovisionamiento real]:

<table>
<tr><th>Nombre de campo   </th><th> Valor  </th><th> Notas </th></tr>
<tr><td>nombre_clúster </td><td>  "CustomerService"   </td><td> Use el nombre que refleje su implementación</td></tr>
<tr><td>listen_address  </td><td>[deje en blanco]   </td><td> Eliminar "host local" </td></tr>
<tr><td>rpc_addres   </td><td>[deje en blanco]  </td><td> Eliminar "host local" </td></tr>
<tr><td>semillas   </td><td>"10.1.2.4, 10.1.2.6, 10.1.2.8" </td><td>Lista de todas las direcciones IP que se hayan designado como base.</td></tr>
<tr><td>endpoint_snitch </td><td> org.apache.cassandra.locator.GossipingPropertyFileSnitch </td><td> Se utiliza por el NetworkTopologyStrateg para deducir el centro de datos y el bastidor de la máquina virtual</td></tr>
</table>

####<a name="step-6-capture-the-vm-image"></a>Paso 6: Capturar la imagen de VM
Inicie sesión en la máquina virtual con el nombre de host (template.cloudapp.net de entidades emisoras de certificados de hk) y la clave privada SSH creado previamente. Vea cómo usar SSH con Linux en Azure para más información sobre cómo iniciar sesión con el comando ssh o putty.exe.

Ejecutar la siguiente secuencia de acciones para capturar la imagen:
#####<a name="1-deprovision"></a>1. deprovision
Use el comando "sudo waagent: deprovision + usuario" Quitar información específica de instancia de máquina Virtual. Ver [cómo capturar una máquina Virtual Linux](virtual-machines-linux-classic-capture-image.md) usar como una plantilla más detalles sobre el proceso de captura de imagen.

#####<a name="2-shutdown-the-vm"></a>2: cierre la máquina virtual
Asegúrese de que la máquina virtual está resaltada y haga clic en el vínculo de cierre de la barra de comandos de la parte inferior.

#####<a name="3-capture-the-image"></a>3: capturar la imagen
Asegúrese de que la máquina virtual está resaltada y haga clic en el vínculo de captura de la barra de comandos de la parte inferior. En la siguiente pantalla, asigne un nombre de imagen (por ejemplo, hk-cas-2-08-ub-14-04-2014071), proceda de la descripción de la imagen y haga clic en la "" marca de verificación para finalizar el proceso de captura.

Tardará unos segundos y la imagen debe estar disponible en la sección Mis imágenes de la Galería de imágenes. El origen de VM estará automáticamente delated después de que la imagen se captura correctamente.

##<a name="single-region-deployment-process"></a>Proceso de implementación de única región
**Paso 1: crear la red Virtual** Inicie sesión en el portal de clásico Azure y cree una red Virtual con la presentación de atributos de la tabla. Vea [Configurar una red Virtual Cloud-Only en el portal de clásico Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md) para conocer los pasos detallados del proceso.      

<table>
<tr><th>Nombre del atributo VM</th><th>Valor</th><th>Notas</th></tr>
<tr><td>Nombre</td><td>vnet-cass-oeste-us</td><td></td></tr>
<tr><td>Región</td><td>Estados Unidos occidental</td><td></td></tr>
<tr><td>Servidores DNS </td><td>Ninguno</td><td>Omitir esta como no usamos un servidor DNS</td></tr>
<tr><td>Configurar una conexión VPN punto al sitio</td><td>Ninguno</td><td> Omitir esta</td></tr>
<tr><td>Configurar una VPN de sitio a otro</td><td>Nnone</td><td> Omitir esta</td></tr>
<tr><td>Espacio de direcciones</td><td>10.1.0.0/16</td><td></td></tr>
<tr><td>Iniciar IP</td><td>red dividida 10.1.0.0</td><td></td></tr>
<tr><td>CIDR </td><td>/ 16 (65531)</td><td></td></tr>
</table>

Agregue las siguientes subredes:

<table>
<tr><th>Nombre</th><th>Iniciar IP</th><th>CIDR</th><th>Notas</th></tr>
<tr><td>Web</td><td>10.1.1.0</td><td>/ 24 (251)</td><td>Subred para el conjunto de servidores web</td></tr>
<tr><td>datos</td><td>10.1.2.0</td><td>/ 24 (251)</td><td>Subred para los nodos de base de datos</td></tr>
</table>

Datos y subredes Web se pueden proteger mediante grupos de seguridad de red la cobertura del que está fuera del ámbito de este artículo.  

**Paso 2: aprovisionar máquinas virtuales** Con la imagen que creó anteriormente, se crearán las siguientes máquinas virtuales en el servidor de nube "hk c-svc Oeste" y enlazarlos a subred tal como se muestra a continuación:

<table>
<tr><th>Nombre del equipo    </th><th>Subred </th><th>Dirección IP </th><th>Conjunto de disponibilidad</th><th>Bastidor/DC</th><th>¿Valor de inicialización?</th></tr>
<tr><td>HK-c1-oeste-us   </td><td>datos   </td><td>10.1.2.4   </td><td>HK-c-un conjunto-1    </td><td>DC = bastidor WESTUS = cada rack1 </td><td>Sí</td></tr>
<tr><td>HK-c2-oeste-us   </td><td>datos   </td><td>10.1.2.5   </td><td>HK-c-un conjunto-1    </td><td>DC = bastidor WESTUS = cada rack1 </td><td>No </td></tr>
<tr><td>HK-c3-oeste-us   </td><td>datos   </td><td>10.1.2.6   </td><td>HK-c-un conjunto-1    </td><td>DC = bastidor WESTUS = rack2 </td><td>Sí</td></tr>
<tr><td>HK-c4-oeste-us   </td><td>datos   </td><td>10.1.2.7   </td><td>HK-c-un conjunto-1    </td><td>DC = bastidor WESTUS = rack2 </td><td>No </td></tr>
<tr><td>HK-c5-oeste-us   </td><td>datos   </td><td>10.1.2.8   </td><td>HK-c-un conjunto-2    </td><td>DC = bastidor WESTUS = rack3 </td><td>Sí</td></tr>
<tr><td>HK-c6-oeste-us   </td><td>datos   </td><td>10.1.2.9   </td><td>HK-c-un conjunto-2    </td><td>DC = bastidor WESTUS = rack3 </td><td>No </td></tr>
<tr><td>HK-c7-oeste-us   </td><td>datos   </td><td>10.1.2.10  </td><td>HK-c-un conjunto-2    </td><td>DC = bastidor WESTUS = rack4 </td><td>Sí</td></tr>
<tr><td>HK-c8-oeste-us   </td><td>datos   </td><td>10.1.2.11  </td><td>HK-c-un conjunto-2    </td><td>DC = bastidor WESTUS = rack4 </td><td>No </td></tr>
<tr><td>HK-B1-oeste-us   </td><td>Web    </td><td>10.1.1.4   </td><td>HK-w-un conjunto-1    </td><td>                       </td><td>N/A.</td></tr>
<tr><td>HK-w2-oeste-us   </td><td>Web    </td><td>10.1.1.5   </td><td>HK-w-un conjunto-1    </td><td>                       </td><td>N/A.</td></tr>
</table>

Creación de la lista anterior de VM requiere el proceso siguiente:

1.  Crear un servicio de nube vacía en una región determinada
2.  Crear una máquina virtual de la imagen capturada anteriormente y adjuntar a la red virtual creada previamente; Repita este paso para todas las máquinas virtuales
3.  Agregar un equilibrador de carga interno del servicio de nube y adjuntar a la subred "datos"
4.  Para cada VM creada anteriormente, agregue un extremo de equilibrio de carga de tráfico de thrift a través de un conjunto de equilibrio de carga conectado al equilibrador de carga interno creado previamente

Puede ejecutar el proceso anterior con Azure portal clásica; usar una máquina de Windows (usar una máquina virtual en Azure si no tiene acceso a un equipo Windows), use el siguiente script de PowerShell para aprovisionar todas máquinas 8 virtuales automáticamente.

**Lista 1: Secuencia de comandos de PowerShell para aprovisionar máquinas virtuales**

        #Tested with Azure Powershell - November 2014
        #This powershell script deployes a number of VMs from an existing image inside an Azure region
        #Import your Azure subscription into the current Powershell session before proceeding
        #The process: 1. create Azure Storage account, 2. create virtual network, 3.create the VM template, 2. crate a list of VMs from the template

        #fundamental variables - change these to reflect your subscription
        $country="us"; $region="west"; $vnetName = "your_vnet_name";$storageAccount="your_storage_account"
        $numVMs=8;$prefix = "hk-cass";$ilbIP="your_ilb_ip"
        $subscriptionName = "Azure_subscription_name";
        $vmSize="ExtraSmall"; $imageName="your_linux_image_name"
        $ilbName="ThriftInternalLB"; $thriftEndPoint="ThriftEndPoint"

        #generated variables
        $serviceName = "$prefix-svc-$region-$country"; $azureRegion = "$region $country"

        $vmNames = @()
        for ($i=0; $i -lt $numVMs; $i++)
        {
           $vmNames+=("$prefix-vm"+($i+1) + "-$region-$country" );
        }

        #select an Azure subscription already imported into Powershell session
        Select-AzureSubscription -SubscriptionName $subscriptionName -Current
        Set-AzureSubscription -SubscriptionName $subscriptionName -CurrentStorageAccountName $storageAccount

        #create an empty cloud service
        New-AzureService -ServiceName $serviceName -Label "hkcass$region" -Location $azureRegion
        Write-Host "Created $serviceName"

        $VMList= @()   # stores the list of azure vm configuration objects
        #create the list of VMs
        foreach($vmName in $vmNames)
        {
           $VMList += New-AzureVMConfig -Name $vmName -InstanceSize ExtraSmall -ImageName $imageName |
           Add-AzureProvisioningConfig -Linux -LinuxUser "localadmin" -Password "Local123" |
           Set-AzureSubnet "data"
        }

        New-AzureVM -ServiceName $serviceName -VNetName $vnetName -VMs $VMList

        #Create internal load balancer
        Add-AzureInternalLoadBalancer -ServiceName $serviceName -InternalLoadBalancerName $ilbName -SubnetName "data" -StaticVNetIPAddress "$ilbIP"
        Write-Host "Created $ilbName"
        #Add add the thrift endpoint to the internal load balancer for all the VMs
        foreach($vmName in $vmNames)
        {
            Get-AzureVM -ServiceName $serviceName -Name $vmName |
                Add-AzureEndpoint -Name $thriftEndPoint -LBSetName "ThriftLBSet" -Protocol tcp -LocalPort 9160 -PublicPort 9160 -ProbePort 9160 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ilbName |
                Update-AzureVM

            Write-Host "created $vmName"     
        }

**Paso 3: Configurar a Casandra en cada VM**

Inicie sesión en la máquina virtual y realice lo siguiente:

* Editar $CASS_HOME/conf/cassandra-rackdc.properties para especificar las propiedades de bastidor y de centro de datos:

       dc =EASTUS, rack =rack1

* Editar cassandra.yaml para configurar los nodos de valor de inicialización como sigue:

       Seeds: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10"

**Paso 4: Iniciar las máquinas virtuales y probar el clúster**

Inicie sesión en uno de los nodos (por ejemplo, hk-c1-oeste-nos) y ejecute el comando siguiente para ver el estado del clúster:

       nodetool –h 10.1.2.4 –p 7199 status

Debe ver la presentación similar al siguiente para un clúster de 8 nodos:

<table>
<tr><th>Estado</th><th>Dirección  </th><th>Carga   </th><th>Tokens </th><th>Es el propietario </th><th>Id. de host  </th><th>Bastidor</th></tr>
<tr><th>REACTIVAR  </td><td>10.1.2.4   </td><td>87.81 KB   </td><td>256    </td><td>% 38.0  </td><td>GUID (quitado)</td><td>cada rack1</td></tr>
<tr><th>REACTIVAR  </td><td>10.1.2.5   </td><td>41.08 KB   </td><td>256    </td><td>% 68.9  </td><td>GUID (quitado)</td><td>cada rack1</td></tr>
<tr><th>REACTIVAR  </td><td>10.1.2.6   </td><td>55.29 KB   </td><td>256    </td><td>% 68.8  </td><td>GUID (quitado)</td><td>rack2</td></tr>
<tr><th>REACTIVAR  </td><td>10.1.2.7   </td><td>55.29 KB   </td><td>256    </td><td>% 68.8  </td><td>GUID (quitado)</td><td>rack2</td></tr>
<tr><th>REACTIVAR  </td><td>10.1.2.8   </td><td>55.29 KB   </td><td>256    </td><td>% 68.8  </td><td>GUID (quitado)</td><td>rack3</td></tr>
<tr><th>REACTIVAR  </td><td>10.1.2.9   </td><td>55.29 KB   </td><td>256    </td><td>% 68.8  </td><td>GUID (quitado)</td><td>rack3</td></tr>
<tr><th>REACTIVAR  </td><td>10.1.2.10  </td><td>55.29 KB   </td><td>256    </td><td>% 68.8  </td><td>GUID (quitado)</td><td>rack4</td></tr>
<tr><th>REACTIVAR  </td><td>10.1.2.11  </td><td>55.29 KB   </td><td>256    </td><td>% 68.8  </td><td>GUID (quitado)</td><td>rack4</td></tr>
</table>

## <a name="test-the-single-region-cluster"></a>Probar el clúster única región
Siga los pasos siguientes para probar el clúster:

1.    Mediante el commandlet Get-AzureInternalLoadbalancer de comandos de Powershell, obtener la dirección IP del equilibrador de carga interno (p. ej.  10.1.2.101). A continuación se muestra la sintaxis del comando: Get-AzureLoadbalancer: ServiceName "hk-c-svc-oeste-us" [muestra los detalles del equilibrador de carga interno junto con su dirección IP]
2.  Inicie sesión en el conjunto de servidores web VM (por ejemplo, hk-B1-oeste-nos) usando Putty o ssh
3.  Ejecutar $CASS_HOME/bin/cqlsh 10.1.2.101 9160
4.  Utilice los siguientes comandos CQL para comprobar si el clúster funciona:

        CREATE KEYSPACE customers_ks WITH REPLICATION = { 'class' : 'SimpleStrategy', 'replication_factor' : 3 };
        USE customers_ks;
        CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');

        SELECT * FROM Customers;

Debería ver una presentación como el siguiente:

<table>
  <tr><th> ID_cliente </th><th> nombre </th><th> apellido </th></tr>
  <tr><td> 1 </td><td> John </td><td> Bermejo </td></tr>
  <tr><td> 2 </td><td> Jane </td><td> Bermejo </td></tr>
</table>

Tenga en cuenta que el espacio de claves que creó en el paso 4 utiliza SimpleStrategy con un replication_factor de 3. SimpleStrategy se recomienda para datos único centro implementaciones mientras que NetworkTopologyStrategy para múltiples data center implementaciones. Un replication_factor de 3 hará tolerancia a errores de nodo.

##<a id="tworegion"> </a>Proceso de implementación de región múltiples
Se aprovechar la implementación única región completada y repita el mismo proceso de instalación de la segunda región. La diferencia clave entre la implementación de región únicos y múltiples es la configuración de túnel VPN para la comunicación entre región; empezar con la instalación de red, proporcionando las máquinas virtuales y configurar a Casandra.

###<a name="step-1-create-the-virtual-network-at-the-2nd-region"></a>Paso 1: Crear la red Virtual en la región 2 º
Inicie sesión en el portal de clásico Azure y cree una red Virtual con la presentación de atributos de la tabla. Vea [Configurar una red Virtual Cloud-Only en el portal de clásico Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para conocer los pasos detallados del proceso.      

<table>
<tr><th>Nombre del atributo    </th><th>Valor    </th><th>Notas</th></tr>
<tr><td>Nombre    </td><td>vnet-cass-Oriente-us</td><td></td></tr>
<tr><td>Región  </td><td>Estados Unidos oriental</td><td></td></tr>
<tr><td>Servidores DNS     </td><td></td><td>Omitir esta como no usamos un servidor DNS</td></tr>
<tr><td>Configurar una conexión VPN punto al sitio</td><td></td><td>     Omitir esta</td></tr>
<tr><td>Configurar una VPN de sitio a otro</td><td></td><td>      Omitir esta</td></tr>
<tr><td>Espacio de direcciones   </td><td>10.2.0.0/16</td><td></td></tr>
<tr><td>Iniciar IP </td><td>10.2.0.0   </td><td></td></tr>
<tr><td>CIDR    </td><td>/ 16 (65531)</td><td></td></tr>
</table>

Agregue las siguientes subredes:
<table>
<tr><th>Nombre    </th><th>Iniciar IP    </th><th>CIDR   </th><th>Notas</th></tr>
<tr><td>Web </td><td>10.2.1.0   </td><td>/ 24 (251)  </td><td>Subred para el conjunto de servidores web</td></tr>
<tr><td>datos    </td><td>10.2.2.0   </td><td>/ 24 (251)  </td><td>Subred para los nodos de base de datos</td></tr>
</table>


###<a name="step-2-create-local-networks"></a>Paso 2: Crear redes locales
Una red Local en una red virtual Azure es un espacio de direcciones del proxy que se asigna a un sitio remoto, incluyendo una nube privada u otra región de Azure. El espacio de direcciones del proxy está enlazado a una puerta de enlace remoto para enrutamiento de red los destinos de red correctos. Consulte [configurar un VNet a VNet conexión](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) para las instrucciones sobre cómo establecer conexión VNET a VNET.

Crear dos redes locales por los siguientes detalles:

| Nombre de red | Dirección de la puerta de enlace VPN | Espacio de direcciones | Notas |
| ------------ | ------------------- | ------------- | ------- |
| HK-lnet-Map-to-East-US | 23.1.1.1  | 10.2.0.0/16   | Al crear la red Local dar un marcador de posición de la dirección de la puerta de enlace. La dirección de la puerta de enlace real se rellena una vez creada la puerta de enlace. Asegúrese de que el espacio de direcciones coincide exactamente con la VNET remoto respectivo; en este caso el VNET creado en la región oriental US. |
| HK-lnet-Map-to-West-US | 23.2.2.2  | 10.1.0.0/16   | Al crear la red Local dar un marcador de posición de la dirección de la puerta de enlace. La dirección de la puerta de enlace real se rellena una vez creada la puerta de enlace. Asegúrese de que el espacio de direcciones coincide exactamente con la VNET remoto respectivo; en este caso el VNET creado en la región de Estados Unidos oeste. |


###<a name="step-3-map-local-network-to-the-respective-vnets"></a>Paso 3: Mapa "" una red Local a la VNETs respectivos
Desde el portal de clásico Azure, seleccione cada vnet, haga clic en "Configurar", consulte "Conectarse a la red local" y seleccione las redes Local por los siguientes detalles:


| Red virtual | Red local |
| --------------- | ------------- |
| HK-vnet-oeste-us | HK-lnet-Map-to-East-US |
| HK-vnet-Oriente-us | HK-lnet-Map-to-West-US |


###<a name="step-4-create-gateways-on-vnet1-and-vnet2"></a>Paso 4: Crear puertas de enlace en VNET1 y VNET2
Desde el panel de la red virtual, haga clic en crear puerta de enlace se activará la puerta de enlace VPN proceso de aprovisionamiento. Después de unos minutos los paneles de cada red virtual deben mostrar la dirección de la puerta de enlace real.

###<a name="step-5-update-local-networks-with-the-respective-gateway-addresses"></a>Paso 5: Actualizar "Local" las redes con las direcciones correspondientes "puerta de enlace"###
Editar la redes locales para reemplazar la dirección IP de puerta de enlace de marcador de posición con la dirección IP real de las puertas de enlace simplemente aprovisionados. Use la siguiente asignación:

<table>
<tr><th>Red local    </th><th>Puerta de enlace de red virtual</th></tr>
<tr><td>HK-lnet-Map-to-East-US </td><td>Puerta de enlace de hk-vnet-oeste-nosotros</td></tr>
<tr><td>HK-lnet-Map-to-West-US </td><td>Puerta de enlace de hk-vnet-Oriente-nosotros</td></tr>
</table>

###<a name="step-6-update-the-shared-key"></a>Paso 6: Actualizar la clave compartida
Use el siguiente script de Powershell para actualizar la clave IPSec de cada puerta de enlace VPN [usar la tecla fin para las puertas de enlace]: hk-lnet-map-to-west-us - LocalNetworkSiteName de AzureVNetGatewayKey Set - VNetName hk-vnet-Oriente-us - SharedKey hk-lnet-map-to-east-us - LocalNetworkSiteName de D9E76BKK Set-AzureVNetGatewayKey - VNetName hk-vnet-oeste-us - SharedKey D9E76BKK

###<a name="step-7-establish-the-vnet-to-vnet-connection"></a>Paso 7: Establecer la conexión de VNET a VNET
Desde el portal de Azure clásico, utilice el menú de "Escritorio" de la red virtual para establecer la conexión de puertas de enlace. Usar los elementos de menú "Conectarse" en la barra de herramientas de la parte inferior. Después de unos minutos el panel debe mostrar gráficamente los detalles de la conexión.

###<a name="step-8-create-the-virtual-machines-in-region-2"></a>Paso 8: Crear máquinas virtuales de región #2
Cree la imagen Ubuntu tal como se describe en la implementación de región #1 siguiendo el mismo pasos o copiar el archivo de disco duro virtual de imagen a la cuenta de almacenamiento de Azure ubicado en la región #2 y la imagen. Utilizar esta imagen y cree la siguiente lista de máquinas virtuales en un nuevo servicio de nube hk-c-svc-Oriente-us.


| Nombre del equipo | Subred | Dirección IP | Conjunto de disponibilidad | Bastidor/DC | ¿Valor de inicialización? |
| ------------ | ------ | ---------- | ---------------- | ------- | ----- |
| HK-c1-Oriente-us | datos  | 10.2.2.4   | HK-c-un conjunto-1      | DC = bastidor EASTUS = cada rack1 | Sí |
| HK-c2-Oriente-us | datos  | 10.2.2.5   | HK-c-un conjunto-1      | DC = bastidor EASTUS = cada rack1 | No  |
| HK-c3-Oriente-us | datos  | 10.2.2.6   | HK-c-un conjunto-1      | DC = bastidor EASTUS = rack2 | Sí |
| HK-c5-Oriente-us | datos  | 10.2.2.8   | HK-c-un conjunto-2      | DC = bastidor EASTUS = rack3 | Sí |
| HK-c6-Oriente-us | datos  | 10.2.2.9   | HK-c-un conjunto-2      | DC = bastidor EASTUS = rack3 | No  |
| HK-c7-Oriente-us | datos  | 10.2.2.10  | HK-c-un conjunto-2      | DC = bastidor EASTUS = rack4 | Sí |
| HK-c8-Oriente-us | datos  | 10.2.2.11  | HK-c-un conjunto-2      | DC = bastidor EASTUS = rack4 | No  |
| HK-B1-Oriente-us | Web   | 10.2.1.4   | HK-w-un conjunto-1      | N/A.                    | N/A. |
| HK-w2-Oriente-us | Web   | 10.2.1.5   | HK-w-un conjunto-1      | N/A.                    | N/A. |


Siga las instrucciones del mismas como región #1 pero usar 10.2.xxx.xxx espacio de direcciones.

###<a name="step-9-configure-cassandra-on-each-vm"></a>Paso 9: Configurar a Casandra en cada VM
Inicie sesión en la máquina virtual y realice lo siguiente:

1. Editar $CASS_HOME/conf/cassandra-rackdc.properties para especificar las propiedades de bastidor y de centro de datos en el formato: dc = bastidor EASTUS = cada rack1
2. Editar cassandra.yaml para configurar los nodos de valor de inicialización: semillas: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10,10.2.2.4,10.2.2.6,10.2.2.8,10.2.2.10"

###<a name="step-10-start-cassandra"></a>Paso 10: Iniciar Casandra
Inicie sesión en cada VM y empiece a Casandra en segundo plano, ejecute el siguiente comando: $CASS_HOME/bin/Casandra

## <a name="test-the-multi-region-cluster"></a>Probar el clúster región múltiples
Ya se ha implementado Casandra a 16 nodos con 8 nodos en cada región Azure. Estos nodos están en el mismo clúster por el nombre de clúster comunes y la configuración de nodos de valor de inicialización. Use este procedimiento para probar el clúster:

###<a name="step-1-get-the-internal-load-balancer-ip-for-both-the-regions-using-powershell"></a>Paso 1: Obtener la dirección IP del equilibrador de carga interno para ambos las regiones con PowerShell
- AzureInternalLoadbalancer Get - ServiceName "hk-c-svc-oeste-us"
- AzureInternalLoadbalancer Get - ServiceName "hk-c-svc-Oriente-us"  

    Tenga en cuenta las direcciones IP (por ejemplo oeste - 10.1.2.101, Oriente - 10.2.2.101) muestra.

###<a name="step-2-execute-the-following-in-the-west-region-after-logging-into-hk-w1-west-us"></a>Paso 2: Ejecute el siguiente en la región Oeste tras iniciar sesión en hk-B1-oeste-us
1.    Ejecutar $CASS_HOME/bin/cqlsh 10.1.2.101 9160
2.  Ejecute los siguientes comandos CQL:

        CREATE KEYSPACE customers_ks
        WITH REPLICATION = { 'class' : 'NetworkToplogyStrategy', 'WESTUS' : 3, 'EASTUS' : 3};
        USE customers_ks;
        CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
        SELECT * FROM Customers;

Debería ver una presentación como el siguiente:

| ID_cliente | nombre | Apellido |
| ----------- | --------- | -------- |
| 1           | John      | Bermejo      |
| 2           | Jane      | Bermejo      |


###<a name="step-3-execute-the-following-in-the-east-region-after-logging-into-hk-w1-east-us"></a>Paso 3: Ejecute el siguiente en la región Este tras iniciar sesión en hk-B1-Oriente-nosotros:
1.    Ejecutar $CASS_HOME/bin/cqlsh 10.2.2.101 9160
2.  Ejecute los siguientes comandos CQL:

        USE customers_ks;
        CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
        SELECT * FROM Customers;

Verá la misma presentación tal como se muestra en la región Oeste:


| ID_cliente | nombre | Apellido   |
|------------ | --------- | ---------- |
| 1           | John      | Bermejo        |
| 2           | Jane      | Bermejo        |


Ejecutar unas inserciones más y ver que los obtengan replicarse en oeste-nos parte del clúster.

## <a name="test-cassandra-cluster-from-nodejs"></a>Prueba Casandra clúster de Node.js
Con una de las máquinas virtuales de Linux en "web" nivel anteriormente, se ejecutará una secuencia de comandos de Node.js simple para leer los datos previamente insertados

**Paso 1: Instalar el cliente de Casandra y Node.js**

1. Instalar Node.js y npm
2. Instalar el paquete "Casandra-cliente de nodo" usando npm
3. Ejecute el siguiente script en el indicador de shell que muestra la cadena json de los datos recuperados:

        var pooledCon = require('cassandra-client').PooledConnection;
        var ksName = "custsupport_ks";
        var cfName = "customers_cf";
        var hostList = ['internal_loadbalancer_ip:9160'];
        var ksConOptions = { hosts: hostList,
                             keyspace: ksName, use_bigints: false };

        function createKeyspace(callback){
           var cql = 'CREATE KEYSPACE ' + ksName + ' WITH strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
           var sysConOptions = { hosts: hostList,  
                                 keyspace: 'system', use_bigints: false };
           var con = new pooledCon(sysConOptions);
           con.execute(cql,[],function(err) {
           if (err) {
             console.log("Failed to create Keyspace: " + ksName);
             console.log(err);
           }
           else {
             console.log("Created Keyspace: " + ksName);
             callback(ksConOptions, populateCustomerData);
           }
           });
           con.shutdown();
        }

        function createColumnFamily(ksConOptions, callback){
          var params = ['customers_cf','custid','varint','custname',
                        'text','custaddress','text'];
          var cql = 'CREATE COLUMNFAMILY ? (? ? PRIMARY KEY,? ?, ? ?)';
        var con =  new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) {
                 console.log("Failed to create column family: " + params[0]);
                 console.log(err);
              }
              else {
                 console.log("Created column family: " + params[0]);
                 callback();
              }
          });
          con.shutdown();
        }

        //populate Data
        function populateCustomerData() {
           var params = ['John','Infinity Dr, TX', 1];
           updateCustomer(ksConOptions,params);

           params = ['Tom','Fermat Ln, WA', 2];
           updateCustomer(ksConOptions,params);
        }

        //update will also insert the record if none exists
        function updateCustomer(ksConOptions,params)
        {
          var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where custid=?';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) console.log(err);
              else console.log("Inserted customer : " + params[0]);
          });
          con.shutdown();
        }

        //read the two rows inserted above
        function readCustomer(ksConOptions)
        {
          var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,[],function(err,rows) {
              if (err)
                 console.log(err);
              else
                 for (var i=0; i<rows.length; i++)
                    console.log(JSON.stringify(rows[i]));
            });
           con.shutdown();
        }

        //exectue the code
        createKeyspace(createColumnFamily);
        readCustomer(ksConOptions)


## <a name="conclusion"></a>Conclusión
Microsoft Azure es una plataforma flexible que permite la ejecución de Microsoft así como software de código abierto, como se muestra en este ejercicio. Clústeres de Casandra altamente disponibles se pueden implementar en un solo centro de datos a través de dispersión, de los nodos de clúster en varios dominios de errores. También se pueden implementar clústeres Casandra en varias áreas de Azure distancia geográfica para sistemas de prueba de desastres. Azure y Casandra juntos permite la creación de alta escalabilidad, alta disponibilidad y servicios en la nube recuperable desastres es necesario por internet de hoy escalar los servicios.  

##<a name="references"></a>Referencias##
- [http://cassandra.Apache.org](http://cassandra.apache.org)
- [http://www.datastax.com](http://www.datastax.com)
- [http://www.nodejs.org](http://www.nodejs.org)
