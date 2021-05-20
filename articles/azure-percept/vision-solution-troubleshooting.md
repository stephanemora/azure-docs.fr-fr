---
title: Résoudre les problèmes liés à Azure Percept Vision et aux modules de vision
description: Obtenir des conseils de dépannage pour certains des problèmes les plus courants rencontrés lors du prototypage IA de la vision
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/29/2021
ms.custom: template-how-to
ms.openlocfilehash: 7befc9648e696e80e0469d5e91173786354574d8
ms.sourcegitcommit: b35c7f3e7f0e30d337db382abb7c11a69723997e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109685744"
---
# <a name="vision-solution-troubleshooting"></a>Résolution des problèmes liés à la solution de vision

Pour plus d’informations sur la résolution des problèmes liés aux solutions de vision sans code dans Azure Percept Studio, consultez les conseils suivants.

## <a name="delete-a-vision-project"></a>Supprimer un projet de vision

1. Atteindre https://www.customvision.ai/projects.

1. Pointez sur le projet que vous souhaitez supprimer, puis cliquez sur l’icône Corbeille pour supprimer le projet.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-project.png" alt-text="Page Projets dans Custom Vision avec l’icône Supprimer mise en évidence.":::

## <a name="check-which-modules-are-on-a-device"></a>Vérifier les modules qui se trouvent sur un appareil

1. Accédez au [portail Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Cliquez sur l’icône **Iot Hub**.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub-2-inline.png" alt-text="Page d’accueil du portail Azure avec l’icône IoT Hub mise en évidence." lightbox= "./media/vision-solution-troubleshooting/vision-iot-hub-2.png":::

1. Sélectionnez le hub IoT auquel votre appareil cible est connecté.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-hub.png" alt-text="Liste des hubs IoT.":::

1. Sélectionnez **IoT Edge**, puis cliquez sur votre appareil sous l’onglet **ID d’appareil**.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-iot-edge.png" alt-text="Page d’accueil IoT Edge.":::

1. Les modules d’appareil sont listés sous l’onglet **Modules**.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-device-modules-inline.png" alt-text="Page IoT Edge de l’appareil sélectionné montrant le contenu de l’onglet Modules." lightbox= "./media/vision-solution-troubleshooting/vision-device-modules.png":::

## <a name="delete-a-device"></a>Supprimer une unité

1. Accédez au [portail Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home).

1. Cliquez sur l’icône **Iot Hub**.

1. Sélectionnez le hub IoT auquel votre appareil cible est connecté.

1. Sélectionnez **IoT Edge**, puis cochez la case en regard de votre ID d’appareil cible. Cliquez sur l’icône Corbeille pour supprimer votre appareil.

    :::image type="content" source="./media/vision-solution-troubleshooting/vision-delete-device.png" alt-text="Icône de suppression mise en évidence dans la page d’accueil IoT Edge.":::

## <a name="eye-module-troubleshooting-tips"></a>Conseils de dépannage pour les modules oculaires

### <a name="check-the-runtime-status-of-azureeyemodule"></a>Vérifier le statut d’exécution d’azureeyemodule

En cas de problème avec **WebStreamModule**, vérifiez que **azureeyemodule**, qui s’occupe de l’inférence du modèle de vision, est en cours d’exécution. Pour vérifier l’état de l’exécution, accédez au [portail Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home), puis accédez à **Toutes les ressources** ->  **\<your IoT hub>**  -> **IoT Edge** ->  **\<your device ID>** . Cliquez sur l’onglet **Modules** pour voir l’état d’exécution de tous les modules installés.

:::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="Écran de l’état d’exécution du module d’appareil." lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

Si l’état d’exécution de **azureeyemodule** n’est pas **en cours d’exécution**, cliquez sur **Définir des modules** -> **azureeyemodule**. Dans la page **Paramètres du module**, définissez **État souhaité** sur **En cours d’exécution**, puis cliquez sur **Mettre à jour**.

 :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="Écran de configuration des paramètres du module.":::

### <a name="update-telemetryintervalneuralnetworkms"></a>Mettre à jour TelemetryIntervalNeuralNetworkMs

Si vous rencontrez l’erreur de limitation de compte suivante, la valeur TelemetryIntervalNeuralNetworkMs dans les paramètres de jumeau du module azureeyemodule doivent être mis à jour.

|Message d’erreur|
|------|
|Le nombre total de messages sur l’IotHub « xxxxxxxxx » a dépassé le quota alloué. Nombre maximal de messages autorisés : « 8000 », nombre de messages actuel : « xxxx ». Les opérations d’envoi et de réception sont bloquées pour ce hub jusqu’au prochain jour UTC. Envisagez d’augmenter les unités pour que ce hub augmente le quota.|

TelemetryIntervalNeuralNetworkMs détermine la fréquence d’envoi des messages (en millisecondes) à partir du réseau neuronal. Les abonnements Azure ont un nombre limité de messages par jour, en fonction de votre niveau d’abonnement. Si vous êtes bloqué en raison de l’envoi d’un trop grand nombre de messages, augmentez la limite à un nombre supérieur. 12000 (une fois toutes les 12 secondes) vous donnera 7200 messages par jour, ce qui se trouve sous la limite de 8000 messages pour l’abonnement gratuit.

Pour mettre à jour votre valeur TelemetryIntervalNeuralNetworkMs, procédez comme suit :

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod#home) et ouvrez **Toutes les ressources**.

1. Dans la page **Toutes les ressources**, cliquez sur le nom de l’IoT Hub qui a été configuré dans votre devkit lors de l’installation.

1. Sur le côté gauche de la page IoT Hub, cliquez sur **IoT Edge** sous **Gestion automatique des appareils**. Sur la page Appareils IoT Edge, recherchez l’ID d’appareil de votre devkit. Cliquez sur l’ID d’appareil de votre devkit pour ouvrir sa page d’appareil IoT Edge.

1. Sélectionnez **azureeyemodule** sous l’onglet **Modules**.

1. Sur la page azureeyemodule, ouvrez **Jumeau d’identité de module**.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-page-inline.png" alt-text="Capture d’écran de la page du module." lightbox= "./media/vision-solution-troubleshooting/module-page.png":::

1. Faites défiler jusqu’à **Propriétés**. Les propriétés « Running » et « Logging » ne sont pas actives pour l’instant.

    :::image type="content" source="./media/vision-solution-troubleshooting/module-identity-twin-inline.png" alt-text="Capture d’écran des propriétés de jumeau de module." lightbox= "./media/vision-solution-troubleshooting/module-identity-twin.png":::

1. Mettez à jour la valeur **TelemetryIntervalNeuralNetworkMs** comme vous le souhaitez, puis cliquez sur l’icône **Enregistrer**.

## <a name="view-device-rtsp-video-stream"></a>Afficher le flux vidéo RTSP de l’appareil

Affichez le flux vidéo RTSP de votre appareil dans [Azure Percept Studio](./how-to-view-video-stream.md) ou [VLC media player](https://www.videolan.org/vlc/index.html).

Pour ouvrir le flux RTSP dans VLC media player, accédez à **Media** -> **Open network stream** -> **rtsp://[device IP address]:8554/result**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la résolution des problèmes Azure Percept DK, consultez le [Guide de résolution des problèmes généraux](./troubleshoot-dev-kit.md) .