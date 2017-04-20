# <a name="securing-your-iot-deployment"></a>Protección de su implementación de IoT

Este artículo proporciona el siguiente nivel de detalle para proteger la infraestructura basada en Azure IoT Internet de las cosas (IoT). Vincula a detalles a nivel de implementación para la configuración e implementación de cada componente. También proporciona comparaciones y opciones entre diversos métodos de competencia.

Protección de la implementación de Azure IoT puede dividirse en las siguientes áreas de seguridad de tres:

- **Dispositivo de seguridad**: proteger el dispositivo de IoT mientras se implementa en la naturaleza.
- **Seguridad de conexión**: asegurar todos los datos transmitidos entre el dispositivo de IoT y IoT concentrador es confidencial y a prueba de manipulaciones.
- **Seguridad de nube**: proporcionar un medio para proteger los datos mientras se mueve a través de y se almacena en la nube.

![Tres áreas de seguridad][img-overview]

## <a name="secure-device-provisioning-and-authentication"></a>Aprovisionamiento de dispositivos segura y autenticación

La Suite de Azure IoT protege IoT dispositivos mediante los dos métodos siguientes:

- Al proporcionar una clave de identidad única (tokens de seguridad) para cada dispositivo, que se puede utilizar el dispositivo para comunicarse con el concentrador de IoT.

- Mediante un [certificado X.509] en el dispositivo[ lnk-x509] y la clave privada como un medio para autenticar el dispositivo al concentrador IoT. Este método de autenticación asegura que la clave privada en el dispositivo no se conoce fuera del dispositivo en cualquier momento, proporcionar un mayor nivel de seguridad.

El método de token de seguridad proporciona autenticación para cada llamada realizada por el dispositivo concentrador IoT asociando la clave simétrica para cada llamada. Autenticación basada en X.509 permite la autenticación de un dispositivo de IoT en la capa física como parte del establecimiento de la conexión TLS. El método basado en tokens de seguridad puede utilizarse sin la autenticación X.509 que es un patrón menos seguro. Cómo proteger la autenticación de dispositivos deba estar y disponibilidad de almacenamiento seguro en el dispositivo (para almacenar de forma segura la clave privada) dependerá principalmente la elección entre los dos métodos.

## <a name="iot-hub-security-tokens"></a>Tokens de seguridad concentrador IoT

Concentrador de IoT utiliza tokens de seguridad para autenticar los dispositivos y servicios para evitar el envío de claves de la red. Además, tokens de seguridad están limitados en su alcance y periodo de validez. SDK de Azure IoT concentrador automáticamente generar tokens sin necesidad de ninguna configuración especial. Algunos escenarios, sin embargo, requieren que el usuario generar y utilizar directamente los tokens de seguridad. Estos incluyen el uso directo de las superficies MQTT, AMQP o HTTP, o la implementación del modelo de servicio de token.

Pueden encontrarse más detalles sobre la estructura del token de seguridad y su uso en los siguientes artículos:

-   [Estructura del token de seguridad][lnk-security-tokens]
-   [Uso de tokens SAS como un dispositivo][lnk-sas-tokens]

Cada concentrador IoT tiene un [Registro de la identidad de dispositivo] [ lnk-identity-registry] que puede utilizarse para crear recursos de cada dispositivo en el servicio, como una cola que contiene mensajes de dispositivo de nube en vuelo y para permitir el acceso a los extremos a través de dispositivo. El registro de la identidad de concentrador IoT proporciona un almacenamiento seguro de identidades de dispositivo y las claves de seguridad para una solución. Individuales o grupos de identidades del dispositivo pueden agregarse a una lista Permitir o una lista de bloqueo, lo que permite el control completo sobre el acceso a los dispositivos. Los artículos siguientes proporcionan más detalles sobre la estructura del registro de identidad del dispositivo y operaciones admiten.

[Concentrador de IoT admite protocolos como HTTP, AMQP y MQTT][lnk-protocols]. Cada uno de estos protocolos utilizar tokens de seguridad desde el dispositivo IoT IoT concentrador diferente:

- AMQP: SASL simple y basada en notificaciones de AMQP seguridad ({policyName}@sas.root.{iothubName} en el caso de los símbolos (token) de nivel de concentrador; {deviceId} en el caso de tokens de ámbito por el dispositivo).

- MQTT: Conectar utiliza paquetes {deviceId} como {ClientId}, {IoThubhostname} / {deviceId} en el campo de **nombre de usuario** y un símbolo (token) SAS en el campo **contraseña** .

- HTTP: Token válido está en el encabezado de la solicitud de autorización.

Registro de identidad de IoT concentrador dispositivos puede utilizarse para configurar las credenciales de seguridad de cada dispositivo y control de acceso. Sin embargo, si una solución IoT ya tiene una importante inversión en un [esquema de registro o autenticación de identidad dispositivo personalizado][lnk-custom-auth], pueden integrarse en una infraestructura existente con concentrador IoT mediante la creación de un servicio de token.

