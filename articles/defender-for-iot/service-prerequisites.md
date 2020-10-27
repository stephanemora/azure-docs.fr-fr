---
title: Composants et prérequis
description: Tout ce qu'il faut pour bien démarrer avec les prérequis du service Azure Defender pour IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.custom: references_regions
ms.openlocfilehash: b0913dc48f807c26396a38e31d293877b4561b7d
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089177"
---
# <a name="azure-defender-for-iot-prerequisites"></a>Prérequis d'Azure Defender pour IoT

Cet article fournit des explications sur les différents composants du service Defender pour IoT ainsi que sur les éléments nécessaires pour bien démarrer, et décrit les concepts de base pour bien comprendre le service.

## <a name="minimum-requirements"></a>Configuration minimale requise

- Analyse sans agent pour les appareils IoT et OT (basés sur la technologie CyberX)
    - Commutateurs réseau prenant en charge la surveillance du trafic via le port SPAN
    - Appliances matérielles pour le capteur NTA. Pour plus d’informations, consultez [Matériel certifié](https://aka.ms/AzureDefenderforIoTBareMetalAppliance)
    - Rôle **Contributeur** pour l’abonnement Azure (requis uniquement pendant l’intégration pour la définition d’appareils validés)
    - Rôle **Contributeur** pour IoT Hub (niveau gratuit ou standard) (pour la gestion connectée au cloud)
- Sécurité des appareils IoT gérés via Azure IoT Hub
    - Rôle **Contributeur** pour IoT Hub
    - IoT Hub : La bascule de fonctionnalité **Azure Defender pour IoT** doit être activée
    - Pour la prise en charge des modules de sécurité au niveau de l’appareil  
        - Les agents Defender pour IoT gèrent une liste croissante d'appareils et de plateformes. Voir la [liste des plateformes prises en charge](how-to-deploy-agent.md)


## <a name="supported-service-regions"></a>Régions de service prises en charge

Pour plus d’informations, consultez [Régions prises en charge par IoT Hub](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub). 

Defender pour IoT achemine le trafic de l'ensemble des régions européennes vers le centre de données régional Europe Ouest, tandis que le trafic des autres régions est acheminé vers le centre de données régional USA Centre.

## <a name="next-steps"></a>Étapes suivantes

- Consultez la [vue d’ensemble](overview.md) de la sécurité Azure IoT
- Découvrez comment [Activer ce service](quickstart-onboard-iot-hub.md)
- Consultez la [FAQ de Defender pour IoT](resources-frequently-asked-questions.md)
- Découvrez comment [comprendre les alertes Defender pour IoT](concept-security-alerts.md)
