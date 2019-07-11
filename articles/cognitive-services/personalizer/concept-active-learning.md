---
title: Apprentissage actif - Personalizer
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: edjez
ms.openlocfilehash: c44afc81a7ec9d05cdc04cc8bc46c77cd51ceaf8
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722527"
---
# <a name="active-learning-and-learning-policies"></a>Apprentissage actif et stratégies d’apprentissage 

Lorsque votre application appelle l’API Rank, vous recevez un classement pour le contenu. La logique métier peut utiliser ce rang pour déterminer si le contenu doit être montré à l’utilisateur. Lorsque vous affichez le contenu classé, il s’agit d’un événement de classement _actif_. Lorsque votre application n’affiche pas le contenu classé, il s’agit d’un événement de classement _inactif_. 

Des informations sur les événements de classement actifs sont retournées à Personalizer. Ces informations sont utilisées pour continuer l’entraînement du modèle via la stratégie d’apprentissage actuelle.

## <a name="active-events"></a>Événements actifs

Les événements actifs doivent toujours être montrés à l’utilisateur, et l’appel de récompense doit être retourné afin de fermer la boucle d’apprentissage. 

### <a name="inactive-events"></a>Événements inactifs 

Les événements inactifs ne doivent pas modifier le modèle sous-jacent parce que l’utilisateur n’a pas eu la possibilité de choisir le contenu classé.

## <a name="dont-train-with-inactive-rank-events"></a>N’utilisez pas les événements de classement inactifs pour l’entraînement 

Pour certaines applications, vous devrez peut-être appeler l’API Rank sans savoir encore si votre application montrera les résultats à l’utilisateur. 

Cela se produit dans les situations suivantes :

* Vous préaffichez une partie de l’interface utilisateur que l’utilisateur peut voir ou ne pas voir. 
* Votre application peut effectuer une personnalisation prédictive dans laquelle les appels d’API Rank sont effectués avec moins de contexte en temps réel, et leur sortie peut ou non être utilisée par l’application. 

### <a name="disable-active-learning-for-inactive-rank-events-during-rank-call"></a>Désactiver l’apprentissage actif pour les événements de classement inactifs pendant un appel Rank

Pour désactiver l’apprentissage automatique, appelez Rank avec `learningEnabled = False`.

L’apprentissage d’un événement inactif est implicitement activé si vous envoyez une récompense pour le classement.

## <a name="learning-policies"></a>Stratégies d’apprentissage

Une stratégie d’apprentissage détermine les *hyperparamètres* de l’entraînement des modèles. Deux modèles qui contiennent des données identiques, mais qui ont été entraînés à l’aide de stratégies d’apprentissage différentes, auront un comportement différent.

### <a name="importing-and-exporting-learning-policies"></a>Importation et exportation de stratégies d’apprentissage

Vous pouvez importer et exporter des fichiers de stratégie d’apprentissage à partir du portail Azure. Ainsi, vous pouvez enregistrer les stratégies existantes, les tester, les remplacer et les archiver dans votre contrôle de code source en tant qu’artefacts, pour référence ultérieure et pour de prochains audits.

### <a name="learning-policy-settings"></a>Paramètres des stratégies d’apprentissage

Dans **Stratégie d’apprentissage**, les paramètres ne sont pas censés être modifiés. Vous ne devez modifier les paramètres que si vous comprenez leur impact sur Personalizer. Sans cela, les modifications entraîneront des effets secondaires, comme des modèles Personalizer invalides.

### <a name="comparing-effectiveness-of-learning-policies"></a>Comparaison de l’efficacité des différentes stratégies d’apprentissage

Vous pouvez comparer les performances des différentes stratégies d’apprentissage par rapport à d’anciennes données de journaux Personalizer en effectuant une [évaluation hors connexion](concepts-offline-evaluation.md).

[Chargez vos propres stratégies d’apprentissage](how-to-offline-evaluation.md) pour les comparer à la stratégie d’apprentissage actuelle.

### <a name="discovery-of-optimized-learning-policies"></a>Découverte des stratégies d’apprentissage optimisées

Personalizer peut créer une stratégie d’apprentissage plus optimisée lors d’une [évaluation hors connexion](how-to-offline-evaluation.md). Une stratégie d’apprentissage plus optimisée, qui obtient de meilleures récompenses dans une évaluation hors connexion, obtiendra de meilleurs résultats lorsqu’elle est utilisée en ligne dans Personalizer.

Une fois que vous avez créé une stratégie d’apprentissage optimisée, vous pouvez l’appliquer directement à Personalizer afin qu’elle remplace immédiatement la stratégie actuelle, ou vous pouvez l’enregistrer en vue d’une évaluation ultérieure et décider de l’ignorer, de l’enregistrer ou de l’appliquer.