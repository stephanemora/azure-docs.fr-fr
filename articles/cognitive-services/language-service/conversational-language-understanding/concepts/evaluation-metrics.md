---
title: Mesures d’évaluation du Language Understanding utilisé dans la conversation
titleSuffix: Azure Cognitive Services
description: En savoir plus sur les mesures d’évaluation du Language Understanding utilisé dans la conversation
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: b81273e3955ab866648a1d70f01991272ecba787
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096646"
---
# <a name="evaluation-metrics-for-conversational-language-understanding-models"></a>Mesures d’évaluation des modèles de Language Understanding utilisé dans la conversation

L’évaluation de modèle dans la compréhension du langage utilisé dans la conversation se sert des mesures suivantes :


|Metric |Description  |Calcul  |
|---------|---------|---------|
|Precision     |  Taux de reconnaissances réussies pour toutes les tentatives de reconnaissance. Cela indique combien de fois la reconnaissance d’entité du modèle est véritablement une bonne reconnaissance.       | `Precision = #True_Positive / (#True_Positive + #False_Positive)`        |
|Rappel     | Rapport entre les reconnaissances réussies et le nombre réel d’entités présentes.        | `Recall = #True_Positive / (#True_Positive + #False_Negatives)`        |
|Score F1    |  Combinaison entre précision et rappel.       |  `F1 Score = 2 * Precision * Recall / (Precision + Recall)`       |

## <a name="model-level-and-entity-level-evaluation-metrics"></a>Mesures d’évaluation au niveau du modèle et de l’entité

La précision, le rappel et le score F1 sont calculés séparément pour chaque entité (évaluation au niveau de l’entité) et collectivement, pour le modèle (évaluation au niveau du modèle).

Les définitions de la précision, du rappel et de l’évaluation sont les mêmes pour les évaluations au niveau de l’entité et au niveau du modèle. Toutefois, le nombre de *Vrais positifs*, de *Faux positifs* et de *Faux négatifs* peut différer. Imaginons, par exemple, le texte suivant.

### <a name="example"></a>Exemple

*La première partie dans ce contrat est John Smith, résidant au 5678 Main Rd., ville de Frederick, État du Nebraska. Et la deuxième partie est Forrest Ray, résidant au 123-345 Integer Rd., ville de Corona, État du Nouveau Mexique. Il y a également Fannie Thomas résidant au 7890 River Road, ville de Colorado Springs, État du Colorado.*

Le modèle qui extrait des entités de ce texte peut proposer les prédictions suivantes :

| Entité | Prédiction en tant que | Type réel |
|--|--|--|
| John Smith | Personne | Personne |
| Frederick | Personne | City |
| Forrest | City | Personne |
| Fannie Thomas | Personne | Personne |
| Colorado Springs | City | City |

### <a name="entity-level-evaluation-for-the-person-entity"></a>Évaluation au niveau de l’entité pour l’entité *Personne* 

Le modèle peut présenter l’évaluation suivante au niveau de l’entité, pour l’entité *Personne* :

| Clé | Count | Explication |
|--|--|--|
| Vrai positif | 2 | *John Smith* et *Fannie Thomas* ont été correctement prédits en tant que *Personne*. |
| Faux positif | 1 | *Frederick* a été prédit de manière incorrecte en tant que *Personne* alors qu’il s’agit d’une *Ville*. |
| Faux négatif | 1 | *Forrest* a été prédit de manière incorrecte en tant que *Ville* alors qu’il s’agit d’une *Personne*. |

* **Précision** : `#True_Positive / (#True_Positive + #False_Positive)` = `2 / (2 + 1) = 0.67`
* **Rappel** : `#True_Positive / (#True_Positive + #False_Negatives)` = `2 / (2 + 1) = 0.67`
* **Score F1** : `2 * Precision * Recall / (Precision + Recall)` = `(2 * 0.67 * 0.67) / (0.67 + 0.67) = 0.67`

### <a name="entity-level-evaluation-for-the-city-entity"></a>Évaluation au niveau de l’entité pour l’entité *Ville*

Le modèle peut présenter l’évaluation suivante au niveau de l’entité, pour l’entité *Ville* :

| Clé | Count | Explication |
|--|--|--|
| Vrai positif | 1 | *Colorado Springs* a été prédit correctement en tant que *Ville*. |
| Faux positif | 1 | *Forrest* a été prédit de manière incorrecte en tant que *Ville* alors qu’il s’agit d’une *Personne*. |
| Faux négatif | 1 | *Frederick* a été prédit de manière incorrecte en tant que *Personne* alors qu’il s’agit d’une *Ville*. |

