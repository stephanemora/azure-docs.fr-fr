---
title: Vue d’ensemble du DK Azure Percept
description: Découvrez le DK Azure Percept.
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 4c2ace609d67cc48d1b73bdb044e7048ebda21e7
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098330"
---
# <a name="azure-percept-dk-overview"></a>Vue d’ensemble du DK Azure Percept

Azure Percept DK est un kit de développement IA et IoT Edge conçu pour le développement de preuves de concept IA de vision et d’audio. Associé à [Azure Percept Studio](./overview-azure-percept-studio.md) et à [Azure Percept Audio](./overview-azure-percept-audio.md), il devient une plateforme puissante et facile à utiliser permettant de créer des solutions d’intelligence artificielle Edge pour un large éventail d’applications IA de vision et d’audio. Il est disponible à l’achat sur [Microsoft Store en ligne](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

:::image type="content" source="./media/overview-azure-percept-dk/dk-image.png" alt-text="Appareil Azure Percept DK.":::

## <a name="key-features"></a>Principales fonctionnalités

- **Capacité d’exécuter l’intelligence artificielle à la périphérie**. Avec l’accélération matérielle intégrée, il peut exécuter des modèles d’IA de vision sans connexion au Cloud.
- **Sécurité intégrée avec racine matérielle de confiance**. Pour plus d’informations, consultez la vue d’ensemble de la [sécurité Azure Percept](./overview-percept-security.md).
- **Intégration transparente à [Azure Percept Studio](./overview-azure-percept-studio.md)** et à d’autres services Azure. Par exemple, Azure IoT Hub, Azure Cognitive Services et [Live Video Analytics](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/overview).
- **Intégration transparente avec [Azure Percept Audio](./overview-azure-percept-audio.md) (facultatif)**
- **Prise en charge des principales plateformes d’IA**. Par exemple, ONNX et TensorFlow.
- **Intégration au système de rails 80/20**. Pour faciliter la création de prototypes dans les environnements de production. En savoir plus sur [l’intégration 80/20](./overview-8020-integration.md).

## <a name="hardware-components"></a>Composants matériels

- Carte de base du DK Azure Percept
    - Processeur NXP iMX8m
    - Module de plateforme sécurisée (TPM) version 2.0
    - Connectivité WiFi et Bluetooth
    - Voir la [fiche technique](./azure-percept-dk-datasheet.md) complète
- Système sur module (SoM) de vision Azure Percept
    - Intel Movidius Myriad X (MA2085) VPU
    - Capteur d’appareil photo RVB avec possibilité d’ajout d’un deuxième
    - Voir la [fiche technique](./azure-percept-vision-datasheet.md) complète

## <a name="get-started-with-the-azure-percept-dk"></a>Bien démarrer avec le DK Azure Percept

- Suivre les guides de démarrage rapide
    - [Déballer et assembler le DK Azure Percept](./quickstart-percept-dk-unboxing.md)
    - [Configurer le DK Azure Percept et exécuter votre premier modèle d’IA de vision](./quickstart-percept-dk-set-up.md)
- Commencer à créer une preuve de concepts avec ces tutoriels
    - [Créer une solution de vision sans code dans Azure Percept Studio](./tutorial-nocode-vision.md)
    - [Créer un Assistant vocal dans Azure Percept Studio](./tutorial-no-code-speech.md)

## <a name="next-steps"></a>Étapes suivantes

Commandez un DK Azure Percept sur la [boutique en ligne de Microsoft](https://go.microsoft.com/fwlink/p/?LinkId=2155270).
