---
title: Surveillance à distance IoT et notifications avec Azure Logic Apps | Microsoft Docs
description: Utilisez Azure Logic Apps pour surveiller la température IoT sur votre hub IoT et envoyer automatiquement des notifications par courrier électronique dans votre boîte aux lettres en cas de détection d’anomalies.
author: robinsh
keywords: surveillance iot, notifications iot, surveillance de température iot
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/19/2019
ms.author: robinsh
ms.openlocfilehash: 26637468f44e12f7ad66f907e0f6be3d907e578f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126084"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Surveillance à distance IoT et notifications avec Azure Logic Apps connectant votre IoT Hub et votre boîte aux lettres

![Diagramme de bout en bout](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) peut vous aider à orchestrer des flux de travail locales et cloud services, une ou plusieurs entreprises et entre les différents protocoles. Une application logique commence par un déclencheur, qui est ensuite suivi par une ou plusieurs actions qui peuvent être exécutées à l’aide de contrôles intégrés, tels que les conditions et les itérateurs. Cette souplesse rend Logic Apps IoT solution idéale pour les scénarios d’analyse IoT. Par exemple, l’arrivée des données de télémétrie à partir d’un appareil à un point de terminaison IoT Hub peut initier des workflows d’application logique pour l’entrepôt de données dans un objet blob Azure Storage, envoyer des alertes par courrier électronique à avertir d’anomalies de données, de planifier une visite de technicien si un appareil signale un échec , et ainsi de suite.

## <a name="what-you-learn"></a>Contenu

Vous apprenez à créer une application logique qui connecte votre IoT Hub et votre boîte aux lettres pour la surveillance de la température et les notifications.

Le code client en cours d’exécution sur votre appareil définit une propriété d’application, `temperatureAlert`, dans chaque télémétrie message qu’il envoie à votre IoT hub. Lorsque le code client détecte une température supérieure à 30 C, il définit cette propriété sur `true`; sinon, il définit la propriété `false`.

Dans cette rubrique, vous configurer le routage sur votre IoT hub pour envoyer des messages dans lequel `temperatureAlert = true` pour un Bus de Service point de terminaison et vous configurez une application logique qui se déclenche sur les messages arrivant sur le point de terminaison de Service Bus et vous envoie une notification par e-mail.

## <a name="what-you-do"></a>Procédure

* Créer un espace de noms Service Bus et lui ajouter une file d’attente Service Bus.
* Ajouter un point de terminaison personnalisé et une règle de routage à votre IoT hub pour router les messages qui contiennent une alerte de température à la file d’attente Service Bus.
* Créer, configurer et tester une application logique pour consommer les messages de votre file d’attente Service Bus et envoyez des e-mails de notification à un destinataire souhaité.

## <a name="what-you-need"></a>Ce dont vous avez besoin

