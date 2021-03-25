---
title: Capturer des images pour une solution de vision sans code dans Azure Percept Studio
description: Découvrez comment capturer des images avec votre DK Azure Percept dans Azure Percept Studio pour une solution de vision sans code.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 44bf498af52f4d8a0d880dc1f1d5874d5b444cae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035532"
---
# <a name="capture-images-for-a-vision-project-in-azure-percept-studio"></a>Capturer des images pour un projet de vision dans Azure Percept Studio

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

    :::image type="content" source="./media/how-to-capture-images/take-photo.png" alt-text="Écran Capture d’image.":::

Toutes les images sont accessibles dans [Custom Vision](https://www.customvision.ai/).

## <a name="next-steps"></a>Étapes suivantes

[Tester et réentraîner votre modèle de vision sans code](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/test-your-model).