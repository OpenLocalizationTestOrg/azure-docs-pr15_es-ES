<properties
    pageTitle="Empezar a trabajar con el catálogo de datos | Microsoft Azure"
    description="Tutorial de llevar a cabo la presentación de las capacidades del catálogo de datos de Azure y escenarios."
    documentationCenter=""
    services="data-catalog"
    authors="steelanddata"
    manager="jhubbard"
    editor=""
    tags=""/>
<tags
    ms.service="data-catalog"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-catalog"
    ms.date="09/20/2016"
    ms.author="spelluru"/>

# <a name="get-started-with-azure-data-catalog"></a>Empezar a trabajar con el catálogo de datos de Azure
Catálogo de datos de Azure es un servicio de nube totalmente administrado que sirve como un sistema de registro y sistema de detección de activos de datos de la empresa. Para obtener información detallada, vea [¿Qué es el catálogo de datos de Azure](data-catalog-what-is-data-catalog.md).

Este tutorial le ayuda a empezar a trabajar con el catálogo de datos de Azure. Realice los siguientes procedimientos de este tutorial:

| Procedimiento | Descripción |
| :--- | :---------- |
| [Catálogo de datos de aprovisionamiento](#provision-data-catalog) | En este procedimiento, aprovisionar o configurar el catálogo de datos de Azure. Solo si no se ha configurado el catálogo antes de realizar este paso. Puede tener solo un catálogo de datos por la organización (dominio de Active Directory de Microsoft Azure), aunque haya varias suscripciones asociadas a su cuenta de Azure. |
| [Registrar activos de datos](#register-data-assets) | En este procedimiento, registrar activos de datos de la base de datos de ejemplo de AdventureWorks2014 con el catálogo de datos. Registro es el proceso de extraer metadatos estructurales clave como nombres, tipos y ubicaciones del origen de datos y copiar metadatos al catálogo. El origen de datos y los activos de datos siguen estando donde están, pero los metadatos que se utilizan en el catálogo para hacerlos más fácilmente detectable y comprensible. |
| [Descubra los activos de datos](#discover-data-assets) | En este procedimiento, se utiliza el portal de catálogo de datos de Azure para detectar los activos de datos que se registraron en el paso anterior. Después de que se ha registrado un origen de datos con el catálogo de datos de Azure, sus metadatos se indizan por el servicio para que los usuarios pueden buscar fácilmente los datos que necesitan. |
| [Anotar activos de datos](#annotate-data-assets) | En este procedimiento para proporcionar anotaciones (información como descripciones, etiquetas, documentación o expertos) de los activos de datos. Esta información el complemento de los metadatos que se extraen del origen de datos y para facilitar el origen de datos a más personas. |
| [Conectarse a los activos de datos](#connect-to-data-assets) | En este procedimiento, abra los activos de datos en las herramientas de cliente integrado (por ejemplo, Excel y las herramientas de datos de SQL Server) y una herramienta no integrada (SQL Server Management Studio). |
| [Administrar activos de datos](#manage-data-assets) | En este procedimiento, configurar la seguridad de los activos de datos. Catálogo de datos no dar a los usuarios acceso a los datos. El propietario del origen de datos controla el acceso a los datos. <br/><br/> Con el catálogo de datos, puede detectar orígenes de datos y ver los **metadatos** relacionados con los orígenes registrados en el catálogo. Puede haber situaciones, sin embargo, donde los orígenes de datos deben estar visibles solo para usuarios específicos o a los miembros de grupos específicos. Para estos escenarios, puede usar el catálogo de datos para asumir la propiedad de activos de datos registrados en el catálogo y controlar la visibilidad de los activos que es el propietario. |
| [Quitar datos activos](#remove-data-assets) | En este procedimiento, obtenga información sobre cómo eliminar los activos de datos desde el catálogo de datos. |  

## <a name="tutorial-prerequisites"></a>Requisitos previos de los tutoriales

### <a name="azure-subscription"></a>Suscripción de Azure
Para configurar el catálogo de datos de Azure, debe ser el propietario o el copropietario de una suscripción de Azure.

Suscripciones de Azure ayudarle a organizar el acceso a los recursos de servicio de nube como catálogo de datos de Azure. También ayudan a controlar cómo se notifica el uso de recursos, facturado y de pago. Cada suscripción puede tener una instalación diferente de pago y facturación, por lo que puede tener varias suscripciones y distintos planes por departamento, project, oficina y así sucesivamente. Cada servicio de nube pertenece a una suscripción y, a continuación, debe tener una suscripción antes de configurar el catálogo de datos de Azure. Para obtener más información, vea [Administrar cuentas, suscripciones y funciones administrativas](../active-directory/active-directory-how-subscriptions-associated-directory.md).

Si no tiene una suscripción, puede crear una cuenta de prueba gratuita de dos minutos. Para obtener información detallada, vea [Prueba gratuita](https://azure.microsoft.com/pricing/free-trial/) .

### <a name="azure-active-directory"></a>Azure Active Directory
Para configurar el catálogo de datos de Azure, debe haber iniciado sesión con una cuenta de usuario de Azure Active Directory (AD Azure). Debe ser el propietario o el copropietario de una suscripción de Azure.  

Azure AD proporciona una forma sencilla de su empresa administrar la identidad y acceso, tanto en la nube y locales. Puede usar una sola cuenta profesional o educativa para iniciar sesión en cualquier aplicación web de nube o local. Catálogo de datos de Azure usa Azure AD para autenticar inicio de sesión. Para obtener más información, consulte [¿Qué es Azure Active Directory](../active-directory/active-directory-whatis.md).

### <a name="azure-active-directory-policy-configuration"></a>Configuración de directiva de Azure Active Directory

Puede encontrarse con una situación donde puede iniciar sesión en el portal de catálogo de datos de Azure, pero cuando intenta iniciar sesión en la herramienta de registro de origen de datos, recibirá un mensaje de error que le impide el inicio de sesión. Este error puede producirse cuando está conectado a la red de la compañía o cuando se conecta desde fuera de la red de la compañía.

La herramienta de registro utiliza la *autenticación de formularios* para validar inicios de sesión de usuario en Active Directory de Azure. Para el inicio de sesión correctamente, un administrador de Azure Active Directory debe habilitar la autenticación de formularios en la *Directiva de autenticación global*.

Con la directiva de autenticación global, puede habilitar la autenticación por separado para intranet y extranets conexiones, tal como se muestra en la siguiente imagen. Si no está habilitada la autenticación de formularios de la red desde la que va a conectarse, pueden producirse errores de inicio de sesión.

 ![Directiva de autenticación global de Azure Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

Para obtener más información, consulte [configuración de las directivas de autenticación](https://technet.microsoft.com/library/dn486781.aspx).

## <a name="provision-data-catalog"></a>Catálogo de datos de aprovisionamiento
Puede aprovisionar solo un catálogo de datos por la organización (dominio de Azure Active Directory). Por lo tanto, si el propietario o el copropietario de una suscripción a Azure que pertenece a este dominio de Azure Active Directory ya ha creado un catálogo, no podrá crear un catálogo de nuevo incluso si tiene varias suscripciones de Azure. Para comprobar si se ha creado un catálogo de datos por el usuario en su dominio de Azure Active Directory, vaya a la [página de inicio del catálogo de datos de Azure](http://azuredatacatalog.com) y compruebe si puede ver el catálogo. Si ya ha creado un catálogo para usted, omita el siguiente procedimiento y vaya a la sección siguiente.    

1. Vaya a la [página de servicio del catálogo de datos](https://azure.microsoft.com/services/data-catalog) y haga clic en **empezar**.

    ![Catálogo de datos de Azure--página de inicio de marketing](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)
2. Inicie sesión con una cuenta de usuario es el propietario o copropietario de una suscripción de Azure. Vea la siguiente página después de iniciar sesión.

    ![Catálogo de datos de Azure--catálogo de datos de aprovisionamiento](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)
3. Especifique un **nombre** para el catálogo de datos, la **suscripción** que desea usar y la **ubicación** para el catálogo.
4. Expanda **precios** y seleccione un catálogo de datos de Azure **edition** (libre o estándar).
    ![Catálogo de datos de Azure: seleccione edition](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)
5. Expanda el **Catálogo de usuarios** y haga clic en **Agregar** para agregar usuarios para el catálogo de datos. Se agregan automáticamente a este grupo.
    ![Catálogo de datos de Azure: los usuarios](media/data-catalog-get-started/data-catalog-add-catalog-user.png)
6. Expanda **Los administradores de catálogo** y haga clic en **Agregar** para agregar los administradores adicionales para el catálogo de datos. Se agregan automáticamente a este grupo.
    ![Catálogo de datos de Azure: los administradores](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)
7. Haga clic en **Catálogo de crear** para crear el catálogo de datos para su organización. Consulte la página principal para el catálogo de datos después de crearlo.
    ![Catálogo de datos de Azure--creado](media/data-catalog-get-started/data-catalog-created.png)    

### <a name="find-a-data-catalog-in-the-azure-portal"></a>Buscar un catálogo de datos en el portal de Azure
1. En una pestaña independiente en el explorador web o en una ventana del explorador web independientes, vaya al [portal de Azure](https://portal.azure.com) e inicie sesión con la misma cuenta que usó para crear el catálogo de datos en el paso anterior.
2. Seleccione **Examinar** y, a continuación, haga clic en **Catálogo de datos**.

    ![Catálogo de datos de Azure--examinar Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png) ver el catálogo de datos que creó.

    ![Catálogo de datos de Azure--catálogo de vista de lista](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)
4.  Haga clic en el catálogo que ha creado. Vea el módulo de **Catálogo de datos** en el portal.

    ![Catálogo de datos de Azure: módulo en el portal ](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)
5. Puede ver las propiedades del catálogo de datos y actualizarlos. Por ejemplo, haga clic en **el nivel de precios** y cambie la edición.

    ![Catálogo de datos de Azure--nivel de precios](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

### <a name="adventure-works-sample-database"></a>Base de datos de ejemplo Adventure Works
En este tutorial, registrar activos de datos (tablas) de la base de datos de ejemplo AdventureWorks2014 para el motor de base de datos de SQL Server, pero puede utilizar cualquier origen de datos compatibles si prefiere trabajar con datos que es relevante para su función y familiares. Para obtener una lista de orígenes de datos compatibles, consulte [orígenes de datos compatibles](data-catalog-dsr.md).

### <a name="install-the-adventure-works-2014-oltp-database"></a>Instalar la base de datos de Adventure Works 2014 OLTP
La base de datos de Adventure Works es compatible con estándares escenarios de procesamiento de transacciones en línea para un fabricante de bicicleta ficticio (Adventure Works ciclos), que incluye productos, compras y ventas. En este tutorial, registrar información sobre los productos en el catálogo de datos de Azure.

Para instalar la base de datos de ejemplo Adventure Works:

1. Descargar [aventura Backup.zip de base de datos de Works 2014 completo](https://msftdbprodsamples.codeplex.com/downloads/get/880661) en CodePlex.
2. Para restaurar la base de datos en su equipo, siga las instrucciones en [restaurar una copia de seguridad de base de datos mediante el uso de SQL Server Management Studio](http://msdn.microsoft.com/library/ms177429.aspx)o siguiendo estos pasos:
    1. Abra SQL Server Management Studio y conectar con el motor de base de datos de SQL Server.
    2. Haga clic en **bases de datos** y haga clic en **Restaurar base de datos**.
    3. En **Restaurar base de datos**, haga clic en la opción de **dispositivo** de **origen** y haga clic en **Examinar**.
    4. **Seleccione dispositivos de copia de seguridad**, haga clic en **Agregar**.
    5. Vaya a la carpeta donde tiene el archivo **AdventureWorks2014.bak** , seleccione el archivo y haga clic en **Aceptar** para cerrar el cuadro de diálogo **Localizar el archivo de copia de seguridad** .
    6. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Seleccione dispositivos de copia de seguridad** .    
    7. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Restaurar base de datos** .

Ahora puede registrar activos de datos de la base de datos de ejemplo Adventure Works mediante el catálogo de datos de Azure.

## <a name="register-data-assets"></a>Registrar activos de datos

En este ejercicio, utilice la herramienta registro registrar activos de datos de la base de datos de Adventure Works con el catálogo. Registro es el proceso de extraer metadatos estructurales clave como nombres, tipos y ubicaciones desde el origen de datos y los activos contiene y copiar metadatos en el catálogo. El origen de datos y los activos de datos siguen estando donde están, pero los metadatos que se utilizan en el catálogo para hacerlos más fácilmente detectable y comprensible.

### <a name="register-a-data-source"></a>Registrar un origen de datos

1.  Vaya a la [página de inicio del catálogo de datos de Azure](https://azuredatacatalog.com) y haga clic en **Publicar datos**.

    ![Catálogo de datos de Azure--publicar datos de botón](media/data-catalog-get-started/data-catalog-publish-data.png)

2.  Haga clic en **Iniciar aplicación** para descargar, instalar y ejecutar la herramienta de registro en el equipo.

    ![Catálogo de datos de Azure: botón Iniciar](media/data-catalog-get-started/data-catalog-launch-application.png)

3. En la página **principal** , haga clic en **iniciar sesión** e introduzca sus credenciales.    

    ![Catálogo de datos de Azure: página de bienvenida](media/data-catalog-get-started/data-catalog-welcome-dialog.png)

4. En la página **Catálogo de datos de Microsoft Azure** , haga clic en **siguiente**y **SQL Server** .

    ![Catálogo de datos de Azure: orígenes de datos](media/data-catalog-get-started/data-catalog-data-sources.png)

5.  Especificar las propiedades de conexión de SQL Server para **AdventureWorks2014** (vea el siguiente ejemplo) y haga clic en **Conectar**.

    ![Catálogo de datos de Azure: configuración de conexión de SQL Server](media/data-catalog-get-started/data-catalog-sql-server-connection.png)

6.  Registrar los metadatos de los activos de datos. En este ejemplo, registrar objetos de **Producción o producto** del espacio de nombres de producción AdventureWorks:

    1. En el árbol de la **Jerarquía de servidor** , expanda **AdventureWorks2014** y haga clic en **producción**.
    2. Seleccionar **producto**, **ProductCategory**, **ProductDescription**y **ProductPhoto** mediante Ctrl + clic del mouse.
    3. Haga clic en la **flecha seleccionado de mover** (**>**). Esta acción mueve todos los objetos seleccionados en la lista de **objetos a registrar** .

        ![Tutorial de catálogo de datos de Azure--examinar y seleccionar objetos](media/data-catalog-get-started/data-catalog-server-hierarchy.png)
    4. Seleccione **incluir una vista previa** para incluir una vista previa de instantánea de los datos. La instantánea incluye hasta 20 registros de cada tabla y se copia en el catálogo.
    5. Seleccione **Incluir perfil de datos** para incluir una instantánea de las estadísticas de objeto para el perfil de datos (por ejemplo: valores mínimos, máximos y medios de una columna, el número de filas).
    6. En el campo **Agregar etiquetas** , escriba **de adventure works, ciclos**. Esta acción agrega etiquetas de la búsqueda para estos activos de datos. Las etiquetas son excelentes para ayudar a los usuarios buscar un origen de datos registrados.
    7. Especifique el nombre de un **experto** en estos datos (opcionales).

        ![Tutorial de catálogo de datos Azure--objetos registrar](media/data-catalog-get-started/data-catalog-objects-register.png)

    8. Haga clic en **REGISTRARSE**. Catálogo de datos de Azure registra los objetos seleccionados. En este ejercicio, se registran los objetos seleccionados de Adventure Works. La herramienta de registro extrae los metadatos de los activos de datos y copia los datos en el servicio de catálogo de datos de Azure. Los datos permanecen donde reside actualmente y permanece en el control de los administradores y directivas del sistema actual.

        ![Catálogo de datos de Azure--objetos registrados](media/data-catalog-get-started/data-catalog-registered-objects.png)

    9. Para ver los objetos de origen de datos registrados, haga clic en **El Portal de la vista**. En el portal de catálogo de datos de Azure, confirme que ve las cuatro tablas y la base de datos en la vista de cuadrícula.

        ![Objetos en el portal de catálogo de datos de Azure ](media/data-catalog-get-started/data-catalog-view-portal.png)


En este ejercicio había registrado objetos de la base de datos de ejemplo Adventure Works para que puedan ser detectados fácilmente los usuarios en toda la organización. En el siguiente ejercicio, aprenda a descubrir activos de datos registrados.

## <a name="discover-data-assets"></a>Descubra los activos de datos
Detección de catálogo de datos de Azure usa dos mecanismos principales: buscar y filtrar.

Buscar está diseñado para intuitiva y eficaz. De forma predeterminada, los términos de búsqueda se comparan con cualquier propiedad en el catálogo, incluidas las anotaciones proporcionado por el usuario.

Filtrado está diseñado para búsqueda de complemento. Puede seleccionar características específicas como expertos, tipo de origen de datos, tipo de objeto y etiquetas para ver los activos de datos coincidentes y para restringir los resultados de búsqueda para que coincidan con los activos.

Mediante una combinación de búsqueda y el filtrado, puede desplazarse rápidamente los orígenes de datos que se han registrado con el catálogo de datos de Azure para detectar los activos de datos que necesita.

En este ejercicio, se utiliza el portal de catálogo de datos de Azure para descubrir activos de datos registrados en el ejercicio anterior. Para obtener más información sobre la sintaxis de la búsqueda, vea [referencia de sintaxis de la búsqueda de catálogo de datos](https://msdn.microsoft.com/library/azure/mt267594.aspx) .

Los siguientes son algunos ejemplos para descubrir activos de datos en el catálogo.  

### <a name="discover-data-assets-with-basic-search"></a>Descubra los activos de datos con la búsqueda básica
Búsqueda básica le ayuda a buscar un catálogo mediante uno o varios términos de búsqueda. Los resultados son los activos que coinciden en cualquier propiedad con uno o más de las condiciones especificadas.

1. Haga clic en **Inicio** , en el portal de catálogo de datos de Azure. Si ha cerrado el explorador web, vaya a la [página de inicio del catálogo de datos de Azure](https://www.azuredatacatalog.com).
2. En el cuadro Buscar, escriba `cycles` y presione **ENTRAR**.

    ![Catálogo de datos de Azure--búsqueda de texto básico](media/data-catalog-get-started/data-catalog-basic-text-search.png)
3. Confirme que ve las cuatro tablas y la base de datos (AdventureWorks2014) en los resultados. Puede cambiar entre la **vista de cuadrícula** y **vista de lista** haciendo clic en la barra de herramientas botones tal como se muestra en la siguiente imagen. Observe que la palabra clave de búsqueda está resaltada en los resultados de búsqueda porque está **activada**la opción de **Resaltar** . También puede especificar el número de **resultados por página** de resultados de búsqueda.

    ![Catálogo de datos de Azure: resultados de la búsqueda de texto básico](media/data-catalog-get-started/data-catalog-basic-text-search-results.png)

    El panel de **búsquedas** está a la izquierda y el panel **Propiedades** está a la derecha. En el panel de **búsquedas** , puede cambiar los criterios de búsqueda y filtrar los resultados. El panel **Propiedades** muestra las propiedades de un objeto seleccionado en la lista o la cuadrícula.

4. Haga clic en **producto** en los resultados de búsqueda. Haga clic en la **vista previa**, **columnas**, **Perfil de datos**y las fichas de **documentación** , o haga clic en la flecha para expandir el panel inferior.  

    ![Catálogo de datos de Azure--inferior](media/data-catalog-get-started/data-catalog-data-asset-preview.png)

    En la ficha de **vista previa** , verá una vista previa de los datos en la tabla **Product** .  
5. Haga clic en la ficha **columnas** para encontrar más detalles acerca de las columnas (como **nombre** y **tipo de datos**) en datos activos.
6. Haga clic en la pestaña de **Perfil de datos** para ver la generación de perfiles de datos (por ejemplo: número de filas, el tamaño de datos o valor mínimo de una columna) de los activos de datos.
7. Filtrar los resultados mediante **filtros** de la izquierda. Por ejemplo, haga clic en **tabla** de **Tipo de objeto**y ver sólo las cuatro tablas, no la base de datos.

    ![Catálogo de datos de Azure: filtrar los resultados de búsqueda](media/data-catalog-get-started/data-catalog-filter-search-results.png)

### <a name="discover-data-assets-with-property-scoping"></a>Descubra los activos de datos con ámbito (propiedad)
Especificar el ámbito propiedad le ayuda a descubrir activos de datos donde el término de búsqueda coincide con la propiedad especificada.

1. Borrar el filtro de **tabla** en el **Tipo de objeto** en **filtros**.  
2. En el cuadro Buscar, escriba `tags:cycles` y presione **ENTRAR**. Vea [referencia de sintaxis de la búsqueda de catálogo de datos](https://msdn.microsoft.com/library/azure/mt267594.aspx) para todas las propiedades que puede usar para buscar el catálogo de datos.
3. Confirme que ve las cuatro tablas y la base de datos (AdventureWorks2014) en los resultados.  

    ![Catálogo de datos: especificar el ámbito de resultados de búsqueda (propiedad)](media/data-catalog-get-started/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>Guardar la búsqueda
1. En el panel de **búsquedas** en la sección **Actual de búsqueda** , escriba un nombre para la búsqueda y haga clic en **Guardar**.

    ![Catálogo de datos de Azure--Guardar búsqueda](media/data-catalog-get-started/data-catalog-save-search.png)
2. Confirme que la búsqueda guardada se muestra en **Búsquedas guardadas**.

    ![Catálogo de datos de Azure--búsquedas guarda](media/data-catalog-get-started/data-catalog-saved-search.png)
3. Seleccione una de las acciones que puede realizar en la búsqueda guardada (**cambiar el nombre**, **Eliminar**, búsqueda **Guardar como predeterminado** ).

    ![Catálogo de datos de Azure--guardado opciones de búsqueda](media/data-catalog-get-started/data-catalog-saved-search-options.png)

### <a name="boolean-operators"></a>Operadores booleanos
Puede ampliar o reducir la búsqueda con operadores booleanos.

1. En el cuadro Buscar, escriba `tags:cycles AND objectType:table`, y presione **ENTRAR**.
2. Confirme que ve sólo tablas (no la base de datos) en los resultados.  

    ![Catálogo de datos de Azure--operador booleano de búsqueda](media/data-catalog-get-started/data-catalog-search-boolean-operator.png)

### <a name="grouping-with-parentheses"></a>Agrupación con paréntesis
Agrupación con paréntesis, puede agrupar elementos de la consulta para conseguir el aislamiento lógico, especialmente junto con operadores booleanos.

1. En el cuadro Buscar, escriba `name:product AND (tags:cycles AND objectType:table)` y presione **ENTRAR**.
2. Confirme que ve la tabla de **productos** en los resultados de búsqueda.

    ![Catálogo de datos de Azure--búsqueda de agrupación](media/data-catalog-get-started/data-catalog-grouping-search.png)   

### <a name="comparison-operators"></a>Operadores de comparación
Con operadores de comparación, puede usar las comparaciones que no sea de igualdad para las propiedades que tienen los tipos de datos numéricos y de fecha.

1. En el cuadro Buscar, escriba `lastRegisteredTime:>"06/09/2016"`.
2. Borrar el filtro de **tabla** en **El tipo de objeto**.
3. Presione **ENTRAR**.
4. Confirme que ve las tablas de **producto**, **ProductCategory**, **ProductDescription**y **ProductPhoto** y la base de datos de AdventureWorks2014 registrado en resultados de búsqueda.

    ![Catálogo de datos de Azure: resultados de búsqueda de comparación](media/data-catalog-get-started/data-catalog-comparison-operator-results.png)

Vea [cómo descubrir activos de datos](data-catalog-how-to-discover.md) para obtener información detallada acerca de descubrimiento de activos de datos y la [referencia de sintaxis de la búsqueda de catálogo de datos](https://msdn.microsoft.com/library/azure/mt267594.aspx) para la sintaxis de la búsqueda.

## <a name="annotate-data-assets"></a>Anotar activos de datos
En este ejercicio, use el portal de catálogo de datos de Azure para realizar anotaciones (agregar información como descripciones, etiquetas o expertos) se ha registrado anteriormente en el catálogo de activos de datos. Las anotaciones completen y mejoran los metadatos estructurales extraídos del origen de datos durante el registro y facilita los activos de datos ver y entender.

En este ejercicio, anotar un activo de datos único (ProductPhoto). Agregue un nombre descriptivo y una descripción para el ProductPhoto datos activos.  

1.  Vaya a la [página de inicio del catálogo de datos de Azure](https://www.azuredatacatalog.com) y buscar con `tags:cycles` para buscar los activos de datos que se ha registrado.  
2. Haga clic en **ProductPhoto** en resultados de búsqueda.  
3. Escriba **imágenes de los productos** para **fotos de producto para los materiales de marketing** y **Un nombre descriptivo** para la **Descripción**.

    ![Catálogo de datos de Azure--ProductPhoto descripción](media/data-catalog-get-started/data-catalog-productphoto-description.png)

    La **Descripción** de otros usuarios ayuda a descubrir y entender por qué y cómo usar los activos de datos seleccionados. También puede agregar más etiquetas y ver las columnas. Ahora puede intentar búsqueda y el filtrado para descubrir activos de datos mediante los metadatos descriptivo que agregó en el catálogo.

También puede hacer lo siguiente en esta página:

- Agregar a expertos para datos activos. Haga clic en **Agregar** en el área de **expertos** .
- Agregar etiquetas en el nivel de conjunto de datos. Haga clic en **Agregar** en el área de **etiquetas** . Una etiqueta puede ser una etiqueta de usuario o una etiqueta de glosario. La edición estándar del catálogo de datos incluye un glosario de empresas que ayuda a los administradores de catálogo definir una taxonomía empresarial central. Los usuarios del catálogo, a continuación, pueden realizar anotaciones en activos de datos con los términos del glosario. Para obtener más información, consulte [cómo configurar el glosario de empresa para regulados etiquetado](data-catalog-how-to-business-glossary.md)
- Agregar etiquetas a nivel de columna. Haga clic en **Agregar** en **las etiquetas** de la columna que desee anotar.
- Agregar descripción en el nivel de columna. Escriba la **Descripción** de una columna. También puede ver los metadatos de descripción extraídos del origen de datos.
- Agregar información de **solicitud de acceso** que se muestra a los usuarios cómo solicitar acceso a datos activos.

    ![Catálogo de datos de Azure: agregar etiquetas, descripciones](media/data-catalog-get-started/data-catalog-add-tags-experts-descriptions.png)

- Elija la ficha **documentación** y proporcionar la documentación de datos activos. Documentación de catálogo de datos de Azure, puede usar el catálogo de datos como un repositorio de contenido para crear una descripción completa de los activos de datos.

    ![Catálogo de datos de Azure--ficha documentación](media/data-catalog-get-started/data-catalog-documentation.png)


También puede agregar una anotación a varios activos de datos. Por ejemplo, puede seleccionar todos los activos de datos que haya registrado y especifique a un experto para ellos.

![Catálogo de datos de Azure--anotar múltiples activos de datos](media/data-catalog-get-started/data-catalog-multi-select-annotate.png)

Catálogo de datos de Azure es compatible con un enfoque multitud de origen para anotaciones. Cualquier usuario del catálogo de datos puede agregar etiquetas (usuario o glosario), descripciones y otros metadatos, para que cualquier usuario con una perspectiva de un activo de datos y su uso puede tener esa perspectiva capturada y disponible para otros usuarios.

Vea [cómo anotar activos de datos](data-catalog-how-to-annotate.md) para obtener información detallada acerca de las anotaciones de activos de datos.

## <a name="connect-to-data-assets"></a>Conectarse a los activos de datos
En este ejercicio, abrir activos de datos en una herramienta de cliente integrado (Excel) y una herramienta no integrada (SQL Server Management Studio) mediante la información de conexión.

> [AZURE.NOTE] Es importante recordar que el catálogo de datos de Azure no le da acceso al origen de datos real, simplemente hace que resulte más fácil descubrir y que lo comprende. Cuando se conecta a un origen de datos, la aplicación cliente que elige usa las credenciales de Windows o solicita las credenciales de según sea necesario. Si no ha concedido acceso al origen de datos, debe tener concedido acceso para poder conectarse.

### <a name="connect-to-a-data-asset-from-excel"></a>Conectarse a un activo de datos desde Excel

1. Seleccione el **producto** de los resultados de búsqueda. Haga clic en **Abrir en** la barra de herramientas y haga clic en **Excel**.

    ![Catálogo de datos de Azure: conectarse a datos activos](media/data-catalog-get-started/data-catalog-connect1.png)
2. Haga clic en **Abrir** en la ventana emergente de descarga. Este proceso puede variar según el explorador.

    ![Catálogo de datos de Azure: descargar el archivo de conexión de Excel](media/data-catalog-get-started/data-catalog-download-open.png)
3. En la ventana de **Aviso de seguridad de Microsoft Excel** , haga clic en **Habilitar**.

    ![Catálogo de datos de Azure--ventana emergente de seguridad de Excel](media/data-catalog-get-started/data-catalog-excel-security-popup.png)
4. Mantener los valores predeterminados en el cuadro de diálogo **Importar datos** y haga clic en **Aceptar**.

    ![Catálogo de datos de Azure: importar datos de Excel](media/data-catalog-get-started/data-catalog-excel-import-data.png)
5. Ver el origen de datos en Excel.

    ![Catálogo de datos de Azure--tabla de productos en Excel](media/data-catalog-get-started/data-catalog-connect2.png)

En este ejercicio, conectada a los activos de datos descubiertos mediante el catálogo de datos de Azure. En el portal de catálogo de datos de Azure, puede conectar directamente mediante las aplicaciones cliente integradas en **Abrir en el** menú. También puede conectar con cualquier aplicación que elija con la información de ubicación de la conexión incluida en los metadatos de activos. Por ejemplo, puede usar SQL Server Management Studio para conectarse a la base de datos de AdventureWorks2014 para tener acceso a los datos de los activos de datos registrados en este tutorial.

1. Abra **SQL Server Management Studio**.
2. En el cuadro de diálogo **Conectar con el servidor** , escriba el nombre del servidor desde el panel de **Propiedades** en el portal de catálogo de datos de Azure.
3. Usar autenticación apropiado y credenciales para tener acceso a datos activos. Si no tiene acceso, use la información en el campo de **Solicitud de acceso** obtenerlo.

    ![Catálogo de datos de Azure--solicitar acceso](media/data-catalog-get-started/data-catalog-request-access.png)

Haga clic en **Las cadenas de conexión de vista** para ver y copiar ADF.NET, ODBC y OLEDB cadenas de conexión en el Portapapeles para utilizarlo en su aplicación.

## <a name="manage-data-assets"></a>Administrar activos de datos
En este paso, consulte cómo configurar la seguridad de los activos de datos. Catálogo de datos no dar a los usuarios acceso a los datos. El propietario del origen de datos controla el acceso a los datos.

Puede usar el catálogo de datos para ver los orígenes de datos y ver los metadatos de los orígenes registrados en el catálogo. Puede haber situaciones, sin embargo, donde los orígenes de datos solo deben ser visibles para usuarios específicos o a los miembros de grupos específicos. Para estos escenarios, puede usar el catálogo de datos para asumir la propiedad de activos de datos registrados en el catálogo y al control, a continuación, la visibilidad de los activos es el propietario.

> [AZURE.NOTE] Las capacidades de administración descritas en este ejercicio están disponibles solo en la edición estándar del catálogo de datos de Azure, no en la edición gratuita.
En el catálogo de datos de Azure, puede asumir la propiedad de activos de datos, Agregar compañeros propietarios a activos de datos y establecer la visibilidad de activos de datos.

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>Asumir la propiedad de activos de datos y restringir la visibilidad

1. Vaya a la [página de inicio del catálogo de datos de Azure](https://www.azuredatacatalog.com). En el cuadro de texto **Buscar** , escriba `tags:cycles` y presione **ENTRAR**.
2. Haga clic en un elemento de la lista de resultados y haga clic en **Tomar propiedad** en la barra de herramientas.
3. En la sección **administración** del panel **Propiedades** , haga clic en **Tomar propiedad**.

    ![Catálogo de datos de Azure--tomar propiedad](media/data-catalog-get-started/data-catalog-take-ownership.png)
4. Para restringir la visibilidad, elija **propietarios y estos usuarios** en la sección de **visibilidad** y haga clic en **Agregar**. Escriba las direcciones de correo electrónico del usuario en el cuadro de texto y presione **ENTRAR**.

    ![Catálogo de datos de Azure--restringir el acceso](media/data-catalog-get-started/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>Quitar datos activos

En este ejercicio, se utiliza el portal de catálogo de datos de Azure para quitar la vista previa de datos de activos de datos registrados y eliminar los activos de datos del catálogo.

En el catálogo de datos de Azure, puede eliminar un activo individual o eliminar varios activos.

1. Vaya a la [página de inicio del catálogo de datos de Azure](https://www.azuredatacatalog.com).
2. En el cuadro de texto **Buscar** , escriba `tags:cycles` y haga clic en **ENTRAR**.
3. Seleccione un elemento en la lista de resultados y haga clic en **Eliminar** en la barra de herramientas tal como se muestra en la siguiente imagen:

    ![Catálogo de datos de Azure: eliminar el elemento de cuadrícula](media/data-catalog-get-started/data-catalog-delete-grid-item.png)

    Si está utilizando la vista de lista, es la casilla de verificación a la izquierda del elemento, como se muestra en la imagen siguiente:

    ![Catálogo de datos de Azure: eliminar el elemento de lista](media/data-catalog-get-started/data-catalog-delete-list-item.png)

    También puede seleccionar varios activos de datos y elimínelo tal como se muestra en la siguiente imagen:

    ![Catálogo de datos de Azure--eliminar varios activos de datos](media/data-catalog-get-started/data-catalog-delete-assets.png)


> [AZURE.NOTE] El comportamiento predeterminado del catálogo es permitir que cualquier usuario para registrar cualquier origen de datos y para permitir que cualquier usuario eliminar los activos de datos que se ha registrado. Las capacidades de administración que se incluyen en la edición estándar del catálogo de datos de Azure ofrecen opciones adicionales para asumir la propiedad de activos, restringir quién puede descubrir activos, y restringir quién puede eliminar activos.


## <a name="summary"></a>Resumen

En este tutorial, explorado capacidades esenciales del catálogo de datos de Azure, incluyendo registrar, anotando, descubrir y administrar activos de datos de la empresa. Ahora que ha finalizado el tutorial, es el momento para empezar. Puede empezar hoy por registrar los orígenes de datos que dependen de usted y su equipo y compañeros invitando a usar el catálogo.

## <a name="references"></a>Referencias

- [Cómo registrar activos de datos](data-catalog-how-to-register.md)
- [Cómo determinar los activos de datos](data-catalog-how-to-discover.md)
- [Cómo hacer anotaciones en activos de datos](data-catalog-how-to-annotate.md)
- [Cómo documentar activos de datos](data-catalog-how-to-documentation.md)
- [Cómo conectarse a los activos de datos](data-catalog-how-to-connect.md)
- [Cómo administrar activos de datos](data-catalog-how-to-manage.md)
