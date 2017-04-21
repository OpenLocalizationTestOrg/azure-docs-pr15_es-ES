<properties
    pageTitle="Desarrollo de aplicaciones de Web Python matraz con DocumentDB | Microsoft Azure"
    description="Revise un tutorial de base de datos sobre el uso de DocumentDB para almacenar y tener acceso a datos desde una aplicación web de Python matraz hospedada en Azure. Buscar soluciones de desarrollo de aplicaciones." 
    keywords="Desarrollo de aplicaciones, tutorial de base de datos, matraz python, aplicación web de python, python web desarrollo, documentdb, azure, Microsoft azure"
    services="documentdb"
    documentationCenter="python"
    authors="syamkmsft"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.date="08/25/2016"
    ms.author="syamk"/>

# <a name="python-flask-web-application-development-with-documentdb"></a>Desarrollo de aplicaciones de Web Python matraz con DocumentDB

> [AZURE.SELECTOR]
- [.NET](documentdb-dotnet-application.md)
- [Node.js](documentdb-nodejs-application.md)
- [Java](documentdb-java-application.md)
- [Python](documentdb-python-application.md)

En este tutorial se muestra cómo usar DocumentDB de Azure para almacenar y datos de access desde una Python hospedado en Azure de aplicación web y se da por supuesto que tiene alguna experiencia anterior con Python y sitios Web de Azure.

Este tutorial de base de datos abarca:

1. La creación y el aprovisionamiento de una cuenta de DocumentDB.
2. Crear una aplicación de MVC Python.
3. Conectarse a y con Azure DocumentDB desde la aplicación web.
4. Implementación de la aplicación web a sitios Web de Azure.

Siguiendo este tutorial, creará una aplicación de voto sencilla que permite votar un sondeo.

![Captura de pantalla de la aplicación de web de lista todo creada por este tutorial de base de datos](./media/documentdb-python-application/image1.png)


## <a name="database-tutorial-prerequisites"></a>Requisitos previos de los tutoriales de base de datos

Antes de seguir las instrucciones de este artículo, debe asegurarse de que tiene instalado lo siguiente:

