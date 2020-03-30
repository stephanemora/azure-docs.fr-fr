---
title: Support pour les conteneurs
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: b40f1833f08074cb0a8d45fe3afc6bac7cbac7f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73522803"
---
## <a name="create-an-anomaly-detector-resource"></a>Créer une ressource Détecteur d’anomalies

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Cliquez sur [Créer une ressource **Détecteur d'anomalies**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)
1. Entrez tous les paramètres obligatoires :

    |Paramètre|Valeur|
    |--|--|
    |Name|Nom de votre choix (2-64 caractères)|
    |Subscription|Sélectionner l’abonnement approprié|
    |Location|Sélectionnez n’importe quel emplacement disponible et proche|
    |Niveau de tarification|`F0` - le niveau tarifaire minimal|
    |Groupe de ressources|Sélectionner un groupe de ressources disponible|
    |Case de confirmation de préversion (obligatoire)|Indique si vous avez lu l’avis relatif à la **Préversion**|

1. Cliquez sur **Créer** et attendez que la ressource soit créée. Après sa création, accédez à la page de ressources
1. Collectez le `endpoint` configuré et une clé d’API :

    |Onglet Ressource dans le portail|Paramètre|Valeur|
    |--|--|--|
    |**Vue d'ensemble**|Point de terminaison|Copiez le point de terminaison. Il ressemble à `https://westus2.api.cognitive.microsoft.com/`|
    |**Clés**|Clé de l’API|Copiez 1 des deux clés. Il s’agit d’une chaîne de 32 caractères alphanumériques sans espaces ni tirets, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|



