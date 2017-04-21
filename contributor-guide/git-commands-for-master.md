<properties pageTitle="GIT comandos para crear un nuevo artículo o actualizar un artículo existente" description="Pasos para trabajar con la técnica de Azure GitHub repositorios de contenido." metaKeywords="" services="" solutions="" documentationCenter="" authors="tysonn" videoId="" scriptId="" manager="carolz" />

<tags ms.service="contributor-guide" ms.devlang="" ms.topic="article" ms.tgt_pltfrm="" ms.workload="" ms.date="01/16/2015" ms.author="tysonn" />

# <a name="git-commands-for-creating-a-new-article-or-updating-an-existing-article"></a>GIT comandos para crear un nuevo artículo o actualizar un artículo existente


## <a name="standard-process-working-from-master"></a>Proceso estándar (trabajo de patrón)
Siga los pasos de este artículo para crear una bifurcación de trabajo local en el equipo para que pueda crear un nuevo artículo de la sección de documentación técnica de azure.microsoft.com o actualizar un artículo existente.

1. Iniciar fiesta Git (o la herramienta de línea de comandos que usa para Git).

 **Nota:** Si está trabajando en el repositorio de público, cambiar pr de contenido de azure al contenido de azure de todos los comandos.

2. Cambiar a pr de contenido de azure:

        cd azure-content-pr
3. Consulte la rama principal:

        git checkout master

4. Crear una nueva bifurcación de trabajo local derivada de la rama principal:

        git pull upstream master:<working branch>


5. Mover a la nueva bifurcación de trabajo:

        git checkout <working branch>

6. Comunique la horquilla que creó la sucursal de trabajo local:

        git push origin <working branch>

7. Crear su nuevo artículo o hacer cambios en un artículo existente. Use el Explorador de Windows para abrir y crear nuevos archivos de descuento y usar Atom (http://atom.io) como editor de descuento. Después de que ha creado o modificado el artículo e imágenes, vaya al paso siguiente.

8. Agregar y confirme los cambios que haya hecho:

        git add .
        git commit –m "<comment>"
        
   O bien agregar específico del tipo de archivo que modificado:

        git add <file path>
        git commit –m "<comment>"

   Si elimina archivos, tiene que use esta opción:
   
        git add --all
        git commit -m "<comment>"

9. Actualizar su rama trabajo local con los cambios de nivel superior:

        git pull upstream master

10. Aplicar los cambios a la horquilla en GitHub:

        git push origin <working branch>

12. Cuando esté listo para enviar el contenido a la rama principal ascendente de ensayo validación, o de publicación, en la UI GitHub, crear una solicitud de extracción desde la horquilla a la rama principal.

13. Si es un empleado trabajando en el repositorio privado, los cambios que envíe se almacenan automáticamente y se escribe un vínculo provisional a la solicitud de extracción. Revise el contenido preconfigurado y firmar en los comentarios de la solicitud de extracción agregando el comentario **signo desactivar #** .  Esto indica que los cambios están listos para enviarse directo.  Si no desea que la solicitud de extracción para que se acepte - si solo se prueba los cambios - agregar la nota **hold desactivar #** a la solicitud de extracción.

14. La acceptor de solicitud de extracción revisa la solicitud de extracción, proporciona comentarios o acepta la solicitud de extracción. 

15. Opcionalmente, compruebe su artículo publicado o cambios en

 http://Azure.Microsoft.com/documentation/articles/*name-of-your-article-without-the-MD-extension*

## <a name="publishing"></a>Publicación

- Artículos están publicados en aproximadamente 10:00 a 3:00 P.M. hora del Pacífico, de lunes a viernes. Pueden tardar hasta 30 minutos para los artículos que aparecen en pantalla después de la publicación. Recuerde que la solicitud de extracción tiene combinarse por un revisor de solicitud de extracción antes de que los cambios pueden incluir en la siguiente publicación programada ejecutar. Necesita trabajar con el revisor de solicitud de extracción antelación para garantizar que una solicitud de extracción se combina para una publicación específica ejecutar. En caso contrario, se revisa PR en el orden en que se han enviado.

- Si es un empleado que trabaja en el repositorio privado, todas las solicitudes de extracción están sujetos a las reglas de validación que deben corregirse antes de la solicitud de extracción puede combinarse. 

## <a name="working-with-release-branches"></a>Trabajar con ramas de lanzamiento

Cuando está trabajando con una versión de bifurcación, la mejor manera de crear una bifurcación de trabajo local de la rama de lanzamiento es usar la sintaxis de este comando:

    git checkout upstream/<upstream branch name> -b <local working branch name>

Esto crea la sucursal local directamente desde la rama precede, evitando cualquier combinación local.

