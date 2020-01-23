---
title: Événements actifs et inactifs - Personalizer
titleSuffix: Azure Cognitive Services
description: Cet article traite de l’utilisation des événements actifs et inactifs, des paramètres d’apprentissage et des stratégies d’apprentissage au sein du service Personalizer.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 90658e030c907a9fd99dd8fb9a6e90698d72b1f0
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834473"
---
# <a name="active-and-inactive-events"></a>Événements actifs et inactifs

Lorsque votre application appelle l’API de classement, vous recevez l’action que l’application doit afficher dans le champ **rewardActionId**.  À partir de ce moment, Personalizer attend un appel de récompense portant le même ID d’événement. Le score de récompense sera utilisé pour former le modèle pour les appels de classement ultérieurs. Si aucun appel de récompense n’est reçu pour l’ID d’événement, une récompense par défaut est appliquée. Les récompenses par défaut sont définies dans le portail Azure.

Dans certains scénarios, l’application peut avoir besoin de l’appel de classement avant de savoir si le résultat sera utilisé ou affiché pour l’utilisateur. Cela peut se produire dans les situations où, par exemple, le rendu de la page du contenu promu est remplacé par une campagne marketing. Si le résultat de l’appel de classement n’a jamais été utilisé et que l’utilisateur ne l’a jamais vu, n’envoyez pas d’appel de récompense correspondant.

En règle générale, ces scénarios se présentent dans les cas suivants :

* Vous prérendez l’interface utilisateur que l’utilisateur peut voir ou pas.
* Votre application effectue une personnalisation prédictive dans laquelle les appels d’API de classement sont effectués avec moins de contexte en temps réel, et leur application peut ou non utiliser la sortie.

Dans ce cas, utilisez Personalizer pour effectuer l’appel de classement, en demandant à ce que l’événement soit _inactif_. Personalizer n’attend pas de récompense pour cet événement, et n’applique pas non plus de récompense par défaut.
Plus tard dans votre logique métier, si l’application utilise les informations de l’appel de classement, _activez_ tout simplement l’événement. Une fois l’événement actif, Personalizer attend une récompense d’événement. Si aucun appel explicite n’est effectué à destination de l'API de récompense, Personalizer applique une récompense par défaut.

## <a name="inactive-events"></a>Événements inactifs

Pour désactiver la formation pour un événement, procédez à un appel de classement à l'aide de `learningEnabled = False`. Pour un événement inactif, la formation est implicitement activée si vous envoyez une récompense pour l’eventId ou appelez l’API `activate` pour cet eventId.

## <a name="learning-settings"></a>Paramètres d’apprentissage

Les paramètres d’apprentissage déterminent les *hyperparamètres* d'apprentissage des modèles. Deux modèles contenant des données identiques, mais ayant été entraînés à l’aide de paramètres d’apprentissage différents, se révéleront différents.

### <a name="import-and-export-learning-policies"></a>Importer et exporter les stratégies d’apprentissage

Vous pouvez importer et exporter des fichiers de stratégie d’apprentissage à partir du portail Azure. Utilisez cette méthode pour enregistrer les stratégies existantes, les tester, les remplacer et les archiver dans votre contrôle de code source en tant qu’artefacts, à des fins de références et d'audits ultérieurs.

Découvrez [comment](how-to-learning-policy.md) importer et exporter une stratégie d’apprentissage.

### <a name="understand-learning-policy-settings"></a>Comprendre les paramètres des stratégies d’apprentissage

Les paramètres des stratégies d'apprentissage ne sont pas censés être modifiés. Pour les modifier, vous devez impérativement comprendre la manière dont ils affectent Personalizer. Si ce n'est pas le cas, vous risquez de provoquer des problèmes, notamment d’invalider des modèles Personalizer.

Personalizer utilise [vowpalwabbit](https://github.com/VowpalWabbit) pour entraîner et scorer les événements. Reportez-vous à la [documentation vowpalwabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) sur la façon de modifier les paramètres d’apprentissage avec vowpalwabbit. Une fois que vous avez les bons arguments de ligne de commande, enregistrez la commande dans un fichier au format suivant (remplacez la valeur de la propriété d’arguments par la commande souhaitée) et chargez le fichier pour importer les paramètres d’apprentissage dans le volet **Paramètres de modèle et d'apprentissage**  du portail Azure pour votre ressource Personalizer.

Le fichier `.json` suivant est un exemple de stratégie d’apprentissage.

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

### <a name="compare-learning-policies"></a>Comparer les stratégies d’apprentissage

Vous pouvez comparer les performances des différentes stratégies d’apprentissage par rapport à d’anciennes données de journaux Personalizer en procédant à des [évaluations hors connexion](concepts-offline-evaluation.md).

[Chargez vos propres stratégies d’apprentissage](how-to-learning-policy.md) pour les comparer à la stratégie d’apprentissage actuelle.

### <a name="optimize-learning-policies"></a>Optimiser les stratégies d’apprentissage

Personalizer peut créer une stratégie d’apprentissage optimisée dans une [évaluation hors connexion](how-to-offline-evaluation.md). Une stratégie d’apprentissage optimisée dotée de meilleures récompenses dans une évaluation hors connexion, obtiendra de meilleurs résultats si elle est utilisée en ligne dans Personalizer.

Après avoir optimisé une stratégie d'apprentissage, vous pouvez l’appliquer directement à Personalizer pour lui permettre de remplacement immédiatement la stratégie actuelle. Vous pouvez également enregistrer la stratégie optimisée pour une évaluation supplémentaire, puis décider ultérieurement de la supprimer, de l’enregistrer ou de l’appliquer.
