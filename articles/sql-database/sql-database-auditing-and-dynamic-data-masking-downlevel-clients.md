<properties
    pageTitle="Soporte técnico de clientes de nivel inferior de la base de datos SQL y extremo IP cambia para auditoría | Microsoft Azure"
    description="Obtenga información sobre compatibilidad con clientes de nivel inferior de base de datos SQL y IP cambios de extremo para auditoría."
    services="sql-database"
    documentationCenter=""
    authors="ronitr"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/10/2016"
    ms.author="ronitr"/>

# <a name="sql-database----downlevel-clients-support-and-ip-endpoint-changes-for-auditing"></a>Base de datos SQL: compatibilidad con clientes y cambios de extremo IP de auditoría


[Auditoría](sql-database-auditing-get-started.md) automáticamente funciona con los clientes de SQL que admiten la redirección de TDS.


##<a id="subheading-1"></a>Compatibilidad con clientes de nivel inferior

Cualquier cliente que implementa TDS 7.4 también debe admitir el redireccionamiento. Excepciones a esto incluyen JDBC 4.0 en el que no se admite la característica de redirección y no se ha implementado tediosas para Node.JS en qué redireccionamiento.

"Los clientes de nivel inferior", es decir, 7.3 y debajo - el FQDN del servidor en la conexión de la cadena de qué versión TDS soporte debe modificar:

FQDN del servidor original de la cadena de conexión: <*nombre del servidor*>. database.windows.net

FQDN del servidor modificado en la cadena de conexión: <*nombre del servidor*> .database. **segura**. windows.net

Incluye una lista parcial de "Clientes de nivel inferior":

- .NET 4.0 y a continuación,
- ODBC 10.0 y a continuación.
- JDBC (JDBC admiten TDS 7.4, la característica de redirección TDS no es totalmente compatible)
- Hace pesado (para Node.JS)

**Comentario:** El servidor anterior modificación FDQN puede ser útil para aplicar una directiva de auditoría del nivel de SQL Server sin necesidad de un paso de la configuración de cada base de datos (mitigación temporal).

##<a id="subheading-2"></a>Extremo IP cambia al habilitar la auditoría

Tenga en cuenta que al habilitar auditoría, cambiará el extremo IP de la base de datos. Si tiene una configuración de firewall estricto, actualice las configuraciones de firewall en consecuencia.

El nuevo extremo IP de base de datos dependen de la región de base de datos:

| Región de base de datos | Posibles extremos IP |
|----------|---------------|
| Norte de China  | 139.217.29.176, 139.217.28.254 |
| China oriental  | 42.159.245.65, 42.159.246.245 |
| Australia Oriental  | 104.210.91.32, 40.126.244.159, 191.239.64.60, 40.126.255.94 |
| Australia sureste | 191.239.184.223, 40.127.85.81, 191.239.161.83, 40.127.81.130 |
| Sur de Brasil  | 104.41.44.161, 104.41.62.230, 23.97.99.54, 104.41.59.191 |
| Central de EE.  | 104.43.255.70, 40.83.14.7, 23.99.128.244, 40.83.15.176 |
| Asia oriental   | 23.99.125.133, 13.75.40.42, 23.97.71.138, 13.94.43.245 |
| Estados Unidos oriental 2 | 104.209.141.31, 104.208.238.177, 191.237.131.51, 104.208.235.50 |
| Estados Unidos oriental   | 23.96.107.223, 104.41.150.122, 23.96.38.170, 104.41.146.44 |
| India central  | 104.211.98.219, 104.211.103.71 |
| India sur   | 104.211.227.102, 104.211.225.157 |
| India occidental  | 104.211.161.152, 104.211.162.21 |
| Japón oriental   | 104.41.179.1, 40.115.253.81, 23.102.64.207, 40.115.250.196 |
| Japón oeste    | 104.214.140.140, 104.214.146.31, 191.233.32.34, 104.214.146.198 |
| Norte Central de EE.  | 191.236.155.178, 23.96.192.130, 23.96.177.169, 23.96.193.231 |
| Europa del Norte  | 104.41.209.221, 40.85.139.245, 137.116.251.66, 40.85.142.176 |
| Sur Central de EE.  | 191.238.184.128, 40.84.190.84, 23.102.160.153, 40.84.186.66 |
| Sudeste asiático  | 104.215.198.156, 13.76.252.200, 23.97.51.109, 13.76.252.113 |
| Europa occidental  | 104.40.230.120, 13.80.23.64, 137.117.171.161, 13.80.8.37, 104.47.167.215, 40.118.56.193, 104.40.176.73, 40.118.56.20 |
| Estados Unidos occidental  | 191.236.123.146, 138.91.163.240, 168.62.194.148, 23.99.6.91 |
| Canadá Central  | 13.88.248.106 |
| Canadá oriental  |  40.86.227.82 |
