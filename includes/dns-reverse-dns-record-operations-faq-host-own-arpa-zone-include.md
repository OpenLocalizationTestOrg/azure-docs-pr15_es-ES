<BR> 
## <a name="faq---hosting-your-arpa-zone-in-azure-dns"></a>Preguntas más frecuentes - aloja su zona ARPA en DNS de Azure

### <a name="can-i-host-arpa-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>¿Hospedar zonas ARPA para mi bloques IP asignada por el ISP en Azure DNS?
Sí. Hospedaje las zonas ARPA para sus propio intervalos de IP en Azure DNS es totalmente compatible.

Simplemente [crear la zona de DNS de Azure](dns-getstarted-create-dnszone.md), a continuación, trabajar con el ISP para [delegar la zona](dns-domain-delegation.md).  A continuación, puede administrar los registros PTR para cada búsqueda inversa de la misma forma que otros tipos de registro.

También puede [importar una zona de búsqueda inversa existente con la CLI de Azure](dns-import-export.md).

### <a name="how-much-does-hosting-my-arpa-zone-cost"></a>¿Cuánto cuesta hospedaje mi costo de zona ARPA?
Aloja la zona ARPA para su dirección IP asignada por el ISP bloque en Azure DNS se cargará a [las tasas de Azure DNS estándar](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-arpa-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>¿Se puede alojar zonas ARPA para las direcciones IPv4 y IPv6 en Azure DNS?
Sí.