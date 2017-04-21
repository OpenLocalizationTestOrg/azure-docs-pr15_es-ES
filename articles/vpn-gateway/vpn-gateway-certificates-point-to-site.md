<properties 
   pageTitle="Crear certificados de firma automática para una red virtual sitio a punto entre conexiones locales usando makecert | Microsoft Azure"
   description="Este artículo contiene pasos para usar makecert para crear los certificados autofirmados en Windows 10."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/22/2016"
   ms.author="cherylmc" />

# <a name="working-with-self-signed-certificates-for-point-to-site-connections"></a>Trabajar con certificados firmados para conexiones de sitio a punto

Este artículo le ayudará a crear un certificado autofirmado mediante **makecert**y genere los certificados de cliente de ella. Los pasos se escriben para makecert en Windows 10. Makecert ha sido validada para crear los certificados que son compatibles con conexiones de P2S. 

Para conexiones de P2S, el método preferido de certificados es usar la solución de certificado de empresa, y asegúrese de que los certificados de cliente con el formato de valor de nombre común 'name@yourdomain.com', en lugar del formato 'NetBIOS nombre_dominio\nombre_usuario'.

Si no tiene una solución empresarial, un certificado autofirmado es necesario para permitir que los clientes P2S para conectarse a una red virtual. Somos conscientes que está desusado makecert, pero sigue siendo un método válido para crear certificados que son compatibles con conexiones de P2S. Estamos trabajando en otra solución para crear los certificados de firma automática, pero en este momento, makecert es el método preferido.

## <a name="create-a-self-signed-certificate"></a>Crear un certificado autofirmado

Makecert es una forma de crear un certificado autofirmado. Los siguientes pasos le guiarán por crear un certificado autofirmado mediante makecert. Estos pasos no son específicos de modelo de implementación. Son válidos para el Administrador de recursos y clásica.

### <a name="to-create-a-self-signed-certificate"></a>Para crear un certificado autofirmado

