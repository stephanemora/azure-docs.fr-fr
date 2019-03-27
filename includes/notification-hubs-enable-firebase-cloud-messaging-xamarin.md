---
title: Fichier Include
description: Fichier Include
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 90ffebf5f3375e94545f82a95f5c240ad845bd94
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2019
ms.locfileid: "57908261"
---
1. Connectez-vous à la [console Firebase](https://firebase.google.com/console/). Créer un nouveau projet Firebase si vous n’en avez pas encore.
2. Une fois le projet créé, sélectionnez **Add Firebase to your Android app** (Ajouter Firebase à votre application Android). 

    ![Ajouter Firebase à votre application Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Dans la page **Add Firebase to your Android app** (Ajouter Firebase à votre application Android), procédez comme suit : 
    1. Pour **Android package name** (Nom du package Android), entrez un nom pour votre package. Par exemple : `tutorials.tutoria1.xamarinfcmapp`. 

        ![Spécifiez le nom du package](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Sélectionnez **Inscrire une application**. 
4. Sélectionnez **Télécharger google-services.json**, enregistrez le fichier dans le dossier **app** de votre projet, puis sélectionnez **Suivant**. 

    ![Téléchargez le fichier google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
6. Sélectionnez **Suivant** sur la page. 
7. Sélectionnez **Ignorer cette étape** sur la page. 

    ![Ignorer la dernière étape](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Dans la console Firebase, sélectionnez la roue dentée associée à votre projet. Ensuite, sélectionnez **Project Settings** (Paramètres du projet).

    ![Sélectionnez Project Settings (Paramètres du projet).](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Si vous n’avez pas téléchargé le fichier **google-services.JSON**, vous pouvez le faire dans cette page. 
5. Basculez vers l’onglet **Messagerie cloud** en haut. 
6. Copiez et enregistrez la **clé du serveur** pour une utilisation ultérieure. Cette valeur est utilisée pour configurer votre hub de notification.
