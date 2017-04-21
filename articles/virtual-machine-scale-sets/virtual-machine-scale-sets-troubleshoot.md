<properties
    pageTitle="Autoescala con conjuntos de escala de máquina Virtual de solución de problemas | Microsoft Azure"
    description="Autoescala con conjuntos de escala de máquina Virtual de solución de problemas. Comprender los problemas típicos que puedan surgir y cómo resolverlos."
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="guybo"/>

# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Autoescala con conjuntos de escala de máquina Virtual de solución de problemas

**Problema** : que haya creado una infraestructura de ajuste automático en Azure Administrador de recursos con conjuntos de escala de VM: por ejemplo al implementar una plantilla similar a esta: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale – tiene las reglas de escala definidas y funciona bien, salvo que independientemente de la cantidad que coloca en las máquinas virtuales de carga, no será Autoescala.

## <a name="troubleshooting-steps"></a>Pasos de solución de problemas

Aspectos a considerar incluyen:

- ¿Cuántos núcleos tiene cada VM y, a continuación, se cargan cada núcleo?
 La plantilla de Azure tutorial de ejemplo anterior tiene una secuencia de comandos do_work.php, que carga un único núcleo. Si está usando una máquina virtual mayor de un solo núcleo tamaño VM como Standard_A1 o D1, a continuación, necesarias para ejecutar esta carga varias veces. Comprobar cuántos núcleos sus máquinas virtuales revisando [máquinas virtuales tamaños de Windows Azure](../virtual-machines/virtual-machines-windows-sizes.md)

- ¿Cuántas máquinas virtuales en el conjunto de escala de VM hacen el trabajo en cada uno de ellos?

    Un evento de escalado sólo producirá cuando el promedio CPU de **todas** las máquinas virtuales de un conjunto de escala supera el valor de umbral, durante el tiempo interno definido en las reglas de Autoescala.

- ¿Pierde los eventos de escala?

    Compruebe que los registros de auditoría en el portal de Azure eventos de escala. ¿Se ha producido una escala hacia arriba y una escala hacia abajo que se ha perdido. Puede filtrar por "Escala"..

    ![Registros de auditoría][audit]

- ¿Son los umbrales de escala y escalado lo suficientemente diferente?

    Suponga que establece una regla escalar cuando CPU promedio es mayor que 50% más de cinco minutos y a escala en cuando CPU promedio es menor que 50%. Esto haría que un problema "aleteo" cuando el uso de CPU está próximo a alcanzar este límite, con acciones de escala constantemente aumentar y disminuir el tamaño del conjunto. Por este motivo, el servicio Autoescala intenta evitar "batir", que puede manifiestos como no escala. Por lo tanto, asegúrese de que el escalado y umbrales de escala son suficientemente diferentes para permitir espacio entre el ajuste de escala.

- ¿Escribir su propia plantilla JSON?

    Es fácil cometer errores, así que comience con una plantilla como uno por encima del cual está comprobado que funcionan y hacer pequeños cambios incrementales. 

- ¿Puede manualmente escalar o de salida?

    Intente volver a implementar el recurso de conjunto de escala de máquina virtual con una configuración de "capacidad" diferente para cambiar el número de máquinas virtuales de forma manual. Un ejemplo de plantilla para realizar esta acción está aquí: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing-debe editar la plantilla para asegurarse de que tiene el mismo tamaño de equipo como el conjunto de escala está utilizando. Si correctamente puede cambiar manualmente el número de máquinas virtuales, sabrá que se aislará para ajustar automáticamente el problema.

