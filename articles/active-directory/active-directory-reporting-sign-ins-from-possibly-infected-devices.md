<properties
    pageTitle="Inicio de sesión ins desde dispositivos posiblemente infectados"
    description="Crear un informe que incluye el inicio de sesión de intentos que se han ejecutado desde dispositivos en los que pueden estar ejecutándose ciertos tipos de malware (software malintencionado)."
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


# <a name="sign-ins-from-possibly-infected-devices"></a>Inicio de sesión ins desde dispositivos posiblemente infectados
Este informe intenta identificar los dispositivos de los usuarios que han sido infectados y ahora forman parte de una red de zombis. Nos relacionar direcciones IP de inicios de sesión de los usuarios con direcciones IP que sabemos pondrá en contacto con servidores botnet.

Recomendación: Este informe indicadores de direcciones IP, no los dispositivos de usuario. Le recomendamos que ponerse en contacto con el usuario y analice todos los dispositivos del usuario para estar seguro. También es posible que el dispositivo personal de un usuario está infectado o que alguien que no sea el usuario, que se usa la misma dirección IP que el usuario, tiene un dispositivo infectado.

Para obtener más información acerca de cómo infección de malware de dirección, consulte el [Centro de protección contra Malware](http://go.microsoft.com/fwlink/?linkid=335773).

![Inicio de sesión ins desde dispositivos posiblemente infectados](./media/active-directory-reporting-sign-ins-from-possibly-infected-devices/signInsFromPossiblyInfectedDevices.PNG)
