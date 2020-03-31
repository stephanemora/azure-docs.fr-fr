---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 63c54f8af91b6b4a76ba49d5e6fc7b3cda9f5b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "66240316"
---
1. Ouvrez le projet avec **Android Studio**, en utilisant **Importer un projet (Eclipse ADT, Gradle, etc.)** . Assurez-vous d’éviter toute erreur JDK avec ce choix d’importation.

2. Ouvrez le fichier `ToDoActivity.java` dans le dossier ZUMOAPPNAME/app/src/main/java/com/example/zumoappname. Le nom de l’application est `ZUMOAPPNAME`.

3. Ouvrez le [Portail Microsoft Azure](https://portal.azure.com/), puis accédez à l’application mobile que vous avez créée. Sur le panneau `Overview`, recherchez l’URL qui correspond au point de terminaison public de votre application mobile. Exemple : la valeur sitename relative au nom de l’application « test123 » est https://test123.azurewebsites.net.

4. Dans la méthode `onCreate()`, remplacez le paramètre `ZUMOAPPURL` par le point de terminaison public ci-dessus.
    
    `new MobileServiceClient("ZUMOAPPURL", this).withFilter(new ProgressFilter());` 
    
    devient
    
    `new MobileServiceClient("https://test123.azurewebsites.net", this).withFilter(new ProgressFilter());`
    
5. Appuyez sur le bouton **Exécuter l’application** pour générer le projet et démarrer l’application dans le simulateur Android.

4. Dans l’application, tapez un texte explicite, comme *Suivre le didacticiel* , puis cliquez sur l’icône Ajouter. Cette action envoie une requête POST au serveur principal Azure déployé précédemment. Le backend insère les données de la requête dans la table SQL TodoItem et renvoie des informations sur les éléments récemment stockés à l’application mobile. L’application mobile affiche ces données dans la liste.
    ![Démarrage rapide d’Android](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)