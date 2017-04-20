<properties
    pageTitle="Cómo usar la biblioteca de cliente de aplicaciones móviles Android"
    description="Cómo utilizar el cliente Android SDK para las aplicaciones móviles de Azure."
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>


# <a name="how-to-use-the-android-client-library-for-mobile-apps"></a>Cómo usar la biblioteca de cliente Android para aplicaciones móviles

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Esta guía muestra cómo usar al cliente de Android SDK para las aplicaciones móviles para implementar escenarios comunes, como por ejemplo:

- Consultas de datos (Insertar, actualizar y eliminar).
- Autenticación.
- Control de errores.
- Personalizar al cliente. 

También se hace un análisis más profundo en usados en aplicaciones móviles más comunes código de cliente.

Esta guía se centra en el SDK de Android del cliente.  Para obtener más información sobre el SDK de servidor para las aplicaciones móviles, vea [trabajar con back-end de .NET SDK] [ 10] o [cómo usar el back-end de Node.js SDK][11].

## <a name="reference-documentation"></a>Documentación de referencia

Puede encontrar la [referencia de la API de Javadocs] [ 12] para la biblioteca de cliente Android en GitHub.

## <a name="supported-platforms"></a>Plataformas admitidas

El SDK de Azure Mobile aplicaciones Android admite niveles de API 19 y 24 (KitKat a través de nueces).  

La autenticación de "flujo de servidor" utiliza una vista de Web para la interfaz de usuario presentado. Si el dispositivo no puede presentar una UI WebView, se necesitan otros métodos de autenticación que está fuera del ámbito del producto.  Este SDK no es adecuado para el tipo de inspección o del mismo modo restringidos dispositivos.

## <a name="setup-and-prerequisites"></a>El programa de instalación y los requisitos previos

Complete el tutorial [rápido de aplicaciones móviles](app-service-mobile-android-get-started.md) .  Esta tarea garantiza que se cumplen todos los requisitos previos para desarrollar aplicaciones móviles de Azure.  El tutorial también le ayuda a configurar su cuenta y crear su primer back-end de aplicación móvil.

Si decide no completar el tutorial rápido, realice las siguientes tareas:

