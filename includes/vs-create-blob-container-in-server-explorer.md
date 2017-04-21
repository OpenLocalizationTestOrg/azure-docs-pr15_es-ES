Puede crear colas de almacenamiento de Azure mediante el **Explorador de servidores**de Visual Studio.

![Servidor Explorer BLOB][Image1]

1. En el menú **Ver** , elija **Explorador de servidores**.
2. En el Explorador de servidores, expanda el nodo de **Azure** para la suscripción, expanda el nodo de **almacenamiento** y el nodo de la cuenta de almacenamiento especificado en el servicio de almacenamiento de Azure conectado.
3. Seleccione el nodo **colas** y elija **Crear cola** en el menú contextual.
4. Escriba un nombre para el contenedor y elija **Aceptar**.   

De forma predeterminada, el nuevo contenedor es privado y debe especificar la clave de acceso de almacenamiento para descargar BLOB de este contenedor. Si desea que los archivos en el contenedor público, seleccione el contenedor en el **Explorador de servidores** y presione `F4` para mostrar la ventana de **Propiedades** . Establecer el **público acceso de lectura** a **Blob**. Cualquier usuario de Internet puede ver BLOB en un contenedor público, pero se puede modificar o eliminar solo si tiene la clave de acceso adecuado.


[Image1]: ./media/vs-create-blob-container-in-server-explorer/vs-storage-create-blob-containers-in-Server-Explorer.png