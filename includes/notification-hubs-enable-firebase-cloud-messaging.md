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
ms.openlocfilehash: 5f919a04b47aa6fdef9500f3d7e6bef4ddaa239e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177315"
---
1. Connectez-vous à la [console Firebase](https://firebase.google.com/console/). Créer un nouveau projet Firebase si vous n’en avez pas encore.
2. Une fois le projet créé, sélectionnez **Add Firebase to your Android app** (Ajouter Firebase à votre application Android). 

    ![Ajouter Firebase à votre application Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Dans la page **Add Firebase to your Android app** (Ajouter Firebase à votre application Android), procédez comme suit : 
    1. Pour le **Nom de package Android**, copiez la valeur de votre **applicationId** dans le fichier **build.gradle** de votre application. Dans cet exemple, il s’agit de `com.fabrikam.fcmtutorial1app`. 

        ![Spécifiez le nom du package](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Sélectionnez **Inscrire une application**. 
4. Sélectionnez **Télécharger google-services.json**, enregistrez le fichier dans le dossier **app** de votre projet, puis sélectionnez **Suivant**. 

    ![Téléchargez le fichier google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Modifiez la **configuration comme suit** à votre projet dans Android Studio. 
    1.  Dans votre fichier **build.gradle au niveau du projet** (&lt;project&gt;/build.gradle), ajoutez l’instruction suivante dans la section **dependencies**. 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. Dans votre fichier **build.gradle au niveau de l’application** (&lt;project&gt;/&lt;app-module&gt;/build.gradle), ajoutez l’instruction suivante dans la section **dependencies**. 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.1'
        ```

    3. Ajoutez la ligne suivante à la fin du fichier **build.gradle au niveau de l’application** après la section dependencies. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Sélectionnez **Synchroniser maintenant*** dans la barre d’outils. 
 
        ![Modifications de configuration de build.gradle](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Sélectionnez **Suivant** sur la page. 
7. Sélectionnez **Ignorer cette étape** sur la page. 

    ![Ignorer la dernière étape](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)1. 
8. Dans la console Firebase, sélectionnez la roue dentée associée à votre projet. Ensuite, sélectionnez **Project Settings** (Paramètres du projet).

    ![Sélectionnez Project Settings (Paramètres du projet).](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Si vous n’avez pas encore téléchargé le fichier **google-services.json** dans le dossier **application** de votre projet Android Studio, vous pouvez le faire à partir de cette page. 
5. Basculez vers l’onglet **Messagerie cloud** en haut. 
6. Copiez et enregistrez la **clé du serveur** pour une utilisation ultérieure. Cette valeur est utilisée pour configurer votre hub de notification.
