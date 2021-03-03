---
title: Déballer et assembler vos composants Azure Percept DK
description: Découvrez comment déballer, connecter et mettre sous tension votre appareil Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/16/2021
ms.custom: template-quickstart
ms.openlocfilehash: 40751401760d877fe3feab39f3fea1f2fbeee54b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664215"
---
# <a name="quickstart-unbox-and-assemble-your-azure-percept-dk-components"></a>Démarrage rapide : Déballer et assembler vos composants Azure Percept DK

Une fois que vous avez reçu votre appareil Azure Percept DK, consultez ce guide pour plus d’informations sur la connexion des composants et la mise sous tension de l’appareil.

## <a name="prerequisites"></a>Prérequis

- Azure Percept DK (devkit)
- Tournevis P7 (Facultatif. Utilisé pour sécuriser le connecteur du câble d’alimentation qui est relié à la carte porteuse)

## <a name="unbox-and-assemble-your-device"></a>Déballer et assembler votre appareil

1. Déballez les composants Azure Percept DK.

    Le devkit contient une carte porteuse, un SoM de vision Azure Percept, une boîte d’accessoires contenant des antennes et des câbles, ainsi qu’une carte de bienvenue avec une clé hexadécimale.

1. Connectez les composants du devkit.

    > [!NOTE]
    > Le port de l’adaptateur se trouve sur le côté droit de la carte porteuse. Les ports restants (2 USB-A, 1 USB-C, 1 HDMI et 1 Ethernet) et le bouton de réinitialisation se trouvent sur le côté gauche de la carte porteuse.

    1. Vissez à la main les deux antennes Wi-Fi sur la carte porteuse.

    1. Connectez le SoM de vision au port USB-C de la carte porteuse à l’aide du câble USB-C.

    1. Connectez le câble d’alimentation à l’adaptateur.

    1. Retirez tous les emballages plastiques restants des appareils.

    1. Connectez l’adaptateur et le câble d’alimentation à la carte porteuse et à une prise murale. Pour sécuriser entièrement le connecteur du câble d’alimentation qui est relié à la carte porteuse, utilisez un tournevis P7 (non inclus dans le devkit) pour serrer les vis du connecteur.

    1. Lorsque vous branchez le câble d’alimentation sur une prise murale, l’appareil se met automatiquement sous tension. Le bouton de réinitialisation situé sur le côté gauche de la carte porteuse s’allume alors. Vous devez attendre que l’appareil démarre.

        > [!NOTE]
        > Le bouton de réinitialisation sert à mettre hors tension ou à réinitialiser l’appareil lorsque celui-ci est branché sur une prise d’alimentation. En cas de panne de courant, l’appareil est automatiquement réinitialisé puis rallumé.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que votre devkit est branché et sous tension, consultez la procédure pas à pas concernant l’installation d’Azure Percept DK pour terminer la configuration de l’appareil. La procédure d’installation vous permet de connecter votre devkit à un réseau Wi-Fi, de configurer une connexion SSH, de créer un hub IoT et de provisionner votre devkit dans votre compte Azure. Une fois que vous avez terminé la configuration de l’appareil, vous êtes prêt à passer au prototypage.