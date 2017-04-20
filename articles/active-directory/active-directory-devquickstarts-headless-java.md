<properties
    pageTitle="Introducción a Azure AD Java | Microsoft Azure"
    description="Cómo crear una aplicación de línea de comandos de Java que inicie la sesión de los usuarios para tener acceso a una API."
    services="active-directory"
    documentationCenter="java"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
  ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>


# <a name="using-java-command-line-app-to-access-an-api-with-azure-ad"></a>Con la aplicación de la línea de comandos de Java tener acceso a una API con Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD facilita la forma muy sencilla delegar la administración de identidades de la aplicación web, proporcionar solo inicio de sesión y cierre de sesión con solo unas pocas líneas de código.  En las aplicaciones web de Java, puede realizar esto con la implementación de la ADAL4J dirigido a la Comunidad de Microsoft.

  Aquí, usaremos ADAL4J para:
- Inicie la sesión de usuario en la aplicación con Azure AD como el proveedor de identidades.
- Mostrar información sobre el usuario.
- Inicie sesión en el usuario de la aplicación.

Para ello, necesitará:

1. Registrar una aplicación con Azure AD
2. Configurar la aplicación para usar la biblioteca ADAL4J.
3. Usar la biblioteca ADAL4J a emitir solicitudes de inicio de sesión y cierre de sesión a Azure AD.
4. Imprimir datos sobre el usuario.

Para comenzar, [Descargue la estructura de la aplicación](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/skeleton.zip) o [descargar el ejemplo completo](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect\/archive/complete.zip).  También necesitará a un inquilino de Azure AD en el que se va a registrar la aplicación.  Si aún no tiene una, [Obtenga información sobre cómo obtener una](active-directory-howto-tenant.md).

## <a name="1--register-an-application-with-azure-ad"></a>1. registrar una aplicación con Azure AD
Para habilitar la aplicación autenticar usuarios, primero deberá registrar una nueva aplicación en su inquilino.

- Inicie sesión en el Portal de administración de Azure.
- En la barra de navegación izquierda, haga clic en **Active Directory**.
- Seleccione al inquilino donde desea registrar la aplicación.
- Haga clic en la pestaña de **aplicaciones** y haga clic en Agregar en la Bandeja de la parte inferior.
- Siga las indicaciones y cree una nueva **aplicación Web o WebAPI**.
    - El **nombre** de la aplicación describe la aplicación a los usuarios finales
    - La **Dirección URL de inicio de sesión** es la dirección URL base de la aplicación.  El valor predeterminado de la estructura es `http://localhost:8080/adal4jsample/`.
    - La **Aplicación identificador URI** es un identificador único para la aplicación.  La convención consiste en usar `https://<tenant-domain>/<app-name>`, p. ej.`http://localhost:8080/adal4jsample/`
- Una vez haya completado el registro, AAD asignar su aplicación un identificador único del cliente.  Necesitará este valor en las siguientes secciones, así que cópielo desde la ficha configurar.

Una vez en el portal de la aplicación cree un **Secreto de aplicación** de la aplicación y copiar hacia abajo.  Se necesita breve.


## <a name="2-set-up-your-app-to-use-adal4j-library-and-prerequisites-using-maven"></a>2. configurar la aplicación para usar la biblioteca de ADAL4J y los requisitos previos con experto
A continuación, se configurará ADAL4J para utilizar el protocolo de autenticación OpenID conectarse.  ADAL4J se usará para emitir solicitudes de inicio de sesión y cierre de sesión, administrar la sesión del usuario y obtener información sobre el usuario, entre otras cosas.

-   En el directorio raíz de su proyecto, abrir o crear `pom.xml` y busque el `// TODO: provide dependencies for Maven` y reemplazar con la siguiente:

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>public-client-adal4j-sample</artifactId>
    <packaging>jar</packaging>
    <version>0.0.1-SNAPSHOT</version>
    <name>public-client-adal4j-sample</name>
    <url>http://maven.apache.org</url>
    <properties>
        <spring.version>3.0.5.RELEASE</spring.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>adal4j</artifactId>
            <version>1.1.2</version>
        </dependency>
        <dependency>
            <groupId>com.nimbusds</groupId>
            <artifactId>oauth2-oidc-sdk</artifactId>
            <version>4.5</version>
        </dependency>
        <dependency>
            <groupId>org.json</groupId>
            <artifactId>json</artifactId>
            <version>20090211</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.5</version>
        </dependency>
