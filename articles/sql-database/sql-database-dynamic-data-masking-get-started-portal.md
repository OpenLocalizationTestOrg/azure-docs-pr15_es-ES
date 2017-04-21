<properties
   pageTitle="Introducción a SQL base de datos dinámicos datos Masking (Portal de Azure clásico)"
   description="Cómo empezar a trabajar con SQL bases de datos dinámicos datos Masking en el Portal de clásico de Azure"
   services="sql-database"
   documentationCenter=""
   authors="ronitr"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/10/2016"
   ms.author="ronitr; ronmat; v-romcal; sstein"/>

# <a name="get-started-with-sql-database-dynamic-data-masking-azure-classic-portal"></a>Introducción a SQL base de datos dinámicos datos Masking (Portal de Azure clásico)

> [AZURE.SELECTOR]
- [Datos dinámicos Masking - Portal de Azure](sql-database-dynamic-data-masking-get-started.md)

## <a name="overview"></a>Información general

Masking datos dinámicos de SQL base de datos limita la exposición de datos confidenciales por masking a los usuarios sin privilegios. Masking dinámico de datos es compatible con la versión de V12 de base de datos de SQL Azure.

Masking dinámico de datos le ayuda a evitar el acceso no autorizado a los datos confidenciales por permitir a los clientes designar la cantidad de los datos confidenciales para revelar con un mínimo impacto en el nivel de aplicación. Es una característica de seguridad basada en la directiva que oculta la información confidencial en el conjunto de resultados de una consulta sobre los campos de base de datos designado, mientras que los datos de la base de datos no cambia.

Por ejemplo, un representante del servicio en un centro de llamadas puede identificar a las personas que llaman por varios dígitos de su número de tarjeta de crédito o número de seguridad social, pero no se deberían exponer totalmente esos elementos de datos con el representante del servicio. Puede definir una regla de máscara que todas las máscaras, pero los últimos cuatro dígitos de cualquier número de seguridad social o número de tarjeta de crédito en el resultado conjunto de cualquier consulta. Por ejemplo, puede definirse una máscara de datos apropiado para proteger los datos de la información de identificación personal (PII), para que un desarrollador puede crear una consulta entornos de producción para solucionar problemas sin infringir reglamentaciones.

## <a name="sql-database-dynamic-data-masking-basics"></a>Conceptos básicos de Masking datos dinámicos de SQL base de datos

Configurar datos dinámicos masking directiva en el Portal de clásico de Azure en la pestaña de auditoría y seguridad de la base de datos.


> [AZURE.NOTE] Para configurar los datos dinámicos masking en el Portal de Azure, consulte [Introducción a SQL base de datos dinámicos datos Masking (Portal de Azure)](sql-database-dynamic-data-masking-get-started.md).


### <a name="dynamic-data-masking-permissions"></a>Permisos de masking dinámico de datos

Masking datos dinámicos puede configurar la base de datos de Azure, Administrador de servidor o roles de oficial de seguridad.

### <a name="dynamic-data-masking-policy"></a>Directiva de masking dinámico de datos

* **Usuarios SQL excluidos de masking** - un conjunto de usuarios SQL o identidades AAD que se obtienen datos sin máscara en los resultados de la consulta SQL. Observe que los usuarios con privilegios de administrador siempre será excluidos de masking y podrá ver los datos originales sin ninguna máscara.

* **Masking reglas** - un conjunto de reglas que definen los campos designados a estar enmascarado y la función masking que va a utilizar. Los campos designados pueden definirse mediante un nombre de esquema de la base de datos, el nombre de la tabla y el nombre de columna.

* **Funciones de masking** - un conjunto de métodos que controlan la exposición de datos para diferentes escenarios.

