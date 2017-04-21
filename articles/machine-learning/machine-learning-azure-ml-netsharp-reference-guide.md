<properties 
    pageTitle="Guía para el lenguaje de especificación de redes Neural Net # | Microsoft Azure" 
    description="Sintaxis de la neural Net # redes de lenguaje de especificación, junto con ejemplos de cómo crear un modelo de red neural personalizadas en Microsoft Azure m con neto#" 
    services="machine-learning" 
    documentationCenter="" 
    authors="jeannt" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="jeannt"/>



# <a name="guide-to-net-neural-network-specification-language-for-azure-machine-learning"></a>Guía de lenguaje de especificación de red neural Net # para aprendizaje del equipo de Azure

## <a name="overview"></a>Información general
NET # es un lenguaje desarrollado por Microsoft que se utiliza para definir arquitecturas de red neuronal para módulos de red neuronal de aprendizaje de Microsoft Azure máquina. En este artículo, aprenderá:  

-   Conceptos básicos de redes neurales
-   Requisitos de red neuronal y cómo definir los componentes principales
-   La sintaxis y palabras clave del lenguaje de especificación de red #
-   Ejemplos de redes de neurales personalizados creados con neto# 
    
[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]  

## <a name="neural-network-basics"></a>Conceptos básicos de red neuronal
Una estructura de red neuronal consta de ***nodos*** que se organizan en ***capas***y ponderada ***conexiones*** (o ***bordes***) entre los nodos. Las conexiones son direcciones y cada conexión tiene un nodo de ***origen*** y un ***destino*** .  

Cada ***capa trainable*** (ocultos o un nivel de salida) tiene uno o varios ***paquetes de conexión***. Un paquete de conexión consta de una capa de origen y una especificación de las conexiones de ese nivel de origen. Todas las conexiones en un paquete determinado compartan la misma ***capa de origen*** y la misma ***capa de destino***. En Net #, un paquete de la conexión se considera que pertenecen a la capa de destino del paquete.  
 
Neto # admite varios tipos de conexión de paquetes, que le permite personalizar las entradas de manera se asignan a capas ocultas y se asignan a las salidas.   

El valor predeterminado o un paquete estándar es un **paquete completo**, en el que está conectado cada nodo en la capa de origen a todos los nodos de la capa de destino.  

Además, neto # admite los siguientes cuatro tipos de paquetes de conexión avanzada:  

-   **Paquetes de filtrado**. El usuario puede definir un predicado mediante las ubicaciones de nodo de capa de origen y el nodo de la capa de destino. Nodos están conectados siempre que el predicado es verdadero.
-   **Paquetes convolutional**. El usuario puede definir los pequeños grupos de nodos en la capa de origen. Cada nodo de la capa de destino está conectado a un entorno de nodos en la capa de origen.
-   **Agrupación de paquetes** y **paquetes de normalización de respuesta**. Estos son similares a los paquetes de convolutional en que el usuario define los pequeños grupos de nodos en la capa de origen. La diferencia es que el grosor de los bordes en estos paquetes no es trainable. En su lugar, una función predefinida se aplica a los valores de nodo de origen para determinar el valor de nodo de destino.  

Usar Net # para definir la estructura de una red neuronal permite definir estructuras complejas como redes neurales oscuro o convolutions de dimensiones arbitrarias, que se conocen para mejorar el aprendizaje en los datos como imagen, audio o vídeo.  

## <a name="supported-customizations"></a>Personalizaciones compatibles
La arquitectura de los modelos de red neuronal que cree en Azure el aprendizaje se puede personalizar ampliamente utilizando neto #. Puedes:  

-   Crear capas ocultas y controlar el número de nodos de cada capa.
-   Especificar cómo se pueden conectar entre sí capas.
-   Definir estructuras de conectividad especiales, como convolutions y grosor de uso compartido de paquetes.
-   Especificar funciones de activación diferente.  