</dependencies>
    <build>
        <finalName>public-client-adal4j-sample</finalName>
        <plugins>
                <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>1.2.1</version>
            <configuration>
                <mainClass>PublicClient</mainClass>
            </configuration>
        </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <executions>
                    <execution>
                        <id>install</id>
                        <phase>install</phase>
                        <goals>
                            <goal>sources</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <version>2.5</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
      </plugin>
      <plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>PublicClient</mainClass>
      </manifest>
    </archive>
  </configuration>
</plugin>
        </plugins>
    </build>

</project>


```



## <a name="3-create-the-java-publicclient-file"></a>3. crear el archivo de PublicClient de java

Como se indicó anteriormente, vamos a usar la API de gráfico para obtener datos sobre el usuario que ha iniciado sesión. Para que resulte fácil nos debemos creamos un archivo para representar un **Objeto de directorio** y un archivo individual para representar al **usuario** para que se puede utilizar el modelo OO de Java.

1. Cree un archivo llamado `DirectoryObject.java` que usamos para almacenar datos básicos sobre cualquier DirectoryObject (que puede no dude en usar este posteriormente para todas las consultas de gráfico que puede hacer). Se puede cortar y pegar esta de los siguientes:

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;

public class PublicClient {

    private final static String AUTHORITY = "https://login.microsoftonline.com/common/";
    private final static String CLIENT_ID = "2a4da06c-ff07-410d-af8a-542a512f5092";

    public static void main(String args[]) throws Exception {

        try (BufferedReader br = new BufferedReader(new InputStreamReader(
                System.in))) {
            System.out.print("Enter username: ");
            String username = br.readLine();
            System.out.print("Enter password: ");
            String password = br.readLine();

            AuthenticationResult result = getAccessTokenFromUserCredentials(
                    username, password);
            System.out.println("Access Token - " + result.getAccessToken());
            System.out.println("Refresh Token - " + result.getRefreshToken());
            System.out.println("ID Token - " + result.getIdToken());
        }
    }

    private static AuthenticationResult getAccessTokenFromUserCredentials(
            String username, String password) throws Exception {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(AUTHORITY, false, service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", CLIENT_ID, username, password,
                    null);
            result = future.get();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }
}


```


##<a name="compile-and-run-the-sample"></a>Compilar y ejecutar el ejemplo

Cambiar vuelve a su directorio raíz y ejecute el comando siguiente para generar el ejemplo simplemente colocar juntos con `maven`. Usará el `pom.xml` escribir las dependencias de archivo.

`$ mvn package`

Ahora debería tener un `adal4jsample.war` de archivos en su `/targets` directorio. Puede implementar en el contenedor de Tomcat y visitar la dirección URL 

`http://localhost:8080/adal4jsample/`


> [AZURE.NOTE] 
Es muy fácil de implementar una guerra con los servidores de Tomcat más recientes. Simplemente vaya a `http://localhost:8080/manager/` y siga las instrucciones de cargar su "adal4jsample.war' archivo. Lo hará autodeploy automáticamente con el extremo correcto.

##<a name="next-steps"></a>Pasos siguientes

¡Felicidades! Ahora tiene un aplicación Java que tiene la capacidad para autenticar usuarios, de forma segura de trabajo llamar a las API de Web mediante OAuth 2.0 y obtenga información básica sobre el usuario.  Si todavía no lo ha hecho, ahora es el momento para rellenar al inquilino de algunos usuarios.

Para referencia, el ejemplo completo (sin los valores de configuración) [se proporciona como un .zip aquí](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/complete.zip)o puede clonar ella desde GitHub:

```git clone --branch complete https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect.git```

