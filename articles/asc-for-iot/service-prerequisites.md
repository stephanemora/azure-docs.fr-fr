---
title: Prérequis d’Azure Security Center pour l’IoT (préversion) | Microsoft Docs
description: Tout ce qu’il faut pour bien démarrer avec les prérequis du service Azure Security Center pour l’IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 198459887ff19b16e897b2a8dde55bca1217c8ac
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616430"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Prérequis d’Azure Security Center pour l’IoT

> [!IMPORTANT]
> Azure Security Center pour IoT est disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article donne une explication des différents blocs de construction du service Azure Security Center (ASC) pour l’IoT, des éléments nécessaires pour commencer et des concepts de base pour bien comprendre le service. 

## <a name="minimum-requirements"></a>Configuration minimale requise

- IoT Hub niveau Standard
    - Privilèges du niveau du rôle RBAC **Propriétaire** 
- [Espace de travail Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Azure Security Center (recommandé)
    - Il est recommandé, mais non obligatoire, d’utiliser Azure Security Center ; cependant, si vous vous en passez, vous risquez de ne pas pouvoir afficher vos autres ressources Azure dans IoT Hub. 
 
## <a name="working-with-asc-for-iot-service"></a>Utiliser le service ASC pour l’IoT

Les insights et le reporting d’ASC pour l’IoT sont disponibles sur Azure IoT Hub et Azure Security Center. Un compte avec des privilèges de niveau **Propriétaire** est nécessaire pour activer ASC pour l’IoT sur Azure IoT Hub. Après activation, les insights d’ASC pour l’IoT s’affichent dans la fonctionnalité **Sécurité** d’Azure IoT Hub et **IoT** dans Azure Security Center. 

## <a name="supported-service-regions"></a>Régions de service prises en charge 

ASC pour l’IoT est actuellement pris en charge pour les hubs IoT des régions Azure suivantes :
  - USA Centre
  - Europe Nord
  - Asie Sud-Est

## <a name="wheres-my-iot-hub"></a>Localiser son hub IoT

Vérifiez l’emplacement de votre hub IoT pour connaître la disponibilité du service avant de commencer. 

1. Ouvrez votre hub IoT. 
2. Cliquez sur **Overview**. 
3. Vérifiez que l’endroit indiqué correspond à l’une des [régions de service prises en charge](#supported-service-regions). 


## <a name="supported-platforms-for-agents"></a>Plateformes prises en charge pour les agents 

Les agents ASC pour l’IoT gèrent une liste croissante d’appareils et de plateformes. Pour vérifier votre bibliothèque d’appareils actuelle ou planifiée, voir la [liste des plateformes prises en charge](how-to-deploy-agent.md).  

## <a name="next-steps"></a>Étapes suivantes
- [Vue d'ensemble](overview.md)
- [Activer le service](quickstart-onboard-iot-hub.md)
- [FAQ ASC pour l’IoT](resources-frequently-asked-questions.md)
- [Comprendre les alertes ASC pour l’IoT](concept-security-alerts.md)
