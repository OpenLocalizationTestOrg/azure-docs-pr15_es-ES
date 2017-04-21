<properties
  pageTitle="Con el DNS de Azure con otros servicios de Azure | Microsoft Azure"
  description="Descripción de cómo usar Azure DNS para resolver el nombre de otros servicios de Azure"
  services="dns"
  documentationCenter="na"
  authors="sdwheeler"
  manager="carmonm"
  editor=""
  tags="azure dns"
/>
<tags
  ms.service="dns"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
  ms.date="09/21/2016"
  ms.author="sewhee"
/>

# <a name="using-azure-dns-with-other-azure-services"></a>Con el DNS de Azure con otros servicios de Azure

Azure DNS es un servicio hospedado de DNS administración y el nombre de resolución. Esto le permite crear nombres DNS públicos para las otras aplicaciones y servicios que se ha implementado en Azure. Crear un nombre para un servicio de Azure en su dominio personalizado es tan simple como agregar un registro del tipo correcto para el servicio.

* Para las direcciones IP asignadas dinámicamente, debe crear un registro CNAME DNS que asigna el nombre de DNS Azure creado para el servicio. Estándares DNS impedirá usando un registro CNAME para el vértice de zona.
* Para las direcciones IP estática asignadas, puede crear un registro DNS A utilizando cualquier nombre, incluyendo un nombre de _dominio vacío_ en el vértice de zona.

La siguiente tabla describe los tipos de registro compatibles que se pueden usar para los distintos servicios de Azure. Como puede ver en esta tabla, Azure DNS solo es compatible con los registros DNS para recursos de la red a través de Internet. Azure DNS no se pueden usar para la resolución de nombres de direcciones internas y privadas.

| Servicio de Azure | Interfaz de red | Descripción |
|---------------|-------------------|-------------|
| Puerta de enlace de aplicación | IP pública front-end | Puede crear un registro DNS A o CNAME. |
| Equilibrador de carga | IP pública front-end | Puede crear un registro DNS A o CNAME. Equilibrador de carga puede tener una dirección IP pública de IPv6 que se asigna dinámicamente. Por consiguiente, debe crear un registro CNAME para una dirección IPv6. |
| Administrador de tráfico | Nombre público | Solo puede crear un CNAME que asigna el nombre de trafficmanager.net asignado a su perfil de administrador de tráfico. Para obtener más información, consulte [Administrador de tráfico cómo funciona](../traffic-manager/traffic-manager-how-traffic-manager-works.md#traffic-manager-example). |
| Servicio de nube | IP pública | Para las direcciones IP estática asignadas, puede crear un registro DNS A. Para las direcciones IP asignadas dinámicamente, debe crear un registro CNAME que asigna el nombre _cloudapp.net_ . Esta regla se aplica a máquinas virtuales que creó en el portal de clásico porque se implementan como un servicio de nube. Para obtener más información, consulte [configurar un nombre de dominio personalizado en servicios en la nube](../cloud-services/cloud-services-custom-domain-name-portal.md). |
| Aplicación de servicio | IP externa | Para las direcciones IP externas, puede crear un registro DNS A. En caso contrario, debe crear un registro CNAME que asigna el nombre azurewebsites.net. Para obtener más información, consulte [asignar un nombre de dominio personalizado para una aplicación de Azure](../app-service-web/web-sites-custom-domain-name.md) |
| Máquinas virtuales de administrador de recursos | IP pública | Máquinas virtuales de administrador de recursos puede tener direcciones IP públicas. También puede ser una máquina virtual con una dirección IP pública detrás de un equilibrador de carga. Puede crear un registro DNS A o CNAME para la dirección pública. Este nombre personalizado puede usarse para omitir a la dirección VIP en el equilibrador de carga. |
| Máquinas virtuales clásicas | IP pública | Máquinas virtuales clásicas creados con PowerShell o CLI puede configurarse con una dirección virtual (reservada) dinámica o estática. Puede crear un CNAME DNS o un registro, respectivamente. |
