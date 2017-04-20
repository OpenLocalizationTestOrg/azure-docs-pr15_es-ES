Almacenamiento está limitado por el espacio en disco o por un límite del *número máximo* de documentos o índices, lo que suceda primero. 

Recursos|Liberar|Básico|S1|S2|S3 |S3 HD
---|---|---|---|----|---|----
Contrato de nivel de servicio (SLA)|No <sup>1</sup> |Sí |Sí  |Sí |Sí |Sí
Almacenamiento por partición|50 MB |2 GB|25 GB|100 GB|200 GB|200 GB
Particiones por cada servicio|N/A.|1|12|12|12|3
Tamaño de la partición|N/A.|2 GB|25 GB|100 GB|200 GB |200 GB
Réplicas|N/A.|1|12|12|12|12
Índices máximos|3|5|50|200|200|1.000 por partición o 3000 por cada servicio
Máximo de documentos|10.000|1 millón|15 millones por partición o millones de 180 por cada servicio |60 millones por partición o 720 millones por cada servicio |120 millones por partición o 1,4 mil millones por cada servicio|1 millón por índice o 200 millones por partición |
Estimado consultas por segundo (QPS)|N/A.|~ 3 por réplica|~ 15 por réplica|~ 60 por réplica|~ 60 por réplica|> 60 por réplica

<sup>1</sup> Free y SKU de vista previa no incluyen contratos de nivel de servicio (SLA). SLA se aplican cuando una SKU vuelve a estar disponible de forma general.