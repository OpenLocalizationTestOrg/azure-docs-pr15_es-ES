<properties
   pageTitle="Línea de base de solución de auditoría y seguridad del conjunto de aplicaciones de administración de operaciones | Microsoft Azure"
   description="Este documento explica cómo usar la seguridad de OMS y una solución para realizar una evaluación de línea base de todos los equipos supervisados a fin de seguridad y cumplimiento de auditoría."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/08/2016"
   ms.author="yurid"/>

# <a name="baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Valoración de seguridad del conjunto de aplicaciones de administración de operaciones y solución de auditoría

Este documento le ayuda a utilizar [solución de auditoría de seguridad de (OMS) del conjunto de aplicaciones de administración de operaciones y](operations-management-suite-overview.md) capacidades de evaluación de línea base para tener acceso el estado de seguridad de los recursos supervisados.

## <a name="what-is-baseline-assessment"></a>¿Qué es la evaluación de línea base?

Microsoft, junto con la industria y administración pública organizaciones de todo el mundo, define una configuración de Windows que representa implementaciones de servidor es muy seguro. Esta configuración es un conjunto de claves de registro, configuración de la directiva de auditoría y configuración de la directiva de seguridad junto con valores recomendados de Microsoft para esta configuración. Este conjunto de reglas se conoce como línea de base de seguridad. Capacidad de evaluación de línea base OMS seguridad y auditoría perfecta puede analizar todos los equipos de cumplimiento. 

Existen tres tipos de reglas:

- **Reglas de registro**: Compruebe que las claves de registro están configuradas correctamente.
- **Las reglas de la directiva de auditoría**: reglas de la directiva de auditoría.
- **Las reglas de directivas de seguridad**: reglas con respecto a los permisos del usuario en el equipo.

> [AZURE.NOTE] Lea [Usar OMS seguridad para evaluar la referencia de configuración de seguridad](https://blogs.technet.microsoft.com/msoms/2016/08/12/use-oms-security-to-assess-the-security-configuration-baseline/) para una breve descripción de esta característica.

## <a name="security-baseline-assessment"></a>Evaluación de seguridad de línea base

Puede revisar la evaluación de línea base de seguridad actual para todos los equipos supervisados por OMS seguridad y auditoría usando el panel de control.  Ejecute los siguientes pasos para tener acceso al panel de evaluación de línea base de seguridad:

1. En el panel principal de la **Serie de administración de operaciones de Microsoft** , haga clic en mosaico de **seguridad y auditoría** .
2. En el panel de **auditoría y seguridad** , haga clic en la **Evaluación de línea base** en **Dominios de seguridad**. El panel de la **Evaluación de la línea de base de seguridad** aparece como se muestra en la imagen siguiente:
    
    ![Seguridad OMS y evaluación de línea base de auditoría](./media/oms-security-baseline/oms-security-baseline-fig1.png)

Este panel está dividido en tres áreas principales:

- **Equipos con respecto a la línea base**: esta sección proporciona un resumen de la cantidad de equipos que se tiene acceso y el porcentaje de equipos que pasa la evaluación. También proporciona los 10 principales equipos y el resultado de porcentaje de la evaluación.
- **Requiere el estado de las reglas**: esta sección tiene la intención de Traer conocimiento de las reglas con errores por gravedad y reglas por tipo de error. Busca el primer gráfico puede identificar rápidamente si mayoría las reglas que no son críticas, o no. También se ofrece una lista de las 10 reglas de error y su gravedad. El segundo gráfico muestra el tipo de regla que no se pudo durante la evaluación. 
- **Equipos falta la evaluación de línea base**: los equipos que no se puede acceder debido a errores o sistema operativo incompatibilidad de la lista de esta sección. 

### <a name="accessing-computers-compared-to-baseline"></a>Acceso a los equipos con respecto a la línea base

Lo ideal es que todos los equipos están sea compatible con la evaluación de seguridad de línea base. Sin embargo esperamos que en algunos de circunstancias esto suceda. Como parte del proceso de administración de seguridad, es importante incluir los equipos que no se puede pasar todas las pruebas de evaluación de seguridad de revisiones. Es una forma rápida de visualizar seleccionando la opción **acceso de equipos** situado en la sección de **equipos con respecto a la línea base** . Debe ver el resultado de la búsqueda de registro que muestra la lista de equipos, tal y como se muestra en la pantalla siguiente:

![Resultados de acceso de equipo](./media/oms-security-baseline/oms-security-baseline-fig2.png)

El resultado de la búsqueda se muestra en un formato de tabla, donde la primera columna tiene el nombre del equipo y el segundo color tiene el número de reglas que no se pudo. Para recuperar la información relacionada con el tipo de regla que no se pudo, haga clic en el número de error en las reglas además el nombre del equipo. Debe ver un resultado similar al que se muestra en la siguiente imagen:

![Detalles de resultados de acceso de equipo](./media/oms-security-baseline/oms-security-baseline-fig3.png)

En este resultado de la búsqueda, tendrá el total de las reglas de acceso, el número de reglas críticas que no se pudo, las reglas de advertencia y las reglas de la información error.

### <a name="accessing-required-rules-status"></a>Obtener acceso al estado de las reglas requeridas

Después de obtener la información relacionada con el número de porcentaje de equipos que pasa la evaluación, desea obtener más información acerca de qué se producen errores en las reglas según la importancia. Esta visualización le ayuda a priorizar los equipos que se deben abordar primero para asegurarse de que serán compatibles en la siguiente evaluación. Mantenga el mouse sobre el elemento del gráfico que se encuentra en la ventana **reglas por gravedad con errores** en **requiere el estado de las reglas** crítico y haga clic en él. Debe ver un resultado similar a la siguiente pantalla:

![No se pudo reglas por los detalles de la gravedad](./media/oms-security-baseline/oms-security-baseline-fig4.png) 

En este resultado de registro, verá el tipo de regla de línea base que no se pudo, la descripción de esta regla y a continuación, el identificador de enumeración de configuración común (CCE) de esta regla de seguridad. Estos atributos deberían ser suficiente para realizar una acción correctiva para solucionar este problema en el equipo de destino.

> [AZURE.NOTE] Para obtener más información sobre CCE, tener acceso a la [Base de datos de vulnerabilidad nacionales](https://nvd.nist.gov/cce/index.cfm).

### <a name="accessing-computers-missing-baseline-assessment"></a>Acceso a los equipos que faltan evaluación de línea base

OMS admite el perfil de línea base del miembro de dominio en Windows Server 2008 R2 hasta Windows Server 2012 R2. Línea de base de Windows Server 2016 aún no está final y se agregará tan pronto como se publica. Todos los otros sistemas operativos capturados a través de la evaluación de línea base de auditoría y seguridad OMS aparecerá en la sección de **equipos falta la evaluación de línea base** .

## <a name="see-also"></a>Vea también

En este documento, ha aprendido sobre seguridad de OMS y auditoría evaluación de línea base. Para obtener más información sobre la seguridad de OMS, consulte los artículos siguientes:

- [Información general de las operaciones Management Suite (OMS)](operations-management-suite-overview.md)
- [Supervisar y responder a las alertas de seguridad de seguridad del conjunto de aplicaciones de administración de operaciones y solución de auditoría](oms-security-responding-alerts.md)
- [Supervisar los recursos de seguridad del conjunto de aplicaciones de administración de operaciones y solución de auditoría](oms-security-monitoring-resources.md)

