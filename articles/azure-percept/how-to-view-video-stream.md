---
title: Afficher le flux vidéo RTSP de votre DK Azure Percept
description: Découvrez comment afficher le flux vidéo RTSP à partir du SoM de vision du DK Azure Percept
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 20fb8495e17d4294351a50c3bc97436de9f03450
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101660255"
---
# <a name="view-your-azure-percept-dks-rtsp-video-stream"></a>Afficher le flux vidéo RTSP de votre DK Azure Percept

Suivez ce guide pour afficher le flux vidéo RTSP à partir du SoM de vision du DK Azure Percept au sein d’Azure Percept Studio. L’inférence à partir des modèles IA de vision déployés sur votre appareil sera visible dans le flux web.

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

    :::image type="content" source="./media/how-to-view-video-stream/select-device.png" alt-text="Écran de vue d’ensemble d’Azure Percept Studio.":::

1. Cliquez sur **View your device stream** (Afficher le flux de votre appareil).

    :::image type="content" source="./media/how-to-view-video-stream/view-device-stream.png" alt-text="Écran de vue d’ensemble d’Azure Percept Studio.":::

    Cela ouvre un onglet séparé qui montre le flux web en direct à partir du SoM de vision de votre DK Azure Percept.

    :::image type="content" source="./media/how-to-view-video-stream/webstream.png" alt-text="Écran de vue d’ensemble d’Azure Percept Studio.":::

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment afficher les [données de télémétrie de votre DK Azure Percept](./how-to-view-telemetry.md).