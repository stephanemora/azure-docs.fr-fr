---
title: Surveillance à distance IoT et notifications avec Azure Logic Apps | Microsoft Docs
description: Utilisez Azure Logic Apps pour surveiller la température IoT sur votre hub IoT et envoyer automatiquement des notifications par courrier électronique dans votre boîte aux lettres en cas de détection d’anomalies.
author: robinsh
keywords: surveillance iot, notifications iot, surveillance de température iot
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: 5a277ac18bcbcb7e7acc6faf52f7bc72759c82a7
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678001"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Surveillance à distance IoT et notifications avec Azure Logic Apps connectant votre IoT Hub et votre boîte aux lettres

![Diagramme de bout en bout](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Azure Logic Apps permet d’automatiser des processus comme une série d’étapes. Une application logique peut établir une connexion via divers services et protocoles. Elle commence par un déclencheur tel que « Lorsqu’un compte est ajouté », suivie d’une combinaison d’actions, similaire à « envoi d’une notification Push ». Cette fonctionnalité rend Logic Apps idéale pour la surveillance IoT, telle que la vigilance face aux anomalies, parmi d’autres scénarios d’utilisation.

## <a name="what-you-learn"></a>Contenu

Vous apprenez à créer une application logique qui connecte votre IoT Hub et votre boîte aux lettres pour la surveillance de la température et les notifications. Lorsque la température est supérieure à 30 °C, l’application cliente indique `temperatureAlert = "true"` dans le message qu’elle envoie à votre IoT Hub. Suite à ce message, l’application logique vous envoie un e-mail de notification.

## <a name="what-you-do"></a>Procédure

* Créez un espace de noms Service Bus et ajoutez-lui une file d’attente.
* Ajoutez un point de terminaison et une règle de routage à votre IoT Hub.
* Créez, configurez et testez une application logique.

## <a name="what-you-need"></a>Ce dont vous avez besoin

* Terminer la [simulateur en ligne Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) didacticiel ou un des didacticiels appareil ; par exemple, [Raspberry Pi avec node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Ces couvrent les exigences suivantes :

  * Un abonnement Azure actif.
  * Une instance Azure IoT Hub associée à votre abonnement.
  * Une application cliente qui envoie des messages à votre instance Azure IoT Hub.

## <a name="create-service-bus-namespace-and-add-a-queue-to-it"></a>Créer un espace de noms Service Bus et lui ajouter une file d’attente

### <a name="create-a-service-bus-namespace"></a>Création d'un espace de noms Service Bus

1. Sur le [Azure portal](https://portal.azure.com/), sélectionnez **créer une ressource** > **Enterprise Integration** > **Service Bus**.

2. Fournissez les informations suivantes :

   **Nom** : Le nom de service bus.

   **Niveau tarifaire** : Sélectionnez **base** > **sélectionnez**. Le niveau de base est suffisant pour ce didacticiel.

   **Groupe de ressources** : utilisez le même groupe de ressources que celui de votre hub IoT.

   **Emplacement** : Utiliser le même emplacement que celui de votre hub IoT.

3. Sélectionnez **Créer**.

   ![Créer un espace de noms Service Bus dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1_create-service-bus-namespace-azure-portal.png)

### <a name="add-a-service-bus-queue"></a>Ajouter une file d’attente Service Bus

1. Ouvrez l’espace de noms service bus, puis sélectionnez **+ file d’attente**.

1. Entrez un nom pour la file d’attente, puis sélectionnez **créer**.

1. Ouvrir la file d’attente du bus de service, puis sélectionnez **stratégies d’accès partagé** > **+ ajouter**.

1. Entrez un nom pour la stratégie, cochez **gérer**, puis sélectionnez **créer**.

   ![Ajouter une file d’attente Service Bus dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2_add-service-bus-queue-azure-portal.png)

## <a name="add-an-endpoint-and-a-routing-query-to-your-iot-hub"></a>Ajouter un point de terminaison et une requête de routage à votre IoT hub

Maintenant, ajoutez un point de terminaison et une requête de routage à votre Iot hub.

### <a name="add-an-endpoint"></a>Ajout d’un point de terminaison

1. Ouvrez votre IoT hub, sélectionnez **points de terminaison** > **+ ajouter**.

1. Entrez les informations suivantes :

   **Nom** : Nom du point de terminaison.

   **Type de point de terminaison**: Sélectionnez **File d’attente Service Bus**.

   **Espace de noms Service Bus**: Sélectionnez l’espace de noms que vous avez créé.

   **File d'attente Service Bus** : Sélectionnez la file d’attente que vous avez créé.

3. Sélectionnez **OK**.

   ![Ajouter un point de terminaison à votre IoT Hub dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3_add-iot-hub-endpoint-azure-portal.png)

### <a name="add-a-routing-rule"></a>Ajouter une règle de routage

1. Dans votre IoT hub, sélectionnez **itinéraires** > **+ ajouter**.

2. Entrez les informations suivantes :

   **Nom** : Le nom de la règle de routage.

   **Source de données** : Sélectionnez **DeviceMessages**.

   **Point de terminaison** : Sélectionnez le point de terminaison que vous avez créé.

   **Chaîne de requête** : Entrez `temperatureAlert = "true"`.

3. Sélectionnez **Enregistrer**.

   ![Ajouter une règle de routage dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4_add-routing-rule-azure-portal.png)

## <a name="create-and-configure-a-logic-app"></a>Créer et configurer une application logique

Ensuite, vous créez et configurez une application logique.

### <a name="create-a-logic-app"></a>Créer une application logique

1. Dans le [Azure portal](https://portal.azure.com/), sélectionnez **créer une ressource** > **Enterprise Integration** > **application logique**.

2. Entrez les informations suivantes :

   **Nom** : Le nom de l’application logique.

   **Groupe de ressources** : utilisez le même groupe de ressources que celui de votre hub IoT.

   **Emplacement** : Utiliser le même emplacement que celui de votre hub IoT.

3. Sélectionnez **Créer**.

### <a name="configure-the-logic-app"></a>Configurer l’application logique

1. Ouvrez l’application logique qui s’ouvre dans le Concepteur d’applications logiques.

2. Dans le Concepteur d’applications logiques, sélectionnez **application logique vide**.

   ![Commencer avec une application logique vide dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5_start-with-blank-logic-app-azure-portal.png)

3. Sélectionnez **Service Bus**.

   ![Sélectionner Service Bus pour commencer à créer votre application logique dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6_select-service-bus-when-creating-blank-logic-app-azure-portal.png)

4. Sélectionnez **Service Bus – lorsqu’un ou plusieurs messages arrivent dans une file d’attente (saisie semi-auto)**.

5. Créez une connexion Service Bus.

   1. Entrez un nom de connexion.

   2. Sélectionnez l’espace de noms service bus > la stratégie service bus > **créer**.

      ![Créer une connexion Service Bus pour votre application logique dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7_create-service-bus-connection-in-logic-app-azure-portal.png)

   3. Sélectionnez **continuer** après la création de la connexion de service bus.

   4. Sélectionnez la file d’attente que vous avez créé, puis entrez `175` pour **nombre maximal de messages**.

      ![Spécifier le nombre maximal de messages pour les connexions Service Bus dans votre application logique](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8_specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   5. Sélectionnez bouton « Enregistrer » pour enregistrer les modifications.

6. Créez une connexion de service SMTP.

   1. Sélectionnez **Nouvelle étape** > **Ajouter une action**.

   2. Type `SMTP`, sélectionnez le **SMTP** de service dans le résultat de recherche, puis sélectionnez **SMTP - envoyer un courrier électronique**.

      ![Créer une connexion SMTP dans votre application logique dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9_create-smtp-connection-logic-app-azure-portal.png)

   3. Entrez les informations SMTP de votre boîte aux lettres, puis sélectionnez **créer**.

      ![Entrer les informations de connexion SMTP dans votre application logique dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10_enter-smtp-connection-info-logic-app-azure-portal.png)

      Obtenez les informations SMTP pour [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en) et [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).

   4. Entrez votre adresse de messagerie pour **De** et **À**, et `High temperature detected` pour **Objet** et **Corps**.

   5. Sélectionnez **Enregistrer**.

L’application logique est en état de marche lorsque vous l’enregistrez.

## <a name="test-the-logic-app"></a>Tester l’application logique

1. Démarrez l’application cliente que vous déployez sur votre appareil dans [Connecter la carte ESP8266 à Azure IoT Hub](iot-hub-arduino-huzzah-esp8266-get-started.md).

2. Faites en sorte que la température ambiante autour du SensorTag soit supérieure à 30 °C. Par exemple, allumez une bougie à côté de votre SensorTag.

3. Vous devriez recevoir un e-mail de notification envoyé par l’application logique.

   > [!NOTE]
   > Il se peut que votre fournisseur de services de messagerie doive vérifier l’identité de l’expéditeur pour s’assurer que vous êtes bien celui qui envoie l’e-mail.

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé avec succès une application logique qui connecte votre IoT Hub et votre boîte aux lettres pour la surveillance de la température et les notifications.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
