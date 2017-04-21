<properties
   pageTitle="Título de la página que se muestra en los resultados de búsqueda y la pestaña del explorador"
   description="Descripción de artículo que se mostrarán en las páginas de inicio y en la mayoría de los resultados de búsqueda"
   services="service-name"
   documentationCenter="dev-center-name"
   authors="GitHub-alias-of-only-one-author"
   manager="manager-alias"
   editor=""/>

<tags
   ms.service="required"
   ms.devlang="may be required"
   ms.topic="article"
   ms.tgt_pltfrm="may be required"
   ms.workload="required"
   ms.date="mm/dd/yyyy"
   ms.author="Your MSFT alias or your full email address;semicolon separates two or more"/>

# <a name="markdown-template-article-heading-1-or-h1-heading-at-the-top-of-the-article"></a>Plantilla de descuento (artículo título 1 o H1): título en la parte superior del artículo

Para copiar la reducción de esta plantilla, copie el artículo en su repo local, o haga clic en el botón sin formato de la UI GitHub y copie el descuento.

  ![Texto alternativo; No deje en blanco. Describa la imagen.][8]

Párrafo introductorio: Lorem dolor amet, adipiscing elit. Phasellus interdum nulla risus, lacinia portal Suspendisse imperdiet archivo SED. Mauris dolor mauris, tempus sed lacinia nec, euismod no felis. NUNC semper portal ultrices. Maecenas neque nulla, condimentum vitae ipsum sentarse amet, aliquet dignissim nisi.

## <a name="heading-2-h2"></a>Título 2 (H2)

Aenean sentarse amet leo nec purus AC fermentum ac gravida odio. Aenean tellus lectus, faucibus en rhoncus en faucibus sed urna.  volutpat mi identificador purus ultrices iaculis nec no neque. [Texto del vínculo vínculo fuera de azure.microsoft.com](http://weblogs.asp.net/scottgu). Dolor de dictum Nullam a pharetra aliquam. Vivamus ac hendrerit mauris [texto de vínculo de ejemplo para el vínculo a un artículo en una carpeta de servicio](../articles/expressroute/expressroute-bandwidth-upgrade.md).

Obtener más de 10 veces tráfico de [Google]  [ gog] que de [Yahoo]  [ yah] o [MSN] [msn].

> [AZURE.NOTE] Texto con sangría.  Se agrega la palabra 'Nota' durante la publicación. Salir de la UE pretium lacus. Nullam purus est iaculis sed est ficar, euismod vehicula odio. Curabitur lacinia, erat tristique iaculis rutrum, erat sem sodales nisi, UE condimentum turpis nisi un purus.

1. Aenean sentarse amet leo nec **Purus** AC fermentum ac gravida odio.

2. Aenean tellus lectus, faucius en **Rhoncus** en faucibus sed urna. Suspendisse volutpat mi identificador purus ultrices iaculis nec no neque.

    ![Texto alternativo; No deje en blanco. Coche de selector en rojo de carreras.][5]

3. Dolor de dictum Nullam a pharetra aliquam. Vivamus ac hendrerit mauris. Sed dolor dui, condimentum y varius a vehicula en Suspendisse.

    ![Texto alternativo; No deje en blanco][6]


Suspendisse volutpat mi identificador purus ultrices iaculis nec no neque. Dolor de dictum Nullam a pharetra aliquam. Vivamus ac hendrerit mauris. Otrus informatus: [1 vínculo a otro tema de documentación azure.microsoft.com](virtual-machines-windows-hero-tutorial.md)

## <a name="heading-h2"></a>Título (H2)

Salir de la UE pretium lacus. Nullam purus est iaculis sed est ficar, euismod vehicula odio.

1. Curabitur lacinia, erat tristique iaculis rutrum, erat sem sodales nisi, UE condimentum turpis nisi un purus.

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

2. Vestibulum ante ipsum primis en faucibus orci luctus y ultrices posuere cubilia.

        // Because toast alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }


    > [AZURE.NOTE] Duis sed diámetro no <i>Suspendisse molestie</i> pharetra eget un est. [Vínculo 2 a otro tema de documentación azure.microsoft.com](web-sites-custom-domain-name.md)


Quisque commodo eros ficar lectus euismod auctor eget sentarse amet leo. Proin faucibus suscipit tellus dignissim ultrices.

## <a name="heading-2-h2"></a>Título 2 (H2)

1. Maecenas condimentum de sed nisi. Suspendisse potenti.

  + Fusce
  + Malesuada
  + SEM

2. Nullam en massa UE tellus tempus hendrerit.

    ![Texto alternativo; No deje en blanco. Ejemplo de un vídeo de canal 9.][7]

3. Quisque felis enim, fermentum Cortar aliquam nec, pellentesque pulvinar magna.




<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes

Vestibul ante ipsum primis en faucibus orci luctus y ultrices posuere cubilia Curae; Nullam ultricies, ipsum vitae volutpat hendrerit, eros de purus diámetro pretium, vitae tincidunt nulla lorem sed turpis: [3 de vínculo a otro tema de documentación de azure.microsoft.com](storage-whatis-account.md).

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/        
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/    
