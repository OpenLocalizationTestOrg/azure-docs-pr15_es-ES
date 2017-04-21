<properties
    pageTitle="Tomcat en una máquina virtual | Microsoft Azure"
    description="Este tutorial usa recursos creados con el modelo de implementación clásica y muestra cómo crear una máquina Virtual de Windows y configúrelo para ejecutar el servidor de aplicaciones de Apache Tomcat."
    services="virtual-machines-windows"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="azure-service-management" />

<tags
    ms.service="virtual-machines-windows"
    ms.workload="web"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="how-to-run-a-java-application-server-on-a-virtual-machine-created-with-the-classic-deployment-model"></a>Cómo ejecutar un servidor de aplicaciones de Java en una máquina virtual creada con el modelo de implementación clásica

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Con Azure, puede usar una máquina virtual para proporcionar funciones de servidor. Por ejemplo, una máquina virtual que se ejecuta en Azure puede estar configurada para hospedar un servidor de aplicaciones Java, como Apache Tomcat. Después de completar a esta guía, tendrá una descripción de cómo crear una máquina virtual que se ejecuta en Azure y configúrelo para ejecutar un servidor de aplicaciones Java.

Aprenderá:

* Cómo crear una máquina virtual que tiene un Kit de desarrollo de Java (JDK) ya instalado.
* Cómo iniciar sesión en la máquina virtual remotamente.
* Cómo instalar a un servidor de aplicaciones de Java en su máquina virtual.
* Cómo crear un extremo de la máquina virtual.
* Cómo abrir un puerto en el firewall para el servidor de aplicaciones.

Para el propósito de este tutorial, se instalará un servidor de aplicaciones de Apache Tomcat en una máquina virtual. La instalación completada se traducirán en una instalación de Tomcat como la siguiente.

![Máquina virtual ejecuta Apache Tomcat][virtual_machine_tomcat]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Para crear una máquina virtual

