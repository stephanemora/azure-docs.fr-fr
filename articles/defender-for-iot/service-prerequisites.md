---
title: Composants et prérequis
description: Tout ce qu'il faut pour bien démarrer avec les prérequis du service Azure Defender pour IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.custom: references_regions
ms.openlocfilehash: 19263f8db58c8d20288d3ae74c24efd85667bc33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90929942"
---
# <a name="azure-defender-for-iot-prerequisites"></a>Prérequis d'Azure Defender pour IoT

Cet article fournit des explications sur les différents composants du service Defender pour IoT ainsi que sur les éléments nécessaires pour bien démarrer, et décrit les concepts de base pour bien comprendre le service.

## <a name="minimum-requirements"></a>Configuration minimale requise

- IoT Hub niveau Standard
  - Privilèges du niveau du rôle Azure **Propriétaire**
- [Espace de travail Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)
- Azure Security Center (recommandé)
  - Il est recommandé, mais non obligatoire, d’utiliser Azure Security Center. Sans Azure Security Center, vous ne pourriez pas afficher vos autres ressources Azure dans le IoT Hub.

## <a name="working-with-defender-for-iot-service"></a>Utiliser le service Defender pour IoT

Des insights et des rapports sur Defender pour IoT sont disponibles sur Azure IoT Hub et Azure Security Center. Un compte doté des privilèges de niveau **Propriétaire** est nécessaire pour activer Defender pour IoT sur Azure IoT Hub. Après activation, les insights relatifs à Defender pour IoT sont disponibles via la fonctionnalité **Sécurité** d'Azure IoT Hub et via **IoT** dans Azure Security Center.

## <a name="supported-service-regions"></a>Régions de service prises en charge

Defender pour IoT est actuellement pris en charge pour les hubs IoT des régions Azure suivantes :

- USA Centre
- USA Est
- USA Est 2
- Centre-USA Ouest
- USA Ouest
- USA Ouest 2
- USA Centre Sud
- Centre-Nord des États-Unis
- Centre du Canada
- Est du Canada
- Europe Nord
- Brésil Sud
- France Centre
- Ouest du Royaume-Uni
- Sud du Royaume-Uni
- Europe Ouest
- Europe Nord
- OuJapon Est
- Japon Est
- Sud-Australie Est
- Australie Est
- Asie Est
- Asie Sud-Est
- Centre de la Corée
- Corée du Sud
- Inde centrale
- Inde Sud

Defender pour IoT achemine le trafic de l'ensemble des régions européennes vers le centre de données régional Europe Ouest, tandis que le trafic des autres régions est acheminé vers le centre de données régional USA Centre.

## <a name="wheres-my-iot-hub"></a>Localiser son hub IoT

Vérifiez l’emplacement de votre hub IoT pour connaître la disponibilité du service avant de commencer.

1. Ouvrez votre hub IoT.
1. Cliquez sur **Overview**.
1. Vérifiez que l’endroit indiqué correspond à l’une des [régions de service prises en charge](#supported-service-regions).

## <a name="supported-platforms-for-agents"></a>Plateformes prises en charge pour les agents

Les agents Defender pour IoT gèrent une liste croissante d'appareils et de plateformes. Pour vérifier votre bibliothèque d’appareils actuelle ou planifiée, voir la [liste des plateformes prises en charge](how-to-deploy-agent.md).

## <a name="next-steps"></a>Étapes suivantes

- Consultez la [vue d’ensemble](overview.md) de la sécurité Azure IoT
- Découvrez comment [Activer ce service](quickstart-onboard-iot-hub.md)
- Consultez la [FAQ de Defender pour IoT](resources-frequently-asked-questions.md)
- Découvrez comment [comprendre les alertes Defender pour IoT](concept-security-alerts.md)
