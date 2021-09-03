---
title: Bonnes pratiques pour l’utilisation de l’API Détecteur d’anomalies (multivarié)
titleSuffix: Azure Cognitive Services
description: Bonnes pratiques pour l’utilisation de l’API Détecteur d’anomalies (multivarié) afin d’appliquer la détection d’anomalies à vos données de série chronologique.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: détection d’anomalie, Machine Learning, algorithmes
ms.openlocfilehash: 4114771276f4fec6dfef0e953ef9f52e165db510
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2021
ms.locfileid: "113297319"
---
# <a name="best-practices-for-using-the-anomaly-detector-multivariate-api"></a>Meilleures pratiques d’utilisation de l’API Détecteur d’anomalies multivarié

Cet article donne des conseils concernant les pratiques recommandées à suivre pour utiliser les API Détecteur d’anomalies multivarié (MVAD, Multivariate Anomaly Detector). Dans ce didacticiel, vous allez :

> [!div class="checklist"]
> * **Utilisation des API** : découvrez comment utiliser MVAD sans erreurs.
> * **Engineering données** : découvrez comment préparer au mieux vos données de sorte que MVAD fonctionne avec plus d’exactitude.
> * **Pièges courants** : découvrez comment éviter les pièges courants que rencontrent les clients.
> * **FAQ** : retrouvez les réponses aux questions fréquentes.

## <a name="api-usage"></a>Utilisation de l’API

Suivez les instructions de cette section pour éviter les erreurs lorsque vous utilisez MVAD. Si vous recevez toujours des erreurs, consultez la [liste complète des codes d’erreur](./troubleshoot.md) pour obtenir des explications et savoir quelles actions effectuer.

[!INCLUDE [mvad-input-params](../includes/mvad-input-params.md)]

[!INCLUDE [mvad-data-schema](../includes/mvad-data-schema.md)]


## <a name="data-engineering"></a>Engineering données

Vous être maintenant en mesure d’exécuter votre code avec les API MVAD sans aucune erreur. Que pouvez-vous faire pour améliorer l’exactitude de votre modèle ?

### <a name="data-quality"></a>Qualité des données

* Étant donné que le modèle apprend les modèles normaux à partir de données historiques, les données d’apprentissage doivent représenter l’état **normal global** du système. Il est difficile pour le modèle d’apprendre ces types de patterns si les données d’entraînement sont pleines d’anomalies. Le seuil empirique de taux anormal, au-dessous duquel l’exactitude est bonne, s’élève à **1 %** .
* En général, le **ratio des valeurs manquantes des données d’entraînement doit être inférieur à 20 %** . S’il manque trop de données, les valeurs renseignées automatiquement (généralement des valeurs linéaires ou constantes) risquent d’être apprises comme des modèles normaux, ce qui peut entraîner la détection de points de données réels (et non manquants) comme des anomalies.
    Toutefois, il existe des cas où un ratio élevé de données manquantes est acceptable. Prenons par exemple deux variables (séries chronologiques) dans un groupe utilisant le mode `Outer` pour aligner leurs horodateurs. L’une d’elles possède une granularité d’une minute, l’autre d’une heure. La variable horaire présente, par nature, au moins 59 points de données manquants sur 60, soit 98,33 %. Dans ce cas, il est acceptable de remplir cette variable en utilisant la seule valeur disponible (non manquante) si elle ne fluctue pas trop d’une manière générale.

### <a name="data-quantity"></a>Quantité de données

