<properties
   pageTitle="¿Qué es Microsoft Power BI incrustado?"
   description="Power BI incrustado permite integrar informes de Power BI en la web o de aplicaciones móviles para que no es necesario crear soluciones personalizadas para visualizar datos para los usuarios"
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

# <a name="what-is-microsoft-power-bi-embedded"></a>¿Qué es Microsoft Power BI incrustados?

Con **Power BI incrustado**, puede integrar informes de Power BI en las aplicaciones web o móviles.

![](media\powerbi-embedded-whats-is\what-is.png)

Power BI incrustado es un **servicio de Azure** que permite a los desarrolladores de aplicación superficies experiencias de datos de Power BI dentro de sus aplicaciones y de proveedores de software independientes. Como desarrollador, había creado aplicaciones y esas aplicaciones tienen sus propios usuarios y el conjunto de características. Estas aplicaciones también pueden ocurrir que algunos elementos de datos integrados como los gráficos e informes que ahora pueden ser ofrecidos por Microsoft Power BI incrustado. Los usuarios no necesitan una cuenta de Power BI para utilizar la aplicación. Puede seguir iniciar sesión en su aplicación como antes y ver e interactuar con la experiencia de reporting sin necesidad de cualquier licencia adicional de Power BI.

## <a name="licensing-for-microsoft-power-bi-embedded"></a>Licencias de Microsoft Power BI incrustados

En el modelo de uso de **Microsoft Power BI incrustados** , la licencia de Power BI no es la responsabilidad del usuario final.  En su lugar, **representa** comprado el desarrollador de la aplicación que consume los efectos visuales y se cargará a la suscripción que es el propietario de los recursos.

## <a name="microsoft-power-bi-embedded-conceptual-model"></a>Microsoft Power BI incrustados modelo Conceptual

![](media\powerbi-embedded-whats-is\model.png)

Como cualquier otro servicio en Azure recursos para Power BI incrustado se suministran a través de las [API de BRAZO de Azure](https://msdn.microsoft.com/library/mt712306.aspx). En este caso, el recurso que se aprovisiona es una **Colección de área de trabajo de Power BI**.

## <a name="workspace-collection"></a>Colección de área de trabajo

Una **Colección de área de trabajo** es Azure contenedor de nivel superior para los recursos que contiene 0 o más **áreas de trabajo**.  Una **colección** de **área de trabajo** tiene todas las propiedades de Azure estándar, así como la siguiente:

-   **Teclas de acceso** : claves utilizadas al llamar de forma segura a las API de BI de Power (descrito en una sección posterior).
-   **Los usuarios** : los usuarios de Azure Active Directory (AAD) que tienen derechos de administrador para administrar la colección de área de trabajo de Power BI a través del portal de Azure o API de ARM.
-   **Región** : como parte de aprovisionamiento de una **Colección de área de trabajo**, puede seleccionar una región estén configurados en. Para obtener más información, vea [Áreas de Azure](https://azure.microsoft.com/regions/).

## <a name="workspace"></a>Área de trabajo

Un **área de trabajo** es un contenedor de contenido de Power BI, lo cual puede incluir conjuntos de datos, informes y paneles. Un **área de trabajo** está vacío cuando se crea por primera vez. En vista previa, deberá crear todo el contenido con Power BI Desktop y se cargará a una de las áreas de trabajo con las [API de REST de Power BI](http://docs.powerbi.apiary.io/reference).

## <a name="using-workspace-collections-and-workspaces"></a>Usar áreas de trabajo y colecciones de área de trabajo
**Colecciones de área de trabajo** y **áreas de trabajo** son contenedores de contenido que se usan y se organizan en la forma que mejor se adapte al diseño de la aplicación que está creando. Habrá varias formas diferentes que podría organizar el contenido dentro de ellas. Puede colocar todo el contenido dentro de un área de trabajo y, a continuación, más adelante con tokens de aplicación subdividen aún más el contenido entre los clientes. También puede colocar todos sus clientes en áreas de trabajo separadas para que hay algunas separación entre ellas. O bien, puede optar por organizar a los usuarios por región en lugar de por cliente. Este diseño flexible permite elegir la mejor forma de organizar el contenido.

## <a name="cached-datasets"></a>Conjuntos de datos en caché

Conjuntos de datos en caché pueden usarse en la vista previa.  Sin embargo, no puede actualizar los datos almacenados en caché cuando se ha cargado en **Microsoft Power BI incrustado**.

## <a name="authentication-and-authorization-with-app-tokens"></a>Autenticación y la autorización con tokens de aplicación

**Microsoft Power BI incrustado** pasa a la aplicación para realizar la autenticación de usuario y la autorización. No hay ningún requisito explícita que los usuarios finales ser clientes de Azure Active Directory (AD Azure).  En su lugar, la aplicación se expresa a **Microsoft Power BI incrustado** autorización para representar un informe de Power BI con **Símbolos de autenticación de la aplicación (aplicación Tokens)**.  Estos **Tokens de aplicación** se crean según sea necesario cuando la aplicación que desea representar un informe.  Vea [Tokens de aplicación](power-bi-embedded-get-started-sample.md#key-flow).

![](media\powerbi-embedded-whats-is\app-tokens.png)

**Símbolos de autenticación de la aplicación (aplicación Tokens)** se utilizan para autenticarse **Incrustados de Microsoft Power BI**.  Hay tres tipos de **Símbolos de la aplicación**:

1.  Aprovisionamiento Tokens - utilizados para el aprovisionamiento de una nueva **área de trabajo** en una **Colección de área de trabajo**
2.  Tokens de desarrollo - utilizados para realizar llamadas directamente a las **API de REST de Power BI**
3.  Incrustar Tokens - utilizados para realizar llamadas para representar un informe en el iframe incrustado

Estos tokens se usan para las diversas fases de sus interacciones con **Microsoft Power BI incrustado**.  Los símbolos están diseñados para que puede delegar permisos desde la aplicación Power BI. Para obtener más información, vea [Flujo Token de aplicación](power-bi-embedded-app-token-flow.md).

## <a name="see-also"></a>Vea también
- [Escenarios comunes de Microsoft Power BI incrustado](power-bi-embedded-scenarios.md)
- [Introducción a Microsoft Power BI incrustado](power-bi-embedded-get-started.md)
