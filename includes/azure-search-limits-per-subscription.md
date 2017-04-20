Puede crear varios servicios dentro de una suscripción, cada una de ellas se aprovisione en un nivel específico, sólo limitado por el número de servicios permitidos en cada nivel. Por ejemplo, puede crear hasta 12 servicios en el nivel básico y otro 12 servicios en el nivel de S1 dentro de la misma suscripción. Para obtener más información sobre niveles, vea [Elegir un SKU o nivel de búsqueda de Azure](../articles/search/search-sku-tier.md).

Límites de máxima de servicio se pueden provocar a petición. Si necesita más servicios dentro de la misma suscripción, póngase en contacto con soporte técnico de Azure.

Recursos|Liberar|Básico|S1|S2|S3 |S3 HD <sup>1</sup>
---|---|---|---|----|---|----
Servicios de máximos |1 |12 |12  |6 |6 |6 
Escala máxima en SU <sup>2</sup>|N/a <sup>3</sup>|SU 3 <sup>4</sup> |SU 36|SU 36|SU 36|SU 12, 3 SU <sup>5</sup>

<sup>1</sup> S3 HD no admite [indizadores](../articles/search/search-indexer-overview.md) en este momento. 

<sup>2</sup> unidades de búsqueda (SU) son facturables unidades por servicio, asignado como una *réplica* o una *partición*. Necesita ambos recursos para operaciones de consulta, indización y almacenamiento. Para obtener más información acerca de las combinaciones válidas mantenerse en los límites máximos, vea [niveles de escala de recursos para cargas de trabajo de consulta e índice](../articles/search/search-capacity-planning.md). 

<sup>3</sup> libre se basa en recursos compartidos usados por varios suscriptores. En este nivel, no existen recursos dedicadas para un suscriptor individual. Por este motivo, escala máxima se marca como no aplicable.

<sup>4</sup> basic tiene una partición fija. En este nivel, SUs adicionales se usan para asignar más réplicas para cargas de trabajo de aumento de consulta.

<sup>5</sup> S3 HD tiene una estructura de asignación diferentes en términos de combinaciones permitidos. Para las réplicas, puede tener un máximo de 12. Para las particiones, el máximo es 3.




