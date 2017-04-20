1. En el **Explorador de soluciones**de Visual Studio, haga clic en el proyecto y seleccione **Agregar > soporte técnico de Docker** en el menú contextual.

    ![Agregar menú contextual de soporte técnico de Docker](media/vs-azure-tools-docker-add-docker-support/docker-support-context-menu.png)

1. Agregar soporte Docker a un núcleo de ASP.NET proyecto web resultados de la adición de varios archivos relacionados con el Docker ser agregado al proyecto, incluidos archivos redacte Docker, secuencias de comandos de Windows PowerShell de implementación y archivos de propiedades de Docker. 

    ![Archivos de docker agregado al proyecto](media/vs-azure-tools-docker-add-docker-support/docker-files-added.png)
    
> [AZURE.NOTE]Si usa la [Versión Beta Docker para Windows](https://beta.docker.com), abra Properties\Docker.props, elimine el valor predeterminado y reinicie Visual Studio para el valor surta efecto.
> 
> ```
> <DockerMachineName Condition="'$(DockerMachineName)'=='' "></DockerMachineName>
> ```