- Una cuenta de Azure active. Si no tiene una cuenta, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- [2013 de Visual Studio](http://www.visualstudio.com/) o superior, o [Visual Studio Express](), que es la versión gratuita. Las instrucciones de este tutorial se escriben específicamente para Visual Studio de 2015. 
- Herramientas de Python para Visual Studio desde [GitHub](http://microsoft.github.io/PTVS/). Este tutorial usa Python herramientas de 2015 VS. 
- Azure SDK Python para Visual Studio, versión 2,4 o superior disponible desde [azure.com](https://azure.microsoft.com/downloads/). Hemos usado el SDK de Microsoft Azure para Python 2.7.
- Python 2.7 de [python.org][2]. Hemos usado Python 2.7.11. 

> [AZURE.IMPORTANT] Si va a instalar Python 2.7 por primera vez, asegúrese de que en la pantalla Personalizar Python 2.7.11, selecciona **Agregar python.exe a la ruta de acceso**.
> 
>    ![Captura de pantalla de la pantalla Personalizar Python 2.7.11, donde debe seleccionar agrega python.exe a la ruta de acceso](./media/documentdb-python-application/image2.png)

- Compilador de Microsoft Visual C++ para Python 2.7 desde el [Centro de descarga de Microsoft][3].

## <a name="step-1-create-a-documentdb-database-account"></a>Paso 1: Crear una cuenta de base de datos DocumentDB

Empecemos creando una cuenta de DocumentDB. Si ya tiene una cuenta, vaya a [paso 2: crear una nueva aplicación web de Python matraz](#step-2:-create-a-new-python-flask-web-application).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

<br/>
Ahora trataremos cómo crear una nueva aplicación web de Python matraz desde el principio hacia arriba.

## <a name="step-2-create-a-new-python-flask-web-application"></a>Paso 2: Crear una nueva aplicación web de Python matraz

1. En Visual Studio, en el menú **archivo** , apunte a **nuevo**y, a continuación, haga clic en **proyecto**.

    Aparece el cuadro de diálogo **Nuevo proyecto** .

2. En el panel izquierdo, expanda **plantillas** y, a continuación, **Python**y, a continuación, haga clic en **Web**. 

3. Seleccione **Matraz Web proyecto** en el panel central, a continuación, en el **nombre del** cuadro tipo **tutorial**y, a continuación, haga clic en **Aceptar**. Recuerde que los nombres de paquete Python deben ser todo a minúsculas, como se describe en la [Guía de estilo de código Python](https://www.python.org/dev/peps/pep-0008/#package-and-module-names).

    Para los nuevos al matraz Python, es un entorno de desarrollo de aplicaciones web que le ayuda a crear aplicaciones web en Python más rápido.

    ![Captura de pantalla de la ventana nuevo proyecto de Visual Studio con Python resaltada en la parte izquierda, Python matraz Web proyecto seleccionado en el centro y el tutorial de nombre en el cuadro Nombre](./media/documentdb-python-application/image9.png)

4. En la ventana de **Python Tools para Visual Studio** , haga clic en **instalar en un entorno virtual**. 

    ![Captura de pantalla del tutorial de base de datos - Python ventana Tools para Visual Studio](./media/documentdb-python-application/image10.png)

5. En la ventana **Agregar entorno Virtual** , puede aceptar los valores predeterminados y utilizar Python 2.7 como el entorno de base porque PyDocumentDB no admite actualmente Python 3.x y, a continuación, haga clic en **crear**. Esta acción configura el entorno virtual de Python necesario para el proyecto.

    ![Captura de pantalla del tutorial de base de datos - Python ventana Tools para Visual Studio](./media/documentdb-python-application/image10_A.png)

    Muestra la ventana de resultados `Successfully installed Flask-0.10.1 Jinja2-2.8 MarkupSafe-0.23 Werkzeug-0.11.5 itsdangerous-0.24 'requirements.txt' was installed successfully.` cuando el entorno está instalado correctamente.

## <a name="step-3-modify-the-python-flask-web-application"></a>Paso 3: Modificar la aplicación web de Python matraz

### <a name="add-the-python-flask-packages-to-your-project"></a>Agregar los paquetes de Python matraz a su proyecto

Después de configura su proyecto, debe agregar los paquetes de matraz necesarios a su proyecto, incluyendo pydocumentdb, el paquete de Python para DocumentDB.

1. En el Explorador de soluciones, abra el archivo denominado **requirements.txt** y reemplace el contenido con lo siguiente:

        flask==0.9
        flask-mail==0.7.6
        sqlalchemy==0.7.9
        flask-sqlalchemy==0.16
        sqlalchemy-migrate==0.7.2
        flask-whooshalchemy==0.55a
        flask-wtf==0.8.4
        pytz==2013b
        flask-babel==0.8
        flup
        pydocumentdb>=1.0.0

2. Guarde el archivo **requirements.txt** . 
3. En el Explorador de soluciones, haga clic en **Ent** y haga clic en **instalar desde requirements.txt**.

    ![Pantalla que muestra ent (Python 2.7) seleccionado con la instalación de requirements.txt resaltado en la lista](./media/documentdb-python-application/image11.png)

    Después de la instalación correcta, la ventana de salida muestra lo siguiente:

        Successfully installed Babel-2.3.2 Tempita-0.5.2 WTForms-2.1 Whoosh-2.7.4 blinker-1.4 decorator-4.0.9 flask-0.9 flask-babel-0.8 flask-mail-0.7.6 flask-sqlalchemy-0.16 flask-whooshalchemy-0.55a0 flask-wtf-0.8.4 flup-1.0.2 pydocumentdb-1.6.1 pytz-2013b0 speaklater-1.3 sqlalchemy-0.7.9 sqlalchemy-migrate-0.7.2

    > [AZURE.NOTE] En algunas ocasiones, es posible que vea un error en la ventana de resultados. Si esto sucede, compruebe si el error está relacionado con el Liberador de espacio. A veces se produce un error en la limpieza, pero la instalación seguirá correcta (desplazarse hacia arriba en la ventana de resultados para comprobarlo). Puede comprobar la instalación, [Compruebe el entorno virtual](#verify-the-virtual-environment). Si el error de instalación, pero la verificación es correcta, es Aceptar para continuar.

### <a name="verify-the-virtual-environment"></a>Comprobar el entorno virtual

¡Asegúrese de que todo está instalado correctamente.

1. Cree la solución presionando **Ctrl**+**MAYÚS**+**B**.
2. Una vez que la compilación se realiza correctamente, inicie el sitio Web, presione **F5**. Se iniciará el servidor de desarrollo de matraz y se inicia el explorador web. Debe ver la página siguiente.

    ![Proyecto de desarrollo de web Python matraz vacío mostrado en un explorador](./media/documentdb-python-application/image12.png)

3. Detener el sitio Web de depuración presionando la **tecla MAYÚS**+**F5** en Visual Studio.

### <a name="create-database-collection-and-document-definitions"></a>Crear base de datos, colección y definiciones de documento

Ahora vamos a crear la aplicación de voto al agregar archivos nuevos y actualizar otros usuarios.

1. En el Explorador de soluciones, haga clic en el proyecto **tutorial** , haga clic en **Agregar**y, a continuación, haga clic en **Nuevo elemento**. Seleccione **Archivo Python vacío** y nombre del archivo **forms.py**.  
2. Agregue el código siguiente al archivo forms.py y, a continuación, guarde el archivo.

```python
from flask.ext.wtf import Form
from wtforms import RadioField

class VoteForm(Form):
    deploy_preference  = RadioField('Deployment Preference', choices=[
        ('Web Site', 'Web Site'),
        ('Cloud Service', 'Cloud Service'),
        ('Virtual Machine', 'Virtual Machine')], default='Web Site')
```


### <a name="add-the-required-imports-to-viewspy"></a>Agregar las importaciones necesarias para views.py

1. En el Explorador de soluciones, expanda la carpeta **tutorial** y abra el archivo **views.py** . 
2. Agregar el siguiente importa instrucciones a la parte superior del archivo **views.py** , a continuación, guarde el archivo. Estos importación PythonSDK de DocumentDB y los paquetes de matraz.

    ```python
    from forms import VoteForm
    import config
    import pydocumentdb.document_client as document_client
    ```


### <a name="create-database-collection-and-document"></a>Crear conjunto de datos y documento

- Aún en **views.py**, agregue el código siguiente al final del archivo. Esto se encarga de la creación de la base de datos utilizada por el formulario. No elimine el código existente en **views.py**. Simplemente anexar al final.

```python
@app.route('/create')
def create():
    """Renders the contact page."""
    client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})

    # Attempt to delete the database.  This allows this to be used to recreate as well as create
    try:
        db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))
        client.DeleteDatabase(db['_self'])
    except:
        pass

    # Create database
    db = client.CreateDatabase({ 'id': config.DOCUMENTDB_DATABASE })

    # Create collection
    collection = client.CreateCollection(db['_self'],{ 'id': config.DOCUMENTDB_COLLECTION })

    # Create document
    document = client.CreateDocument(collection['_self'],
        { 'id': config.DOCUMENTDB_DOCUMENT,
          'Web Site': 0,
          'Cloud Service': 0,
          'Virtual Machine': 0,
          'name': config.DOCUMENTDB_DOCUMENT 
        })

    return render_template(
       'create.html',
        title='Create Page',
        year=datetime.now().year,
        message='You just created a new database, collection, and document.  Your old votes have been deleted')
```

> [AZURE.TIP] El método de **CreateCollection** toma un opcional **RequestOptions** como tercer parámetro. Esto puede usarse para especificar el tipo de la oferta para la colección. Si no se proporciona ningún valor offerType, a continuación, la colección se creará utilizando el tipo de oferta predeterminado. Para obtener más información sobre tipos de ofrecer DocumentDB, vea [niveles de rendimiento en DocumentDB](documentdb-performance-levels.md).


### <a name="read-database-collection-document-and-submit-form"></a>Leer la base de datos, colección, documento y envíe el formulario

- Aún en **views.py**, agregue el código siguiente al final del archivo. Esto se encarga de configurar el formulario, el documento, base de datos y recopilación de lectura. No elimine el código existente en **views.py**. Simplemente anexar al final.

```python
@app.route('/vote', methods=['GET', 'POST'])
def vote(): 
    form = VoteForm()
    replaced_document ={}
    if form.validate_on_submit(): # is user submitted vote  
        client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})

        # Read databases and take first since id should not be duplicated.
        db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))

        # Read collections and take first since id should not be duplicated.
        coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config.DOCUMENTDB_COLLECTION))

        # Read documents and take first since id should not be duplicated.
        doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config.DOCUMENTDB_DOCUMENT))

        # Take the data from the deploy_preference and increment our database
        doc[form.deploy_preference.data] = doc[form.deploy_preference.data] + 1
        replaced_document = client.ReplaceDocument(doc['_self'], doc)

        # Create a model to pass to results.html
        class VoteObject:
            choices = dict()
            total_votes = 0

        vote_object = VoteObject()
        vote_object.choices = {
            "Web Site" : doc['Web Site'],
            "Cloud Service" : doc['Cloud Service'],
            "Virtual Machine" : doc['Virtual Machine']
        }
        vote_object.total_votes = sum(vote_object.choices.values())

        return render_template(
            'results.html', 
            year=datetime.now().year, 
            vote_object = vote_object)

    else :
        return render_template(
            'vote.html', 
            title = 'Vote',
            year=datetime.now().year,
            form = form)
```


### <a name="create-the-html-files"></a>Crear los archivos HTML

1. En el Explorador de soluciones, en la carpeta **tutorial** , haga clic con el botón secundario del mouse en la carpeta **plantillas** , haga clic en **Agregar**y, a continuación, haga clic en **Nuevo elemento**. 
2. Seleccione **Página HTML**y, a continuación, en el cuadro Nombre, escriba **create.html**. 
3. Repita los pasos 1 y 2 para crear dos archivos HTML adicionales: results.html y vote.html.
4. Agregue el código siguiente a **create.html** en la `<body>` elemento. Muestra un mensaje que indica que se creó una nueva base de datos, la colección y el documento.

    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```

5. Agregue el código siguiente a **results.html** en la `<body`> elemento. Muestra los resultados de la encuesta.

    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Results of the vote</h2>
        <br />
        
    {% for choice in vote_object.choices %}
    <div class="row">
        <div class="col-sm-5">{{choice}}</div>
            <div class="col-sm-5">
                <div class="progress">
                    <div class="progress-bar" role="progressbar" aria-valuenow="{{vote_object.choices[choice]}}" aria-valuemin="0" aria-valuemax="{{vote_object.total_votes}}" style="width: {{(vote_object.choices[choice]/vote_object.total_votes)*100}}%;">
                                {{vote_object.choices[choice]}}
                </div>
            </div>
            </div>
    </div>
    {% endfor %}
    
    <br />
    <a class="btn btn-primary" href="{{ url_for('vote') }}">Vote again?</a>
    {% endblock %}
    ```

6. Agregue el código siguiente a **vote.html** en la `<body`> elemento. Muestra el sondeo y acepta los votos. Acerca de cómo registrar los votos, el control se pasa views.py donde se reconocerá la conversión de voto y adjuntar el documento según corresponda.

    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>What is your favorite way to host an application on Azure?</h2>
    <form action="" method="post" name="vote">
        {{form.hidden_tag()}}
            {{form.deploy_preference}}
            <button class="btn btn-primary" type="submit">Vote</button>
    </form>
    {% endblock %}
    ```

7. En la carpeta **plantillas** , reemplace el contenido de **index.html** con los siguientes elementos. Sirve como la página de inicio de la aplicación.
    
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Python + DocumentDB Voting Application.</h2>
    <h3>This is a sample DocumentDB voting application using PyDocumentDB</h3>
    <p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```

### <a name="add-a-configuration-file-and-change-the-initpy"></a>Agregar un archivo de configuración y cambiar la \_ \_inicialización\_\_.py

1. En el Explorador de soluciones, haga clic en el proyecto **tutorial** , haga clic en **Agregar**, haga clic en **Nuevo elemento**, seleccione **Archivo Python vacío**y, a continuación, el nombre del archivo **config.py**. Formularios en matraz requiere este archivo de configuración. Puede usarlo para proporcionar una clave secreta también. Esta clave no es necesario para este tutorial aunque.

2. Agregar el siguiente código a config.py, tendrá que modificar los valores de **DOCUMENTDB\_HOST** y **DOCUMENTDB\_clave** en el siguiente paso.

    ```python
    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    
    DOCUMENTDB_HOST = 'https://YOUR_DOCUMENTDB_NAME.documents.azure.com:443/'
    DOCUMENTDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
    
    DOCUMENTDB_DATABASE = 'voting database'
    DOCUMENTDB_COLLECTION = 'voting collection'
    DOCUMENTDB_DOCUMENT = 'voting document'
    ```

3. En el [portal de Azure](https://portal.azure.com/), desplácese hasta el módulo de **claves** haciendo clic en **Examinar**, **DocumentDB cuentas**, haga doble clic en el nombre de la cuenta que desea usar y, a continuación, haga clic en el botón de **teclas** en el área de **Essentials** . En el módulo de **teclas** , copie el valor **URI** y péguelo en el archivo **config.py** , como el valor de la **DOCUMENTDB\_HOST** propiedad. 
4. En el portal de Azure, en el módulo de **teclas** , copie el valor de la **Clave principal** o la **Clave secundaria**atrás y pegarlo en el archivo **config.py** , como el valor de la **DOCUMENTDB\_clave** propiedad.
5. En la ** \_ \_inicialización\_\_.py** , agregue la línea siguiente. 

        app.config.from_object('config')

    Para que el contenido del archivo es:

    ```python
    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views
    ```

6. Después de agregar todos los archivos, el Explorador de soluciones debe ser similar a esta:

    ![Captura de pantalla de la ventana del explorador de soluciones de Visual Studio](./media/documentdb-python-application/image15.png)


## <a name="step-4-run-your-web-application-locally"></a>Paso 4: Ejecute la aplicación web localmente

1. Cree la solución presionando **Ctrl**+**MAYÚS**+**B**.
2. Una vez que la compilación se realiza correctamente, inicie el sitio Web, presione **F5**. Verá lo siguiente en la pantalla.

    ![Captura de pantalla de la Python + DocumentDB voto aplicación que se muestra en un explorador web](./media/documentdb-python-application/image16.png)

3. Haga clic en **Crear o borrar la base de datos de voto** para generar la base de datos.

    ![Captura de pantalla de la página crear de la aplicación web: detalles sobre el desarrollo](./media/documentdb-python-application/image17.png)

4. A continuación, haga clic en **votar** y seleccione la opción.

    ![Captura de pantalla de la aplicación web con la que plantea una pregunta de voto](./media/documentdb-python-application/image18.png)

5. Para cada voto que se convierte, se incrementa el contador adecuado.

    ![Captura de pantalla de los resultados de la página de voto mostrada](./media/documentdb-python-application/image19.png)

6. Detener la depuración del proyecto, presione MAYÚS + F5.

## <a name="step-5-deploy-the-web-application-to-azure-websites"></a>Paso 5: Implementar la aplicación web a sitios Web de Azure

Ahora que tiene la aplicación completa funcionan correctamente contra DocumentDB, vamos a implementar esto a sitios Web de Azure.

1. Haga clic en el proyecto en el Explorador de soluciones (asegúrese de que no está todavía ejecuta localmente) y seleccione **Publicar**.  

    ![Captura de pantalla del tutorial seleccionado en el Explorador de soluciones, con la opción de publicación resaltada](./media/documentdb-python-application/image20.png)

2. En la ventana de la **Publicación Web** , seleccione **Aplicaciones Web de Microsoft Azure**y, a continuación, haga clic en **siguiente**.

    ![Captura de pantalla de la ventana de publicación Web con Microsoft Azure Web Apps resaltado](./media/documentdb-python-application/image21.png)

3. En la ventana de la **Ventana de aplicaciones Web de Microsoft Azure** , haga clic en **nuevo**.

    ![Captura de pantalla de la ventana de la ventana de aplicaciones Web de Microsoft Azure](./media/documentdb-python-application/select-existing-website.png)

4. En la ventana **Crear sitio en Microsoft Azure** , escriba un **nombre de la aplicación Web**, **plan de servicios de aplicación**, **grupo de recursos**y de **región**y luego haga clic en **crear**.

    ![Captura de pantalla del sitio crear en la ventana de Microsoft Azure](./media/documentdb-python-application/create-site-on-microsoft-azure.png)

5. En la ventana de la **Publicación Web** , haga clic en **Publicar**.

    ![Captura de pantalla del sitio crear en la ventana de Microsoft Azure](./media/documentdb-python-application/publish-web.png)

3. En unos segundos, Visual Studio se terminar de publicar su aplicación web e inicie un explorador donde puede ver su trabajo práctico que se ejecuta en Azure!

## <a name="troubleshooting"></a>Solución de problemas

Si esta es la primera aplicación de Python que ejecutar en el equipo, asegúrese de que las siguientes carpetas (o las ubicaciones de instalación equivalente) se incluyen en la variable de ruta de acceso:

    C:\Python27\site-packages;C:\Python27\;C:\Python27\Scripts;

Si recibe un error en la página de voto y nombre del proyecto distinto a **tutorial**, asegúrese de que ** \_ \_inicialización\_\_.py** hace referencia al nombre de proyecto correcto en la línea: `import tutorial.view`.

## <a name="next-steps"></a>Pasos siguientes

¡Felicidades! Acaba de completar su primera aplicación web de Python con DocumentDB de Azure y publicado a sitios Web de Azure.

Actualizamos y mejorar este tema con frecuencia en función de sus comentarios.  Una vez completado el tutorial, por favor, con los botones de voto en la parte superior e inferior de esta página y asegúrese de incluir sus comentarios en qué realizan mejoras en la que desea ver. Si le gustaría ponernos en contacto con usted directamente, no dude en incluir su dirección de correo electrónico en sus comentarios.

Para agregar funcionalidad adicional a la aplicación web, revise la API disponibles en el [SDK de Python DocumentDB](documentdb-sdk-python.md).

Para obtener más información acerca de Azure, Visual Studio y Python, consulte el [Centro para desarrolladores de Python](https://azure.microsoft.com/develop/python/). 

Para los tutoriales de Python matraz adicionales, vea [el matraz Mega-Tutorial, parte I: Hola, mundo!](http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world). 

  [Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
  [2]: https://www.python.org/downloads/windows/
  [3]: https://www.microsoft.com/download/details.aspx?id=44266
  [Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  [Azure portal]: http://portal.azure.com
