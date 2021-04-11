---
title: Vue d’ensemble de l’appareil Azure Percept Audio
description: Apprenez-en davantage sur Azure Percept Audio
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: template-concept
ms.openlocfilehash: 23fae249cfceec75af0b7c49a3875ab447ecbafd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564260"
---
# <a name="introduction-to-azure-percept-audio"></a>Présentation d’Azure Percept Audio

Azure Percept Audio est un appareil accessoire qui ajoute des fonctionnalités d’intelligence artificielle vocales à [Azure Percept DK](./overview-azure-percept-dk.md). Il contient un processeur audio préconfiguré et un tableau linéaire à quatre microphones, ce qui offre la possibilité d’utiliser des commandes vocales, la détection de mots clés et le message à champ lointain avec Azure Cognitive Services. Il est intégré à l’origine à Azure Percept DK, à [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) et à d’autres services de gestion Azure Edge. Azure Percept Audio est disponible à l’achat sur [Microsoft Store en ligne](https://go.microsoft.com/fwlink/p/?LinkId=2155270).

> [!div class="nextstepaction"]
> [Acheter maintenant](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

</br>

> [!VIDEO https://www.youtube.com/embed/Qj8NGn-7s5A]

## <a name="azure-percept-audio-components"></a>Composants Azure Percept Audio

Azure Percept Audio contient les principaux composants suivants :

- Appareil Azure Percept Audio prêt pour la production (SoM) avec tableau linéaire à quatre microphones et traitement audio effectué par un codec XMOS
- Carte de développeur (interposeur) : 2 boutons, 3 LED, Micro USB et prise audio 3,5 mm
- Câbles nécessaires : câble FPC, USB Micro Type B vers USB-A
- Carte de bienvenue
- Plaque de montage mécanique avec montage de série 1010 80/20 intégré

## <a name="compute-capabilities"></a>Fonctionnalités de calcul 

Azure Percept Audio transmet l’entrée audio via la pile de messages qui s’exécute sur le CPU de l’unité Azure Percept DK de manière cloud edge hybride. Une carte de base dotée d’un système d’exploitation qui prend en charge la pile vocale est donc nécessaire pour qu’Azure Percept Audio puisse fonctionner. 

Le traitement audio est effectué de la façon suivante : 

- Azure Percept Audio : capture et convertit l’audio, puis l’envoie vers le DK et la prise audio.

- Azure Percept DK : la pile Speech se charge de la formation de faisceau et de la suppression d’écho, et traite l’audio entrant pour optimiser la reconnaissance vocale. Après traitement, il effectue une détection de mots clés.

- Cloud : traite les commandes et expressions en langage naturel, la vérification des mots clés et le réentraînement. 

- Hors connexion : si l’appareil est hors connexion, il détecte le mot clé et capture les données de télémétrie de l’état de la connexion Internet. Une augmentation du taux d’acceptations erronées pour la détection de mots clés peut être observée, car la vérification des mots clés dans le cloud ne peut pas être effectuée. 

## <a name="getting-started"></a>Prise en main

- [Assembler votre DK Azure Percept](./quickstart-percept-dk-unboxing.md)
- [Connecter votre appareil Azure Percept Audio à votre kit de développement](./quickstart-percept-audio-setup.md)
- [Finaliser l’expérience d’installation du DK Azure Percept](./quickstart-percept-dk-set-up.md)

## <a name="build-a-no-code-prototype"></a>Créer un prototype sans code

Créez une [solution de reconnaissance vocale sans code](./tutorial-no-code-speech.md) dans [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) à l'aide de modèles de l'assistant vocal Azure Percept pour les scénarios d'hôtellerie, de santé, d'inventaire et d'automobile.

### <a name="manage-your-no-code-speech-solution"></a>Gestion de la solution vocale sans code

- [Configurer l’Assistant vocal dans Azure Percept Studio](./how-to-manage-voice-assistant.md)
- [Configurer l’Assistant vocal dans IoT Hub](./how-to-configure-voice-assistant.md)
- [Résolution des problèmes Azure Percept Audio](./troubleshoot-audio-accessory-speech-module.md)

## <a name="additional-technical-information"></a>Informations techniques supplémentaires

- [Feuille de données Azure Percept Audio](./azure-percept-audio-datasheet.md)
- [Comportement des boutons et des LED](./audio-button-led-behavior.md)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Achetez un appareil Azure Percept Audio auprès de Microsoft Store en ligne](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
