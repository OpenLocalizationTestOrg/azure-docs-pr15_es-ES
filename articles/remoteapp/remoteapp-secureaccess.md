
<properties 
    pageTitle="Proteger el acceso a RemoteApp de Azure y más allá de | Microsoft Azure"
    description="Obtenga información sobre cómo proteger el acceso a Azure RemoteApp usando acceso condicional de Azure Active Directory"
    services="remoteapp"
    documentationCenter="" 
    authors="piotrci" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />

# <a name="securing-access-to-azure-remoteapp-and-beyond"></a>Proteger el acceso a RemoteApp de Azure y más allá

> [AZURE.IMPORTANT]
> RemoteApp Azure es que se utilizan. Lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) para obtener información detallada.

En este artículo se hará una descripción general de cómo un administrador puede configurar un canal de acceso seguro a partir del usuario final, a través de RemoteApp de Azure y acabando por un recurso seguro, como una base de datos SQL o en otra aplicación back-end. El objetivo es asegurarse de que sólo los usuarios autorizados las condiciones que desee de la reunión pueden tener acceso a aplicaciones remotas y que el back-end seguro sólo se puede acceder desde el entorno de Azure RemoteApp controlado y no desde otras ubicaciones.

Hay 3 necesita el administrador para ver las áreas principales:

![Consideraciones de acceso condicional RemoteApp Azure](./media/remoteapp-secureaccess/ra-conditionalenvironment.png)

Lectura en para obtener información y respuestas a estas preguntas.

## <a name="who-can-access-the-collection"></a>¿Quién puede tener acceso a la colección?
El administrador elige los usuarios que puedan tener acceso remotas aplicaciones en la colección. Puede usar el trabajo de Azure Active Directory (AD Azure) o cuentas de la escuela (anteriormente denominadas, "cuentas de la organización") o cuentas de Microsoft (por ejemplo, @outlook.com). La mayoría de los escenarios de empresa usar cuentas de Azure AD; permitir usar access condicional características se describen más adelante y también están la única opción para colecciones de dominio. El resto de este artículo se supone que está utilizando cuentas de Azure AD con RemoteApp de Azure.

**Lo que nos hemos logrado:**

Usar cuentas de Azure AD para controlar el acceso a RemoteApp de Azure, nos proporciona dos cosas:

1.  Siempre sepa quién puede tener acceso a las aplicaciones que se hayan publicado y acceder a las aplicaciones de fondo conectan a.
2.  Nos controlar la Azure AD subyacente para podemos crear y eliminar cuentas de usuario, establecer directivas de contraseña, usan autenticación multifactor, etcetera. 

## <a name="how-is-the-collection-accessed-from-where"></a>¿Cómo se accede a la colección? ¿Dónde?
Normalmente los administradores deseen definir directivas para obtener acceso a un entorno de a través de Internet público, como RemoteApp de Azure. Por ejemplo, desea asegurarse de que el entorno de fuera de la red corporativa de acceso de usuarios tienen usar autenticación multifactor (AMF) para tener acceso; o tal vez deben bloquearse totalmente.

Administradores de RemoteApp Azure pueden utilizar las funciones disponibles a través de prima de Azure AD configurar directivas de acceso condicional para su entorno de RemoteApp de Azure. También puede usar las características de alerta y generación de informes enriquecidos para controlar cómo se tiene acceso el entorno.

### <a name="how-to-set-up-conditional-access-for-azure-remoteapp"></a>Cómo configurar el acceso condicional para RemoteApp de Azure
Vamos a recorrer un escenario de ejemplo: el RemoteApp de Azure administrador desea bloquear el acceso al entorno cuando los usuarios están fuera de la red corporativa.

>[AZURE.NOTE] Se asume que ha actualizado Azure AD al nivel Premium y que se ha creado al menos una colección de RemoteApp de Azure.

1.  En el portal de Azure, haga clic en la pestaña de **Active Directory** . A continuación, haga clic en el directorio que desea configurar.

    Recuerde: Acceso condicional es una propiedad de su directorio y no de RemoteApp de Azure, por lo que toda la configuración se realiza en el nivel de directorio. Esto también significa que debe ser el Administrador de directorios para realizar estos cambios.

2.  Haga clic en **aplicaciones**y, a continuación, haga clic en **Microsoft Azure RemoteApp** para configurar el acceso condicional. Tenga en cuenta que puede configurar el acceso condicional para cada aplicación de "software como servicio" en el directorio por separado.
![Configurar el acceso condicional para RemoteApp de Azure](./media/remoteapp-secureaccess/ra-conditionalaccessscreen.png)
 

3.  En la ficha **Configurar** , establezca **Habilitar reglas de acceso** en activado.
![Habilitar reglas de acceso para RemoteApp de Azure](./media/remoteapp-secureaccess/ra-enableaccessrules.png)
 

4.  Ahora puede configurar varias reglas y elija quién aplicarlos a:

    1. Elija **bloquear el acceso cuando no está en trabajo** completamente evitar que los usuarios accedan a Azure RemoteApp fuera del entorno de red que especifique.
    2. Haga clic en la opción siguiente para definir los intervalos de direcciones IP que constituyen su "red de confianza". Todo el contenido fuera de los serán rechazado.

5.  Pruebe su configuración iniciando el cliente de Azure RemoteApp desde una dirección IP fuera del rango especificado. Una vez que inicie sesión con sus credenciales de Azure AD verá un mensaje similar a esta:

![Acceso denegado a RemoteApp de Azure](./media/remoteapp-secureaccess/ra-accessdenied.png)
 

