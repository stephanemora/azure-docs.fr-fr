---
title: Configurer Azure Percept Audio
description: Découvrez comment connecter votre appareil Azure Percept Audio au DK Azure Percept
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: fa3dad8cdd38e6db621d8194cc9472430c7c5008
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605788"
---
# <a name="azure-percept-audio-setup"></a>Installation d’Azure Percept Audio

Azure Percept Audio est prêt à l’emploi avec le DK Azure Percept. Aucune configuration unique n’est nécessaire.

## <a name="prerequisites"></a>Prérequis

- DK (devkit) Azure Percept
- Azure Percept Audio
- [Abonnement Azure](https://azure.microsoft.com/free/)
- [Expérience d’installation du DK Azure Percept](./quickstart-percept-dk-set-up.md) : vous avez connecté votre devkit à un réseau Wi-Fi, créé un hub IoT et connecté votre devkit au hub IoT
- Haut-parleur ou casque qui peut être branché à une prise audio de 3,5 mm (facultatif)

## <a name="connecting-your-devices"></a>Connexion de vos appareils

1. Raccordez l’appareil Azure Percept Audio à la carte de base d’Azure Percept DK à l’aide du câble Micro USB vers USB Type A inclus. Connectez l’extrémité du câble Micro USB à la carte d’interposeur (développeur) audio, et l’extrémité du câble Type A à la carte de base Percept DK.

1. (Facultatif) Branchez votre haut-parleur ou votre casque sur l’appareil Azure Percept Audio en utilisant la prise audio étiquetée « Line Out » (sortie de ligne). De cette façon, vous pourrez entendre les réponses audio.

1. Mettez sous tension le devkit. La LED L02 sur la carte d’interposeur audio passe au blanc clignotant pour indiquer que l’appareil a été mis sous tension et que Audio SoM s’authentifie.

1. Attendez que le processus d’authentification se termine. Cette opération peut prendre jusqu’à 3 minutes.

1. Vous pouvez commencer le prototypage quand vous voyez l’une des choses suivantes :

    - La LED L02 passe au blanc fixe : l’authentification est terminée et le devkit n’a pas encore été configuré avec un mot clé.
    - Les trois LED deviennent bleues : l’authentification est terminée et le devkit a été configuré avec un mot clé.

## <a name="next-steps"></a>Étapes suivantes

Créez une [solution de reconnaissance vocale sans code](./tutorial-no-code-speech.md) dans [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).
