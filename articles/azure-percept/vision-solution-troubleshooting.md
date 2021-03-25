---
title: Résoudre les problèmes liés à Azure Percept Vision et aux modules de vision
description: Obtenir des conseils de dépannage pour certains des problèmes les plus courants rencontrés lors du prototypage IA de la vision
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: e7351079e7325aa7750dc0d10f0923bc847ccc3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101678311"
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

En cas de problème avec **WebStreamModule**, vérifiez que **azureeyemodule** (qui s’occupe de l’inférence du modèle de vision) est en cours d’exécution. Pour vérifier l’état de l’exécution, accédez au [portail Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home), puis accédez à **Toutes les ressources** ->  **\<your IoT hub>**  -> **IoT Edge** ->  **\<your device ID>** . Cliquez sur l’onglet **Modules** pour voir l’état d’exécution de tous les modules installés.

:::image type="content" source="./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page-inline.png" alt-text="Écran de l’état d’exécution du module d’appareil." lightbox= "./media/vision-solution-troubleshooting/over-the-air-iot-edge-device-page.png":::

Si l’état d’exécution de **azureeyemodule** n’est pas **en cours d’exécution**, cliquez sur **Définir des modules** -> **azureeyemodule**. Dans la page **Paramètres du module**, définissez **État souhaité** sur **En cours d’exécution**, puis cliquez sur **Mettre à jour**.

 :::image type="content" source="./media/vision-solution-troubleshooting/firmware-desired-status-stopped.png" alt-text="Écran de configuration des paramètres du module.":::

## <a name="view-device-rtsp-video-stream"></a>Afficher le flux vidéo RTSP de l’appareil

Affichez le flux vidéo RTSP de votre appareil dans [Azure Percept Studio](./how-to-view-video-stream.md) ou [VLC media player](https://www.videolan.org/vlc/index.html).

Pour ouvrir le flux RTSP dans VLC media player, accédez à **Media** -> **Open network stream (Ouvrir le flux réseau)**  -> **rtsp://[adresse IP de l’appareil]/result**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la résolution des problèmes Azure Percept DK, consultez le [Guide de résolution des problèmes généraux](./troubleshoot-dev-kit.md) .