- [crear un aplicación móvil de back-end] [ 13] para usar con la aplicación Android.
- En Studio Android, [actualice los archivos de compilación Gradle](#gradle-build).
- [Habilitar permisos de internet](#enable-internet).

###<a name="gradle-build"></a>Actualizar el archivo de generación de Gradle

Cambiar ambos archivos **build.gradle** :

1. Agregue este código en el archivo de *proyecto* nivel **build.gradle** dentro de la etiqueta *buildscript* :

        buildscript {
            repositories {
                jcenter()
            }
        }

2. Agregue este código en el archivo de nivel **build.gradle** *módulo aplicación* dentro de la etiqueta de *dependencias* :

        compile 'com.microsoft.azure:azure-mobile-android:3.1.0'

    Actualmente la versión más reciente está 3.1.0. Las versiones compatibles se enumeran [aquí][14].

###<a name="enable-internet"></a>Habilitar permisos de internet

Para obtener acceso a Azure, la aplicación debe tener el permiso de INTERNET habilitado. Si aún no está habilitada, agregue la siguiente línea de código al archivo **AndroidManifest.xml** :

    <uses-permission android:name="android.permission.INTERNET" />

## <a name="the-basics-deep-dive"></a>El análisis detallado de conceptos básicos

Esta sección describen algunos del código de la aplicación de tutorial rápido que hace referencia al uso de las aplicaciones móviles de Azure.  

###<a name="data-object"></a>Definir las clases de datos de cliente

Para obtener acceso a datos de tablas de SQL Azure, definir las clases de datos de cliente que corresponden a las tablas de la aplicación móvil de back-end. Ejemplos de este tema supone una tabla denominada **ToDoItem**, que tiene las columnas siguientes:

- Id.
- texto
- completar

El correspondiente escribió el objeto de cliente:

    public class ToDoItem {
        private String id;
        private String text;
        private Boolean complete;
    }

El código reside en un archivo llamado **ToDoItem.java**.

Si la tabla de SQL Azure contiene más columnas, puede agregar los campos correspondientes a esta clase.  Por ejemplo, si el campo (objeto de transferencia de datos) tenía una columna de prioridad de tipo entero, a continuación, puede agregar este campo, junto con sus métodos Get y Set:

    private Integer priority;

    /**
    * Returns the item priority
    */
    public Integer getPriority() {
        return mPriority;
    }
    
    /**
    * Sets the item priority
    *
    * @param priority
    *            priority to set
    */
    public final void setPriority(Integer priority) {
        mPriority = priority;
    }

Para obtener información sobre cómo crear tablas adicionales en el servidor de aplicaciones móviles, vea [Cómo: definir un controlador de tabla] [ 15] (servidor .NET) o [Definir tablas mediante un esquema dinámico] [ 16] (servidor de Node.js). Para Node.js back-end, también puede usar la configuración de **fáciles tablas** en el [portal de Azure].

###<a name="create-client"></a>Cómo: crear el contexto de cliente

Este código crea el objeto de **MobileServiceClient** que se usa para tener acceso a la aplicación móvil de back-end. El código está en el `onCreate` método de la clase de **actividad** especificada en *AndroidManifest.xml* como una acción de **principal** y el **SELECTOR de** categoría. En el código de tutorial rápido, aparece en el archivo **ToDoActivity.java** .

        MobileServiceClient mClient = new MobileServiceClient(
            "MobileAppUrl", // Replace with the Site URL
            this)

En este código, reemplace `MobileAppUrl` con la dirección URL de la aplicación móvil de back-end, que se encuentra en el [portal de Azure] en el módulo de la aplicación móvil de back-end. Para esta línea de código para compilar, también debe agregar la siguiente instrucción **Importar** :

    import com.microsoft.windowsazure.mobileservices.*;

###<a name="instantiating"></a>Cómo: crear una referencia de tabla

Es la manera más sencilla de consultar o modificar datos en el back-end mediante *escribió el modelo de programación*, ya que Java es un lenguaje inflexible. Este modelo proporciona fluido serialización JSON y deserialización mediante la [gson] [ 3] biblioteca al enviar datos entre tablas y objetos de cliente en el servidor de SQL Azure.

Para obtener acceso a una tabla, cree primero una [MobileServiceTable] [ 8] objeto llamando el **getTable** método en la [MobileServiceClient][9].  Este método tiene dos sobrecargas:

    public class MobileServiceClient {
        public <E> MobileServiceTable<E> getTable(Class<E> clazz);
        public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
    }

En el siguiente código, **mClient** es una referencia al objeto MobileServiceClient.  La primera sobrecarga se usa en el que el nombre de la clase y el nombre de la tabla son los mismos, y el tutorial de:

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);

La segunda sobrecarga se usa cuando el nombre de tabla es diferente del nombre de clase: el primer parámetro es el nombre de tabla.

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

###<a name="binding"></a>Cómo: enlazar datos a la interfaz de usuario

Enlace de datos implica tres componentes:

- El origen de datos
- El diseño de la pantalla
- El adaptador que reúne los dos.

En nuestro ejemplo de código, se devuelven los datos de la tabla de Mobile aplicaciones SQL Azure **ToDoItem** en una matriz. Esta actividad es un modelo común para aplicaciones de datos.  Consultas de base de datos a menudo devuelven un conjunto de filas que obtiene el cliente en una lista o matriz. En este ejemplo, la matriz es el origen de datos.

El código especifica un diseño de pantalla que define la vista de los datos que aparece en el dispositivo.  Los dos se enlazan junto con un adaptador, que en este código es una extensión de la **ArrayAdapter&lt;ToDoItem&gt; ** clase.

#### <a name="layout"></a>Cómo: definir el diseño

El diseño definido por varios fragmentos de código XML. Proporciona un diseño existente, el siguiente código representa **ListView** queremos para rellenar con los datos del servidor.

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>

En el código anterior, el atributo *listitem* especifica el identificador del diseño de una fila individual en la lista. Este código especifica una casilla de verificación y su texto asociado y se inicia una vez para cada elemento de la lista. Este diseño no muestra el campo **identificador** y un diseño más complejo debería especificar campos adicionales en la pantalla. Este código se encuentra en el archivo **row_list_to_do.xml** .

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="horizontal">
        <CheckBox
            android:id="@+id/checkToDoItem"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/checkbox_text" />
    </LinearLayout>


#### <a name="adapter"></a>Cómo: definir el adaptador

Dado que el origen de datos de la vista es una matriz de **ToDoItem**, nos subclase nuestro adaptador desde una **ArrayAdapter&lt;ToDoItem&gt; ** clase. Esta subclase genera una vista para cada **ToDoItem** con el diseño de **row_list_to_do** .

En nuestro código se define la clase siguiente que es una extensión de la **ArrayAdapter&lt;E&gt; ** clase:

    public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {

Reemplazar el método de **getView** adaptador. Por ejemplo:

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }

        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });


        return row;
    }

