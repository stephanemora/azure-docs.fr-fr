---
title: Limites du service
titleSuffix: Azure Machine Learning
description: Limites de service permettant de planifier la capacité et limites maximales des requêtes et réponses d’Azure Machine Learning.
services: machine-learning
author: andscho
ms.author: andscho
ms.reviewer: mldocs
ms.topic: reference
ms.service: machine-learning
ms.subservice: core
ms.date: 12/21/2020
ms.openlocfilehash: b675e72df4f128d0ce096b3ac398fab63c20557e
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97930801"
---
# <a name="service-limits-in-azure-machine-learning"></a>Limites de service dans Azure Machine Learning

Cette section répertorie les quotas de base et les seuils de limitation qui s’appliquent à Azure Machine Learning. Pour savoir comment augmenter les quotas de ressources, consultez [« Gérer et augmenter les quotas pour les ressources »](how-to-manage-quotas.md).

## <a name="workspaces"></a>Workspaces
| Limite | Valeur |
| --- | --- |
| Nom de l’espace de travail | 2-32 caractères |

## <a name="runs"></a>Exécutions
| Limite | Valeur |
| --- | --- |
| Exécutions par espace de travail | 10 millions |
| RunId/ParentRunId | 256 caractères |
| DataContainerId | 261 caractères |
| DisplayName |256 caractères|
| Description |5 000 caractères|
| Nombre de propriétés |50 |
| Longueur de la clé de propriété |100 caractères |
| Longueur de la valeur de propriété |1 000 caractères |
| Nombre d’étiquettes |50 |
| Longueur de la clé d’étiquette |100 |
| Longueur de la valeur d’étiquette |1 000 caractères |
| CancelUri / CompleteUri / DiagnosticsUri |1 000 caractères |
| Longueur du message d’erreur |3 000 caractères |
| Longueur du message d’avertissement |300 caractères |
| Nombre de jeux de données d’entrée |200 |
| Nombre de jeux de données de sortie |20 |


## <a name="metrics"></a>Mesures
| Limite | Valeur |
| --- | --- |
| Noms de métriques par exécution |50|
| Lignes de métriques par nom de métrique |10 millions|
| Colonnes par ligne de métrique |15|
| Longueur du nom de colonne de métrique |255 caractères |
| Longueur de la valeur de colonne de métrique |255 caractères |
| Lignes de métriques par lot chargées | 250 |

> [!NOTE]
> Si vous atteignez la limite de noms de métriques par exécution car vous formatez des variables dans le nom de la métrique, utilisez plutôt une métrique de ligne où une première colonne est la valeur de la variable et la seconde colonne la valeur de la métrique.

## <a name="artifacts"></a>Artifacts

| Limite | Valeur |
| --- | --- |
| Nombre d’artefacts par exécution |10 millions|
| Longueur maximale du chemin d’artefact |5 000 caractères |

## <a name="limit-increases"></a>Augmentations de limite
Certaines limites peuvent être augmentées pour des espaces de travail individuels en [contactant le support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/). 

## <a name="next-steps"></a>Étapes suivantes

- [Configurer votre environnement Azure Machine Learning](how-to-configure-environment.md)
- Découvrez comment augmenter les quotas de ressources dans [« Gérer et augmenter les quotas pour les ressources »](how-to-manage-quotas.md).