- Compruebe su Microsoft.Compute/virtualMachineScaleSet y recursos de Microsoft.Insights en el [Explorador de recursos de Azure](https://resources.azure.com/)

    Esta es una herramienta de solución de problemas imprescindible que muestra el estado de los recursos de administrador de recursos de Azure. Haga clic en la suscripción y busque el grupo de recursos solucionar. En el proveedor de recursos de cálculo mirar el conjunto de escala de máquina virtual que creó y compruebe la vista instancia, que muestra el estado de una implementación. Compruebe también la vista de instancia de VM del conjunto de la escala de la máquina virtual. A continuación, vaya al proveedor de recursos de Microsoft.Insights y compruebe que las reglas de Autoescala tengan un buenas aspecto.

- ¿La extensión de diagnóstico funciona y emisión de los datos de rendimiento?

    __Actualización:__ Autoescala Azure se ha mejorado para usar una canalización de métricas basado en host que ya no necesita instalar una extensión de diagnósticos. Esto significa que la siguiente que varios párrafos ya no se aplican si crea una aplicación de ajuste automático mediante la canalización de nueva. Ejemplos de plantillas de Azure que se han convertido a usar la canalización de host: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale, https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale. 

    Usar métricas basado en host para Autoescala es mejor para las siguientes razones:

    - Menos partes móviles como sin extensiones de diagnósticos deben instalarse.
    - Plantillas más simples. Basta con agregar reglas de Autoescala perspectivas a una plantilla de conjunto de escala existente.
    - Creación de informes más fiable y el inicio rápido de nuevas máquinas virtuales.

    Las razones sola que desea seguir utilizando una extensión de diagnóstico sería si necesita memoria diagnósticos informes y ajuste de escala. Métricas basado en host no informan de memoria.

    Con esto en mente, siga el resto de este artículo solo si sigue usando las extensiones de diagnósticos para el ajuste automático.

    Autoescala en el Administrador de recursos de Azure puede trabajar (pero ya no tiene que) por medio de una máquina virtual extensión llamada la extensión de diagnósticos. Emite datos de rendimiento a una cuenta de almacenamiento que se define en la plantilla. A continuación, se agregan estos datos por el servicio de Monitor de Azure.

    Si el servicio de recomendaciones no puede leer los datos de las máquinas virtuales, se supone que enviar un correo electrónico, por ejemplo si las máquinas virtuales fueron hacia abajo, así que compruebe su correo electrónico (aquel que especificó al crear la cuenta de Azure).

    También puede ir y buscar en los datos. Busque la cuenta de almacenamiento de Azure con el Explorador de nube. Ejemplo usando el [Explorador de nube de Visual Studio](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), inicie sesión y seleccione la suscripción Azure está usando y el nombre de cuenta de almacenamiento de información de diagnóstico hace referencia en la definición de la extensión de diagnóstico en la plantilla de implementación...

    ![Explorador de nube][explorer]

    Aquí verá un conjunto de tablas donde se está almacenando los datos de cada máquina virtual. Tomar notas Linux y la métrica de CPU como ejemplo, busque filas las más recientes. El Explorador de nube de Visual Studio es compatible con un lenguaje de consulta para que pueda ejecutar una consulta como "datetime gt de marca de tiempo ' 2016-02-02T21:20:00Z'" para asegurarse de que recibe los eventos más recientes (suponga que es el tiempo en UTC). ¿Significa que los datos que se ven en allí corresponden a las reglas de escala configurar? En el ejemplo siguiente, la CPU del equipo 20 iniciado aumentado al 100% en los últimos 5 minutos...

    ![Tablas de almacenamiento][tables]

    Si los datos no están, que implica que el problema está relacionado con la extensión de diagnóstico que se ejecuta en las máquinas virtuales. Si los datos están allí, implica que hay un problema con las reglas de escala o con el servicio de perspectivas. Compruebe el [estado de Azure](https://azure.microsoft.com/status/).

    Una vez que ha sido estos pasos si sigue teniendo problemas para Autoescala podría pruebe en los foros de [MSDN](https://social.msdn.microsoft.com/forums/azure/home?category=windowsazureplatform%2Cazuremarketplace%2Cwindowsazureplatformctp)o [desbordamiento de pila](http://stackoverflow.com/questions/tagged/azure)o iniciar una llamada de soporte técnico. Prepárese compartir la plantilla y una vista de los datos de rendimiento.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