### <a name="x509-certificate-based-device-authentication"></a>Autenticación de dispositivos basados en certificados X.509

El uso de un [certificado X.509 dispositivo] [ lnk-use-x509] y su par de claves pública y privada asociado permite autenticación adicional en la capa física. La clave privada se almacena de forma segura en el dispositivo y no es reconocible fuera del dispositivo. El certificado X.509 contiene información acerca del dispositivo, como el ID de dispositivo y otros detalles organizativos. Se genera una firma del certificado mediante la clave privada.

Flujo de aprovisionamiento de dispositivo de alto nivel:

- Asociar un identificador a un dispositivo físico: identidad del dispositivo o el certificado X.509 asociado al dispositivo durante la fabricación o puesta en servicio de los dispositivos.

- Crear una entrada de identidad correspondiente en concentrador IoT: identidad del dispositivo e información de dispositivo asociado en el registro de dispositivo concentrador IoT.

- Almacenar de forma segura huella digital del certificado X.509 en el registro del dispositivo concentrador IoT.

### <a name="root-certificate-on-device"></a>Certificado raíz en el dispositivo

Al establecer una conexión TLS segura con IoT concentrador, el dispositivo IoT autentica concentrador IoT con un certificado raíz que forma parte del SDK de dispositivo. Para el cliente C SDK, el certificado se encuentra en la carpeta "\\c\\certificados" bajo la raíz del repositorio. Aunque estos certificados raíz son de larga duración, todavía pueden caducar o ser revocados. Si no hay ninguna manera de actualizar el certificado en el dispositivo, es posible que el dispositivo no podrá conectarse posteriormente al concentrador IoT (o cualquier otro servicio de nube). Tiene un medio para actualizar el certificado raíz una vez implementado el dispositivo IoT efectivamente se mitigar este riesgo.

## <a name="securing-the-connection"></a>Protección de la conexión

Conexión a Internet entre el dispositivo de IoT y concentrador IoT está protegida mediante el estándar de seguridad de capa de transporte (TLS). IoT Azure es compatible con [TLS 1.2][lnk-tls12], TLS 1.1 y 1.0 de TLS, en este orden. Compatibilidad con TLS 1.0 se proporciona para la compatibilidad con versiones anteriores. Se recomienda utilizar TLS 1.2, ya que proporciona la máxima seguridad.

Suite de IoT Azure es compatible con los siguientes conjuntos de cifrado, en este orden.

| Conjunto de cifrado | Longitud |
|--------------|--------|
| TLS\_ECDHE\_RSA\_con\_AES\_256\_CBC\_SHA384 secp384r1 ECDH (0xc028) (EC. 7680 bits RSA) FS | 256    |
| TLS\_ECDHE\_RSA\_con\_AES\_128\_CBC\_SHA256 secp256r1 ECDH (0xc027) (EC. FS 3072 bits RSA) | 128    |
| TLS\_ECDHE\_RSA\_con\_AES\_256\_CBC\_SHA (0xc014) secp384r1 ECDH (EC. 7680 bits RSA) FS           | 256    |
| TLS\_ECDHE\_RSA\_con\_AES\_128\_CBC\_SHA (0xc013) secp256r1 ECDH (EC. FS 3072 bits RSA)           | 128    |
| TLS\_RSA\_con\_AES\_256\_GCM\_SHA384 (0x9d) | 256    |
| TLS\_RSA\_con\_AES\_128\_GCM\_SHA256 (0x9c) | 128    |
| TLS\_RSA\_con\_AES\_256\_CBC\_SHA256 (0x3d) | 256    |
| TLS\_RSA\_con\_AES\_128\_CBC\_SHA256 (0x3c) | 128    |
| TLS\_RSA\_con\_AES\_256\_CBC\_SHA (0x35)    | 256    |
| TLS\_RSA\_con\_AES\_128\_CBC\_SHA (0x2f)    | 128    |
| TLS\_RSA\_con\_3DES\_EDE\_CBC\_SHA (0xa)    | 112    |

## <a name="securing-the-cloud"></a>Protección de la nube

Concentrador de IoT Azure permite la definición de [las políticas de control de acceso] [ lnk-protocols] para cada clave de seguridad. Utiliza el siguiente conjunto de permisos para conceder acceso a cada uno de los extremos del concentrador IoT. Permisos de limitan el acceso a un concentrador de IoT basado en la funcionalidad.

- **RegistryRead**. Concede acceso de lectura al registro de identidad del dispositivo. Para obtener más información, vea [Registro de identidad de dispositivo][lnk-identity-registry].

- **RegistryReadWrite**. Subvenciones de lectura y acceso de escritura al registro de identidad del dispositivo. Para obtener más información, vea [Registro de identidad de dispositivo][lnk-identity-registry].

