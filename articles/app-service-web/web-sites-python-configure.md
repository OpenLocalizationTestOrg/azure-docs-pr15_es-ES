<properties 
    pageTitle="Configurar Python con aplicaciones de Azure de aplicación de servicio Web" 
    description="Este tutorial describe las opciones para crear y configurar un servidor Web básico aplicación de Python compatible con interfaz de puerta de enlace (WSGI) en Azure aplicación de servicio Web Apps." 
    services="app-service" 
    documentationCenter="python" 
    tags="python"
    authors="huguesv" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="02/26/2016" 
    ms.author="huvalo"/>




# <a name="configuring-python-with-azure-app-service-web-apps"></a>Configurar Python con aplicaciones de Azure de aplicación de servicio Web

Este tutorial describe las opciones para crear y configurar una aplicación básica de interfaz de puerta de enlace de servidor Web (WSGI) compatible con Python en [Azure aplicación de servicio Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

Describe las características adicionales de implementación de Git, como entorno virtual y la instalación del paquete mediante requirements.txt.


## <a name="bottle-django-or-flask"></a>¿Botella, Django o matraz?

Azure Marketplace contiene plantillas para los marcos botella, Django y matraz. Si va a desarrollar su primera aplicación web en la aplicación de servicio de Azure o no está familiarizado con Git, le recomendamos que siga uno de estos tutoriales, que incluyen instrucciones paso a paso para crear una aplicación de trabajo de la galería mediante la implementación de Git desde Windows o Mac:

- [Crear aplicaciones web con botella](web-sites-python-create-deploy-bottle-app.md)
- [Crear aplicaciones web con Django](web-sites-python-create-deploy-django-app.md)
- [Crear aplicaciones web con matraz](web-sites-python-create-deploy-flask-app.md)


## <a name="web-app-creation-on-azure-portal"></a>Creación de aplicaciones Web en el Portal de Azure

En este tutorial se presupone una suscripción existente de Azure y acceder al Portal de Azure.

Si no tiene una aplicación web existente, puede crear uno desde el [Portal de Azure](https://portal.azure.com).  Haga clic en el botón nuevo en la esquina superior izquierda, haga clic en **Web + Mobile** > **Web app**.

## <a name="git-publishing"></a>Publicación de GIT

Configurar la publicación de Git para la aplicación web recién creado siguiendo las instrucciones en la [Implementación Local de Git al servicio de la aplicación de Azure](app-service-deploy-local-git.md). Este tutorial usa Git para crear, administrar y publicar nuestra aplicación web de Python al servicio de la aplicación de Azure.

Una vez configurada la publicación Git, se crea un repositorio Git y asociado a la aplicación web. Dirección URL del repositorio se mostrará y de aquí en adelante puede usarse para insertar datos desde el entorno de desarrollo local en la nube. Para publicar aplicaciones mediante Git, asegúrese de que también se instala un cliente Git y use las instrucciones para insertar el contenido de la aplicación web al servicio de la aplicación de Azure.


## <a name="application-overview"></a>Información general de la aplicación

En las siguientes secciones, se crean los siguientes archivos. Se colocarán en la raíz del repositorio Git.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## <a name="wsgi-handler"></a>Controlador WSGI

WSGI es un estándar de Python descrito por [PEP 3333](http://www.python.org/dev/peps/pep-3333/) define una interfaz entre el servidor web y Python. Proporciona una interfaz estándar para escribir varias aplicaciones web y marcos con Python. Marcos de web Python populares hoy usan WSGI. Azure aplicación de servicio Web Apps le da que asistencia técnica para cualquier dichos marcos; Además, los usuarios avanzados pueden incluso crear sus propios como el controlador personalizado sigue las directrices de la especificación de WSGI.

Aquí tenemos un ejemplo de un `app.py` que define un controlador personalizado:

    def wsgi_app(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        response_body = 'Hello World'
        yield response_body.encode()

    if __name__ == '__main__':
        from wsgiref.simple_server import make_server

        httpd = make_server('localhost', 5555, wsgi_app)
        httpd.serve_forever()

Puede ejecutar esta aplicación localmente con `python app.py`, a continuación, vaya a `http://localhost:5555` en el explorador web.


## <a name="virtual-environment"></a>Entorno virtual

Aunque la aplicación de ejemplo anterior no requiere los paquetes externos, es probable que la aplicación requerirá algunos.

Para ayudar a administrar dependencias de paquete externo, implementación de Azure Git es compatible con la creación de entornos virtuales.

Al Azure detecta un requirements.txt en la raíz del repositorio, se crea automáticamente un entorno virtual denominado `env`. Sólo se produce en la primera implementación o durante cualquier implementación después de la Python seleccionado runtime ha cambiado.

Probable que desee crear un entorno virtual localmente para el desarrollo, pero no incluirlo en el repositorio de Git.


## <a name="package-management"></a>Administración de paquetes

Paquetes incluidos en requirements.txt se instalará automáticamente en el entorno virtual con puntos. Esto ocurre en cada implementación, pero puntos omitirá instalación si ya está instalado un paquete.

Ejemplo `requirements.txt`:

    azure==0.8.4


## <a name="python-version"></a>Versión de Python

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

Ejemplo `runtime.txt`:

    python-2.7


## <a name="webconfig"></a>Web.config

Debe crear un archivo web.config para especificar cómo debe tratar el servidor solicitudes.

Tenga en cuenta que si tiene un archivo web.x.y.config en el repositorio, donde x.y coincide con el tiempo de ejecución de Python seleccionado y luego Azure copiará automáticamente el archivo como web.config adecuado.

Los siguientes ejemplos de web.config se basan en una secuencia de comandos de proxy de entorno virtual, que se describe en la siguiente sección.  Trabajar con el controlador WSGI que se usan en el ejemplo `app.py` encima.

Ejemplo `web.config` para Python 2.7:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\activate_this.py" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_virtualenv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python27\python.exe|D:\Python27\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite"
                      url="handler.fcgi/{R:1}"
                      appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Ejemplo `web.config` para Python 3.4:

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\python.exe" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_venv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python27_via_FastCGI" />
          <remove name="Python34_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python34\python.exe|D:\Python34\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{REQUEST_URI}" pattern="^/static/.*" ignoreCase="true" negate="true" />
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Archivos estáticos se tratará por el servidor web directamente, sin pasar por el código de Python, para mejorar el rendimiento.

En los ejemplos anteriores, la ubicación de los archivos estáticos en disco debe coincidir con la ubicación en la dirección URL. Esto significa que una solicitud de `http://pythonapp.azurewebsites.net/static/site.css` servirá el archivo en el disco en `\static\site.css`.

`WSGI_ALT_VIRTUALENV_HANDLER`es donde se especifica el controlador WSGI. En los ejemplos anteriores, tiene `app.wsgi_app` porque el controlador es una función denominada `wsgi_app` en `app.py` en la carpeta raíz.

`PYTHONPATH`se puede personalizar, pero si instala todas las dependencias en el entorno virtual especificando en requirements.txt, no es necesario cambiarlo.


## <a name="virtual-environment-proxy"></a>Entorno virtual Proxy

La siguiente secuencia de comandos se usa para recuperar el controlador WSGI, activar los errores de registro y el entorno virtuales. Está diseñado para ser genéricos y se utilizan sin modificaciones.

Contenido de `ptvs_virtualenv_proxy.py`:

     # ############################################################################
     #
     # Copyright (c) Microsoft Corporation. 
     #
     # This source code is subject to terms and conditions of the Apache License, Version 2.0. A 
     # copy of the license can be found in the License.html file at the root of this distribution. If 
     # you cannot locate the Apache License, Version 2.0, please send an email to 
     # vspython@microsoft.com. By using this source code in any fashion, you are agreeing to be bound 
     # by the terms of the Apache License, Version 2.0.
     #
     # You must not remove this notice, or any other, from this software.
     #
     # ###########################################################################

    import datetime
    import os
    import sys
    import traceback

    if sys.version_info[0] == 3:
        def to_str(value):
            return value.decode(sys.getfilesystemencoding())

        def execfile(path, global_dict):
            """Execute a file"""
            with open(path, 'r') as f:
                code = f.read()
            code = code.replace('\r\n', '\n') + '\n'
            exec(code, global_dict)
    else:
        def to_str(value):
            return value.encode(sys.getfilesystemencoding())

    def log(txt):
        """Logs fatal errors to a log file if WSGI_LOG env var is defined"""
        log_file = os.environ.get('WSGI_LOG')
        if log_file:
            f = open(log_file, 'a+')
            try:
                f.write('%s: %s' % (datetime.datetime.now(), txt))
            finally:
                f.close()

    ptvsd_secret = os.getenv('WSGI_PTVSD_SECRET')
    if ptvsd_secret:
        log('Enabling ptvsd ...\n')
        try:
            import ptvsd
            try:
                ptvsd.enable_attach(ptvsd_secret)
                log('ptvsd enabled.\n')
            except: 
                log('ptvsd.enable_attach failed\n')
        except ImportError:
            log('error importing ptvsd.\n');

    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_ALT_VIRTUALENV_HANDLER env var must be set')
    
        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)
    
        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None
        last_tb = ''

        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
                last_tb = ''
                for name, should_call in name_list:
                    handler = getattr(handler, name)
                    if should_call:
                        handler = handler()
                break
            except ImportError:
                module_name, _, callable_name = module_name.rpartition('.')
                should_call = callable_name.endswith('()')
                callable_name = callable_name[:-2] if should_call else callable_name
                name_list.insert(0, (callable_name, should_call))
                handler = None
                last_tb = ': ' + traceback.format_exc()
    
        if handler is None:
            raise ValueError('"%s" could not be imported%s' % (handler_name, last_tb))
    
        return handler

    activate_this = os.getenv('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS')
    if not activate_this:
        raise Exception('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS is not set')

    def get_virtualenv_handler():
        log('Activating virtualenv with %s\n' % activate_this)
        execfile(activate_this, dict(__file__=activate_this))

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler

    def get_venv_handler():
        log('Activating venv with executable at %s\n' % activate_this)
        import site
        sys.executable = activate_this
        old_sys_path, sys.path = sys.path, []
    
        site.main()
    
        sys.path.insert(0, '')
        for item in old_sys_path:
            if item not in sys.path:
                sys.path.append(item)

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler


## <a name="customize-git-deployment"></a>Personalizar Git implementación

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-deployment.md)]


## <a name="troubleshooting---package-installation"></a>Solución de problemas - paquete de instalación

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## <a name="troubleshooting---virtual-environment"></a>Solución de problemas - entorno Virtual

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de Python](/develop/python/).

>[AZURE.NOTE] Si desea empezar a trabajar con el servicio de aplicación de Azure antes de suscribirse para una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de starter corta duración en la aplicación de servicio. Sin tarjetas de crédito obligatoria; sin compromisos.

## <a name="whats-changed"></a>¿Qué ha cambiado
* Encontrará una guía para el cambio de sitios Web a la aplicación de servicio: [servicio de aplicación de Azure y su influencia sobre los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)





 
