Generador de datos es un servicio de múltiples arrendatario que tiene los siguientes límites de forma predeterminada en su lugar para asegurarse de que están protegidas suscripciones de clientes de cargas de trabajo de los demás. Muchos de los límites pueden provocar fácilmente la suscripción hasta el límite máximo póngase en contacto con soporte técnico. 

**Recursos** | **Límite predeterminado** | **Límite máximo**
-------- | ------------- | -------------
fábricas de datos en una suscripción de Azure | 50 | [Póngase en contacto con soporte técnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
canalizaciones dentro de un generador de datos | 2500 | [Póngase en contacto con soporte técnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
conjuntos de datos dentro de un generador de datos | 5000 | [Póngase en contacto con soporte técnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
sectores simultáneos por el conjunto de datos | 10 | 10
bytes por objeto para objetos de canalización <sup>1</sup> | 200 KB | KB 2000
bytes por objeto de conjunto de datos y objetos de servicio vinculadas <sup>1</sup> | 100 KB | KB 2000
Núcleos de clúster a petición HDInsight dentro de una suscripción <sup>2</sup> | 48 | [Póngase en contacto con soporte técnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
Movimiento de datos de nube unidad <sup>3</sup> | 8 | [Póngase en contacto con soporte técnico](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
Número de reintentos para ejecuciones de actividad de canalización | 1000 | MaxInt (32 bits)

<sup>1</sup> canalización, conjunto de datos y objetos de servicio vinculadas representan una agrupación lógica de su carga de trabajo. Límites de estos objetos no relacionan con la cantidad de datos, puede mover y procesar con el servicio de generador de datos de Azure. Generador de datos está diseñado para escalar para manejar petabytes de datos.

<sup>2</sup> se asignan a petición HDInsight núcleos fuera de la suscripción que contiene el generador de datos. Como resultado, el límite anterior es el generador de datos aplica el límite de núcleo para núcleos de HDInsight a petición y es diferente del límite de core asociada a su suscripción de Azure.

<sup>3</sup> unidad de movimiento de datos de nube (DMU) se utiliza en una operación de copia de la nube a la nube. Es una medida que representa la potencia (una combinación de CPU, la memoria y la asignación de recursos de la red) de una sola unidad en el generador de datos. Puede lograr un mayor rendimiento de copia aprovechando más DMUs para algunos escenarios. Consulte la sección de [unidades de movimiento de datos de nube](../../articles/data-factory/data-factory-copy-activity-performance.md#cloud-data-movement-units) en detalles.

**Recursos** | **Límite inferior de forma predeterminada** | **Límite mínimo**
-------- | ------------------- | -------------
Intervalo de programación | 15 minutos | 15 minutos
Intervalo entre reintentos | 1 segundo | 1 segundo
Valor de tiempo de espera de reintento | 1 segundo | 1 segundo


### <a name="web-service-call-limits"></a>Límites de llamada de servicio Web

Administrador de recursos de Azure tiene límites de llamadas a la API. Puede realizar llamadas de API a una velocidad dentro de los [límites de la API del Administrador de recursos de Azure](../azure-subscription-service-limits.md#resource-group-limits). 


