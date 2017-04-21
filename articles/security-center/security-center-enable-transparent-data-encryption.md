<properties
   pageTitle="Habilitar el cifrado de datos transparente en el centro de seguridad de Azure | Microsoft Azure"
   description="Este documento muestra cómo implementar la recomendación del centro de seguridad de Azure **Habilitar cifrado de datos transparente**."
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
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Habilitar el cifrado de datos transparente en el centro de seguridad de Azure

Centro de seguridad de Azure se recomienda que habilite el cifrado de datos transparente (TDE) en bases de datos SQL si TDE ya no está habilitado. TDE protege los datos y le ayuda a cumplir los requisitos de cumplimiento mediante el cifrado de la base de datos, las copias de seguridad asociados y archivos de registro de transacciones en el resto, sin necesidad de cambios en la aplicación. Para obtener que más información, vea [Cifrado de datos transparente con la base de datos de SQL Azure](https://msdn.microsoft.com/library/dn948096).

Esta recomendación se aplica al servicio de SQL Azure. no incluir ejecutando en sus máquinas virtuales SQL.

> [AZURE.NOTE] Este documento presenta el servicio mediante el uso de una implementación de ejemplo.  No es una guía paso a paso.

## <a name="implement-the-recommendation"></a>Implementar la recomendación

1. En el módulo de **recomendaciones** , seleccione **Habilitar cifrado de datos transparente**.
![Habilitar el cifrado de datos transparente][1]

2. Se abrirá el módulo de **Habilitar el cifrado de datos transparente en bases de datos SQL** . Seleccione una base de datos SQL para habilitar TDE en.
![Seleccione la base de datos de SQL para habilitar TDE en][2]
3. En el módulo de **cifrado de datos transparente** , seleccione **activado** en cifrado de datos y seleccione **Guardar** en la cinta de opciones superior del módulo.
![Activar TDE][3]

  Una vez TDE está habilitada en la base de datos SQL seleccionada, el **estado de cifrado** cambiará a **cifrado**.    

  ![Estado de cifrado][4]

## <a name="see-also"></a>Vea también

En este artículo le mostramos cómo implementar la recomendación del centro de seguridad "Habilitar cifrado de datos transparente". Para obtener más información acerca de SQL TDE, consulte lo siguiente:

- [Cifrado de datos transparente con la base de datos SQL Azure](https://msdn.microsoft.com/library/dn948096)
- [Empezar a trabajar con el cifrado de datos transparente (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

Para obtener más información sobre el centro de seguridad, consulte lo siguiente:

- [Configurar directivas de seguridad en el centro de seguridad de Azure](security-center-policies.md) : Obtenga información sobre cómo configurar directivas de seguridad para suscripciones de Azure y grupos de recursos.
- [Recomendaciones de seguridad de la administración de centro de seguridad de Azure](security-center-recommendations.md) : Descubra cómo recomendaciones ayudar a proteger los recursos de Azure.
- [Supervisión del estado de seguridad del centro de seguridad de Azure](security-center-monitoring.md) : Obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administrar y responder a las alertas de seguridad en el centro de seguridad de Azure](security-center-managing-and-responding-alerts.md) : Obtenga información sobre cómo administrar y responder a las alertas de seguridad.
- [Las soluciones de asociados de supervisión con el centro de seguridad de Azure](security-center-partner-solutions.md) : Obtenga información sobre cómo supervisar el estado de las soluciones de socios.
- [Preguntas más frecuentes sobre centro de seguridad de azure](security-center-faq.md) --buscar con frecuencia preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de azure](http://blogs.msdn.com/b/azuresecurity/) : obtener las últimas noticias de seguridad de Azure y la información.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
