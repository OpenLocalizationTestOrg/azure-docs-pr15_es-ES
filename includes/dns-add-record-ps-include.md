### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Crear un registro AAAA configurado con un único registro

    $rs = New-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="create-a-cname-record-set-with-a-single-record"></a>Cree un registro CNAME con un solo registro

    $rs = New-AzureRmDnsRecordSet -Name "test-cname" -RecordType CNAME -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="create-an-mx-record-set-with-a-single-record"></a>Crear un registro MX con un solo registro

En este ejemplo, se utiliza el nombre de conjunto de registros "@" para crear un registro MX en el vértice de zona (en este caso, "contoso.com"). Esto es común para los registros MX.

    $rs = New-AzureRmDnsRecordSet -Name "@" -RecordType MX -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="create-an-ns-record-set-with-a-single-record"></a>Crear un registro NS configurado con un solo registro

    $rs = New-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Crear un registro PTR configurado con un solo registro
En este caso ' Mi-arpa-zone.com' representa la zona ARPA que representa el intervalo de IP.  Cada registro PTR establecer en esta zona corresponde a una dirección IP dentro de este intervalo IP.  

    $rs = New-AzureRmDnsRecordSet -Name "10" -RecordType PTR -Ttl 3600 -ZoneName my-arpa-zone.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ptrdname "myservice.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="create-an-srv-record-set-with-a-single-record"></a>Crear un registro SRV con un solo registro

Si va a crear un registro SRV en la raíz de una zona, solo tiene que especificar *_service* y *_protocol* en el nombre del registro. No es necesario para incluir "@" en el nombre del registro.

    $rs = New-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="create-a-txt-record-set-with-a-single-record"></a>Crear un registro TXT configurado con un solo registro

    $rs = New-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
    Set-AzureRmDnsRecordSet -RecordSet $rs