### <a name="future-conditional-access-features"></a>Características de acceso condicional en el futuro 
El equipo de Azure Active Directory está trabajando en nuevas capacidades de acceso condicional. Los administradores podrán crear nuevos tipos de reglas más allá de la red reglas según ubicación. Una vista previa pública de la nueva funcionalidad debe estar disponible próximamente.

### <a name="how-to-monitor-access-to-azure-remoteapp"></a>Cómo supervisar el acceso a RemoteApp de Azure
Una gran característica usar junto con acceso condicional es la funcionalidad de informes de Azure Active Directory Premium. Puede usar informes para supervisar quién tiene acceso a su entorno y detectar cualquier actividad sospechosa.

Por ejemplo, puede ver los nombres de los usuarios que tiene acceso a RemoteApp de Azure, cuántas veces que se han hecho y cuándo.

1.  En el portal de Azure, haga clic en **Active Directory**y, a continuación, haga clic en el directorio.

2.  Vaya a la pestaña **informes** .

3.  En la lista de informes, seleccione el **uso de la aplicación** en **aplicaciones integradas**.

    Verá algunas estadísticas agregados de RemoteApp de Azure. 
![Estadísticas de acceso de Azure RemoteApp agregados](./media/remoteapp-secureaccess/ra-accessstats.png)
 
5.  Haga clic en la aplicación para revelar información acerca de los usuarios acceso a RemoteApp de Azure.
![Estadísticas de acceso de usuario de RemoteApp de Azure](./media/remoteapp-secureaccess/ra-userstats.png)
 
### <a name="summary"></a>Resumen
Puede configurar reglas de acceso a RemoteApp de Azure (y otro software como una aplicación de servicio disponible a través de Azure AD) con Azure Active Directory Premium. Las reglas están limitadas actualmente a las directivas de según ubicación de red, pero en el futuro se ampliarán a otros aspectos de la administración de la empresa.

Premium de Azure AD también ofrece informes y funciones que amplían aún más el control de la administración de supervisión tiene sobre su entorno de RemoteApp de Azure.

## <a name="how-do-i-make-sure-my-secure-resource-is-accessible-only-from-my-azure-remoteapp-environment"></a>¿Cómo me aseguro de que mi recurso seguro sólo está accesible desde el entorno de RemoteApp de Azure?
En las secciones anteriores de este artículo se centra en proteger el acceso al entorno de RemoteApp de Azure. Hemos logra elegir los usuarios que tienen permiso de acceso y configurando reglas de acceso para controlar aún más cómo puede usar el servicio.

Un escenario común para implementaciones de RemoteApp de Azure es que las aplicaciones remotas necesitan comunicarse con un recurso de back-end, por ejemplo una base de datos SQL. Este recurso se encuentra en modo local (por ejemplo, en una red corporativa) o en la nube (por ejemplo, en Azure IaaS). Los administradores con qué frecuencia desean asegurarse de que solo se puede acceder el recurso de back-end mediante aplicaciones instaladas a través de RemoteApp de Azure y no por ejemplo una aplicación ejecuta directamente en un PC y obtener acceso a través de Internet público. Azure RemoteApp a menudo se considera el entorno de administración centralizada y seguro y, por tanto, la ruta de acceso sólo a través del cual los usuarios deben interactuar con los recursos de back-end.

La solución consiste en colocar el entorno de RemoteApp de Azure y el recurso seguro en la red Virtual de Azure (VNET) mismo. Si el recurso se encuentra en otro sitio, puede establecer una conexión VPN de sitio a otro, por ejemplo, para crear un VNet que dura el centro de datos de Azure y el entorno local del cliente.

RemoteApp Azure es compatible con dos tipos de implementaciones colección donde puede proporcionar su propio VNET:

-   No unido al dominio: las aplicaciones tendrán "línea de visión" de otros recursos en el VNET. Por ejemplo, esto puede usarse para conectar aplicaciones a una base de datos SQL que utiliza la autenticación de SQL (aplicaciones autentican al usuario directamente en la base de datos)

-   Dominio une: los equipos virtuales utilizados por RemoteApp de Azure se unen a un controlador de dominio en la VNET. Esto es útil cuando las aplicaciones deben autenticarse en un controlador de dominio de Windows para poder obtener acceso a un recurso de back-end.
![Una colección de dominio en RemoteApp de Azure](./media/remoteapp-secureaccess/ra-domainjoined.png)
 
### <a name="how-to-create-a-secure-connection-between-azure-and-my-on-premises-environment"></a>Cómo crear una conexión segura entre Azure y mi entorno local
Existen varias opciones de configuración para conectar los entornos locales y de Azure. Información general completa de las opciones está disponible aquí.

Con RemoteApp de Azure, debe configurar su VNet en primer lugar y, a continuación, se utiliza durante el proceso de creación de la colección. 

## <a name="the-complete-solution"></a>La solución completa
El diagrama siguiente muestra la solución completa que hemos creado un canal de acceso seguro del usuario final, a través de RemoteApp Azure (ARA), en el recurso de back-end.
![Proteger RemoteApp de Azure](./media/remoteapp-secureaccess/ra-secureoverview.png) en fase 1 que los usuarios seleccionados y crear reglas de acceso que determinan cómo se puede acceder ARA. En el siguiente ejemplo se solo permitir el acceso de los usuarios que trabajen en la red corporativa. Los usuarios no conforme no podrá obtener acceso al entorno de ARA en absoluto.
En "Fase 2" nos hemos expuesto el recurso de back-end sólo a través de la configuración de la VPN VNet que se controla. Azure RemoteApp está guardada en el mismo VNet. El resultado final es que el recurso solo se puede acceder a través del entorno de ARA.