Creamos una instancia de esta clase en nuestra actividad como sigue:

    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

El segundo parámetro al constructor ToDoItemAdapter es una referencia en el diseño. Ahora podemos crear una instancia **ListView** y asignar al adaptador a **ListView**.

    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);

### <a name="api"></a>La estructura de la API

Operaciones de tabla de aplicaciones móviles y llamadas a la API personalizadas son asincrónicas. Usar los objetos [futuro] y [AsyncTask] para los métodos asincrónicos relacionados con consultas, inserciones, actualizaciones y eliminaciones. Uso de futuros facilita realizar varias operaciones en un subproceso en segundo plano sin tener que tratar con varias devoluciones de llamada anidadas.

Revisar el archivo **ToDoActivity.java** en el proyecto Tutorial Android desde el [portal de Azure] para obtener un ejemplo.

#### <a name="use-adapter"></a>Cómo: utilizar el adaptador

Ya está listo para utilizar el enlace de datos. El código siguiente muestra cómo obtener los elementos de la tabla y rellena el adaptador local con los artículos devueltos.

    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }

Llame el adaptador en cualquier momento que modificar la tabla **ToDoItem** . Dado que las modificaciones se realizan en una base de registro por registro, manejar una sola fila en lugar de una colección. Al insertar un elemento, llame al método **Agregar** en el adaptador; al eliminar, llame al método **remove** .

##<a name="querying"></a>Cómo: consultar los datos de la aplicación móvil de back-end

Esta sección describe cómo realizar consultas en el servidor de la aplicación móvil, que incluye las siguientes tareas:

