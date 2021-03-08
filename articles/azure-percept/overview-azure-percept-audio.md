---
title: Vue d’ensemble de l’appareil Azure Percept Audio
description: Apprenez-en davantage sur Azure Percept Audio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 8884663b3f0e861e62f48c3aab680f0f31e74428
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098381"
---
# <a name="introduction-to-azure-percept-audio"></a>Présentation d’Azure Percept Audio

Azure Percept Audio est un appareil accessoire qui ajoute des fonctionnalités d’intelligence artificielle vocales au DK Azure Percept. Il contient un processeur audio préconfiguré et une barrette à quatre microphones, ce qui offre la possibilité d’appliquer des commandes vocales, la détection de mots clés et la reconnaissance vocale à champ lointain à des appareils d’écoute locaux avec Azure Cognitive Services. Azure Percept Audio permet aux fabricants d’appareils d’étendre Azure Percept DK au-delà des fonctionnalités de vision, pour créer de nouveaux appareils intelligents commandés par la voix. Il est intégré à l’origine au DK Azure Percept, à Azure Percept Studio et à d’autres services de gestion Azure Edge. Il est disponible à l’achat sur [Microsoft Store en ligne](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

:::image type="content" source="./media/overview-azure-percept-audio/percept-audio.png" alt-text="Appareil Azure Percept Audio.":::

## <a name="azure-percept-audio-components"></a>Composants Azure Percept Audio

Azure Percept Audio contient les principaux composants suivants :

- Appareil Azure Percept Audio prêt pour la production avec barrette à quatre microphones et traitement audio effectué par un codec XMOS
- Carte de développeur (interposeur) (2 boutons, 3 LED, Micro USB et prise audio 3,5 mm)
- Câbles nécessaires : câble FPC, USB Micro Type B vers USB-A
- Carte de bienvenue
- Plaque de montage mécanique avec montage de série 1010 80/20 intégré

## <a name="compute-capabilities"></a>Fonctionnalités de calcul 

Azure Percept Audio transmet l’entrée audio par le biais de la pile vocale qui s’exécute sur le processeur de la carte de base d’Azure Percept DK de manière hybride Edge-cloud. Une carte de base dotée d’un système d’exploitation qui prend en charge la pile vocale est donc nécessaire pour qu’Azure Percept Audio puisse fonctionner. 

Le traitement est effectué de la façon suivante : 

- Azure Percept Audio : effectue la formation de faisceau et la suppression d’écho, et traite l’audio entrant pour optimiser la reconnaissance vocale et l’envoyer à DK.  

- Azure Percept DK : la pile vocale effectue la détection de mots clés.  

- Cloud : traite les commandes et expressions en langage naturel, la vérification des mots clés et le réentraînement. 

- Hors connexion : si l’appareil est hors connexion, il détecte le mot clé et capture les données de télémétrie de l’état de la connexion Internet. Une augmentation du taux d’acceptations erronées pour la détection de mots clés peut être observée, car la vérification des mots clés dans le cloud ne peut pas être effectuée. 

<!---

## How it works

Azure Percept Audio passes the audio input to the Azure Percept DK carrier board in a hybrid edge-cloud manner. Specifically,

- The Azure Percept Audio device: processes the incoming speech input to the clearest format by executing beam forming and echo cancellation befor sending the input to the Azure Percept DK. 
- The Azure Percept DK uses edge processing to perform keyword spotting and then sends the relevant inputs to Azure speech services.
- Cloud: Processing of natural language commands and phrases, in addition to keyword verification and retraining.
- Offline: If the device is offline it will detect the keyword and capture telemetry that there is no internet connection at the time of the command. It will not be able to weed out false accepts since it cannot perform keyword verification.

-->

## <a name="getting-started"></a>Prise en main

- [Assembler votre DK Azure Percept](./quickstart-percept-dk-unboxing.md)
- [Finaliser l’expérience d’installation du DK Azure Percept](./quickstart-percept-dk-set-up.md)
- [Connecter votre appareil Azure Percept Audio à votre kit de développement](./quickstart-percept-audio-setup.md)

## <a name="build-a-no-code-prototype"></a>Créer un prototype sans code

Créez une [solution vocale sans code](./tutorial-no-code-speech.md) à l’aide de modèles de l’assistant vocal Azure Percept pour les scénarios d’hôtellerie, de santé, d’inventaire et d’automobile.

### <a name="manage-your-no-code-speech-solution"></a>Gestion de la solution vocale sans code

- [Configurer l’Assistant vocal dans IoT Hub](./how-to-manage-voice-assistant.md)
- [Configurer l’Assistant vocal dans Azure Percept Studio](./how-to-configure-voice-assistant.md)
- [Résolution des problèmes Azure Percept Audio](./troubleshoot-audio-accessory-speech-module.md)

## <a name="additional-technical-information"></a>Informations techniques supplémentaires

- [Feuille de données Azure Percept Audio](./azure-percept-audio-datasheet.md)
- [Comportement des boutons et des LED](./audio-button-led-behavior.md)

## <a name="next-steps"></a>Étapes suivantes

Commandez un appareil Azure Percept Audio auprès de [Microsoft Store en ligne](https://go.microsoft.com/fwlink/p/?LinkId=2155270).
