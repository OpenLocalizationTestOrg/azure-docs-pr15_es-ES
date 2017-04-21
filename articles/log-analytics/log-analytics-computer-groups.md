<properties
    pageTitle="Grupos de equipo en el análisis de registro iniciar búsquedas | Microsoft Azure"
    description="Los grupos de equipo en el análisis de registro permiten búsquedas de registro de ámbito a un conjunto determinado de equipos.  En este artículo se describe los diferentes métodos que puede usar para crear grupos de equipos y cómo usarlas en una búsqueda de registros."
    services="log-analytics"
    documentationCenter=""
    authors="bwren"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="bwren"/>

# <a name="computer-groups-in-log-analytics-log-searches"></a>Grupos de equipo en el análisis de registro iniciar búsquedas
Grupos de equipo en el análisis de registro permiten ámbito [iniciar búsquedas](log-analytics-log-searches.md) en un conjunto determinado de equipos.  Cada grupo se rellena con equipos mediante una consulta que defina o importando grupos de orígenes diferentes.  Cuando el grupo se incluye en una búsqueda de registros, los resultados se limitan a los registros que coincidan con los equipos en el grupo.

## <a name="creating-a-computer-group"></a>Crear un grupo de equipos
Puede crear un grupo de equipos en análisis de registro con cualquiera de los métodos de la tabla siguiente.  En las siguientes secciones se proporcionan detalles en cada método. 

| Método | Descripción |
|:---|:---|
| Búsqueda de registros       | Crear una búsqueda de registros que devuelve una lista de equipos y guardar los resultados como un grupo de equipos. |
| API de búsqueda de registros   | Use la API de búsqueda de registro para crear mediante programación un grupo de equipos en función de los resultados de búsqueda de registros. |
| Active Directory | Digitalizar automáticamente la pertenencia a grupos de los equipos agente que forman parte de un dominio de Active Directory y crear un grupo de análisis de registro para cada grupo de seguridad.
| WSUS              | Automáticamente examine servidores WSUS o sus clientes destinadas a grupos y crear un grupo en el análisis de registro para cada uno. |


### <a name="log-search"></a>Búsqueda de registros

Grupos de equipo creados a partir de una búsqueda de registro contendrá todos los equipos devueltos por una consulta de búsqueda que defina.  Esta consulta se ejecuta cada vez que se utiliza el grupo de equipo para que se reflejarán los cambios desde que se creó el grupo.

Use el procedimiento siguiente para crear un grupo de equipo de una búsqueda de registro.

1. [Crear una búsqueda de registros](log-analytics-log-searches.md) que devuelve una lista de equipos.  La búsqueda debe devolver un conjunto determinado de equipos mediante algo como **Equipos distintos** o **medida count() por equipo** en la consulta.  
2. Haga clic en el botón **Guardar** en la parte superior de la pantalla.
3. Seleccione **Sí** para **Guardar esta consulta como un grupo de equipos:**.
4. Escriba un **nombre** y una **categoría** para el grupo.  Si ya existe una búsqueda con el mismo nombre y categoría, se le pedirá sobrescribir.  Puede tener varias búsquedas con el mismo nombre en diferentes categorías. 

Siguientes son búsquedas de ejemplo que se puede guardar como un grupo de equipos.

    Computer="Computer1" OR Computer="Computer2" | distinct Computer 
    Computer=*srv* | measure count() by Computer

### <a name="log-search-api"></a>API de búsqueda de registros

Grupos de equipos creados con la API de búsqueda de registro están la misma que búsquedas creadas con búsqueda de registros.

