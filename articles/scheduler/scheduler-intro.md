<properties
 pageTitle="¿Qué es programador de Azure? | Microsoft Azure"
 description="Planificador de Azure permite mediante declaración se describen las acciones que se ejecute en la nube. A continuación, programa y se ejecuta automáticamente a las acciones."
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="what-is-azure-scheduler"></a>¿Qué es programador de Azure?

Planificador de Azure permite mediante declaración se describen las acciones que se ejecute en la nube. A continuación, programa y se ejecuta automáticamente a las acciones.  Para ello, el programador utilizando [el portal de Azure](scheduler-get-started-portal.md), código, [API de REST](https://msdn.microsoft.com/library/mt629143.aspx)o PowerShell de Azure.

El programador crea, mantiene e invoca el trabajo programado.  Programador no alojar las cargas de trabajo o ejecutar cualquier código. TI sólo el código _invoca_ alojado en otro lugar, en Azure, local, o con otro proveedor. Invoca a través de HTTP, HTTPS, una cola de almacenamiento, una cola de bus de servicio o un tema de bus de servicio.

Programador de programar [trabajos](scheduler-concepts-terms.md), mantiene un historial de resultados de la ejecución de trabajo que uno puede revisar y programa las cargas de trabajo que se ejecutará de forma determinista y confiable. Azure WebJobs (parte de la característica de aplicaciones Web de servicio de la aplicación de Azure) y otra funciones de programación de Azure usan a Programador en segundo plano. La [API de REST de programador](https://msdn.microsoft.com/library/mt629143.aspx) ayuda a administrar la comunicación de estas acciones. Por lo tanto, programador admite [programaciones complejas y avanzada periodicidad](scheduler-advanced-complexity.md) fácilmente.

Existen varios escenarios que adecuadas para el uso del programador. Por ejemplo:

+ _Acciones de aplicación repetitiva:_ Periódicamente recopilación de datos de Twitter en una fuente.
+ _Mantenimiento diario:_ Eliminación diaria de registros, realizar copias de seguridad y otras tareas de mantenimiento. Por ejemplo, un administrador puede hacer la copia de seguridad de la base de datos a 1:00 A.M. todos los días para los próximos meses nueve.

Programador le permite crear, actualizar, eliminar, ver y administrar [colecciones de trabajo](scheduler-concepts-terms.md) y trabajos mediante programación, mediante secuencias de comandos y, en el portal.

## <a name="see-also"></a>Vea también

 [Azure programador conceptos, terminología y la jerarquía de entidad](scheduler-concepts-terms.md)

 [Empezar a usar a planificador en el portal de Azure](scheduler-get-started-portal.md)

 [Planes y facturación en el programador de Azure](scheduler-plans-billing.md)

 [Cómo crear programaciones complejas y periodicidad avanzada con el programador de Azure](scheduler-advanced-complexity.md)

 [Referencia de API de REST de programador de Azure](https://msdn.microsoft.com/library/mt629143)

 [Referencia de cmdlets de PowerShell de programador Azure](scheduler-powershell-reference.md)

 [Azure programador de alta disponibilidad y confiabilidad](scheduler-high-availability-reliability.md)

 [Límites de programador Azure, valores predeterminados y códigos de error](scheduler-limits-defaults-errors.md)

 [Autenticación de salida de Azure programador](scheduler-outbound-authentication.md)
