---
title: Prérequis d’Azure Security Center pour IoT (préversion) | Microsoft Docs
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
ms.date: 07/28/2019
ms.author: mlottner
ms.openlocfilehash: cc2dc3a190e3ad06bdc048f2a5770eae2a6990ec
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596816"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Prérequis d’Azure Security Center pour l’IoT

Cet article fournit des explications sur les différents blocs de construction du service Azure Security Center (ASC) IoT, des éléments nécessaires pour commencer et des concepts de base pour bien comprendre le service. 

## <a name="minimum-requirements"></a>Configuration minimale requise

- IoT Hub niveau Standard
    - Privilèges du niveau du rôle RBAC **Propriétaire** 
- [Espace de travail Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Azure Security Center (recommandé)
    - Il est recommandé, mais non obligatoire, d’utiliser Azure Security Center. Sans Azure Security Center, vous ne pourriez pas afficher vos autres ressources Azure dans le IoT Hub. 
 
## <a name="working-with-asc-for-iot-service"></a>Utiliser le service ASC pour l’IoT

Les insights et le reporting d’ASC pour l’IoT sont disponibles sur Azure IoT Hub et Azure Security Center. Un compte avec des privilèges de niveau **Propriétaire** est nécessaire pour activer ASC pour l’IoT sur Azure IoT Hub. Après activation, les insights d’ASC pour l’IoT s’affichent dans la fonctionnalité **Sécurité** d’Azure IoT Hub et **IoT** dans Azure Security Center. 

## <a name="supported-service-regions"></a>Régions de service prises en charge 

ASC pour l’IoT est actuellement pris en charge pour les hubs IoT des régions Azure suivantes :
  - USA Centre  
  - East US 
  - USA Est 2
  - USA Centre-Ouest
  - USA Ouest
  - Ouest des États-Unis 2
  - USA Centre Sud
  - USA Centre Nord
  - Centre du Canada
  - Est du Canada 
  - Europe Nord    
  - Brésil Sud
  - France Centre  
  - Ouest du Royaume-Uni 
  - Sud du Royaume-Uni
  - Europe Ouest 
  - Europe Nord 
  - Japon Ouest  
  - Japon Est  
  - Australie Sud-Est
  - Australie Est
  - Asie Est   
  - Asie Sud-Est
  - Centre de la Corée
  - Corée du Sud 
  - Inde Centre
  - Inde Sud
  
## <a name="wheres-my-iot-hub"></a>Localiser son hub IoT

Vérifiez l’emplacement de votre hub IoT pour connaître la disponibilité du service avant de commencer. 

1. Ouvrez votre hub IoT. 
2. Cliquez sur **Overview**. 
3. Vérifiez que l’endroit indiqué correspond à l’une des [régions de service prises en charge](#supported-service-regions). 


## <a name="supported-platforms-for-agents"></a>Plateformes prises en charge pour les agents 

Les agents ASC pour l’IoT gèrent une liste croissante d’appareils et de plateformes. Pour vérifier votre bibliothèque d’appareils actuelle ou planifiée, voir la [liste des plateformes prises en charge](how-to-deploy-agent.md).  

## <a name="next-steps"></a>Étapes suivantes
- Consultez la [vue d’ensemble](overview.md) de la sécurité Azure IoT
- Découvrez comment [Activer ce service](quickstart-onboard-iot-hub.md)
- Consultez la [FAQ d’Azure Security Center pour IoT](resources-frequently-asked-questions.md)
- Découvrez comment [comprendre les alertes Azure Security Center pour IoT](concept-security-alerts.md)
