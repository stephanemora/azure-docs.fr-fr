---
title: Bien démarrer avec Azure Percept Audio
description: Découvrez comment connecter votre appareil Azure Percept Audio au DK Azure Percept
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: 660f03ce248a27a00fdd443964fbdba2fe3adeb0
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179270"
---
# <a name="azure-percept-audio-setup"></a>Installation d’Azure Percept Audio

Azure Percept Audio est prêt à l’emploi avec le DK Azure Percept. Aucune configuration unique n’est nécessaire.

## <a name="prerequisites"></a>Prérequis

- DK (devkit) Azure Percept
- Azure Percept Audio
- [Abonnement Azure](https://azure.microsoft.com/free/)
- [Expérience d’installation du DK Azure Percept](./quickstart-percept-dk-set-up.md) : vous avez connecté votre devkit à un réseau Wi-Fi, créé un hub IoT et connecté votre devkit au hub IoT
- Haut-parleur ou casque qui peut être branché à une prise audio de 3,5 mm (facultatif)

## <a name="connecting-your-devices"></a>Connexion de vos appareils

1. Raccordez l’appareil Azure Percept Audio à la carte de base d’Azure Percept DK à l’aide du câble Micro USB vers USB Type A inclus. Connectez l’extrémité du câble Micro USB à la carte d’interposeur (développeur), et l’extrémité du câble Type A à la carte de base Percept DK.
1. (Facultatif) Branchez votre haut-parleur ou votre casque à Azure Percept Audio à l’aide de la prise audio étiquetée « Line Out ». Cela vous permettra d’entendre les réponses audio de votre Assistant vocal. Si vous ne connectez pas de haut-parleur ou de casque, vous pourrez toujours voir les réponses sous forme de texte dans la fenêtre de démonstration. 

1. Mettez sous tension le devkit. La LED L02 sur la carte d’interposeur passe au blanc clignotant pour indiquer que l’appareil a été mis sous tension et que Percept DK s’authentifie.

1. Attendez que le processus d’authentification se termine. Cette opération peut prendre jusqu’à 3 minutes.

1. Vous pouvez commencer le prototypage quand vous voyez l’une des choses suivantes :

    - La LED L02 passe au blanc fixe. Cela indique que l’authentification est terminée et que le devkit n’a pas encore été configuré avec un mot clé.
    - Les trois voyants passent au bleu. Cela indique que l’authentification est terminée et que le devkit a été configuré avec un mot clé.

## <a name="next-steps"></a>Étapes suivantes

Créez une [solution de reconnaissance vocale sans code](./tutorial-no-code-speech.md) dans [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).
