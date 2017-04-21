<properties
   pageTitle="Administrar conjuntos de registros de DNS y los registros con el portal de Azure | Microsoft Azure"
   description="Administración de DNS registro establece y registros cuando su dominio en Azure DNS de host."
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="manage-dns-records-and-record-sets-by-using-the-azure-portal"></a>Administrar registros DNS y conjuntos de registros mediante el portal de Azure


> [AZURE.SELECTOR]
- [Portal de Azure](dns-operations-recordsets-portal.md)
- [CLI de Azure](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


Este artículo le muestra cómo administrar los registros de la zona DNS y conjuntos de registros mediante el portal de Azure.

Es importante comprender la diferencia entre los conjuntos de registros de DNS y los registros DNS individuales. Un conjunto de registros es una colección de registros de una zona que tienen el mismo nombre y el mismo tipo. Para obtener más información, consulte [registros mediante el portal de Azure y conjuntos de registros de DNS de crear](dns-getstarted-create-recordset-portal.md).

## <a name="create-a-new-record-set-and-record"></a>Crear un nuevo conjunto de registros y registro

Para crear un registro establecido en el portal de Azure, vea [crear registros DNS a través del portal de Azure](dns-getstarted-create-recordset-portal.md).


## <a name="view-a-record-set"></a>Ver un conjunto de registros

1. En el portal de Azure, vaya a la hoja de **zona DNS** .

2. Busque el conjunto de registros y selecciónelo. Esto abre las propiedades del conjunto de registros.

    ![Buscar un conjunto de registros](./media/dns-operations-recordsets-portal/searchset500.png)


## <a name="add-a-new-record-to-a-record-set"></a>Agregar un nuevo registro a un conjunto de registros

Puede agregar hasta 20 registros a cualquier conjunto de registros. Un conjunto de registros no puede contener dos registros idénticos. Conjuntos de registros vacíos (con cero registros) se pueden crear, pero no aparecen en los servidores de nombres DNS de Azure. Conjuntos de registros de tipo CNAME pueden contener un registro como máximo.


1. En el módulo de **registro de establece las propiedades** de la zona DNS, haga clic en el conjunto de registros que desea agregar un registro a.

    ![Seleccione un conjunto de registros](./media/dns-operations-recordsets-portal/selectset500.png)

2. Especificar que el registro del conjunto de propiedades rellenando los campos.

    ![Agregar un registro](./media/dns-operations-recordsets-portal/addrecord500.png)

2. Haga clic en **Guardar** en la parte superior de la hoja para guardar la configuración. A continuación, cierre el módulo.

3. En la esquina, verá que está guardando el registro.

    ![Guardar conjunto de registros.](./media/dns-operations-recordsets-portal/saving150.png)

Después de guardar el registro, los valores en el módulo de **zona DNS** reflejarán el nuevo registro.


## <a name="update-a-record"></a>Actualizar un registro

Cuando se actualiza un registro en un conjunto de registros existente, los campos que se puede actualizar varían en función del tipo de registro que está trabajando con.

1. En el módulo de **registro de establece las propiedades** para el conjunto de registros, busque el registro.

2. Modificar el registro. Cuando se modifica un registro, puede cambiar la configuración disponible para el registro. En el ejemplo siguiente, se selecciona el campo **dirección IP** y la dirección IP se está modificando.

    ![Modificar un registro](./media/dns-operations-recordsets-portal/modifyrecord500.png)

3. Haga clic en **Guardar** en la parte superior de la hoja para guardar la configuración. En la esquina superior derecha, verá la notificación que se ha guardado el registro.

    ![Guarda el conjunto de registros](./media/dns-operations-recordsets-portal/saved150.png)


Después de guardar el registro, los valores para el registro que se establece en el módulo de **zona DNS** reflejará el registro actualizado.


## <a name="remove-a-record-from-a-record-set"></a>Quitar un registro de un conjunto de registros

Puede usar el portal de Azure para quitar registros de un conjunto de registros. Tenga en cuenta que el último registro de un conjunto de registros no se elimina el conjunto de registros.

1. En el módulo de **registro de establece las propiedades** para el conjunto de registros, busque el registro.

2. Haga clic en el registro que desea quitar. A continuación, seleccione **Quitar**.

    ![Quitar un registro](./media/dns-operations-recordsets-portal/removerecord500.png)

3. Haga clic en **Guardar** en la parte superior de la hoja para guardar la configuración.

3. Una vez se ha eliminado el registro, los valores para el registro en el módulo de **zona DNS** reflejarán la eliminación.


## <a name="delete"></a>Eliminar un conjunto de registros

1. En el **registro de configuración de propiedades de** módulo para el registro conjunto, haga clic en **Eliminar**.

    ![Eliminar un conjunto de registros](./media/dns-operations-recordsets-portal/deleterecordset500.png)

2. Muestra un mensaje preguntándole si desea eliminar el conjunto de registros.

3. Compruebe que el nombre coincide con el conjunto de registros que desea eliminar y, a continuación, haga clic en **Sí**.

4. En el módulo de **zona DNS** , compruebe que el conjunto de registros ya no está visible.


## <a name="work-with-ns-and-soa-records"></a>Trabajar con registros NS y SOA

Administran los registros NS y SOA que se crean automáticamente diferente de otros tipos de registros.

### <a name="modify-soa-records"></a>Modificar los registros de inicio de autoridad

No puede agregar o quitar registros desde el conjunto en el vértice de zona de registros de inicio de autoridad creado automáticamente (nombre = "@"). Sin embargo, puede modificar cualquiera de los parámetros en el registro de inicio de autoridad (excepto "Host") y el registro establece TTL.

### <a name="modify-ns-records-at-the-zone-apex"></a>Modificar los registros del servidor DNS en el vértice de zona

No puede agregar, para quitar o modificar los registros en el conjunto en el vértice de zona de registros de NS creado automáticamente (nombre = "@"). Es el único cambio que está permitido modificar el conjunto de registros TTL.

### <a name="delete-soa-or-ns-record-sets"></a>Eliminar conjuntos de registros de inicio de autoridad o NS

No se puede eliminar el inicio de autoridad y registro NS se establece en el vértice de zona (nombre = "@") que se crean automáticamente cuando se crea la zona. Se eliminan automáticamente cuando se elimina la zona.

## <a name="next-steps"></a>Pasos siguientes

-   Para obtener más información acerca de DNS de Azure, consulte la [Introducción a Azure DNS](dns-overview.md).
-   Para obtener más información sobre la automatización de DNS, vea [zonas DNS de creación y registro establece mediante .NET SDK](dns-sdk.md).
-   Para obtener más información acerca de los registros DNS inversos, consulte [cómo administrar los registros DNS inversos de los servicios con PowerShell](dns-reverse-dns-record-operations-ps.md).
