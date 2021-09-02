---
title: Capturer des images dans Azure Percept Studio
description: Comment capturer des images avec Azure percept DK dans Azure Percept Studio
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: b724e18d8f9119ac33d878cfbf4393c4712d003f
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123219679"
---
# <a name="capture-images-in-azure-percept-studio"></a>Capturer des images dans Azure Percept Studio

Suivez ce guide afin de capturer des images avec Azure Percept DK pour un projet de vision existant. Si vous n’avez pas encore créé de projet de vision, consultez le [tutoriel pour créer un projet de vision sans code](./tutorial-nocode-vision.md).

## <a name="prerequisites"></a>Prérequis

- DK (devkit) Azure Percept
- [Abonnement Azure](https://azure.microsoft.com/free/)
- [Expérience d’installation du DK Azure Percept](./quickstart-percept-dk-set-up.md) : vous avez connecté votre devkit à un réseau Wi-Fi, créé un hub IoT et connecté votre devkit au hub IoT.
- [Projet de vision sans code](./tutorial-nocode-vision.md)

## <a name="capture-images"></a>Images de capture

1. Mettez votre devkit sous tension.

1. Accédez à [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. À gauche de la page de vue d’ensemble, sélectionnez **Appareils**.

    :::image type="content" source="./media/how-to-capture-images/overview-devices-inline.png" alt-text="Écran de vue d’ensemble d’Azure Percept Studio." lightbox="./media/how-to-capture-images/overview-devices.png":::

1. Sélectionnez votre devkit dans la liste.

    :::image type="content" source="./media/how-to-capture-images/select-device.png" alt-text="Liste des appareils Percept.":::

1. Dans la page de votre appareil, sélectionnez **Capturer les images d’un projet**.

    :::image type="content" source="./media/how-to-capture-images/capture-images.png" alt-text="Page des appareils Percept avec les actions disponibles listées.":::

1. Dans la fenêtre **Capture d’image**, effectuez les étapes suivantes :

    1. Dans le menu déroulant **Projet**, sélectionnez le projet de vision pour lequel vous souhaitez collecter des images.

    1. Sélectionnez **Afficher le flux d’appareil** pour vérifier que la caméra du SoM Vision est bien placée.

    1. Sélectionnez **Prendre une photo** pour capturer une image.

    1. Vous pouvez également cocher la case à côté de **Capture d’image automatique** afin de configurer un minuteur pour la capture d’image :

        1. Sélectionnez le taux d’acquisition d’images de votre choix sous **Taux de capture**.
        1. Sélectionnez le nombre total d’images que vous souhaitez collecter sous **Cible**.

    :::image type="content" source="./media/how-to-capture-images/take-photo.png" alt-text="Écran Capture d’image.":::

Toutes les images sont accessibles dans [Custom Vision](https://www.customvision.ai/).

## <a name="next-steps"></a>Étapes suivantes

[Tester et réentraîner votre modèle de vision sans code](../cognitive-services/custom-vision-service/test-your-model.md).