- [Devolver todos los elementos]
- [Filtrar los datos devueltos]
- [Ordenar los datos devueltos]
- [Devolver datos en las páginas]
- [Seleccionar columnas específicas]
- [Concatenar métodos de consulta](#chaining)

### <a name="showAll"></a>Cómo: devolver todos los elementos de una tabla

La siguiente consulta devuelve todos los elementos de la tabla **ToDoItem** .

    List<ToDoItem> results = mToDoTable.execute().get();

La variable de *resultados* devuelve el conjunto de resultados de la consulta como una lista.

### <a name="filtering"></a>Cómo: filtrar los datos devueltos

Ejecución de la siguiente consulta devuelve todos los elementos de la tabla de **ToDoItem** donde **completa** es igual a **false**.

    List<ToDoItem> result = mToDoTable.where()
                                .field("complete").eq(false)
                                .execute().get();

**mToDoTable** es la referencia a la tabla de servicios móviles que hemos creado previamente.

Definir un filtro mediante la llamada al método **donde** en la referencia de tabla. El método **donde** va seguido de un método de **campo** seguido de un método que especifica el predicado lógico. Métodos de predicado posibles incluyen **EC** (igual a), **ne** (no es igual), **gt** (mayor que), **página** (mayor o igual que), **lt** (menor que), **le** (menor o igual a). Estos métodos le permiten comparar los campos número y cadena para valores específicos.

Puede filtrar por fechas. Los métodos siguientes le permiten comparar el campo de fecha completo o partes de la fecha: **año**, **mes**, **día**, **hora**, **minuto**y **segundo**. En el ejemplo siguiente se agrega un filtro para los elementos cuya *fecha de vencimiento* es igual a 2013.

    mToDoTable.where().year("due").eq(2013).execute().get();

Los métodos siguientes admiten filtros complejos en campos de cadena: **startsWith**, **endsWith**, **concatenación**, **subcadena**, **indexOf**, **Reemplazar**, **toLower**, **toUpper**, **Recortar**y **longitud**. En el ejemplo siguiente se filtra para las filas de la tabla donde la columna de *texto* comienza con "PRI0."

    mToDoTable.where().startsWith("text", "PRI0").execute().get();

Se admiten los siguientes métodos de operador en los campos números: **Agregar**, **sub**, **mul**, **div**, **mod**, **floor**, **techo**y **redondear**. En el ejemplo siguiente se filtra para las filas de la tabla donde la **duración** es un número par.

    mToDoTable.where().field("duration").mod(2).eq(0).execute().get();

Puede combinar predicados con estos métodos lógicos: **y**, **o** y **no**. En el ejemplo siguiente se combinan dos de los ejemplos anteriores.

    mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
                .execute().get();

Agrupar y anidar operadores lógicos:

    mToDoTable.where()
                .year("due").eq(2013)
                    .and
                (startsWith("text", "PRI0").or().field("duration").gt(10))
                .execute().get();

Para obtener información más detallada y ejemplos de filtrado, consulte [Explorar la variedad del modelo de consulta de cliente Android](http://hashtagfail.com/post/46493261719/mobile-services-android-querying).

### <a name="sorting"></a>Cómo: ordenar datos devueltos

El código siguiente devuelve que todos los elementos de una tabla de **ToDoItems** orden ascendente por el campo de *texto* . *mToDoTable* es la referencia a la tabla de back-end que creó anteriormente:

    mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

El primer parámetro del método **orderBy** es una cadena igual al nombre del campo que desea ordenar. El segundo parámetro utiliza la enumeración **QueryOrder** para especificar si el orden ascendente o descendente.  Si va a filtrar con el método ***donde*** , el método ***donde*** se debe invocar antes del método ***orderBy*** .

### <a name="paging"></a>Cómo: devolver datos en las páginas

El primer ejemplo muestra cómo seleccionar los cinco elementos de una tabla. La consulta devuelve los elementos de una tabla de **ToDoItems**. **mToDoTable** es la referencia a la tabla de back-end que creó anteriormente:

    List<ToDoItem> result = mToDoTable.top(5).execute().get();


Esta es una consulta que omite los cinco primeros elementos y, a continuación, devuelve los cinco siguientes:

    mToDoTable.skip(5).top(5).execute().get();

### <a name="selecting"></a>Cómo: seleccionar columnas específicas

El código siguiente muestra cómo devolver todos los elementos de una tabla de **ToDoItems**, pero solo mostrará los campos **completado** y el **texto** . **mToDoTable** es la referencia a la tabla de back-end que hemos creado previamente.

    List<ToDoItemNarrow> result = mToDoTable.select("complete", "text").execute().get();

Los parámetros de la función select son los nombres de cadena de columnas de la tabla que se desea devolver.

El método **select** debe seguir métodos como **donde** y **orderBy**. Puede ir seguido de métodos de paginación como **parte superior**.

### <a name="chaining"></a>Cómo: concatenar métodos de consulta

Se pueden concatenar los métodos de consultar tablas de back-end. Encadenar métodos de consulta permite seleccionar columnas específicas de filas filtradas que están ordenados y paginar. Puede crear filtros lógicos complejos.
Cada método de consulta devuelve un objeto de consulta. Para finalizar la serie de métodos y ejecutar la consulta, llame al método **Ejecutar** . Por ejemplo:

    mToDoTable.where()
        .year("due").eq(2013)
        .and().startsWith("text", "PRI0")
        .or().field("duration").gt(10)
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .top(20)
        .execute().get();

Los métodos de consulta encadenados deben pedir como sigue:

1. Métodos de filtrado (**donde**).
2. Métodos de ordenación (**orderBy**).
3. Métodos de selección (**Seleccionar**).
4. métodos (**Omitir** y **superior**) de paginación.

##<a name="inserting"></a>Cómo: insertar datos en el back-end

Crear una instancia de la clase *ToDoItem* y establezca sus propiedades.

    ToDoItem item = new ToDoItem();
    item.text = "Test Program";
    item.complete = false;

A continuación, usar **insert()** para insertar un objeto:

    ToDoItem entity = mToDoTable.insert(item).get();

Las coincidencias de entidad devuelta los datos que se inserta en la tabla de back-end, incluido el identificador y otros valores de configuración en el servidor.

Tablas de aplicaciones móviles requieren una columna de clave principal denominada **id**. De forma predeterminada, esta columna es una cadena. El valor predeterminado de la columna ID es un GUID.  Puede proporcionar otros valores únicos, como las direcciones de correo electrónico o nombres de usuario. Cuando no se proporciona un valor de identificador de cadena para un registro insertado, el servidor genera un nuevo GUID.

Valores de cadena ID proporcionan las siguientes ventajas:

+ Se pueden generar identificadores sin realizar un recorrido en la base de datos.
+ Registros son más fáciles de combinación a partir de diferentes tablas o bases de datos.
+ Valores del identificador integran mejor con lógica de la aplicación.

Valores de cadena ID son **necesarias** para la compatibilidad con la sincronización sin conexión.

##<a name="updating"></a>Cómo: actualizar datos en una aplicación móvil

Para actualizar datos en una tabla, pase el objeto nuevo al método **update()** .

    mToDoTable.update(item).get();

En este ejemplo, el *elemento* es una referencia a una fila de la tabla *ToDoItem* , que ha tenido algunos cambios realizados en él.
Se actualiza la fila con el mismo **id** .

##<a name="deleting"></a>Cómo: eliminar datos en una aplicación móvil

El código siguiente muestra cómo eliminar datos de una tabla especificando el objeto de datos.

    mToDoTable.delete(item);

También puede eliminar un elemento especificando el campo **identificador** de la fila a eliminar.

    String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
    mToDoTable.delete(myRowId);

##<a name="lookup"></a>Cómo: buscar un elemento específico

Buscar un elemento con un campo específico **id** con el método **lookUp()** :

    ToDoItem result = mToDoTable
                        .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
                        .get();

##<a name="untyped"></a>Cómo: trabajar con datos sin tipo

El modelo de programación sin tipo proporciona un control exacto sobre serialización JSON.  Hay algunos escenarios comunes que se desea usar un modelo de programación sin tipo. Por ejemplo, si la tabla de back-end contiene muchas columnas y solo debe hacer referencia a un subconjunto de las columnas.  El modelo escrito debe definir todas las aplicaciones móviles columnas de la tabla en la clase de datos.  

La mayoría de las llamadas a API para tener acceso a datos es similar a las llamadas de programación escritas. La principal diferencia es que en el modelo sin tipo invocar métodos en el objeto **MobileServiceJsonTable** , en lugar del objeto **MobileServiceTable** .

### <a name="json_instance"></a>Cómo: crear una instancia de una tabla sin tipo

De forma similar al modelo de escrito, iniciar obteniendo una referencia de tabla, pero en este caso es un objeto **MobileServicesJsonTable** . Obtener la referencia llamando el **getTable** método en una instancia del cliente:

    private MobileServiceJsonTable mJsonToDoTable;
    //...
    mJsonToDoTable = mClient.getTable("ToDoItem");

Una vez que haya creado una instancia de la **MobileServiceJsonTable**, tiene prácticamente la misma API disponible como con el modelo de programación escrito. En algunos casos, los métodos toman un parámetro sin tipo en lugar de un parámetro escrito.

### <a name="json_insert"></a>Cómo: insertar en una tabla sin tipo

El código siguiente muestra cómo hacer una instrucción insert. El primer paso es crear una [JsonObject][1], que forma parte de la [gson] [ 3] biblioteca.

    JsonObject jsonItem = new JsonObject();
    jsonItem.addProperty("text", "Wake up");
    jsonItem.addProperty("complete", false);

A continuación, Use **insert()** para insertar el objeto sin tipo en la tabla.

    mJsonToDoTable.insert(jsonItem).get();

Si necesita obtener el identificador del objeto insertado, use el método de **getAsJsonPrimitive()** .

    jsonItem.getAsJsonPrimitive("id").getAsInt());

### <a name="json_delete"></a>Cómo: eliminar de una tabla sin tipo

El código siguiente muestra cómo eliminar una instancia, en este caso, la misma instancia de **JsonObject** que se creó en el ejemplo anterior *Insertar* . El código es el mismo que en el caso escrito, pero el método tiene una firma diferente porque hace referencia a un **JsonObject**.

         mToDoTable.delete(jsonItem);

También puede eliminar una instancia directamente mediante su identificador:

         mToDoTable.delete(ID);

### <a name="json_get"></a>Cómo: devolver todas las filas de una tabla sin tipo

El código siguiente muestra cómo recuperar una tabla completa. Puesto que está utilizando una tabla de JSON, puede recuperar selectivamente solo algunas de las columnas de la tabla.

    public void showAllUntyped(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final JsonElement result = mJsonToDoTable.execute().get();
                    final JsonArray results = result.getAsJsonArray();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (JsonElement item : results) {
                                String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                                String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                                Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                                ToDoItem mToDoItem = new ToDoItem();
                                mToDoItem.setId(ID);
                                mToDoItem.setText(mText);
                                mToDoItem.setComplete(mComplete);
                                mAdapter.add(mToDoItem);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

El mismo conjunto de filtros, filtrado y la paginación métodos que están disponibles para el modelo escrito están disponibles para el modelo sin tipo.

##<a name="custom-api"></a>Cómo: llamar a una API personalizada

Una API personalizada le permite definir extremos personalizados que exponen la funcionalidad del servidor que no se asignan a una instrucción insert, actualizar, eliminar o la operación de lectura. Mediante una API personalizada, puede tener más control sobre la mensajería incluidos leer y establecer encabezados del mensaje HTTP y definir un formato de cuerpo de mensaje que no sea JSON.

Desde un cliente Android, llame al método **invokeApi** para llamar al extremo API personalizado. En el ejemplo siguiente se muestra cómo llamar a un extremo de API denominado **completeAll**, que devuelve una clase de colección denominada **MarkAllResult**.

    public void completeItem(View view) {

        ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll", MarkAllResult.class );

            Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }

                @Override
                public void onSuccess(MarkAllResult result) {
                    createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
                    refreshItemsFromTable();
                }
            });
        }

