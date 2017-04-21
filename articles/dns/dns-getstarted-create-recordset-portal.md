<properties
   pageTitle="Crear un conjunto de registros y registros de una zona de DNS con el portal de Azure | Microsoft Azure"
   description="Cómo crear registros de host DNS de Azure y crear conjuntos de registros y los registros con el portal de Azure"
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



# <a name="create-dns-record-sets-and-records-by-using-the-azure-portal"></a>Crear conjuntos de registros de DNS y registros mediante el portal de Azure


> [AZURE.SELECTOR]
- [Portal de Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [CLI de Azure](dns-getstarted-create-recordset-cli.md)


En este artículo le guiará por el proceso de creación de registros y conjuntos de registros mediante el portal de Azure. Después de crear la zona DNS, agregue los registros DNS para su dominio. Para ello, debe comprender los registros DNS y los conjuntos de registros.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]


## <a name="create-a-record-set-and-record"></a>Crear un conjunto de registros y registro

En el ejemplo siguiente, se le guiará por el proceso de creación de un conjunto de registros y el registro a través del portal de Azure. Usaremos el tipo de registro de DNS "A".

1. Inicie sesión en el portal.

2. Vaya a la hoja de **zona DNS** en el que desea crear un conjunto de registros.

3. En la parte superior de la hoja de **zona DNS** , seleccione el **conjunto de registros** para abrir el módulo de **registro de agregar la configuración** .

    ![Nuevo conjunto de registros.](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. En el **conjunto de registros de agregar** el módulo, asigne un nombre a su conjunto de registros. Por ejemplo, podría denominar el conjunto de registros "**www**".

    ![Agregar conjunto de registros.](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. Seleccione el tipo de registro que desea crear. Por ejemplo, seleccione **A**.

6. Establecer el **TTL**. La hora predeterminada Live en el portal es una hora.

7. Agregue las direcciones IP, una dirección IP por línea. Cuando se utilizan el nombre de conjunto de registros sugerido y el tipo de registro que se ha descrito anteriormente, agregue las direcciones IP de IPv4 para **el registro para el conjunto de registros de www** .

8. Cuando termine de agregar las direcciones IP, seleccione **Aceptar** en la parte inferior de la hoja. Se creará el conjunto de registros de DNS.


## <a name="next-steps"></a>Pasos siguientes

Para administrar el conjunto de registros y los registros, consulte [Administrar DNS records y registro se establece mediante el portal de Azure](dns-operations-recordsets-portal.md).

Para obtener más información acerca de DNS de Azure, consulte la [Introducción a Azure DNS](dns-overview.md).
