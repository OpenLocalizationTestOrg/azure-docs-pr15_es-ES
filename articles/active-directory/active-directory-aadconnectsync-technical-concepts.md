<properties
    pageTitle="Sincronización de Azure AD Connect: conceptos técnicos | Microsoft Azure"
    description="Explica los conceptos técnicos de sincronización de Azure AD Connect."
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
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-technical-concepts"></a>Sincronización de Azure AD Connect: conceptos técnicos
En este artículo es un resumen de la [Descripción de la arquitectura](active-directory-aadconnectsync-technical-concepts.md)de tema.

Sincronización de Azure AD Connect se basa en una plataforma de sincronización metadirectorio sólido.
Las secciones siguientes presentan los conceptos de sincronización metadirectorio.
Basándose en MIIS, ILM y FIM, los servicios de sincronización de Azure Active Directory proporciona la plataforma siguiente para conectarse a orígenes de datos, sincronizar datos entre orígenes de datos, así como el aprovisionamiento y baja de identidades.

![Conceptos técnicos](./media/active-directory-aadconnectsync-technical-concepts/scenario.png)

Las siguientes secciones proporcionan más detalles sobre los siguientes aspectos del servicio de sincronización de FIM:

- Conector
- Flujo de atributos
- Espacio de conector
- Metaverse
- Aprovisionamiento

## <a name="connector"></a>Conector

Los módulos de código que se usan para comunicarse con un directorio conectado se denominan conectores (anteriormente conocidos como agentes de administración (MAs)).

Están instalados en el equipo que ejecuta la sincronización de Azure AD Connect.
Los conectores permiten sin agente conversar con protocolos de sistema remoto en lugar de confiar en la implementación de agentes especializados. Esto significa menor riesgo y tiempos de implementación, especialmente cuando se trabaja con sistemas y aplicaciones críticas.

En la imagen anterior, el conector es sinónimo del espacio del conector pero abarca todas las comunicaciones con el sistema externo.

El conector es responsable de importar todo y exportar funcionalidad al sistema y libera a los desarrolladores de necesidad de conocer cómo conectarse a cada sistema de forma nativa cuando se usa el aprovisionamiento descriptiva para personalizar las transformaciones de datos.

Importaciones y exportaciones solo se producen cuando programado, lo que permite más aislamiento de los cambios que se producen en el sistema, ya que los cambios no propaga automáticamente al origen de datos conectados. Además, los desarrolladores también pueden crear sus propios conectores para conectarse a prácticamente cualquier origen de datos.

## <a name="attribute-flow"></a>Flujo de atributos

Metaverse es la vista consolidada de todas las identidades combinadas de las espacios de conector. En la figura anterior, se muestra el flujo de atributos por líneas con puntas de flecha para el flujo de entrada y salido. Flujo de atributos es el proceso de copiar o transformar datos de un sistema y todos los atributos flujos (entrantes o salientes).

Flujo de atributos se produce entre el espacio del conector y metaverse bidireccional cuando se programan operaciones de sincronización (completa o delta) para ejecutar.

Flujo de atributos sólo se produce cuando se ejecutan estas sincronizaciones. Flujos de atributos se definen en reglas de sincronización. Pueden ser entrante (ISR en la imagen de arriba) o salida (OSR en la imagen anterior).

## <a name="connected-system"></a>Sistema conectado

Sistema conectado (también conocido como directorio conectado) se hace referencia en el sistema remoto Azure AD Connect sincronización está conectado a y leer y escribir datos de identidad a y desde.

## <a name="connector-space"></a>Espacio de conector

Cada origen de datos conectados está representado como un subconjunto de los objetos y atributos en el espacio del conector filtrado.
Permite que el servicio de sincronización trabajar con localmente sin necesidad de ponerse en contacto con el sistema remoto al sincronizar los objetos y restringe interacción para la importación y exporta solo.

Si el origen de datos y el conector que tienen la capacidad de una lista de cambios (una importación delta), a continuación, la eficacia operativa aumenta considerablemente como solo los cambios desde el último ciclo de sondeo se intercambiado. El espacio del conector aísla el origen de datos conectados desde cambios propagarse automáticamente al requerir que la programación del conector importa y exporta. Este seguro agregado concede tranquilidad mientras pruebas, obtener una vista previa o confirmar la próxima actualización.

## <a name="metaverse"></a>Metaverse

Metaverse es la vista consolidada de todas las identidades combinadas de las espacios de conector.

A medida que se vinculan las identidades y entidad emisora está asignado para varios atributos a través de importar asignaciones de flujo, el objeto de metaverso central comenzará agregar información de varios sistemas. Desde este flujo de atributos del objeto, asignaciones contienen información a sistemas de salida.

Los objetos se crean cuando un sistema relevantes los proyectos en metaverse. En cuanto se eliminan todas las conexiones, se elimina el objeto de metaverse.

Objetos de metaverse no se pueden editar directamente. Todos los datos en el objeto se deben contribuidos a través del flujo de atributos. Metaverse mantiene conectores persistentes con cada espacio de conector. Estos conectores no requiere una nueva evaluación para cada sincronización ejecutar. Esto significa que la sincronización de Azure AD Connect no tiene que localizar el objeto remoto coincidente cada vez. Esto evita la necesidad de costosas agentes evitar cambios en los atributos que suele ser responsables de correlación de los objetos.

Cuando descubrir nuevos orígenes de datos que pueden contener objetos preexistentes que deben administrarse, sincronización de Azure AD Connect usa un proceso de una regla de combinación para evaluar a candidatos potenciales con el que desea establecer un vínculo.
Una vez que se establece el vínculo, que no se repita esta evaluación y flujo de atributos normal puede ocurrir entre el origen de datos conectados remoto y metaverse.

## <a name="provisioning"></a>Aprovisionamiento

Cuando los proyectos de origen autorizado un nuevo objeto en un nuevo objeto de espacio de conector metaverse puede crearse en otro conector de un origen de datos conectados descendente.

Esto inherente establece un vínculo y flujo de atributos puede continuar bidireccional.

Siempre que una regla determina que es necesario crear un nuevo objeto de espacio de conector, se denomina aprovisionamiento. Sin embargo, porque esta operación sólo lleve a cabo en el espacio del conector, mantienen en el origen de datos conectados hasta que se realiza una exportación.

## <a name="additional-resources"></a>Recursos adicionales

* [Sincronización de conectar Azure AD: Personalizar las opciones de sincronización](active-directory-aadconnectsync-whatis.md)
* [Integrar las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