El método **invokeApi** se llama en el cliente, que se envía una solicitud de entrada a la nueva API personalizada. El resultado devuelto por la API personalizada se muestra en un cuadro de diálogo de mensaje, como los errores. Otras versiones de **invokeApi** le permiten enviar un objeto en el cuerpo de la solicitud opcionalmente, especificar el método HTTP y enviar parámetros de la consulta con la solicitud. También se proporcionan versiones sin tipo de **invokeApi** .

##<a name="authentication"></a>Cómo: agregar autenticación para su aplicación

Tutoriales ya describen detalladamente cómo agregar estas características.

Aplicación de servicio compatible con la [autenticación de usuarios de la aplicación](app-service-mobile-android-get-started-users.md) con una amplia variedad de proveedores de identidades externo: Azure Active Directory, Google, Microsoft Account, Twitter y Facebook. Puede establecer permisos en las tablas para restringir el acceso para operaciones específicas a solo los usuarios autenticados. También puede utilizar la identidad de los usuarios autenticados para implementar reglas de autorización en su servidor.

Se admiten dos flujos de autenticación: un flujo de **servidor** y un **cliente** . El flujo de servidor proporciona la experiencia de autenticación más sencilla, como se basa en la interfaz de web de proveedores de identidades.  Ningún SDK adicional es necesarios para implementar la autenticación de flujo del servidor. Autenticación de servidor flujo no proporciona una integración profunda en el dispositivo móvil y solo se recomienda para prueba de escenarios de concepto.

