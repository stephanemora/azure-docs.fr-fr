---
title: Comment améliorer les performances du modèle de Reconnaissance d’entités nommées (NER) personnalisées
titleSuffix: Azure Cognitive Services
description: En savoir plus sur l’amélioration d’un modèle pour la reconnaissance d’entités nommées (NER) personnalisées.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 5ae65097fbd4e7bdc9f4e602a64bc392e45ef75e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096703"
---
# <a name="improve-the-performance-of-custom-named-entity-recognition-ner-models"></a>Améliorer les performances des modèles de Reconnaissance d’entités nommées (NER) personnalisées 

Une fois que vous avez entraîné votre modèle et que vous avez examiné les détails de son évaluation, vous pouvez commencer à améliorer ses performances. Dans cet article, vous passez en revue les incohérences entre les classes prédites et les classes étiquetées par le modèle, et examinez la répartition des données.

## <a name="prerequisites"></a>Prérequis

* Un [projet créé](create-project.md) correctement avec un compte de stockage Blob Azure configuré
    * Des données textuelles qui [ont été chargées](create-project.md#prepare-training-data) dans votre compte de stockage.
* Des [données étiquetées](tag-data.md)
* Un [modèle correctement entraîné](train-model.md)
* Avoir passé en revue les [détails de l’évaluation du modèle](view-model-evaluation.md) pour déterminer les performances de votre modèle.
    * Vous être familiarisé avec les [métriques d’évaluation](../concepts/evaluation-metrics.md) utilisées pour l’évaluation.

Pour plus d’informations, consultez le [cycle de vie du développement d’applications](../overview.md#application-development-lifecycle).


## <a name="improve-model"></a>Améliorer le modèle

Une fois que vous avez revu l’[évaluation de votre modèle](view-model-evaluation.md), vous avez une idée de ce qui ne va pas dans la prédiction de votre modèle. 

> [!NOTE]
> Ce guide se concentre sur les données du [jeu de validation](train-model.md#data-split) créé pendant l’entraînement.

### <a name="review-validation-set"></a>Vérifier le jeu de validation

En utilisant Language Studio, vous pouvez examiner les performances de votre modèle et les comparer aux performances attendues. Vous pouvez passer en revue les classes prédites et les classes étiquetées de chaque modèle que vous avez entraîné.

1. Accédez à la page de votre projet dans [Langage Studio](https://aka.ms/languageStudio).
    1. Recherchez la section de Language Studio intitulée **Classifier du texte**.
    2. Sélectionnez **Classification de texte personnalisée**. 

2. Dans le menu de gauche, sélectionnez **Améliorer le modèle**.

3. Sélectionnez **Vérifier le jeu de validation**.

4. Sélectionnez votre modèle entraîné dans la liste déroulante **Modèle**.

5. Pour faciliter l’analyse, vous pouvez basculer sur **Montrer les prédictions incorrectes uniquement** et voir seulement les erreurs.

Utilisez les informations suivantes pour identifier les améliorations possibles du modèle. 

* Si l’entité `X` est constamment identifiée comme une entité `Y`, il y a une ambiguïté entre ces types d’entités et vous devez revoir votre schéma.

* Si une entité complexe n’est pas extraite à plusieurs reprises, décomposez-la en entités plus simples pour faciliter l’extraction.

* Si une entité est prédite alors qu’elle n’était pas étiquetée dans vos données, vous devez revoir vos étiquettes. Vérifiez que toutes les instances d’une entité sont correctement étiquetées dans tous les fichiers.

### <a name="examine-data-distribution"></a>Examiner la répartition des données

En examinant la répartition des données dans vos fichiers, vous pouvez déterminer si une classe est sous-représentée. Les données sont déséquilibrées quand les étiquettes ne sont pas réparties de manière égale entre vos entités, et ce déséquilibre risque d’impacter les performances de votre modèle. Par exemple, si l’*entité 1* contient 50 étiquettes alors que l’*entité 2* en a seulement 10, les données sont déséquilibrées, car l’*entité 1* est sur-représentée et l’*entité 2* est sous-représentée. Le modèle est biaisé, car il peut favoriser l’extraction de l’*entité 1* et négliger l’*entité 2*. Le déséquilibre des données peut entraîner des problèmes plus complexes si le schéma est ambigu. Si les deux entités sont similaires et que l’*entité 2* est sous-représentée, le modèle l’extrait probablement comme une *entité 1*.

Dans l’[évaluation du modèle](view-model-evaluation.md), les entités sur-représentées ont tendance à avoir un rappel plus élevé que les autres entités, tandis que sous les entités sous-représentées ont un rappel plus faible.

Pour examiner la répartition des données dans votre jeu de données :

1. Accédez à la page de votre projet dans [Langage Studio](https://aka.ms/languageStudio).
    1. Recherchez dans Language Studio la section intitulée **Extract information**.
    2. Sélectionnez **Extraction d’entités nommées personnalisées**. 
2. Dans le menu de gauche, sélectionnez **Améliorer le modèle**.

3. Sélectionnez **Examiner la répartition des données**.

## <a name="next-steps"></a>Étapes suivantes

Dès que vous êtes satisfait des performances de votre modèle, vous pouvez commencer à [envoyer des demandes d’extraction d’entités](call-api.md) en utilisant l’API d’exécution.
