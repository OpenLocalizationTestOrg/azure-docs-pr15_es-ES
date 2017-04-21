<properties
    pageTitle="Aplicación de Java descarga intensiva en una máquina virtual | Microsoft Azure"
    description="Aprenda a crear una máquina virtual Azure que se ejecuta una aplicación de Java descarga intensiva que puede supervisar por otra aplicación de Java."
    services="virtual-machines-windows"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="how-to-run-a-compute-intensive-task-in-java-on-a-virtual-machine"></a>Cómo ejecutar una tarea de descarga intensiva en Java en una máquina virtual

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
 

Con Azure, puede usar una máquina virtual para administrar las tareas de descarga intensiva. Por ejemplo, una máquina virtual puede controlar tareas y entregar resultados a los equipos cliente o aplicaciones móviles. Después de leer este artículo, tendrá una descripción de cómo crear una máquina virtual que se ejecuta una aplicación de Java descarga intensiva que puede supervisar por otra aplicación de Java.

En este tutorial se supone que sabe cómo crear aplicaciones de consola de Java, puede importar bibliotecas a su aplicación de Java y puede generar un archivo de Java (JAR). No se supone que se conoce de Microsoft Azure.

Aprenderá:

* Cómo crear una máquina virtual con un Kit de desarrollo de Java (JDK) ya instalado.
* Cómo iniciar sesión la máquina virtual remotamente.
* Cómo crear un espacio de nombres de bus de servicio.
* Cómo crear una aplicación de Java que realiza una tarea de descarga intensiva.
* Cómo crear una aplicación de Java que supervisa el progreso de la tarea de descarga intensiva.
* Cómo ejecutar las aplicaciones de Java.
* Cómo detener las aplicaciones de Java.

En este tutorial usará el problema del viajante para la tarea de descarga intensiva. El siguiente es un ejemplo de la aplicación de Java que se ejecuta la tarea de descarga intensiva.

![Solver problema del viajante][solver_output]

El siguiente es un ejemplo de la aplicación de Java supervisar la tarea de descarga intensiva.

![Cliente de problema del viajante][client_output]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Para crear una máquina virtual

