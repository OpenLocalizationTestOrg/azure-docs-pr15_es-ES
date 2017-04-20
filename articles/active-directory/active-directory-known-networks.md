<properties 
    pageTitle="Conocidos redes | Microsoft Azure" 
    description="Al configurar redes conocidas, puede evitar tener direcciones IP que pertenecen a su organización incluida en los complementos de inicio de sesión de varias regiones geográficas y complementos de inicio de sesión de las direcciones IP con informes de actividad sospechosa." 
    services="active-directory" 
    documentationCenter="" 
    authors="markusvi" 
    manager="femila"  
    editor=""/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="markvi"/>

# <a name="known-networks"></a>Redes conocidas


Puede usar informes de uso y acceso de Azure Active Directory para obtener visibilidad de la integridad y seguridad de directorio de su organización. Con esta información, un administrador de directorio puede determinar mejor donde puede encontrarse posibles riesgos de seguridad para que puedan planear adecuadamente a mitigar estos riesgos.

Es posible que los informes "*complementos de inicio de sesión de diversas localidades*" y "*complementos de inicio de sesión de las direcciones IP con actividad sospechosa*" marcar incorrectamente direcciones IP que realmente pertenecen a su organización. 

Por ejemplo, esto puede suceder cuando: 

- Un usuario en su SAN Cristóbal office ha iniciado sesión en forma remota a su centro de datos de San Francisco activa el informe "Firmar ins desde varias regiones geográficas" 

- Un usuario de su organización intenta sesión varias veces con los desencadenadores de contraseña incorrecta del informe "Firmar ins de direcciones IP con actividad sospechosa" 

Para evitar estos casos de generación de informes de seguridad confuso, deberá agregar conocidos intervalos de direcciones IP a la lista de la dirección IP pública de su organización.    


###<a name="to-add-your-organizations-public-ip-address-ranges-perform-the-following-steps"></a>Para agregar intervalos de direcciones IP públicas de su organización, realice los pasos siguientes: 

1.  Inicio de sesión en el [portal de administración de Azure](https://manage.windowsazure.com).

2.  En el panel izquierdo, haga clic en **Active Directory**. <br><br>![Cómo funciona la detección de la aplicación de nube](./media/active-directory-known-networks/known-netwoks-01.png)

3.  En la ficha **directorio** , seleccione el directorio.

4.  En el menú de la parte superior, haga clic en **Configurar**. <br><br>![Cómo funciona la detección de la aplicación de nube](./media/active-directory-known-networks/known-netwoks-02.png)

5.  En la ficha configurar, vaya a **los intervalos de direcciones IP públicos organizaciones** <br><br>![Cómo funciona la detección de la aplicación de nube](./media/active-directory-known-networks/known-netwoks-03.png)

6.  Haga clic en **Agregar intervalos de direcciones IP conocido**.

7.  Agregar los intervalos de direcciones en el cuadro de diálogo que aparece y, a continuación, haga clic en el botón Comprobar cuando haya terminado. <br><br>![Cómo funciona la detección de la aplicación de nube](./media/active-directory-known-networks/known-netwoks-04.png)


**Recursos adicionales**


* [Ver los informes de access y el uso](active-directory-view-access-usage-reports.md)
* [Complementos de inicio de sesión de las direcciones IP con la actividad sospechosa](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [Complementos de inicio de sesión de diversas localidades](active-directory-reporting-sign-ins-from-multiple-geographies.md)


