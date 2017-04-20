## <a name="send-messages-to-event-hubs"></a>Enviar mensajes a Hubs de evento

En esta sección escribimos una aplicación de C para enviar eventos a su centro de evento. Usaremos la biblioteca protones AMQP del [proyecto Qpid Apache](http://qpid.apache.org/). Esto equivale a usar colas de Bus de servicio y temas con AMQP de C como se muestra [aquí](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Para obtener más información, consulte la [documentación de Qpid protones](http://qpid.apache.org/proton/index.html).

1. En la [página Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html), haga clic en el vínculo de **Instalación de Qpid protones** y siga las instrucciones en función del entorno. Se considera un entorno Linux; Por ejemplo, una [Máquina virtual Linux de Azure](../articles/virtual-machines/virtual-machines-linux-quick-create-cli.md) con Ubuntu 14.04.

2. Para compilar la biblioteca protones, instale los paquetes siguientes:

    ```
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```

3. Descargar la biblioteca de [Qpid protones](http://qpid.apache.org/proton/index.html) y extraer, por ejemplo:

    ```
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```

4. Crear un directorio de generación, compilación e instale:

    ```
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```

5. En el directorio de trabajo, cree un nuevo archivo llamado **sender.c** con el siguiente contenido. No olvide sustituir el valor de su nombre de espacio de nombres y concentrador de evento (normalmente es el último `{event hub name}-ns`). También debe sustituir una versión de URL codificada de la clave para el **SendRule** que creó anteriormente. Puede codificar a la dirección URL [aquí](http://www.w3schools.com/tags/ref_urlencode.asp).

    ```
    #include "proton/message.h"
    #include "proton/messenger.h"

    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>

    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }  

    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  

    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }

    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";

        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();

        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);

        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));

        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);

        pn_message_free(message);
    }

    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");

        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);

        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }

        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);

        return 0;
    }
    ```

6. Compilar el archivo, suponiendo que **gcc**:

    ```
    gcc sender.c -o sender -lqpid-proton
    ```

> [AZURE.NOTE] En este código, usamos una ventana de salida de 1 para forzar los mensajes fuera tan pronto como sea posible. En general, la aplicación debería intentar mensajes por lotes para aumentar el rendimiento. Ver [página Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html) para obtener más información sobre cómo usar la biblioteca de Qpid protones en este y otros entornos y de plataformas para el que se proporcionan enlaces (actualmente Perl, PHP, Python y Ruby).
