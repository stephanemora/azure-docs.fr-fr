---
title: Device Update pour Azure Real Time Operating System | Microsoft Docs
description: Bien démarrer avec Device Update pour Azure Real Time Operating System
author: ValOlson
ms.author: valls
ms.date: 3/18/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: c912984879cac12a9ad9fe078056677dccb48cf7
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111970130"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-azure-real-time-operating-system-rtos"></a>Tutoriel sur Device Update pour Azure IoT Hub avec Azure Real Time Operating System (RTOS)

Ce tutoriel vous montre comment créer l’agent Device Update pour IoT Hub dans Azure RTOS NetX Duo. Il fournit également des API simples permettant aux développeurs d’intégrer les fonctionnalités de Device Update dans leur application. Explorez les [exemples](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU) de cartes d’évaluation des semiconducteurs clés qui incluent des guides de démarrage pour apprendre à configurer, générer et déployer des mises à jour OTA sur les appareils.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :
> [!div class="checklist"]
> * Bien démarrer
> * Étiqueter votre appareil
> * Créer un groupe d’appareils
> * Déployer une mise à jour basée sur une image
> * Superviser le déploiement de la mise à jour

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis
* Accès à un hub IoT. Il est recommandé d’utiliser un niveau S1 (Standard) ou supérieur.
* Une instance et un compte Device Update liés à votre hub IoT. Suivez le guide montrant comment [créer et lier](./create-device-update-account.md) un compte Device Update si vous ne l’avez pas déjà fait.

## <a name="get-started"></a>Bien démarrer

Chaque exemple de projet Azure RTOS spécifique à une carte contient du code et de la documentation sur l’utilisation de Device Update pour IoT Hub. 
1. Téléchargez les exemples de fichiers spécifiques à une carte à partir des [exemples Azure RTOS et Device Update](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU).
2. Recherchez le dossier docs à partir de l’exemple téléchargé.
3. À partir de la documentation, suivez les étapes pour préparer les ressources Azure, le compte et y inscrire les appareils IoT.
5. Suivez ensuite la documentation pour générer une nouvelle image de microprogramme et importer le manifeste pour votre carte.
6. Ensuite, publiez l’image du microprogramme et le manifeste sur Device Update pour IoT Hub.
7. Enfin, téléchargez et exécutez le projet sur votre appareil.

Apprenez-en davantage sur [Azure RTOS](/azure/rtos/).  

## <a name="tag-your-device"></a>Étiqueter votre appareil

1. Veillez à ce que l’application de l’appareil de l’étape précédente soit toujours en cours d’exécution.
2. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez au hub IoT.
3. Sous « Appareils IoT » dans le volet de navigation gauche, recherchez votre appareil IoT et accédez au jumeau d’appareil.
4. Dans le jumeau d’appareil, supprimez toute valeur d’étiquette Device Update existante en lui affectant la valeur null.
5. Ajoutez une nouvelle valeur d’étiquette Device Update comme indiqué ci-dessous.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="create-update-group"></a>Créer un groupe de mises à jour

1. Accédez au hub IoT que vous avez précédemment connecté à votre instance Device Update.
2. Sélectionnez l’option Mises à jour de l’appareil sous Gestion automatique des appareils dans la barre de navigation de gauche.
3. Sélectionnez l’onglet Groupes en haut de la page. 
4. Sélectionnez le bouton Ajouter pour créer un nouveau groupe.
5. Sélectionnez l’étiquette IoT Hub que vous avez créée à l’étape précédente à partir de la liste. Sélectionnez Créer un groupe de mises à jour.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Capture d’écran illustrant la sélection d’une étiquette" lightbox="media/create-update-group/select-tag.PNG":::

[En savoir plus](create-update-group.md) sur l’ajout d’étiquettes et la création de groupes de mises à jour

## <a name="deploy-new-firmware"></a>Déployer un nouveau microprogramme

1. Une fois le groupe créé, vous devriez voir une nouvelle mise à jour disponible pour votre groupe d’appareils, avec un lien vers cette mise à jour sous Mises à jour en attente. Vous devrez peut-être actualiser une fois. 
2. Cliquez sur la mise à jour disponible.
3. Vérifiez que le groupe approprié est sélectionné en tant que groupe cible. Planifiez votre déploiement, puis sélectionnez Déployer la mise à jour.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Sélectionner la mise à jour" lightbox="media/deploy-update/select-update.png":::

4. Visualisez le graphique de conformité. Vous devez voir que la mise à jour est maintenant en cours. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Mise à jour en cours" lightbox="media/deploy-update/update-in-progress.png":::

5. Une fois votre appareil correctement mis à jour, vous devez voir votre graphique de conformité et les détails du déploiement refléter la même chose. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Mise à jour réussie" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Superviser le déploiement d’une mise à jour

1. Sélectionnez l’onglet Déploiements en haut de la page.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Onglet Déploiements" lightbox="media/deploy-update/deployments-tab.png":::

2. Sélectionnez le déploiement que vous avez créé pour en examiner les détails.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Détails du déploiement" lightbox="media/deploy-update/deployment-details.png":::

3. Sélectionnez Actualiser pour voir les détails d’état les plus récents. Poursuivez ce processus jusqu’à ce que l’état passe à Réussi.

Vous avez maintenant réussi une mise à jour d’image de bout en bout à l’aide du service Device Update pour IoT Hub sur un appareil Raspberry Pi 3 B+. 

## <a name="cleanup-resources"></a>Nettoyer les ressources

Quand vous n’en avez plus besoin, nettoyez vos compte, instance, hub IoT et appareil IoT Device Update. 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Azure RTOS et sur son fonctionnement avec Azure IoT, consultez https://azure.com/rtos.