El flujo de cliente permite una mayor integración con capacidades específicas del dispositivo, como el inicio de sesión único que se basa en el SDK proporcionada por el proveedor de identidades.  Por ejemplo, puede integrar el SDK de Facebook en la aplicación móvil.  El cliente móvil cambia en la aplicación de Facebook y confirma el inicio de sesión antes de intercambiar volver a la aplicación móvil.

Cuatro pasos son necesarios para habilitar la autenticación en su aplicación:

- Registrar la aplicación para la autenticación con un proveedor de identidades.
- Configurar el servicio de aplicaciones de back-end.
- Restringir los permisos de la tabla a los usuarios autenticados únicamente en el servicio de aplicaciones de back-end.
- Agregar código de autenticación para su aplicación.

Puede establecer permisos en las tablas para restringir el acceso para operaciones específicas a solo los usuarios autenticados. También puede usar al SID de un usuario autenticado para modificar solicitudes.  Para obtener más información, revise [empezar con la autenticación] y la documentación del servidor SDK cómo.

### <a name="caching"></a>Cómo: agregar código de autenticación para su aplicación

El código siguiente inicia un proceso de inicio de sesión de flujo de servidor con el proveedor de servicios de Google:

    MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google);

Obtener el identificador del usuario que haya iniciado sesión desde un **MobileServiceUser** con el método **getUserId** . Para obtener un ejemplo de cómo usar futuros para llamar a la API de inicio de sesión asincrónica, vea [Introducción a la autenticación].

### <a name="caching"></a>Cómo: tokens de autenticación de caché

Almacenamiento en caché tokens de autenticación debe almacenar el identificador de usuario y el símbolo de autenticación localmente en el dispositivo. La próxima vez que inicie la aplicación, active la memoria caché y si estos valores están presentes, puede omitir el registro en el procedimiento y rehidratarse del cliente con estos datos. No obstante, estos datos son importantes y debe almacenarse cifrado de seguridad en caso de que obtiene robe el teléfono.

Puede ver un ejemplo completo de cómo tokens de autenticación de caché en la [sección de tokens de autenticación de caché][7].

Cuando intenta usar un token caducado, recibe una respuesta *401 no autorizado* . Puede controlar los errores de autenticación mediante filtros.  Filtros interceptan solicitudes del servicio de aplicaciones de back-end. El código de filtro prueba la respuesta para un 401, activa el proceso de inicio de sesión y, a continuación, reanuda la solicitud que generó el 401. 

## <a name="adal"></a>Cómo: autenticar a los usuarios con el Active Directory Authentication Library

Puede usar la biblioteca de autenticación de Active Directory (ADAL) para iniciar la sesión de los usuarios en la aplicación con Azure Active Directory. Con un inicio de sesión de flujo de cliente a menudo es preferible al uso de la `loginAsync()` métodos tal como se proporciona una apariencia más nativo de UX y permite personalizar adicional.

