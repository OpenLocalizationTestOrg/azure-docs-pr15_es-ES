<properties
    pageTitle="Solución de problemas: Crear y conectarse a un área de trabajo de aprendizaje de equipo | Microsoft Azure"
    description="Soluciones para problemas comunes en la creación y conectarse a un área de trabajo de aprendizaje del equipo de Azure"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/09/2016"
    ms.author="garye"/>


# <a name="troubleshooting-guide-create-and-connect-to-an-machine-learning-workspace"></a>Guía de solución de problemas: crear y conectarse a un área de trabajo de aprendizaje de equipo

Esta guía ofrece soluciones para algunas preguntas encuentran desafíos al configurar áreas de trabajo de aprendizaje del equipo de Azure.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="workspace-owner"></a>Propietario de área de trabajo

Al crear un área de trabajo de aprendizaje, el ID que especifique en el campo propietario de área de trabajo debe ser una cuenta válida de Microsoft (anteriormente, Windows Live ID), por ejemplo, john-contoso@live.com o john-contoso@hotmail.com. No puede ser una cuenta no son de Microsoft, como su cuenta de correo electrónico corporativo. Para crear una cuenta gratuita de Microsoft, vaya a [www.live.com](http://www.live.com).

Tenga en cuenta que la cuenta que usó para iniciar sesión en el portal de Azure para crear el área de trabajo no automáticamente tiene permiso para *Abrir* dicha área de trabajo, a menos que especifique esa cuenta como propietario. Para abrir un área de trabajo en equipo aprendizaje Studio, debe haber iniciado sesión en la Account de Microsoft que se ha definido como propietario del área de trabajo o debe recibir una invitación del propietario para unir el área de trabajo. Desde el portal de clásico Azure puede, sin embargo, *Administrar* el área de trabajo, que incluye la posibilidad de cambiar el propietario y configurar el acceso.

Para obtener más información sobre cómo administrar un área de trabajo, consulte [administrar un área de trabajo de aprendizaje del equipo de Azure].

[Administrar un área de trabajo de aprendizaje del equipo de Azure]: machine-learning-manage-workspace.md

## <a name="allowed-regions"></a>Regiones permitidas

Aprendizaje de máquina está disponible actualmente en un número limitado de regiones. Si la suscripción no incluye una de estas regiones, verá el mensaje de error, "No tiene suscripciones en las regiones permitidas".

Para solicitar que una región se agregue a su suscripción, seleccione **Contacto de soporte técnico de Microsoft** desde el Portal de Azure clásica, elija **facturación** como el tipo de problema y siga las indicaciones para enviar su solicitud.

![Póngase en contacto con soporte técnico de Microsoft][screen1]

## <a name="storage-account"></a>Cuenta de almacenamiento

El servicio de aprendizaje de equipo necesita una cuenta de almacenamiento para almacenar los datos. Puede usar una cuenta de almacenamiento existente, o puede crear una nueva cuenta de almacenamiento al crear el área de trabajo de aprendizaje del equipo (si tiene cuota para crear una nueva cuenta de almacenamiento).

<!-- These instructions no longer work, but I'm not sure what to replace them with
To see if you can create a new storage account, in the Classic Portal, go to **Settings** and then click **Usage**.
-->

![Crear área de trabajo][screen2]

Después de crea el área de trabajo de aprendizaje de equipo, puede iniciar sesión Studio de aprendizaje de equipo mediante la cuenta de Microsoft especificado como propietario del área de trabajo. Si aparece el mensaje de error "Área de trabajo no encontrado" (similar a la siguiente captura de pantalla), utilice los siguientes pasos para eliminar las cookies de explorador.

![Área de trabajo no encontrado][screen3]

**Para eliminar cookies de explorador**

Si usa Internet Explorer, haga clic en el botón de **Herramientas** en la esquina superior derecha y seleccione **Opciones de Internet**.  

![Opciones de Internet][screen4]

En la pestaña **General** , haga clic en **Eliminar...**

![Pestaña General][screen5]

En el cuadro de diálogo **Eliminar el historial de exploración** , asegúrese de que **las Cookies y datos del sitio Web** está seleccionada y haga clic en **Eliminar**.

![Eliminar las cookies][screen6]

Después de eliminarán las cookies, reinicie el explorador y, a continuación, vaya a la página de [Aprendizaje de Microsoft Azure máquina](https://studio.azureml.net) . Cuando se le pida un nombre de usuario y contraseña, escriba la misma cuenta de Microsoft especificado como propietario del área de trabajo.

Nuestro objetivo es facilitar el aprendizaje del equipo es tan transparente como sea posible. Envíe los comentarios y problemas en el [foro de aprendizaje del equipo de Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) para ayudarnos a servirle mejor.

[screen1]:media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]:media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]:media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]:media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]:media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]:media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png
