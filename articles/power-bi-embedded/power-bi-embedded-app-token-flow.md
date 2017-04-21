<properties
   pageTitle="Autenticar y autorizar con Power BI incrustado"
   description="Autenticar y autorizar con Power BI incrustado"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="authenticating-and-authorizing-with-power-bi-embedded"></a>Autenticación y autorización con Power BI incrustado

El servicio Power BI incrustado utiliza **claves** y **Símbolos de la aplicación** para la autenticación y la autorización, en lugar de autenticación de usuario final explícitos. En este modelo, la aplicación administra la autenticación y la autorización para los usuarios finales. Cuando es necesario, la aplicación crea y envía los símbolos de la aplicación que indica a nuestro servicio para representar el informe solicitado. Este diseño no requiere la aplicación para usar Azure Active Directory para la autenticación de usuario y la autorización, aunque puede seguir.

## <a name="two-ways-to-authenticate"></a>Dos maneras para autenticar

**Clave** : puede usar las teclas para todas las llamadas de API de REST incrustado de Power BI. Las claves pueden encontrarse en el **portal de Azure** haciendo clic en **toda la configuración** y, a continuación, **las teclas de acceso**. Siempre tratar la clave como si fuera una contraseña. Estas teclas tienen permisos para realizar las API de REST llamar a un conjunto determinado de área de trabajo.

Para usar una clave en una llamada REST, agregue el encabezado de autorización siguientes:            

    Authorization: AppKey {your key}

**Símbolo de aplicación** - aplicación tokens se usan para todas las solicitudes de incrustación. Está diseñados para ejecutarse de cliente, por lo que están restringidos a un solo informe y es recomendable establecer una fecha de caducidad.

Tokens de aplicación son un JWT (JSON Web Token) que está firmado por una de las claves.

El token de aplicación puede contener las siguientes afirmaciones:

| Reclamar      | Descripción        |
|--------------|------------|
| **ver**      | La versión de token de aplicación. 0.2.0 es la versión actual.       |
| **y**      | El destinatario del token. Para usar Power BI incrustado: "https://analysis.windows.net/powerbi/api".  |
| **ISS**      |  Una cadena que indica que la aplicación que emite el token.    |
| **tipo**     | El tipo de token de aplicación que se va a crear. Actual el único tipo admitido es **incrustar**.   |
| **WCN**      | El token de nombre de la colección de área de trabajo se publica para.  |
| **WID**      | Id. de área de trabajo el token se publica para.  |
| **eliminar**      | Se publica para el token de ID de informe.     |
| **nombre de usuario** (opcional) |  Usa RLS, esta es una cadena que puede ayudarle a identificar al usuario cuando la aplicación de reglas RLS. |
| **roles** (opcional)   |   Una cadena que contiene los roles para seleccionar al aplicar las reglas de seguridad de nivel de fila. Si pasar más de una función, se deben pasar como una matriz de cadena.    |
| **exp** (opcional)    |   Indica el tiempo en el que va a expirar el token. Estos deben pasar como las marcas de tiempo de Unix.   |
| **NBF** (opcional)    |   Indica la hora en que inicia el token no es válida. Estos deben pasar como las marcas de tiempo de Unix.   |

Un símbolo de la aplicación de ejemplo tendrá este aspecto:

![](media\power-bi-embedded-app-token-flow\power-bi-embedded-app-token-flow-sample-coded.png)


Cuando descodificar, tendrá un aspecto similar a este:

![](media\power-bi-embedded-app-token-flow\power-bi-embedded-app-token-flow-sample-decoded.png)


## <a name="heres-how-the-flow-works"></a>¿Cómo funciona el flujo

1. Copiar las claves API a la aplicación. Puede obtener las teclas en **El Portal de Azure**.

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

2. Token declara una notificación y tiene una fecha de caducidad.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-2.png)

3. Token obtiene firmado con un teclas de acceso de la API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-3.png)

4. Solicitudes de usuario para ver un informe.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-4.png)

5.  Token se valida con un teclas de acceso de la API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-5.png)

6.  Power BI incrustado envía un informe de usuario.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-6.png)

Después de **Power BI incrustado** envía al usuario de un informe, el usuario puede ver el informe en la aplicación personalizada. Por ejemplo, si ha importado la [PBIX de datos de ventas de análisis de ejemplo](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), la aplicación web de ejemplo tendría un aspecto similar a este:

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)

## <a name="see-also"></a>Vea también
- [Introducción a Microsoft Power BI incrustado muestra](power-bi-embedded-get-started-sample.md)
- [Escenarios comunes de Microsoft Power BI incrustado](power-bi-embedded-scenarios.md)
- [Introducción a Microsoft Power BI incrustado](power-bi-embedded-get-started.md)