1. Configurar la aplicación móvil de back-end para el inicio de sesión de AAD siguiendo el tutorial de [cómo configurar la aplicación de servicio de inicio de sesión de Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md) . Asegúrese de completar el paso opcional de registrar una aplicación nativa de cliente.

2. Instalar ADAL modificando el archivo build.gradle para incluir las definiciones siguientes:

```
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
packagingOptions {
    exclude 'META-INF/MSFTSIG.RSA'
    exclude 'META-INF/MSFTSIG.SF'
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
    compile 'com.android.support:support-v4:23.0.0'
}
```

3. Agregue el código siguiente a la aplicación y las sustituciones siguientes:

* Reemplazar **Aquí de autoridad de INSERCIÓN** con el nombre del inquilino en el que se aprovisione la aplicación. El formato debe ser https://login.windows.net/contoso.onmicrosoft.com. Este valor se puede copiar en la ficha de dominio de Azure Active Directory en [Azure clásico portal].

* Reemplazar **Insertar recurso ID aquí** con el ID de cliente para la aplicación móvil de back-end. Puede obtener el identificador de cliente de la ficha **Opciones avanzadas** , en **Configuración de Azure Active Directory** en el portal.

* Reemplazar **Insertar cliente ID aquí** con el ID de cliente que ha copiado de la aplicación cliente nativa.

* Reemplazar **Insertar REDIRECCIÓN URI aquí** con extremo de _/.auth/login/done_ de su sitio, mediante la combinación de HTTPS. Este valor debe ser similar a _https://contoso.azurewebsites.net/.auth/login/done_.

        private AuthenticationContext mContext;

        private void authenticate() {
            String authority = "INSERT-AUTHORITY-HERE";
            String resourceId = "INSERT-RESOURCE-ID-HERE";
            String clientId = "INSERT-CLIENT-ID-HERE";
            String redirectUri = "INSERT-REDIRECT-URI-HERE";
            try {
                mContext = new AuthenticationContext(this, authority, true);
                mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
            } catch (Exception exc) {
                exc.printStackTrace();
            }
        }

        private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    Log.d(TAG, "Cancelled");
                } else {
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    Log.d(TAG, "Token is empty");
                } else {
                    try {
                        JSONObject payload = new JSONObject();
                        payload.put("access_token", result.getAccessToken());
                        ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                        Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                            @Override
                            public void onFailure(Throwable exc) {
                                exc.printStackTrace();
                            }
                            @Override
                            public void onSuccess(MobileServiceUser user) {
                                Log.d(TAG, "Login Complete");
                            }
                        });
                    }
                    catch (Exception exc){
                        Log.d(TAG, "Authentication error:" + exc.getMessage());
                    }
                }
            }
        };

        @Override
        protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            super.onActivityResult(requestCode, resultCode, data);
            if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
            }
        }

## <a name="how-to-add-push-notification-to-your-app"></a>Cómo: agregar la notificación de inserción a la aplicación

Puede [leer una descripción general] [ 6] que describe cómo Hubs de notificación de Microsoft Azure es compatible con una amplia variedad de notificaciones de inserción.  En [este tutorial][5], se envía una notificación de inserción a todos los dispositivos cada vez que se inserta un registro.

## <a name="how-to-add-offline-sync-to-your-app"></a>Cómo: agregar la sincronización sin conexión a su aplicación

El tutorial rápido contiene código que implementa la sincronización sin conexión. Busque el prefijo con comentarios de código:

    // Offline Sync

Quitando el comentario de las líneas de código siguientes puede implementar la sincronización sin conexión y puede agregar código similar a otro código de aplicaciones móviles.

##<a name="customizing"></a>Cómo: personalizar el cliente

Hay varias formas de personalizar el comportamiento predeterminado del cliente.

### <a name="headers"></a>Cómo: personalizar encabezados de solicitud

