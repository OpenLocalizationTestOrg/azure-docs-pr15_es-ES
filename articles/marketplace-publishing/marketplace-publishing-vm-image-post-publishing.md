<properties
   pageTitle="Administración de la imagen de la máquina virtual en el catálogo de soluciones de Azure | Microsoft Azure"
   description="Guía detallada sobre cómo administrar su imagen de máquina virtual de Azure Marketplace después de la publicación inicial."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="08/03/2016"
   ms.author="hascipio;"/>

# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Guía de producción posteriores a la máquina virtual ofertas de Azure Marketplace

En este artículo se explica cómo puede actualizar una oferta de máquina Virtual directo de Azure Marketplace. También le guiará en el proceso de agregar uno o más nuevas SKU a una oferta existente y quitar una oferta de máquina Virtual o SKU directo de Azure Marketplace.

Una vez que se traslada un SKU de oferta en el [Portal de Azure](http://portal.azure.com), no puede cambiar los campos que se indican a continuación:

- **Ofrecen identificador:** [Portal de publicación -> máquinas virtuales -> seleccionar su oferta -> ficha imágenes VM -> ofrecen identificador]
- **Identificador SKU:** [Portal de publicación -> máquinas virtuales -> seleccionar su oferta -> SKU pestaña -> agrega un SKU]
- **Publisher Namespace:** [Portal de publicación -> máquinas virtuales -> Tutorial pestaña -> información nos sobre la empresa (encontrados en "Paso 2 registrar su empresa") -> Publisher Namespace -> Namespace]

Una vez que la SKU de oferta se enumera en el [Catálogo de soluciones de Azure](http://azure.microsoft.com/marketplace), no puede cambiar los campos que se indican a continuación:

- **Ofrecen identificador:** [Portal de publicación -> máquinas virtuales -> seleccionar su oferta -> ficha imágenes VM -> ofrecen identificador]
- **Identificador SKU:** [Portal de publicación -> máquinas virtuales -> seleccionar su oferta -> SKU pestaña -> agrega un SKU]
- **Publisher Namespace:** [Portal de publicación -> máquinas virtuales -> Tutorial -> pestaña saber nos acerca de su empresa (encontrados en paso 2 registrar) Publisher Namespace -> Namespace]
- **Puertos** [Portal de publicación -> máquinas virtuales -> seleccionar su oferta -> ficha imágenes VM -> Abrir puertos]
- **Cambio de precios de lista SKU(s)**
- **Cambio de modelo de SKU(s) enumerados de facturación**
- **Eliminación de regiones de SKU(s) enumerados de facturación**
- **Cambiar el número de disco de datos de lista SKU(s)**



## <a name="1-how-to-update-the-technical-details-of-a-sku"></a>1. cómo actualizar los detalles técnicos de un SKU

Puede agregar una versión nueva a la lista SKU y volver a publicar su oferta siguiendo los pasos que se indican a continuación:

1. Inicie sesión en el [portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **máquinas virtuales de Windows** y seleccione la oferta.
3. En el menú de lado izquierdo, haga clic en la ficha **Imágenes de máquina virtual** .
4. En la sección de **SKU** de la ficha **VM imágenes** , busque la SKU que desea actualizar.
5. Después de eso, agregar un nuevo número de versión de la SKU y haga clic en el botón **"+"** . La nueva versión debe tener el formato X.Y.Z donde X, Y Z son números enteros. Cambios de versión solo debería incrementales.
6. En el cuadro **Dirección URL del disco duro virtual de sistema operativo** , agregar la firma de acceso compartido que URI creado para el sistema operativo del disco duro virtual y guarde los cambios.

    >[AZURE.IMPORTANT] No se puede aumentar o disminuir el número de disco de datos de una lista SKU. Tiene que crear un nuevo SKU en este caso. Consulte la sección [3. Cómo agregar un nuevo SKU en una oferta de lista](#3-how-to-add-a-new-sku-under-a-live-offer) para obtener instrucciones detalladas.

7. Después de realizar los cambios, vaya a la pestaña **Publicar** y haga clic en el botón **De INSERCIÓN a la prueba**. Para obtener instrucciones detalladas sobre cómo probar su oferta en el entorno de ensayo, consulte este [vínculo](marketplace-publishing-vm-image-test-in-staging.md)
8. Después de probar su oferta en ensayo, vaya a la pestaña **Publicar** en la publicación portal y haga clic en el botón **Solicitar aprobación a PUSH a producción** para volver a publicar la oferta en Azure Marketplace.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku"></a>2. cómo actualizar los detalles de una oferta o un SKU técnicos

Puede actualizar la técnica (marketing, legal, soporte, categorías) detalles de la oferta live o SKU de Azure Marketplace.

### <a name="21-update-the-offer-description-and-logos"></a>2.1 actualizar la descripción de la oferta y logotipos

Puede actualizar los detalles de la oferta y volver a publicar su oferta siguiendo los pasos siguientes:

1. Inicie sesión en el [portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **máquinas virtuales de Windows** y seleccione la oferta.
3. En el menú de lado izquierdo, haga clic en la pestaña **MARKETING** .
4. Haga clic en el botón de **Inglés (Estados Unidos)** .
5. En el menú de lado izquierdo, haga clic en la ficha **Detalles** . En la sección *Descripción* de la ficha **Detalles** puede actualizar el título de la oferta, ofrecer resumen, ofrecer resumen larga y guardar los cambios.

    >[AZURE.NOTE] Por favor, tenga cuidado de las siguientes acciones mientras está actualizando los detalles SKU.
    **No escriba texto duplicado en la descripción de la oferta y la descripción de SKU. No escriba texto duplicado en el título SKU y la oferta cuánto tiempo resumen. No escriba texto duplicado en el título SKU y el resumen de la oferta.**

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)

6. En la sección *logotipos* de la pestaña **Detalles** , puede actualizar los logotipos. Sin embargo, asegúrese de que los logotipos siguen las [directrices de Azure Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (consulte la sección paso 1: contenido de marketing de proporcionar Marketplace -> detalles -> directrices de Azure Marketplace logotipo).

    >[AZURE.NOTE] Icono de imagen es opcional. Puede elegir no cargar un icono de imagen. Sin embargo, una vez que se carga el icono de imagen, a continuación, no es necesario para eliminar la publicación de portal. En ese caso, debe seguir las [instrucciones del icono de imagen](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (consulte la sección paso 1: contenido de marketing de proporcionar Marketplace -> detalles -> instrucciones adicionales para la pancarta de logotipo héroe).

7. Después de realizar los cambios, vaya a la pestaña **Publicar** y haga clic en el botón **De INSERCIÓN a la prueba**. Para obtener instrucciones detalladas sobre cómo probar su oferta en el entorno de ensayo, consulte este [vínculo](marketplace-publishing-vm-image-test-in-staging.md).
8. Después de probar su oferta en ensayo, vaya a la pestaña **Publicar** en la publicación portal y haga clic en el botón **Solicitar aprobación a PUSH a producción** para volver a publicar la oferta en Azure Marketplace.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="22-update-the-sku-description"></a>2.2. Actualizar la descripción de SKU

Puede actualizar los detalles SKU y volver a publicar su oferta siguiendo los pasos siguientes:

1. Inicie sesión en el [portal de publicación](https://publish.windowsazure.com)
2. Vaya a la pestaña **máquinas virtuales de Windows** y seleccione la oferta.
3. En el menú de lado izquierdo, haga clic en la pestaña **MARKETING** .
4. Haga clic en el botón de **Inglés (Estados Unidos)** .
5. En el menú de lado izquierdo, haga clic en la pestaña **planes** . En la sección *SKU* de la pestaña **planes** puede actualizar el título SKU, SKU resumen y detalles de descripción SKU y guardar los cambios.

    >[AZURE.NOTE] Por favor, tenga cuidado de las siguientes acciones mientras está actualizando los detalles SKU. **No escriba texto duplicado en la descripción de la oferta y la descripción de SKU. No escriba texto duplicado en el título de la SKU y la oferta cuánto tiempo resumen. No escriba texto duplicado en el título de SKU y el resumen de la oferta.**

6. Después de realizar los cambios, vaya a la pestaña **Publicar** y haga clic en el botón **De INSERCIÓN a la prueba**. Para obtener instrucciones detalladas sobre cómo probar su oferta en el entorno de ensayo, consulte este vínculo
7. Después de probar su oferta en ensayo, vaya a la pestaña **Publicar** en la publicación portal y haga clic en el botón **Solicitar aprobación a PUSH a producción** para volver a publicar la oferta en Azure Marketplace.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="23-change-the-existing-links-or-add-new-links"></a>2.3 cambiar los vínculos existentes o agregar nuevos vínculos

Puede cambiar los vínculos existentes o agregar nuevos vínculos y, a continuación, volver a publicar su oferta siguiendo los pasos siguientes:

1. Inicie sesión en el [portal de publicación](https://publish.windowsazure.com)
2. Vaya a la pestaña **máquinas virtuales de Windows** y seleccione la oferta.
3. En el menú de lado izquierdo, haga clic en la pestaña **MARKETING** .
4. Haga clic en el botón de **Inglés (Estados Unidos)** .
5. En el menú de lado izquierdo, haga clic en la pestaña de **vínculos** .
6. Si desea agregar un vínculo nuevo, en los *vínculos* de la sección haga clic en el botón **Agregar vínculo** . Se abrirá el cuadro de diálogo *"Agregar vínculo"* . En este cuadro de diálogo, puede agregar el vínculo del título y la dirección URL de campos y guardar los cambios. Puede introducir cualquier vínculo que contiene información que puede ayudar a los clientes.
7. Si desea actualizar o eliminar un vínculo existente, seleccione el vínculo correspondiente y haga clic en el botón Editar o en el botón Eliminar en consecuencia.

    >[AZURE.NOTE] Asegúrese de que los vínculos que ha introducido en esta sección funcionan correctamente, como obtengan validados estos vínculos durante el proceso de solicitud de producción.

8. Después de realizar los cambios, vaya a la pestaña **Publicar** y haga clic en el botón **De INSERCIÓN a la prueba**. Para obtener instrucciones detalladas sobre cómo probar su oferta en el entorno de ensayo, consulte este [vínculo](marketplace-publishing-vm-image-test-in-staging.md).
9. Después de probar su oferta en ensayo, vaya a la pestaña **Publicar** en la publicación portal y haga clic en el botón **Solicitar aprobación a PUSH a producción** para volver a publicar la oferta en Azure Marketplace.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="24-change-an-existing-sample-image-or-add-a-new-sample-image"></a>2,4 cambiar una imagen de ejemplo existente o agregar una nueva imagen de ejemplo

Puede cambiar las imágenes de ejemplo existentes o agregar nuevas imágenes de ejemplo y, a continuación, volver a publicar su oferta siguiendo los pasos siguientes:

>[AZURE.NOTE] Imagen de solo ejemplo se muestra en la [https://portal.azure.com](https://portal.azure.com).

1. Inicie sesión en el [portal de publicación](https://publish.windowsazure.com)
2. Vaya a la pestaña **máquinas virtuales de Windows** y seleccione la oferta.
3. En el menú de lado izquierdo, haga clic en la pestaña **MARKETING** .
4. Haga clic en el botón de **Inglés (Estados Unidos)** .
5. En el menú de lado izquierdo, haga clic en la pestaña de **Imágenes de ejemplo** .
6. Si desea agregar una nueva imagen de ejemplo, en la sección de *Imágenes de ejemplo* haga clic en el botón **Cargar una nueva imagen** y, a continuación, guarde los cambios.

    >[AZURE.NOTE] La inclusión de una imagen de ejemplo es un paso opcional.

7. Si desea actualizar o eliminar una imagen de ejemplo existente, a continuación, busque la imagen de ejemplo adecuada y, a continuación, haga clic en el botón **Reemplazar imagen** o el botón Eliminar en consecuencia.

8. Después de realizar los cambios, vaya a la pestaña **Publicar** y haga clic en el botón **De INSERCIÓN a la prueba**. Para obtener instrucciones detalladas sobre cómo probar su oferta en el entorno de ensayo, consulte este [vínculo](marketplace-publishing-vm-image-test-in-staging.md).
9. Después de probar su oferta en ensayo, vaya a la pestaña **Publicar** en la publicación portal y haga clic en el botón **Solicitar aprobación a PUSH a producción** para volver a publicar la oferta en Azure Marketplace.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="25-update-the-legal-content"></a>2.5 actualización de el contenido legal

Puede actualizar el contenido legal y volver a publicar su oferta siguiendo los pasos siguientes:

1. Inicie sesión en el [portal de publicación](https://publish.windowsazure.com)
2. Vaya a la pestaña **máquinas virtuales de Windows** y seleccione la oferta.
3. En el menú de lado izquierdo, haga clic en la pestaña **MARKETING** .
4. Haga clic en el botón de **Inglés (Estados Unidos)** .
5. En el menú del lado izquierdo, haga clic en la ficha **LEGAL** . En la sección *Legal* puede actualizar sus directivas/condiciones de uso. Escriba o pegue los términos de directivas en el cuadro de texto de *Términos de uso* y guardar los cambios.
6. El límite de caracteres para los términos de uso legales es 1.000.000 caracteres.
7. Después de realizar los cambios, vaya a la pestaña **Publicar** y haga clic en el botón **De INSERCIÓN a la prueba**. Para obtener instrucciones detalladas sobre cómo probar su oferta en el entorno de ensayo, consulte este [vínculo](marketplace-publishing-vm-image-test-in-staging.md)
8. Después de probar su oferta en ensayo, vaya a la pestaña **Publicar** en la publicación portal y haga clic en el botón **Solicitar aprobación a PUSH a producción** para volver a publicar la oferta en Azure Marketplace.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="26-update-the-support-information"></a>2.6 actualizar la información de soporte técnico

Puede actualizar la información de soporte técnico y volver a publicar su oferta siguiendo los pasos siguientes:

1. Inicie sesión en el [portal de publicación](https://publish.windowsazure.com)
2. Vaya a la pestaña **máquinas virtuales de Windows** y seleccione la oferta.
3. En el menú de lado izquierdo, haga clic en la ficha **compatibilidad** .
4. En la sección *Ingeniería de contacto* de la ficha **compatibilidad** puede actualizar los detalles de contacto. Estos detalles se usan para la comunicación interna entre el partner y Microsoft solo.
5. En la sección de *Asistencia al cliente* de la ficha **compatibilidad** puede actualizar los detalles de contacto de soporte técnico como **nombre, el correo electrónico, el teléfono** y la **Dirección URL de soporte técnico**. Estos detalles se usan para la comunicación interna entre el partner y Microsoft solo.

    >[AZURE.NOTE] Si desea proporcionar solo asistencia por correo electrónico, proporcione un número de teléfono ficticias en la sección de **Asistencia al cliente** . En este caso, el correo electrónico proporcionado se utilizará en su lugar.

6. Después de realizar los cambios, vaya a la pestaña **Publicar** y haga clic en el botón **De INSERCIÓN a la prueba**. Para obtener instrucciones detalladas sobre cómo probar su oferta en el entorno de ensayo, consulte este [vínculo](marketplace-publishing-vm-image-test-in-staging.md)
7. Después de probar su oferta en ensayo, vaya a la pestaña **Publicar** en la publicación portal y haga clic en el botón **Solicitar aprobación a PUSH a producción** para volver a publicar la oferta en Azure Marketplace.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="27-update-the-categories"></a>2.7 actualizar las categorías

Puede actualizar la sección de categorías para la oferta y volver a publicar su oferta siguiendo los pasos siguientes:

1. Inicie sesión en el [portal de publicación](https://publish.windowsazure.com)
2. Vaya a la pestaña **máquinas virtuales de Windows** y seleccione la oferta.
3. En el menú de lado izquierdo, haga clic en la ficha **categorías** .
4. En la sección *categorías* puede actualizar las categorías para la oferta y guardar los cambios. Puede seleccionar un máximo de cinco categorías para la Galería de Azure Marketplace.
5. Después de realizar los cambios, vaya a la pestaña **Publicar** y haga clic en el botón **De INSERCIÓN a la prueba**. Para obtener instrucciones detalladas sobre cómo probar su oferta en el entorno de ensayo, consulte este [vínculo](marketplace-publishing-vm-image-test-in-staging.md)
6. Después de probar su oferta en ensayo, vaya a la pestaña **Publicar** en la publicación portal y haga clic en el botón **Solicitar aprobación a PUSH a producción** para volver a publicar la oferta en Azure Marketplace.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="3-how-to-add-a-new-sku-under-a-listed-offer"></a>3. cómo agregar un nuevo SKU en una oferta de lista

Puede agregar un nuevo SKU en su oferta en directo, siguiendo los pasos que se indican a continuación:

1. Inicie sesión en el [portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **máquinas virtuales de Windows** y seleccione la oferta.
3. En el menú de lado izquierdo, haga clic en la pestaña **SKU** . Después de que haga clic en el botón **Agregar un SKU**.  Se abrirá un cuadro de diálogo nuevo. Escriba un identificador SKU en minúsculas. Active la casilla de verificación traer sus propias facturación model(BYOL) si desea publicar la SKU de nueva con el modelo de facturación de BYOL. En caso contrario, desactive la casilla de verificación para BYOL. Después de que haga clic en la marca de graduación en el cuadro de diálogo para crear un nuevo SKU. Si no optar por el modelo de facturación de BYOL para la SKU de nueva, a continuación, el modelo de facturación se establece automáticamente en cada hora para la SKU de nueva. Si desea habilitar la versión de prueba gratuita de 30days para cada hora modelo de facturación, a continuación, haga clic en la opción "Un mes" de "es una prueba gratuita disponible?". En caso contrario, seleccione "Sin prueba". [Nota: la opción "es una prueba gratuita disponible?" se muestra únicamente si no ha seleccionado BYOL en el cuadro de diálogo Crear nuevas SKU.]

    >[AZURE.IMPORTANT] La opción "Ocultar esta SKU de Marketplace porque siempre deben ser comprado a través de una plantilla de solución" debe ser marcados como "Sí" solo si están aprobadas para la publicación de una oferta de plantilla de solución de Azure Marketplace. En caso contrario, esta opción siempre debe marcarse como "NO".

4. Ahora en el menú de lado izquierdo, haga clic en la ficha **Imágenes de máquina virtual** y averigüe el nuevo SKU que ha creado.
5. Para configurar el nuevo SKU, consulte el paso 5 de este [vínculo](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) para obtener instrucciones.
6. Para agregar el material de marketing para la SKU de nuevo, consulte la sección paso 1: contenido de marketing de proporcionar Marketplace -> detalles -> números de punto de 2 a 5 de este [vínculo](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Para agregar la información de precios para la SKU de nueva, consulte la sección 2.1. Establecer los precios de máquina virtual de este [vínculo](marketplace-publishing-push-to-staging.md#step-2-set-your-prices)
8. Después de realizar los cambios, vaya a la pestaña **Publicar** y haga clic en el botón **De INSERCIÓN a la prueba**. Para obtener instrucciones detalladas sobre cómo probar su oferta en el entorno de ensayo, consulte este [vínculo](marketplace-publishing-vm-image-test-in-staging.md)
9. Después de probar su oferta en ensayo, vaya a la pestaña **Publicar** en la publicación portal y haga clic en el botón **Solicitar aprobación a PUSH a producción** para volver a publicar la oferta en Azure Marketplace.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="4-how-to-change-the-data-disk-count-for-a-listed-sku"></a>4. cómo cambiar el número de una lista SKU discos de datos

No se puede aumentar o disminuir el número de disco de datos de una lista SKU. En este caso debe crear un nuevo SKU. Consulte la sección [3. Cómo agregar un nuevo SKU en una oferta de live](#3-how-to-add-a-new-sku-under-a-live-offer) para obtener instrucciones detalladas.

## <a name="5---how-to-delete-a-listed-offer-from-the-azure-marketplace"></a>5. cómo eliminar una oferta de lista de Azure Marketplace

Existen varios aspectos que deben tenerse en cuenta en caso de una solicitud para quitar una oferta directo. Siga los pasos siguientes para obtener instrucciones desde el equipo de soporte técnico para quitar una oferta de lista de Azure Marketplace:

1.  Subir una incidencia de soporte técnico con este [vínculo](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681)
2.  Seleccione el tipo de problema como **"Administrar ofertas"** y categoría como **"Modificación de una oferta o SKU ya en producción"**
3.  Enviar la solicitud

El equipo de soporte técnico le guiará a través del proceso de eliminación de oferta o SKU.

>[AZURE.NOTE] Siempre puede eliminar la oferta mientras está en un estado de borrador (es decir, no en provisional o producción) haciendo clic en el botón **Descartar borrador** en la pestaña **historial** .

## <a name="6-how-to-delete-a-listed-sku-from-the-azure-marketplace"></a>6. cómo eliminar una lista SKU de Azure Marketplace

Puede eliminar un SKU mostrado de Azure Marketplace, siguiendo los pasos que se indican a continuación:

1. Inicie sesión en el [portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **máquinas virtuales de Windows** y seleccione la oferta.
3. En el panel lateral izquierda, haga clic en la pestaña **SKU** .
4. Seleccione el SKU que desea eliminar y haga clic en el botón Eliminar contra esa SKU.
5. Cuando haya terminado, vaya a la pestaña publicar en la publicación portal y haga clic en el botón **Solicitar aprobación a PUSH a producción** para volver a publicar la oferta de Azure Marketplace.
6. Una vez que vuelva a obtiene publicada la oferta en Azure Marketplace, se eliminará el SKU de Azure Marketplace y el Portal de Azure.

## <a name="7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace"></a>7. cómo eliminar la versión actual de una lista SKU de Azure Marketplace

Puede eliminar la versión actual de una lista SKU de Azure Marketplace, siguiendo los pasos que se indican a continuación. Una vez finalizado el proceso, el SKU volverá a su versión anterior.

1. Inicie sesión en el [portal de publicación](https://publish.windowsazure.com).
2.  Vaya a la pestaña **máquinas virtuales de Windows** y seleccione la oferta.
3.  Desde el panel de lado izquierdo, haga clic en la ficha **Imágenes de máquina virtual** .
4.  Seleccione el SKU cuya versión actual que desea eliminar y haga clic en el botón Eliminar frente a esa versión.
5.  Cuando haya terminado, vaya a la pestaña **Publicar** en la publicación portal y haga clic en el botón **Solicitar aprobación a PUSH a producción** para volver a publicar la oferta de Azure Marketplace.
6.  Una vez que vuelva a obtiene publicada la oferta en Azure Marketplace, se eliminará la versión actual de la lista SKU de Azure Marketplace y el Portal de Azure. El SKU volverá a su versión anterior.

## <a name="8-how-to-revert-listing-price-to-production-values"></a>8. cómo revertir precio de lista de valores de producción
He cambiado los precios de un SKU mostrado (o bien, he quitado las regiones de facturación de una lista SKU). No se admite en Azure Marketplace, quiero revertir los cambios a los valores de producción. ¿Cómo conseguir?

Siga los pasos que se indican a continuación:

1. Inicie sesión en el [portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **máquinas virtuales de Windows** y seleccione la oferta.
3. En el menú de lado izquierdo, haga clic en la pestaña de **precios** .
4. En la ficha precios, seleccione una región cuyos precios que desea restablecer.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)

5. En caso de SKU con cada hora modelo facturación, restablecer los precios de todas las muestras que aparecen en la producción para la región seleccionada. Para SKU con el modelo de facturación de BYOL, disponer el SKU en la región activando la casilla de verificación contra el SKU en la sección disponibilidad de SKU EXTERNALLY-LICENSED (BYOL) (consulte la siguiente captura de pantalla).

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)

6. Ahora haga clic en el botón **AUTOPRICE otros mercados basándose en los precios en estadounidense**.

    >[AZURE.NOTE] La etiqueta del botón puede ser diferente dependiendo de la región que ha seleccionado. Puesto que hemos seleccionado Estados Unidos durante la creación de este documento, así que el botón tiene la etiqueta como "Otros mercados en función de los precios en Estados Unidos precio automática" en la siguiente captura de pantalla.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)

7. Se abrirá el Asistente de precio automático. La primera página muestra la selección de mercado base. Asegúrese de la sección y desplazarse a la página siguiente haciendo clic en el botón **"->"** .

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)

8. Opción para seleccionar los planes y núcleos se mostrará en la página 2. Seleccione el plan que quiera y las muestras y haga clic en "->" botón.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)

9. Página 3 muestra las regiones de mercados. Haga clic en el botón Alternar todas para seleccionar todas las regiones o manualmente Active las casillas de región. Haga clic en el botón "->" para ir a la página siguiente.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)

10. Página 4 muestra los tipos de cambio. Haga clic en el botón Finalizar para completar los pasos. El Asistente restablecerá los precios según la selección.

11. Ahora, vaya a la pestaña precio y haga clic en el botón "Ver resumen y cambios".
Seleccione "Borrador" en la sección "Vista versión" y "Producción" en la sección "Comparar con" (consulte la siguiente captura de pantalla). Si no ve ninguna diferencia de precio, implica precios han vuelto a los valores de producción correctamente.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)

12. Después de realizar los cambios, vaya a la pestaña publicar y haga clic en el botón **De INSERCIÓN a la prueba**. Para obtener instrucciones detalladas sobre cómo probar su oferta en el entorno de ensayo, consulte este [vínculo](marketplace-publishing-vm-image-test-in-staging.md)
13. Después de probar su oferta en ensayo, vaya a la pestaña publicar en la publicación portal y haga clic en el botón **Solicitar aprobación a PUSH a producción** para volver a publicar la oferta en Azure Marketplace.

## <a name="9-how-to-revert-billing-model-to-production-values"></a>9. cómo revertir el modelo de facturación para valores de producción
He cambiado el modelo de facturación de una lista SKU. No se admite en Azure Marketplace, quiero revertir los cambios a los valores de producción. ¿Cómo conseguir?

Siga los pasos siguientes:

1. Inicie sesión en el [portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **máquinas virtuales de Windows** y seleccione la oferta.
3. En el menú de lado izquierdo, haga clic en la pestaña **SKU** .
4. Haga clic en el botón Editar para revertir el modelo de facturación. Se abrirá una ventana. Active o desactive la casilla de verificación **'facturación y licencias se realiza externamente de Azure (también conocido como Traer su propia licencia)'** según corresponda.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)

5. Una vez, consulte la respuesta de la pregunta 8 en este documento para volver atrás los precios.
6. Después de que vaya a la pestaña **Publicar** en la publicación en el portal y la inserción de la oferta al ensayo para probarlo. Una vez haya terminado con la oferta de pruebas y luego haga clic en el botón **Solicitar aprobación a PUSH a producción** para volver a publicar la oferta en Azure Marketplace.

## <a name="10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value"></a>10. cómo revertir la configuración de visibilidad de un SKU mostrado en el valor de producción

Siga los pasos siguientes:

1. Inicie sesión en el [portal de publicación](https://publish.windowsazure.com).
2. Vaya a la pestaña **máquinas virtuales de Windows** y seleccione la oferta.
3. En el menú de lado izquierdo, haga clic en la pestaña **SKU** .
4. Seleccione los SKU y revertir la configuración de visibilidad de la SKU para el valor de producción.

    ![dibujo](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)

5. Una vez haya terminado con los cambios y luego haga clic en el botón **Solicitar aprobación a PUSH a producción** para volver a publicar la oferta en Azure Marketplace.

## <a name="see-also"></a>Vea también
- [Introducción: Cómo publicar una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)
- [Descripción de perspectivas vendedor informes](marketplace-publishing-report-seller-insights.md)
- [Descripción de los informes de pago](marketplace-publishing-report-payout.md)
- [Cómo cambiar su incentivos distribuidor de proveedor de soluciones de nube](marketplace-publishing-csp-incentive.md)
- [Solución de problemas comunes de publicación en el catálogo de soluciones](marketplace-publishing-support-common-issues.md)
- [Obtener soporte técnico como un editor](marketplace-publishing-get-publisher-support.md)
- [Crear una imagen de máquina virtual local](marketplace-publishing-vm-image-creation-on-premise.md)
- [Crear una máquina virtual ejecuta Windows en el portal de vista previa de Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
