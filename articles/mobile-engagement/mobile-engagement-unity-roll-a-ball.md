<properties
    pageTitle="Álbum de la unidad un tutorial bola"
    description="Pasos para crear la unidad clásica dar un juego de bola que es un requisito previo para todos los tutoriales de unidad de compromiso de móvil"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

#<a id="unity-roll-a-ball"></a>Crear unidad dar un juego de bola

Este tutorial se guiará por los pasos principales para un [álbum de la unidad un tutorial bola](http://unity3d.com/learn/tutorials/projects/roll-ball-tutorial)de modificado ligeramente. Este juego de ejemplo consta de un objeto esférica 'jugador' controlada por el usuario de la aplicación y el objetivo del juego es recopilar objetos recopilables por choque el objeto Reproductor con estos objetos recopilables. Esto supone familiaridad básica con el entorno del editor de unidad. Si tiene problemas, a continuación, consulte el tutorial completo. 

### <a name="setting-up-the-game"></a>Configurar el juego
Los pasos siguientes son desde el [tutorial de unidad](https://unity3d.com/learn/tutorials/projects/roll-a-ball/set-up?playlist=17141)

1. Abra el **Editor de unidad** y haga clic en **nuevo**. 
    
    ![][51] 
    
2. Proporcione un **nombre de proyecto** & **ubicación**, seleccione **3D** y haga clic en **crear un proyecto**.
    
    ![][52]

3. Guardar la escena predeterminada que acaba de crear como parte del nuevo proyecto como con el nombre **MiniGame** dentro de un nuevo ** \_escenas** carpeta situada debajo de la carpeta de **activos** :
    
    ![][53]

4. Crear un **objeto 3D -> plano** como el campo de juego y cambiar el nombre de este objeto de plano como **superficie**

    ![][1]

5. Restablecer el componente de transformación de este objeto de **superficie** para que esté en el origen. 

    ![][3]

6. Desactive **Mostrar cuadrícula** en **el menú de chismes** para el objeto de **superficie** .

    ![][4]

7. Actualizar el componente de **escala** para el objeto de **superficie** sea [X = 2, Y = 1, Z = 2]. 

    ![][5]

8. Agregar un nuevo **objeto 3D -> ámbito** al proyecto y cambiar el nombre de este objeto de ámbito como **Reproductor**. 

    ![][6]

9. Seleccione el objeto de **Reproductor** y haga clic en **Restablecer transformación** similar al objeto de plano. 

10. Actualización **transformación -> posición -> Y coordenadas** componente para la Y el Reproductor de 0,5.  

    ![][7]

11. Crear una nueva carpeta llamada **materiales** del proyecto donde se crea el material para el Reproductor de color. 

12. Crear un nuevo **Material** denominado **fondo** en esta carpeta. 

    ![][8]

13. Actualizar el color del material al actualizar la propiedad **Albedo** del mismo. Puede seleccionar los valores RGB de [0,32,64]. 

    ![][9]

14. Arrastre este material a la vista de la escena para aplicar color a objeto de **superficie** . 

    ![][10]

17. Por último actualizar el **transformación -> giro -> Y** 60 en el objeto de luz direccional para claridad. 

    ![][12]

### <a name="moving-the-player"></a>Mover el Reproductor
Los pasos siguientes son desde el [tutorial de unidad](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-player?playlist=17141)

1. Agregar un componente de **RigidBody** al objeto **Reproductor** . 

    ![][13]

2. Crear una nueva carpeta llamada **Scripts** en el proyecto. 

3. Haga clic en **Agregar componente -> nueva secuencia de comandos -> C# Script**. Asígnele el nombre **PlayerController**y haga clic en **crear y agregar**. Esto creará y adjuntar una secuencia de comandos para el objeto del Reproductor.  

    ![][14]

5. Mover esta secuencia de comandos en la carpeta de **secuencias de comandos** en el proyecto. 

6. Abra la secuencia de comandos para su edición en el editor de secuencias de comandos favoritos, actualice el código de secuencia de comandos con el siguiente código y guárdelo. 

        using UnityEngine;
        using System.Collections;
        
        public class PlayerController : MonoBehaviour 
        {
            public float speed;
            private Rigidbody rb;
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
                rb.AddForce (movement * speed);
            }
        }
    
8. Observe que la secuencia de comandos anterior utiliza una propiedad de **velocidad** . En el editor de unidad, actualice la propiedad velocidad a 10.  

    ![][15]

9. Haga clic en **Reproducir** en el Editor de unidad. Ahora debería poder controlar la bola mediante el teclado y debe girar y desplazarse. 

### <a name="moving-the-camera"></a>Mover la cámara
Los pasos siguientes son desde el [tutorial de unidad](https://unity3d.com/learn/tutorials/projects/roll-a-ball/moving-the-camera?playlist=17141) y fuerzan la **Cámara principal** para el objeto del **Reproductor** . 

1. Actualizar la **Transform.Position** para ser X = 0, Y = 10.5, Z =-10.  
2. Actualizar la **Transform.Rotation** para ser X = 45, Y = 0, Z = 0.  

    ![][16]

2. Agregar un script nuevo llamado **CameraController** a la **MainCamera** y desplazarse dentro de la carpeta de secuencias de comandos. 

    ![][17]

3. Abra la secuencia de comandos para su edición y agregue el código siguiente en él:

        using UnityEngine;
        using System.Collections;
        
        public class CameraController : MonoBehaviour {
        
            public GameObject player;
        
            private Vector3 offset;
        
            void Start ()
            {
                offset = transform.position - player.transform.position;
            }
            
            void LateUpdate ()
            {
                transform.position = player.transform.position + offset;
            }
        }
    
5. En el entorno de unidad - arrastre la variable del Reproductor la franja de Reproductor para el objeto de la cámara principal para que los dos están asociados entre sí. 

    ![][18]

6. Ahora si haga clic en Reproducir en el editor de unidad y girar el objeto Reproductor bola se muestra la cámara siguiendo el movimiento.  

### <a name="setting-up-the-play-area"></a>Configurar el área de reproducción
Los pasos siguientes son desde el [tutorial de unidad](https://unity3d.com/learn/tutorials/projects/roll-a-ball/setting-up-the-play-area?playlist=17141). Vamos a crear las paredes que rodean conceptos básicos para que el objeto de Reproductor bola no colocar fuera del área de reproducción en su movimiento. 

1. Haga clic en **crear -> Crear vacía -> juego objeto** y asígnele el nombre **paredes**

    ![][19]

2. En este objeto paredes - crear un nuevo **objeto 3D -> cubo** y asígnele el nombre "Pared Oeste". 

    ![][20]

3. Actualizar la **transformación -> posición** y **Transformar -> escala** para este objeto de pared oeste. 

    ![][21]

4. Duplicar la pared Oeste para crear una **pared oriental** con la escala y posición de transformación actualizado. 

    ![][22]

5. Duplicar la pared Oriente para crear una **pared Norte** con la posición de transformación actualizada y escala. 

    ![][23]

6. Duplicar la pared Norte y cree una **pared sur** con la posición de transformación actualizada y escala. 

    ![][24]

### <a name="creating-collectible-objects"></a>Crear objetos recopilables
Los pasos siguientes son desde el [tutorial de unidad](https://unity3d.com/learn/tutorials/projects/roll-a-ball/creating-collectables?playlist=17141). Vamos a crear algunos objetos de aspecto atractivos que forman el conjunto de objetos recopilables que el objeto de Reproductor bola necesita recopilar por choque con ellos. 

1. Crear un nuevo **objeto de cubo 3D** y asígnele el nombre recogida. 

2. Ajustar la **transformación -> giro** & **transformación -> escala** del objeto recogido. 

    ![][25]

3. Crear y adjuntar un **Script nuevo C#** llamado **rotación** al objeto recogido. Asegúrese de poner la secuencia de comandos en la carpeta de secuencias de comandos. 

    ![][26]

4. Abrir este script para editarlo y actualizar para que sea lo siguiente: 

        using UnityEngine;
        using System.Collections;
        
        public class Rotator : MonoBehaviour {
        
            void Update () 
            {
                transform.Rotate (new Vector3 (15, 30, 45) * Time.deltaTime);
            }
        }

5. A continuación, pulse Girar el modo de reproducción en el Editor de unidad y la presentación de objeto recogida en el eje.

6. Crear una nueva carpeta llamada **Prefabs** 

    ![][27]

7. Arrastre el objeto **recogida** y colóquelo en la carpeta Prefabs.

    ![][28]

8. Crear un nuevo **objeto del juego vacío** denominado **camionetas**. Restablecer su posición en el origen y, a continuación, arrastre el objeto recogido en este objeto juego.  

    ![][29]

9. Duplicar el objeto **recogida** y separe en el objeto de **superficie** alrededor del objeto **Reproductor** actualizando los valores **de Transform.Position X y Z** adecuadamente. 

    ![][30]

10. Crear un **nuevo material** denominado **recogida** y actualizar para que sea rojo en color actualizando la **propiedad Albedo** similar a lo que hizo para actualizar el objeto de superficie. 

    ![][31]

11. Aplicar el material a todos los objetos de recogida 4.

    ![][32]

### <a name="collecting-the-pickup-objects"></a>Recopilar los objetos recogidos
Los pasos siguientes son desde el [tutorial de unidad](https://unity3d.com/learn/tutorials/projects/roll-a-ball/collecting-pick-up-objects?playlist=17141). Actualizaremos el Reproductor para que sea capaz de recopilar los objetos recogidos por choque con ellos. 

1. Abra la secuencia de comandos de **PlayerController** adjuntado al objeto Reproductor para editar y actualizar a la siguiente:  

        using UnityEngine;
        using System.Collections;
        
        public class PlayerController : MonoBehaviour {
        
            public float speed;
        
            private Rigidbody rb;
        
            void Start ()
            {
                rb = GetComponent<Rigidbody>();
            }
        
            void FixedUpdate ()
            {
                float moveHorizontal = Input.GetAxis ("Horizontal");
                float moveVertical = Input.GetAxis ("Vertical");
        
                Vector3 movement = new Vector3 (moveHorizontal, 0.0f, moveVertical);
        
                rb.AddForce (movement * speed);
            }
        
            void OnTriggerEnter(Collider other) 
            {
                if (other.gameObject.CompareTag ("Pick Up"))
                {
                    other.gameObject.SetActive (false);
                }
            }
        }

2. Crear una nueva **etiqueta** denominado **Elija hacia arriba** (debe coincidir con lo que está en la secuencia de comandos)  

    ![][33]
    
    ![][34]

3. Aplicar esta **etiqueta** al objeto Prefab recogida. 

    ![][35]

4. Active la casilla de verificación de **IsTrigger** para el objeto Prefab.

    ![][36]

5. Agregue un cuerpo rígido recogida Prefab objeto. Para optimizar el rendimiento actualizaremos la collider estático que hemos usado para un collider dinámica. 

    ![][37]
  
6. Por último, compruebe la propiedad **IsKinematic** para el objeto prefab. 

    ![][38]

7. Visitas **Reproducir** en el editor de unidad y se podrán jugar **dar una bola** moviendo el objeto de Reproductor utilizando las teclas del teclado para la entrada de dirección. 

### <a name="updating-the-game-for-mobile-play"></a>Actualizar el juego para reproducir móvil
Las secciones anteriores concluye el tutorial de unidad básico. Ahora vamos a modificar el juego para que sea un dispositivo móvil descriptivo. Tenga en cuenta que utilizamos entrada del teclado del juego hasta ese momento para realizar pruebas. Ahora vamos a modificar, por lo que podemos controlar el Reproductor utilizando el movimiento del teléfono, es decir usando situado como entrada. 

Abra la secuencia de comandos **PlayerController** para editar y actualizar el método **FixedUpdate** para usar el movimiento de la situado para mover el objeto del Reproductor. 

        void FixedUpdate()
        {
            //float moveHorizontal = Input.GetAxis("Horizontal");
            //float moveVertical = Input.GetAxis("Vertical");
            //Vector3 movement = new Vector3(moveHorizontal, 0.0f, moveVertical);
            rb.AddForce(Input.acceleration.x * Speed, 0, -Input.acceleration.z * Speed);
        }

Este tutorial finaliza la creación de un juego básica con unidad y se puede implementar en un dispositivo de su elección para jugar. 

<!-- Images -->
[1]: ./media/mobile-engagement-unity-roll-a-ball/1.png  
[2]: ./media/mobile-engagement-unity-roll-a-ball/2.png
[3]: ./media/mobile-engagement-unity-roll-a-ball/3.png
[4]: ./media/mobile-engagement-unity-roll-a-ball/4.png
[5]: ./media/mobile-engagement-unity-roll-a-ball/5.png
[6]: ./media/mobile-engagement-unity-roll-a-ball/6.png
[7]: ./media/mobile-engagement-unity-roll-a-ball/7.png
[8]: ./media/mobile-engagement-unity-roll-a-ball/8.png
[9]: ./media/mobile-engagement-unity-roll-a-ball/9.png  
[10]: ./media/mobile-engagement-unity-roll-a-ball/10.png    
[11]: ./media/mobile-engagement-unity-roll-a-ball/11.png    
[12]: ./media/mobile-engagement-unity-roll-a-ball/12.png
[13]: ./media/mobile-engagement-unity-roll-a-ball/13.png
[14]: ./media/mobile-engagement-unity-roll-a-ball/14.png
[15]: ./media/mobile-engagement-unity-roll-a-ball/15.png
[16]: ./media/mobile-engagement-unity-roll-a-ball/16.png
[17]: ./media/mobile-engagement-unity-roll-a-ball/17.png
[18]: ./media/mobile-engagement-unity-roll-a-ball/18.png
[19]: ./media/mobile-engagement-unity-roll-a-ball/19.png    
[20]: ./media/mobile-engagement-unity-roll-a-ball/20.png    
[21]: ./media/mobile-engagement-unity-roll-a-ball/21.png    
[22]: ./media/mobile-engagement-unity-roll-a-ball/22.png    
[23]: ./media/mobile-engagement-unity-roll-a-ball/23.png    
[24]: ./media/mobile-engagement-unity-roll-a-ball/24.png    
[25]: ./media/mobile-engagement-unity-roll-a-ball/25.png    
[26]: ./media/mobile-engagement-unity-roll-a-ball/26.png    
[27]: ./media/mobile-engagement-unity-roll-a-ball/27.png    
[28]: ./media/mobile-engagement-unity-roll-a-ball/28.png    
[29]: ./media/mobile-engagement-unity-roll-a-ball/29.png    
[30]: ./media/mobile-engagement-unity-roll-a-ball/30.png    
[31]: ./media/mobile-engagement-unity-roll-a-ball/31.png    
[32]: ./media/mobile-engagement-unity-roll-a-ball/32.png    
[33]: ./media/mobile-engagement-unity-roll-a-ball/33.png    
[34]: ./media/mobile-engagement-unity-roll-a-ball/34.png    
[35]: ./media/mobile-engagement-unity-roll-a-ball/35.png    
[36]: ./media/mobile-engagement-unity-roll-a-ball/36.png    
[37]: ./media/mobile-engagement-unity-roll-a-ball/37.png    
[38]: ./media/mobile-engagement-unity-roll-a-ball/38.png    
[51]: ./media/mobile-engagement-unity-roll-a-ball/new-project.png
[52]: ./media/mobile-engagement-unity-roll-a-ball/new-project-properties.png
[53]: ./media/mobile-engagement-unity-roll-a-ball/save-scene.png

    
    
    
    
    
    
    
    
    
    
    
    
