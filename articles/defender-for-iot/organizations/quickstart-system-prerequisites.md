---
title: 'Démarrage rapide : Prérequis système'
description: Ce guide de démarrage rapide répertorie les prérequis système nécessaires à l'exécution d'Azure Defender pour IoT.
ms.date: 11/30/2020
ms.topic: quickstart
ms.openlocfilehash: f770a6436563439603403e0aedf62d496a38d805
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113011102"
---
# <a name="quickstart-system-prerequisites"></a>Démarrage rapide : Prérequis système

Cet article liste les prérequis système nécessaires à l’exécution d’Azure Defender pour IoT.

## <a name="prerequisites"></a>Prérequis

- Aucun

## <a name="minimum-requirements"></a>Configuration minimale requise

- Commutateurs réseau qui prennent en charge la supervision du trafic via le port SPAN.
- Appliances matérielles pour les capteurs NTA.
- Rôle Contributeur pour l’abonnement Azure. Il est nécessaire uniquement pendant l’intégration pour définir les appareils validés et la connexion à Azure Sentinel.
- Rôle **Contributeur** pour Azure IoT Hub (niveau gratuit ou standard) pour la gestion connectée au cloud. Assurez-vous que la fonctionnalité **Azure Defender pour IoT** est activée.

## <a name="supported-service-regions"></a>Régions de service prises en charge

Defender pour IoT route tout le trafic de toutes les régions européennes vers le centre de données régional Europe Ouest. Il route le trafic de toutes les autres régions vers le centre de données régional USA Centre.

Pour plus d’informations, consultez [Régions prises en charge par IoT Hub](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Identifier les appliances nécessaires](how-to-identify-required-appliances.md)
