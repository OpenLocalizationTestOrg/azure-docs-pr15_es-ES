<properties
   pageTitle="Supervisar los recursos de solución de auditoría y de seguridad del conjunto de aplicaciones de administración de operaciones | Microsoft Azure"
   description="Este documento le ayuda a usar OMS seguridad y capacidad para supervisar los recursos e identificar problemas de seguridad de auditoría."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.topic="article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="monitoring-resources-in-operations-management-suite-security-and-audit-solution"></a>Supervisar los recursos de seguridad del conjunto de aplicaciones de administración de operaciones y solución de auditoría

Este documento le ayuda a utilizar seguridad OMS y las capacidades de auditoría para supervisar los recursos e identificar problemas de seguridad.

## <a name="what-is-oms"></a>¿Qué es OMS?

Suite de administración de operaciones de Microsoft (OMS) es la nube de Microsoft basado en solución de administración de TI que le ayuda a administrar y proteger su local y la infraestructura de nube. Para obtener más información acerca de OMS, lea el artículo de la [Serie de administración de operaciones](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="monitoring-resources"></a>Supervisar los recursos

Siempre que sea posible, desea evitar que las incidencias de seguridad ocurra en primer lugar. Sin embargo, es posible evitar que todas las incidencias de seguridad. Cuando se produce un incidente de seguridad, debe asegurarse de que su impacto está minimizado.  Hay tres recomendaciones importantes que se pueden usar para minimizar el número y el impacto de las incidencias de seguridad:

- Evaluar la vulnerabilidad de su entorno de forma regular.
- Comprobar todos los equipos y dispositivos de red para asegurarse de que tiene todas las revisiones más recientes instaladas con regularidad.
- Comprobar todos los registros y mecanismos de registro, incluidos los registros de eventos de sistema operativo, registros específicos de aplicaciones y registros del sistema de detección de intrusiones con regularidad.

Seguridad de OMS y auditoría solución permite a TI para supervisar la actively todos los recursos, que le ayudarán a minimizar el impacto de las incidencias de seguridad. Seguridad de OMS y auditoría tiene dominios de seguridad que se pueden usar para supervisar los recursos. Los dominios de seguridad ofrece un acceso rápido a una opciones para la supervisión de la seguridad en más detalles, se describen los siguientes dominios:

- Evaluación de malware
- Evaluación de actualización
- Acceso e identidad

> [AZURE.NOTE] Para obtener información general de todas estas opciones, lea la [Introducción a la solución de auditoría y de seguridad del conjunto de aplicaciones de administración de operaciones](oms-security-getting-started.md).

### <a name="monitoring-system-protection"></a>Protección del sistema de supervisión

En una enfoque de profundidad defensa, cada nivel de protección es importante para el estado general de los activos de seguridad. Equipos con amenazas detectados y equipos con protección insuficiente se muestran en el mosaico de la evaluación de Malware en dominios de seguridad. Con la información de la evaluación de Malware, puede identificar un plan para aplicar la protección a los servidores que necesitan. Para tener acceso a esta opción, siga los pasos siguientes:

1. En el panel principal del **Conjunto de aplicaciones de administración de operaciones de Microsoft** , haga clic en mosaico de **seguridad y auditoría** .

    ![Seguridad y auditoría](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)

2. En el panel de **seguridad y auditoría** , haga clic en **Evaluación Antimalware** en **Dominios de seguridad**. El panel de la **Evaluación de Antimalware** aparece como se muestra a continuación:

![Evaluación de malware](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig2-ga.png)

Puede utilizar el panel de la **Evaluación de Malware** para identificar los siguientes problemas de seguridad:

- **Activas amenazas**: equipos que se han comprometido y tengan amenazas activas en el sistema.
- **Remediated amenazas**: se corrigen los equipos que se han comprometido pero las amenazas.
- **Firma caducado**: equipos que tienen habilitada la protección contra malware, pero la firma no está actualizado.
- **Sin protección en tiempo real**: equipos que no tengan antimalware instalado.

### <a name="monitoring-updates"></a>Actualizaciones de supervisión 

Aplicar las actualizaciones más recientes de seguridad es práctica recomendada de seguridad y debe introducirse en su estrategia de administración de la actualización. Servicio de agente de supervisión de Microsoft (HealthService.exe) lee la información de actualización de equipos supervisados y envía esta información actualizada al servicio de OMS en la nube para el procesamiento. El servicio Agente de supervisión de Microsoft está configurado como un servicio automático y debe estar ejecutándose siempre en el equipo de destino.

![actualizaciones de supervisión](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig3.png)

Lógica se aplica a los datos de actualización y el servicio de nube registra los datos. Si se encuentran las actualizaciones que faltan, que se muestren en el panel de **actualizaciones** . Puede usar el panel de **actualizaciones** para trabajar con las actualizaciones que faltan y desarrollar un plan para aplicarlos a los servidores que necesiten. Siga los pasos siguientes para tener acceso al panel de **actualizaciones** :

1. En el panel principal del **Conjunto de aplicaciones de administración de operaciones de Microsoft** , haga clic en mosaico de **seguridad y auditoría** .
2. En el panel de **auditoría y seguridad** , haga clic en la **Evaluación de actualización** en **Dominios de seguridad**. El panel de actualización aparece como se muestra a continuación:

![evaluación de actualización](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig4.png)

En este panel puede realizar una evaluación de actualización para conocer el estado actual de sus equipos y solucionar las amenazas más importantes. Usando el icono **tareas críticas o actualizaciones de seguridad** , los administradores de TI podrán obtener acceso a información detallada sobre las actualizaciones que faltan tal como se muestra a continuación:

![resultado de la búsqueda](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig5.png)

Este informe incluye información importante que puede usarse para identificar el tipo de amenaza este sistema es vulnerable a, que incluye los artículos de Microsoft KB asociados con la actualización de seguridad y el MS Bulletin que tenga más detalles acerca de la vulnerabilidad.

### <a name="monitoring-identity-and-access"></a>Supervisión de acceso e identidad

Con usuarios trabajar desde cualquier lugar, con diferentes dispositivos y obtener acceso a una gran cantidad de aplicaciones de nube y locales, es fundamental que están protegidas sus credenciales. Ataques de robo de credenciales son aquellos en los que un intruso inicialmente obtiene acceso a las credenciales de un usuario normal para tener acceso a un sistema dentro de la red. Muchas veces, este ataque inicial es solo una forma de obtener acceso a la red, el objetivo es descubrir cuentas con privilegios. 

Atacantes permanecerá en la red, mediante gratuitamente herramientas para extraer las credenciales de las sesiones de otras cuentas de inicio de sesión. Según la configuración del sistema, se pueden extraer estas credenciales en forma de hash, vales o contraseñas de texto simple par.  

> [AZURE.NOTE] equipos que se exponen directamente a Internet verán varios intentos que intenta iniciar sesión utilizando todo tipo de nombres de usuario conocidos (por ejemplo Administrador). En la mayoría de los casos está bien si no se usan los nombres de usuario conocidos y si la contraseña es suficientemente eficaz.

Es posible identificar estas algún intruso antes de que una cuenta con privilegios de peligro. Puede aprovechar la **seguridad de OMS y solución de auditoría** para supervisar la identidad y acceso. Siga los pasos siguientes para tener acceso al panel de **identidad y acceso** :

1. En el panel principal del **Conjunto de aplicaciones de administración de operaciones de Microsoft** , haga clic en seguridad y auditoría de mosaico.
2. En el panel de **seguridad y auditoría** , haga clic en **acceso e identidad** en **Dominios de seguridad**. El panel de **acceso e identidad** aparece como se muestra a continuación:

![acceso e identidad](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig6-ga.png)

Como parte de su estrategia de supervisión regular, debe incluir la supervisión de identidad. Si hay un nombre de usuario válido específico que tiene el número de intentos, debe ser administrador de TI. Esto puede indicar cualquier intruso que adquirido el nombre de usuario real e intente fuerza bruta o una herramienta automática usos modificable contraseña que ha expirado.

Este habilitar paneles TI identificar rápidamente posibles amenazas relacionados con la identidad y acceso a los recursos de empresa. Es determinado importante también identificar posibles tendencias, por ejemplo en el mosaico de inicios de sesión largo del tiempo, puede ver durante el período de tiempo cuántas veces se realizó un intento de inicio. En este caso, el equipo **servidor de archivos** recibidos 35 intentos de inicio de sesión. Puede explorar más detalles sobre este equipo haciendo clic en él. 

![más detalles](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig7-new.png)

El informe generado para este equipo aporta valiosos detalles sobre este patrón. Observado que la columna **cuenta** le ofrece la cuenta de usuario que usó para intentar obtener acceso al sistema, la columna **TIMEGENERATED** le da el intervalo de tiempo en el que se ha realizado el intento y la columna **LOGONTYPENAME** proporciona la ubicación donde se ha realizado este intento. Si estos intentos realizados localmente en el sistema de un programa, la columna de **proceso** , Mostrar nombre del proceso. En escenarios donde el intento de inicio de sesión procedente de un programa, ya tiene el nombre del proceso y ahora puede realizar más investigaciones en el sistema de destino.

## <a name="see-also"></a>Vea también

En este documento, ha aprendido a usar la seguridad de OMS y una solución para supervisar los recursos de auditoría. Para obtener más información sobre la seguridad de OMS, consulte los artículos siguientes:

- [Información general de las operaciones Management Suite (OMS)](operations-management-suite-overview.md)
- [Introducción a la seguridad del conjunto de aplicaciones de administración de operaciones y solución de auditoría](oms-security-getting-started.md)
- [Supervisar y responder a las alertas de seguridad de seguridad del conjunto de aplicaciones de administración de operaciones y solución de auditoría](oms-security-responding-alerts.md)