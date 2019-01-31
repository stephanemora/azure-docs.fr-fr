---
title: Bonnes pratiques
titleSuffix: Language Understanding - Azure Cognitive Services
description: Découvrez les bonnes pratiques LUIS pour obtenir les meilleurs résultats à partir du modèle de votre application LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: 5a6f9c559ce6fe66d4fe3df9382bc931f4a55e6a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55209364"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Bonnes pratiques pour la création d’une application Language Understanding avec Cognitive Services
Suivez le processus de création d’applications pour générer votre application LUIS. 

* Générer un modèle de langage
* Ajouter quelques exemples d’énoncés d’apprentissage (10-15 par intention)
* Publish 
* Tester à partir du point de terminaison 
* Ajouter des fonctionnalités

Une fois votre application [publiée](luis-how-to-publish-app.md), utilisez le cycle de création pour y ajouter des fonctionnalités, la publier et la tester à partir du point de terminaison. Ne commencez pas le cycle de création suivant en ajoutant d’autres exemples d’énoncés. LUIS ne pourrait pas apprendre votre modèle avec des énoncés d’utilisateurs réels. 

Pour que LUIS soit efficace dans sa tâche d’apprentissage, ne développez pas les énoncés tant que le jeu actuel d’exemples d’énoncés et d’énoncés du point de terminaison ne retourne pas des scores sûrs à haute valeur prédictive. Améliorez les résultats à l’aide de l’[apprentissage actif](luis-concept-review-endpoint-utterances.md), de [modèles](luis-concept-patterns.md) et de [listes d’expressions](luis-concept-feature.md). 

## <a name="do-and-dont"></a>Bonnes et mauvaises pratiques
La liste suivante indique les meilleures pratiques pour les applications LUIS :

