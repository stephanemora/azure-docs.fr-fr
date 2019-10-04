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
ms.openlocfilehash: 3c42bf2b2acc2472741bd603ea9d653a314ecc40
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717261"
---
## <a name="create-a-speech-resource"></a>Créer une ressource Speech

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Cliquez sur [Créer une ressource **Speech**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)
1. Entrez tous les paramètres obligatoires :

    |Paramètre|Valeur|
    |--|--|
    |Nom|Nom de votre choix (2-64 caractères)|
    |Subscription|Sélectionner l’abonnement approprié|
    |Location|Sélectionnez n’importe quel emplacement disponible et proche|
    |Niveau de tarification|`F0` - le niveau tarifaire minimal|
    |Groupe de ressources|Sélectionner un groupe de ressources disponible|

1. Cliquez sur **Créer** et attendez que la ressource soit créée. Après sa création, accédez à la page de ressources
1. Collectez le `endpoint` configuré et une clé d’API :

    |Onglet Ressource dans le portail|Paramètre|Valeur|
    |--|--|--|
    |**Vue d'ensemble**|Point de terminaison|Copiez le point de terminaison. Il ressemble à `https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken`|
    |**Clés**|Clé de l’API|Copiez 1 des deux clés. Il s’agit d’une chaîne de 32 caractères alphanumériques sans espaces ni tirets, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
