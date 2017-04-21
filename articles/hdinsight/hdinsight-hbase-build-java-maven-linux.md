<properties
    pageTitle="Crear una aplicación de HBase con Maven y Java y luego implementar a HDInsight de Linux | Microsoft Azure"
    description="Obtenga información sobre cómo usar a Apache experto para crear una aplicación basada en Java Apache HBase, a continuación, impleméntelo en HDInsight basados en Linux en la nube de Azure."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="larryfr"/>

#<a name="use-maven-to-build-java-applications-that-use-hbase-with-linux-based-hdinsight-hadoop"></a>Utilizar a Maven para generar aplicaciones de Java que usan HBase con HDInsight basados en Linux (Hadoop)

Aprenda a crear y generar una aplicación de [Apache HBase](http://hbase.apache.org/) en Java con Apache experto. A continuación, use la aplicación con un clúster de HDInsight basados en Linux.

[Maven](http://maven.apache.org/) es una herramienta de comprensión que le permite crear software, documentación e informes de proyectos de Java y administración de proyectos de software. En este artículo, aprenderá a usarlo para crear una aplicación de Java básica que crea, consultas y elimina una tabla de HBase en un clúster de HDInsight basados en Linux.

> [AZURE.NOTE] Los pasos de este documento se supone que está usando un clúster HDInsight basados en Linux. Para obtener información sobre el uso de un clúster de HDInsight basado en Windows, vea [Usar Maven para generar aplicaciones de Java que utilizan HBase con HDInsight de Windows](hdinsight-hbase-build-java-maven.md)

##<a name="requirements"></a>Requisitos

* [Plataforma Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 o posterior

* [Experto](http://maven.apache.org/)

* [Un clúster de basados en Linux Azure HDInsight con HBase](../hdinsight-hbase-tutorial-get-started-linux.md#create-hbase-cluster)

    > [AZURE.NOTE] Los pasos de este documento se ha probado con las versiones de clúster de HDInsight 3.2, 3.3 y 3.4. Los valores predeterminados proporcionados en los ejemplos son para un clúster de HDInsight 3.4.

* **Estar familiarizado con SSH y SCP**. Para obtener más información sobre cómo utilizar SSH y SCP con HDInsight, consulte lo siguiente:

    * **Clientes Linux, Unix o OS X**: vea [Usar SSH con basados en Linux Hadoop en HDInsight de Linux, OS X o Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Los clientes de Windows**: vea [Usar SSH con basados en Linux Hadoop en HDInsight de Windows.](hdinsight-hadoop-linux-use-ssh-windows.md)

##<a name="create-the-project"></a>Crear el proyecto

1. Desde la línea de comandos en su entorno de desarrollo, cambie los directorios a la ubicación donde desea crear el proyecto, por ejemplo, `cd code/hdinsight`.

2. Use el comando __mvn__ , que se instala con Maven, para generar el scaffolding para el proyecto.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Esto crea un nuevo directorio en el directorio actual, con el nombre especificado por el parámetro __artifactID__ (**hbaseapp** en este ejemplo). Este directorio contendrá los siguientes elementos:

    * __pom.XML__: el modelo de objeto de proyectos ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) contiene los detalles de configuración e información se usa para generar el proyecto.

    * __src__: el directorio que contiene el directorio __principal, java, com, microsoft o ejemplos__ , donde creará la aplicación.

3. Elimine el archivo __src/test/java/com/microsoft/examples/apptest.java__ ya no se utilizará en este ejemplo.

##<a name="update-the-project-object-model"></a>Actualizar el modelo de objeto de proyectos

1. Editar el archivo __pom.xml__ y agregue el código siguiente dentro de la `<dependencies>` sección:

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

    Indica que el proyecto requiere la versión de __cliente de hbase__ __1.1.2__experto. En tiempo de compilación, se descargarán desde el repositorio de experto en predeterminado. Puede usar la [Búsqueda de repositorio Central de experto en](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) para obtener más información sobre esta dependencia.

    > [AZURE.IMPORTANT] El número de versión debe coincidir con la versión de HBase que se incluye con el clúster de HDInsight. Utilice la siguiente tabla para buscar el número de versión correcto.

  	| Versión de clúster HDInsight | Versión del HBase |
  	| ----- | ----- |
  	| 3.2 | 0.98.4-hadoop2 |
  	| 3.3 y 3.4 | 1.1.2 |

    Para obtener más información sobre las versiones de HDInsight y componentes, consulte [¿Qué son los distintos componentes de Hadoop disponibles con HDInsight](hdinsight-component-versioning.md).

2. Si está utilizando un 3.3 HDInsight o clúster 3.4, también debe agregar las siguientes acciones para la `<dependencies>` sección:

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
    
    Esto cargará los componentes principales Caracas, que son necesarios con Hbase versión 1.1.x.

2. Agregue el código siguiente al archivo __pom.xml__ . Esto debe estar dentro de la `<project>...</project>` etiquetas en el archivo, por ejemplo, entre `</dependencies>` y `</project>`.

        <build>
          <sourceDirectory>src</sourceDirectory>
          <resources>
            <resource>
              <directory>${basedir}/conf</directory>
              <filtering>false</filtering>
              <includes>
                <include>hbase-site.xml</include>
              </includes>
            </resource>
          </resources>
          <plugins>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
                        <version>3.3</version>
              <configuration>
                <source>1.7</source>
                <target>1.7</target>
              </configuration>
            </plugin>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-shade-plugin</artifactId>
              <version>2.3</version>
              <configuration>
                <transformers>
                  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                  </transformer>
                </transformers>
              </configuration>
              <executions>
                <execution>
                  <phase>package</phase>
                  <goals>
                    <goal>shade</goal>
                  </goals>
                </execution>
              </executions>
            </plugin>
          </plugins>
        </build>

    Configura un recurso (__de conferencia, hbase-site.xml__), que contiene información de configuración de HBase.

    > [AZURE.NOTE] También puede establecer los valores de configuración a través del código. Ver los comentarios en el ejemplo __CreateTable__ que sigue para que saber cómo hacerlo.

    Esto también configura el [Experto compilador complemento](http://maven.apache.org/plugins/maven-compiler-plugin/) y el [Complemento de experto en sombra](http://maven.apache.org/plugins/maven-shade-plugin/). El compilador complemento se utiliza para compilar la topología. Se usa el complemento de sombra para evitar la duplicación de licencia en el paquete JAR integrado experto. El motivo que se utiliza es que los archivos de licencia duplicadas producen un error en tiempo de ejecución en el clúster HDInsight. Usando experto sombrear-complemento con la `ApacheLicenseResourceTransformer` implementación impide que este error.

    El complemento de sombreado de experto en también genera una ejercicios jar (o jar fat,) que contiene todas las dependencias necesarios para la aplicación.

3. Guarde el archivo __pom.xml__ .

4. Crear un nuevo directorio denominado __conferencia__ en el directorio __hbaseapp__ . Se utilizará para almacenar la información de configuración para conectar con HBase.

5. Use el comando siguiente para copiar la configuración de HBase desde el servidor de HDInsight en el directorio de __conferencia__ . Reemplazar el **nombre de usuario** el nombre de su inicio de sesión SSH. Reemplazar **NOMBREDECLÚSTER** con su nombre de clúster HDInsight:

        scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml

    > [AZURE.NOTE] Si utiliza una contraseña para su cuenta SSH, se le pedirá que escriba la contraseña. Si utiliza una clave SSH con la cuenta, tendrá que usar la `-i` parámetro para especificar la ruta de acceso al archivo de clave. En el ejemplo siguiente se cargará la clave privada de `~/.ssh/id_rsa`:
    >
    > `scp -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml`

##<a name="create-the-application"></a>Creación de la aplicación

1. Vaya al directorio __hbaseapp/src/main/java/com/microsoft/ejemplos__ y cambie el nombre del archivo app.java __CreateTable.java__.

2. Abra el archivo __CreateTable.java__ y reemplace el contenido existente con lo siguiente:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;
        import org.apache.hadoop.hbase.HTableDescriptor;
        import org.apache.hadoop.hbase.TableName;
        import org.apache.hadoop.hbase.HColumnDescriptor;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Put;
        import org.apache.hadoop.hbase.util.Bytes;

        public class CreateTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Example of setting zookeeper values for HDInsight
            // in code instead of an hbase-site.xml file
            //
            // config.set("hbase.zookeeper.quorum",
            //            "zookeepernode0,zookeepernode1,zookeepernode2");
            //config.set("hbase.zookeeper.property.clientPort", "2181");
            //config.set("hbase.cluster.distributed", "true");
            //
            //NOTE: Actual zookeeper host names can be found using Ambari:
            //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"
            
            //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
            config.set("zookeeper.znode.parent","/hbase-unsecure");

            // create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // create the table...
            HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
            // ... with two column families
            tableDescriptor.addFamily(new HColumnDescriptor("name"));
            tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
            admin.createTable(tableDescriptor);

            // define some people
            String[][] people = {
                { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
                { "2", "Franklin", "Holtz", "franklin@contoso.com" },
                { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
                { "4", "Rae", "Schroeder", "rae@contoso.com" },
                { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
                { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

            HTable table = new HTable(config, "people");

            // Add each person to the table
            //   Use the `name` column family for the name
            //   Use the `contactinfo` column family for the email
            for (int i = 0; i< people.length; i++) {
              Put person = new Put(Bytes.toBytes(people[i][0]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
              person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
              table.put(person);
            }
            // flush commits and close the table
            table.flushCommits();
            table.close();
          }
        }

    Esta es la clase __CreateTable__ , que creará una tabla denominada __personas__ y rellénela con algunos usuarios predefinidos.

3. Guarde el archivo __CreateTable.java__ .

4. En el directorio __hbaseapp/src/main/java/com/microsoft/ejemplos__ , cree un nuevo archivo denominado __SearchByEmail.java__. Utilice la siguiente como el contenido de este archivo:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Scan;
        import org.apache.hadoop.hbase.client.ResultScanner;
        import org.apache.hadoop.hbase.client.Result;
        import org.apache.hadoop.hbase.filter.RegexStringComparator;
        import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
        import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
        import org.apache.hadoop.hbase.util.Bytes;
        import org.apache.hadoop.util.GenericOptionsParser;

        public class SearchByEmail {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Use GenericOptionsParser to get only the parameters to the class
            // and not all the parameters passed (when using WebHCat for example)
            String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
            if (otherArgs.length != 1) {
              System.out.println("usage: [regular expression]");
              System.exit(-1);
            }

            // Open the table
            HTable table = new HTable(config, "people");

            // Define the family and qualifiers to be used
            byte[] contactFamily = Bytes.toBytes("contactinfo");
            byte[] emailQualifier = Bytes.toBytes("email");
            byte[] nameFamily = Bytes.toBytes("name");
            byte[] firstNameQualifier = Bytes.toBytes("first");
            byte[] lastNameQualifier = Bytes.toBytes("last");

            // Create a new regex filter
            RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
            // Attach the regex filter to a filter
            //   for the email column
            SingleColumnValueFilter filter = new SingleColumnValueFilter(
              contactFamily,
              emailQualifier,
              CompareOp.EQUAL,
              emailFilter
            );

            // Create a scan and set the filter
            Scan scan = new Scan();
            scan.setFilter(filter);

            // Get the results
            ResultScanner results = table.getScanner(scan);
            // Iterate over results and print  values
            for (Result result : results ) {
              String id = new String(result.getRow());
              byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
              String firstName = new String(firstNameObj);
              byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
              String lastName = new String(lastNameObj);
              System.out.println(firstName + " " + lastName + " - ID: " + id);
              byte[] emailObj = result.getValue(contactFamily, emailQualifier);
              String email = new String(emailObj);
              System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
            }
            results.close();
            table.close();
          }
        }

    La clase __SearchByEmail__ puede utilizarse para consultar filas por la dirección de correo electrónico. Porque utiliza un filtro de expresión regular, puede proporcionar una cadena o una expresión regular al utilizar la clase.

5. Guarde el archivo __SearchByEmail.java__ .

6. En el directorio __hbaseapp/src/main/hava/com/microsoft/ejemplos__ , cree un nuevo archivo denominado __DeleteTable.java__. Utilice la siguiente como el contenido de este archivo:

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;

        public class DeleteTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // Disable, and then delete the table
            admin.disableTable("people");
            admin.deleteTable("people");
          }
        }

    Esta clase es para limpiar este ejemplo deshabilitando y eliminar la tabla creada por la clase __CreateTable__ .

7. Guarde el archivo __DeleteTable.java__ .

##<a name="build-and-package-the-application"></a>Generar y empaquetar la aplicación

2. Desde el directorio __hbaseapp__ , use el comando siguiente para generar un archivo JAR que contiene la aplicación:

        mvn clean package

    Esto limpia cualquier artefacto de compilación anterior, descargas dependencias que no se han instalado, a continuación, crea y paquete de la aplicación.

3. Cuando finalice el comando, el directorio de __hbaseapp/destino__ contendrá un archivo llamado __hbaseapp-1.0-SNAPSHOT.jar__.

    > [AZURE.NOTE] El archivo __hbaseapp-1.0-SNAPSHOT.jar__ es un ejercicios jar (a veces denominado un jar fat,) que contiene todas las dependencias necesarias para ejecutar la aplicación.

##<a name="upload-the-jar-file-and-run-jobs"></a>Cargue el archivo JAR y ejecutar trabajos

1. Utilice el siguiente para cargar el tarro en el clúster HDInsight. Reemplazar el **nombre de usuario** el nombre de su inicio de sesión SSH. Reemplazar **NOMBREDECLÚSTER** con su nombre de clúster HDInsight:

        scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    Esto cargue el archivo en el directorio de inicio para su cuenta de usuario SSH.

    > [AZURE.NOTE] Si utiliza una contraseña para su cuenta SSH, se le pedirá que escriba la contraseña. Si utiliza una clave SSH con la cuenta, tendrá que usar la `-i` parámetro para especificar la ruta de acceso al archivo de clave. En el ejemplo siguiente se cargará la clave privada de `~/.ssh/id_rsa`:
    >
    > `scp -i ~/.ssh/id_rsa ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`

2. Utilizar SSH para conectar con el clúster de HDInsight. Reemplazar el **nombre de usuario** el nombre de su inicio de sesión SSH. Reemplazar **NOMBREDECLÚSTER** con su nombre de clúster HDInsight:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [AZURE.NOTE] Si utiliza una contraseña para su cuenta SSH, se le pedirá que escriba la contraseña. Si utiliza una clave SSH con la cuenta, tendrá que usar la `-i` parámetro para especificar la ruta de acceso al archivo de clave. En el ejemplo siguiente se cargará la clave privada de `~/.ssh/id_rsa`:
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. Una vez conectado, utilice la siguiente para crear una nueva tabla de HBase mediante la aplicación de Java:

        hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable

    Esto se crear una nueva tabla de HBase denominada __personas__y rellenar con datos.

4. A continuación, use las siguientes acciones para buscar direcciones de correo electrónico almacenadas en la tabla:

        hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com

    Recibirá los siguientes resultados:

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

##<a name="delete-the-table"></a>Eliminar la tabla

Cuando haya terminado con el ejemplo, use el siguiente comando de la sesión de Azure PowerShell para eliminar la tabla de __personas__ que se utiliza en este ejemplo:

    hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable

