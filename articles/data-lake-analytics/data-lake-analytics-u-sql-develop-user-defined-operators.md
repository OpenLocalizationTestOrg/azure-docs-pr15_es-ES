<properties 
   pageTitle="Desarrollar operadores U SQL definidas por el usuario para las tareas de análisis de lago de datos de Azure | Azure" 
   description="Obtenga información sobre cómo desarrollar operadores definidas por el usuario para utilizar y reutilizar en trabajos de análisis de datos lago. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>


# <a name="develop-u-sql-user-defined-operators-for-azure-data-lake-analytics-jobs"></a>Desarrollar operadores U SQL definidas por el usuario para las tareas de análisis de lago de datos de Azure

Obtenga información sobre cómo desarrollar operadores definidas por el usuario para utilizar y reutilizar en trabajos de análisis de datos lago. Desarrollará un operador personalizado para convertir los nombres de país.

##<a name="prerequisites"></a>Requisitos previos

- Actualizar Visual Studio de 2015, 2013 de Visual Studio con Visual C++ instalado 4 o Visual Studio 2012 
- Microsoft Azure SDK para .NET versión 2.5 o posterior.  Instalar mediante el instalador de plataforma Web.
- Una cuenta de análisis de datos lago.  Consulte [Introducción a Azure datos lago análisis con el Portal de Azure](data-lake-analytics-get-started-portal.md).
- Pasar por el tutorial de [Introducción a Azure datos lago análisis U SQL Studio](data-lake-analytics-u-sql-get-started.md) .
- Conectarse a Azure, consulte [Introducción a Azure datos lago análisis U SQL Studio](data-lake-analytics-u-sql-get-started.md#connect-to-azure). 
- Cargar el origen de datos, vea [Introducción a Azure datos lago análisis U SQL Studio](data-lake-analytics-u-sql-get-started.md#upload-source-data-files). 

## <a name="define-and-use-user-defined-operator-in-u-sql"></a>Definir y usar el operador definidas por el usuario en SQL U

**Para crear y enviar un trabajo U SQL** 

1. En el menú **archivo** , haga clic en **nuevo**y, a continuación, haga clic en **proyecto**.
2. Seleccione el tipo de **Proyecto U SQL** .

    ![nuevo proyecto de Visual Studio de SQL U](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Haga clic en **Aceptar**. Visual studio crea una solución con un archivo de Script.usql.
4. Desde el **Explorador de soluciones**, expanda Script.usql y, a continuación, haga doble clic en **Script.usql.cs**.
5. Pegue el código siguiente en el archivo:

        using Microsoft.Analytics.Interfaces;
        using System.Collections.Generic;
        
        namespace USQL_UDO
        {
            public class CountryName : IProcessor
            {
                private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
                {
                    {
                        "Deutschland", "Germany"
                    },
                    {
                        "Schwiiz", "Switzerland"
                    },
                    {
                        "UK", "United Kingdom"
                    },
                    {
                        "USA", "United States of America"
                    },
                    {
                        "中国", "PR China"
                    }
                };
        
                public override IRow Process(IRow input, IUpdatableRow output)
                {
        
                    string UserID = input.Get<string>("UserID");
                    string Name = input.Get<string>("Name");
                    string Address = input.Get<string>("Address");
                    string City = input.Get<string>("City");
                    string State = input.Get<string>("State");
                    string PostalCode = input.Get<string>("PostalCode");
                    string Country = input.Get<string>("Country");
                    string Phone = input.Get<string>("Phone");
        
                    if (CountryTranslation.Keys.Contains(Country))
                    {
                        Country = CountryTranslation[Country];
                    }
                    output.Set<string>(0, UserID);
                    output.Set<string>(1, Name);
                    output.Set<string>(2, Address);
                    output.Set<string>(3, City);
                    output.Set<string>(4, State);
                    output.Set<string>(5, PostalCode);
                    output.Set<string>(6, Country);
                    output.Set<string>(7, Phone);
        
                    return output.AsReadOnly();
                }
            }
        }

5. Abra Script.usql y pegue el siguiente script U SQL:

        @drivers =
            EXTRACT UserID      string,
                    Name        string,
                    Address     string,
                    City        string,
                    State       string,
                    PostalCode  string,
                    Country     string,
                    Phone       string
            FROM "/Samples/Data/AmbulanceData/Drivers.txt"
            USING Extractors.Tsv(Encoding.Unicode);
        
        @drivers_CountryName =
            PROCESS @drivers
            PRODUCE UserID string,
                    Name string,
                    Address string,
                    City string,
                    State string,
                    PostalCode string,
                    Country string,
                    Phone string
            USING new USQL_UDO.CountryName();    
        
        OUTPUT @drivers_CountryName
            TO "/Samples/Outputs/Drivers.csv"
            USING Outputters.Csv(Encoding.Unicode);

6. Desde el **Explorador de soluciones**, haga clic con el botón secundario del mouse en **Script.usql**y, a continuación, haga clic en **Generar secuencia de comandos**.
6. Desde el **Explorador de soluciones**, haga clic con el botón secundario del mouse en **Script.usql**y, a continuación, haga clic en **Enviar Script**.
7. Si todavía no lo ha conectarse a su suscripción de Azure, usted será el símbolo del sistema escriba sus credenciales de cuenta de Azure.
7. Haga clic en **Enviar**. Resultados de envío y vínculo de trabajo están disponibles en la ventana de resultados cuando se haya completado la presentación.
8. Debe hacer clic en el botón Actualizar para ver el estado del trabajo más reciente y actualizar la pantalla.

**Para ver los resultados de la tarea**

1. Desde el **Explorador de servidores**, expanda **Azure**, expanda **El análisis de datos lago**, expandir su cuenta de análisis de datos lago, expandir **Cuentas de almacenamiento**, haga el almacenamiento predeterminado y, a continuación, haga clic en **Explorador**. 
2. Expandir muestras, expanda los resultados y, a continuación, haga doble clic en **controladores.csv**.


##<a name="see-also"></a>Vea también

- [Introducción a análisis de datos lago con PowerShell](data-lake-analytics-get-started-powershell.md)
- [Introducción a análisis de lago de datos con el portal de Azure](data-lake-analytics-get-started-portal.md)
- [Utilizar datos lago Tools para Visual Studio para desarrollar aplicaciones de SQL U](data-lake-analytics-data-lake-tools-get-started.md)
