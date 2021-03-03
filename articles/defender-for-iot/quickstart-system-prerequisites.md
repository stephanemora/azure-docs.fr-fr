---
title: Prérequis système
description: Obtenez les prérequis système nécessaires à l’exécution d’Azure Defender pour IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/30/2020
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 7b609fec2a47dc0685b30dac12f43263127f70ef
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523207"
---
# <a name="system-prerequisites"></a>Prérequis système
Cet article liste les prérequis système nécessaires à l’exécution d’Azure Defender pour IoT.

## <a name="minimum-requirements"></a>Configuration minimale requise

- Commutateurs réseau qui prennent en charge la supervision du trafic via le port SPAN.
- Appliances matérielles pour les capteurs NTA.
- Rôle Contributeur pour l’abonnement Azure. Il est nécessaire uniquement pendant l’intégration pour définir les appareils validés et la connexion à Azure Sentinel.
- Rôle **Contributeur** pour Azure IoT Hub (niveau gratuit ou standard) pour la gestion connectée au cloud. Assurez-vous que la fonctionnalité **Azure Defender pour IoT** est activée.
- Pour la prise en charge des modules de sécurité au niveau de l’appareil, les agents Defender pour IoT gèrent une liste croissante d’appareils et de plateformes. Consultez la [liste des plateformes prises en charge](how-to-deploy-agent.md).

## <a name="supported-service-regions"></a>Régions de service prises en charge

Defender pour IoT route tout le trafic de toutes les régions européennes vers le centre de données régional Europe Ouest. Il route le trafic de toutes les autres régions vers le centre de données régional USA Centre.

Pour plus d’informations, consultez [Régions prises en charge par IoT Hub](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).

## <a name="next-steps"></a>Étapes suivantes

[Identifier les appliances nécessaires](how-to-identify-required-appliances.md)
[À propos de la configuration réseau d’Azure Defender pour IoT](how-to-set-up-your-network.md)
