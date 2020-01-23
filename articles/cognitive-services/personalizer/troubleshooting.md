---
title: Résolution des problèmes - Personalizer
titleSuffix: Azure Cognitive Services
description: Cet article contient des réponses aux questions fréquemment posées sur la résolution des problèmes de Personalizer.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: diberry
ms.openlocfilehash: 5aeda9abcebda50cf97e1473b458d8f1f9d15970
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832174"
---
# <a name="personalizer-troubleshooting"></a>Résolution des problèmes de Personalizer

Cet article contient des réponses aux questions fréquemment posées sur la résolution des problèmes de Personalizer.

## <a name="transaction-errors"></a>Erreurs des transactions

### <a name="i-get-an-http-429-too-many-requests-response-from-the-service-what-can-i-do"></a>J’obtiens une réponse HTTP 429 (Trop de demandes) du service. Que puis-je faire ?

Si vous avez choisi un niveau tarifaire gratuit lorsque vous avez créé l’instance Personalizer, vous avez une limite de quota sur le nombre de demandes de classement autorisées. Consultez votre taux d’appels d’API pour l’API Rank (dans le volet Métriques du portail Azure pour votre ressource Personalizer) et ajustez le niveau tarifaire (dans le volet Niveau tarifaire) si votre volume d’appels est supposé augmenter au-delà du seuil du niveau tarifaire choisi.

### <a name="im-getting-a-5xx-error-on-rank-or-reward-apis-what-should-i-do"></a>J’obtiens une erreur 5xx sur les API Rank ou Reward. Que dois-je faire ?

Ces problèmes doivent être transparents. S’ils persistent, contactez le support en sélectionnant **Nouvelle demande de support** dans la section **Support + dépannage** du portail Azure pour votre ressource Personalizer.


## <a name="learning-loop"></a>Boucle d’apprentissage

<!--

### How do I import a learning policy?


-->

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>La boucle d’apprentissage ne semble pas apprendre. Comment la corriger ?

La boucle d’apprentissage a besoin de quelques milliers d’appels Reward avant que les appels Rank n’établissent des priorités efficaces.

En cas de doute sur le comportement actuel de votre boucle d’apprentissage, exécutez une [évaluation hors connexion](concepts-offline-evaluation.md) et appliquez la stratégie d’apprentissage corrigée.

### <a name="i-keep-getting-rank-results-with-all-the-same-probabilities-for-all-items-how-do-i-know-personalizer-is-learning"></a>Je continue à obtenir des résultats de classement avec les mêmes probabilités pour tous les éléments. Comment savoir que Personalizer apprend ?

Personalizer retourne les mêmes probabilités dans un résultat d’API Rank lorsqu’il vient de démarrer et qu’il a un modèle _vide_, ou lorsque vous réinitialisez la boucle de Personalizer et que votre modèle est toujours dans la période de **Fréquence de mise à jour du modèle**.

Lorsque la nouvelle période de mise à jour commence, le modèle mis à jour est utilisé et vous voyez les probabilités changer.

### <a name="the-learning-loop-was-learning-but-seems-to-not-learn-anymore-and-the-quality-of-the-rank-results-isnt-that-good-what-should-i-do"></a>La boucle d’apprentissage apprenait, mais semble ne plus apprendre, et la qualité des résultats de classement n’est pas si bonne. Que dois-je faire ?

* Veillez à effectuer et appliquer une évaluation dans le portail Azure pour cette ressource Personalizer (boucle d’apprentissage).
* Assurez-vous que toutes les récompenses sont envoyées via l’API Reward et qu’elles sont traitées.

### <a name="how-do-i-know-that-the-learning-loop-is-getting-updated-regularly-and-is-used-to-score-my-data"></a>Comment savoir que la boucle d’apprentissage est mise à jour régulièrement et utilisée pour établir le score de mes données ?

Vous pouvez trouver l’heure de la dernière mise à jour du modèle dans la page **Paramètres de modèle et d’apprentissage** du portail Azure. Si vous voyez un ancien horodatage, c’est probablement parce que vous n’envoyez pas les appels Rank et Reward. Si le service n’a pas de données entrantes, il ne met pas à jour l’apprentissage. Si vous voyez que la boucle d’apprentissage n’est pas mise à jour assez souvent, vous pouvez modifier la **Fréquence de mise à jour du modèle** de la boucle.


## <a name="offline-evaluations"></a>Évaluations hors connexion

### <a name="an-offline-evaluations-feature-importance-returns-a-long-list-with-hundreds-or-thousands-of-items-what-happened"></a>L’importance d’une caractéristique d’une évaluation hors connexion retourne une longue liste avec des centaines ou des milliers d’éléments. Que s’est-il passé ?

C’est généralement dû aux horodateurs, aux ID d’utilisateur ou à d’autres caractéristiques très spécifiques qui y ont été envoyées.

### <a name="i-created-an-offline-evaluation-and-it-succeeded-almost-instantly-why-is-that-i-dont-see-any-results"></a>J’ai créé une évaluation hors connexion et celle-ci a réussi presque instantanément. Pourquoi ? Je ne vois aucun résultat.

L’évaluation hors connexion utilise les données de modèles entraînés des événements de cette période. Si vous n’avez pas envoyé de données pendant la période entre l’heure de début et l’heure de fin de l’évaluation, celle-ci ne produit aucun résultat. Procédez à une nouvelle évaluation hors connexion en sélectionnant une plage horaire avec les événements dont vous savez qu’ils ont été envoyés à Personalizer.

## <a name="learning-policy"></a>Stratégie d’apprentissage

### <a name="how-do-i-import-a-learning-policy"></a>Comment importer une stratégie d’apprentissage ?

Découvrez les [concepts de la stratégie d’apprentissage](concept-active-learning.md#understand-learning-policy-settings) et [comment appliquer](how-to-learning-policy.md) une nouvelle stratégie d’apprentissage. Si vous ne voulez pas sélectionner une stratégie d’apprentissage, vous pouvez utiliser l’[évaluation hors connexion](how-to-offline-evaluation.md) pour suggérer une stratégie d’apprentissage en fonction de vos événements actuels.


## <a name="security"></a>Sécurité

### <a name="the-api-key-for-my-loop-has-been-compromised-what-can-i-do"></a>La clé API de ma boucle a été compromise. Que puis-je faire ?

Vous pouvez régénérer une clé après avoir demandé à vos clients de changer et d’utiliser l’autre clé. Le fait de disposer de deux clés vous permet de propager la clé de manière différée sans avoir besoin de temps d’arrêt. Nous vous recommandons de le faire au cours d’un cycle normal comme mesure de sécurité.


## <a name="next-steps"></a>Étapes suivantes

[Configurer la fréquence de mise à jour du modèle](how-to-settings.md#model-update-frequency)