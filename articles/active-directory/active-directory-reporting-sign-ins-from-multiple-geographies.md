<properties
    pageTitle="Complementos de inicio de sesión de varias regiones geográficas"
    description="Un informe que indica los usuarios donde dos firmar ins parece proceden de diferentes regiones y el tiempo entre el inicio de sesión que ins impide que el usuario ha recorrido entre las regiones."
    services="active-directory"
    documentationCenter=""
    authors="SSalahAhmed"
    manager="gchander"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/04/2016"
    ms.author="saah;kenhoff"/>

# <a name="sign-ins-from-multiple-geographies"></a>Inicios de sesión de diversas localidades

Este informe incluye los complementos de inicio de sesión correcta de un usuario, donde dos inicios de sesión parecían proceden de diferentes regiones y el tiempo entre los inicios de sesión impide que el usuario haya desplazado entre las regiones. Las posibles causas se incluyen:

- Usuario comparte su contraseña con otros usuarios

- Usuario está usando un escritorio remoto para iniciar un explorador web para el inicio de sesión

- Un intruso ha iniciado sesión en la cuenta de un usuario de un país diferente

- Usuario que está utilizando un proxy o VPN

- Usuario ha iniciado sesión desde varios dispositivos al mismo tiempo, como un escritorio y un teléfono móvil, y la dirección IP del teléfono móvil es inusual.

Resultados de este informe mostrará el inicio de sesión sucesos correctos, junto con el tiempo entre los inicios de sesión, las regiones donde parecían proceden de los complementos de inicio de sesión y el tiempo de viaje estimado entre las regiones. El tiempo de viaje mostrado es solo una estimación y puede diferir del tiempo de viaje real entre las ubicaciones.


![Complementos de inicio de sesión de varias regiones geográficas](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)
