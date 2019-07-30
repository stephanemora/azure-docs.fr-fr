---
title: Créer une ressource Analyse de texte Cognitive Services
titleSuffix: Azure Cognitive Services
description: Découvrez comment créer une ressource Analyse de texte Cognitive Services.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 7e8b4480911f00afa8524ef4b81d697bb8ee5bcc
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877469"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Créer une ressource Analyse de texte Cognitive Services

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Sélectionnez **Créer une ressource**, puis accédez à **IA + Machine Learning** > **Analyse de texte**.
   Ou accédez à [Créer des analyses de texte](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Entrez tous les paramètres obligatoires :

    |Paramètre|Valeur|
    |--|--|
    |Nom|Entrez un nom (entre 2 et 64 caractères)|
    |Subscription|Sélectionnez l’abonnement approprié|
    |Location|Sélectionner un emplacement proche|
    |Niveau tarifaire| Entrez **S**, le niveau tarifaire standard|
    |Resource group|Sélectionner un groupe de ressources disponible|

1. Sélectionnez **Créer** et attendez que la ressource soit créée. Votre navigateur vous redirige automatiquement vers la page de la ressource créée.
1. Collectez `endpoint` configuré et une clé d’API :

    |Onglet Ressource dans le portail|Paramètre|Valeur|
    |--|--|--|
    |**Vue d'ensemble**|Point de terminaison|Copiez le point de terminaison. Il ressemble à `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |**Clés**|Clé de l’API|Copiez l’une des deux clés. Il s’agit d’une chaîne de 32 caractères alphanumériques sans espaces ni tirets, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