| Función masking | Masking lógica |
|----------|---------------|
| **Predeterminado**  |**Masking completa según los tipos de datos de los campos designados**<br/><br/>• Use XXXX o menos x si el tamaño del campo es menor que 4 caracteres para los tipos de datos string (nchar, ntext, nvarchar).<br/>• Use el valor cero para los tipos de datos numéricos (bigint, bit, decimal, int, dinero, numérico, smallint, smallmoney, tinyint, flotación, real).<br/>• Use 01-01-1900 para tipos de datos de fecha y hora (fecha, datetime2, datetime, datetimeoffset, smalldatetime, tiempo).<br/>• Para variante SQL, el valor predeterminado del tipo actual se usa.<br/>• Para XML del documento <masked/> se utiliza.<br/>• Use un valor vacío para tipos de datos especiales (marca de tiempo de tabla, hierarchyid, GUID, binario, image, varbinary espacial tipos).
| **Tarjeta de crédito** |**Masking método que expone los últimos cuatro dígitos de los campos designados** y se agrega una constante de cadena como un prefijo en el formulario de una tarjeta de crédito.<br/><br/>XXXX-XXXX-XXXX-1234|
| **Número de seguridad social** |**Masking método que expone los últimos cuatro dígitos de los campos designados** y se agrega una constante de cadena como un prefijo en el formulario de un número de seguridad social estadounidense.<br/><br/>LARGO Y LARGOB-XX-1234 |
| **Correo electrónico** | **Masking método que expone la primera letra y reemplaza el dominio con XXX.com** utilizando un prefijo constante de cadena en forma de una dirección de correo electrónico.<br/><br/>aXX@XXXX.com |
| **Número aleatorio** | **Masking método que genera un número aleatorio** según los límites seleccionados y los tipos de datos reales. Si los límites designados son iguales, la función masking será un número constante.<br/><br/>![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started-portal/1_DDM_Random_number.png) |
| **Texto personalizado** | **Método que expone el primer y último carácter de masking** y agrega una cadena de relleno personalizada en el medio. Si la cadena original es menor que expuesto prefijo y sufijo, se utilizará la cadena de relleno.<br/>prefijo [relleno] sufijo<br/><br/>![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started-portal/2_DDM_Custom_text.png) |


<a name="Anchor1"></a>

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-classic-portal"></a>Configurar masking dinámico de datos para la base de datos con el Portal de clásico de Azure

1. Inicie el Portal de clásico Azure en [https://manage.windowsazure.com](https://manage.windowsazure.com).

2. Haga clic en la base de datos que desee ocultar y, a continuación, haga clic en la pestaña **seguridad y auditoría** .

3. **Datos dinámicos masking**, haga clic en **habilitado** para habilitar la característica de masking dinámico de datos.  

4. Escriba los usuarios SQL o identidades AAD que se deben excluir de masking y tengan acceso a los datos confidenciales sin máscara. Esto debe ser una lista de valores separados por punto y coma de usuarios. Tenga en cuenta que los usuarios con privilegios de administrador siempre tienen acceso a los datos originales sin máscara.

    >[AZURE.TIP] Para hacerlo de manera que la capa de aplicación puede mostrar datos confidenciales para usuarios con privilegios de la aplicación, agregue el usuario SQL o identidad AAD que usa la aplicación para la base de datos de la consulta. Se recomienda que esta lista contienen un número mínimo de usuarios con privilegios para minimizar la exposición de la información confidencial.

    ![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started-portal/4_ddm_policy_classic_portal.png)

5. En la parte inferior de la página en la barra de menús, haga clic en **Agregar máscara** para abrir el masking ventana de configuración de la regla.

6. Seleccione el **esquema**, **tabla** y **columna** de las listas desplegables para definir los campos designados que se estar enmascarados.

7. Elegir una **Función MASKING** de la lista de datos confidenciales masking categorías.

    ![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started-portal/5_DDM_Add_Masking_Rule_Classic_Portal.png)

8. Haga clic en **Aceptar** en los datos masking ventana regla para actualizar el conjunto de reglas en los datos dinámicos masking directiva de masking.

9. Haga clic en **Guardar** para guardar la directiva de masking nuevo o actualizado.


## <a name="set-up-dynamic-data-masking-for-your-database-using-transact-sql-statements"></a>Configurar datos dinámicos masking para la base de datos mediante instrucciones Transact-SQL

Ver [datos dinámicos Masking](https://msdn.microsoft.com/library/mt130841.aspx).

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Configurar datos dinámicos masking para la base de datos con los cmdlets de Powershell

Vea [Cmdlets de base de datos SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Configurar masking dinámico de datos para la base de datos con la API de REST

Vea [las operaciones de bases de datos SQL Azure](https://msdn.microsoft.com/library/dn505719.aspx).
