# <a name="using-cdn-for-azure"></a>Usar CDN de Azure

La red de entrega de contenido (CDN) de Azure ofrece a los desarrolladores una solución global para la entrega de contenido de banda ancha por caché BLOB y contenido estático de instancias de cálculo en los nodos físicos en los Estados Unidos, Europa, Asia, Australia y América del sur. Para obtener una lista de ubicaciones de nodo CDN, vea [Ubicaciones de nodo de Azure CDN].

Esta tarea incluye los siguientes pasos:

* [Paso 1: Crear una cuenta de almacenamiento](#Step1)
* [Paso 2: Crear un nuevo punto final CDN para su cuenta de almacenamiento](#Step2)
* [Paso 3: Obtener acceso al contenido CDN](#Step3)
* [Paso 4: Quitar el contenido CDN](#Step4)

Las ventajas de usar CDN en caché datos Azure incluyen:

-   Mejorar el rendimiento y usuario experiencia de los usuarios finales que están lejos de un origen de contenido y está usando aplicaciones donde muchos 'viajes internet' deben cargar contenido
-   Gran escala distribuida para tratar mejor instantánea carga es alta, por ejemplo, al comienzo de un evento como un lanzamiento del producto

Los clientes existentes de CDN ahora pueden usar la CDN de Azure en el [portal de clásico de Azure]. La CDN es una característica complementaria a su suscripción y tiene otro [plan de facturación].

<a id="Step1"> </a>
<h2>Paso 1: Crear una cuenta de almacenamiento</h2>

Use el procedimiento siguiente para crear una nueva cuenta de almacenamiento para una suscripción de Azure. Una cuenta de almacenamiento proporciona acceso a los servicios de almacenamiento de Azure. La cuenta de almacenamiento representa el nivel superior del espacio de nombres para tener acceso a cada uno de los componentes del servicio de almacenamiento de Azure: Blob servicios, servicios de cola y servicios de tabla. Para obtener más información acerca de los servicios de almacenamiento de Azure, vea [usar los servicios de almacenamiento de Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx).

Para crear una cuenta de almacenamiento, debe ser administrador del servicio o un administrador de colaboración para la suscripción asociada.

> [AZURE.NOTE] Para obtener información acerca de cómo realizar esta operación mediante el uso de la API de administración de servicio de Azure, vea el tema de referencia de [Crear la cuenta de almacenamiento](http://msdn.microsoft.com/library/windowsazure/hh264518.aspx) .

**Para crear una cuenta de almacenamiento para una suscripción de Azure**

1.  Inicie sesión en el [portal de clásico de Azure].
2.  En la esquina inferior izquierda, haga clic en **nuevo**. En el cuadro de diálogo **nuevo** , seleccione los **Servicios de datos**, haga clic en **almacenamiento**, a continuación, **Crear rápido**.

    Aparece el cuadro de diálogo **Crear cuenta de almacenamiento** .

    ![Crear cuenta de almacenamiento][create-new-storage-account]

4. En el campo **URL** , escriba un nombre de subdominio. Esta entrada puede contener de 3-24 letras minúsculas y números.

    Este valor se convierte en el nombre de host en el URI que se utiliza para dirigir recursos blobs, cola o tabla para la suscripción. Para un recurso de contenedor en el servicio de blobs de direcciones, debe usar un URI en el siguiente formato, donde * &lt;StorageAccountLabel&gt; * hace referencia al valor que escribió en **Escriba una dirección URL**:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt; *

    **Importante:** La etiqueta de dirección URL el subdominio de la cuenta de almacenamiento URI de formularios y debe ser única entre todos los servicios alojados en Azure.

    Este valor también se usa como el nombre de la cuenta de almacenamiento en el portal de o al obtener acceso a esta cuenta mediante programación.

5.  Desde la lista desplegable **Grupo afinidad/región** , seleccione un grupo de afinidad para la cuenta de almacenamiento o la región. Seleccione un grupo de afinidad en lugar de una región si desea que los servicios de almacenamiento en el mismo centro de datos con otros servicios de Windows Azure que está utilizando. Esto puede mejorar el rendimiento y no se incurre cargos de salida.  

    **Nota:** Para crear un grupo de afinidad, abra el área **configuración** del Portal de administración, haga clic en **grupos de afinidad**y, a continuación, haga clic en **Agregar un grupo de afinidad** o **Agregar**. También puede crear y administrar grupos de afinidad con la API de administración de servicio de Windows Azure. Para obtener más información, vea [operaciones en grupos de afinidad].

6. En la lista desplegable de **suscripción** , seleccione la suscripción que se utilizará con la cuenta de almacenamiento.
7.  Haga clic en **crear cuenta de almacenamiento**. El proceso de creación de la cuenta de almacenamiento podría tardar varios minutos en completarse.
8.  Para comprobar que la cuenta de almacenamiento se creó correctamente, compruebe que la cuenta aparece en los elementos que aparecen para el **almacenamiento de** estado **en línea**.

<a id="Step2"> </a>
<h2>Paso 2: Crear un nuevo punto final CDN para su cuenta de almacenamiento</h2>

Después de habilitar el acceso CDN a una cuenta de almacenamiento o servicio alojado, todos los objetos disponibles públicamente son elegibles para CDN almacenamiento en caché. Si modifica un objeto que actualmente se almacena en caché la CDN, el nuevo contenido no estará disponible a través de la CDN hasta que la CDN actualiza su contenido cuando finalice el período de tiempo de vida contenido en caché.

**Para crear un nuevo punto final CDN para su cuenta de almacenamiento**

1. En el [portal de clásica Azure], en el panel de navegación, haga clic en **CDN**.

2. En la cinta de opciones, haga clic en **nuevo**. En el cuadro de diálogo **nuevo** , seleccione **Los servicios de aplicación**, **CDN**, a continuación, **Crear rápido**.

3. En el menú desplegable del **Dominio de origen** , seleccione la cuenta de almacenamiento que creó en la sección anterior de la lista de sus cuentas de almacenamiento disponible. 

4. Haga clic en el botón **crear** para crear el extremo de nuevo.

5. Una vez creado el extremo, aparece en una lista de extremos de la suscripción. La vista de lista muestra la dirección URL para tener acceso a contenido de la caché, así como el dominio de origen. 

    El dominio de origen es la ubicación desde la que la CDN almacena contenido. El dominio de origen puede ser una cuenta de almacenamiento o un servicio de nube para el propósito de este ejemplo, se utiliza una cuenta de almacenamiento. Contenido de almacenamiento de información se almacena en caché para servidores perimetrales conforme a una configuración de control de caché que especifique o que la heurística predeterminada de la red de almacenamiento en caché. 


    > [AZURE.NOTE] La configuración creada para el extremo no inmediatamente estará disponible; puede tardar hasta 60 minutos para el registro en propagarse a través de la red CDN. Los usuarios que intenten utilizar el nombre de dominio CDN inmediatamente pueden recibir el código de estado 400 (solicitud incorrecta) hasta que el contenido esté disponible a través de la CDN.

<a id="Step3"> </a>
<h2>Paso 3: Acceso CDN contenido</h2> 

Para obtener acceso a contenido almacenado en la CDN, use la dirección URL de CDN proporcionados en el portal. La dirección de un blob en caché será similar al siguiente:

http://<*CDNNamespace*\>.vo.msecnd.net/ <*myPublicContainer*\>/<*BlobName*\>

<a id="Step4"> </a>
<h2>Paso 4: Quitar el contenido de la CDN</h2>

Si ya no desea un objeto en la red de entrega de contenido (CDN) de Azure en caché, puede realizar uno de los siguientes pasos:

-   Para un blobs de Windows Azure, puede eliminar el blob desde el contenedor público.
-   Puede hacer que el contenedor privado en lugar de público. Para obtener más información, vea [Restringir el acceso a los contenedores y BLOB](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/#restrict-access-to-containers-and-blobs) .
-   Puede deshabilitar o eliminar el extremo CDN con el Portal de administración.
-   Puede modificar el servicio hospedado ya no responder a solicitudes para el objeto.

Un objeto ya en caché en la CDN permanecerá en caché hasta que el período de tiempo de vida para el objeto. Cuando expira el período de tiempo de vida, la CDN comprueba si el extremo CDN sigue siendo válido y el objeto todavía accesible de forma anónima. Si no es así, ya no se caché el objeto.

La capacidad de purga inmediatamente el contenido no es compatible actualmente en el Portal de administración de Azure. Póngase en contacto con [soporte técnico de Azure](https://azure.microsoft.com/support/options/) si necesita purgar inmediatamente contenido. 

## <a name="additional-resources"></a>Recursos adicionales

-   [Cómo crear un grupo de afinidad en Azure]
-   [Cómo: administrar cuentas de almacenamiento para una suscripción de Azure]
-   [Acerca de la API de administración de servicio]
-   [Cómo asignar CDN contenido a un dominio personalizado]

  [Create Storage Account]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/
  [Ubicaciones de nodo de Azure CDN]: http://msdn.microsoft.com/library/windowsazure/gg680302.aspx
  [Portal de clásico de Azure]: https://manage.windowsazure.com/
  [plan de facturación]: /pricing/calculator/?scenario=full
  [Cómo crear un grupo de afinidad en Azure]: http://msdn.microsoft.com/library/azure/ee460798.aspx
  [Overview of the Azure CDN]: http://msdn.microsoft.com/library/windowsazure/ff919703.aspx
  [Acerca de la API de administración de servicio]: http://msdn.microsoft.com/library/windowsazure/ee460807.aspx
  [Cómo asignar CDN contenido a un dominio personalizado]: http://msdn.microsoft.com/library/windowsazure/gg680307.aspx


[create-new-storage-account]: ./media/cdn/CDN_CreateNewStorageAcct.png
[Previous Management Portal]: ../../Shared/Media/previous-portal.png
