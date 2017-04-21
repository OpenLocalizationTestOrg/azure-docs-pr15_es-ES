<properties
    pageTitle="Siempre se cifra: Proteger datos confidenciales en la base de datos de SQL Azure con cifrado de base de datos | Microsoft Azure"
    description="Proteger datos confidenciales en la base de datos SQL en minutos."
    keywords="cifrar datos, el cifrado de sql, el cifrado de base de datos, datos confidenciales, siempre cifrados"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="sstein"/>

# <a name="always-encrypted-protect-sensitive-data-in-sql-database-and-store-your-encryption-keys-in-the-windows-certificate-store"></a>Siempre se cifra: Proteger datos confidenciales en la base de datos SQL y almacenar las claves de cifrado en el almacén de certificados de Windows

> [AZURE.SELECTOR]
- [Depósito de clave de Azure](sql-database-always-encrypted-azure-key-vault.md)
- [Almacén de certificados de Windows](sql-database-always-encrypted.md)


Este artículo le muestra cómo proteger datos confidenciales en una base de datos SQL con cifrado de base de datos mediante el [Asistente para cifrado siempre](https://msdn.microsoft.com/library/mt459280.aspx) en [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). También muestra cómo almacenar las claves de cifrado en el almacén de certificados de Windows.

Siempre cifrado es una nueva tecnología de cifrado de datos en la base de datos de SQL Azure y SQL Server que le ayuda a proteger los datos confidenciales almacenados en el servidor, durante el movimiento entre cliente y servidor mientras los datos se utiliza, cómo garantizar que los datos confidenciales nunca aparece como texto sin formato dentro del sistema de base de datos. Después de cifrar datos, solo las aplicaciones cliente o servidores de aplicación que tienen acceso a las claves pueden acceder a datos de texto simple. Para obtener información detallada, vea [Siempre cifrados (motor de base de datos)](https://msdn.microsoft.com/library/mt163865.aspx).

Después de configurar la base de datos para usar siempre cifrados, creará una aplicación de cliente en C# con Visual Studio para trabajar con los datos cifrados.

Siga los pasos de este artículo para obtener información sobre cómo configurar el cifrado siempre para una base de datos de SQL Azure. En este artículo, aprenderá a realizar las siguientes tareas:

- Utilice al asistente siempre cifrados en SSMS crear [Siempre claves de cifrado](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
    - Crear una [Columna de clave (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
    - Crear una [clave de cifrado de columna (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
- Crear una tabla de base de datos y cifrar columnas.
- Crear una aplicación que inserta, se selecciona y muestra los datos de las columnas cifradas.

## <a name="prerequisites"></a>Requisitos previos

En este tutorial, necesitará:

- Una cuenta de Azure y de suscripción. Si no tiene una, suscribirse a una [versión de prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).
- [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) versión 13.0.700.242 o posterior.
- [.NET framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) o posterior (en el equipo cliente).
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).



## <a name="create-a-blank-sql-database"></a>Crear una base de datos SQL en blanco
1. Inicie sesión en el [portal de Azure](https://portal.azure.com/).
2. Haga clic en **nuevo** > **datos + almacenamiento** > **base de datos SQL**.
3. Crear una base de datos **en blanco** con el nombre de **sesión** en un servidor nuevo o existente. Para obtener instrucciones detalladas sobre cómo crear una base de datos en el portal de Azure, consulte [crear una base de datos SQL en minutos](sql-database-get-started.md).

    ![Crear una base de datos en blanco](./media/sql-database-always-encrypted/create-database.png)

Más adelante en el tutorial, necesitará la cadena de conexión. Después de crea la base de datos, vaya a la nueva base de datos de sesión y copie la cadena de conexión. Puede obtener la cadena de conexión en cualquier momento, pero es fácil copiarlo cuando esté en el portal de Azure.

1. Haga clic en **bases de datos SQL** > **sesión** > **mostrar cadenas de conexión de base de datos**.
2. Copie la cadena de conexión de **ADO.NET**.

    ![Copie la cadena de conexión](./media/sql-database-always-encrypted/connection-strings.png)


## <a name="connect-to-the-database-with-ssms"></a>Conectarse a la base de datos con SSMS

Abra SSMS y conectarse al servidor con la base de datos de sesión.


1. Abra SSMS. (Haga clic en **Conectar** > **Motor de base de datos** para abrir la ventana de **Conectar con el servidor** si no está abierto).
2. Escriba el nombre del servidor y las credenciales. Se puede encontrar el nombre del servidor en el módulo de base de datos SQL y en la cadena de conexión que copió anteriormente. Escriba el nombre completo del servidor, incluyendo *database.windows.net*.

    ![Copie la cadena de conexión](./media/sql-database-always-encrypted/ssms-connect.png)

Si se abre la ventana **Nueva regla de Firewall** , inicie sesión en Azure y deje que SSMS a crear una nueva regla de firewall para usted.


## <a name="create-a-table"></a>Crear una tabla

En esta sección, creará una tabla para mantener los datos de pacientes. Se trata de una tabla normal inicialmente--configurará cifrado en la siguiente sección.

1. Expanda **bases de datos**.
1. Haga clic en la base de datos de **sesión** y haga clic en **Nueva consulta**.
2. Pegue el siguiente Transact-SQL (T-SQL) en la nueva ventana de consulta y **Ejecutar** él.


        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1),
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL,
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO


## <a name="encrypt-columns-configure-always-encrypted"></a>Cifrar columnas (configurar siempre cifrados)

SSMS proporciona un Asistente para configurar fácilmente siempre cifrados mediante la configuración de las columnas cifradas, CEK y CMK para usted.

1. Expanda **bases de datos** > **sesión** > **tablas**.
2. Haga clic en la tabla **pacientes** y seleccione **Cifrar columnas** para abrir al asistente siempre cifrados:

    ![Cifrar columnas](./media/sql-database-always-encrypted/encrypt-columns.png)

El Asistente de cifrado siempre incluye las siguientes secciones: **Selección de columna**, **Configuración de clave principal** (CMK), **validación**y **Resumen**.

### <a name="column-selection"></a>Selección de columna ###

En la página de **Introducción** para abrir la página de **Selección de columna** , haga clic en **siguiente** . En esta página, seleccione las columnas que quiere cifrar, [el tipo de cifrado y qué clave de cifrado de columna (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) usar.

Cifrar la información **Nº de SS** y **fecha de nacimiento** para cada paciencia. La columna **Nº de SS** usará el cifrado determinista, que es compatible con las búsquedas de igualdad, combinaciones y agrupar por. La columna **fecha de nacimiento** usará el cifrado aleatorio, que no admite operaciones.

Establecer el **Tipo de cifrado** para la columna **Nº de SS** **deterministas** y la columna **fecha de nacimiento** a **Randomized**. Haga clic en **siguiente**.

![Cifrar columnas](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>Configuración de la clave principal###

La página de **Configuración de la clave principal** es donde configurar su CMK y seleccione el proveedor de almacén de claves donde se almacena el CMK. Actualmente, puede almacenar una CMK en el almacén de certificados de Windows, depósito de clave de Azure o un módulo de seguridad de hardware (HMS). Este tutorial muestra cómo almacenar las claves en el almacén de certificados de Windows.

Compruebe que está seleccionado el **almacén de certificados de Windows** y haga clic en **siguiente**.

![Configuración de la clave principal](./media/sql-database-always-encrypted/master-key-configuration.png)


### <a name="validation"></a>Validación###

Puede cifrar las columnas ahora o guardar una secuencia de comandos de PowerShell para que se ejecute más tarde. En este tutorial, seleccione **Continuar para finalizar ahora** y haga clic en **siguiente**.

### <a name="summary"></a>Resumen###

Compruebe que la configuración es todo corregir y haga clic en **Finalizar** para completar la configuración de cifrado siempre.

![Resumen](./media/sql-database-always-encrypted/summary.png)


### <a name="verify-the-wizards-actions"></a>Comprobar acciones del Asistente

Una vez finalizado el asistente, la base de datos está configurado para cifrar siempre. El Asistente para realiza las siguientes acciones:

- Crea un CMK.
- Crea un CEK.
- Configurar las columnas seleccionadas para el cifrado. La tabla **pacientes** actualmente no tiene datos, pero ahora se cifran los datos existentes en las columnas seleccionadas.

Puede comprobar la creación de las claves de SSMS yendo a la **sesión** > **seguridad** > **Siempre claves de cifrado**. Ahora puede ver las nuevas claves generadas por el Asistente para usted.


## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Crear una aplicación de cliente que funciona con los datos cifrados

Ahora que siempre se cifran está configurado, puede crear una aplicación que realiza *inserta* y *selecciona* en las columnas cifradas. Para ejecutar correctamente la aplicación de ejemplo, debe ejecutarlo en el mismo equipo donde ejecutó el asistente siempre cifrados. Para ejecutar la aplicación en otro equipo, debe implementar los certificados de cifrado siempre al equipo que ejecuta la aplicación de cliente.  

> [AZURE.IMPORTANT] La aplicación debe utilizar los objetos [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) al pasar datos de texto simple en el servidor con columnas siempre cifrados. Pasar valores literales sin usar los objetos SqlParameter se producirá una excepción.


1. Abra Visual Studio y cree una nueva aplicación de consola de C#. Asegúrese de que el proyecto está establecido en **.NET Framework 4.6** o posterior.
2. Nombre del proyecto **AlwaysEncryptedConsoleApp** y haga clic en **Aceptar**.

![Nueva aplicación de consola](./media/sql-database-always-encrypted/console-app.png)



## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Modificar la cadena de conexión para habilitar siempre cifrados

En esta sección se explica cómo habilitar siempre cifrados en la cadena de conexión de base de datos. Se modifique la aplicación de consola que acaba de crear en la siguiente sección, "Aplicación de consola de ejemplo de cifrado siempre".


Para habilitar siempre cifrados, debe agregar la palabra clave de la **Configuración de cifrado de la columna** a la cadena de conexión y establecer en **habilitado**.

Puede establecer directamente en la cadena de conexión o se puede establecer mediante una [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). La aplicación de ejemplo en la sección siguiente muestra cómo usar **SqlConnectionStringBuilder**.

> [AZURE.NOTE] Este es el único cambio necesario en una aplicación de cliente específica siempre cifrados. Si tiene una aplicación existente que almacena la cadena de conexión externamente (es decir, en un archivo de configuración), es posible que pueda habilitar siempre cifrados sin cambiar el código.


### <a name="enable-always-encrypted-in-the-connection-string"></a>Habilitar siempre cifrados en la cadena de conexión

Agregue la siguiente palabra clave en la cadena de conexión:

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Habilitar siempre cifrado con SqlConnectionStringBuilder

El código siguiente muestra cómo habilitar siempre cifrado estableciendo la [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) en [habilitado](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;



## <a name="always-encrypted-sample-console-application"></a>Siempre cifrado aplicación de consola de ejemplo

Este ejemplo muestra cómo:

- Modifique la cadena de conexión para habilitar siempre cifrados.
- Insertar datos en las columnas cifradas.
- Seleccionar un registro por un filtro de un valor específico en una columna de cifrado.

Reemplazar el contenido de **Program.cs** con el código siguiente. Reemplazar la cadena de conexión para la variable de cadena de conexión global en la línea directamente encima del método Main con la cadena de conexión válida desde el portal de Azure. Este es el único cambio que debe realizar para este código.

Ejecute la aplicación para ver siempre cifrados en acción.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;

    namespace AlwaysEncryptedConsoleApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"Replace with your connection string";

        static void Main(string[] args)
        {
            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();


            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient() {
                SSN = "999-99-0001", FirstName = "Orlando", LastName = "Gee", BirthDate = DateTime.Parse("01/04/1964") });
            InsertPatient(new Patient() {
                SSN = "999-99-0002", FirstName = "Keith", LastName = "Harris", BirthDate = DateTime.Parse("06/20/1977") });
            InsertPatient(new Patient() {
                SSN = "999-99-0003", FirstName = "Donna", LastName = "Carreras", BirthDate = DateTime.Parse("02/09/1973") });
            InsertPatient(new Patient() {
                SSN = "999-99-0004", FirstName = "Janet", LastName = "Gates", BirthDate = DateTime.Parse("08/31/1985") });
            InsertPatient(new Patient() {
                SSN = "999-99-0005", FirstName = "Lucy", LastName = "Harrington", BirthDate = DateTime.Parse("05/06/1993") });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now let's locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 123-45-6789):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
         VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
    }


## <a name="verify-that-the-data-is-encrypted"></a>Comprobar que los datos están cifrados

Rápidamente, puede comprobar que los datos reales en el servidor está cifrados al consultar los datos de **pacientes** con SSMS. (Utilice donde aún no está habilitada la configuración de cifrado de columna de la conexión actual).

Ejecutar la consulta siguiente en la base de datos de sesión.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Puede ver que las columnas cifradas no contienen los datos de texto simple.

   ![Nueva aplicación de consola](./media/sql-database-always-encrypted/ssms-encrypted.png)


Para usar SSMS para tener acceso a los datos de texto, puede agregar la **configuración de cifrado de columna = habilitado** parámetro para la conexión.

1. En SSMS, haga clic en el servidor en **El Explorador de objetos**y, a continuación, haga clic en **Desconectar**.
2. Haga clic en **Conectar** > **Motor de base de datos** para abrir la ventana de **Conectar con el servidor** y, a continuación, haga clic en **Opciones**.
3. Haga clic en **Parámetros de conexión adicionales** y tipo **configuración de cifrado de columna = habilitado**.

    ![Nueva aplicación de consola](./media/sql-database-always-encrypted/ssms-connection-parameter.png)

4. Ejecutar la consulta siguiente en la base de datos de **sesión** .

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     Ahora puede ver los datos de texto en las columnas cifradas.


    ![Nueva aplicación de consola](./media/sql-database-always-encrypted/ssms-plaintext.png)



> [AZURE.NOTE] Si se conecta con SSMS (o cualquier cliente) desde un equipo diferente, no tendrá acceso a las claves de cifrado y no podrá descifrar los datos.



## <a name="next-steps"></a>Pasos siguientes
Después de crear una base de datos que usa cifrado siempre, desea hacer lo siguiente:

- Ejecutar este ejemplo en un equipo distinto. No tendrá acceso a las claves de cifrado, por lo que no tendrán acceso a los datos de texto simple y no se ejecutará correctamente.
- [Girar y limpiar las claves](https://msdn.microsoft.com/library/mt607048.aspx).
- [Migrar datos que ya está cifrados con cifrado siempre](https://msdn.microsoft.com/library/mt621539.aspx).
- [Certificados siempre implementar cifrados a otros equipos de cliente](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) (consulte la sección "Realizar certificados disponibles para las aplicaciones y los usuarios").

## <a name="related-information"></a>Información relacionada

- [Siempre cifrados (desarrollo de cliente)](https://msdn.microsoft.com/library/mt147923.aspx)
- [Cifrado de datos transparente](https://msdn.microsoft.com/library/bb934049.aspx)
- [Cifrado de SQL Server](https://msdn.microsoft.com/library/bb510663.aspx)
- [Asistente para siempre cifrada](https://msdn.microsoft.com/library/mt459280.aspx)
- [Blog siempre cifrada](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)