* **Précision** = `#True_Positive / (#True_Positive + #False_Positive)` = `2 / (2 + 1) = 0.67`
* **Rappel** = `#True_Positive / (#True_Positive + #False_Negatives)` = `2 / (2 + 1) = 0.67`
* **Score F1** = `2 * Precision * Recall / (Precision + Recall)` =  `(2 * 0.67 * 0.67) / (0.67 + 0.67) = 0.67`

### <a name="model-level-evaluation-for-the-collective-model"></a>Évaluation au niveau du modèle pour le modèle collectif

Le modèle présente l’évaluation suivante pour le modèle dans son intégralité :

| Clé | Count | Explication |
|--|--|--|
| Vrai positif | 3 | *John Smith* et *Fannie Thomas* ont été correctement prédits en tant que *Personne*. *Colorado Springs* a été prédit correctement en tant que *Ville*. Il s’agit de la somme des vrais positifs pour toutes les entités. |
| Faux positif | 2 | *Forrest* a été prédit de manière incorrecte en tant que *Ville* alors qu’il s’agit d’une *Personne*. *Frederick* a été prédit de manière incorrecte en tant que *Personne* alors qu’il s’agit d’une *Ville*. Il s’agit de la somme des faux positifs pour toutes les entités. |
| Faux négatif | 2 | *Forrest* a été prédit de manière incorrecte en tant que *Ville* alors qu’il s’agit d’une *Personne*. *Frederick* a été prédit de manière incorrecte en tant que *Personne* alors qu’il s’agit d’une *Ville*. Il s’agit de la somme des faux négatifs pour toutes les entités. |

* **Précision** = `#True_Positive / (#True_Positive + #False_Positive)` = `3 / (3 + 2) = 0.6`
* **Rappel** = `#True_Positive / (#True_Positive + #False_Negatives)` = `3 / (3 + 2) = 0.6`
* **Score F1** = `2 * Precision * Recall / (Precision + Recall)` =  `(2 * 0.6 * 0.6) / (0.6 + 0.6) = 0.6`

## <a name="interpreting-entity-level-evaluation-metrics"></a>Interprétation des mesures d’évaluation au niveau de l’entité

Ainsi, que signifie réellement avoir une haute précision ou un rappel élevé pour une certaine entité ?

| Rappel | Precision | Interprétation |
|--|--|--|
| Élevé | Élevé | Cette entité est correctement gérée par le modèle. |
| Faible | Élevé | Le modèle ne peut pas toujours extraire cette entité, mais dans ce cas, elle est avec un niveau de confiance élevé. |
| Élevé | Faible | Le modèle extrait cette entité correctement. Toutefois, elle présente un faible taux de confiance, car elle est parfois extraite comme un autre type. |
| Faible | Faible | Ce type d’entité est mal géré par le modèle, car il n’est généralement pas extrait. Lorsque c’est le cas, il ne présente pas un niveau de confiance élevé. |

## <a name="confusion-matrix"></a>Matrice de confusion

Une matrice de confusion est une matrice N x N utilisée pour l’évaluation du niveau de performance du modèle, où N est le nombre d’entités.
La matrice compare les balises réelles aux balises prédites par le modèle.
Cela donne une vue holistique de l’efficacité du modèle et des types d’erreurs qu’il effectue.

Vous pouvez utiliser la matrice de confusion pour identifier les entités qui sont trop proches les unes des autres et qui sont souvent erronées (ambiguïté). Dans ce cas, envisagez de fusionner ces types d’entités ensemble. Si cela n’est pas possible, envisagez d’ajouter plus d’exemples balisés des deux entités pour aider le modèle à les différencier.

La diagonale en surbrillance dans l’image ci-dessous correspond aux entités prédites correctement, où la balise prédite est la même que la balise réelle.

:::image type="content" source="../media/confusion-matrix-example.png" alt-text="Capture d’écran d’un exemple de matrice de confusion" lightbox="../media/confusion-matrix-example.png":::

Vous pouvez calculer les mesures d’évaluation au niveau de l’entité et au niveau du modèle à partir de la matrice de confusion :

* Les valeurs de la diagonale sont les valeurs de *Vrais positifs* de chaque entité.
* La somme des valeurs dans les lignes d’entité (à l’exception de la diagonale) est le *faux positif* du modèle.
* La somme des valeurs dans les colonnes d’entité (à l’exception de la diagonale) est le *faux négatif* du modèle.

De même,

* Le *vrai positif* du modèle est la somme des *vrais positifs* pour toutes les entités.
* Le *faux positif* du modèle est la somme des *faux positifs* pour toutes les entités.
* Le *faux négatif* du modèle est la somme des *faux négatifs* pour toutes les entités.

## <a name="next-steps"></a>Étapes suivantes

[Effectuer l'apprentissage d’un modèle dans Language Studio](../how-to/train-model.md)