* Le modèle sous-jacent de MVAD possède des millions de paramètres. Il a besoin d’un volume minimal de points de données pour apprendre un ensemble optimal de paramètres. La règle empirique est qu’il faut fournir **au moins 15 000 points de données (horodateurs) par variable** pour effectuer l’apprentissage du modèle avec une bonne exactitude. En général, plus il y a de données d’apprentissage, meilleure est l’exactitude. Si vous n’êtes pas en mesure d’accumuler autant de données, nous vous encourageons à effectuer quand même des essais pour voir si la précision compromise reste acceptable.
* Chaque fois que vous appelez l’API d’inférence, vous devez vous assurer que le fichier de données source contient le bon volume de points de données. Ce volume s’élève généralement à `slidingWindow` + le nombre de points de données qui ont **vraiment** besoin des résultats de l’inférence. Prenons par exemple un cas de diffusion en continu : chaque fois que vous souhaitez effectuer une inférence sur **UN** nouvel horodateur, le fichier de données peut contenir uniquement la fenêtre `slidingWindow` de début + **UN** point de données. Vous pouvez alors créer un autre fichier zip comportant le même nombre de points de données (`slidingWindow` + 1), mais en vous décalant d’UN pas vers le côté « droit », et l’envoyer pour une autre tâche d’inférence. 

    Les points situés au-delà ou « avant » la fenêtre glissante de début n’ont aucun impact sur le résultat de l’inférence. Ils peuvent seulement entraîner une dégradation des performances. Les points situés au-dessous risquent d’entraîner une erreur `NotEnoughInput`.


### <a name="timestamp-round-up"></a>Arrondi de l’horodateur

Dans un groupe de variables (séries chronologiques), chacune peut être collectée à partir d’une source indépendante. Dans certains cas, les horodateurs de différentes variables présentent des incohérences les uns avec les autres et avec les fréquences connues. Voici un exemple simple.

*Variable-1*

| timestamp | value |
| --------- | ----- |
| 12:00:01  | 1.0   |
| 12:00:35  | 1.5   |
| 12:01:02  | 0.9   |
| 12:01:31  | 2.2   |
| 12:02:08  | 1.3   |

*Variable-2*

| timestamp | value |
| --------- | ----- |
| 12:00:03  | 2.2   |
| 12:00:37  | 2.6   |
| 12:01:09  | 1.4   |
| 12:01:34  | 1.7   |
| 12:02:04  | 2.0   |

Deux variables sont collectées à partir de deux capteurs qui envoient un point de données toutes les 30 secondes. Toutefois, les capteurs n’envoient pas les points de données à une fréquence strictement égale, mais parfois plus tôt, parfois plus tard. Étant donné que MVAD prend en compte les corrélations entre différentes variables, les horodateurs doivent être bien alignés pour que les métriques puissent refléter correctement l’état du système. Dans l’exemple ci-dessus, les horodateurs des variables 1 et 2 doivent être correctement « arrondis » à leur fréquence avant alignement.

Voyons ce qui se passe s’ils ne sont pas prétraités. Si l’on définit `alignMode` sur `Outer` (ce qui correspond à l’union de deux ensembles), on obtient la table fusionnée suivante :

| timestamp | Variable-1 | Variable-2 |
| --------- | -------- | -------- |
| 12:00:01  | 1.0      | `nan`    |
| 12:00:03  | `nan`    | 2.2      |
| 12:00:35  | 1.5      | `nan`    |
| 12:00:37  | `nan`    | 2.6      |
| 12:01:02  | 0.9      | `nan`    |
| 12:01:09  | `nan`    | 1.4      |
| 12:01:31  | 2.2      | `nan`    |
| 12:01:34  | `nan`    | 1.7      |
| 12:02:04  | `nan`    | 2.0      |
| 12:02:08  | 1.3      | `nan`    |

`nan` indique des valeurs manquantes. De toute évidence, la table fusionnée ne correspond à pas à ce que l’on attendrait. Les variables 1 et 2 s’imbriquent, et le modèle MVAD ne peut pas extraire d’informations sur leurs corrélations. Si l’on définit `alignMode` sur `Inner`, la table fusionnée est vide, car il n’existe aucun horodateur commun dans les variables 1 et 2.

Par conséquent, les horodateurs des variables 1 et 2 doivent être prétraités (arrondi aux 30 secondes les plus proches). On obtient les nouvelles séries chronologiques suivantes :

*Variable-1*

| timestamp | value |
| --------- | ----- |
| 12:00:00  | 1.0   |
| 12:00:30  | 1.5   |
| 12:01:00  | 0.9   |
| 12:01:30  | 2.2   |
| 12:02:00  | 1.3   |

*Variable-2*

