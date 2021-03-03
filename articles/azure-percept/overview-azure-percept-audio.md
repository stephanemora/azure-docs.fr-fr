---
title: Vue d’ensemble de l’appareil Azure Percept Audio
description: Apprenez-en davantage sur Azure Percept Audio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: a63f471498667f58fc80f89323ad93b0feb8bc95
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678339"
---
# <a name="introduction-to-azure-percept-audio"></a>Présentation d’Azure Percept Audio

Azure Percept Audio est un appareil accessoire qui ajoute des fonctionnalités d’intelligence artificielle vocales au DK Azure Percept. Il contient un accélérateur d’IA vocal préconfiguré et un tableau linéaire à quatre microphones, ce qui vous permet d’appliquer des commandes personnalisées, une dentification de mot clé et une reconnaissance vocale à champ lointain aux appareils d’écoute locaux. Azure Percept Audio permet aux fabricants d’appareils d’étendre les fonctionnalités de vision du DK Azure Percept à de nouveaux appareils intelligents activés par la voix. Il est intégré à l’origine au DK Azure Percept, à Azure Percept Studio et à d’autres services de gestion Azure Edge. Il est disponible à l’achat sur [Microsoft Store en ligne](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

![Appareil Azure Percept Audio.](./media/overview-azure-percept-audio/percept-audio.png)


## <a name="azure-percept-audio-components"></a>Composants Azure Percept Audio

Azure Percept Audio contient les principaux composants suivants :

- Appareil Azure Percept Audio (SoM) prêt pour la production avec tableau linéaire à quatre microphones
- Carte de développement (avec 2 boutons, 3 voyants LED, micro USB et prise audio 3,5 mm)
- Câbles requis : câble flexible, micro USB de type B vers USB-A
- Carte de bienvenue
- Plaque de montage mécanique avec montage de série 1010 80/20 intégré


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

### <a name="build-a-no-code-prototype"></a>Créer un prototype sans code

Créez une [solution vocale sans code](./tutorial-no-code-speech.md) à l’aide de modèles de l’assistant vocal Azure Percept pour les scénarios d’hôtellerie, de santé, d’inventaire et d’automobile.

## <a name="additional-technical-information"></a>Informations techniques supplémentaires

- [Feuille de données Azure Percept Audio](./azure-percept-audio-datasheet.md)

## <a name="next-steps"></a>Étapes suivantes

Commandez un appareil Azure Percept Audio auprès de [Microsoft Store en ligne](https://go.microsoft.com/fwlink/p/?LinkId=2155270).