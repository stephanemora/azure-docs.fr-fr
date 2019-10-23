---
title: Événements actifs et inactifs - Personalizer
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: aa6f53901f21dcb0726454d641a4a2a66007f9e0
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429039"
---
# <a name="active-and-inactive-events"></a>Événements actifs et inactifs

Lorsque votre application appelle l’API de classement, vous recevez l’action que l’application doit afficher dans le champ rewardActionId.  À partir de ce moment, Personalizer attend un appel de récompense avec le même ID d’événement. Le score de récompense sera utilisé pour former le modèle qui sera utilisé pour les appels de classement ultérieurs. Si aucun appel de récompense n’est reçu pour l’ID d’événement, une récompense par défaut sera appliquée. Les récompenses par défaut sont établies dans le portail Azure.

Dans certains cas, l’application peut avoir besoin de l’appel de classement avant de savoir si le résultat sera utilisé ou affiché pour l’utilisateur. Cela peut se produire dans les situations où, par exemple, le rendu de la page du contenu promu est remplacé par une campagne marketing. Si le résultat de l’appel de classement n’a jamais été utilisé et que l’utilisateur n’a jamais eu l’occasion de le voir, il serait incorrect de le former avec une récompense, zéro ou autre.
En général, cela se produit dans les cas suivants :

* Vous préaffichez une partie de l’interface utilisateur que l’utilisateur peut voir ou ne pas voir. 
* Votre application peut effectuer une personnalisation prédictive dans laquelle les appels d’API Rank sont effectués avec moins de contexte en temps réel, et leur sortie peut ou non être utilisée par l’application. 

Dans ce cas, la façon correcte d’utiliser Personalizer consiste à effectuer l’appel de classement en demandant à ce que l’événement soit _inactif_. Personalizer n’attend pas de récompense pour cet événement et n’applique pas non plus de récompense par défaut. Plus tard, dans votre logique métier, si l’application utilise les informations de l’appel de classement, il vous suffit _d’activer_ l’événement. À partir du moment où l’événement est actif, Personalizer s’attend à obtenir une récompense pour l’événement ou applique une récompense par défaut si aucun appel explicite n’est effectué pour l’API de récompense.

## <a name="get-inactive-events"></a>Obtenir les événements inactifs

Pour désactiver la formation pour un événement, faites un appel de classement avec `learningEnabled = False`.

La formation pour un événement inactif est implicitement activée si vous envoyez une récompense pour l’eventID ou appelez l’API `activate` pour cet eventID.

## <a name="learning-settings"></a>Paramètres d’apprentissage

Les paramètres d’apprentissage déterminent les *hyperparamètres* de l’entraînement des modèles. Deux modèles qui contiennent des données identiques, mais qui ont été entraînés à l’aide de paramètres d’apprentissage différents, seront différents au final.

### <a name="import-and-export-learning-policies"></a>Importer et exporter les stratégies d’apprentissage

Vous pouvez importer et exporter des fichiers de stratégie d’apprentissage à partir du portail Azure. Ainsi, vous pouvez enregistrer les stratégies existantes, les tester, les remplacer et les archiver dans votre contrôle de code source en tant qu’artefacts, pour référence ultérieure et pour de prochains audits.

### <a name="learning-policy-settings"></a>Paramètres des stratégies d’apprentissage

Dans **Stratégie d’apprentissage**, les paramètres ne sont pas censés être modifiés. Vous ne devez modifier les paramètres que si vous comprenez leur impact sur Personalizer. Sans cela, les modifications entraîneront des effets secondaires, comme des modèles Personalizer invalides.

### <a name="comparing-effectiveness-of-learning-policies"></a>Comparaison de l’efficacité des différentes stratégies d’apprentissage

Vous pouvez comparer les performances des différentes stratégies d’apprentissage par rapport à d’anciennes données de journaux Personalizer en effectuant une [évaluation hors connexion](concepts-offline-evaluation.md).

[Chargez vos propres stratégies d’apprentissage](how-to-offline-evaluation.md) pour les comparer à la stratégie d’apprentissage actuelle.

### <a name="discovery-of-optimized-learning-policies"></a>Découverte des stratégies d’apprentissage optimisées

Personalizer peut créer une stratégie d’apprentissage plus optimisée lors d’une [évaluation hors connexion](how-to-offline-evaluation.md). Une stratégie d’apprentissage plus optimisée, qui obtient de meilleures récompenses dans une évaluation hors connexion, obtiendra de meilleurs résultats lorsqu’elle est utilisée en ligne dans Personalizer.

Une fois que vous avez créé une stratégie d’apprentissage optimisée, vous pouvez l’appliquer directement à Personalizer afin qu’elle remplace immédiatement la stratégie actuelle, ou vous pouvez l’enregistrer en vue d’une évaluation ultérieure et décider de l’ignorer, de l’enregistrer ou de l’appliquer.
