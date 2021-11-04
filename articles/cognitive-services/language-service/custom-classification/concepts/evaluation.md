---
title: Métriques d’évaluation de la classification personnalisée
titleSuffix: Azure Cognitive Services
description: Découvrez les métriques d’évaluation dans l’extraction d’entité personnalisée.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 53765ebe1862b4c46a1b4ad8caf75f64f6203e37
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096828"
---
# <a name="evaluation-metrics"></a>Mesures d’évaluation

Votre [jeu de données est divisé](../how-to/train-model.md#data-splits) en deux parties : un jeu pour l’entraînement et un jeu pour le test. Le jeu d’entraînement est utilisé pour créer le modèle et le jeu de test sert de jeu témoin pour évaluer les performances du modèle après l’entraînement.

L’évaluation du modèle est déclenchée une fois l’entraînement effectué. Le processus d’évaluation commence en utilisant le modèle entraîné afin de prédire les classes définies par l’utilisateur pour les fichiers du jeu de test, et les compare aux étiquettes de données fournies (ce qui établit une base de référence de confiance). Les résultats sont retournés pour pouvoir examiner les performances du modèle. Pour l’évaluation, la classification de texte personnalisée utilise les métriques suivantes :

* **Précision** : mesure la précision/justesse de votre modèle. Il s’agit du rapport entre les positifs correctement identifiés (vrais positifs) et tous les positifs identifiés. La métrique de précision révèle le nombre de classes prédites qui sont correctement étiquetées. 

    `Precision = #True_Positive / (#True_Positive + #False_Positive)`

* **Rappel** : mesure la capacité du modèle à prédire les classes positives réelles. Il s’agit du rapport entre les vrais positifs prédits et ce qui a été réellement étiqueté. La métrique de rappel révèle le nombre de classes prédites correctes.

    `Recall = #True_Positive / (#True_Positive + #False_Negatives)`

* **Score F1** : le score F1 est une fonction de précision et de rappel. Il est nécessaire quand vous recherchez l’équilibre entre précision et rappel.

    `F1 Score = 2 * Precision * Recall / (Precision + Recall)` <br> 

>[!NOTE]
> La précision, le rappel et le score F1 sont calculés séparément pour chaque classe (évaluation au *niveau de la classe*) et collectivement pour le modèle (évaluation au *niveau du modèle*).

## <a name="model-level-and-class-level-evaluation-metrics"></a>Métriques d’évaluation au niveau du modèle et au niveau de la classe

Les définitions de la précision, du rappel et de l’évaluation sont les mêmes pour les évaluations au niveau de la classe et au niveau du modèle. Toutefois, le nombre de *vrais positifs*, de *faux positifs* et de *faux négatifs* diffère, comme indiqué dans l’exemple suivant.

Les sections ci-dessous utilisent l’exemple de jeu de données suivant :

| Fichier | Classes réelles | Classes prédites |
|--|--|--|
| 1 | action, comedy | comedy|
| 2 | action | action |
| 3 | romance | romance |
| 4 | romance, comedy | romance |
| 5 | comedy | action |

### <a name="class-level-evaluation-for-the-action-class"></a>Évaluation au niveau de la classe pour la classe *action* 

| Clé | Count | Explication |
|--|--|--|
| Vrai positif | 1 | Le fichier 2 a été correctement classé dans *action*. |
| Faux positif | 1 | Le fichier 5 a été classé par erreur dans *action*. |
| Faux négatif | 1 | Le fichier 1 n’a pas été classé dans *action*, mais aurait dû l’être. |

**Précision** = `#True_Positive / (#True_Positive + #False_Positive) = 1 / (1 + 1) = 0.5`

**Rappel** = `#True_Positive / (#True_Positive + #False_Negatives) = 1 / (1 + 1) = 0.5`

**Score F1** = `2 * Precision * Recall / (Precision + Recall) =  (2 * 0.5 * 0.5) / (0.5 + 0.5) = 0.5`

### <a name="class-level-evaluation-for-the-comedy-class"></a>Évaluation au niveau de la classe pour la classe *comedy* 

| Clé | Count | Explication |
|--|--|--|
| Vrai positif | 1 | Le fichier 1 a été correctement classé dans *comedy*. |
| Faux positif | 0 | Aucun fichier n’a été classé par erreur dans *comedy*. |
| Faux négatif | 2 | Les fichiers 5 et 4 n’ont pas été classés dans *comedy*, mais ils auraient dû l’être. |

**Précision** = `#True_Positive / (#True_Positive + #False_Positive) = 1 / (1 + 0) = 1`

**Rappel** = `#True_Positive / (#True_Positive + #False_Negatives) = 1 / (1 + 2) = 0.67`

**Score F1** = `2 * Precision * Recall / (Precision + Recall) =  (2 * 1 * 0.67) / (1 + 0.67) = 0.8`

### <a name="model-level-evaluation-for-the-collective-model"></a>Évaluation au niveau du modèle pour le modèle collectif

| Clé | Count | Explication |
|--|--|--|
| Vrai positif | 4 | La prédiction pour les fichiers 1, 2, 3 et 4 a donné des classes correctes. |
| Faux positif | 1 | La prédiction pour le fichier 5 a donné une classe incorrecte. |
| Faux négatif | 2 | La prédiction pour les fichiers 1 et 4 n’a pas donné toutes les classes correctes. |

**Précision** = `#True_Positive / (#True_Positive + #False_Positive) = 4 / (4 + 1) = 0.8`

**Rappel** = `#True_Positive / (#True_Positive + #False_Negatives) = 4 / (4 + 2) = 0.67`

**Score F1** = `2 * Precision * Recall / (Precision + Recall) =  (2 * 0.8 * 0.67) / (0.8 + 0.67) = 0.12`

> [!NOTE] 
> Pour les modèles de classification à une seule étiquette, le nombre de faux négatifs et de faux positifs est toujours égal. Les modèles de classification à une seule étiquette personnalisée prédisent toujours une classe pour chaque fichier. Si la prédiction n’est pas correcte, le nombre de faux positifs de la classe prédite augmente de 1 et celui des faux négatifs de la classe réelle augmente de 1, le nombre total de faux positifs et de faux négatifs pour le modèle est toujours égal. Ce n’est pas le cas de la classification multi-étiquette, car l’échec de la prédiction de l’une des classes d’un fichier est considéré comme un faux négatif. 

## <a name="interpreting-class-level-evaluation-metrics"></a>Interprétation des métriques d’évaluation au niveau de la classe

Que signifie réellement avoir une précision élevée ou un rappel élevé pour une classe donnée ?

| Rappel | Precision | Interprétation |
|--|--|--|
| Élevé | Élevé | Cette classe est parfaitement gérée par le modèle. |
| Faible | Élevé | Le modèle ne peut pas toujours prédire cette classe, mais quand il le fait, le niveau de confiance est élevé. Cette classe est peut-être sous-représentée dans le jeu de données : essayez d’équilibrer la répartition de vos données.|
| Élevé | Faible | Le modèle prédit cette classe correctement, mais son niveau de confiance est faible. Cette classe est peut-être sur-représentée dans le jeu de données : essayez d’équilibrer la répartition de vos données. |
| Faible | Faible | Cette classe est mal gérée par le modèle où elle n’est généralement pas prédite et, quand elle l’est, le niveau de confiance n’est pas élevé. |

Les modèles de classification personnalisée sont censés avoir à la fois des faux négatifs et des faux positifs. Pensez à la façon dont ces faux négatifs et positifs affectent l’ensemble du système, et réfléchissez soigneusement aux scénarios dans lesquels le modèle ignore les prédictions correctes et reconnaît les prédictions incorrectes. Selon votre scénario, la *précision* ou le *rappel* peuvent être plus appropriés pour évaluer les performances de votre modèle.  

Par exemple, si votre scénario implique le traitement de tickets de support technique, la prédiction de la mauvaise classe peut entraîner le transfert d’un ticket au mauvais service ou à la mauvaise équipe. Dans cet exemple, vous devez rendre votre système plus sensible aux faux positifs et valoriser la précision dans l’évaluation. 

Autre exemple : si votre scénario implique de catégoriser les e-mails dans « *important* » ou « *spam* », une mauvaise prédiction peut vous faire manquer un e-mail utile s’il est étiqueté comme « *spam* ». Toutefois, si un e-mail de spam est étiqueté comme *important*, vous pouvez simplement l’ignorer. Dans cet exemple, vous devez rendre votre système plus sensible aux faux négatifs et valoriser le rappel dans l’évaluation. 

Si vous voulez optimiser votre modèle pour des scénarios d’usage général, ou quand la précision et le rappel sont tous deux importants, vous pouvez utiliser le score F1. Les scores d’évaluation dépendent de votre scénario et de vos critères d’acceptation. Il n’y a pas de métrique absolue qui fonctionne pour tous les scénarios. 

## <a name="see-also"></a>Voir aussi

* [Visualiser l’évaluation du modèle](../how-to/view-model-evaluation.md)
* [Entraîner un modèle](../how-to/train-model.md)