Para obtener más información sobre cómo crear un grupo de equipos con la API de búsqueda de registro consulte [API de REST de búsqueda de registro de grupos de equipo en el análisis de registro](log-analytics-log-search-api.md#computer-groups).

### <a name="active-directory"></a>Active Directory

Cuando configura el análisis de registro para importar las pertenencias a grupos de Active Directory, analizará la pertenencia a grupos de los equipos de dominio unido con el agente OMS.  Se crea un grupo de equipo en el análisis de registro para cada grupo de seguridad de Active Directory y cada equipo se agrega a los grupos de equipo correspondientes a los grupos de seguridad que pertenecen.  Esta suscripción se actualiza continuamente cada 4 horas.  

Configurar el análisis de registro para importar los grupos de seguridad de Active Directory desde el menú de **Grupos de equipos** en la **configuración**de análisis de registro.  Seleccione **automatización** y **pertenencia a grupos de Active Directory de importación de equipos**.  No es necesaria realizar ninguna configuración adicional.

![Grupos de equipos desde Active Directory](media/log-analytics-computer-groups/configure-activedirectory.png)

Cuando se hayan importado grupos, el menú mostrará el número de equipos con pertenencia detectado y el número de grupos importados.  Puede hacer clic en cualquiera de estos vínculos para devolver los registros **ComputerGroup** con esta información.

### <a name="windows-server-update-service"></a>Servicio de actualización de Windows Server

Cuando configura el análisis de registro para importar las pertenencias a grupos WSUS, analizará la pertenencia a destinatarios de los equipos con el agente OMS.  Si está utilizando el cliente de destino, cualquier equipo que se conecta a OMS y forma parte de ningún WSUS dirige a grupos tendrá su pertenencia importó al análisis de registro. Si está utilizando el servidor de destino, la OMS agente debe instalarse en el servidor WSUS en orden de la información de pertenencia a un grupo que se importarán a OMS.  Esta suscripción se actualiza continuamente cada 4 horas. 

Configurar el análisis de registro para importar los grupos de seguridad de Active Directory desde el menú de **Grupos de equipos** en la **configuración**de análisis de registro.  Seleccione **Active Directory** y **pertenencia a grupos de Active Directory de importación de equipos**.  No es necesaria realizar ninguna configuración adicional.

![Grupos de equipos desde Active Directory](media/log-analytics-computer-groups/configure-wsus.png)

Cuando se hayan importado grupos, el menú mostrará el número de equipos con pertenencia detectado y el número de grupos importados.  Puede hacer clic en cualquiera de estos vínculos para devolver los registros **ComputerGroup** con esta información.

## <a name="managing-computer-groups"></a>Administrar grupos de equipos

Puede ver los grupos de equipos que se crearon a partir de una búsqueda de registros o la API de búsqueda de registro en el menú de **Grupos de equipos** en la **configuración**de análisis de registro.  Haga clic en la **x** en la columna **Quitar** para eliminar el grupo de equipos.  Haga clic en el icono **Ver miembros** de un grupo ejecutar la búsqueda de registros del grupo que devuelve a sus miembros. 

![Grupos de equipo guardada](media/log-analytics-computer-groups/configure-saved.png)

Para modificar un grupo, cree un nuevo grupo con la misma **categoría** y **nombre** para sobrescribir el grupo original.

## <a name="using-a-computer-group-in-a-log-search"></a>Uso de un grupo de equipo en una búsqueda de registro
Use la siguiente sintaxis para hacer referencia a un grupo de equipos en una búsqueda de registros.  Si tiene grupos de equipos con el mismo nombre en diferentes categorías necesario especificar que la **categoría** es opcional y solo. 

    $ComputerGroups[Category: Name]

Cuando se ejecuta una búsqueda, los miembros de los grupos de equipos incluidos en la búsqueda se resuelven en primer lugar.  Si el grupo se basa en una búsqueda de registros, que la búsqueda se ejecuta para devolver a los miembros del grupo antes de realizar la búsqueda de registros de nivel superior.

Grupos de equipos se usan normalmente con la cláusula **IN** en la búsqueda de registros como en el ejemplo siguiente.

    Type=UpdateSummary Computer IN $ComputerGroups[My Computer Group]

## <a name="computer-group-records"></a>Registros de grupo de equipo

Se crea un registro en el repositorio OMS para cada pertenencia a grupos de equipo creado a partir de Active Directory o WSUS.  Estos registros tienen un tipo de **ComputerGroup** y las propiedades de la tabla siguiente.  No se crean registros para los grupos de equipo basados en búsquedas de registro.

| (Propiedad) | Descripción |
|:--|:--|
| Tipo                | *ComputerGroup* |
| SourceSystem        | *SourceSystem*  |
| Equipo            | Nombre del equipo miembro. |
| Grupo               | Nombre del grupo. |
| GroupFullName       | Ruta de acceso completa del grupo como el origen y el nombre de origen.
| GroupSource         | Grupo de origen se recopiló desde. <br><br>Active Directory<br>WSUS<br>WSUSClientTargeting |
| GroupSourceName     | Nombre de la fuente que se ha recopilado los grupos.  De Active Directory, este es el nombre de dominio. |
| ManagementGroupName | Nombre del grupo de administración de agentes SCOM.  Para otros agentes, esto es AOI -\<identificador de área de trabajo\> |
| TimeGenerated       | Fecha y hora en el grupo de equipo se creó o se actualizó. |



## <a name="next-steps"></a>Pasos siguientes

- Obtenga información acerca de las [búsquedas de registro](log-analytics-log-searches.md) analizar los datos recopilados desde orígenes de datos y soluciones.  