| timestamp | value |
| --------- | ----- |
| 12:00:00  | 2.2   |
| 12:00:30  | 2.6   |
| 12:01:00  | 1.4   |
| 12:01:30  | 1.7   |
| 12:02:00  | 2.0   |

La table fusionnée est maintenant plus raisonnable.

| timestamp | Variable-1 | Variable-2 |
| --------- | -------- | -------- |
| 12:00:00  | 1.0      | 2.2      |
| 12:00:30  | 1.5      | 2.6      |
| 12:01:00  | 0.9      | 1.4      |
| 12:01:30  | 2.2      | 1.7      |
| 12:02:00  | 1.3      | 2.0      |

Les valeurs de variables différentes présentant des horodateurs proches sont bien alignées. Le modèle MVAD peut maintenant extraire les informations de corrélation.

## <a name="common-pitfalls"></a>Pièges courants

En dehors de la [table des codes d’erreur](./troubleshoot.md), nous avons appris auprès de clients quelques pièges courants liés à l’utilisation des API MVAD. Ce tableau vous aidera à éviter ces problèmes.

| Piège | Conséquence |Explication et solution |
| --------- | ----- | ----- |
| Horodateurs des données d’apprentissage et/ou d’inférence non arrondis pour s’aligner sur la fréquence de données respective de chaque variable | Les horodateurs des résultats de l’inférence ne correspondent pas aux attentes : trop ou trop peu d’horodateurs.  | Consultez [Arrondi des horodateurs](#timestamp-round-up).  |
| Trop de points de données anormaux dans les données d’apprentissage | L’exactitude du modèle est affectée, car il traite les points de données anormaux comme des modèles normaux pendant l’apprentissage. | De manière empirique, il est utile de maintenir un taux anormal inférieur ou égal à **1 %** . |
| Trop peu de données d’apprentissage | L’exactitude du modèle est compromise. | De manière empirique, l’apprentissage d’un modèle MVAD exige au moins 15 000 points de données (horodateurs) par variable pour garantir une bonne exactitude.|
| Tous les points de données pour lesquels `isAnomaly`=`true` pris comme des anomalies | Le nombre de faux positifs est trop élevé. | Utilisez `isAnomaly` et `severity` (ou `score`) pour filtrer les anomalies qui ne sont pas graves, et (éventuellement) le regroupement pour vérifier la durée des anomalies en vue de supprimer les bruits aléatoires. Consultez la section [FAQ](#faq) ci-dessous pour connaître la différence entre `severity` et `score`.  |
| Sous-dossiers compressés dans le fichier de données à des fins d’apprentissage ou d’inférence | Les fichiers de données CSV situés dans les sous-dossiers sont ignorés au cours de l’apprentissage et de l’inférence. | Aucun sous-dossier n’est autorisé dans le fichier zip. Pour plus d’informations, consultez [Structure des dossiers](#folder-structure). |
| Trop de données dans le fichier de données d’inférence (par exemple compression de toutes les données historiques dans le fichier zip des données d’inférence) | Il ne se produit pas forcément d’erreurs. En revanche, une dégradation des performances est observée au moment de télécharger le fichier zip vers l’objet Blob Azure ou d’exécuter l’inférence. | Pour plus d’informations, consultez [Quantité de données](#data-quantity). |
| Création de ressources Détecteur d’anomalies sur des régions Azure qui ne prennent pas encore en charge MVAD et appel des API MVAD  | Une erreur « Ressource introuvable » se produit lors de l’appel des API MVAD. | Pendant la phase de préversion, MVAD n’est disponible que dans certaines régions. Veuillez créer un signet [Nouveautés du Détecteur d’anomalies](../whats-new.md) pour rester informé des déploiements de régions MVAD. Vous pouvez également signaler un problème GitHub ou nous contacter à l’adresse AnomalyDetector@microsoft.com pour demander l’ajout de régions spécifiques. |

## <a name="faq"></a>Forum aux questions

### <a name="how-does-mvad-sliding-window-work"></a>Comment fonctionne la fenêtre glissante MVAD ?

Prenons deux exemples pour découvrir comment fonctionne la fenêtre glissante de MVAD. Supposons que `slidingWindow` = 1 440 et que les données d’entrée présentent une granularité d’une minute.

* **Scénario de diffusion en continu** : vous souhaitez prédire si LE point de données situé à « 2021-01-02T00:00:00Z » est anormal. `startTime` et `endTime` possèdent la même valeur (« 2021-01-02T00:00:00Z »). Votre source de données d’inférence, toutefois, doit contenir au moins 1 440 + 1 horodateurs. En effet, MVAD prend les données de début avant le point de données cible (« 2021-01-02T00:00:00Z ») pour déterminer si la cible constitue une anomalie. La longueur des données de début nécessaires est `slidingWindow`, soit 1 440 dans ce cas. Or, 1 440 = 60 × 24. Les données d’entrée doivent donc commencer à « 2021-01-01T00:00:00Z » au plus tard.

* **Scénario de traitement par lots** : vous avez plusieurs points de données cibles à prédire. `endTime` est supérieur à `startTime`. Dans ce cas, l’inférence est effectuée en mode « fenêtre mobile ». Par exemple, MVAD utilise les données de `2021-01-01T00:00:00Z` à `2021-01-01T23:59:00Z` (inclus) pour déterminer si les données situées à `2021-01-02T00:00:00Z` sont anormales. Il passe ensuite aux données de `2021-01-01T00:01:00Z` à `2021-01-02T00:00:00Z` (inclus) pour déterminer si les données situées à `2021-01-02T00:01:00Z` sont anormales. Il avance ainsi (en prenant 1 440 points de données à comparer) jusqu’au dernier horodateur spécifié par `endTime` (ou le dernier horodateur proprement dit). Par conséquent, la source de données d’inférence doit contenir des données à partir de `startTime` - `slidingWindow`. Dans l’idéal, sa taille est de `slidingWindow` + (`endTime` - `startTime`).

### <a name="why-only-accepting-zip-files-for-training-and-inference"></a>Pourquoi seuls les fichiers zip sont-ils acceptés pour l’apprentissage et l’inférence ?

Dans les scénarios de traitement par lots, la taille attendue des données d’apprentissage et d’inférence serait très élevée. Ces données ne pourraient pas être placées dans le corps de la requête HTTP. Les fichiers zip permettent aux utilisateurs d’effectuer une inférence par lots sur des données historiques pour la validation du modèle ou l’analyse de données.

Toutefois, cela n’est pas très pratique pour l’inférence en continu et les données à fréquence élevée. Nous avons l’intention d’ajouter une nouvelle API spécifiquement conçue pour l’inférence en continu, afin que les utilisateurs puissent passer des données dans le corps de la requête.

### <a name="whats-the-difference-between-severity-and-score"></a>Quelle est la différence entre `severity` et `score` ?

En règle générale, nous vous recommandons d’utiliser `severity` afin de filtrer les « anomalies » qui ne sont pas si importantes pour votre entreprise. En fonction du scénario et du modèle de données, ces anomalies non essentielles possèdent souvent des valeurs `severity` relativement basses ou des valeurs `severity` élevées autonomes (discontinues) comme des pics aléatoires.

Dans les cas où vous avez identifié qu’il vous fallait des règles plus sophistiquées que les seuils `severity` et la durée des valeurs `severity` élevées continues, vous pouvez utiliser `score` pour créer des filtres plus puissants. Comprendre comment MVAD utilise `score` pour déterminer les anomalies peut vous aider :

Nous considérons si un point de données est anormal du point de vue global et local. Si la valeur `score` à un horodateur donné est supérieure à un certain seuil, l’horodateur est marqué comme une anomalie. Si la valeur `score` est inférieure au seuil, mais relativement élevée dans un segment, il est également marqué comme une anomalie.

## <a name="next-steps"></a>Étapes suivantes

* [Démarrages rapides : Utilisation de la bibliothèque de client Détecteur d’anomalies multivarié](../quickstarts/client-libraries-multivariate.md)
* [Découvrir les algorithmes sous-jacents utilisés par le détecteur d’anomalies multivarié](https://arxiv.org/abs/2009.02040)
