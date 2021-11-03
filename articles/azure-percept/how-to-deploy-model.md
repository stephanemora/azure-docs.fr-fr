---
title: Déployer un modèle IA de vision sur votre appareil Azure Percept DK
description: Découvrir comment déployer un modèle IA de vision sur votre DK Azure Percept à partir d’Azure Percept Studio
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 55948f2beede0597a30dd557a82ee297c6a540ea
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006370"
---
# <a name="deploy-a-vision-ai-model-to-azure-percept-dk"></a>Déployer un modèle IA de vision sur votre appareil Azure Percept DK

Suivez ce guide pour déployer un modèle IA de vision sur votre DK Azure Percept à partir d’Azure Percept Studio

## <a name="prerequisites"></a>Prérequis

- DK (devkit) Azure Percept
- [Abonnement Azure](https://azure.microsoft.com/free/)
- [Expérience d’installation du DK Azure Percept](./quickstart-percept-dk-set-up.md) : vous avez connecté votre devkit à un réseau Wi-Fi, créé un hub IoT et connecté votre devkit au hub IoT.

## <a name="model-deployment"></a>Déploiement de modèle

1. Mettez votre devkit sous tension.

1. Accédez à [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. À gauche de la page de vue d’ensemble, cliquez sur **Appareils**.

    :::image type="content" source="./media/how-to-deploy-model/overview-devices-inline.png" alt-text="Écran de vue d’ensemble d’Azure Percept Studio." lightbox="./media/how-to-deploy-model/overview-devices.png":::

1. Sélectionnez votre devkit dans la liste.

    :::image type="content" source="./media/how-to-deploy-model/select-device.png" alt-text="Liste des appareils Percept.":::

1. Dans la page suivante, cliquez sur **Déployer un exemple de modèle** si vous souhaitez déployer l’un des exemples de modèles de vision préentraînés. Si vous souhaitez déployer une [solution de vision sans code personnalisée](./tutorial-nocode-vision.md) existante, cliquez sur **Déployer un projet Custom Vision**.

    :::image type="content" source="./media/how-to-deploy-model/deploy-model.png" alt-text="Choix de modèles pour le déploiement.":::

1. Si vous avez choisi de déployer une solution de vision sans code, sélectionnez votre projet et votre itération de modèle préférée, puis cliquez sur **Déployer**.

1. Si vous avez choisi de déployer un exemple de modèle, sélectionnez le modèle et cliquez sur **Déployer sur l’appareil**.

1. Une fois le déploiement de votre modèle réussi, vous recevez un message d’état dans le coin supérieur droit de votre écran. Pour afficher l’inférence de votre modèle en action, cliquez sur le lien **Afficher le flux** dans le message d’état. Vous pouvez alors voir le flux vidéo RTSP du SoM Vision de votre devkit.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment afficher les [données de télémétrie de votre DK Azure Percept](how-to-view-telemetry.md).
