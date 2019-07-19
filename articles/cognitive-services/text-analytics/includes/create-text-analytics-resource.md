---
title: Support pour les conteneurs
titleSuffix: Azure Cognitive Services
description: Découvrez comment créer une ressource Analyse de texte Cognitive Services.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 022ffcd806d4d4f89f8a8cf256a541518ea12602
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455078"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Créer une ressource Analyse de texte Cognitive Services

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez **+ Créer une ressource**, accédez à **IA + Machine Learning > Analyse de texte**, ou cliquez sur [Créer **Analytise de texte**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)
1. Entrez tous les paramètres obligatoires :

    |Paramètre|Valeur|
    |--|--|
    |Nom|Nom de votre choix (2-64 caractères)|
    |Abonnement|Sélectionne l’abonnement approprié|
    |Location|Sélectionner un emplacement proche|
    |Niveau de tarification|`S` : niveau tarifaire standard|
    |Groupe de ressources|Sélectionner un groupe de ressources disponible|

1. Cliquez sur **Créer** et attendez que la ressource soit créée. Lorsqu’elle est créée, votre navigateur vous redirige automatiquement vers la page de la nouvelle ressource
1. Collecter le `endpoint` configuré et une clé API :

    |Onglet Ressource dans le portail|Paramètre|Valeur|
    |--|--|--|
    |**Vue d'ensemble**|Point de terminaison|Copiez le point de terminaison. Il ressemble à `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |**Clés**|Clé de l’API|Copiez 1 des deux clés. Il s’agit d’une chaîne de 32 caractères alphanumériques sans espaces ni tirets, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|