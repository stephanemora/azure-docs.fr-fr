---
title: Afficher le flux vidéo RTSP de votre appareil Azure Percept DK
description: Découvrez comment afficher le flux vidéo RTSP d’Azure Percept DK.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: ef0f7a581329e9ee5e4d891b41d321ed559d64d7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531586"
---
# <a name="view-your-azure-percept-dk-rtsp-video-stream"></a>Afficher le flux vidéo RTSP de votre appareil Azure Percept DK

Suivez ce guide pour afficher le flux vidéo RTSP d’Azure Percept DK au sein d’Azure Percept Studio. L’inférence à partir des modèles IA de vision déployés sur votre appareil sera visible dans le flux web.

## <a name="prerequisites"></a>Prérequis

- DK (devkit) Azure Percept
- [Abonnement Azure](https://azure.microsoft.com/free/)
- [Expérience d’installation du DK Azure Percept](./quickstart-percept-dk-set-up.md) : vous avez connecté votre kit de développement à un réseau Wi-Fi, créé un hub IoT et connecté votre kit de développement au hub IoT

## <a name="view-the-rtsp-video-stream"></a>Afficher le flux vidéo RTSP

1. Mettez votre kit de développement sous tension.

1. Accédez à [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. À gauche de la page de vue d’ensemble, cliquez sur **Appareils**.

    :::image type="content" source="./media/how-to-view-video-stream/overview-devices-inline.png" alt-text="Écran de vue d’ensemble d’Azure Percept Studio." lightbox="./media/how-to-view-video-stream/overview-devices.png":::

1. Sélectionnez votre kit de développement dans la liste.

    :::image type="content" source="./media/how-to-view-video-stream/select-device.png" alt-text="Capture d’écran des appareils disponibles dans Azure Percept Studio.":::

1. Cliquez sur **View your device stream** (Afficher le flux de votre appareil).

    :::image type="content" source="./media/how-to-view-video-stream/view-device-stream.png" alt-text="Capture d’écran de la page de l’appareil montrant les actions de projet de vision disponibles.":::

    Un onglet séparé s’ouvre. Il montre le flux web en direct d’Azure Percept DK.

    :::image type="content" source="./media/how-to-view-video-stream/webstream.png" alt-text="Capture d’écran du flux web de l’appareil.":::

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment afficher les [données de télémétrie de votre DK Azure Percept](./how-to-view-telemetry.md).