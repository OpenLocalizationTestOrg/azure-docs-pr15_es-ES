<BR> 
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>Preguntas más frecuentes - DNS inversa para la dirección IP asignada de Azure

### <a name="how-much-do-reverse-dns-records-cost"></a>¿Cuánto invertir el costo de registros DNS?
¡Libremente!  No hay ningún costo adicional para los registros DNS inversos o consultas.

### <a name="will-the-reverse-dns-records-for-my-azure-assigned-public-ip-address-resolve-from-the-internet"></a>¿Los registros DNS inversos para mi dirección IP pública asignada Azure resolverá desde internet?
Sí. Una vez establecida la propiedad DNS inversa para la dirección IP pública, Azure administra todas las zonas DNS necesarias para asegurarse de que el registro DNS inversa resuelve para todos los usuarios de internet y delegación de DNS.

### <a name="will-a-default-reverse-dns-record-be-created-for-my-public-ip-addresses"></a>¿Se creará un registro DNS inverso predeterminado para las direcciones IP públicas?
No. DNS inversa es una característica opcional. Si decide no configurarlos, no se crean registros DNS inversas predeterminado.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>¿Qué es el formato del nombre de dominio completo (FQDN)?
FQDN se especifica en orden directo y debe terminar con un punto (por ejemplo, "app1.contoso.com.").

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>¿Qué sucede si no las comprobaciones de validación para el DNS inversa he especificado?
Donde la validación de DNS inversa comprueba si hay errores, se producirá un error en la operación de administración del servicio. Corrija el valor DNS inverso según sea necesario y vuelva a intentarlo.

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>¿Puedo administrar DNS inversa de Mi sitio Web de Azure?
No se admite el DNS inversa para sitios Web de Azure. DNS inversa es compatible para Azure máquinas virtuales de Windows.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-public-ip-address"></a>¿Puedo configurar varios registros DNS inversos para mi dirección IP pública?
No. Azure es compatible con un único registro DNS inverso para cada dirección IP pública. Sin embargo, cada dirección IP pública puede tener su propio registro DNS inversa.

### <a name="can-i-configure-reverse-dns-records-for-an-ipv6-public-ip-address"></a>¿Puedo configurar los registros DNS inversos para una dirección IP pública de IPv6?
No.  En este momento, los registros DNS inversos sólo se admiten para las direcciones IP de IPv4 públicas.

### <a name="can-i-configure-a-reverse-dns-record-for-my-public-ip-address-without-having-a-domainnamelabel-specified"></a>¿Puedo configurar un registro DNS inverso para mi dirección IP pública sin tener un DomainNameLabel especificado?
No. Para aprovechar los registros DNS inversos para las direcciones IP públicas, debe especificar la propiedad DomainNameLabel.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>¿Puedo enviar correos electrónicos a dominios externos desde Mis servicios calcular Azure?
No. [Calcular azure services no admiten enviarlos correos electrónicos a dominios externos](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).