1. Inicie sesión en el [portal de clásico de Azure](https://manage.windowsazure.com).
2. Haga clic en **nuevo**, haga clic en **calcular**, haga clic en la **Máquina Virtual**y, a continuación, haga clic en **Desde la Galería**.
3. En el cuadro de diálogo **Seleccionar imagen de máquina Virtual** , seleccione **JDK 7 Windows Server 2012**.
Tenga en cuenta que está disponible si tiene aplicaciones heredadas que no están listos para que se ejecute en JDK 7 **JDK 6 Windows Server 2012** .
4. Haga clic en **siguiente**.
5. En el cuadro de diálogo **configuración de máquina Virtual** :
    1. Especifique un nombre para la máquina virtual.
    2. Especificar el tamaño para la máquina virtual.
    3. Escriba un nombre para el administrador en el campo **Nombre de usuario** . Recuerde que este nombre y la contraseña que se especifique a continuación, que usa cuando inicie sesión remotamente la máquina virtual.
    4. Escriba una contraseña en el campo **nueva contraseña** y vuelva a escribirla en el campo **Confirmar** . Esta es la contraseña de la cuenta de administrador.
    5. Haga clic en **siguiente**.
6. En el cuadro de diálogo **configuración de la máquina Virtual** siguiente:
    1. **Servicio de nube**, use el valor predeterminado de **crear un nuevo servicio de nube**.
    2. El valor de **nombre DNS del servicio de nube** debe ser único en cloudapp.net. Si es necesario modificar este valor para que ese Azure indica que sea único.
    2. Especificar una región, grupo afinidad o una red virtual. Para el propósito de este tutorial, especifique una región como **US oeste**.
    2. Para la **Cuenta de almacenamiento**, seleccione **usar una cuenta de almacenamiento generada automáticamente**.
    3. Para **Establecer la disponibilidad**, seleccione **(ninguno)**.
    4. Haga clic en **siguiente**.
7. En el último cuadro de diálogo **configuración de la máquina Virtual** :
    1. Aceptar las entradas de extremo predeterminado.
    2. Haga clic en **Finalizar**.

## <a name="to-remotely-sign-in-to-your-virtual-machine"></a>Para iniciar sesión en la máquina virtual remotamente

1. Inicie sesión en el [portal de clásico de Azure](https://manage.windowsazure.com).
2. Haga clic en **máquinas virtuales de Windows**.
3. Haga clic en el nombre de la máquina virtual que desee iniciar sesión en.
4. Una vez comenzada la máquina virtual, un menú emergente en la parte inferior de la página permite las conexiones.
5. Haga clic en **Conectar**.
6. Responder a las preguntas según sea necesario para conectarse a la máquina virtual. Esto supondrá guardar o abrir el archivo RDP que contiene los detalles de la conexión. Es posible que tenga que copiar la dirección url: puerto como la última parte de la primera línea del archivo RDP y pegar en una aplicación de inicio de sesión remota.

## <a name="to-install-a-java-application-server-on-your-virtual-machine"></a>Instalar un servidor de aplicaciones de Java en la máquina virtual

Puede copiar un servidor de aplicaciones Java a su equipo virtual, o puede instalar a un servidor de aplicaciones Java a través de un instalador.

Para el propósito de este tutorial, se instalará Tomcat.

1. Cuando ha iniciado sesión la máquina virtual, abra una sesión de explorador en [Apache Tomcat](http://tomcat.apache.org/download-70.cgi).
2. Haga doble clic en el vínculo para el **Instalador de servicio de Windows de 32 bits o 64 bits**. Mediante esta técnica, Tomcat se instala como un servicio de Windows.
3. Cuando se le pida, elija ejecutar el instalador.
4. En el Asistente de **Configuración de Apache Tomcat** , siga las indicaciones para instalar Tomcat. Para el propósito de este tutorial, acepte la configuración predeterminada es adecuado. Cuando llegue el cuadro de diálogo **completar el Asistente de configuración de Apache Tomcat** , puede comprobar si lo desea **Ejecutar Apache Tomcat** para tener Tomcat iniciar ahora. Haga clic en **Finalizar** para completar el proceso de instalación de Tomcat.

## <a name="to-start-tomcat"></a>Para iniciar Tomcat
Si no ha escogido ejecutar Tomcat en el cuadro de diálogo **completar el Asistente de configuración de Apache Tomcat** , inicio al abrir un símbolo del sistema en el equipo virtual y ejecutar **net iniciar Tomcat7**.

Ahora debe ver Tomcat ejecutando si ejecutar el Explorador de la máquina virtual y abrir <http://localhost: 8080>.

Para ver Tomcat ejecutando desde equipos externos, debe crear un extremo y abrirlo.

## <a name="to-create-an-endpoint-for-your-virtual-machine"></a>Para crear un extremo de la máquina virtual
1. Inicie sesión en el [portal de clásico de Azure](https://manage.windowsazure.com).
2. Haga clic en **máquinas virtuales de Windows**.
3. Haga clic en el nombre de la máquina virtual que se está ejecutando el servidor de aplicaciones Java.
4. Haga clic en los **extremos**.
5. Haga clic en **Agregar**.
6. En el cuadro de diálogo **Agregar extremo** , asegúrese de **Agregar independiente extremo** está seleccionada y, a continuación, haga clic en **siguiente**.
7. En el cuadro de diálogo **Detalles de extremo de nuevo** :
    1. Especifique un nombre para el extremo; Por ejemplo, **HttpIn**.
    2. Especifique el protocolo **TCP** .
    3. Especifique **80** para el puerto público.
    4. Especificar **8080** para el puerto privado.
    5. Haga clic en el botón **completado** para cerrar el cuadro de diálogo. Ahora se creará el extremo.

## <a name="to-open-a-port-in-the-firewall-for-your-virtual-machine"></a>Para abrir un puerto en el firewall para el equipo virtual
1. Inicie sesión en la máquina virtual.
2. Haga clic en **Inicio de Windows**.
3. Haga clic en **Panel de Control**.
4. Haga clic en **sistema y seguridad**, haga clic en **Firewall de Windows**y, a continuación, haga clic en **Configuración avanzada**.
5. Haga clic en **Reglas de entrada**y, a continuación, haga clic en **Nueva regla**.
 ![Nueva regla de entrada][NewIBRule]
6. Para el **Tipo de regla**, seleccione **puerto**y, a continuación, haga clic en **siguiente**.
 ![Puerto nueva regla de entrada][NewRulePort]
7. En la pantalla de **puertos y protocolo** , seleccione **TCP**, especificar **8080** como **puerto local específico**y, a continuación, haga clic en **siguiente**.
 ![Nueva regla de entrada][NewRuleProtocol]
8. En la pantalla de la **acción** , seleccione **Permitir la conexión**y, a continuación, haga clic en **siguiente**.
 ![Nueva acción de regla de entrada][NewRuleAction]
9. En la pantalla de **perfil** , asegúrese de que están seleccionadas **dominio**, **privado**y **público** y, a continuación, haga clic en **siguiente**.
 ![Nuevo perfil de la regla de entrada][NewRuleProfile]
10. En la pantalla **nombre** , especifique un nombre para la regla, como **HttpIn** (no se requiere el nombre de la regla para que coincida con el nombre del extremo, sin embargo) y, a continuación, haga clic en **Finalizar**.  
 ![Nuevo nombre de regla de entrada][NewRuleName]

En este momento, debe ser el sitio Web de Tomcat visible en un explorador externo mediante una dirección URL del formulario * *http://*su\_DNS\_nombre*. cloudapp.net**donde ** *su\_DNS\_nombre*** es el nombre DNS que especificó al crear la máquina virtual.

## <a name="application-lifecycle-considerations"></a>Consideraciones de ciclo de vida de aplicación
* Podría crear su propio archivo de aplicación web (guerra) y agregar a la carpeta de **aplicaciones Web** . Por ejemplo, crear un proyecto de web dinámica servicio página JSP (Java) básico y exportar como un archivo de guerra, copie la guerra a la carpeta de **aplicaciones Web** de Apache Tomcat en la máquina virtual, a continuación, se ejecute en un explorador.
* De forma predeterminada cuando se instala el servicio Tomcat, está configurado para iniciarse manualmente. Puede elegir que se inicie automáticamente mediante el complemento de servicios. Inicie el complemento de servicios haciendo clic en **Inicio de Windows**, **Herramientas administrativas**y, a continuación, en **Servicios**. Haga doble clic en el servicio **Apache Tomcat** y establecer el **tipo de inicio** en **automático**.

    ![Establecer un servicio para iniciarse automáticamente][service_automatic_startup]

    La ventaja de disponer de inicio de Tomcat automáticamente es que se iniciará si se reinicia la máquina virtual (por ejemplo, después de instalarán las actualizaciones de software que requieren reiniciar el equipo).

## <a name="next-steps"></a>Pasos siguientes
Obtenga información sobre otros servicios (como el almacenamiento de Azure, bus de servicio y base de datos SQL) que desea incluir con las aplicaciones de Java mediante la visualización de la información disponible en el [Centro para desarrolladores de Java](https://azure.microsoft.com/develop/java/).

[virtual_machine_tomcat]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewInboundRule.png
[NewRulePort]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRulePort.png
[NewRuleProtocol]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleProtocol.png
[NewRuleAction]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleAction.png
[NewRuleName]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleName.png
[NewRuleProfile]: ./media/virtual-machines-windows-classic-java-run-tomcat-app-server/NewRuleProfile.png
