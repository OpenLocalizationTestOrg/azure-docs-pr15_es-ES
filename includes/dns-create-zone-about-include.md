Una zona DNS se utiliza para alojar los registros DNS para un dominio en particular. Para iniciar el host de su dominio, debe crear una zona DNS. Cualquier registro DNS que se creó para un dominio determinado estarán dentro de una zona DNS del dominio. 

Por ejemplo, el dominio "contoso.com" puede contener un número de registros DNS, como "mail.contoso.com" (para un servidor de correo) y "www.contoso.com" (para un sitio web). 


## <a name="names"></a>Acerca de los nombres de zona DNS
 
- El nombre de la zona debe ser único dentro del grupo de recursos y la zona no debe existir ya. En caso contrario, se producirá un error en la operación.

- El mismo nombre de zona puede volver a utilizar en otro grupo de recursos o una suscripción de Azure diferente. 

- Cuando hay varias zonas que comparten el mismo nombre, se asignarán cada instancia diferentes direcciones del servidor y solo una instancia se puede delegar desde el dominio principal. Para obtener más información, vea [delegado un dominio DNS de Azure](../articles/dns/dns-domain-delegation.md).