<properties
   pageTitle="Comparación de almacén de lago de datos de Azure con Azure almacenamiento de blobs | Microsoft Azure"
   description="Comparación de almacén de lago de datos de Azure con Azure almacenamiento de blobs"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/15/2016"
   ms.author="nitinme"/>

# <a name="comparing-azure-data-lake-store-and-azure-blob-storage"></a>Comparación de lago almacén de datos de Azure y el almacenamiento de blobs de Windows Azure

La tabla en este artículo resume las diferencias entre el almacén de lago de datos de Azure y el almacenamiento de blobs de Windows Azure a lo largo de algunos aspectos clave general del procesamiento de datos. Almacenamiento de blobs de Windows Azure es un propósito general, el almacén de objetos scalable que está diseñado para una amplia variedad de escenarios de almacenamiento. Almacén de lago de datos de Azure es un repositorio de gran escala que está optimizado para cargas de trabajo de análisis de datos grande.

|    | Almacén de lago de datos de Azure  | Almacenamiento de blobs de Windows Azure  |
|----|-----------------------|--------------------|
| Propósito  | Almacenamiento de información optimizado para cargas de trabajo de análisis de datos grande                                                                          | Almacenamiento de objeto de propósito general para una amplia variedad de escenarios de almacenamiento                                                                                |
| Casos de uso                                   | El lote interactivos, streaming análisis y datos de aprendizaje de equipo como archivos de registro, IoT datos, haga clic en flujos de grandes conjuntos de datos | Realizar copias de cualquier tipo de datos de texto o binario, por ejemplo, la aplicación final, datos de copia de seguridad, almacenamiento de medios para los datos de finalidad streaming y general |
| Conceptos clave                                | Cuenta de almacén de datos lago contiene carpetas, que contiene los datos almacenados como archivos a su vez | Cuenta de almacenamiento tiene contenedores, que a su vez datos en forma de BLOB |
| Estructura | Sistema de archivos jerárquico                                                                                                    | Almacén de objetos con un espacio de nombres plano  |
| API   | API de REST sobre HTTPS | API de REST a través de HTTP/HTTPS                                                                                                                            |
| API de servidor                             | [API de REST de WebHDFS compatible](https://msdn.microsoft.com/library/azure/mt693424.aspx)                                                                                                 | [Almacenamiento de blobs de Windows Azure API de REST](https://msdn.microsoft.com/library/azure/dd135733.aspx)                                                                                                                         |
| Cliente de sistema de archivos Hadoop                   | Sí                                                                                                                         | Sí                                                                                                                                                 |
| Operaciones de datos - autenticación            | En función de [las identidades de Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md) | En función de la información confidencial compartido - [Compartido claves de firma de acceso](../storage/storage-dotnet-shared-access-signature-part-1.md)y [Teclas de acceso de la cuenta](../storage/storage-create-storage-account.md#manage-your-storage-account) .                                                                       |
| Operaciones de datos - protocolo de autenticación     | OAuth 2.0. Llamadas deben contener un JWT válido (JSON Web Token) emitido por Azure Active Directory                     | Código de autenticación de mensajes basado en hash (HMAC). Llamadas deben contener un hash SHA-256 codificado base 64 sobre una parte de la solicitud HTTP. |
| Operaciones de datos - autorización               | POSIX listas de Control de acceso (ACL).  ACL basadas en identidades de Azure Active Directory se pueden establecer el nivel de archivos y carpetas. | Para la autorización de nivel de cuenta: usar [Las teclas de acceso de cuenta](../storage/storage-create-storage-account.md#manage-your-storage-account)<br>De cuenta, contenedor o autorización de blobs: uso [Compartido claves de firma de Access](../storage/storage-dotnet-shared-access-signature-part-1.md) |
| Operaciones de datos - auditoría                    | Disponible. Consulte [aquí](data-lake-store-diagnostic-logs.md) para obtener información.                                                                                                                   | Disponible                                                                                                                                           |
| Cifrado de los datos almacenados                     | Transparente, lado del servidor (próximamente)<ul><li>Con claves administrado por el servicio</li><li>Con claves administrado por el cliente en KeyVault de Azure</li></ul>| <ul><li>Transparente, lado del servidor</li> <ul><li>Con claves administrados por el servicio</li><li>Con claves administrado por el cliente en KeyVault de Azure (próximamente)</li></ul><li>Cifrado de cliente</li></ul> |
| Operaciones de administración (por ejemplo, crear cuenta) | [Control de acceso basado en roles](../active-directory/role-based-access-control-what-is.md) (RBAC) proporcionados por Azure para administración de cuentas                                                                       | [Control de acceso basado en roles](../active-directory/role-based-access-control-what-is.md) (RBAC) proporcionados por Azure para administración de cuentas                                                                                               |
| SDK de desarrollador                              | . NET, Java, Node.js                                                                                                         | . NET, Java, Python, Node.js, C++, Ruby                                                                                                              |
| Rendimiento de carga de trabajo de análisis              | Rendimiento optimizado para cargas de trabajo de análisis paralelo. Alto rendimiento y IOPS.                                           | No está optimizado para las cargas de trabajo de análisis                                                                                                               |
| Límites de tamaño                                 | Sin límites de tamaño de la cuenta, tamaños de archivo o número de archivos                                                                   | Límites específicos documentado [aquí](../azure-subscription-service-limits.md#storage-limits)                                                                                                                     |
| Redundancia de geo                              | Localmente redundantes (varias copias de datos en una región de Azure)                                                             | Localmente redundantes (LRS), globalmente redundantes (GRS), acceso de lectura globalmente redundantes (RA de GRS). Consulte [aquí](../storage/storage-redundancy.md) para obtener más información |
| Estado del servicio                               | Vista previa de público                                                                                                              | Disponible de forma general                                                                                                                                 |
| Disponibilidad regional  | Consulte [aquí](https://azure.microsoft.com/regions/#services)| Consulte [aquí](https://azure.microsoft.com/regions/#services) |
| Precio                                       |     Consulte [precios](https://azure.microsoft.com/pricing/details/data-lake-store/)| Consulte [precios](https://azure.microsoft.com/pricing/details/storage/) |

### <a name="next-steps"></a>Pasos siguientes

- [Introducción a Azure lago de almacén de datos](data-lake-store-overview.md)
- [Empezar a trabajar con el almacén de datos de lago](data-lake-store-get-started-portal.md)



