<properties
    pageTitle="Sincronización de Azure AD Connect: descripción de aprovisionamiento descriptiva | Microsoft Azure"
    description="Se explica el modelo de configuración de aprovisionamiento descriptiva en Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="billmath"/>


# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Sincronización de Azure AD Connect: descripción de aprovisionamiento descriptiva
En este tema se explica el modelo de configuración de Azure AD Connect. El modelo se denomina aprovisionamiento descriptiva y le permite cambiar una configuración con facilidad. Muchas cosas que se describe en este tema se avanzada y no es necesario para la mayoría de los escenarios de cliente.

## <a name="overview"></a>Información general
Aprovisionamiento descriptiva está procesando objetos procedentes de un directorio conectado de origen y determina cómo el objeto y los atributos deben ser transforma desde un origen a un destino. Un objeto se procesa en un proceso de sincronización y la canalización es el mismo para reglas de entrada y salidas. Una regla de entrada es un espacio de conector metaverse y una regla de salida de metaverse es un espacio de conector.

![Canalización de sincronización](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/sync1.png)  

La canalización tiene varios módulos diferentes. Cada uno de ellos es responsable de un concepto de sincronización del objeto.

![Canalización de sincronización](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/pipeline.png)  

- Origen, el objeto de origen
- [Ámbito](#scope), busca todas las reglas de sincronización que están en el ámbito
- [Unirse a](#join)determina relación entre el espacio del conector y metaverse
- [Transformar](#transform), calcula modo de transformación de atributos y flujo
- [Prioridad](#precedence), resuelve contribuciones de atributo en conflicto
- Destino, el objeto de destino

## <a name="scope"></a>Ámbito
El módulo de ámbito está evaluando un objeto y determina las reglas que están en el ámbito y se incluirá en el procesamiento. Dependiendo de los valores de atributos en el objeto, se evalúan reglas de sincronización diferentes para en el ámbito. Por ejemplo, un usuario deshabilitado sin buzón de Exchange tienen diferentes reglas que un usuario con un buzón habilitado.  
![Ámbito](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope1.png)  

El ámbito se define como cláusulas y grupos. Las cláusulas están dentro de un grupo. Se usa un operador lógico AND entre todas las cláusulas de un grupo. Por ejemplo, (Departamento = TI y país = Dinamarca). Se usa un operador lógico OR entre los grupos.

![Ámbito](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope2.png)  
El ámbito en esta imagen se debe leer como (Departamento = TI y país = Dinamarca) o (país = Suecia). Si el grupo de 1 o 2 del grupo se evalúa en true, la regla está en el ámbito.

El módulo de ámbito es compatible con las siguientes operaciones.

Operación | Descripción
--- | ---
IGUAL, NOTEQUAL | Comparación de cadena que se evalúa como si el valor es igual al valor del atributo. Atributos de varios valores, vea p y ISNOTIN.
LESSTHAN, LESSTHAN_OR_EQUAL | Una comparación de cadena que se evalúa como si el valor es menor que el valor en el atributo.
CONTIENE, NOTCONTAINS | Comparación de cadena que se evalúa como si valor se encuentra en algún lugar dentro de valor en el atributo.
STARTSWITH, NOTSTARTSWITH | Comparación de cadena que se evalúa como si el valor está en el principio del valor en el atributo.
ENDSWITH, NOTENDSWITH | Comparación de cadena que se evalúa como si el valor está en el final del valor en el atributo.
MAYOR, GREATERTHAN_OR_EQUAL | Comparación de cadena que se evalúa como si el valor es mayor que el valor en el atributo.
ISNULL, ISNOTNULL | Evalúa si falta el atributo del objeto. Si el atributo no está presente y, por tanto, null, la regla está en ámbito.
P, ISNOTIN | Evalúa si el valor está presente en el atributo definido. Esta operación es la variación de valor múltiple de igual y NOTEQUAL. El atributo se supone que es un atributo multivalor y si el valor se encuentra en cualquiera de los valores de atributo, a continuación, la regla está en el ámbito.
ISBITSET, ISNOTBITSET | Evalúa si se establece un bit concreto. Por ejemplo, puede utilizarse para evaluar los bits de userAccountControl para ver si un usuario está habilitado o deshabilitado.
ISMEMBEROF, ISNOTMEMBEROF | El valor debe contener un DN a un grupo en el espacio del conector. Si el objeto es un miembro del grupo especificado, la regla está en ámbito.

## <a name="join"></a>Unirse a
El módulo de combinación de la canalización de sincronización es responsable de encontrar la relación entre el objeto en el origen de un objeto en el destino. En una regla de entrada, esta relación sería un objeto en un espacio de conector que se ha encontrado una relación a un objeto en el metaverso.  
![Unirse a entre cs y mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join1.png)  
El objetivo es ver que si hay un objeto está en el metaverso, creado por otro conector debe estar asociado. Por ejemplo, en un bosque de recursos de la cuenta del usuario del bosque de cuentas debe combinarse con el usuario del bosque de recursos.

Las combinaciones se usan principalmente en las reglas de entrada para unir objetos del espacio de conector al mismo objeto de metaverso.

Las combinaciones se definen como uno o más grupos. Dentro de un grupo, tiene cláusulas. Se usa un operador lógico AND entre todas las cláusulas de un grupo. Se usa un operador lógico OR entre los grupos. Los grupos se procesan en orden de arriba a abajo. Cuando un grupo encuentra exactamente una coincidencia con un objeto en el destino, no se evalúan otras reglas de combinación. Si cero o más de un objeto se encuentra, procesamiento continúa al siguiente grupo de reglas. Por este motivo, las reglas debería creado en el orden de primero más explícito y más aproximada al final.  
![Unirse a la definición](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join2.png)  
Combinaciones en esta imagen se procesan de arriba a abajo. En primer lugar la canalización de sincronización ve si hay una coincidencia en IdEmpleado. Si no es así, la segunda regla ve si el nombre de cuenta puede usarse para unir los objetos. Si no es una coincidencia o bien, la tercera y última regla es una coincidencia aproximada más mediante el nombre de usuario.

Si se han evaluado todas las reglas de combinación y no hay exactamente una coincidencia, se utiliza el **Tipo de vínculo** en la página **Descripción** . Si esta opción se establece en **abastecerse**, se crea un nuevo objeto en el destino.  
![Aprovisionar o unirse](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join3.png)  

Un objeto solo debe tener una regla solo sincronización con reglas de unión en el ámbito. Si hay varias reglas de sincronización donde se define la combinación, se produce un error. Prioridad no se usa para resolver conflictos de combinación. Un objeto debe tener una regla de combinación en el ámbito de atributos de flujo con la misma dirección de entrada o salida. Si necesita atributos de flujo entrante y saliente al mismo objeto, debe tener una entrada y una regla de salida de sincronización con la combinación.

Combinación de salida tiene un comportamiento especial cuando intenta aprovisionar un objeto a un espacio de conector de destino. El atributo DN se utiliza para probar primero una combinación de inversa. Si ya existe un objeto en el espacio de conector de destino con el mismo DN, se unen los objetos.

El módulo de combinación sólo se evalúa una vez cuando trata de una nueva regla de sincronización en el ámbito. Cuando se ha unido un objeto, no se solucionará incluso si ya no se cumple los criterios de unión. Si desea retirar un objeto, la regla de sincronización que une los objetos debe ir fuera del ámbito.

### <a name="metaverse-delete"></a>Eliminar de Metaverse
Un objeto de metaverso permanece siempre y cuando haya es una regla de sincronización en el ámbito con el **Tipo de vínculo** conjunto a **disposición** o **StickyJoin**. Se usa un StickyJoin cuando no se permite un conector proporcionando un nuevo objeto en el metaverso, pero al que se ha unido, deben eliminarse en el origen antes de elimina el objeto de metaverse.

Cuando se elimina un objeto de metaverso, se marcan todos los objetos asociados con una regla de salida de sincronización marcada para **aprovisionar** para eliminar.

## <a name="transformations"></a>Transformaciones
Las transformaciones se usan para definir cómo deben flujo atributos del origen al destino. Los flujos de pueden tener uno de los siguientes **tipos de flujo**: directos, constante o expresión. Un flujo directo, pase un valor de atributo como-es sin transformaciones adicionales. Valor constante establece el valor especificado. Una expresión utiliza el lenguaje de expresiones de aprovisionamiento descriptiva para expresar cómo debe ser la transformación. Los detalles para el lenguaje de expresiones se pueden encontrar en el tema de [lenguaje de expresiones de aprovisionamiento descriptiva de descripción](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) .

![Aprovisionar o unirse](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/transformations1.png)  

La casilla de verificación **aplicar a la vez** define que solo se debe establecer el atributo cuando se crea inicialmente el objeto. Por ejemplo, esta configuración se puede utilizar para establecer una contraseña inicial de un objeto de usuario nuevo.

### <a name="merging-attribute-values"></a>Combinar valores de atributo
En los flujos de atributo hay una opción para determinar si se deben combinar atributos con varios valores de varios conectores diferentes. El valor predeterminado es de **actualización**, que indica que la regla de sincronización con mayor prioridad debería ganan.

![Tipos de combinación](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/mergetype.png)  

También hay **Combinar** y **MergeCaseInsensitive**. Estas opciones permiten combinar los valores de orígenes diferentes. Por ejemplo, puede usarse para combinar el atributo proxyAddresses o miembro de varios bosques diferentes. Al usar esta opción, todas las reglas de sincronización en el ámbito de un objeto deben utilizar el mismo tipo de combinación. No puede definir **actualización** desde un conector y **Combinar** desde otro. Si intenta, recibirá un error.

La diferencia entre **Combinar** y **MergeCaseInsensitive** es cómo procesar los valores de atributo duplicado. El motor de sincronización asegura de que no se insertan valores duplicados en el atributo de destino. Con **MergeCaseInsensitive**, los valores duplicados con solo una diferencia en caso de que no va a estar presente. Por ejemplo, no debería ver ambas "SMTP:bob@contoso.com" y "smtp:bob@contoso.com" en el atributo de destino. **Combinar** es solo miramos los valores exactos y varios valores que solo hay una diferencia en caso podría ser presentar.

La opción **Reemplazar** es idéntica a la **actualización**, pero no se usa.

### <a name="control-the-attribute-flow-process"></a>Controlar el proceso de flujo de atributo
Cuando se configuran varias reglas de sincronización entrante para poder colaborar en el mismo atributo de metaverse, prioridad se utiliza para determinar el ganador. La regla de sincronización con mayor prioridad (menor valor numérico) va a colaborar el valor. Ocurre lo mismo para las reglas de salida. La sincronización de regla con wins de prioridad más alta y contribuir el valor en el directorio conectado.

En algunos casos, en lugar de contribuir a un valor, debe determinar la regla de sincronización, lo que comportan de otras reglas. Hay algunos literales especiales utilizados para este caso.

Para las reglas de sincronización entrante, el literal **NULL** puede utilizarse para indicar que el flujo no tiene ningún valor para poder colaborar. Otra regla con prioridad más baja puede contribuir a un valor. Si no hay ninguna regla contribuido con un valor, se quita el atributo de metaverse. Para una regla de salida, si **NULL** es el valor final después de que se han procesado todas las reglas de sincronización, el valor se quita en el directorio conectado.

El literal **AuthoritativeNull** es similar, pero con la diferencia de que no hay ninguna regla de prioridad inferior puede contribuir a un valor **nulo** .

Un flujo de atributo también puede usar **IgnoreThisFlow**. Es similar a NULL en el sentido de que indica que no hay nada para poder colaborar. La diferencia es que no se elimina un valor ya existente en el destino. Es como si nunca ha sido existe el flujo de atributos.

Aquí tenemos un ejemplo:

Puede encontrar el siguiente flujo en *Salí a AD - híbrida de Exchange del usuario* :  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Esta expresión se debe leer como: si el buzón del usuario se encuentra en Azure AD, flujo, a continuación, el atributo de Azure AD a AD. Si no es así, ¿fluyen nada a Active Directory. En este caso, podría tenga el valor existente en AD.

### <a name="importedvalue"></a>ImportedValue
La función ImportedValue es diferente a todas las otras funciones como el nombre del atributo debe ir entre comillas, en lugar de corchetes:  
`ImportedValue("proxyAddresses")`.

Normalmente durante la sincronización de un atributo usa el valor esperado, incluso si todavía no ha exportado aún o se ha recibido un error durante la exportación ("parte superior de la torre"). Una sincronización entrante se supone que un atributo que no se ha alcanzado todavía un directorio conectado finalmente llega a ella. En algunos casos, es importante sincronizar únicamente un valor que se ha confirmado por el directorio conectado ("holograma y delta importar torre").

Un ejemplo de esta función se puede encontrar en la regla de sincronización del cuadro de *en de AD: usuario comunes de Exchange*. En Exchange híbrido, el valor añadido por Exchange online solo se sincronizarán cuando confirme que el valor se ha exportado correctamente:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Prioridad
Cuando intentan varias reglas de sincronización poder colaborar el mismo valor de atributo al destino, el valor de prioridad se utiliza para determinar el ganador. La regla con prioridad más alta, el menor valor numérico, va a colaborar el atributo en un conflicto.

![Tipos de combinación](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/precedence1.png)  

Este orden puede usarse para definir más precisos flujos de atributo para un subconjunto pequeño de objetos. Por ejemplo, la de fuera del cuadro reglas Asegúrese de que los atributos de una cuenta habilitada (**Usuario AccountEnabled**) tienen prioridad de otras cuentas.

Puede definirse prioridad entre conectores. Conectores que permite con datos mejor para poder colaborar valores en primer lugar.

### <a name="multiple-objects-from-the-same-connector-space"></a>Varios objetos desde el mismo espacio de conector
Si tiene varios objetos en el mismo espacio de conector unido al mismo objeto de metaverso, debe ajustarse prioridad. Si varios objetos están en el ámbito de la misma regla de sincronización, el motor de sincronización no es capaz de determinar prioridad. Es ambigua qué objeto de origen debe contribuir el valor a metaverse. Esta configuración se notifica como ambigua incluso si los atributos del origen tienen el mismo valor.  
![Varios objetos Unidos al mismo objeto mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple1.png)  

En este escenario, debe cambiar el ámbito de las reglas de sincronización para que los objetos de origen tienen reglas de sincronización diferentes en el ámbito. Que le permite definir la precedencia diferente.  
![Varios objetos Unidos al mismo objeto mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre el lenguaje de expresiones en [Expresiones de aprovisionamiento descriptiva en Descripción](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
- Vea cómo descriptiva aprovisionamiento es usados de predefinidas en la [Descripción de la configuración predeterminada](active-directory-aadconnectsync-understanding-default-configuration.md).
- Vea cómo hacer un cambio de práctica con aprovisionamiento descriptiva cómo [hacer un cambio en la configuración predeterminada](active-directory-aadconnectsync-change-the-configuration.md).
- Siga leyendo funcionan conjunto de los usuarios y los contactos en la [Descripción de usuarios y contactos](active-directory-aadconnectsync-understanding-users-and-contacts.md).

**Temas de información general**

- [Sincronización de Azure AD Connect: entender y personalizar la sincronización](active-directory-aadconnectsync-whatis.md)
- [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

**Temas de referencia**

- [Sincronización de Azure AD Connect: referencia de funciones](active-directory-aadconnectsync-functions-reference.md)