- **ServiceConnect**. Concede acceso a la nube orientada al servicio de comunicación y supervisión de extremos. Por ejemplo, concede permiso a los servicios de nube de servicios de fondo para recibir mensajes del dispositivo a la nube, enviar mensajes de nube al dispositivo y recuperar las confirmaciones de entrega correspondiente.

- **DeviceConnect**. Concede acceso a los extremos de la comunicación a través de dispositivo. Por ejemplo, concede permiso para enviar mensajes del dispositivo a la nube y recibir mensajes de nube al dispositivo. Este permiso es utilizado por los dispositivos.

Existen dos formas para obtener los permisos de **DeviceConnect** con concentrador IoT con [tokens de seguridad][lnk-sas-tokens]: utilizando una clave de identidad de dispositivo o una clave de directiva de acceso compartido. Además, es importante destacar que todas las funciones accesibles desde dispositivos está expuesta por diseño en los extremos con el prefijo `/devices/{deviceId}`.

[Componentes de servicio sólo pueden generar tokens de seguridad] [ lnk-service-tokens] utilizar comparten las directivas de acceso que concede los permisos apropiados.

Concentrador de Azure IoT y otros servicios que pueden formar parte de la solución permiten la administración de usuarios con Active Directory de Azure.

Ingerida por Azure IoT concentrador de datos pueden consumir una gran variedad de servicios como análisis de secuencia de Azure y almacenamiento Azure blob. Estos servicios permiten el acceso a la administración. Lea más sobre estos servicios y opciones disponibles a continuación:

- [Azure DocumentDB][lnk-docdb]: un servicio de base de datos escalable, totalmente indizados para datos semiestructurados que administra los metadatos para los dispositivos de provisión, como atributos, configuración y las propiedades de seguridad. DocumentDB ofrece procesamiento de alto rendimiento y de alto rendimiento, independiente del esquema de indización de los datos y una completa interfaz de consulta SQL.

- [Análisis de secuencia Azure][lnk-asa]: en la nube que permite desarrollar e implementar con rapidez una solución de análisis de bajo costo para descubrir perspectivas en tiempo real de dispositivos, sensores, infraestructura y aplicaciones de procesamiento de flujo en tiempo real. Los datos de este servicio totalmente gestionado pueden escalar a cualquier volumen al mismo tiempo conseguir resistencia, baja latencia y alto rendimiento.

- [Servicios de Azure App][lnk-appservices]: una plataforma de nube para generar eficaces web y aplicaciones móviles que se conectan a los datos en cualquier lugar; en la nube o local. Generar la participación de aplicaciones móviles para Windows, iOS y Android. Se integran con el Software como servicio (SaaS) y las aplicaciones empresariales con conectividad de cuadro decenas de servicios basados en cloud y aplicaciones empresariales. El código de su lenguaje favorito y IDE (. NET, NodeJS, PHP, Python o Java) para crear aplicaciones web y las API más rápido que nunca.

- [Lógica de aplicaciones][lnk-logicapps]: característica de aplicaciones de la lógica de servicio de la aplicación de Azure ayuda a integrar la solución IoT para los sistemas de línea de negocio existentes y automatizar los procesos de flujo de trabajo. Lógica de aplicaciones permite a los desarrolladores diseñar flujos de trabajo que se inician desde un desencadenador y, a continuación, ejecutan una serie de pasos, reglas y acciones que utilizan conectores eficaces de integrar con los procesos de negocio. Lógica de aplicaciones ofrece conectividad de cuadro a un gran ecosistema de SaaS, basada en la nube y aplicaciones locales.

- [Almacenamiento de Azure blob][lnk-blob]: almacenamiento en nube fiable y económica para los datos que los dispositivos se envían a la nube.

## <a name="conclusion"></a>Conclusión

Este artículo proporciona información general sobre la implementación de detalles a nivel de diseño e implementación de una infraestructura de IoT con Azure IoT. La configuración de cada componente para que sea seguro es clave en la protección de toda la infraestructura de IoT. Las opciones de diseño disponibles en Azure IoT proporcionan cierto nivel de flexibilidad y elección; Sin embargo, cada opción puede tener implicaciones de seguridad. Se recomienda que cada una de estas opciones se evalúan a través de una evaluación del riesgo y costo.

[img-overview]: media/iot-secure-your-deployment/overview.png

[lnk-security-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure
[lnk-sas-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-as-a-device
[lnk-identity-registry]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-protocols]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-custom-auth]: ../articles/iot-hub/iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: http://www.itu.int/rec/T-REC-X.509-201210-I/en
[lnk-use-x509]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-tls12]: https://tools.ietf.org/html/rfc5246
[lnk-service-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#using-security-tokens-from-service-components
[lnk-docdb]: https://azure.microsoft.com/services/documentdb/
[lnk-asa]: https://azure.microsoft.com/services/stream-analytics/
[lnk-appservices]: https://azure.microsoft.com/services/app-service/
[lnk-logicapps]: https://azure.microsoft.com/services/app-service/logic/
[lnk-blob]: https://azure.microsoft.com/services/storage/
