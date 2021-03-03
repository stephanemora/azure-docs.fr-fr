---
title: Déployer un modèle IA de vision sur votre DK Azure Percept
description: Découvrir comment déployer un modèle IA de vision sur votre DK Azure Percept à partir d’Azure Percept Studio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 5ad263f5ae3b0b8e91de30b620002e33086608b4
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101660229"
---
# <a name="capture-images-for-a-vision-project"></a>Capturer des images pour un projet de vision

Suivez ce guide afin de capturer des images à l’aide du SoM Vision du DK Azure Percept pour un projet de vision existant dans Azure Percept Studio. Si vous n’avez pas encore créé de projet de vision, consultez le [tutoriel pour créer un projet de vision sans code](./tutorial-nocode-vision.md).

## <a name="prerequisites"></a>Prérequis

- DK (devkit) Azure Percept
- [Abonnement Azure](https://azure.microsoft.com/free/)
- [Expérience d’installation du DK Azure Percept](./quickstart-percept-dk-set-up.md) : vous avez connecté votre devkit à un réseau Wi-Fi, créé un hub IoT et connecté votre devkit au hub IoT.
- [Projet de vision sans code](./tutorial-nocode-vision.md)

## <a name="capture-images"></a>Images de capture

1. Mettez votre devkit sous tension.

1. Accédez à [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. À gauche de la page de vue d’ensemble, cliquez sur **Appareils**.

    :::image type="content" source="./media/how-to-capture-images/overview-devices-inline.png" alt-text="Écran de vue d’ensemble d’Azure Percept Studio." lightbox="./media/how-to-capture-images/overview-devices.png":::

1. Sélectionnez votre devkit dans la liste.

    :::image type="content" source="./media/how-to-capture-images/select-device.png" alt-text="Liste des appareils Percept.":::

1. Dans la page de votre appareil, cliquez sur **Capturer les images d’un projet**.

    :::image type="content" source="./media/how-to-capture-images/capture-images.png" alt-text="Page des appareils Percept avec les actions disponibles listées.":::

1. Dans la fenêtre **Capture d’image**, effectuez les étapes suivantes :

    1. Dans le menu déroulant **Projet**, sélectionnez le projet de vision pour lequel vous souhaitez collecter des images.

    1. Cliquez sur **Afficher le flux d’appareil** pour vérifier que la caméra du SoM Vision est bien placée.

    1. Cliquez sur **Prendre une photo** pour capturer une image.

    1. Vous pouvez également cocher la case à côté de **Capture d’image automatique** afin de configurer un minuteur pour la capture d’image :

        1. Sélectionnez le taux d’acquisition d’images de votre choix sous **Taux de capture**.
        1. Sélectionnez le nombre total d’images que vous souhaitez collecter sous **Cible**.

    :::image type="content" source="./media/how-to-capture-images/take-photo.png" alt-text="Page des appareils Percept avec les actions disponibles listées.":::

Toutes les images sont accessibles dans [Custom Vision](https://www.customvision.ai/).

## <a name="next-steps"></a>Étapes suivantes

[Tester et réentraîner votre modèle de vision sans code](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/test-your-model).