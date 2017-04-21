<properties
   pageTitle="Implemente la oferta de Azure Marketplace | Microsoft Azure"
   description="Obtenga más información sobre y siga las instrucciones para implementar la oferta: imagen de máquina virtual, servicio de desarrollador, servicio de datos, etc.: a Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/02/2016"
   ms.author="hascipio" />

# <a name="deploy-your-offer-to-the-azure-marketplace"></a>Implemente la oferta de Azure Marketplace
Cuando esté satisfecho con la oferta (es decir, ha probado los escenarios de cliente, marketing contenido, etc.) y esté listo para iniciar, solicitar **poner en producción** en la pestaña **Publicar** .  

1. Los cuatro pasos en el tutorial de página en la publicación portal debe ser completado y verde. Para las ofertas de máquina Virtual, asegúrese de que se siguen las directrices siguientes.

    ![dibujo][img-pubportal-walkthru-checked]

2. Seleccione la pestaña **Publicar** de la lista en el lado izquierdo.

    ![dibujo][img-pubportal-menu-publish]

3. Haga clic en el botón **Solicitar aprobación para poner en producción**. Una vez realizada la solicitud, el equipo de aprobación ejecuta una revisión final y, a continuación, la oferta estará disponible en Azure Marketplace.

    ![dibujo][img-pubportal-publish-pushproduction]

>[AZURE.IMPORTANT] En el caso de máquinas virtuales, al hacer clic en el botón aprobación de solicitud de poner en producción, los pasos siguientes se realizan detrás de la escena. Podrá ver el progreso de cada paso en la pestaña publicar en la publicación en el portal. Debe comprobar esta página a intervalos regulares (hasta que el estado muestra "Listada") para cualquier información de error que necesita corrección desde el final.

> - En primer lugar la solicitud de producción va al equipo de certificación que validar el disco duro virtual. Sin embargo, si está actualizando su oferta ya enumerado y la solicitud tiene solo cambio de marketing, se omite el paso de certificación.
> - En el paso siguiente, la solicitud de proceder al equipo de validación del contenido que compruebe el contenido de marketing de la oferta.
> - Si los pasos anteriores están correcta, se aprueba la oferta en producción. En este momento, el estado se convierten en "muestra" en el portal de publicación. Sin embargo, este estado "Listada" implica que el proceso esté completo. Los pasos siguientes deben estar completa antes de la oferta está disponible en Azure Marketplace.
> - Una vez que se apruebe la oferta de producción, en el paso anterior, inicie la replicación de la oferta a través de todos los centros de datos de Azure. Generalmente toma 24 48hours para la replicación completar, pero puede tardar hasta una semana según el tamaño. Sin embargo, si está actualizando su oferta ya enumerado y tiene solo cambio de marketing, la replicación es más rápida.
> - Una vez completada la replicación, a continuación, la oferta estará disponible en Azure Marketplace.

> Siempre puede eliminar la oferta mientras está en un estado de **Borrador** (es decir, nunca **Push a ensayo** o **inserción a producción**). En la pestaña de **historial** , haga clic en el botón **Descartar borrador** en la parte inferior de la página para eliminar un borrador.


## <a name="production-checklist-for-all-virtual-machine-offers"></a>Lista de comprobación de producción para todas las ofertas de máquina Virtual

- Asegúrese de que sea un socio certificado de Microsoft Azure
- En la pestaña SKU, la opción "ocultar esta SKU de Marketplace porque siempre deben ser comprado a través de una plantilla de solución" debe marcarse como sí solo si la SKU es parte de una plantilla de soluciones. En todos los demás casos, esta opción siempre debe marcarse como NO.
- Recuerde: No debe cambiar la configuración de visibilidad SKU una vez que se muestra el SKU. No se admite esta funcionalidad.
- Asegúrese de que los logotipos se ajustan a las instrucciones del logotipo de Azure Marketplace indicadas a continuación.
- Descripción de la oferta y SKU no debe ser la misma.
- Título SKU y ofrecer larga resumen no deben ser la misma.
- Título de SKU y ofrecer resumen no deben ser la misma.
- Títulos de SKU no deben ser idénticos en una oferta con varias SKU.

**Directrices de logotipo de Azure Marketplace**

- El diseño de Azure tiene una paleta de colores simple. Mantenga el número de primaria y secundaria colores en el logotipo baja.
- Los colores del tema del portal de Azure están en blancos y negro. Por lo tanto, evite usar estos colores como el color de fondo de su logotipos. Use algunas color que haga su logotipos destacada en el portal de Azure. Se recomienda simples colores primarios. Si está utilizando un fondo transparente, asegúrese de que el logotipo y texto no es blanco y negro.
- No utilice un fondo degradado en el logotipo.
- Evite colocar el texto, incluso la empresa o el nombre de la marca, en el logotipo.
- La apariencia de un logotipo debe ser 'plana' y evite degradados.
- No debe ajustarse el logotipo.

**Instrucciones adicionales para el logotipo de imagen:**

- El logotipo de la imagen es opcional. El editor puede elegir no cargar un logotipo de la imagen. **Sin embargo una vez cargado el icono de imagen no se puede eliminar la publicación de portal. En ese momento, el partner debe seguir las instrucciones de Azure Marketplace para la oferta no se aprobará más iconos de imagen para producción.**
- El nombre de presentación de Publisher, título SKU y la oferta resumen cuánto tiempo se muestran en el color de fuente blanco. Por lo tanto, debe evitar mantener cualquier color claro en el fondo del icono de imagen. Negro, fondo blanco y transparente no está permitido de los iconos de imagen.
- El Editor Mostrar nombre, el título SKU, la oferta resumen cuánto tiempo y el botón Crear incrustados mediante programación dentro del logotipo de héroe una vez que la oferta va enumerada. Así que no debe escribir cualquier texto al diseñar el logotipo de la imagen. Simplemente dejar espacios en blanco en la parte derecha, porque el texto (es decir, nombre para mostrar de Publisher, el título SKU, la oferta cuánto tiempo resumen) se incluirán mediante programación por nosotros sobre allí. El espacio en blanco para el texto debe ser 415 x 100 a la derecha (y desplazamiento por 370px desde la izquierda).


## <a name="additional-production-checklist-for-already-listed-virtual-machine-offers"></a>Lista de comprobación de producción adicionales para ya enumerados Máquina Virtual ofrece

- Compruebe si ya hay una oferta con el mismo nombre de la oferta de su empresa. En caso afirmativo, deberá agregar una nueva versión de la SKU de la oferta existente en lugar de crear una nueva oferta duplicada.
- Disco de datos no debe cambiar entre dos versiones de la misma SKU.
- Azure Marketplace no admite el cambio de precios de la lista SKU puesto que afecta a la facturación de los clientes existentes. Asegúrese de no cambiar los precios de las SKU mostradas en las regiones donde está disponible el SKU. Sin embargo, puede agregar nuevas SKU o agregar nuevas áreas a un SKU existente.


## <a name="next-steps"></a>Pasos siguientes
Una vez que la oferta queda directo, pruebe los escenarios de cliente para validar todas las funcionalidades y contratos funcionan correctamente en el entorno de producción como probado y validar en el entorno de ensayo.

## <a name="see-also"></a>Vea también
- [Introducción: cómo publicar una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]:media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]:media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]:media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png