|À faire|À ne pas faire|
|--|--|
|[Définir des intentions distinctes](#do-define-distinct-intents) |[Ajouter de nombreux exemples d’énoncés aux intentions](#dont-add-many-example-utterances-to-intents) |
|[Trouver l’équilibre idéal, ni trop générique ni trop spécifique, pour chaque intention](#do-find-sweet-spot-for-intents)|[Utiliser LUIS comme plateforme d’apprentissage](#dont-use-luis-as-a-training-platform)|
|[Générer l’application de manière itérative](#do-build-the-app-iteratively)|[Ajouter de nombreux exemples d’énoncés du même format, en ignorant les autres formats](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Ajouter des listes d’expression et des modèles dans les itérations ultérieures](#do-add-phrase-lists-and-patterns-in-later-iterations)|[Mélanger la définition des intentions et des entités](#dont-mix-the-definition-of-intents-and-entities)|
|[Équilibrer vos énoncés entre toutes les intentions](#balance-your-utterances-across-all-intents), sauf l’intention None.<br>[Ajouter des exemples d’énoncés à l’intention None](#do-add-example-utterances-to-none-intent)|[Créer des listes d’expressions avec toutes les valeurs possibles](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Tirer parti de la fonctionnalité de suggestion pour l’apprentissage actif](#do-leverage-the-suggest-feature-for-active-learning)|[Ajouter trop de modèles](#dont-add-many-patterns).|
|[Analyser les performances de l’application](#do-monitor-the-performance-of-your-app)|[Effectuer l’apprentissage et publier à chaque ajout d’exemple d’énoncé](#dont-train-and-publish-with-every-single-example-utterance)|
|[Utiliser des versions pour chaque itération de l’application](#do-use-versions-for-each-app-iteration)||

## <a name="do-define-distinct-intents"></a>Définir des intentions distinctes
Le vocabulaire de chaque intention doit porter uniquement sur cette intention, sans chevauchement avec une autre. Par exemple, si vous souhaitez disposer d’une application qui gère les préparatifs de voyage, comme les vols et les hôtels, vous pouvez choisir de faire de ces domaines des intentions distinctes ou une même intention avec des entités pour les données précises à l’intérieur de l’énoncé.

Si le vocabulaire des deux intentions est identique, combinez l’intention et utilisez des entités. 

Prenons les exemples d’énoncés suivants :

|Exemples d’énoncés|
|--|
|Book a flight|
|Book a hotel|

« Book a flight » (« Réserver un vol ») et « Book a hotel » (« Réserver un hôtel ») utilisent le même vocabulaire « Book a » (« Réserver un »). Le format étant le même, il doit s’agir de la même intention avec différentes entités extraites pour les mots « flight » (« vol ») et « hotel » (« hôtel »). 

## <a name="do-find-sweet-spot-for-intents"></a>Trouver l’équilibre idéal pour les intentions
Utilisez les données de prédiction de LUIS pour déterminer si vos intentions se chevauchent, ce qui serait source de confusion pour LUIS. La meilleure intention serait alors trop proche d’une autre intention. Dans la mesure où LUIS n’utilise pas à chaque fois le même chemin d’accès à travers les données pour l’apprentissage, l’intention présentant un recoupement a des chances de finir première ou deuxième lors de l’apprentissage. Pour éviter cela, il faut que le score de l’énoncé de chaque intention soit plus éloigné. Lorsque les intentions sont bien distinctes les unes des autres, la meilleure intention devrait être à chaque fois conforme aux attentes. 
 
## <a name="do-build-the-app-iteratively"></a>Générer l’application de manière itérative
Conservez un jeu distinct d’énoncés ne servant pas d’[exemples d’énoncés](luis-concept-utterance.md) ou d’énoncés du point de terminaison. Continuez à améliorer l’application pour votre jeu de test. Adaptez celui-ci de façon à refléter les énoncés d’utilisateurs réels. Utilisez ce jeu de test pour évaluer chaque itération ou version de l’application. 

Les développeurs doivent avoir trois jeux de données : les exemples d’énoncés servant à générer le modèle, un jeu permettant de tester le modèle au point de terminaison et les données de test aveugle utilisées dans les [tests par lots](luis-how-to-batch-test.md). Ce dernier jeu n’est ni utilisé pour entraîner l’application ni envoyé sur le point de terminaison.  

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>Ajouter des listes d’expressions et des modèles dans les itérations ultérieures
Les [listes d’expressions](luis-concept-feature.md) vous permettent de définir des dictionnaires de mots liés à votre domaine d’application. Commencez par créer une liste d’expressions de quelques mots, puis utiliser la fonctionnalité de suggestion pour que LUIS connaisse d’autres mots de vocabulaire spécifiques à votre application. N’ajoutez pas tous les mots au vocabulaire, car la liste d’expressions ne fonctionne pas par correspondance exacte. 

Les énoncés d’utilisateurs réels du point de terminaison, très similaires les uns aux autres, peuvent révéler des motifs dans le choix et la place des mots. La fonctionnalité [modèle](luis-concept-patterns.md) s’appuie sur ces motifs et sur des expressions régulières pour améliorer la précision des prédictions. L’ajout d’une expression régulière au modèle permet d’ignorer certains mots et signes de ponctuation tout en préservant la correspondance avec le modèle. 

Utilisez la [syntaxe facultative](luis-concept-patterns.md) du modèle afin d’ignorer la ponctuation. Utilisez la [liste explicite](luis-concept-patterns.md#explicit-lists) pour compenser les problèmes de syntaxe pattern.any. 

N’appliquez pas ces pratiques tant que votre application n’a pas reçu de demandes du point de terminaison, car cela fausserait la confiance.  

## <a name="balance-your-utterances-across-all-intents"></a>Équilibrer vos énoncés entre toutes les intentions

Pour que LUIS puisse faire des prédictions précises, la quantité d’exemples d’énoncés dans chaque intention (à l’exception de l’intention None) doit être plus ou moins égale. 

Si vous avez une intention avec 100 exemples d’énoncés et une autre avec 20 exemples d’énoncés, celle avec 100 énoncés aura un taux de prédiction plus élevé.  

## <a name="do-add-example-utterances-to-none-intent"></a>Ajouter des exemples d’énoncés à l’intention None

Cette intention est l’intention de secours, qui indique tout ce qui ne concerne pas l’application. Ajoutez un exemple d’énoncé à l’intention None tous les 10 exemples d’énoncés dans le reste de votre application LUIS.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Tirer parti de la fonctionnalité de suggestion pour l’apprentissage actif

Utilisez régulièrement la fonctionnalité **Vérifier les énoncés du point de terminaison** de [l’apprentissage actif](luis-how-to-review-endoint-utt.md), au lieu d’ajouter d’autres exemples d’énoncés aux intentions. L’application reçoit constamment des énoncés du point de terminaison, ce qui allonge et fait évoluer cette liste.

## <a name="do-monitor-the-performance-of-your-app"></a>Analyser les performances de l’application

Supervisez la précision des prédictions à l’aide d’un jeu de [tests par lots](luis-concept-batch-test.md). 

## <a name="dont-add-many-example-utterances-to-intents"></a>Ne pas ajouter de nombreux exemples d’énoncés aux intentions

Une fois l’application publiée, ajoutez seulement des énoncés à partir de l’apprentissage actif selon un processus itératif. S’ils sont trop proches, ajoutez un modèle. 

## <a name="dont-use-luis-as-a-training-platform"></a>Ne pas utiliser LUIS comme plateforme d’apprentissage

LUIS est propre au domaine d’un modèle de langage. Il n’est pas conçu pour fonctionner comme une plateforme générale d’entraînement en langage naturel. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Ne pas ajouter de nombreux exemples d’énoncés du même format, en ignorant les autres formats

LUIS attend des variations dans les énoncés d’une intention. Les énoncés peuvent différer (longueur, choix et place des mots notamment) tout en gardant la même signification générale. 

|N’utilisez pas le même format|Utilisez des formats variables|
|--|--|
|Acheter un billet pour Seattle<br>Acheter un billet pour Paris<br>Acheter un billet pour Orlando|Acheter 1 billet pour Seattle<br>Réserver deux sièges pour le vol de nuit à destination de Paris lundi prochain<br>J’aimerais réserver 3 billets pour Orlando pour les vacances de printemps|

La deuxième colonne utilise différents verbes (acheter, réserver), différentes quantités (1, deux, 3) et différents ordres des mots, mais tous les énoncés ont la même intention : acheter des billets d’avion pour voyager. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Ne pas mélanger la définition des intentions et des entités

Créez une intention pour chaque action du bot. Utilisez des entités comme des paramètres qui rendent cette action possible. 

Pour un chatbot qui réserve des vols, créez une intention **RéserverVol**. Ne créez pas une intention pour chaque compagnie aérienne ou chaque destination. Utilisez ces éléments de données comme [entités](luis-concept-entity-types.md) et marquez-les dans les exemples d’énoncés. 

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Ne pas créer des listes d’expressions avec toutes les valeurs possibles

Donnez quelques exemples dans les [listes d’expressions](luis-concept-feature.md), mais pas tous les mots. LUIS généralise et tient compte du contexte. 

## <a name="dont-add-many-patterns"></a>Ne pas ajouter de nombreux modèles

Évitez d’ajouter trop de [modèles](luis-concept-patterns.md). LUIS est conçu pour apprendre rapidement avec peu d’exemples. Ne surchargez pas inutilement le système.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Ne pas effectuer l’apprentissage et publier à chaque ajout d’exemple d’énoncé

Ajoutez 10 ou 15 énoncés avant de passer à l’apprentissage et à la publication. Vous pourrez ainsi en voir l’impact sur la précision des prédictions. L’ajout d’un seul énoncé n’aura peut-être pas d’incidence visible sur le score. 

## <a name="do-use-versions-for-each-app-iteration"></a>Ne pas utiliser des versions pour chaque itération de l’application

Chaque cycle de création doit figurer dans une nouvelle [version](luis-concept-version.md), clonée à partir d’une version existante. LUIS ne limite pas les versions. Un nom de version étant utilisé dans le cadre de la route d’API, il est important de choisir des caractères autorisés dans une URL et de ne pas dépasser 10 caractères. Développez une stratégie de nom de version pour organiser vos versions. 

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [planifier votre application](luis-how-plan-your-app.md) dans votre application LUIS.