1. Inicie sesión en el [portal de clásico de Azure](https://manage.windowsazure.com).
2. Haga clic en **nuevo**, haga clic en **calcular**, haga clic en la **Máquina Virtual**y, a continuación, haga clic en **Desde la Galería**.
3. En el cuadro de diálogo **Seleccionar imagen de máquina Virtual** , seleccione **JDK 7 Windows Server 2012**.
Tenga en cuenta que **JDK 6 Windows Server 2012** está disponible en caso de que tiene aplicaciones heredadas que no aún está listos para que se ejecute en JDK 7.
4. Haga clic en **siguiente**.
4. En el cuadro de diálogo **configuración de máquina Virtual** :
    1. Especifique un nombre para la máquina virtual.
    2. Especificar el tamaño para la máquina virtual.
    3. Escriba un nombre para el administrador en el campo **Nombre de usuario** . Recuerde que este nombre y la contraseña que se especifique a continuación, que usa al iniciar sesión remotamente la máquina virtual.
    4. Escriba una contraseña en el campo **nueva contraseña** y vuelva a escribirla en el campo **Confirmar** . Esta es la contraseña de la cuenta de administrador.
    5. Haga clic en **siguiente**.
5. En el cuadro de diálogo **configuración de la máquina Virtual** siguiente:
    1. **Servicio de nube**, use el valor predeterminado de **crear un nuevo servicio de nube**.
    2. El valor de **nombre DNS del servicio de nube** debe ser único en cloudapp.net. Si es necesario modificar este valor para que ese Azure indica que sea único.
    2. Especificar una región, grupo afinidad o una red virtual. Para el propósito de este tutorial, especifique una región como **US oeste**.
    2. Para la **Cuenta de almacenamiento**, seleccione **usar una cuenta de almacenamiento generada automáticamente**.
    3. Para **Establecer la disponibilidad**, seleccione **(ninguno)**.
    4. Haga clic en **siguiente**.
5. En el último cuadro de diálogo **configuración de la máquina Virtual** :
    1. Aceptar las entradas de extremo predeterminado.
    2. Haga clic en **Finalizar**.

## <a name="to-remotely-log-in-to-your-virtual-machine"></a>Iniciar sesión la máquina virtual remotamente

1. Inicie sesión en el [portal de clásico de Azure](https://manage.windowsazure.com).
2. Haga clic en **máquinas virtuales de Windows**.
3. Haga clic en el nombre de la máquina virtual que desea iniciar sesión en.
4. Haga clic en **Conectar**.
5. Responder a las preguntas según sea necesario para conectarse a la máquina virtual. Cuando se le solicite el nombre de administrador y la contraseña, utilice los valores que proporcionan cuando creó la máquina virtual.

Observe que la funcionalidad de Bus de servicio de Azure requiere el certificado de raíz de Baltimore CyberTrust que se instala como parte de la tienda de su JRE **cacerts** . Este certificado se incluye automáticamente en el entorno de tiempo de ejecución de Java (JRE) usado por este tutorial. Si no tiene este certificado en el almacén de **cacerts** JRE, consulte [Agregar un certificado al almacén de certificados de entidad emisora de certificados de Java] [ add_ca_cert] para obtener información sobre la adición de él (así como información sobre cómo ver los certificados en la tienda cacerts).

## <a name="how-to-create-a-service-bus-namespace"></a>Cómo crear un espacio de nombres de bus de servicio

Para empezar a usar colas de Bus de servicio en Azure, primero debe crear un espacio de nombres de servicio. Un espacio de nombres de servicio proporciona un contenedor de ámbito para el direccionamiento de recursos de Bus de servicio de la aplicación.

Para crear un espacio de nombres de servicio:

1.  Inicie sesión en el [portal de clásico de Azure](https://manage.windowsazure.com).
2.  En el panel de navegación de la esquina inferior izquierda del portal Azure clásico, haga clic en **Bus de servicio, Control de acceso y almacenamiento en caché**.
3.  En el panel de la esquina superior izquierda del portal Azure clásico, haga clic en el nodo de **Bus de servicio** y, a continuación, haga clic en el botón **nuevo** .  
    ![Captura de pantalla de nodo de Bus de servicio][svc_bus_node]
4.  En el cuadro de diálogo **crear un nuevo Namespace de servicio** , escriba un **Namespace**y para asegurarse de que es único, haga clic en el botón **Comprobar disponibilidad** .  
    ![Crear una captura de pantalla de Namespace nueva][create_namespace]
5.  Una vez que asegurarse de que el espacio de nombres está disponible, elija el país o región en la que debe estar alojado el espacio de nombres y, a continuación, haga clic en el botón **Crear Namespace** .  

    El espacio de nombres que creó aparecerá en el portal de clásico Azure y tarda un momento en Activar. Espere a que el estado es **activo** antes de continuar con el siguiente paso.

## <a name="obtain-the-default-management-credentials-for-the-namespace"></a>Obtener las credenciales de administración predeterminado para el espacio de nombres

Para poder realizar operaciones de administración, como la creación de una cola, en el espacio de nombres, debe obtener las credenciales de administración para el espacio de nombres.

1.  En el panel de navegación izquierdo, haga clic en el nodo de **Bus de servicio** para mostrar la lista de espacios de nombres disponibles.
    ![Captura de pantalla de espacios de nombres disponible][avail_namespaces]
2.  Seleccione el espacio de nombres que acaba de crear de la lista que aparece.
    ![Captura de pantalla de Namespace lista][namespace_list]
3.  El panel derecho de **Propiedades** enumera las propiedades para el nuevo espacio de nombres.
    ![Captura de pantalla de panel de propiedades][properties_pane]
4.  Se oculta la **Clave predeterminada** . Haga clic en el botón de **vista** para mostrar las credenciales de seguridad.
    ![Captura de pantalla de clave predeterminado][default_key]
5.  Tome nota del **Emisor predeterminada** y la **Clave predeterminada** cuando se usa esta información a continuación para realizar operaciones con el espacio de nombres.

## <a name="how-to-create-a-java-application-that-performs-a-compute-intensive-task"></a>Cómo crear una aplicación de Java que realiza una tarea de descarga intensiva

1. En el equipo de desarrollo (que no tiene que ser la máquina virtual que ha creado), descargue el [SDK de Azure para Java](https://azure.microsoft.com/develop/java/).
2. Crear una aplicación de consola de Java mediante el código de ejemplo al final de esta sección. En este tutorial, usaremos **TSPSolver.java** como el nombre de archivo de Java. Modificar la **su\_servicio\_bus\_espacio de nombres**, **su\_service\_bus\_propietario**, y **su\_servicio\_bus\_clave** marcadores de posición para usar el servicio de bus de **espacio de nombres**, **Emisor predeterminado** y valores de **Clave predeterminado** , respectivamente.
3. Después de codificar, exportar la aplicación a un archivo ejecutable de Java (JAR) y empaquetar las bibliotecas necesarias en el TARRO generado. En este tutorial, usaremos **TSPSolver.jar** como el nombre JAR generado.

<p/>

    // TSPSolver.java

    import com.microsoft.windowsazure.services.core.Configuration;
    import com.microsoft.windowsazure.services.core.ServiceException;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import java.io.*;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import java.util.ArrayList;
    import java.util.Date;
    import java.util.List;

    public class TSPSolver {

        //  Value specifying how often to provide an update to the console.
        private static long loopCheck = 100000000;  

        private static long nTimes = 0, nLoops=0;

        private static double[][] distances;
        private static String[] cityNames;
        private static int[] bestOrder;
        private static double minDistance;
        private static ServiceBusContract service;

        private static void buildDistances(String fileLocation, int numCities) throws Exception{
            try{
                BufferedReader file = new BufferedReader(new InputStreamReader(new DataInputStream(new FileInputStream(new File(fileLocation)))));
                double[][] cityLocs = new double[numCities][2];
                for (int i = 0; i<numCities; i++){
                    String[] line = file.readLine().split(", ");
                    cityNames[i] = line[0];
                    cityLocs[i][0] = Double.parseDouble(line[1]);
                    cityLocs[i][1] = Double.parseDouble(line[2]);
                }
                for (int i = 0; i<numCities; i++){
                    for (int j = i; j<numCities; j++){
                        distances[i][j] = Math.hypot(Math.abs(cityLocs[i][0] - cityLocs[j][0]), Math.abs(cityLocs[i][1] - cityLocs[j][1]));
                        distances[j][i] = distances[i][j];
                    }
                }
            } catch (Exception e){
                throw e;
            }
        }

        private static void permutation(List<Integer> startCities, double distSoFar, List<Integer> restCities) throws Exception {

            try
            {
                nTimes++;
                if (nTimes == loopCheck)
                {
                    nLoops++;
                    nTimes = 0;
                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.print("Current time is " + dateFormat.format(date) + ". ");
                    System.out.println(  "Completed " + nLoops + " iterations of size of " + loopCheck + ".");
                }

                if ((restCities.size() == 1) && ((minDistance == -1) || (distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-1)] < minDistance))){
                    startCities.add(restCities.get(0));
                    newBestDistance(startCities, distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-2)]);
                    startCities.remove(startCities.size()-1);
                }
                else{
                    for (int i=0; i<restCities.size(); i++){
                        startCities.add(restCities.get(0));
                        restCities.remove(0);
                        permutation(startCities, distSoFar + distances[startCities.get(startCities.size()-1)][startCities.get(startCities.size()-2)],restCities);
                        restCities.add(startCities.get(startCities.size()-1));
                        startCities.remove(startCities.size()-1);
                    }
                }
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        private static void newBestDistance(List<Integer> cities, double distance) throws ServiceException, Exception {
            try
            {
                minDistance = distance;
                String cityList = "Shortest distance is "+minDistance+", with route: ";
                for (int i = 0; i<bestOrder.length; i++){
                    bestOrder[i] = cities.get(i);
                    cityList += cityNames[bestOrder[i]];
                    if (i != bestOrder.length -1)
                        cityList += ", ";
                }
                System.out.println(cityList);
                service.sendQueueMessage("TSPQueue", new BrokeredMessage(cityList));
            }
            catch (ServiceException se)
            {
                throw se;
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        public static void main(String args[]){

            try {

                Configuration config = ServiceBusConfiguration.configureWithWrapAuthentication(
                        "your_service_bus_namespace", "your_service_bus_owner",
                        "your_service_bus_key",
                        ".servicebus.windows.net",
                        "-sb.accesscontrol.windows.net/WRAPv0.9");

                service = ServiceBusService.create(config);

                int numCities = 10;  // Use as the default, if no value is specified at command line.
                if (args.length != 0)
                {
                    if (args[0].toLowerCase().compareTo("createqueue")==0)
                    {
                        // No processing to occur other than creating the queue.
                        QueueInfo queueInfo = new QueueInfo("TSPQueue");

                        service.createQueue(queueInfo);

                        System.out.println("Queue named TSPQueue was created.");

                        System.exit(0);
                    }

                    if (args[0].toLowerCase().compareTo("deletequeue")==0)
                    {
                        // No processing to occur other than deleting the queue.
                        service.deleteQueue("TSPQueue");

                        System.out.println("Queue named TSPQueue was deleted.");

                        System.exit(0);
                    }

                    // Neither creating or deleting a queue.
                    // Assume the value passed in is the number of cities to solve.
                    numCities = Integer.valueOf(args[0]);  
                }

                System.out.println("Running for " + numCities + " cities.");

                List<Integer> startCities = new ArrayList<Integer>();
                List<Integer> restCities = new ArrayList<Integer>();
                startCities.add(0);
                for(int i = 1; i<numCities; i++)
                    restCities.add(i);
                distances = new double[numCities][numCities];
                cityNames = new String[numCities];
                buildDistances("c:\\TSP\\cities.txt", numCities);
                minDistance = -1;
                bestOrder = new int[numCities];
                permutation(startCities, 0, restCities);
                System.out.println("Final solution found!");
                service.sendQueueMessage("TSPQueue", new BrokeredMessage("Complete"));
            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }
        }

    }



## <a name="how-to-create-a-java-application-that-monitors-the-progress-of-the-compute-intensive-task"></a>Cómo crear una aplicación de Java que supervisa el progreso de la tarea de descarga intensiva

1. En su equipo de desarrollo, cree una aplicación de consola de Java utilizando el código de ejemplo al final de esta sección. En este tutorial, usaremos **TSPClient.java** como el nombre de archivo de Java. Como se muestra anteriormente, modifique la **su\_servicio\_bus\_espacio de nombres**, **su\_servicio\_bus\_propietario**, y **su\_servicio\_bus\_clave** marcadores de posición para usar el servicio de bus de **espacio de nombres**, **Emisor predeterminado** y valores de **Clave predeterminado** , respectivamente.
2. Exportar la aplicación a un JAR puede ejecutar y empaquetar las bibliotecas necesarias en el TARRO generado. En este tutorial, usaremos **TSPClient.jar** como el nombre JAR generado.

<p/>

    // TSPClient.java

    import java.util.Date;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;

    public class TSPClient
    {

        public static void main(String[] args)
        {
                try
                {

                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.println("Starting at " + dateFormat.format(date) + ".");

                    String namespace = "your_service_bus_namespace";
                    String issuer = "your_service_bus_owner";
                    String key = "your_service_bus_key";

                    Configuration config;
                    config = ServiceBusConfiguration.configureWithWrapAuthentication(
                            namespace, issuer, key,
                            ".servicebus.windows.net",
                            "-sb.accesscontrol.windows.net/WRAPv0.9");

                    ServiceBusContract service = ServiceBusService.create(config);

                    BrokeredMessage message;

                    int waitMinutes = 3;  // Use as the default, if no value is specified at command line.
                    if (args.length != 0)
                    {
                        waitMinutes = Integer.valueOf(args[0]);  
                    }

                    String waitString;

                    waitString = (waitMinutes == 1) ? "minute." : waitMinutes + " minutes.";

                    // This queue must have previously been created.
                    service.getQueue("TSPQueue");

                    int numRead;

                    String s = null;

                    while (true)
                    {

                        ReceiveQueueMessageResult resultQM = service.receiveQueueMessage("TSPQueue");
                        message = resultQM.getValue();

                        if (null != message && null != message.getMessageId())
                        {

                            // Display the queue message.
                            byte[] b = new byte[200];

                            System.out.print("From queue: ");

                            s = null;
                            numRead = message.getBody().read(b);
                            while (-1 != numRead)
                            {
                                s = new String(b);
                                s = s.trim();
                                System.out.print(s);
                                numRead = message.getBody().read(b);
                            }
                            System.out.println();
                            if (s.compareTo("Complete") == 0)
                            {
                                // No more processing to occur.
                                date = new Date();
                                System.out.println("Finished at " + dateFormat.format(date) + ".");
                                break;
                            }
                        }
                        else
                        {
                            // The queue is empty.
                            System.out.println("Queue is empty. Sleeping for another " + waitString);
                            Thread.sleep(60000 * waitMinutes);
                        }
                    }

            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }

        }

    }

## <a name="how-to-run-the-java-applications"></a>Cómo ejecutar las aplicaciones de Java
Ejecute la aplicación de descarga intensiva, en primer lugar para crear la cola, a continuación, para resolver el problema de Saleseman de viaje, que se agregará la mejor ruta actual a la cola de bus de servicio. Mientras la aplicación de descarga intensiva está ejecutando (o posteriormente), ejecutar el cliente para mostrar los resultados de la cola de bus de servicio.

### <a name="to-run-the-compute-intensive-application"></a>Para ejecutar la aplicación de descarga intensiva

1. Inicie sesión en la máquina virtual.
2. Crear una carpeta donde se ejecutará la aplicación. Por ejemplo, **c:\TSP**.
3. Copiar **TSPSolver.jar** a **c:\TSP**,
4. Cree un archivo llamado **c:\TSP\cities.txt** con el siguiente contenido.

        City_1, 1002.81, -1841.35
        City_2, -953.55, -229.6
        City_3, -1363.11, -1027.72
        City_4, -1884.47, -1616.16
        City_5, 1603.08, -1030.03
        City_6, -1555.58, 218.58
        City_7, 578.8, -12.87
        City_8, 1350.76, 77.79
        City_9, 293.36, -1820.01
        City_10, 1883.14, 1637.28
        City_11, -1271.41, -1670.5
        City_12, 1475.99, 225.35
        City_13, 1250.78, 379.98
        City_14, 1305.77, 569.75
        City_15, 230.77, 231.58
        City_16, -822.63, -544.68
        City_17, -817.54, -81.92
        City_18, 303.99, -1823.43
        City_19, 239.95, 1007.91
        City_20, -1302.92, 150.39
        City_21, -116.11, 1933.01
        City_22, 382.64, 835.09
        City_23, -580.28, 1040.04
        City_24, 205.55, -264.23
        City_25, -238.81, -576.48
        City_26, -1722.9, -909.65
        City_27, 445.22, 1427.28
        City_28, 513.17, 1828.72
        City_29, 1750.68, -1668.1
        City_30, 1705.09, -309.35
        City_31, -167.34, 1003.76
        City_32, -1162.85, -1674.33
        City_33, 1490.32, 821.04
        City_34, 1208.32, 1523.3
        City_35, 18.04, 1857.11
        City_36, 1852.46, 1647.75
        City_37, -167.44, -336.39
        City_38, 115.4, 0.2
        City_39, -66.96, 917.73
        City_40, 915.96, 474.1
        City_41, 140.03, 725.22
        City_42, -1582.68, 1608.88
        City_43, -567.51, 1253.83
        City_44, 1956.36, 830.92
        City_45, -233.38, 909.93
        City_46, -1750.45, 1940.76
        City_47, 405.81, 421.84
        City_48, 363.68, 768.21
        City_49, -120.3, -463.13
        City_50, 588.51, 679.33

5. En el símbolo del sistema, cambie al directorio c:\TSP.
6. Asegúrese de que la carpeta de Papelera del JRE está en la variable de entorno de ruta de acceso.
7. Deberá crear la cola de bus de servicio antes de ejecutar las permutaciones de solver TSP. Ejecute el comando siguiente para crear la cola de bus de servicio.

        java -jar TSPSolver.jar createqueue

8. Ahora que se crea la cola, puede ejecutar las permutaciones de solver TSP. Por ejemplo, ejecute el siguiente comando para ejecutar solver de 8 ciudades.

        java -jar TSPSolver.jar 8

 Si no especifica un número, se ejecutará para 10 ciudades. Como solver encuentra rutas más cortas actuales, se agregarlos a la cola.

> [AZURE.NOTE]
> Cuanto mayor sea el número que especifique, más tiempo se ejecutará solver. Por ejemplo, ejecutando 14 ciudades podrían tardar varios minutos y ejecución de 15 ciudades podría tardar varias horas. Aumento de ciudades de 16 o más podría provocar días de runtime (finalmente semanas, meses y años). Esto es debido a la rápido aumento en el número de permutaciones evaluada Solver como el número de ciudades aumenta.

### <a name="how-to-run-the-monitoring-client-application"></a>Cómo ejecutar la aplicación cliente de supervisión
1. Inicie sesión en el equipo donde se ejecutará la aplicación cliente. Esto no es necesario ser el mismo equipo que ejecuta la aplicación **TSPSolver** , aunque puede ser.
2. Crear una carpeta donde se ejecutará la aplicación. Por ejemplo, **c:\TSP**.
3. Copiar **TSPClient.jar** a **c:\TSP**,
4. Asegúrese de que la carpeta de Papelera del JRE está en la variable de entorno de ruta de acceso.
5. En el símbolo del sistema, cambie al directorio c:\TSP.
6. Ejecute el siguiente comando.

        java -jar TSPClient.jar

    Opcionalmente, especifique el número de minutos en suspensión entre comprobando la cola, pasando un argumento de línea de comandos. El período de suspensión predeterminado para comprobar la cola es 3 minutos, que se utiliza si ningún argumento de línea de comandos se pasa a **TSPClient**. Si desea usar un valor diferente para el intervalo de suspensión, por ejemplo, un minuto, ejecute el siguiente comando.

        java -jar TSPClient.jar 1

    El cliente se ejecutará hasta que se ve un mensaje de cola "Completa". Tenga en cuenta que si ejecutar varias apariciones de solver sin ejecutar al cliente, debe ejecutar el cliente de varias veces para vaciar la cola de completamente. Como alternativa, puede eliminar la cola y crear de nuevo. Para eliminar la cola, ejecute el siguiente comando de **TSPSolver** (no **TSPClient**).

        java -jar TSPSolver.jar deletequeue

    Solver se ejecutará hasta que termina examinando todas las rutas.

## <a name="how-to-stop-the-java-applications"></a>Cómo detener las aplicaciones de Java
Para los solver y aplicaciones cliente, puede presionar **Ctrl + C** para salir si desea que finalice antes de finalizar normal.


[solver_output]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/WA_JavaTSPSolver.png
[client_output]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/WA_JavaTSPClient.png
[svc_bus_node]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_02_SvcBusNode.jpg
[create_namespace]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[avail_namespaces]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[namespace_list]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_05_NamespaceList.jpg
[properties_pane]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_06_PropertiesPane.jpg
[default_key]: ./media/virtual-machines-windows-classic-java-run-compute-intensive-task/SvcBusQueues_07_DefaultKey.jpg
[add_ca_cert]: ../java-add-certificate-ca-store.md
