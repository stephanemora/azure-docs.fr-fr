---
title: Fichier Include
description: Fichier Include
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/05/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 4dc67175f2ccc569ab6dfe5338607fa6bebe3882
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
1. Connectez-vous à la [console Firebase](https://firebase.google.com/console/). Créer un nouveau projet Firebase si vous n’en avez pas encore.
2. Une fois le projet créé, sélectionnez **Add Firebase to your Android app** (Ajouter Firebase à votre application Android). Ensuite, suivez les instructions fournies. Téléchargez le fichier **google-services.json**. 

    ![Ajouter Firebase à votre application Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Dans la console Firebase, sélectionnez la roue dentée associée à votre projet. Ensuite, sélectionnez **Project Settings** (Paramètres du projet).

    ![Sélectionnez Project Settings (Paramètres du projet).](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Sélectionnez l’onglet **General** (Général) dans les paramètres du projet. Ensuite, téléchargez le fichier **google-services.json** contenant la clé d’API du serveur et l’ID client.
5. Cliquez sur l’onglet **Cloud Messaging** (Messagerie cloud) dans les paramètres de votre projet, puis copiez la valeur de l’instance **Legacy server key** (Clé serveur héritée). Cette valeur est utilisée pour configurer la stratégie d’accès du hub de notification.
