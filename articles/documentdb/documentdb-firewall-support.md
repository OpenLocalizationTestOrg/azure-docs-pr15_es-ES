<properties
    pageTitle="Soporte de firewall DocumentDB | Microsoft Azure"
    description="Obtenga información sobre cómo usar las directivas de control de acceso IP de compatibilidad de firewall de cuentas de base de datos de Azure DocumentDB."
    keywords="Control de acceso IP, soporte de firewall"
    services="documentdb"
    authors="shahankur11"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager"
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="ankshah; kraman"/>

# <a name="documentdb-firewall-support"></a>Soporte de firewall DocumentDB

Para proteger los datos almacenados en una cuenta de base de datos de Azure DocumentDB, DocumentDB ha proporcionado soporte técnico para un secreto basado en [modelo de autorización](https://msdn.microsoft.com/library/azure/dn783368.aspx) que utiliza un código de autenticación fuerte mensaje basado en Hash (HMAC). Ahora, además del modelo de autorización basados en secreto, DocumentDB admite directiva controlados por los controles de acceso basado en IP para soporte de firewall de entrada. Este modelo es muy similar a las reglas de firewall de un sistema de base de datos tradicional y proporciona un nivel de seguridad para la cuenta de la base de datos de DocumentDB adicional. Con este modelo, ahora puede configurar una cuenta de base de datos de DocumentDB para accesibles solamente desde un conjunto de equipos aprobado o servicios de nube. Acceso a los recursos de DocumentDB de estos aprobados conjuntos de equipos y servicios aún requieren el llamador presentar un token de autorización válida.

## <a name="ip-access-control-overview"></a>Información general sobre el control de acceso IP

De forma predeterminada, una cuenta de base de datos de DocumentDB es accesible desde internet pública, siempre y cuando la solicitud se acompaña de un token de autorización válida. Para configurar el control de acceso basado en directiva IP, el usuario debe proporcionar el conjunto de direcciones IP o intervalos de direcciones IP en formulario CIDR se incluyan como la lista permitida de IP de cliente para una cuenta de base de datos determinado. Una vez que se aplica esta configuración, se bloquearán todas las solicitudes que provienen de equipos fuera de esta lista permitido por el servidor.  En el siguiente diagrama se describe la conexión de flujo de proceso para el control de acceso basado en IP.

![Diagrama que muestra el proceso de conexión para el control de acceso basado en IP](./media/documentdb-firewall-support/documentdb-firewall-support-flow.png)

## <a name="connections-from-cloud-services"></a>Conexiones de servicios de nube

En Azure, servicios de nube son una forma muy común para hospedar lógica de servicio de nivel intermedio con DocumentDB. Para habilitar el acceso a una cuenta de base de datos de DocumentDB de un servicio de nube, la dirección IP pública del servicio de nube debe agregarse a la lista de direcciones IP asociada a su cuenta de base de datos de DocumentDB póngase en contacto con [soporte técnico de Azure](#configure-ip-policy)permitida.  Esto asegura que todas las instancias de rol de servicios de nube tengan acceso a su cuenta de base de datos de DocumentDB. Puede recuperar direcciones IP de los servicios de nube en el portal de Azure, como se muestra en la siguiente captura de pantalla. 

![Captura de pantalla que muestra la dirección IP pública de un servicio de nube que se muestran en el portal de Azure](./media/documentdb-firewall-support/documentdb-public-ip-addresses.png)

Cuando se escalar su servicio de nube mediante la adición de todas las instancias de funciones adicionales, las nuevas instancias tiene automáticamente acceso a la cuenta de base de datos DocumentDB ya que son parte del mismo servicio de nube.

## <a name="connections-from-virtual-machines"></a>Conexiones de máquinas virtuales

[Máquinas virtuales](https://azure.microsoft.com/services/virtual-machines/) o [conjuntos de escala de la máquina virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) puede utilizarse también para hospedar servicios de nivel intermedio mediante DocumentDB.  Para configurar la DocumentDB cuenta de base de datos para permitir el acceso de máquinas virtuales, las direcciones IP públicas de máquina virtual o conjunto de escala de máquina virtual debe estar configurada como una de las direcciones IP permitidas para su cuenta de base de datos de DocumentDB póngase en contacto con [soporte técnico de Azure](#configure-ip-policy). Puede recuperar direcciones IP para equipos virtuales en el portal de Azure, como se muestra en la siguiente captura de pantalla.

![Captura de pantalla que muestra una dirección IP pública para una máquina virtual que se muestran en el portal de Azure](./media/documentdb-firewall-support/documentdb-public-ip-addresses-dns.png)

Al agregar instancias de máquina virtual adicional al grupo, se proporcionan acceso a su cuenta de base de datos DocumentDB automáticamente.

## <a name="connections-from-the-internet"></a>Conexiones de internet

Cuando se tiene acceso a una cuenta de base de datos de DocumentDB desde un equipo en internet, la dirección IP del cliente o el intervalo de direcciones IP de la máquina debe agregarse a la lista de direcciones IP para la cuenta de la base de datos de DocumentDB permitida. 

## <a id="configure-ip-policy"></a>Configuración de la directiva de control de acceso IP

Usar el portal de Azure en el archivo de una solicitud de [Soporte técnico de Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para habilitar la directiva de control de acceso IP de su cuenta de base de datos.

1. En el módulo de [ayuda + soporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , seleccione **nueva solicitud de soporte técnico**.
2. En el módulo de **solicitud de soporte técnico de nuevo** , seleccione **conceptos básicos**.
3. En el módulo de **conceptos básicos** , seleccione lo siguiente:
    - **Tipo de problema**: cuota
    - **Suscripción**: la suscripción asociada con la cuenta en la que desea agregar la directiva de control de acceso IP.
    - **Tipo de cuota**: DocumentDB
    - **Plan de soporte técnico**: soporte de cuota - incluido.
4. En el módulo de **problema** , haga lo siguiente:
    - **Gravedad**: seleccione C - impacto mínimo
    - **Detalles**: copie el siguiente texto en el cuadro e incluir su nombre de cuenta/s y IP dirección/es: "Deseo activar el soporte de firewall de mi cuenta de la base de datos de DocumentDB. Cuenta de base de datos: *cuenta de incluir nombre/s*. Permite o intervalos de direcciones IP: *incluir/intervalo de direcciones IP en formato CIDR, por ejemplo 13.91.6.132, 13.91.6.1/24*. "
    - Haga clic en **siguiente**. 
5. En el módulo de **información de contacto** , rellene la información de contacto y haga clic en **crear**. 

Una vez que se recibe la solicitud, control de acceso IP debe estar habilitado en 24 horas. Se le notificará una vez completada la solicitud.

![Captura de pantalla de la Ayuda + admite aspas](./media/documentdb-firewall-support/documentdb-firewall-support-request-access.png)

![Captura de pantalla del módulo de problema](./media/documentdb-firewall-support/documentdb-firewall-support-request-access-ticket.png)

## <a name="troubleshooting-the-ip-access-control-policy"></a>Solución de problemas de la directiva de control de acceso IP

### <a name="portal-operations"></a>Operaciones de portal

Al habilitar una directiva de control de acceso IP de su cuenta de base de datos DocumentDB, todo el acceso a su cuenta de base de datos de DocumentDB de equipos fuera de la configuración permite lista de intervalos de direcciones IP están bloqueados. Gracias a este modelo, la operación de plano de datos desde el portal de exploración también se bloqueará para garantizar la integridad de control de acceso. 

### <a name="sdk--rest-api"></a>API de Rest & de SDK

Por motivos de seguridad, access mediante SDK o API de REST de máquinas no se encuentran en la lista permitida devolverá genérico 404 no encontrado respuesta sin detalles adicionales. Compruebe la dirección IP configurada para su cuenta de base de datos de DocumentDB de lista admitida para garantizar que la configuración correcta de directiva se aplica a su cuenta de base de datos de DocumentDB.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información acerca de la red sugerencias de rendimiento relacionados, vea [sugerencias para el rendimiento](documentdb-performance-tips.md).