Configurar un **ServiceFilter** para agregar un encabezado HTTP personalizado a cada solicitud:

    private class CustomHeaderFilter implements ServiceFilter {

        @Override
        public ListenableFuture<ServiceFilterResponse> handleRequest(
                    ServiceFilterRequest request,
                    NextServiceFilterCallback next) {

            runOnUiThread(new Runnable() {

                @Override
                public void run() {
                    request.addHeader("My-Header", "Value");                    }
            });

            SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
            try {
                ServiceFilterResponse response = next.onNext(request).get();
                result.set(response);
            } catch (Exception exc) {
                result.setException(exc);
            }
        }

### <a name="serialization"></a>Cómo: personalizar la serialización

El cliente supone que los nombres de tabla, los nombres de columna y los datos se escribe en el back-end todos coinciden exactamente los objetos de datos definidos en el cliente. Puede haber varias razones, ¿por qué no pueden ajustarse a los nombres de servidor y cliente. En su caso, es recomendable hacer los siguientes tipos de personalizaciones:

- Los nombres de columna utilizados en la tabla de servicios de aplicación no coinciden con los nombres que se usa en el cliente.
- Usar una tabla de servicio de la aplicación que tiene un nombre distinto de la clase que está asignado a en el cliente.
- Activar el uso de mayúsculas automático propiedad.
- Agregar propiedades complejas a un objeto.

### <a name="columns"></a>Cómo: asignar diferentes nombres de cliente y de servidor

Suponga que el código de cliente de Java utiliza los nombres de estilo Java estándar para las propiedades de objeto **ToDoItem** , como las siguientes propiedades:

- Extrae
- mText
- mComplete
- DURACION

Serializar los nombres de cliente en los nombres JSON que coinciden con los nombres de columna de la tabla **ToDoItem** en el servidor. El código siguiente utiliza la [gson] [ 3] biblioteca para anotar las propiedades:

    @com.google.gson.annotations.SerializedName("text")
    private String mText;

    @com.google.gson.annotations.SerializedName("id")
    private int mId;

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;

    @com.google.gson.annotations.SerializedName("duration")
    private String mDuration;

### <a name="table"></a>Cómo: asignar nombres de tabla diferente entre el cliente y el back-end

Asignar el nombre de la tabla de cliente a un nombre de tabla de servicios móviles diferentes mediante un reemplazo de la [getTable()] [ 4] método:

    mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### <a name="conversions"></a>Cómo: automatizar las asignaciones de nombre de columna

Puede especificar una estrategia de conversión que se aplica a todas las columnas utilizando la [gson] [ 3] API. La biblioteca de cliente Android usa [gson] [ 3] entre bastidores serializar objetos de Java a datos JSON antes de envían los datos al servicio de la aplicación de Azure.  El código siguiente utiliza el método **setFieldNamingStrategy()** para definir la estrategia. En este ejemplo se eliminará el carácter inicial (una "m") y, a continuación, minúsculas el carácter siguiente para cada nombre de campo. Por ejemplo, podría convertir "extrae" en "id".

    client.setGsonBuilder(
        MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(new FieldNamingStrategy() {
            public String translateName(Field field) {
                String name = field.getName();
                return Character.toLowerCase(name.charAt(1))
                    + name.substring(2);
                }
            });

Este código se debe ejecutar antes de utilizar el **MobileServiceClient**.

### <a name="complex"></a>Cómo: almacenar una propiedad de objeto o matriz en una tabla

Hasta ese momento, los ejemplos de serialización han implicado a tipos simples como enteros y cadenas.  Tipos simples serializan fácilmente en JSON.  Si desea agregar un objeto complejo que no serializar automáticamente a JSON, necesitamos proporcionar el método de serialización JSON.  Para ver un ejemplo de cómo proporcionar serialización JSON personalizada, consulte la entrada de blog [serialización de personalización con la biblioteca de gson en el cliente móvil servicios Android][2].

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[The API structure]: #api
[How to: Query data from a mobile service]: #querying
[Devolver todos los elementos]: #showAll
[Filtrar los datos devueltos]: #filtering
[Ordenar los datos devueltos]: #sorting
[Devolver datos en las páginas]: #paging
[Seleccionar columnas específicas]: #selecting
[How to: Concatenate query methods]: #chaining
[How to: Bind data to the user interface]: #binding
[How to: Define the layout]: #layout
[How to: Define the adapter]: #adapter
[How to: Use the adapter]: #use-adapter
[How to: Insert data into a mobile service]: #inserting
[How to: update data in a mobile service]: #updating
[How to: Delete data in a mobile service]: #deleting
[How to: Look up a specific item]: #lookup
[How to: Work with untyped data]: #untyped
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching
[How to: Handle errors]: #errors
[How to: Design unit tests]: #tests
[How to: Customize the client]: #customizing
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next Steps]: #next-steps
[Setup and Prerequisites]: #setup

<!-- Images. -->

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Portal de Azure]: https://portal.azure.com
[Introducción a la autenticación]: app-service-mobile-android-get-started-users.md
[1]: http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html
[2]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: http://go.microsoft.com/fwlink/p/?LinkId=290801
[4]: http://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: http://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: http://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[Futuro]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html