* Terminer la [simulateur en ligne Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) didacticiel ou un des didacticiels appareil ; par exemple, [Raspberry Pi avec node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Ces couvrent les exigences suivantes :

  * Un abonnement Azure actif.
  * Une instance Azure IoT Hub associée à votre abonnement.
  * Une application cliente en cours d’exécution sur un appareil qui envoie des messages de télémétrie à votre Azure IoT hub.

## <a name="create-service-bus-namespace-and-queue"></a>Créer une file d’attente et d’espace de noms Service Bus

Créez un espace de noms et une file d’attente Service Bus. Plus loin dans cette rubrique, vous créez une règle de routage dans votre IoT hub pour diriger les messages qui contiennent une alerte de température à la file d’attente Service Bus, où ils ne seront récupérés par une application logique et déclenchent pour envoyer un e-mail de notification.

### <a name="create-a-service-bus-namespace"></a>Création d’un espace de noms Service Bus

1. Sur le [Azure portal](https://portal.azure.com/), sélectionnez **+ créer une ressource** > **intégration** > **Service Bus**.

1. Sur le **créer l’espace de noms** volet, fournissez les informations suivantes :

   **Nom** : Le nom de l’espace de noms service bus. L’espace de noms doit être unique dans Azure.

   **Niveau tarifaire** : Sélectionnez **base** dans la liste déroulante. Le niveau de base est suffisant pour ce didacticiel.

   **Groupe de ressources** : utilisez le même groupe de ressources que celui de votre hub IoT.

   **Emplacement** : Utiliser le même emplacement que celui de votre hub IoT.

   ![Créer un espace de noms Service Bus dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Sélectionnez **Créer**. Attendez que le déploiement se termine avant de passer à l’étape suivante.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Ajouter une file d’attente Service Bus à l’espace de noms

1. Ouvrez l’espace de noms Service Bus. Le moyen le plus simple pour accéder à l’espace de noms Service Bus consiste à sélectionner **groupes de ressources** à partir du volet de la ressource, sélectionnez votre groupe de ressources, puis sélectionnez l’espace de noms Service Bus à partir de la liste des ressources.

1. Sur le **Service Bus Namespace** volet, sélectionnez **+ file d’attente**.

1. Entrez un nom pour la file d’attente, puis sélectionnez **créer**. Lorsque la file d’attente a été créé avec succès, le **Create queue** volet se ferme.

   ![Ajouter une file d’attente Service Bus dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. Sur le **Service Bus Namespace** volet, sous **entités**, sélectionnez **files d’attente**. Ouvrir la file d’attente Service Bus dans la liste, puis sélectionnez **stratégies d’accès partagé** > **+ ajouter**.

1. Entrez un nom pour la stratégie, cochez **gérer**, puis sélectionnez **créer**.

   ![Ajouter une stratégie de file d’attente de bus de service dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Ajouter un point de terminaison personnalisé et une règle de routage à votre IoT hub

Ajouter un point de terminaison personnalisé pour la file d’attente Service Bus à votre IoT hub et créez une règle de routage de message pour diriger les messages qui contiennent une alerte de température à ce point de terminaison, où il seront récupérés par votre application logique. La règle de routage utilise une requête de routage, `temperatureAlert = "true"`pour transférer des messages en fonction de la valeur de la `temperatureAlert` propriété application définie par le code client en cours d’exécution sur l’appareil. Pour plus d’informations, consultez [selon les propriétés de message de requête routage de messages](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties).

### <a name="add-a-custom-endpoint"></a>Ajouter un point de terminaison personnalisé

1. Ouvrez votre IoT Hub. Le moyen le plus simple pour accéder à l’IoT hub consiste à sélectionner **groupes de ressources** à partir du volet de la ressource, sélectionnez votre groupe de ressources, puis sélectionnez votre IoT hub à partir de la liste des ressources.

1. Sous **Messaging**, sélectionnez **le routage des messages**. Sur le **le routage des messages** volet, sélectionnez le **les points de terminaison personnalisé** onglet, puis sélectionnez **+ ajouter**. Dans la liste déroulante, sélectionnez **file d’attente de bus de Service**.

   ![Ajouter un point de terminaison à votre IoT Hub dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. Sur le **ajouter un point de terminaison de service bus** volet, entrez les informations suivantes :

   **Nom du point de terminaison**: Nom du point de terminaison.

   **Espace de noms Service Bus** : Sélectionnez l’espace de noms que vous avez créé.

   **File d’attente de bus de service**: Sélectionnez la file d’attente que vous avez créé.

   ![Ajouter un point de terminaison à votre IoT Hub dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Sélectionnez **Créer**. Une fois que le point de terminaison est créé avec succès, passez à l’étape suivante.

### <a name="add-a-routing-rule"></a>Ajouter une règle de routage

1. Sur le **le routage des messages** volet, sélectionnez le **itinéraires** onglet, puis sélectionnez **+ ajouter**.

1. Sur le **ajouter un itinéraire** volet, entrez les informations suivantes :

   **Nom** : Le nom de la règle de routage.

   **Point de terminaison** : Sélectionnez le point de terminaison que vous avez créé.

   **Source de données** : Sélectionnez **Messages de télémétrie appareil**.

   **Requête de routage** : Entrez `temperatureAlert = "true"`.

   ![Ajouter une règle de routage dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Sélectionnez **Enregistrer**. Vous pouvez fermer le **le routage des messages** volet.

## <a name="create-and-configure-a-logic-app"></a>Créer et configurer une application logique

Dans la section précédente, vous configurez votre IoT hub pour router les messages contenant une alerte de température à votre file d’attente Service Bus. Maintenant, vous configurez une application logique pour surveiller la file d’attente Service Bus et envoyer une notification par courrier électronique chaque fois qu’un message est ajouté à la file d’attente.

### <a name="create-a-logic-app"></a>Créer une application logique

1. Sélectionnez **créer une ressource** > **intégration** > **application logique**.

1. Entrez les informations suivantes :

   **Nom** : Le nom de l’application logique.

   **Groupe de ressources** : utilisez le même groupe de ressources que celui de votre hub IoT.

   **Emplacement** : Utiliser le même emplacement que celui de votre hub IoT.

   ![Créer une application logique dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Sélectionnez **Créer**.

### <a name="configure-the-logic-app-trigger"></a>Configurer le déclencheur d’application logique

1. Ouvrez l’application logique. Le moyen le plus simple pour accéder à l’application logique consiste à sélectionner **groupes de ressources** à partir du volet de la ressource, sélectionnez votre groupe de ressources, puis votre application logique à partir de la liste des ressources. Lorsque vous sélectionnez l’application logique, le concepteur Logic Apps s’ouvre.

1. Dans le Concepteur d’applications logiques, faites défiler jusqu'à **modèles** et sélectionnez **application logique vide**.

   ![Commencer avec une application logique vide dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Sélectionnez le **tous les** onglet, puis sélectionnez **Service Bus**.

   ![Sélectionner Service Bus pour commencer à créer votre application logique dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Sous **déclencheurs**, sélectionnez **lorsqu’un ou plusieurs messages arrivent dans une file d’attente (saisie semi-auto)**.

   ![Sélectionnez le déclencheur pour votre application logique dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Créez une connexion Service Bus.
   1. Entrez un nom de connexion et sélectionnez votre espace de noms Service Bus dans la liste. L’écran suivant s’ouvre.

      ![Créer une connexion Service Bus pour votre application logique dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Sélectionnez la stratégie service bus (RootManageSharedAccessKey). Puis sélectionnez **créer**.

      ![Créer une connexion Service Bus pour votre application logique dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Dans le dernier écran, pour **nom de la file d’attente**, sélectionnez la file d’attente que vous avez créé à partir de la liste déroulante. Entrez `175` pour **nombre maximal de messages**.

      ![Spécifier le nombre maximal de messages pour les connexions Service Bus dans votre application logique](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Sélectionnez **enregistrer** dans le menu en haut du Concepteur d’applications logique pour enregistrer vos modifications.

### <a name="configure-the-logic-app-action"></a>Configurer l’action d’application logique

1. Créez une connexion de service SMTP.

   1. Sélectionnez **Nouvelle étape**. Dans **choisir une action**, sélectionnez le **tous les** onglet.

   1. Type `smtp` dans la zone de recherche, sélectionnez le **SMTP** de service dans le résultat de recherche, puis sélectionnez **envoyer un message électronique**.

      ![Créer une connexion SMTP dans votre application logique dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Entrez les informations SMTP de votre boîte aux lettres, puis sélectionnez **créer**.

      ![Entrer les informations de connexion SMTP dans votre application logique dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Obtenez les informations SMTP pour [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en) et [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html).

      > [!NOTE]
      > Vous devrez peut-être désactiver le protocole SSL pour établir la connexion. Si c’est le cas et que vous souhaitez réactiver SSL après avoir établi la connexion, consultez l’étape facultative à la fin de cette section.

   1. À partir de la **ajouter un nouveau paramètre** déroulante sur le **envoyer un message électronique** étape, sélectionnez **à partir de**, **à**, **sujet**et **corps**. Cliquez ou appuyez sur n’importe où sur l’écran pour fermer la zone de sélection.

      ![Choisissez les champs de connexion de messagerie SMTP](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Entrez votre adresse de messagerie pour **De** et **À**, et `High temperature detected` pour **Objet** et **Corps**. Si le **ajouter du contenu dynamique à partir d’applications et connecteurs utilisés dans ce flux** boîte de dialogue s’ouvre, sélectionnez **masquer** pour la fermer. Vous n’utilisez pas de contenu dynamique dans ce didacticiel.

      ![Champs de l’e-mail SMTP connexion manuelle](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Sélectionnez **enregistrer** pour enregistrer la connexion SMTP.

1. (Facultatif) Si vous deviez désactiver le protocole SSL pour établir une connexion avec votre fournisseur de messagerie et souhaitez réactiver, procédez comme suit :

   1. Sur le **application logique** volet, sous **outils de développement**, sélectionnez **connexions d’API**.

   1. Dans la liste des connexions d’API, sélectionnez la connexion SMTP.

   1. Sur le **smtp connexion d’API** volet, sous **général**, sélectionnez **connexion d’API modifier**.

   1. Sur le **modifier la connexion API** volet, sélectionnez **activer SSL ?**, entrer à nouveau le mot de passe pour votre compte de messagerie, puis sélectionnez **enregistrer**.

      ![Modifier la connexion d’API SMTP dans votre application logique dans le portail Azure](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

Votre application logique est maintenant prête à traiter les alertes de température à partir de la file d’attente Service Bus et envoyer des notifications à votre compte de messagerie.

## <a name="test-the-logic-app"></a>Tester l’application logique

1. Démarrer l’application cliente sur votre appareil.

1. Si vous utilisez un appareil physique, soigneusement mettre une source de chaleur près le détecteur de chaleur jusqu'à ce que la température dépasse 30 degrés C. Si vous utilisez le simulateur en ligne, le code client au hasard générera des messages de télémétrie qui dépassent 30 c

1. Vous devez commencer à recevoir des notifications par courrier électronique envoyées par l’application logique.

   > [!NOTE]
   > Il se peut que votre fournisseur de services de messagerie doive vérifier l’identité de l’expéditeur pour s’assurer que vous êtes bien celui qui envoie l’e-mail.

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé avec succès une application logique qui connecte votre IoT Hub et votre boîte aux lettres pour la surveillance de la température et les notifications.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