Para obtener información detallada de la sintaxis del lenguaje de especificación, vea [Especificación de estructura](#Structure-specifications).  
 
Para obtener ejemplos de definir redes neurales algunas tareas de simplex a complejos, de aprendizaje del equipo comunes vea [ejemplos](#Examples-of-Net#-usage).  

## <a name="general-requirements"></a>Requisitos generales
-   Debe haber exactamente un resultado capa, al menos una capa de entrada y cero o más capas ocultas. 
-   Cada capa tiene un número fijo de nodos, que conceptualmente se organizan en una matriz rectangular de dimensiones arbitrarios. 
-   Capas de entrada no tienen asociados capacitados parámetros y representan el punto donde los datos de instancia entran en la red. 
-   Capas trainable (capas ocultas y de salida) tienen asociados capacitados parámetros, conocidas como pesos y sesgos. 
-   Los nodos de origen y de destino deben estar en capas independientes. 
-   Las conexiones deben ser acíclicos; en otras palabras, no puede ser una cadena de conexiones, lo que lleva al nodo de origen inicial.
-   La capa de salida no puede ser una capa de origen de un paquete de conexión.  

## <a name="structure-specifications"></a>Especificaciones de la estructura
Una especificación de la estructura de red neuronal está formada por tres secciones: la **declaración de constante**, la **declaración de capa**, la **declaración de conexión**. También es una sección opcional **Compartir declaración** . Las secciones se pueden especificar en cualquier orden.  

## <a name="constant-declaration"></a>Declaración de constante 
Una declaración de constante es opcional. Proporciona un medio para definir valores en otra aplicación usados en la definición de red neuronal. La instrucción de declaración consta de un identificador seguido por un signo igual y una expresión de valor.   

Por ejemplo, la siguiente instrucción define una constante **x**:  


    Const X = 28;  

Para definir simultáneamente dos o más constantes, incluya los nombres de identificador y los valores entre llaves y sepárelos con punto y coma. Por ejemplo:  

    Const { X = 28; Y = 4; }  

El lado derecho de las expresiones de asignación puede ser un número entero, un número real, un valor booleano (True o False) o una expresión matemática. Por ejemplo:  

    Const { X = 17 * 2; Y = true; }  

## <a name="layer-declaration"></a>Declaración de capa
Se requiere la declaración de capa. Define el tamaño y el origen de la capa, incluidos sus atributos y paquetes de conexión. La instrucción de declaración comienza con el nombre de la capa (de entrada, ocultas o de salida), seguido de las dimensiones de la capa (una tupla de enteros positivos). Por ejemplo:  

    input Data auto;
    hidden Hidden[5,20] from Data all;
    output Result[2] from Hidden all;  

-   El producto de las dimensiones es el número de nodos de la capa. En este ejemplo, hay dos dimensiones [5,20], lo que significa que hay 100 nodos en la capa.
-   Las capas se pueden declarar en cualquier orden, con una excepción: si ha definido más de una capa de entrada, el orden en el que se declara debe coincidir con el orden de las características de los datos de entrada.  


Para especificar el número de nodos en una capa determinará automáticamente, utilice la palabra clave **auto** . La palabra clave **auto** tiene efectos distintos, dependiendo de la capa:  

-   En una declaración de la capa de entrada, el número de nodos es el número de características de los datos de entrada.
-   En una declaración de capa oculta, el número de nodos es el número especificado por el valor del parámetro para el **número de nodos ocultos**. 
-   En una declaración de nivel de salida, el número de nodos es 2 para clasificación de clase de dos, 1 de regresión e igual al número de nodos de salida para multiclass clasificación.   

Por ejemplo, la siguiente definición de red permite cambiar el tamaño de todas las capas que se establezca automáticamente:  

    input Data auto;
    hidden Hidden auto from Data all;
    output Result auto from Hidden all;  


Una declaración de capa para una capa trainable (capas ocultas o de salida) puede incluir opcionalmente la función salida (también denominada función de activación), cuyo valor predeterminado es **sigmoid** para modelos de clasificación y **lineal** para modelos de regresión. (Aunque use el valor predeterminado, se puede indicar explícitamente la función de activación, si lo desea para claridad.)

Se admiten las siguientes funciones de salida:  

-   sigmoid
-   lineal
-   softmax
-   rlinear
-   cuadrado
-   SQRT
-   srlinear
-   ABS
-   TANH 
-   brlinear  

Por ejemplo, la declaración siguiente usa la función **softmax** :  

    output Result [100] softmax from Hidden all;  

## <a name="connection-declaration"></a>Declaración de conexión
Inmediatamente después de definir la capa trainable, debe declarar conexiones entre las capas que haya definido. La declaración de paquete de conexión empieza con la palabra clave **de**, seguido del nombre de la capa de origen del paquete y el tipo de paquete de conexión para crear.   

Actualmente, se admiten cinco tipos de paquetes de conexión:  

-   Paquetes **completa** , indicados por la palabra clave de **todos los**
-   Paquetes de **filtrado** , indicados por la palabra clave **donde**, seguido de una expresión de predicado
-   Paquetes de **Convolutional** , indicados por la palabra clave **convolve**, seguido de los atributos de circunvolución
-   Paquetes de **agrupación** , indicados por palabras clave o el **máximo del grupo** **la intención de grupo**
-   Paquetes de **normalización de respuesta** , indicados por la palabra clave **norma de respuesta**      

## <a name="full-bundles"></a>Paquetes completos  

Un paquete de conexión completa incluye una conexión de cada nodo en la capa de origen para cada nodo de la capa de destino. Esto es el tipo de conexión de red de forma predeterminada.  

## <a name="filtered-bundles"></a>Paquetes filtrados
Una especificación de paquete de conexión filtrada incluye muy predicado, sintácticamente, expresado como una expresión lambda de C#. En el ejemplo siguiente se define dos paquetes de filtrado:  

    input Pixels [10, 20];
    hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
    hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;  

-   En el predicado para _ByRow_, **s** es un parámetro que representa un índice en la matriz rectangular de nodos de la capa de entrada, _píxeles_, y **d** es un parámetro que representa un índice en la matriz de nodos de la capa oculta, _ByRow_. El tipo de **s** y **d** es una tupla de enteros de longitud de dos. Conceptualmente, **s** rangos sobre todos los pares de enteros con _0 < = s [0] < 10_ y _0 < = s[1] < 20_, y **d.** rangos sobre todos los pares de enteros, con _0 < = d [0] < 10_ y _0 < = d[1] < 12_. 
-   En el lado derecho de la expresión predicado, hay una condición. En este ejemplo, para cada valor de **s** y **d** tal que la condición es verdadera, hay un borde desde el nodo de la capa de origen al nodo de capa de destino. Por lo tanto, esta expresión de filtro indica que el paquete incluye una conexión desde el nodo definida por **s** al nodo definido por **d.** en todos los casos donde s [0] es igual a d [0].  

Si lo desea, puede especificar un conjunto de grosores para el paquete filtrado. El valor del atributo **grosores** debe ser una tupla de los valores de punto flotante con una longitud que coincida con el número de conexiones definidas por el paquete. De forma predeterminada, se generan aleatoriamente grosores.  

Valores de peso se agrupan por el índice de nodo de destino. Es decir, si el primer nodo de destino está conectado a los nodos de origen K, los primeros elementos _K_ de la tupla **grosores** son los pesos del primer nodo de destino, en orden de índice de origen. Lo mismo sucede con los demás nodos de destino.  

Es posible especificar grosores directamente como valores constantes. Por ejemplo, si previamente ha aprendido los pesos, puede especificarlos como constantes mediante la siguiente sintaxis:

    const Weights_1 = [0.0188045055, 0.130500451, ...]


## <a name="convolutional-bundles"></a>Paquetes convolutional
Cuando los datos de entrenamiento tienen una estructura homogénea, conexiones convolutional se usan generalmente para obtener información sobre las características de alto nivel de los datos. Por ejemplo, en la imagen de datos de audio o vídeo, dimensiones espacial o temporal pueden ser bastante uniformes.  

Paquetes convolutional emplean rectangular **kernels** son Deslizar a través de las dimensiones. Básicamente, cada núcleo define un conjunto de grosores aplicado a grupos locales, denominadas **aplicaciones de kernel**. Cada aplicación de kernel corresponde a un nodo en la capa de origen, que se conoce como el **nodo central**. Las proporciones de un núcleo comparten muchas conexiones. En un paquete convolutional, cada núcleo es rectangular y todas las aplicaciones de kernel tienen el mismo tamaño.  

Paquetes de convolutional es compatible con los siguientes atributos:

**InputShape** define las dimensiones de la capa de origen para los fines de este conjunto de convolutional. El valor debe ser una tupla de enteros positivos. El producto de los enteros debe coincidir con el número de nodos en la capa de origen, pero en caso contrario, no es necesario para que coincida con las dimensiones declarada para la capa de origen. La longitud de esta tupla se convierte en el valor de **aridad** para el paquete convolutional. (Normalmente aridad hace referencia al número de operandos que puede realizar una función o de argumentos.)  

Para definir la forma y las ubicaciones de los kernels, utilice los atributos **KernelShape**, **paso**, **relleno**, **LowerPad**y **UpperPad**:   

-   **KernelShape**: (obligatorio) define las dimensiones de cada núcleo para el paquete convolutional. El valor debe ser una tupla de enteros positivos con una longitud que es igual a la aridad del paquete. Cada componente de esta tupla debe ser no mayor que el componente correspondiente de **InputShape**. 
-   **Paso**: (opcional) define el tamaño de paso deslizante de circunvolución (tamaño de paso para cada dimensión), que es la distancia entre los nodos central. El valor debe ser una tupla de enteros positivos con una longitud que sea la aridad del paquete. Cada componente de esta tupla debe ser no mayor que el componente correspondiente de **KernelShape**. El valor predeterminado es una tupla con todos los componentes iguales a uno. 
-   **Compartir**: (opcional) define el grosor de uso compartido para cada dimensión de la circunvolución. El valor puede ser un único valor booleano o una tupla de valores booleanos con una longitud que sea la aridad del paquete. Un único valor booleano se ha ampliado para ser una tupla la longitud correcta con todos los componentes de iguales al valor especificado. El valor predeterminado es una tupla que consta de todos los valores True. 
-   **MapCount**: (opcional) define el número de la característica de mapas para el paquete convolutional. El valor puede ser un número entero positivo o una tupla de enteros positivos con una longitud que sea la aridad del paquete. Un solo valor entero se ha ampliado para ser una tupla la longitud correcta con los primeros componentes iguales que el valor especificado y todos los componentes restantes es igual a uno. El valor predeterminado es uno. El número total de mapas de característica es el producto de los componentes de la tupla. La división de este número total a través de los componentes determina cómo se agrupan los valores del mapa de función en los nodos de destino. 
-   **Grosores**: (opcional) define los pesos iniciales para el paquete. El valor debe ser una tupla de valores de punto flotante con una longitud que es el número de veces que kernels el número de pesos por núcleo, según se define más adelante en este artículo. Se generan aleatoriamente los pesos predeterminados.  

Hay dos conjuntos de propiedades que controlan relleno, las propiedades que se excluyen mutuamente:

-   **Relleno**: determina (opcional) si la entrada debe estar rellena mediante un **esquema de relleno predeterminado**. El valor puede ser un único valor booleano, o puede ser una tupla de valores booleanos con una longitud que sea la aridad del paquete. Un único valor booleano se ha ampliado para ser una tupla la longitud correcta con todos los componentes de iguales al valor especificado. Si el valor de una dimensión es verdadero, el origen se rellena lógicamente de la dimensión con las celdas con valores de cero para admitir aplicaciones adicionales del núcleo, por ejemplo, que los nodos central de la kernel primera y última de la dimensión son los nodos y el apellido de la dimensión en la capa de origen. Por lo tanto, se determina automáticamente, el número de nodos "ficticios" en cada dimensión para que encaje exactamente _(InputShape [d] - 1) / paso [d] + 1_ kernels en la capa de origen rellena. Si el valor de una dimensión es falso, se definen los kernels para que el número de nodos en cada lado que quedan fuera es el mismo (hasta una diferencia de 1). El valor predeterminado de este atributo es una tupla con todos los componentes iguales a False.
-   **UpperPad** y **LowerPad**: (opcional) proporcionar mayor control sobre la cantidad de relleno que se use. **Importante:** Estos atributos pueden definirse solo si la propiedad **de relleno** anterior es ***no*** definido. Los valores deben ser tuplas de enteros con longitudes de la aridad del paquete. Cuando se especifican estos atributos, se agregan nodos "ficticios" a los extremos superiores e inferiores de cada dimensión de la capa de entrada. El número de nodos agregado a los extremos superiores e inferiores de cada dimensión depende de **LowerPad**[i] y **UpperPad**[i] respectivamente. Para asegurarse de que kernels corresponden únicamente a los nodos "reales" y no a nodos "ficticios", deben cumplirse las condiciones siguientes:
    -   Cada componente de **LowerPad** debe ser estrictamente menor que KernelShape [d] / 2. 
    -   Cada componente de **UpperPad** debe ser mayor no KernelShape [d] / 2. 
    -   El valor predeterminado de estos atributos es una tupla con todos los componentes iguales a 0. 

La configuración de **relleno** = verdadero permite tanto relleno que necesite para mantener el "Centro" del núcleo dentro de "real" de entrada. Esto cambia las matemáticas un poco para calcular el tamaño de salida. En general, el tamaño de salida _d._ se calcula como _d. = (I - K) o S + 1_, donde _I_ es el tamaño de la entrada, _K_ es el tamaño del núcleo, _S_ es el intervalo, y _/_ es la división de enteros (redondear hacia el cero). Si establece UpperPad = [1, 1], el tamaño de entrada __ es un modo eficaz 29 y, por tanto, _d. = (29-5) / 2 + 1 = 13_. Sin embargo, cuando **relleno** = verdadero, esencialmente __ obtiene incrementará _K - 1_; por lo tanto, _d. = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14_. Especificando valores para **UpperPad** y **LowerPad** obtener un mayor control sobre el relleno que si acaba de establecer **relleno** = verdadero.

Para obtener más información acerca de redes convolutional y sus aplicaciones, vea estos artículos:  

-   [http://deeplearning.NET/tutorial/lenet.HTML](http://deeplearning.net/tutorial/lenet.html )
-   [http://Research.Microsoft.com/pubs/68920/icdar03.PDF](http://research.microsoft.com/pubs/68920/icdar03.pdf) 
-   [http://people.csail.MIT.edu/jvb/papers/cnn_tutorial.PDF](http://people.csail.mit.edu/jvb/papers/cnn_tutorial.pdf)  

## <a name="pooling-bundles"></a>Agrupación de paquetes
Un **paquete de agrupación** aplica geometría similar a convolutional conectividad, pero utiliza funciones predefinidas a los valores de nodo de origen para obtener el valor de nodo de destino. Por lo tanto, los paquetes de agrupación no tienen ningún estado trainable (pesos o sesgos). Paquetes de agrupación admiten todos los atributos convolutional excepto el **uso compartido**, **MapCount**y **grosores**.  

Normalmente, no se superponen los kernels resumidos por unidades de agrupación adyacentes. Si paso [d] es igual a KernelShape [d] en cada dimensión, la capa que se obtienen es la tradicional capa local agrupación se emplea habitualmente en redes neurales convolutional. Cada nodo de destino calcula el valor máximo o la media de las actividades de su kernel en la capa de origen.  

En el ejemplo siguiente se muestra un paquete de agrupación: 

    hidden P1 [5, 12, 12]
      from C1 max pool {
        InputShape  = [ 5, 24, 24];
        KernelShape = [ 1,  2,  2];
        Stride      = [ 1,  2,  2];
      }  

-   La aridad del paquete es 3 (la longitud de las tuplas **InputShape**, **KernelShape**y **paso**). 
-   El número de nodos en la capa de origen es _5 *24* 24 = 2880_. 
-   Esto es un nivel de agrupación local tradicional porque **KernelShape** y **paso** son iguales. 
-   El número de nodos de la capa de destino es _5 *12* 12 = 1440_.  
    
Para obtener más información acerca de las capas de agrupación, consulte estos artículos:  

-   [http://www.cs.Toronto.edu/~hinton/absps/imagenet.PDF](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Sección 3.4)
-   [http://cs.nyu.edu/~koray/Publis/lecun-iscas-10.PDF](http://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf) 
-   [http://cs.nyu.edu/~koray/Publis/jarrett-iccv-09.PDF](http://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)
    
## <a name="response-normalization-bundles"></a>Paquetes de normalización de respuesta
**Normalización de respuesta** es una combinación de normalización local se presentó por Geoffrey Hinton, etc., en papel [ImageNet Classiﬁcation con profundidad redes neurales Convolutional](http://www.cs.toronto.edu/~hinton/absps/imagenet.pdf). Normalización de respuesta se usa para facilitar la generalización en redes neurales. Cuando se está activando uno neuron con un nivel de activación muy alta, una capa de normalización respuesta local suprime el nivel de activación de la neurons adyacentes. Esto se hace con tres parámetros (***α***, ***β***y ***k***) y una estructura convolutional (o forma de entorno). Cada neuron en la capa de destino ***y*** corresponde a una neuron ***x*** en la capa de origen. El nivel de activación de ***y*** se expresa mediante la siguiente fórmula, donde ***f*** es el nivel de activación de un neuron y ***Nx*** es el núcleo (o el conjunto que contiene el neurons en el entorno de ***x***), definida por la estructura convolutional siguiente:  

![][1]  

Paquetes de respuesta normalización admiten todos los atributos convolutional excepto el **uso compartido**, **MapCount**y **grosores**.  
 
-   Si el núcleo contiene neurons en el mismo mapa como ***x***, la combinación de normalización se conoce como **normalización mismo de mapa**. Para definir la misma normalización de mapa, la primera coordenadas en **InputShape** deben tener el valor 1.
-   Si el núcleo contiene neurons en la misma posición espacial como ***x***, pero la neurons en otros mapas, la combinación de normalización se llama **a través de mapas normalización**. Este tipo de normalización respuesta implementa un formulario de inhibición lateral inspirado en el tipo que se encuentra en neurons reales, crear competencia para niveles de activación grande entre los resultados neuron a calcular en los mapas diferentes. Para definir en mapas normalización, la primera coordenadas deben ser un número entero mayor que uno y no es mayor que el número de mapas y el resto de las coordenadas debe tener el valor 1.  

Como los paquetes de respuesta normalización aplicarán una función predefinida a los valores de nodo de origen para determinar el valor de nodo de destino, no tienen ningún estado trainable (pesos o sesgos).   

**Alerta**: los nodos de la capa de destino corresponden a neurons que son los nodos de los kernels central. Por ejemplo, si KernelShape [d] es impar, a continuación, _KernelShape [d] / 2_ corresponde al nodo núcleo central. Si _KernelShape [d]_ es par, el nodo central está en _KernelShape [d] / 2-1_. Por lo tanto, si el **relleno**[d] es falso, la primera y la última _KernelShape [d] / 2_ nodos no tienen nodos correspondientes en la capa de destino. Para evitar esta situación, definir **relleno** como [true, true,..., true].  

Además de los cuatro atributos descritos anteriormente, los paquetes de respuesta normalización también admiten los siguientes atributos:  

-   **Alfa**: (requerido) especifica un valor de punto flotante que corresponde a ***α*** en la fórmula anterior. 
-   **Beta**: (requerido) especifica un valor de punto flotante que corresponde a ***β*** en la fórmula anterior. 
-   **Desplazamiento**: (opcional) especifica un valor de punto flotante que corresponde a ***k*** en la fórmula anterior. El valor predeterminado es 1.  

En el ejemplo siguiente se define un paquete de normalización respuesta con estos atributos:  

    hidden RN1 [5, 10, 10]
      from P1 response norm {
        InputShape  = [ 5, 12, 12];
        KernelShape = [ 1,  3,  3];
        Alpha = 0.001;
        Beta = 0.75;
      }  

-   La capa de origen incluye cinco mapas, cada una con aof dimensión de 12 x 12, calcular los totales en los nodos de 1440. 
-   El valor de **KernelShape** indica que se trata de una misma capa de normalización de mapa, donde el entorno es un rectángulo de 3 x 3. 
-   El valor predeterminado de **relleno** es falso, por lo tanto la capa de destino tiene solo 10 nodos en cada dimensión. Para incluir un nodo en la capa de destino que corresponde a todos los nodos de la capa de origen, agregar relleno = [verdadero; verdadero; verdadero]; y cambiar el tamaño de RN1 [12, 5, 12].  

## <a name="share-declaration"></a>Declaración de compartir 
Neto # opcionalmente admite definir varios paquetes con grosores compartidos. Las proporciones de las dos paquetes pueden compartirse Si sus estructuras son iguales. La sintaxis siguiente define paquetes con grosores compartidas:  

    share-declaration:
        share    {    layer-list    }
        share    {    bundle-list    }
       share    {    bias-list    }
    
    layer-list:
        layer-name    ,    layer-name
        layer-list    ,    layer-name
    
    bundle-list:
       bundle-spec    ,    bundle-spec
        bundle-list    ,    bundle-spec
    
    bundle-spec:
       layer-name    =>     layer-name
    
    bias-list:
        bias-spec    ,    bias-spec
        bias-list    ,    bias-spec
    
    bias-spec:
        1    =>    layer-name
    
    layer-name:
        identifier  

Por ejemplo, la declaración de compartir siguiente especifica los nombres de capa, que indica que deben compartirse grosores y sesgos:  

    Const {
      InputSize = 37;
      HiddenSize = 50;
    }
    input {
      Data1 [InputSize];
      Data2 [InputSize];
    }
    hidden {
      H1 [HiddenSize] from Data1 all;
      H2 [HiddenSize] from Data2 all;
    }
    output Result [2] {
      from H1 all;
      from H2 all;
    }
    share { H1, H2 } // share both weights and biases  

-   Las características de entrada se dividen en dos niveles de entrada con el tamaño iguales. 
-   Las capas ocultas para calcular las características de nivel superior en las dos capas de entrada. 
-   La declaración de compartir especifica que va a calcular _H1_ y _H2_ de la misma forma desde sus respectivos entradas.  
 
Como alternativa, esto podría ser especificado con dos declaraciones de compartir independientes como sigue:  

    share { Data1 => H1, Data2 => H2 } // share weights  

<!-- -->

    share { 1 => H1, 1 => H2 } // share biases  

Puede utilizar la forma abreviada únicamente cuando las capas contienen un único paquete. En general, es posible compartir solo cuando la estructura relevante es idéntica, lo que significa que tienen el mismo tamaño, misma geometría convolutional y así sucesivamente.  

## <a name="examples-of-net-usage"></a>Ejemplos de uso de red #
En esta sección se proporciona algunos ejemplos de cómo puede usar Net # para agregar las capas ocultas, defina la forma en que las capas ocultas interactúan con otras capas y crear redes convolutional.   

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Definir una red neuronal personalizada simple: ejemplo "Hola a todos"
Este sencillo ejemplo muestra cómo crear un modelo de red neuronal que tiene una sola capa oculta.  

    input Data auto;
    hidden H [200] from Data all;
    output Out [10] sigmoid from H all;  

El ejemplo muestra algunos comandos básicos como sigue:  

-   La primera línea define la capa de entrada (denominada _datos_). Cuando se utiliza la palabra clave **auto** , la red neuronal incluye automáticamente todas las columnas de la característica en los ejemplos de entrada. 
-   La segunda línea crea la capa oculta. El nombre _H_ se asigna a la capa oculta, que tiene 200 nodos. Esta capa totalmente está conectada a la capa de entrada.
-   La tercera línea define la capa salida (denominada _O_), que contiene 10 nodos de salida. Si se usa la red neuronal de clasificación, hay un nodo de resultados por clase. La palabra clave **sigmoid** indica que la función de salida se aplica a la capa de salida.   

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Definir varios niveles ocultos: ejemplo de visión de equipo
En el ejemplo siguiente se muestra cómo definir una red neural ligeramente más compleja con varias capas ocultas personalizadas.  

    // Define the input layers 
    input Pixels [10, 20];
    input MetaData [7];
    
    // Define the first two hidden layers, using data only from the Pixels input
    hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
    hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
    
    // Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
    hidden Gather [100] 
    {
      from ByRow all;
      from ByCol all;
    }
    
    // Define the output layer and its sources
    output Result [10]  
    {
      from Gather all;
      from MetaData all;
    }  

Este ejemplo muestra varias características del lenguaje de especificación de redes neurales:  

-   La estructura tiene dos capas de entrada, _píxeles_ y _metadatos_.
-   La capa de _píxeles_ es una capa de origen para dos paquetes de conexión con capas de destino, _ByRow_ y _ByCol_.
-   Las capas _recopilar_ y el _resultado_ son capas de destino en varios paquetes de conexión.
-   La capa de salida, el _resultado_, es una capa de destino en dos paquetes de conexión; una con el segundo nivel oculto (recopilar) como una capa de destino y el otro con la capa de entrada (metadatos) como una capa de destino.
-   Las capas ocultas, _ByRow_ y _ByCol_, especifican conectividad filtrada mediante expresiones de predicado. Más concretamente, en el nodo _ByRow_ en [x, y] está conectado a los nodos en _píxeles_ que tienen la primera coordenadas de índice igual a coordenadas primera del nodo, x. Asimismo, el nodo _ByCol en [x, y] está conectado a los nodos en _píxeles_ que tengan el segundo índice coordinar dentro de una de las coordenadas de segundo del nodo, y.  

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Definir una red convolutional para clasificación multiclass: ejemplo de reconocimiento de dígitos
La definición de la red siguiente está diseñada para reconocer números y muestra algunas técnicas avanzadas para personalizar una red neuronal.  

    input Image [29, 29];
    hidden Conv1 [5, 13, 13] from Image convolve 
    {
       InputShape  = [29, 29];
       KernelShape = [ 5,  5];
       Stride      = [ 2,  2];
       MapCount    = 5;
    }
    hidden Conv2 [50, 5, 5]
    from Conv1 convolve 
    {
       InputShape  = [ 5, 13, 13];
       KernelShape = [ 1,  5,  5];
       Stride      = [ 1,  2,  2];
       Sharing     = [false, true, true];
       MapCount    = 10;
    }
    hidden Hid3 [100] from Conv2 all;
    output Digit [10] from Hid3 all;  


-   La estructura tiene una sola capa de entrada, _imagen_.
-   La palabra clave **convolve** indica que las capas denominadas _Conv1_ y _Conv2_ convolutional capas. Cada una de estas declaraciones de capa va seguida de una lista de los atributos de circunvolución.
-   La red oculta una tercera capa, _Hid3_, que se conecte totalmente a la segunda capa oculta, _Conv2_.
-   La capa de salida, _dígitos_, se conecta únicamente a la tercera capa oculta, _Hid3_. Palabra clave **todos** indica que la capa de salida totalmente está conectada a _Hid3_.
-   La aridad de la circunvolución es tres (la longitud de las tuplas **InputShape**, **KernelShape**, **paso**y **uso compartido**). 
-   El número de pesos por núcleo es _1 + **KernelShape**\[0] * * *KernelShape**\[1]* **KernelShape**\[2] = 1 + 1 *5* 5 = 26. O 26 * 50 = 1300_.
-   Puede calcular los nodos de cada capa oculta como sigue:
    -   **NodeCount**\[0] = (5 - 1) o 1 + 1 = 5.
    -   **NodeCount**\[1] = (13-5) / 2 + 1 = 5. 
    -   **NodeCount**\[2] = (13-5) / 2 + 1 = 5. 
-   Puede calcular el número total de nodos mediante el uso de las dimensiones de la capa, declarada [50, 5, 5], como sigue: _ **MapCount** * * *NodeCount**\[0]* **NodeCount**\[1] * * *NodeCount**\[2] = 10* 5 *5* 5_
-   Debido a **Compartir**[d] es False solo para _d. == 0_, el número de kernels es _ **MapCount** * * *NodeCount**\[0] = 10* 5 = 50_. 


## <a name="acknowledgements"></a>Confirmaciones

El idioma neto # para personalizar la arquitectura de redes neurales fue desarrollado en Microsoft por Shon Katzenberger (arquitecto, el aprendizaje) y Alexey Kamenev (ingeniería de Software, Microsoft Research). Se usa internamente para aprendizaje proyectos y aplicaciones desde la detección de imagen para el análisis de texto. Para obtener más información, vea [Redes neurales en Azure m - Introducción a la red #](http://blogs.technet.com/b/machinelearning/archive/2015/02/16/neural-nets-in-azure-ml-introduction-to-net.aspx)


[1]:./media/machine-learning-azure-ml-netsharp-reference-guide/formula_large.gif
 
