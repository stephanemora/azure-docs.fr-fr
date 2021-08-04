---
title: Résoudre les problèmes liés à Azure Percept Vision et aux modules de vision
description: Obtenir des conseils de dépannage pour certains problèmes courants rencontrés lors du prototypage IA de la vision.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/29/2021
ms.custom: template-how-to
ms.openlocfilehash: 80e25690e133b348ad5ee180bb5a3e01d4176c90
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113136240"
---
# <a name="vision-solution-troubleshooting"></a>Résolution des problèmes liés à la solution de vision

Cet article fournit des informations sur la résolution des problèmes liés aux solutions de vision sans code dans Azure Percept Studio.

## <a name="delete-a-vision-project"></a>Supprimer un projet de vision

1. Accédez à la page [Projets Custom Vision](https://www.customvision.ai/projects).

1. Pointez sur le projet que vous souhaitez supprimer, puis sélectionnez l’icône Corbeille pour supprimer le projet.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-project.png" alt-text="Capture d’écran qui montre la page Projets dans Custom Vision avec l’icône Supprimer mise en évidence.":::

## <a name="check-which-modules-are-on-a-device"></a>Vérifier les modules qui se trouvent sur un appareil

1. Accédez au [portail Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Sélectionnez l’icône **Iot Hub**.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub-2-inline.png" alt-text="Capture d’écran montrant la page d’accueil du portail Azure avec l’icône Iot Hub mise en évidence." lightbox= "./media/vision-solution-troubleshooting/vision-iot-hub-2.png":::

1. Sélectionnez le hub IoT auquel votre appareil cible est connecté.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub.png" alt-text="Capture d’écran montrant une liste de hubs IoT.":::

1. Sélectionnez **IoT Edge**, puis sélectionnez votre appareil sous l’onglet **ID d’appareil**.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-edge.png" alt-text="Capture d’écran montrant la page d’accueil IoT Edge.":::

1. Vos modules d’appareil s’affichent dans une liste sous l’onglet **Modules**.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="Capture d’écran montrant la page de IoT Edge pour l’appareil sélectionné affichant le contenu de l’onglet Modules." lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>Supprimer une unité

1. Accédez au [portail Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Sélectionnez l’icône **Iot Hub**.

1. Sélectionnez le hub IoT auquel votre appareil cible est connecté.

1. Sélectionnez **IoT Edge**, puis cochez la case en regard de l’ID de votre appareil cible. Sélectionnez **Supprimer** pour supprimer votre appareil.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="Capture d’écran montrant le bouton Supprimer mis en évidence dans la page d’accueil IoT Edge.":::

## <a name="check-the-runtime-status-of-azureeyemodule"></a>Vérifier le statut d’exécution d’azureeyemodule

En cas de problème avec **WebStreamModule**, vérifiez que **azureeyemodule**, qui s’occupe de l’inférence du modèle de vision, est en cours d’exécution. Pour vérifier l’état de l’exécution :

1. Accédez au [portail Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home), puis accédez à **Toutes les ressources** >  *\<your IoT hub>*  > **IoT Edge** >  *\<your device ID>* . 
1. Sélectionnez l’onglet **Modules** pour voir l’état d’exécution de tous les modules installés.

   :::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="Capture d’écran montrant l’écran d’état de l’exécution du module d’appareil." lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

1. Si l’état de l’exécution de **azureeyemodule** n’est pas **en cours d’exécution**, sélectionnez **Définir des modules** > **azureeyemodule**. 
1. Dans la page **Paramètres du module**, définissez **État souhaité** sur **En cours d’exécution**, puis sélectionnez **Mettre à jour**.

    :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="Capture montrant l’écran de configuration des paramètres du module.":::

## <a name="change-how-often-messages-are-sent-from-the-azureeyemodule"></a>Changer la fréquence d’envoi des messages depuis azureeyemodule

Votre niveau d’abonnement peut limiter le nombre de messages pouvant être envoyés de votre appareil vers IoT Hub. Par exemple, le niveau gratuit limite le nombre de messages à 8 000 par jour. Une fois cette limite atteinte, votre azureeyemodule cesse de fonctionner et vous risquez de recevoir cette erreur :

|Message d’erreur|
|------|
|*Le nombre total de messages sur le hub IoT « xxxxxxxxx » a dépassé le quota alloué. Nombre maximal de messages autorisés : « 8 000 », nombre de messages actuel : « xxxx ». Les opérations d’envoi et de réception sont bloquées pour ce hub jusqu’au jour suivant UTC. Envisagez d’augmenter les unités pour ce hub afin d’augmenter le quota.*|

Avec le jumeau de module azureeyemodule, il est possible de changer l’intervalle de fréquence d’envoi des messages. La valeur entrée pour l’intervalle indique la fréquence à laquelle chaque message est envoyé, en millisecondes. Plus le nombre est élevé, plus il y a de temps entre chaque message. Par exemple, si vous définissez l’intervalle sur 12 000, cela signifie qu’un message sera envoyé toutes les 12 secondes. Pour un modèle qui s’exécute toute la journée, cet intervalle revient à 7 200 messages par jour, ce qui est inférieur à la limite du niveau gratuit. La valeur que vous choisissez dépend de la réactivité dont vous avez besoin pour votre modèle de vision.

> [!NOTE]
> Le changement d’intervalle des messages n’affecte pas la taille de chaque message. La taille des messages dépend de plusieurs facteurs, tels que le type de modèle et le nombre d’objets détectés dans chaque message. Par conséquent, il est difficile de déterminer la taille des messages.

Pour mettre à jour l’intervalle des messages, suivez ces étapes :

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod#home) et ouvrez **Toutes les ressources**.

1. Dans la page **Toutes les ressources**, sélectionnez le nom du hub IoT qui a été provisionné dans votre kit de développement lors de l’installation.

1. Sur le côté gauche de la page **IoT Hub**, sous **Gestion automatique des appareils**, sélectionnez **IoT Edge**. Dans la page Appareils IoT Edge, recherchez l’ID d’appareil de votre kit de développement. Sélectionnez l’ID d’appareil de votre kit de développement pour ouvrir la page de son appareil IoT Edge.

1. Sous l’onglet **Modules**, sélectionnez **azureeyemodule**.

1. Dans la page **azureeyemodule**, ouvrez **Jumeau d’identité de module**.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-page-inline.png" alt-text="Capture d’écran d’une page de module." lightbox= "./media/vision-solution-troubleshooting/module-page.png":::

1. Faites défiler jusqu’à **Propriétés**.
1. Recherchez **TelemetryInterval** et remplacez-le par **TelemetryIntervalNeuralNetworkMs**

    :::image type="content" source="./media/vision-solution-troubleshooting/module-identity-twin-inline-02.png" alt-text="Capture d’écran des propriétés du jumeau d’identité de module." lightbox= "./media/vision-solution-troubleshooting/module-identity-twin.png":::

1. Mettez à jour la valeur **TelemetryIntervalNeuralNetworkMs** avec la valeur nécessaire.

1. Sélectionnez l’icône **Enregistrer**.

## <a name="view-device-rtsp-video-stream"></a>Afficher le flux vidéo RTSP de l’appareil

Affichez le flux vidéo RTSP de votre appareil dans [Azure Percept Studio](./how-to-view-video-stream.md) ou [VLC media player](https://www.videolan.org/vlc/index.html).

Pour ouvrir le flux RTSP dans VLC media player, accédez à **Media** > **Open network stream** > **rtsp://[device IP address]:8554/result**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la résolution des problèmes rencontrés avec votre instance d’Azure Percept DK, consultez le [guide général de résolution des problèmes](./troubleshoot-dev-kit.md).