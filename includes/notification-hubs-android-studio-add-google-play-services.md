---
title: Fichier Include
description: Fichier Include
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 02/05/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bc920493b32d500602a5b683c098d23aff855150
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823193"
---
1. Dans **Android Studio**, sélectionnez **Outils** dans le menu, puis **SDK Manager**. 
2. Sélectionnez la version cible d’Android SDK utilisée dans votre projet, puis **Afficher les détails du package**. 

    ![Android SDK Manager - sélectionner la version cible](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Sélectionnez **API Google**, si elles ne sont pas déjà installées.

    ![Android SDK Manager - API Google sélectionnées](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Basculez vers l’onglet **SDK Tools**. Si vous n’avez pas déjà installé un service Google Play, sélectionnez **Google Play Services** comme indiqué dans l’image suivante. Cliquez ensuite sur **Appliquer** pour procéder à l’installation. Notez le chemin du Kit de développement logiciel (SDK). Vous l’utiliserez à une étape suivante.

    ![Android SDK Manager - Google Play Services sélectionné](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Si la boîte de dialogue de **confirmation de la modification** s’affiche, sélectionnez **OK**. Le programme d’installation installe les composants demandés. Sélectionnez **Terminer** une fois que les composants sont installés.
4. Sélectionnez **OK** pour fermer la boîte de dialogue **Paramètres pour les nouveaux projets**.  
5. Ouvrez le fichier `build.gradle` dans le répertoire **app** et ajoutez cette ligne sous `dependencies`. 

    ```text
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. Sélectionnez l’icône **Synchroniser maintenant** dans la barre d’outils.

    ![Synchroniser avec Gradle](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. Ouvrez **AndroidManifest.xml** et ajoutez cette balise à la balise *application* .

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
