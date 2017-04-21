
<properties
    pageTitle="Novedades en RemoteApp de Azure | Microsoft Azure"
    description="Obtenga más información sobre los cambios y mejoras de RemoteApp de Azure"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="whats-new-in-azure-remoteapp"></a>Novedades en RemoteApp de Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

Una de las ventajas de RemoteApp de Azure es que siempre estamos trabajando para mejorarlo. Cada vez que se hace, se le anunciar esos cambios aquí.

## <a name="future-updates"></a>Futuras actualizaciones
Hola - ¿sabía que el equipo de Azure RemoteApp publicaciones actualizaciones mensuales en el blog RDS? Puede encontrar no solo novedades cambio en Azure RemoteApp, sino también otra información sobre cómo utilizar RDS. Consulte su blog, [Blog de servicios de escritorio remoto](https://blogs.msdn.microsoft.com/rds/), para obtener más información. Por ejemplo, un par de hace semanas, que publicó una entrada sobre [levantar y desplazar las cargas de trabajo con RemoteApp de Azure y Azure AD](https://blogs.msdn.microsoft.com/rds/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services/).
 
## <a name="september-2015"></a>Septiembre de 2015
- Infopath agregada a la imagen de plantilla y Galería de Microsoft Office 365. Si desea compartir Infopath, asegúrese de actualizar las colecciones con la imagen más reciente.
- Actualizaciones de cliente:
    - Cliente de Windows actualizado para permitir a los usuarios para compartir comentarios, especialmente alrededor de los problemas de conexión.
    - cliente de iOS actualizado para corregir el error de mensajería y solucionar un problema donde sus credenciales caducado antes de lo esperado.
- Estamos trabajando para obtener soporte técnico de Office 2016 probado. Una vez que haya finalizado, busque imágenes actualizadas.
- Publicar un nuevo artículo acerca de las [diferencias entre colecciones de nube y híbrido](remoteapp-collections.md) - Esto le ayudará a elegir el tipo de colección que funciona mejor para sus aplicaciones - solo nube, nube + VNET o híbrido.
- ¿Desea compartir QuickBooks usando RemoteApp de Azure, pero no está seguro de los pasos? Consulte [artículo de Carlos](remoteapp-quickbooks.md) que indica que lo que desea hacer.

## <a name="august-2015"></a>Agosto del de 2015
Ha ocurrido cambios importantes en agosto - presentamos los aspectos destacados:

- ¡Ahora puede utilizar un VNET de Azure con una colección de nube! Consulte las [instrucciones de creación de nube](remoteapp-create-cloud-deployment.md) para ver los pasos nuevos.
- ¿Hace posible agregar aplicaciones al menú **Inicio **para el cliente de RemoteApp de Windows. Aplicaciones aparecerá en la lista de aplicaciones y, a continuación, puede anclar al menú **Inicio **de Windows.
- Agrega una nueva imagen a la Galería de Azure VM - Windows Server escritorio remoto con Microsoft Office 365 ProPlus.
- Fija al cliente de Mac para que aplicaciones con windows modales se detendrán inmovilizar.
- Describe cómo puede usar la [suscripción a Office 365 ProPlus](remoteapp-officesubscription.md) con RemoteApp de Azure.
- Detalla cómo puede [proteger las aplicaciones y los datos](remoteapp-secure.md) de la colección de RemoteApp de Azure.

## <a name="july-2015"></a>Julio de 2015

Julio establece la etapa de cambios procedentes de agosto, por lo que no hay una gran cantidad de hablar sobre ahora, principalmente las actualizaciones del documento. Estos son los cambios más recientes:

- Agrega una ficha **compatibilidad** en el portal para que pueda acceder más fácilmente los recursos de soporte técnico, como los foros.
- Rehacer la información de solución de problemas para crear una colección híbrido. Consulte [la última y mejor](remoteapp-hybridtrouble.md) sugerencias como cómo identificar los puertos correctos para configurar para su VNET para resolver problemas.
- Documenta cómo se creado y guardado en Azure RemoteApp los [datos de usuario](remoteapp-upd.md) .
- Documenta cómo [bloqueo de aplicaciones](remoteapp-secure.md).
- Publicar los [cmdlets de RemoteApp de Azure](https://msdn.microsoft.com/library/mt428031.aspx).
- Y por último, se inicia una conversación con algunos usuarios de Azure RemoteApp sobre terminología. Buscar cambios en la forma que nos referimos a las opciones de recopilación diferente.

## <a name="june-2015"></a>Junio de 2015

¡Tantos cambios! El equipo ha sido muy ocupado en junio:

- Rediseñado de RemoteApp de Azure que [página de inicio](https://www.remoteapp.windowsazure.com/) - desprotegerlo!
- Actualizar el software en todas las imágenes disponibles como parte de su suscripción.
- Realizar mejoras en colecciones de híbridos, incluidos forzado túnel soporte técnico y comprobar el tamaño de subred IP antes de intentar crear la colección.
- Descubre que el * comodín no funciona para cámaras. En su lugar, debe especificar el identificador de la instancia o GUID. Se va a actualizar la información de redirección para reflejar.
- Hecho para que pueda agregar software antivirus personalizado a la imagen cuando se crea una imagen de la plantilla de la Galería de Azure.

Tenemos más cambios distribuir en julio, por lo que vamos a ser con otra actualización pronto.

## <a name="may-2015"></a>Mayo de 2015

Ha habido un número de adiciones (y meses) desde que se crea por primera vez este tema, por lo que esta lista trampas un poco y desde el principio de marzo a través de mayo. Consulte estas nuevas características:

- Automatizar todo: RemoteApp de Azure tiene ahora [cmdlets en el módulo de PowerShell de Azure](remoteapp-tutorial-arawithpowershell.md).
- [Crear una imagen de Azure RemoteApp desde una máquina virtual Azure](remoteapp-image-on-azurevm.md). Hace que cargar la imagen personalizada en Azure mucho más rápido.
- Usar un VNET de Azure en lugar de un VNET RemoteApp para conectarse a los recursos de la red corporativa de Azure. Hemos actualizado las [instrucciones de la colección de híbrido](remoteapp-create-hybrid-deployment.md) para mostrarle la creación de un VNET de Azure (es el paso 1).
- Hablando de VNETs, consulte [la Guía de nueva](remoteapp-vnetsizing.md) alrededor de los límites de tamaño VNET y limitaciones.
- ¿Y hablando de límites - simplemente cuáles son los [valores predeterminados y límites de servicio](../azure-subscription-service-limits.md)?

¿Desea obtener más información acerca de RemoteApp de Azure? El equipo de RemoteApp fue fuera vigentes en Ignite hace unas semanas. Consulte vídeo de Carlos, [los conceptos básicos de Microsoft Azure RemoteApp de administración](http://channel9.msdn.com/Events/Ignite/2015/BRK3868).

¿Necesita ver RemoteApp de Azure en el mundo real? Consulte el tutorial de [ejecutar cualquier aplicación en cualquier dispositivo en cualquier lugar](remoteapp-anyapp.md) , muestra cómo compartir el acceso con los usuarios, incluidos los archivos de base de datos de uso compartido. También tenemos un tutorial en [hacer que Office 365](remoteapp-tutorial-o365anywhere.md) ejecutar la misma en cualquier dispositivo.

Gracias por se conserva con nosotros: mes siguiente con las actualizaciones más anterior.


### <a name="help-us-help-you"></a>Ayúdenos a ayudarle
¿Sabía que, además de valoración de este artículo y realizar comentarios hacia abajo, a continuación, puede realizar cambios en el artículo? ¿Falta algo? ¿Algún error? ¿Escribe algo que es confuso simplemente? Desplazarse hacia arriba y haga clic en **Editar en GitHub** para realizar cambios: los vaya a nosotros para revisión y, a continuación, una vez se aprobar en ellas, verá los cambios y mejoras aquí.
