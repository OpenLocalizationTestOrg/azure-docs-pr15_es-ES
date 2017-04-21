<properties
pageTitle="Instalar y configurar las herramientas de creación en GitHub"
description="Herramientas y los pasos para obtener configurado para la creación de contenido de Azure en GitHub."
services="contributor-guide"
documentationCenter=""
authors="tysonn"  
manager="carolz" />

<tags
ms.service="contributor-guide"
 ms.devlang=""
 ms.topic="article"
  ms.tgt_pltfrm=""
  ms.workload=""
  ms.date="01/19/2015"
  ms.author="tysonn" />

#<a name="install-and-set-up-tools-for-authoring-in-github"></a>Instalar y configurar las herramientas de creación en GitHub

Siga los pasos de este artículo para configurar las herramientas para contribuir a la documentación técnica Azure. Probablemente informales y ocasionales colaboradores pueden usar la interfaz de usuario se describe en el paso 2 de GitHub.

Si no está familiarizado con Git, desea revisar terminología Git: [https://help.github.com/articles/github-glossary](https://help.github.com/articles/github-glossary). Además, este subproceso StackOverflow contiene un glosario de términos Git que encontrará en este conjunto de pasos: [http://stackoverflow.com/questions/7076164/terminology-used-by-git](http://stackoverflow.com/questions/7076164/terminology-used-by-git)

## <a name="contents"></a>Contenido

- [Crear una cuenta de GitHub y configurar su perfil](#create-a-github-account-and-set-up-your-profile)
- [Iniciar sesión en Disqus](#sign-up-for-disqus)
- [Determinar si necesita realmente seguir el resto de estos pasos](#determine-whether-you-really-need-to-follow-the-rest-of-these-steps)
- [Permisos en GitHub](#permissions-in-github)
- [Instalar Git para Windows](#install-git-for-windows)
- [Habilitar la autenticación de dos factores](#enable-two-factor-authentication)
- [Instalar a un editor de descuento](#install-a-markdown-editor)
- [Configurar Atom](#configure-atom)
- [Se bifurcan del repositorio y copiarlo en el equipo](#fork-the-repository-and-copy-it-to-your-computer)
- [Configure el nombre de usuario y el correo electrónico localmente](#configure-your-user-name-and-email-locally)
- [Pasos siguientes](#next-steps)

## <a name="create-a-github-account-and-set-up-your-profile"></a>Crear una cuenta de GitHub y configurar su perfil

Para contribuir al contenido técnico Azure, tendrá una cuenta de [GitHub](http://www.github.com) .

Si es un colaborador de Microsoft, debe configurar su cuenta de GitHub para que claramente se hayan identificado como empleado de Microsoft. Configurar su perfil como sigue:

- **Imagen de perfil**: una imagen de la que (obligatorio)
- **Nombre**: su nombre y apellidos (obligatorio)
- **Correo electrónico**: su dirección de correo electrónico de Microsoft (opcional)
- **Empresa**: Microsoft Corporation (obligatorio)
- **Ubicación**: mostrar su ubicación (opcional)

Su perfil debería ser similar a este perfil:

<p align="center">
 ![Ejemplo de perfil de GitHub](./media/tools-and-setup/githubprofile.png)

## <a name="sign-up-for-disqus"></a>Iniciar sesión en Disqus

Cada artículo técnico Azure publicado tiene una secuencia de comentario proporcionada por el servicio de Disqus.

 ![Logotipo de disco](./media/tools-and-setup/discus.png)

Si es un empleado de Microsoft y, si es el autor del o colaborador a un artículo, debe iniciar sesión en Disqus por lo que puede participar en el flujo de comentario para el artículo.

1. Suscribirse a una cuenta en [http://www.disqus.com/](http://www.disqus.com/)
2. Rellene el perfil como sigue:

 - **Nombre completo**: su nombre completo como se muestra en el listado de libreta de direcciones Microsoft, además de la información entre corchetes, que es el alias plus @MSFT. Formato: *primero último [alias@MSFT] *
 - **Ubicación**: su ubicación
 - **Breve biografía**: el título

## <a name="determine-whether-you-really-need-to-follow-the-rest-of-these-steps"></a>Determinar si necesita realmente seguir el resto de estos pasos

No debe seguir todos los pasos de este artículo. Depende del tipo de contenido contribución que desee o necesita realizar.

###<a name="submit-a-text-only-change-to-an-existing-article"></a>Envíe un cambio de solo texto a un artículo existente

Si solo necesita o desea realizar actualizaciones de texto a un artículo existente, probablemente no es necesario que siga el resto de los pasos. Puede utilizar el editor de descuento basada en web de GitHub enviar los cambios. Haga clic en el vínculo de GitHub en el artículo que desea modificar:

 ![Ejemplo de perfil de GitHub](./media/tools-and-setup/contributetogit.png)

 A continuación, haga clic en el icono de edición en la versión GitHub del artículo

 ![Ejemplo de perfil de GitHub](./media/tools-and-setup/editicon.PNG)

 Que abre el editor de web fácil de utilizar que hace que sea fácil enviar cambios. No es necesario que siga el resto de los pasos de este artículo.

###<a name="all-other-changes"></a>Todos los demás cambios
La UI GitHub admiten la creación de nuevos archivos y arrastrar y soltar imágenes. Sin embargo, cuando trabaja en la interfaz de usuario, administrar bifurcaciones puede ser confusa normalmente recomendamos que instale las herramientas y obtenga información sobre los comandos para crear y administrar artículos. Si desea usar la interfaz de usuario, consulte:

- [Crear archivos en Github](https://github.com/blog/1327-creating-files-on-github)
- [Cargar archivos en los repositorios](https://github.com/blog/2105-upload-files-to-your-repositories)

Para el siguiente tipo de trabajo, se recomienda instalar y aprenda a usar las herramientas:

 - Realizar cambios importantes en un artículo
 - Crear y publicar un nuevo artículo
 - Agregar nuevas imágenes o actualización de imágenes
 - Actualizar un artículo durante un período de días sin publicación cambios de los días
 - Creación de contenido para una versión que tiene que ir un día determinado en un momento determinado

##<a name="permissions-in-github"></a>Permisos en GitHub

Cualquier persona que tenga una cuenta de GitHub puede contribuir a Azure contenido técnico a través de nuestro repositorio público en [https://github.com/Azure/azure-content](https://github.com/Azure/azure-content). No se requiere ningún permiso especial.

Si es el jefe de Microsoft o escritor quién está trabajando en Azure contenido, debe trabajar en nuestra privado repositorio de contenido, pr de contenido de azure. Visite [https://repos.opensource.microsoft.com](https://repos.opensource.microsoft.com ) para solicitar los permisos de lectura que le permiten contribuciones a través de la repo privado: inicie sesión en GitHub con el botón > haga clic en Azure > haga clic en **unirse a un grupo** o **unirse a otro equipo**y, a continuación, busque y unirse al grupo de **lectura de contenido de azure** .

## <a name="install-git-for-windows"></a>Instalar Git para Windows

Instale Git para Windows de [http://git-scm.com/download/win](http://git-scm.com/download/win). Esta descarga instala el sistema de control de versiones de Git e instala Git fiesta, la aplicación de línea de comandos que usará para interactuar con el repositorio de Git local.

Puede aceptar la configuración predeterminada; Si desea que los comandos estén disponibles en la línea de comandos de Windows, seleccione la opción que le permite.

<p align="center">
 ![Ejemplo de perfil de GitHub](./media/tools-and-setup/gitbashinstall.png)

(Nota: no es igual a "Github para Windows". "Github para Windows" es una herramienta de gráfica diferentes que también funcionarán si desea Infórmese sobre usted mismo. [https://windows.github.com/](https://windows.github.com/))

## <a name="enable-two-factor-authentication"></a>Habilitar la autenticación de dos factores

Debe habilitar la autenticación de dos factores (2FA) en la cuenta de GitHub si está trabajando en el repositorio de contenido privado. Se requiere en el repositorio privado.

Para habilitar esta opción, siga las instrucciones en ambos los temas de ayuda GitHub siguientes:

- [Información sobre autenticación en dos fases](https://help.github.com/articles/about-two-factor-authentication/)
- [Crear un token de acceso para el uso de línea de comandos](https://help.github.com/articles/creating-an-access-token-for-command-line-use/)

Cuando se crea el token, seleccione todos los ámbitos disponibles en la interfaz de usuario de creación de tokens ([detalles en cada ámbito](https://developer.github.com/v3/oauth/#scopes))

Después de habilitar 2FA, debe escribir el token de acceso en lugar de la contraseña de GitHub en el símbolo del sistema al intentar obtener acceso a un repositorio de GitHub desde la línea de comandos. El token de acceso no es el código de autenticación que se obtiene en un mensaje de texto al configurar 2FA. Es una cadena larga que tiene un aspecto similar a este: fdd3b7d3d4f0d2bb2cd3d58dba54bd6bafcd8dee. Algunas notas sobre esto:

- Cuando se crea el token de acceso, guardarlo en un archivo de texto para que sea accesible cuando lo necesite.

- Más adelante, cuando necesite pegar el token, saber para pegar en la línea de comandos de dos formas:

 - Haga clic en el icono en la esquina superior izquierda de la ventana de la línea de comandos > Editar > Pegar.
 - Haga clic en el icono en la esquina superior izquierda de la ventana y haga clic en Propiedades > Opciones > modo de edición rápida. Esto configura la línea de comandos para que pueda pegar con el botón secundario en la ventana de la línea de comandos.

## <a name="install-a-markdown-editor"></a>Instalar a un editor de descuento

Es el creador de contenido utilizando la notación de simple "descuento" en los archivos, en lugar de complejo "beneficio" (HTML, XML, etcetera). Por lo tanto, deberá instalar a un editor de descuento.

- **Atom**: la mayoría de nosotros utilizarlo de GitHub Atom descuento: [http://atom.io](http://atom.io). No se requiere una licencia para uso empresarial. Tiene el corrector ortográfico.

- **El Bloc de notas**: puede usar el Bloc de notas para una opción muy ligera.

- **Texto**: se trata de un editor de descuento ligero, elegante, en línea y Abrir origen que ofrece una vista previa. Visite [http://prose.io](http://prose.io) y autorizar el texto en el repositorio.

- **[Código de visual Studio](https://www.visualstudio.com/products/code-vs.aspx)** - entrada de Microsoft en este espacio.

## <a name="configure-atom"></a>Configurar Atom

Si usa Atom, deberá configurar algunas cosas.

- Atom utiliza de forma predeterminada 2 espacios para las tabulaciones, pero descuento espera 4 espacios. Si deja el valor predeterminado de los dos, el artículo tendrá un aspecto excelente en vista previa, pero no cuando se importa en Azure. Por lo tanto, configurar Atom para usar 4 espacios - puede encontrar esta opción en archivo > Configuración > configuración del Editor > longitud de pestaña.
- Probablemente también desee activar Ajustar suaves en esta sección, que hace lo mismo que "ajuste" en el Bloc de notas.
- Para activar la vista previa del descuento, haga clic en paquetes > vista previa de descuento > vista previa de alternancia. Puede usar Ctrl-Mayús-M para activar o desactivar la vista previa de vista HTML.

## <a name="fork-the-repository-and-copy-it-to-your-computer"></a>Se bifurcan del repositorio y copiarlo en el equipo

1. Crear una bifurcación del repositorio en GitHub, vaya a la parte superior derecha de la página y haga clic en el botón de horquilla. Si se le solicita, seleccione su cuenta como la ubicación donde se debe crear la bifurcación. Esto crea una copia del repositorio dentro de su cuenta de concentrador Git. Por lo general, redactores técnicos y administradores de programas necesitan horquilla azure-contenido-pr, la repo privado. Contenido de azure de bifurcación, el público repo necesitan colaboradores de la Comunidad. Solo debe se bifurcan una vez; Después de la instalación de la primera, si desea copiar la horquilla en otro equipo, solo tiene que ejecute los comandos siguientes de esta sección para copiar la repo en su equipo.  Si elige crear bifurcaciones de ambos repositorios, debe crear una bifurcación para cada repositorio.

2. Copiar el Personal acceso Token que obtuvo de [https://github.com/settings/tokens](https://github.com/settings/tokens). Puede aceptar los permisos predeterminados para el token.  Guarde el Token de acceso Personal en un archivo de texto para su uso posterior.

3. A continuación, copie el repositorio a su equipo con sus credenciales incrustadas en la cadena de comando.  Para ello, abra Git fiesta y ejecutar como administrador. En el símbolo del sistema, escriba el siguiente comando.  Este comando crea un directorio azure-content(-pr) en el equipo.  Si usa la ubicación predeterminada, estará en c:\users<your Windows user name>\azure-content(-pr).

Repo público:

        git clone https://[your GitHub user name]:[token]@github.com/<your GitHub user name>/azure-content.git

Repo privado:

        git clone https://[your GitHub user name]:[token]@github.com/<your GitHub user name>/azure-content-pr.git

Por ejemplo, este comando clonar podría tener un aspecto similar a este:

        git clone https://smithj:b428654321d613773d423ef2f173ddf4a312345@github.com/smithj/azure-content-pr.git  

## <a name="set-remote-repository-connection-and-configure-credentials"></a>Conexión de repositorio remoto y configurar las credenciales

Crear una referencia al repositorio raíz especificando estos comandos. Establece las conexiones en el repositorio de GitHub para que puedan obtener los cambios más recientes en el equipo local y los cambios de inserción vuelva a GitHub. Este comando también configura el token localmente para que no es necesario que escriba su nombre y contraseña cada vez que intenta obtener acceso a la repo ascendente y la horquilla en GitHub.

Repo público:

        cd azure-content
        git remote add upstream https://[your GitHub user name]:[token]@github.com/Azure/azure-content.git
        git fetch upstream

Repo privado:

        cd azure-content-pr
        git remote add upstream https://[your GitHub user name]:[token]@github.com/Azure/azure-content-pr.git
        git fetch upstream

Esto suele lleva un tiempo. Después de esto, que no tiene que se bifurcan nuevo o vuelva a escribir sus credenciales. Solo tendrá que copiar las bifurcaciones en un equipo local de nuevo si configura las herramientas en otro equipo.


## <a name="configure-your-user-name-and-email-locally"></a>Configure el nombre de usuario y el correo electrónico localmente

Para asegurarse de que se muestran correctamente como un colaborador, debe configurar su nombre de usuario y el correo electrónico localmente en Git.

1. Iniciar fiesta Git y cambiar al contenido de azure o pr de contenido de azure:

   ````
   cd azure-content
   ````

 o

   ````
   cd azure-content-pr
   ````

2. Configurar el nombre de usuario para que coincida con su nombre tal y como se configurado en el perfil de GitHub:

    ````
    git config --global user.name "John Doe"
    ````
3. Configurar el correo electrónico para que coincida con el correo electrónico principal designado en el perfil de GitHub; Si es un empleado MSFT, debe ser su dirección de correo electrónico MSFT:

    ````
    git config --global user.email "alias@example.com"
    ````
4. Tipo de `git config -l` y revise la configuración local para garantizar que el nombre de usuario y correo electrónico en la configuración es correcta.

##<a name="next-steps"></a>Pasos siguientes

- Comprender el tipo de contenido al que pertenece el repo contenido técnico y saber lo que no pertenece. ¡Consulte la [Guía de canal contenido](./content-channel-guidance.md)!
- Siga [estos pasos para crear o modificar un artículo y, a continuación, enviarlo para su publicación](./git-commands-for-master.md).
- Copie [la plantilla descuento](../markdown templates/markdown-template-for-new-articles.md) como base para un nuevo artículo.
- Use [esta lista de comprobación para comprobar su solicitud de extracción cumple los criterios de calidad](./contributor-guide-pr-criteria.md) para la combinación.


###<a name="contributors-guide-navigation"></a>Navegación de la Guía de colaboradores

- [Artículo de información general](./../README.md)
- [Índice de artículos de guía](./contributor-guide-index.md)



<!--Anchors-->
[Use a customer-friendly voice]: #use-a-customer-friendly-voice
[Consider localization and machine translation]: #consider-localization-and-machine-translation
[other style and voice issues to watch for]: #other-style-and-voice-issues-to-watch-for


[Create a GitHub account and set up your profile]: #create-a-github-account-and-set-up-your-profile
[Determine whether you really need to follow the rest of these steps]: #determine-whether-you-really-need-to-follow-the-rest-of-these-steps
[Permissions in GitHub]: #permissions-in-github
[Install Git for Windows]: #install-git-for-windows
[Enable two-factor authentication]: #enable-two-factor-authentication
[Install a markdown editor]: #install-a-markdown-editor
[Fork the repository and copy it to your computer]: #fork-the-repository-and-copy-it-to-your-computer
[Install git-credential-winstore]: #install-git-credential-winstore
[Sign up for Disqus]: #sign-up-for-disqus
[Configure your user name and email locally]: #configure-your-user-name-and-email-locally
[Next steps]: #next-steps
