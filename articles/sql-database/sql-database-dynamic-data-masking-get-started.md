<properties
   pageTitle="Introducción a SQL base de datos dinámicos datos Masking (Portal de Azure)"
   description="Cómo empezar a trabajar con SQL bases de datos dinámicos datos Masking en el Portal de Azure"
   services="sql-database"
   documentationCenter=""
   authors="ronitr"
   manager="jhubbard"
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/10/2016"
   ms.author="ronitr; ronmat; v-romcal; sstein"/>


# <a name="get-started-with-sql-database-dynamic-data-masking-azure-portal"></a>Introducción a SQL base de datos dinámicos datos Masking (Portal de Azure)

> [AZURE.SELECTOR]
- [Datos dinámicos Masking - Azure Portal clásica](sql-database-dynamic-data-masking-get-started-portal.md)

## <a name="overview"></a>Información general

Masking datos dinámicos de SQL base de datos limita la exposición de datos confidenciales por masking a los usuarios sin privilegios. Masking dinámico de datos es compatible con la versión de V12 de base de datos de SQL Azure.

Masking dinámico de datos le ayuda a evitar el acceso no autorizado a los datos confidenciales por permitir a los clientes designar la cantidad de los datos confidenciales para revelar con un mínimo impacto en el nivel de aplicación. Es una característica de seguridad basada en la directiva que oculta la información confidencial en el conjunto de resultados de una consulta sobre los campos de base de datos designado, mientras que los datos de la base de datos no cambia.

Por ejemplo, un representante del servicio en un centro de llamadas puede identificar a las personas que llaman por varios dígitos de su número de tarjeta de crédito o número de seguridad social, pero no se deberían exponer totalmente esos elementos de datos con el representante del servicio. Puede definir una regla de máscara que todas las máscaras, pero los últimos cuatro dígitos de cualquier número de seguridad social o número de tarjeta de crédito en el resultado conjunto de cualquier consulta. Por ejemplo, puede definirse una máscara de datos apropiado para proteger los datos de la información de identificación personal (PII), para que un desarrollador puede crear una consulta entornos de producción para solucionar problemas sin infringir reglamentaciones.

## <a name="sql-database-dynamic-data-masking-basics"></a>Conceptos básicos de Masking datos dinámicos de SQL base de datos

Configurar un dinámico masking directiva en el Portal de Azure seleccionando la operación Masking dinámico de datos en el módulo de configuración de base de datos SQL o el módulo de configuración de datos.


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
| **Número aleatorio** | **Masking método que genera un número aleatorio** según los límites seleccionados y los tipos de datos reales. Si los límites designados son iguales, la función masking será un número constante.<br/><br/>![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Texto personalizado** | **Método que expone el primer y último carácter de masking** y agrega una cadena de relleno personalizada en el medio. Si la cadena original es menor que expuesto prefijo y sufijo, se utilizará la cadena de relleno. <br/>prefijo [relleno] sufijo<br/><br/>![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |


<a name="Anchor1"></a>
### <a name="recommended-fields-to-mask"></a>Campos recomendados para la máscara

El motor de recomendaciones DDM indicadores determinados campos de la base de datos como campos potencialmente confidenciales, que pueden ser buenos candidatos para masking. En el módulo Masking de datos dinámicos en el portal de, verá las columnas recomendadas para la base de datos. Todo lo que debe hacer es haga clic en **Agregar máscaras** para una o más columnas y, a continuación, **Guardar** para aplicar una máscara de estos campos.

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Configurar masking dinámico de datos para la base de datos con el Portal de Azure

1. Inicie el Portal de Azure en [https://portal.azure.com](https://portal.azure.com).

2. Vaya a la hoja de configuración de la base de datos que incluye los datos importantes que desea ocultar.

3. Haga clic en el mosaico **Masking dinámico de datos** que se inicia el módulo de configuración **Masking dinámico de datos** .

    * Como alternativa, puede desplazarse hacia abajo hasta la sección de **operaciones** y haga clic en **Masking dinámico de datos**.

    ![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>


4. En el módulo de configuración **Masking dinámico de datos** puede ver algunas columnas de la base de datos que ha marcado el motor de recomendaciones para masking. Para aceptar las recomendaciones, simplemente haga clic en **Agregar máscaras** para una o más columnas y una máscara se creará en función del tipo predeterminado para esta columna. Puede cambiar la función masking haciendo clic en la regla de máscara y editar el masking formato del campo a otro formato de su elección. Asegúrese de hacer clic en **Guardar** para guardar la configuración.

    ![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>


5. Para agregar una máscara de cualquier columna de la base de datos, en la parte superior del módulo de configuración de **Masking dinámico de datos** haga clic en **Agregar máscara** para abrir el módulo de configuración de **Agregar regla Masking**

    ![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>

6. Seleccione el **esquema**, **tabla** y **columna** para definir el campo designado que se estar enmascarado.

7. Elija un **Formato de campo Masking** de la lista de datos confidenciales masking categorías.

    ![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>     

8. Haga clic en **Guardar** los datos masking módulo de regla para actualizar el conjunto de reglas en los datos dinámicos masking directiva de masking.

9. Escriba los usuarios SQL o identidades AAD que se deben excluir de masking y tengan acceso a los datos confidenciales sin máscara. Esto debe ser una lista de valores separados por punto y coma de usuarios. Tenga en cuenta que los usuarios con privilegios de administrador siempre tienen acceso a los datos originales sin máscara.

    ![Panel de navegación](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    >[AZURE.TIP] Para hacerlo de manera que la capa de aplicación puede mostrar datos confidenciales para usuarios con privilegios de la aplicación, agregue el usuario SQL o identidad AAD que usa la aplicación para la base de datos de la consulta. Se recomienda que esta lista contienen un número mínimo de usuarios con privilegios para minimizar la exposición de la información confidencial.

10. Haga clic en **Guardar** los datos masking módulo de configuración para guardar la directiva de masking nuevo o actualizado.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Configurar datos dinámicos masking para la base de datos con los cmdlets de Powershell

Vea [Cmdlets de base de datos SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx).


## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Configurar masking dinámico de datos para la base de datos con la API de REST

Vea [las operaciones de bases de datos SQL Azure](https://msdn.microsoft.com/library/dn505719.aspx).
