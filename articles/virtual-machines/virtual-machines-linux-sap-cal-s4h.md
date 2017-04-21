<properties 
pageTitle="Implementar HANA HANA S/4 o BYN/4 en una máquina virtual de Azure | Microsoft Azure" 
description="Implementar S/4 HANA o HANA BYN/4 en una máquina virtual de Azure" 
services="virtual-machines-linux" 
documentationCenter="" 
authors="hermanndms" 
manager="timlt" 
editor="" 
tags="azure-resource-manager" 
  keywords=""/> 
<tags 
  ms.service="virtual-machines-linux" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.tgt_pltfrm="vm-linux" 
  ms.workload="infrastructure-services" 
  ms.date="09/15/2016" 
  ms.author="hermannd"/> 


# <a name="deploying-s4-hana-or-bw4-hana-on-microsoft-azure"></a>Implementar S/4 HANA o HANA BYN/4 en Microsoft Azure 

En este artículo se describe cómo implementar HANA S/4 en Microsoft Azure a través del dispositivo Library 3.0 de nube de SAP.
Las capturas de pantalla muestran el proceso paso a paso. Implementar otras soluciones basadas en SAP HANA como BYN/4 HANA funciona del mismo modo desde una perspectiva de proceso. Uno solo debe seleccionar una solución diferente.

Para empezar con biblioteca de dispositivo de nube de SAP (SAP CAL) vaya [aquí](https://cal.sap.com/). Hay una entrada de blog desde SAP sobre el nuevo [dispositivo Library 3.0 de nube de SAP](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 


Las capturas de pantalla siguientes enseñan paso a paso implementar HANA S/4 en Microsoft Azure. El proceso funciona de la misma forma por otras soluciones likeBW/4 HANA.


![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-1b.jpg)

La primera imagen muestra todas las soluciones basadas en SAP CAL HANA que están disponibles en Microsoft Azure.
Exemplarily la "SAP S/4 HANA local edición" (solución en la parte inferior de la captura de pantalla) se ha elegido para ir a través del proceso.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-2.jpg)

En primer lugar una cuenta nueva de SAP CAL tiene que crear. Hay dos opciones para Azure - estándar Azure y Azure en continente China que es a través de 21Vianet de partner.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic3b.jpg)

Uno tiene que especificar el identificador de suscripción Azure que se encuentra en el portal de Azure - Vea también más abajo cómo obtenerlo. Posteriormente es necesario descargar un certificado de administración de Azure.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic6b.jpg)

En el nuevo Azure uno portal busca el elemento "Suscripciones" en el lado izquierdo. Haga clic para mostrar todas las suscripciones activas para el usuario.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic7b.jpg)

Seleccionando una de las suscripciones y eligiendo "Administración de certificados" explica que hay es un nuevo concepto con "principales de servicio" para el nuevo modelo de administrador de recursos de Azure.
CAL de SAP no adaptado aún para este nuevo modelo y requiere que el modelo "clásico" y el portal de Azure antiguo para trabajar con los certificados de administración.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic4b.jpg)

Aquí se puede ver el portal de Azure antiguo. La carga de un certificado de administración le da SAP CAL los permisos para crear máquinas virtuales dentro de una suscripción de cliente. En la "suscripciones" pestaña uno puede encontrar el identificador de la suscripción que se pueden especificar en el portal de SAP CAL.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic5.jpg)

En la pestaña de la segunda, a continuación, es posible cargar el certificado de administración que descargó antes de CAL de SAP.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic8.jpg)

Aparecerá un pequeño cuadro de diálogo para seleccionar el archivo de certificado descargado.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic9.jpg)

Una vez que se cargó el certificado puede comprobar la conexión entre CAL de SAP y el cliente Azure suscripción dentro de SAP CAl. Aparecerá un pequeño mensaje que indica que la conexión es válida.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic10.jpg)

Después de la configuración de una cuenta de uno tiene que seleccionar una solución que se debe implementar y crea una instancia.
Con el modo "básico", es realmente trivial. Escriba un nombre de instancia, elija una región Azure y definir la contraseña maestra para la solución.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic11.jpg)

Después de un tiempo según el tamaño y la complejidad de la solución (una estimación se expresa por SAP CAL) se muestra como "activos" y listos para usarse. Es muy sencillo.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic12.jpg)

En algunos detalles de la solución uno puede ver qué tipo de VM se implementaron. En este caso se crearon tres máquinas virtuales de Azure de diferentes tamaños y finalidad.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic13.jpg)

Las máquinas virtuales pueden encontrarse en el portal de Azure, comenzando con el mismo nombre de instancia que se proporcionó CAL de SAP.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic14b.jpg)

Ahora es posible conectar con la solución mediante el botón conectar en el portal de SAP CAL. El cuadro de diálogo pequeño contiene un vínculo a una guía de usuario que describe todas las credenciales de forma predeterminada para que funcione con la solución.

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic15.jpg)

Otra opción es iniciar sesión en el cliente de máquina virtual de Windows e iniciar, por ejemplo, el gráfico de SAP preconfigurada.







