<properties
   pageTitle="Título de la página que se muestra en los resultados de búsqueda y la pestaña del explorador"
   description="Descripción de artículo que se mostrarán en las páginas de inicio y en la mayoría de los resultados de búsqueda"
   services="service-name"
   documentationCenter="dev-center-name"
   authors="GitHub-alias-of-only-one-author"
   manager="manager-alias"
   editor=""
   tags="comma-separates-additional-tags-if-required"/>

<tags
   ms.service="required"
   ms.devlang="may be required"
   ms.topic="article"
   ms.tgt_pltfrm="may be required"
   ms.workload="required"
   ms.date="mm/dd/yyyy"
   ms.author="Your MSFT alias or your full email address;semicolon separates two or more"/>

# <a name="title-maximum-120-characters-target-the-primary-keyword"></a>Título (máximo 120 caracteres, la palabra clave principal de destino)

_Usar palabras clave secundario de 2-3 en la descripción._

_Seleccione una de las siguientes declinaciones de responsabilidades dependiendo de su situación. Si el artículo es independiente del modelo de implementación, omita este._

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásico.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [learn-about-deployment-models](../../learn-about-deployment-models-both-include.md)]

## <a name="summary-optional-especially-when-the-article-is-short"></a>Resumen (opcional, especialmente cuando el artículo breve)

- _Describa brevemente el problema._
- _La sección de resumen es un buen lugar para usar otras palabras clave de los en el título, pero asegúrese de que no sea muy información adicional. Las frases deben flujo bien y fáciles de entender._
- _Excepciones (opcionales): lista de los escenarios relevantes que no están cubiertos en este artículo. Por ejemplo, "escenarios de Linux/OSS no se tratan en este artículo"._

_Si se trata de un artículo en el tema de la facturación, incluya la nota siguiente (la nota siguiente es ligeramente distinto al final de este artículo):_
> [AZURE.NOTE] Si necesita más ayuda en cualquier punto de este artículo, póngase [en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obtener el problema resuelto rápidamente.

_Si no es un artículo de facturación, utilice la siguiente referencia:_
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Síntoma

- _¿Qué medidas haría el usuario esté intentando completar?_
- _¿Qué es el error?_
- _¿Qué software y sistemas habría ha utilizado el usuario?_
- _¿Qué se muestran mensajes de error?_
- _Incluir la captura de pantalla si es posible._

## <a name="cause"></a>Causa

- _¿Qué hace este problema._

## <a name="solution"></a>Solución

- _Agregar capturas de pantalla si es posible._
- _Si hay varias soluciones, colocarlos en el orden de complejidad y proporcionar instrucciones sobre cómo elegir entre ellas._

| <em>Versión 1: El artículo es independiente del modelo de implementación</em> | <em>Versión 2: Pasos para el Administrador de recursos y clásica en gran medida son los mismos</em> | <em>Versión 3: Pasos para el Administrador de recursos y clásica principalmente son diferentes. <br />En este caso, utilice la <a href="https://github.com/Azure/azure-content-pr/blob/master/contributor-guide/custom-markdown-extensions.md#simple-selectors">técnica de selectores simples en Github</a>. <br />Nota: Artículos de la máquina virtual para excepciones ARM y no se debe usar el selector ARM/clásico.</em> |
|:------------------------------------------------------|:-----------------------------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <p><h3>Solución 1</h3><em>(el más sencillo y más eficaz)</em></p><ol><li>[Paso 1]</li><li>[Paso 2]</li></ol><p><h3>Solución 2</h3><em>(menos simple o eficaces)</em></p><ol><li>[Paso 1]</li><li>[Paso 2]</li></ol><br /><br /><br /><br /><br /><br /><br /><br /> | <p><h3>Solución 1</h3><em>(el más sencillo y más eficaz)</em></p><ol><li>[Paso 1]</li><li>Si utiliza el modelo de implementación clásica, [hacerlo].<br />Si utiliza el modelo de implementación de administrador de recursos, [hacerlo].</li><li>[Paso 3]</li></ol><p><h3>Solución 2</h3><em>(menos simple o eficaces)</em></p><ol><li>[Paso 1]</li><li>Si utiliza el modelo de implementación clásica, [hacerlo].<br />Si utiliza el modelo de implementación de administrador de recursos, [hacerlo].</li><li>[Paso 3]</li></ol> | <img src="media/markdown-template-for-support-articles-symptom-cause-resolution/rm-classic.png" alt="ARM-Classic"><p><h3>Solución 1</h3><em>(el más sencillo y más eficaz)</em></p><ol><li>[Paso 1]</li><li>[Paso 2]</li></ol><p><h3>Solución 2</h3><em>(menos simple o eficaces)</em></p><ol><li>[Paso 1]</li><li>[Paso 2]</li></ol><br /><br /><br /><br /> |

## <a name="next-steps"></a>Pasos siguientes
_Incluir esta sección si hay-1 3 concretas, relevancia pasos siguientes, que debe tener el usuario. Eliminar si no hay ningún paso siguiente. No es un lugar para obtener una lista de vínculos. Si incluye vínculos a los pasos siguientes, asegúrese de incluir texto que explique por qué los pasos siguientes son importantes y relevantes._

_Si se trata de un artículo en el tema de la facturación, incluya la nota siguiente (la nota siguiente es ligeramente diferente al principio de este artículo):_
> [AZURE.NOTE] Si aún tiene más preguntas, póngase [en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obtener el problema resuelto rápidamente.
