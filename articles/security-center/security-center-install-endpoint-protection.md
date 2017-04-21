<properties
   pageTitle="Instalar Endpoint Protection en Centro de seguridad de Azure | Microsoft Azure"
   description="Este documento muestra cómo implementar la recomendación del centro de seguridad de Azure **Instalar Endpoint Protection**."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# <a name="install-endpoint-protection-in-azure-security-center"></a>Instalar Endpoint Protection en Centro de seguridad de Azure

Centro de seguridad de Azure se recomienda aprovisionar un programa antimalware a los equipos virtuales Azure (VM) si antimalware ya no está habilitado. Esta recomendación se aplica solo a máquinas virtuales de Windows.

> [AZURE.NOTE] Este documento presenta el servicio mediante el uso de una implementación de ejemplo.  No es una guía paso a paso.

## <a name="implement-the-recommendation"></a>Implementar la recomendación

1. En el módulo de **recomendaciones** , seleccione **Instalar Endpoint Protection**.
![Seleccione instalar Endpoint Protection][1]

2. El módulo de **Instalar Endpoint Protection** abrirá una lista de máquinas virtuales sin antimalware habilitado. En la lista, seleccione las máquinas virtuales que desea instalar antimalware en y haga clic en **instalar en máquinas virtuales**.
![Seleccione VM instalar antimalware en][2]

3. Se abre el módulo **Seleccione Endpoint Protection** para que pueda seleccionar la solución antimalware que desea usar. En este ejemplo, seleccionaremos **Antimalware de Microsoft**.
![Seleccione Endpoint Protection][3]

4. Se muestra información adicional sobre la solución antimalware. Seleccione **crear**.
![Crear solución antimalware][4]

5. Escriba los valores de configuración necesarios en el módulo de **Extensión de agregar** y, a continuación, seleccione **Aceptar**. Para obtener más información acerca de las opciones de configuración, vea [predeterminadas y configuración de Antimalware personalizada](../security/azure-security-antimalware.md#default-and-custom-antimalware-configuration).

Ahora está activo en las VM seleccionadas [Antimalware de Microsoft](../azure-security-antimalware.md) .

## <a name="see-also"></a>Vea también

En este artículo le mostramos cómo implementar la recomendación del centro de seguridad "Instalar Endpoint Protection". Para obtener más información acerca de cómo habilitar un programa antimalware en Azure, consulte lo siguiente:

- [Antimalware de Microsoft para servicios en la nube y máquinas virtuales](../azure-security-antimalware.md) : Obtenga información sobre cómo implementar antimalware de Microsoft.

Para obtener más información sobre el centro de seguridad, consulte lo siguiente:

- [Configurar directivas de seguridad en el centro de seguridad de Azure](security-center-policies.md) : Obtenga información sobre cómo configurar directivas de seguridad.
- [Recomendaciones de seguridad de la administración de centro de seguridad de Azure](security-center-recommendations.md) : Descubra cómo recomendaciones ayudar a proteger los recursos de Azure.
- [Supervisión del estado de seguridad del centro de seguridad de Azure](security-center-monitoring.md) : Obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md) : Obtenga información sobre cómo administrar y responder a las alertas de seguridad.
- [Las soluciones de asociados de supervisión con el centro de seguridad de Azure](security-center-partner-solutions.md) : Obtenga información sobre cómo supervisar el estado de las soluciones de socios.
- [Preguntas más frecuentes sobre centro de seguridad de azure](security-center-faq.md) --buscar con frecuencia preguntas más frecuentes sobre el uso del servicio.
- Entradas de [blog de seguridad de azure](http://blogs.msdn.com/b/azuresecurity/) --buscar blog sobre cumplimiento y la seguridad de Azure.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/select-install-endpoint-protection.png
[2]:./media/security-center-install-endpoint-protection/install-endpoint-protection-blade.png
[3]:./media/security-center-install-endpoint-protection/select-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/create-antimalware-solution.png
