<properties 
pageTitle="Implementar SAP IDE EHP7 SP3 para SAP ERP 6.0 en Microsoft Azure | Microsoft Azure" 
description="Implementar SAP IDE EHP7 SP3 para SAP ERP 6.0 en Microsoft Azure" 
services="virtual-machines-windows" 
documentationCenter="" 
authors="hermanndms" 
manager="timlt" 
editor="" 
tags="azure-resource-manager" 
keywords=""/> 
<tags 
ms.service="virtual-machines-windows" 
ms.devlang="na" 
ms.topic="article" 
ms.tgt_pltfrm="vm-windows" 
ms.workload="infrastructure-services" 
ms.date="09/16/2016" 
ms.author="hermannd"/> 


# <a name="deploying-sap-ides-ehp7-sp3-for-sap-erp-60-on-microsoft-azure"></a>Implementar SAP IDE EHP7 SP3 para SAP ERP 6.0 en Microsoft Azure 

En este artículo se describe cómo implementar IDE de SAP que se ejecutan con SQL Server y el sistema operativo Windows en Microsoft Azure a través del dispositivo Library 3.0 de nube de SAP. Las capturas de pantalla muestran el proceso paso a paso. Implementar otras soluciones en la lista funciona del mismo modo desde una perspectiva de proceso. Uno solo debe seleccionar una solución diferente.

Para empezar con biblioteca de dispositivo de nube de SAP (SAP CAL) vaya [aquí](https://cal.sap.com/). Hay una entrada de blog desde SAP sobre el nuevo [dispositivo Library 3.0 de nube de SAP](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 


Las capturas de pantalla siguientes enseñan paso a paso implementar IDE de SAP en Microsoft Azure. El proceso funciona de la misma forma por otras soluciones.


![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

La primera imagen muestra todas las soluciones que están disponibles en Microsoft Azure. El resaltado se ha elegido la solución IDE basado en Windows SAP que solo está disponible en Azure para ir a través del proceso.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic2.jpg)

En primer lugar una cuenta nueva de SAP CAL tiene que crear. Hay dos opciones para Azure - estándar Azure y Azure en continente China que es a través de 21Vianet de partner.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic3.jpg)

Uno tiene que especificar el identificador de suscripción Azure que se encuentra en el portal de Azure - Vea también más abajo cómo obtenerlo. Posteriormente es necesario descargar un certificado de administración de Azure.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic6.jpg)

En el nuevo Azure uno portal busca el elemento "Suscripciones" en el lado izquierdo. Haga clic para mostrar todas las suscripciones activas para el usuario.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic7.jpg)

Seleccionando una de las suscripciones y eligiendo "Administración de certificados" explica que hay es un nuevo concepto con "principales de servicio" para el nuevo modelo de administrador de recursos de Azure.
CAL de SAP no adaptado aún para este nuevo modelo y requiere que el modelo "clásico" y el portal de Azure antiguo para trabajar con los certificados de administración.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic4.jpg)

Aquí se puede ver el portal de Azure antiguo. La carga de un certificado de administración le da SAP CAL los permisos para crear máquinas virtuales dentro de una suscripción de cliente. En la "suscripciones" pestaña uno puede encontrar el identificador de la suscripción que se pueden especificar en el portal de SAP CAL.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic5.jpg)

En la pestaña de la segunda, a continuación, es posible cargar el certificado de administración que descargó antes de CAL de SAP.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic8.jpg)

Aparecerá un pequeño cuadro de diálogo para seleccionar el archivo de certificado descargado.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic9.jpg)

Una vez que se cargó el certificado puede comprobar la conexión entre CAL de SAP y el cliente Azure suscripción dentro de SAP CAl. Aparecerá un pequeño mensaje que indica que la conexión es válida.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic10.jpg)

Después de la configuración de una cuenta de uno tiene que seleccionar una solución que se debe implementar y crea una instancia.
Con el modo "básico", es realmente trivial. Escriba un nombre de instancia, elija una región Azure y definir la contraseña maestra para la solución.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic11.jpg)

Después de un tiempo según el tamaño y la complejidad de la solución (una estimación se expresa por SAP CAL) se muestra como "activos" y listos para usarse. Es muy sencillo.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic12.jpg)

En algunos detalles de la solución uno puede ver qué tipo de VM se implementaron. En este caso, no hay una única VM de Azure de tamaño D12 creado por CAL de SAP.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic13.jpg)

En el portal de Azure, se puede encontrar la máquina virtual comenzando con el mismo nombre de instancia que se proporcionó CAL de SAP.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic14.jpg)

Ahora es posible conectar con la solución mediante el botón conectar en el portal de SAP CAL. El cuadro de diálogo pequeño contiene un vínculo a una guía de usuario que describe todas las credenciales de forma predeterminada para que funcione con la solución.
[Aquí](https://caldocs.hana.ondemand.com/caldocs/help/Getting_Started_Guide_IDES607MSSQL.pdf) es el vínculo a la guía para la solución de IDE.

![](./media/virtual-machines-windows-sap-cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Otra opción es iniciar sesión en la máquina virtual de Windows e iniciar, por ejemplo, el gráfico de SAP preconfigurada.





