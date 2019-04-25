---
title: Azure Security Center pour les composants requis de IoT Preview | Microsoft Docs
description: Détails de tous les éléments nécessaires pour bien démarrer avec Azure Security Center pour la configuration requise du service IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: f09d768c0c6c1d351f737b053da9fd3282867099
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60505340"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Azure Security Center, configuration requise pour l’IoT

> [!IMPORTANT]
> Azure Security Center pour IoT est disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Cet article fournit une explication des différents blocs de construction de l’Azure Security Center (ASC) IoT service, ce dont vous avez besoin commencer et concepts de base pour aider à comprendre le service. 

## <a name="minimum-requirements"></a>Configuration minimale requise

- Niveau Standard de IoT Hub
    - Rôle RBAC **propriétaire** privilèges de niveau 
- [Espace de travail log Analytique](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Azure Security Center (recommandé)
    - Tandis que l’utilisation d’Azure Security Center est uniquement une recommandation et n’est pas obligatoire, sans cela, il se peut que vous ne pourrez pas afficher vos autres ressources Azure dans IoT Hub. 
 
## <a name="working-with-asc-for-iot-service"></a>Utilisation de ASC pour le service IoT

ASC pour insights IoT et les rapports sont disponibles à l’aide d’Azure IoT Hub et Azure Security Center. Pour activer ASC pour IoT sur votre Azure IoT Hub, un compte avec **propriétaire** des privilèges de niveau est requise. Après avoir activé ASC pour IoT dans votre IoT Hub, ASC pour IoT insights sont affichées en tant que le **sécurité** dans Azure IoT Hub et en tant que fonctionnalité **IoT** dans Azure Security Center. 

## <a name="supported-service-regions"></a>Régions de service pris en charge 

ASC pour IoT est actuellement pris en charge pour les Hubs IoT dans les régions Azure suivantes :
  - USA Centre
  - Europe du Nord
  - Asie Sud-Est

## <a name="wheres-my-iot-hub"></a>Où est mon IoT Hub ?

Vérifiez l’emplacement de votre IoT Hub pour vérifier la disponibilité du service avant de commencer. 

1. Ouvrez votre hub IoT. 
2. Cliquez sur **Overview**. 
3. Vérifiez l’endroit indiqué correspond à l’un de le [service régions prises en charge](#supported-service-regions). 


## <a name="supported-platforms-for-agents"></a>Plateformes prises en charge pour les agents 

ASC pour les agents de IoT prend en charge une liste croissante de périphériques et plates-formes. Consultez le [pris en charge de la liste des plateformes](how-to-deploy-agent.md) pour vérifier votre bibliothèque de périphériques existant ou planifié.  

## <a name="next-steps"></a>Étapes suivantes
- [Vue d'ensemble](overview.md)
- [Activer le service](quickstart-onboard-iot-hub.md)
- [ASC pour IoT Forum aux questions](resources-frequently-asked-questions.md)
- [Présentation de ASC pour les alertes de l’IoT](concept-security-alerts.md)
