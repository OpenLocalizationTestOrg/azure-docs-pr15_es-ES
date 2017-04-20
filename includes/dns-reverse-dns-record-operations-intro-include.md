## <a name="what-is-reverse-dns"></a>¿Qué es DNS inversa?

Registros DNS convencionales habilitar una asignación de un nombre DNS (como 'www.contoso.com') a una dirección IP (por ejemplo, 64.4.6.100).  DNS inversa permite la traducción de una dirección IP (64.4.6.100) a un nombre ('www.contoso.com').

Registros DNS inversos se usan en una gran variedad de situaciones. Por ejemplo, los registros DNS inversos ampliamente se usan para combatir el correo basura comprobando el remitente de un mensaje de correo electrónico.  El servidor de correo receptor recuperará el registro DNS inverso de la dirección IP del servidor envío y comprobar si ese host está autorizado para enviar correo electrónico desde el dominio de origen. (Tenga en cuenta sin embargo que [Azure calcular services no admiten enviarlos correos electrónicos a dominios externos](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).)

## <a name="how-reverse-dns-works"></a>Inverso de cómo funciona DNS

Registros DNS inversos se hospedan en especiales zonas DNS, conocidas como zonas 'ARPA'.  Estas zonas forman una jerarquía DNS independiente en paralelo con la jerarquía normal hospedaje dominios como 'contoso.com'.

Por ejemplo, el registro DNS 'www.contoso.com' se implementa mediante un registro DNS 'A' con el nombre "www" en la zona contoso.com.  Este registro se redirige a la dirección IP correspondiente, en este caso 64.4.6.100.  La búsqueda inversa se implementa por separado, con un registro de 'PTR' denominado '100' en la zona '6.4.64.in-addr.arpa' (tenga en cuenta que las direcciones IP se invierten en zonas ARPA)  Este registro PTR, si se ha configurado correctamente, señale el nombre www.contoso.com.

Cuando una organización se asigna un bloque de direcciones IP, adquieren el derecho para administrar la zona ARPA correspondiente. Las zonas ARPA correspondientes a los bloques de direcciones IP utilizados por Azure son hospedadas y administradas por Microsoft. Su ISP puede hospedar la zona ARPA para sus propias direcciones IP para usted o es posible que hospeda la zona ARPA en un servicio DNS de su elección, como DNS de Azure.

>[AZURE.NOTE] Búsquedas DNS directas y DNS inversas se implementan en paralelo, independientes jerarquías DNS. La búsqueda inversa de 'www.contoso.com' **no** alojado en la zona 'contoso.com', en su lugar está alojado en la zona ARPA para el bloque de direcciones IP correspondiente.

Para obtener más información sobre DNS inversa, vea [Búsqueda DNS inversa](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).

## <a name="azure-support-for-reverse-dns"></a>Compatibilidad con Azure DNS inversa

Azure admite dos escenarios independientes relativas para invertir DNS:

1. Aloja la zona ARPA correspondiente a su bloque de direcciones IP.
2. Lo que le permite configurar el registro DNS inverso para la dirección IP asignada a su servicio de Azure.

Para admitir el DNS antiguo, Azure puede usarse para alojar las zonas ARPA y administrar los registros PTR para cada búsqueda DNS inversa.  El proceso de creación de la zona ARPA, configurar la delegación y configurar los registros PTR es el mismo que para las zonas DNS normales.  Las únicas diferencias son que se debe configurar la delegación a través de su ISP en lugar de su registrador de DNS y se debe utilizar el tipo de registro PTR.

Para admitir el último, Azure le permite configurar la búsqueda inversa para las direcciones IP asignadas a su servicio.  Esta búsqueda inversa se configura Azure como registro PTR en la zona ARPA correspondiente.  Estas zonas ARPA, correspondiente a todos los intervalos IP utilizados Azure, se hospedan en Microsoft. **El resto de este artículo describe en este escenario en detalle.**
