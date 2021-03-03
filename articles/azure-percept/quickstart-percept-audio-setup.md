---
title: Bien démarrer avec Azure Percept Audio
description: Découvrez comment connecter votre appareil Azure Percept Audio au DK Azure Percept
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: 575107859f56df742ab41a299269c250511022b3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664216"
---
# <a name="azure-percept-audio-setup"></a>Installation d’Azure Percept Audio

Azure Percept Audio est prêt à l’emploi avec le DK Azure Percept. Aucune configuration unique n’est nécessaire.

## <a name="prerequisites"></a>Prérequis

- DK (devkit) Azure Percept
- Azure Percept Audio
- [Abonnement Azure](https://azure.microsoft.com/free/)
- [Expérience d’installation du DK Azure Percept](./quickstart-percept-dk-set-up.md) : vous avez connecté votre DevKit à un réseau Wi-Fi, créé un hub IoT et connecté votre DevKit au hub IoT.

## <a name="connecting-your-devices"></a>Connexion de vos appareils

1. Connectez l’appareil Azure Percept Audio à la carte de base du DK Azure Percept à l’aide du câble USB Micro Type-B vers USB Type-A inclus. Connectez l’extrémité du câble Micro Type-B au SoM audio, et l’extrémité du câble Type-A à la carte porteuse Percept DK.

1. Mettez sous tension le devkit.

    - Le voyant L01 du SoM audio passe au vert fixe pour indiquer que l’appareil est sous tension.
    - Le voyant L02 passe au vert clignotant pour indiquer que le SoM audio s’authentifie.

1. Attendez que le processus d’authentification se termine. Cette opération peut prendre jusqu’à 3 minutes.

1. Vous pouvez commencer le prototypage quand vous voyez l’une des choses suivantes :

    - Le voyant L01 s’éteint et L02 s’allume en blanc. Cela indique que l’authentification est terminée et que le devkit n’a pas encore été configuré avec un mot clé.
    - Les trois voyants passent au bleu. Cela indique que l’authentification est terminée et que le devkit a été configuré avec un mot clé.

    > [!NOTE]
    > Contactez le support si votre devkit ne parvient pas à s’identifier.

## <a name="next-steps"></a>Étapes suivantes

Créez une [solution de reconnaissance vocale sans code](./tutorial-no-code-speech.md).