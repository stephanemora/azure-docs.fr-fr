---
title: Déployer une mise à jour en utilisant Device Update pour Azure IoT Hub | Microsoft Docs
description: Déployez une mise à jour en utilisant Device Update pour Azure IoT Hub.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 0a11c8e8946229941c1fe60f7f2ce84d9fadb2ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101678359"
---
# <a name="deploy-an-update-using-device-update-for-iot-hub"></a>Déployer une mise à jour en utilisant Device Update pour IoT Hub

Découvrez comment déployer une mise à jour sur un appareil IoT en utilisant Device Updatepour IoT Hub.

## <a name="prerequisites"></a>Prérequis

* [Accès à un hub IoT avec Device Update pour IoT Hub activé](create-device-update-account.md). Il est recommandé d’utiliser un niveau S1 (Standard) ou supérieur pour votre hub IoT. 
* [Au moins une mise à jour a été correctement importée pour l’appareil provisionné.](import-update.md) 
* Un appareil IoT (ou un simulateur) configuré pour Device Update dans IoT Hub.
* [Une étiquette a été affectée à l’appareil IoT que vous essayez de mettre à jour. L’appareil fait partie d’au moins un groupe de mise à jour.](create-update-group.md)
* Navigateurs pris en charge :
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="deploy-an-update"></a>Déployer une mise à jour

1. Accédez au [portail Azure](https://portal.azure.com).

2. Accédez au panneau Device Update de votre hub IoT.

  :::image type="content" source="media/deploy-update/device-update-iot-hub.png" alt-text="IoT Hub" lightbox="media/deploy-update/device-update-iot-hub.png":::

3. Sélectionnez l’onglet Groupes en haut de la page. [En savoir plus](device-update-groups.md) sur les groupes d’appareils. 

  :::image type="content" source="media/deploy-update/updated-view.png" alt-text="Onglet Groupes" lightbox="media/deploy-update/updated-view.png":::

4. Affichez le graphique de conformité des mises à jour et la liste des groupes. Vous voyez normalement une nouvelle mise à jour disponible pour votre groupe d’appareils, avec un lien vers la mise à jour sous Mises à jour en attente (il peut être nécessaire d’actualiser une fois). [En savoir plus sur la conformité des mises à jour.](device-update-compliance.md) 

5. Sélectionnez la mise à jour disponible.

6. Vérifiez que le groupe approprié est sélectionné comme groupe cible. Planifiez votre déploiement, puis sélectionnez Déployer la mise à jour.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Sélectionner la mise à jour" lightbox="media/deploy-update/select-update.png":::

7. Visualisez le graphique de conformité. Vous devez voir que la mise à jour est maintenant en cours. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Mise à jour en cours" lightbox="media/deploy-update/update-in-progress.png":::

8. Une fois votre appareil correctement mis à jour, vous devez voir votre graphique de conformité et les détails du déploiement refléter la même chose. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Mise à jour réussie" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Superviser le déploiement d’une mise à jour

1. Sélectionnez l’onglet Déploiements en haut de la page.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Onglet Déploiements" lightbox="media/deploy-update/deployments-tab.png":::

2. Sélectionnez le déploiement que vous avez créé pour en examiner les détails.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Détails du déploiement" lightbox="media/deploy-update/deployment-details.png":::

3. Sélectionnez Actualiser pour voir les détails d’état les plus récents. Poursuivez ce processus jusqu’à ce que l’état passe à Réussi.


## <a name="retry-an-update-deployment"></a>Réessayer le déploiement d’une mise à jour

Si votre déploiement échoue pour une raison quelconque, vous pouvez réessayer le déploiement pour les appareils en échec. 

1. Accédez à l’onglet Déploiements, puis sélectionnez le déploiement qui a échoué. 

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Détails du déploiement" lightbox="media/deploy-update/deployment-details.png":::

2. Cliquez sur l’état d’appareil « En échec » dans le volet des informations détaillées du déploiement.

3. Cliquez sur « Réessayer les appareils en échec » et accusez réception de la notification de confirmation. 

## <a name="next-steps"></a>Étapes suivantes

[Résolution des problèmes courants](troubleshoot-device-update.md)
