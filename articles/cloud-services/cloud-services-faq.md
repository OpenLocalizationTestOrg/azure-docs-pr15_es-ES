<properties
    pageTitle="Preguntas más frecuentes sobre servicios de la nube | Microsoft Azure"
    description="Preguntas más frecuentes sobre servicios en la nube."
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="adegeo"/>

# <a name="cloud-services-faq"></a>Preguntas más frecuentes sobre los servicios de nube
En este artículo se da respuesta a algunas preguntas más frecuentes sobre servicios de nube de Microsoft Azure. También puede visitar las [P+F de soporte técnico de Azure](http://go.microsoft.com/fwlink/?LinkID=185083) para información general de soporte técnico y precios de Azure. También puede consultar la [página de tamaño de memoria virtual de servicios de nube](cloud-services-sizes-specs.md) para obtener información de tamaño.

## <a name="certificates"></a>Certificados

### <a name="where-should-i-install-my-certificate"></a>¿Dónde debo instalar mi certificado?

- **Mi**  
Certificado de aplicación con clave privada (\*.pfx, \*. p12).

- **ENTIDAD EMISORA DE CERTIFICADOS**  
Todos los certificados intermedios vaya en este almacén (directiva y entidades emisoras de certificados de Sub).

- **RAÍZ**  
Almacén de la entidad emisora de certificados raíz, para que el certificado de entidad emisora de certificados raíz principal debe ir aquí.

### <a name="i-cant-remove-expired-certificate"></a>No puedo quitar certificado caducado

Azure impide quitar un certificado mientras está en uso. Debe eliminar la implementación que utiliza el certificado, o actualizar la implementación con un certificado diferente o renovado.

### <a name="delete-an-expired-certificate"></a>Eliminar un certificado caducado

Como el certificado no está en uso, puede usar el cmdlet de PowerShell [AzureCertificate quitar](https://msdn.microsoft.com/library/azure/mt589145.aspx) para quitar un certificado.

### <a name="i-have-expired-certificates-named-windows-azure-service-management-for-extensions"></a>He caducado denominados administración de servicio de Windows Azure para extensiones de certificados

Estos certificados se crean cuando se agrega una extensión al servicio de nube como la extensión de escritorio remoto. Estos certificados sólo se utilizan para cifrar y descifrar la configuración privada de la extensión. No importa si estos certificados expiren. No se comprueba la fecha de vencimiento.

### <a name="certificates-i-have-deleted-keep-reappearing"></a>Continúan apareciendo los certificados que he eliminado

Siguen apareciendo probablemente debido a una herramienta que está usando, como Visual Studio. Cada vez que se conecte de nuevo con una herramienta que está utilizando un certificado, se cargarán nuevamente en Azure.

### <a name="my-certificates-keep-disappearing"></a>Mantienen desaparezca mis certificados

Cuando la instancia de máquina virtual recicla, se pierden todos los cambios locales. Use una [tarea de inicio](cloud-services-startup-tasks.md) para instalar certificados en la máquina virtual cada vez que inicie el rol.

### <a name="i-cannot-find-my-management-certificates-in-the-portal"></a>No encuentro mis certificados de administración en el portal

[Certificados de administración](..\azure-api-management-certs.md) solo están disponibles en el Portal de clásico de Azure. El portal de Azure actual no utiliza certificados de administración. 

### <a name="how-can-i-disable-a-management-certificate"></a>¿Cómo se puede deshabilitar un certificado de administración?

No se puede deshabilitar [certificados de administración](..\azure-api-management-certs.md) . Elimina a través del Portal de clásico de Azure cuando no desea que se utilizarán ya.

### <a name="how-do-i-create-an-ssl-certificate-for-a-specific-ip-address"></a>¿Cómo puedo crear un certificado SSL para una dirección IP específica?

Siga las instrucciones de la [crear un tutorial de certificado](cloud-services-certs-create.md). Usar la dirección IP como el nombre de DNS.

## <a name="security"></a>Seguridad

### <a name="disable-ssl-30"></a>Deshabilitar SSL 3.0

Para deshabilitar 3.0 de SSL y TLS la seguridad, cree una tarea de inicio que documentado en esta entrada de blog: https://azure.microsoft.com/en-us/blog/how-to-disable-ssl-3-0-in-azure-websites-roles-and-virtual-machines/

## <a name="scale-a-cloud-service"></a>Ajustar el tamaño de un servicio de nube

### <a name="i-cannot-scale-beyond-x-instances"></a>No se puede ajustar más allá de X instancias

Su suscripción de Azure tiene un límite del número de núcleos que puede usar. Ajuste de escala no funcionará si ha utilizado las muestras disponibles. Por ejemplo, si tiene un límite de 100 núcleos, esto significa podría tener 100 instancias de máquina virtual de A1 con el tamaño de su servicio de nube o A2 50 tamaño instancias de máquina virtual.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>No se puede reservar una dirección IP en un servicio de nube de múltiples VIP

En primer lugar, asegúrese de que la instancia de máquina virtual que intenta reservar la dirección IP de está activada. En segundo término, asegúrese de que esté usando direcciones IP reservadas para molestarse en implementaciones de ensayo y producción. **No** cambiar la configuración mientras está actualizando la implementación.

