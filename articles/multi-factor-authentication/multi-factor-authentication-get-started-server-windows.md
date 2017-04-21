<properties 
    pageTitle="Servidor de autenticación multifactor de autenticación y de Windows Azure"
    description="Esta es la página de autenticación multifactor de Azure que le ayudarán a implementar la autenticación de Windows y el servidor de autenticación multifactor de Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>Servidor de autenticación multifactor de autenticación y de Windows Azure

La sección autenticación de Windows permite que el administrador habilitar y configurar la autenticación de Windows para una o más aplicaciones.  A continuación se muestra una lista de cosas que debe tener en cuenta antes de configurar la autenticación de Windows.

-  es necesario reiniciar antes de la autenticación multifactor de Azure para Terminal Services se activa.
-  Si 'Coincidencia de usuario requieren autenticación multifactor de Azure' está activada y no está en la lista de usuarios, no podrá iniciar sesión en el equipo después de reiniciar el equipo.
-  Direcciones IP de confianza depende de si la aplicación puede proporcionar la IP del cliente con la autenticación. Es compatible actualmente solo Terminal Services.  







>[AZURE.NOTE]Esta característica no se admite seguro Terminal Services en Windows Server 2012 R2.




## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>Para proteger una aplicación con la autenticación de Windows, utilice el procedimiento siguiente.

1. Haga clic en el icono de autenticación de Windows en el servidor de autenticación multifactor de Azure.
![Autenticación de Windows](./media/multi-factor-authentication-get-started-server-windows/windowsauth.png)
2. Active la casilla de verificación de autenticación de Windows habilitar. De forma predeterminada, esta casilla está desactivada.
3. La ficha aplicaciones permite al administrador que configure una o más aplicaciones para la autenticación de Windows.
4. Seleccione un servidor o la aplicación: especifique si se habilita la aplicación de servidor. Haga clic en Aceptar.
5. Haga clic en Agregar... botón.
6. La ficha IP fiables permite omitir las sesiones de Azure multifactor autenticación de Windows que provienen de direcciones IP específicas. Por ejemplo, si los empleados utilizan la aplicación de la oficina y de inicio, puede decidir que no desea que sus teléfonos suenen Azure con autenticación multifactor mientras se encuentra en la oficina. Para ello, debe especificar la subred office como entrada de IP fiables.
7. Haga clic en Agregar... botón.
8. Si desea omitir una única dirección IP, seleccione IP única.
9. Seleccione el rango de IP si desea omitir todo un rango IP. 10.63.193.1-10.63.193.100 de ejemplo.
10. Seleccione subred si desea especificar un intervalo de direcciones IP utilizando la notación de subred. Escriba IP de comienzo de subred y elija la máscara de red adecuada de la lista desplegable.
11. Haga clic en el botón Aceptar.