1. Desde un equipo que ejecuta Windows 10, descargue e instale el [Kit de desarrollo de Software de Windows (SDK) para Windows 10](https://dev.windows.com/en-us/downloads/windows-10-sdk).

2. Después de la instalación, puede encontrar la utilidad makecert.exe en esta ruta de acceso: C:\Program Files (x86) \Windows Kits\10\bin\<arco >. 
        
    Ejemplo:`C:\Program Files (x86)\Windows Kits\10\bin\x64`

3. A continuación, crear e instalar un certificado en el almacén de certificados personales en el equipo. En el ejemplo siguiente se crea un archivo *.cer* correspondientes que cargar en Azure al configurar P2S. Ejecute el siguiente comando, como administrador. Reemplazar *ARMP2SRootCert* y *ARMP2SRootCert.cer* con el nombre que desea usar para el certificado.<br><br>El certificado se encuentran en sus certificados - actual\Personal\Certificados actual.

        makecert -sky exchange -r -n "CN=ARMP2SRootCert" -pe -a sha1 -len 2048 -ss My "ARMP2SRootCert.cer"


###  <a name="rootpublickey"></a>Para obtener la clave pública

Como parte de la configuración de puerta de enlace VPN para conexiones de punto a sitios, la clave pública para el certificado raíz cargada en Azure.

1. Para obtener un archivo .cer del certificado, abra **certmgr.msc**. Haga clic en el certificado autofirmado raíz, haga clic en **todas las tareas**y, a continuación, haga clic en **Exportar**. Se abre el **Asistente para exportación de certificados**.

2. En el asistente, haga clic en **siguiente**, seleccione **No, no exportar la clave privada**y, a continuación, haga clic en **siguiente**.

3. En la página de **Formato de archivo de exportación** , seleccione **Base 64 codificado X.509 (. CER).** A continuación, haga clic en **siguiente**. 

4. En el **archivo de exportación**, **busque** la ubicación a la que desea exportar el certificado. Para **nombre de archivo**, el nombre del archivo de certificado. A continuación, haga clic en **siguiente**.

5. Haga clic en **Finalizar** para exportar el certificado.

 
### <a name="export-the-self-signed-certificate-optional"></a>Exporte el certificado autofirmado (opcional)

Desea exportar el certificado autofirmado y almacenar de forma segura. Si es necesario, más tarde puede instalarlo en otro equipo y generar más certificados de cliente o exportar otro archivo .cer. Cualquier equipo que tenga instalado un certificado de cliente y también está configurado con el cliente VPN adecuado configuración puede conectarse a una red virtual a través de P2S. Por este motivo, desea asegurarse de que los certificados de cliente se generan e instalados solo cuando sea necesario y que el certificado autofirmado se almacena de forma segura.

Para exportar el certificado autofirmado como un .pfx, seleccione el certificado raíz y usar los mismos pasos, como se describe en [exportar un certificado de cliente](#clientkey) para exportar.

## <a name="create-and-install-client-certificates"></a>Crear e instalar certificados de cliente

No instale el certificado autofirmado directamente en el equipo cliente. Debe generar un certificado de cliente desde el certificado autofirmado. A continuación, exportar e instale el certificado de cliente en el equipo cliente. Los pasos siguientes no son específicos de modelo de implementación. Son válidos para el Administrador de recursos y clásica.

### <a name="part-1---generate-a-client-certificate-from-a-self-signed-certificate"></a>Parte 1: generar un certificado de cliente desde un certificado autofirmado

Los siguientes pasos le guiará a través de una forma de generar un certificado de cliente desde un certificado autofirmado. Puede generar varios certificados de cliente desde el mismo certificado. Cada certificado de cliente puede exportarse e instalado en el equipo cliente. 

1. En el mismo equipo que usó para crear el certificado autofirmado, abra un símbolo del sistema como administrador.

2. En este ejemplo, "ARMP2SRootCert" hace referencia al certificado autofirmado que ha generado. 
    - Cambiar *"ARMP2SRootCert"* en el nombre de la raíz autofirmado que generan el certificado de cliente desde. 
    - Cambiar *ClientCertificateName* el nombre que desea generar un certificado de cliente para que sea. 


    Modificar y ejecutar el ejemplo para generar un certificado de cliente. Si ejecuta el ejemplo siguiente sin modificarlo, el resultado es un certificado de cliente con el nombre ClientCertificateName en el almacén de certificados personales que generó de certificado raíz ARMP2SRootCert.

        makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "ARMP2SRootCert" -is my -a sha1

4. Todos los certificados se almacenan en sus 'certificados - actual\Personal\Certificados actual' almacenar en su equipo. Puede generar varios certificados de cliente según sea necesario basándose en este procedimiento.

### <a name="clientkey"></a>Parte 2: exportar un certificado de cliente

1. Para exportar un certificado de cliente, abra **certmgr.msc**. Haga clic en el certificado de cliente que desea exportar, haga clic en **todas las tareas**y, a continuación, haga clic en **Exportar**. Se abre el **Asistente para exportación de certificados**.

2. En el asistente, haga clic en **siguiente**, seleccione **exportar la clave privada**y, a continuación, haga clic en **siguiente**.

3. En la página de **Formato de archivo de exportación** , puede dejar la configuración predeterminada seleccionada. A continuación, haga clic en **siguiente**. 
 
4. En la página de **seguridad** , debe proteger la clave privada. Si selecciona usar una contraseña, asegúrese de recordar la contraseña que ha configurado para este certificado o grabar. A continuación, haga clic en **siguiente**.

5. En el **archivo de exportación**, **busque** la ubicación a la que desea exportar el certificado. Para **nombre de archivo**, el nombre del archivo de certificado. A continuación, haga clic en **siguiente**.

6. Haga clic en **Finalizar** para exportar el certificado.  

### <a name="part-3---install-a-client-certificate"></a>Parte 3: instalar un certificado de cliente

Cada cliente que desea conectarse a una red virtual mediante una conexión punto a sitio debe tener instalado un certificado de cliente. Este certificado es además del paquete de configuración de VPN necesario. Los siguientes pasos le guíe durante la instalación manual de certificado de cliente.

1. Busque y copie el archivo *.pfx* en el equipo cliente. En el equipo cliente, haga doble clic en el archivo *.pfx* instalar. Dejar la **Ubicación del almacén** como **Usuario actual**y luego haga clic en **siguiente**.

2. En el **archivo** para importar página, no realice los cambios. Haga clic en **siguiente**.

3. En la página de **protección de clave privada** , escriba la contraseña para el certificado si utiliza una, o compruebe que la entidad de seguridad que va a instalar el certificado es correcta, y luego haga clic en **siguiente**.

4. En la página **Almacén de certificados** , deje la ubicación predeterminada y, a continuación, haga clic en **siguiente**.

5. Haga clic en **Finalizar**. En la **Advertencia de seguridad** para la instalación del certificado, haga clic en **Sí**. El certificado se importa correctamente.

## <a name="next-steps"></a>Pasos siguientes

Continuar con la configuración del sitio de punto. 

- Para pasos de modelo de implementación de **Administrador de recursos** , vea [Configurar una conexión punto a sitio a una VNet con PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md). 
- Para pasos **clásica** del modelo de implementación, consulte [configurar un punto de sitio la conexión VPN para un VNet con el portal clásico](vpn-gateway-point-to-site-create.md).