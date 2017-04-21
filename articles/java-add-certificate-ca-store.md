<properties 
    pageTitle="Agregar un certificado en el almacén de CA de Java | Microsoft Azure" 
    description="Obtenga información sobre cómo agregar un certificado de entidad emisora de certificados en el almacén de certificados (cacerts) de la entidad emisora de certificados de Java para servicio de Twilio o Bus de servicio de Azure." 
    services="" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="adding-a-certificate-to-the-java-ca-certificates-store"></a>Agregar un certificado en el almacén de certificados de entidad emisora de certificados de Java
Los pasos siguientes muestran cómo agregar un certificado de entidad emisora de certificados al almacén de certificados (cacerts) Java CA. El ejemplo utilizado es para el certificado de entidad emisora de certificados requerido por el servicio de Twilio. Información más adelante en el tema describe cómo instalar el certificado de entidad emisora de certificados para el Bus de servicio de Azure. 

Puede usar la herramienta de claves para agregar el certificado de entidad emisora de certificados antes de comprimir el JDK y agregarlo a la carpeta de **approot** de su proyecto Azure, o puede ejecutar una tarea de inicio de Azure que usa la herramienta de claves para agregar el certificado. Este ejemplo se supone que va a agregar un certificado de entidad emisora de certificados antes del JDK se zip. Además, un certificado específico de la entidad emisora de certificados que se utilizará en el ejemplo, pero los pasos de obtener un certificado de entidad emisora de certificados diferentes e importar en la tienda cacerts sería similares.

## <a name="to-add-a-certificate-to-the-cacerts-store"></a>Para agregar un certificado en el almacén de cacerts

1. En el símbolo del sistema que se establece en la carpeta de **jdk\jre\lib\security** de su JDK, ejecute el siguiente procedimiento para ver qué certificados están instalados:

    `keytool -list -keystore cacerts`

    Se le pedirá la contraseña de la tienda. La contraseña predeterminada es **cambiarlo**. (Si desea cambiar la contraseña, consulte la documentación de la herramienta de claves en <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>). En este ejemplo se da por hecho que el certificado con MD5 huella digital 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 no está en la lista y que desea importar (es necesario este certificado determinado por el servicio de la API de Twilio).
2. Obtener el certificado de la lista de certificados que se indican a [GeoTrust de certificados raíz](http://www.geotrust.com/resources/root-certificates/). Haga clic en el vínculo para el certificado con 35:DE:F4:CF de número de serie y guárdelo en la carpeta **jdk\jre\lib\security** . Para el propósito de este ejemplo, que se guardó en un archivo llamado **Equifax\_seguro\_certificado\_Authority.cer**.
3. Importar el certificado mediante el siguiente comando:

    `keytool -keystore cacerts -importcert -alias equifaxsecureca -file Equifax_Secure_Certificate_Authority.cer`

    Cuando se le indique que confíe en este certificado, si el certificado tiene MD5 huella 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4, responder escribiendo **y**.
4. Ejecute el comando siguiente para asegurarse de que el certificado de entidad emisora de certificados se ha importado correctamente:

    `keytool -list -keystore cacerts`

5. Comprima el JDK y agregarlo a la carpeta de **approot** de su proyecto de Azure.

Para obtener información acerca de la herramienta de claves, consulte <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.

## <a name="azure-root-certificates"></a>Certificados raíz de Azure

Las aplicaciones que usan servicios de Azure (como Bus de servicio de Azure) necesitan confiar en el certificado de raíz de Baltimore CyberTrust. (A partir del 15 de abril de 2013 Azure comenzado migrar desde la raíz Global de CyberTrust GTE en la raíz de Baltimore CyberTrust. Esta migración tardó varios meses).

La Baltimore certificado puede que ya esté instalado en la tienda cacerts, así que acordarse de ejecutar la **herramienta de claves-lista** comando primero para ver si ya existe.

Si necesita agregar la raíz de Baltimore CyberTrust, tiene número de serie 02:00:00:b9 y SHA1 huella d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2 c: 78:db:28:52:ca:e4:74. Puede descargarse desde <https://cacert.omniroot.com/bc2025.crt>, guardado en un archivo local con la extensión **.cer**y, a continuación, importar mediante la **herramienta de claves** como se indicó anteriormente.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de los certificados raíz de Azure, consulte [Migración de certificados raíz de Azure](http://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx).

Para obtener más información acerca de Java, consulte el [Centro para desarrolladores de Java](/develop/java/).
