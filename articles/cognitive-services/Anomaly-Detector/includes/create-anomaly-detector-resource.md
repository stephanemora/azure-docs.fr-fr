---
title: Support pour les conteneurs
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: e896ac5f4625d36060d713d66fa885f8b24756f1
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014735"
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

1. Cliquez sur **Créer** et attendez que la ressource soit créée. Après sa création, accédez à la page de ressources
1. Collectez le `endpoint` configuré et une clé d’API :

    |Onglet Ressource dans le portail|Paramètre|Valeur|
    |--|--|--|
    |**Vue d'ensemble**|Point de terminaison|Copiez le point de terminaison. Il ressemble à `https://westus2.api.cognitive.microsoft.com/`|
    |**Clés**|Clé de l’API|Copiez 1 des deux clés. Il s’agit d’une chaîne de 32 caractères alphanumériques sans espaces ni tirets, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|



