---
title: Tableau de bord - reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Résoudre les intentions avec le tableau de bord d’analytique, un outil de création de rapports visualisé.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/22/2019
ms.author: diberry
ms.openlocfilehash: 055d113a2bc77f8de1b4b881718007c869470532
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236944"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Comment utiliser le tableau de bord pour améliorer votre application

Recherchez et corrigez les problèmes avec des intentions de votre application formé lorsque vous utilisez des énoncés d’exemple. Le tableau de bord affiche des informations sur l’application globale, en soulignant intents qui devraient être corrigés. 

Passez en revue le tableau de bord analyse est un processus itératif, répété que vous modifiez et améliorez votre modèle.

Cette page n’aura pas une analyse appropriée pour les applications qui n’ont pas les énoncés qui exemple dans les intentions, connu sous le nom _modèle seule_ applications. 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Les problèmes peuvent être résolus à partir du tableau de bord ?

Les trois problèmes résolus dans le tableau de bord sont :

|Problème|Couleur de graphique|Explication|
|--|--|--|
|Déséquilibre de données|-|Cela se produit lorsque la quantité des énoncés d’exemple varie considérablement. Tous les intentions doivent _à peu près_ le même nombre d’énoncés d’exemple - sauf aucune intention. Il doit uniquement avoir 10 à 15 % de la quantité totale d’énoncés dans l’application.<br><br> Si les données sont déséquilibrées, mais la précision intent se situe au-dessus de certain seuil, ce déséquilibre n’est pas signalé comme un problème.<br><br>**Démarrer avec ce problème : il peut être la cause racine des autres problèmes.**|
|Prédictions incertaine|Orange|Cela se produit lorsque l’intention supérieure et les scores de l’objectif suivant sont suffisamment proches pour qu’ils peuvent retourner sur la formation suivante, en raison [l’échantillonnage négatif](luis-how-to-train.md#train-with-all-data) ou plus énoncés exemple ajoutés à l’intention. |
|Prédictions incorrectes|Rouge|Cela se produit lorsqu’un énoncé de l’exemple n’est pas prévue pour l’intention étiquetée (l’intention dans).|

Prédictions correctes sont représentées par la couleur bleue.

Le tableau de bord affiche ces problèmes et vous indique les intentions sont affectées et suggère la procédure à suivre pour améliorer l’application. 

## <a name="before-app-is-trained"></a>Avant de l’application est formée 

Avant de vous formez à l’application, le tableau de bord ne contient pas de vos suggestions concernant les correctifs. Apprentissage de votre application pour voir ces suggestions.  

## <a name="check-your-publishing-status"></a>Vérifier votre état de publication

Le **l’état de publication** carte contient des informations sur les actifs version de publication du dernier. 

Vérifiez que la version active est la version que vous souhaitez corriger. 

![Services externes de l’application de tableau de bord affiche, publié des régions et agrégée des présences dans le point de terminaison.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

Cela montre des régions publiées, les services externes et vous agrégées des présences dans le point de terminaison. 

## <a name="review-training-evaluation"></a>Passez en revue la version d’évaluation de la formation

Le **version d’évaluation de la formation** carte contient la synthèse agrégée de la précision globale de votre application par zone. Le score indique la qualité intentionnelle. 

![La carte d’évaluation de formation contient la première zone d’informations sur la précision globale de votre application.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

Le graphique indique les intentions prédites correctement et les domaines avec différentes couleurs. Comme vous améliorez l’application avec les suggestions, cela augmente de score. 

Les suggestions de correction sont séparés et répartis par type de problème et sont les plus importantes pour votre application. Si vous préférez examiner et résoudre les problèmes par intention, utilisez le **[intentions avec des erreurs](#intents-with-errors)** carte en bas de la page. 

Chaque domaine problématique a intents qui doivent être corrigées. Lorsque vous sélectionnez le nom d’intention, la **intention** page s’ouvre avec un filtre appliqué à énoncés. Ce filtre permet de vous concentrer sur les énoncés qui posent problème.

### <a name="compare-changes-across-versions"></a>Comparez les modifications entre les versions

Créer une nouvelle version avant d’apporter des modifications à l’application. Dans la nouvelle version, apportez les modifications suggérées à énoncés d’exemple de l’objectif, puis former à nouveau. Dans la page tableau de bord **version d’évaluation de la formation** de carte, utilisez le **Show diffère d’une version formée** de comparer les modifications. 

![Comparez les modifications entre les versions](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Corriger la version en ajoutant ou modifiant des énoncés d’exemple et reformation

La principale méthode de résolution de votre application sera à ajouter ou modifier des énoncés d’exemple et recycler. Énoncés nouveaux ou modifiés doivent suivre les instructions pour [variées énoncés](luis-concept-utterance.md).

Ajout des énoncés d’exemple doit être effectuée par une personne qui :

* a un degré élevé de compréhension des énoncés dans des intentions différentes
* sait comment énoncés dans un seul objectif peuvent être confondues avec un autre objectif
* est en mesure de décider si les deux modes, qui sont souvent confondues, doivent être rassemblées en une intention unique, et les différentes données extraites avec les entités

### <a name="patterns-and-phrase-lists"></a>Modèles et des listes d’expression

La page d’analytique n’indique pas quand utiliser [modèles](luis-concept-patterns.md) ou [une phrase listes](luis-concept-feature.md). Si vous les ajoutez, il peut aider aux prédictions incorrectes ou peu claire, mais sont inutiles avec un déséquilibre de données. 

### <a name="review-data-imbalance"></a>Déséquilibre de données de révision

Démarrer avec ce problème : il peut être la cause racine des autres problèmes.

Le **déséquilibre de données** intent liste montre les intentions dont ont besoin de plus énoncés pour corriger le déséquilibre de données. 

**Pour résoudre ce problème**:

* Ajouter des énoncés plus à l’intention puis former à nouveau. 

N’ajoutez pas énoncés intent None, sauf si qui est suggéré sur le tableau de bord.

> [!Tip]
> Utilisez la troisième section de la page, **énoncés par intention** avec la **énoncés (nombre)** définition, comme un guide visuel rapide des intentions doivent énoncés plus.  
    ![Utilisez « Énoncés (nombre) » pour rechercher les intentions de déséquilibre de données.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Passez en revue les prédictions incorrectes

Le **prédiction incorrecte** affichent intent intents qui ont des énoncés, qui sont utilisés comme exemples pour une intention spécifique, mais sont prévues pour des intentions différentes. 

**Pour résoudre ce problème**:

* Modifier les énoncés pour être plus précis pour les intentions et les former à nouveau.
* Combiner les intentions si énoncés sont trop étroitement lié et former à nouveau.

### <a name="review-unclear-predictions"></a>Passez en revue les prédictions incertaine

Le **prédiction incertaine** intent liste montre les intentions avec énoncés avec des scores de prédiction qui ne sont pas suffisamment loin de façon à partir de leur rival le plus proche, susceptible de changer l’intention supérieure pour l’énoncé sur la formation suivante, en raison [ l’échantillonnage négatif](luis-how-to-train.md#train-with-all-data).

**Pour résoudre ce problème**;

* Modifier les énoncés pour être plus précis pour les intentions et les former à nouveau.
* Combiner les intentions si énoncés sont trop étroitement lié et former à nouveau.

## <a name="utterances-per-intent"></a>Énoncés par intention

Cette carte affiche l’intégrité d’application globale sur les intentions. Comme les intentions et recyclage corrigé, vous devez continuer à coup cette carte pour les problèmes.

Le graphique suivant montre une application bien équilibrée avec presque aucun problème à résoudre.

![Le graphique suivant montre une application bien équilibrée avec presque aucun problème à résoudre.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

Le graphique suivant montre une application mal à charge équilibrée avec nombreux problèmes à résoudre.

![Le graphique suivant montre une application bien équilibrée avec presque aucun problème à résoudre.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Placez le curseur au-dessus de la barre de chaque objectif pour obtenir des informations sur l’intention. 

![Le graphique suivant montre une application bien équilibrée avec presque aucun problème à résoudre.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

Utilisez le **trier par** fonctionnalité pour organiser les intentions par type de problème afin de vous concentrer sur les intentions plus problématiques avec ce problème. 

## <a name="intents-with-errors"></a>Intentions avec des erreurs

Cette carte permet de passer en revue les problèmes pour un objectif spécifique. La vue par défaut de cette carte est intentions plus problématiques afin de savoir où concentrer vos efforts.

![Les intentions de carte d’erreurs vous permet passer en revue les problèmes pour un objectif spécifique. La carte est filtrée sur les intentions plus problématiques, par défaut, afin de savoir où concentrer vos efforts.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

Le graphique en anneau supérieur affiche les problèmes avec l’intention sur les trois types de problème. S’il existe des problèmes dans les trois types de problème, chaque type a son propre graphique ci-dessous, ainsi que les intentions rivales. 

### <a name="filter-intents-by-issue-and-percentage"></a>Filtrer les intentions en problème et en pourcentage

Cette section de la carte vous permet de trouver des énoncés d’exemple qui diminue en dehors de votre seuil d’erreur. Dans l’idéal, vous souhaitez que les prédictions correctes pour être significatif. Ce pourcentage est entreprise et pilotée par les clients. 

Déterminer les pourcentages de seuil que vous maîtrisez pour votre entreprise. 

Le filtre vous permet de trouver les intentions avec problème spécifique :

|Filtrer|Pourcentage conseillé|Objectif|
|--|--|--|
|Intentions plus problématiques|-|**Commencez ici** -résolution des énoncés dans cette intention améliorera l’application, et plusieurs autres correctifs.|
|Prédictions correctes ci-dessous|60 %|Il s’agit du pourcentage d’énoncés dans l’intention sélectionnée qui sont corrects mais qui ont un score de confiance inférieur au seuil. |
|Difficile de savoir prédictions ci-dessus|15 %|Il s’agit du pourcentage d’énoncés dans l’intention sélectionnée sont confondu avec l’intention de rivale le plus proche.|
|Prédictions incorrectes ci-dessus|15 %|Il s’agit du pourcentage des énoncés dans l’intention sélectionnée prédit de façon incorrecte. |

### <a name="correct-prediction-threshold"></a>Seuil de prédiction correcte

Qu’est un score de confiance de certitude de prédiction pour vous ? Au début du développement d’applications, 60 % peut être votre cible. Utilisez le **corriger des prédictions ci-dessous** avec le pourcentage de 60 % pour rechercher les énoncés qui dans l’intention sélectionnée qui doivent être corrigées.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Seuil de prédiction vous semble abscons ou incorrect

Ces deux filtres vous permettent de trouver énoncés dans l’intention sélectionnée au-delà de votre seuil. Vous pouvez considérer ces deux pourcentages sous forme de pourcentages d’erreur. Si vous êtes familiarisé avec un taux d’erreur de 10 à 15 % pour les prédictions, définissez le seuil de filtre à 15 % pour rechercher tous les énoncés au-dessus de cette valeur. 

## <a name="next-steps"></a>Étapes suivantes

* [Gérer vos ressources Azure](luis-how-to-azure-subscription.md)
