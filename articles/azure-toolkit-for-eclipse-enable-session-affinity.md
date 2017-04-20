<properties
    pageTitle="Habilitar la afinidad de la sesión con el Kit de herramientas de Azure para Eclipse"
    description="Obtenga información sobre cómo habilitar la afinidad de la sesión con el Kit de herramientas de Azure para Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690950.aspx -->

# <a name="enable-session-affinity"></a>Habilitar la afinidad de la sesión #

En el Kit de herramientas de Azure para Eclipse, puede habilitar la afinidad de la sesión HTTP o "sesiones permanentes", para los roles. La imagen siguiente muestra las propiedades de **Equilibrio de carga de** cuadro de diálogo que se utiliza para habilitar la característica de afinidad de sesión:

![][ic719492]

## <a name="to-enable-session-affinity-for-your-role"></a>Para habilitar la afinidad de la sesión de su rol ##

1. Haga clic en el rol en explorador del Eclipse de proyectos, haga clic en **Azure**y, a continuación, haga clic en **Equilibrio de carga**.
1. En el cuadro de diálogo **Propiedades de equilibrio de carga de WorkerRole1** :
    1. Comprobar **afinidad de la sesión de habilitar HTTP (sesiones rápidas) para este rol.**
    1. **Extremo de entrada para usar**, seleccione un extremo de entrada para usar, por ejemplo, **http (público: 80, privado: 8080)**. La aplicación debe utilizar este extremo como su extremo HTTP. Puede habilitar varios extremos para su rol, pero puede seleccionar sólo uno de ellos para admitir sesiones rápidas.
    1. Volver a generar la aplicación.

Una vez habilitada, si tiene más de una instancia de rol, solicitudes HTTP procedentes de un cliente en particular seguirá controlando la misma instancia de rol.

El Kit de herramientas Eclipse permite instalar un módulo IIS especial denominado enrutamiento de solicitud de aplicación (ARR) en cada una de las instancias de la función. ARR redirige las solicitudes HTTP a la instancia de rol correspondiente. El Kit de herramientas vuelve a configurar automáticamente el extremo seleccionado para que el tráfico HTTP entrante se enruta primero al software ARR. El Kit de herramientas también crea un nuevo extremo interno que el servidor de Java está configurado para escuchar. Que es el extremo ARR utilizado para redirigir el tráfico HTTP a la instancia de rol correspondiente. De este modo, cada instancia de rol en la implementación de instancias múltiples sirve como un proxy inverso para todas las demás instancias, lo que permite sesiones rápidas.

## <a name="notes-about-session-affinity"></a>Notas acerca de la afinidad de la sesión ##

* Afinidad de la sesión no funciona en el emulador de cálculo. La configuración se puede aplicar en el emulador cálculo sin interferir con el proceso de creación o calcula ejecución emulador, pero no funciona la característica propio emulador de cálculo.
* Al habilitar la afinidad sesión dará como resultado un aumento de espacio en disco ocupado por la implementación de Azure, tal como se descargará y se instalan en las instancias de la función cuando se inicie el servicio en la nube de Azure software adicional.
* La hora de iniciar cada función tardará más.
* Un extremo interno, para que funcione como un rerouter de tráfico, como se mencionó anteriormente, será added.ss

Para obtener un ejemplo de cómo mantener los datos de la sesión cuando se habilita la afinidad de la sesión, consulte [cómo mantener los datos de sesión con la afinidad de la sesión][].

## <a name="see-also"></a>Vea también ##

[Kit de herramientas de Azure para Eclipse][]

[Crear una aplicación del mundo Hola para Azure en Eclipse][]

[Instalar el Kit de herramientas de Azure Eclipse][] 

[Cómo mantener los datos de sesión con la afinidad de sesión][]

Para obtener más información sobre el uso de Azure con Java, consulte el [Centro para desarrolladores de Java de Azure][].

<!-- URL List -->

[Centro para desarrolladores de Java de Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de herramientas de Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Crear una aplicación del mundo Hola para Azure en Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Cómo mantener los datos de sesión con la afinidad de sesión]: http://go.microsoft.com/fwlink/?LinkID=699539
[Instalar el Kit de herramientas de Azure Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719492]: ./media/azure-toolkit-for-eclipse-enable-session-affinity/ic719492.png
