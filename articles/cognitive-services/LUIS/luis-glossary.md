---
title: Glossaire - LUIS
titleSuffix: Azure Cognitive Services
description: Le glossaire explique les termes que vous pourriez rencontrer en utilisant le service API LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 4f78b4c50f4cd65f3dc32c48cea81b705dc44de1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219151"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Glossaire Language Understanding des termes et concepts courants
Le glossaire Language Understanding (LUIS) explique les termes que vous pourriez rencontrer lors de l’utilisation du service API LUIS.

## <a name="active-version"></a><a name="active-version"></a>Version active

La version active de LUIS est la version qui reçoit les modifications apportées au modèle. Dans le portail [LUIS](luis-reference-regions.md), si vous souhaitez modifier une version qui n’est pas la version active, vous devrez tout d’abord la définir comme étant la version active.

## <a name="authoring"></a><a name="authoring"></a>Création

La création correspond à la possibilité de créer, de gérer et de déployer une [application LUIS](#luis-app), soit avec le portail [LUIS](luis-reference-regions.md), soit avec les [API de création](https://go.microsoft.com/fwlink/?linkid=2092087).

## <a name="authoring-key"></a><a name="authoring-key"></a>Clé de création

Cette clé, auparavant nommée clé « programmatique », est utilisée pour créer l’application et non pour les requêtes du point de terminaison au niveau de la production. Pour plus d'informations, voir [Limites des clés](luis-boundaries.md#key-limits).

## <a name="batch-test-json-file"></a><a name="batch-test-json-file"></a>Fichier JSON de tests par lot

Les tests par lot permettent de valider un modèle actuel d’application LUIS avec un ensemble cohérent et connu d’énoncés utilisateurs. Le test par lot est défini dans un [fichier au format JSON](luis-concept-batch-test.md#batch-file-format).

Voir aussi :
* [Concepts](luis-concept-batch-test.md)
* [Guide pratique](luis-how-to-batch-test.md)
* [Didacticiel](luis-tutorial-batch-testing.md)

## <a name="collaborator"></a><a name="collaborator"></a>Collaborateur

Un collaborateur/contributeur n’est pas le [propriétaire](#owner) de l’application, mais il dispose des mêmes autorisations pour ajouter, modifier et supprimer les intentions, les entités et les énoncés.

## <a name="contributor"></a><a name="contributor"></a>Contributeur

Un contributeur est pareil qu’un [collaborateur](#collaborator).

## <a name="descriptor"></a><a name="descriptor"></a>Descripteur

Un descripteur est une [fonctionnalité](#features) appliquée à un modèle au moment de la formation, y compris des [listes de phrases](#phrase-list) et des [entités](#entity). 

## <a name="domain"></a><a name="domain"></a>Domaine

Dans le contexte de LUIS, un **domaine** est un champ de connaissances propre à l’application. Il peut s’agir d’un domaine général, comme l’application d’agent de voyages. Une application d’agent de voyages peut également se rattacher spécifiquement au champ d’informations relatif à l’entreprise (zones géographiques, langues et services en particulier).

## <a name="endpoint"></a><a name="endpoint"></a>Point de terminaison

L’URL du [point de terminaison LUIS](https://go.microsoft.com/fwlink/?linkid=2092356) est celle à laquelle les requêtes LUIS sont envoyées après création et publication de [l’application LUIS](#luis-app). Elle contient la région de l’application publiée, ainsi que l’ID de l’application. Vous trouverez le point de terminaison dans la page **[Keys and endpoints (Clés et points de terminaison)](luis-how-to-azure-subscription.md)** de votre application, ou vous pouvez obtenir l’URL de point de terminaison à partir de l’API [Obtenir des informations sur l’application](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37).

## <a name="entity"></a><a name="entity"></a>Entité

Les [entités](luis-concept-entity-types.md) sont des mots importants dans les [énoncés](luis-concept-utterance.md) qui décrivent des informations relatives à [l’intention](luis-concept-intent.md) et sont parfois essentiels à cet égard. Une entité est globalement un type de données dans LUIS.

## <a name="f-measure"></a><a name="f-measure"></a>F-mesure

Il s’agit d’une mesure de précision d’un [test par lots](luis-interactive-test.md#batch-testing).

## <a name="false-negative-fn"></a><a name="false-negative"></a>Faux négatif (FN)

Dans les [tests par lots](luis-interactive-test.md#batch-testing), les points de données représentent les énoncés dans lesquels votre application a mal prédit l’absence de l’intention/entité cible.

## <a name="false-positive-fp"></a><a name="false-positive"></a>Faux positif (FP)

Dans les [tests par lots](luis-interactive-test.md#batch-testing), les points de données représentent les énoncés dans lesquels votre application a mal prédit l’existence de l’intention/entité cible.

## <a name="features"></a><a name="features"></a>Caractéristiques

En Machine Learning, une [caractéristique](luis-concept-feature.md) (« feature ») est un trait ou un attribut distinctif des données observées par le système.

## <a name="intent"></a><a name="intent"></a>Intention

Une [intention](luis-concept-intent.md) représente une tâche ou une action que l’utilisateur souhaite effectuer. Il s’agit d’un but ou d’un objectif exprimé dans l’énoncé d’un utilisateur, par exemple réserver un vol, régler une facture ou trouver un article de presse. Dans LUIS, la prédiction des intentions s’appuie sur la totalité de l’énoncé. Les entités, elles, sont des parties d’énoncés.

## <a name="labeling"></a><a name="labeling"></a>Étiquetage

L’étiquetage, ou marquage, est le processus d’association d’un mot ou une expression dans [l’énoncé](#utterance) d’une intention avec une [entité](#entity) (type de données).

## <a name="luis-app"></a><a name="luis-app"></a>Application LUIS

Une application LUIS est une collection de modèles de langage pour le traitement du langage naturel, y compris des [intentions](#intent), [entités](#entity) et [énoncés](#utterance) étiquetés.

## <a name="owner"></a><a name="owner"></a>Propriétaire

Chaque application possède un propriétaire, à savoir la personne qui a créé l’application. Le propriétaire peut ajouter des [collaborateurs](#collaborator).

## <a name="patterns"></a><a name="pattern"></a>Modèles
L’ancienne fonctionnalité Modèle est remplacée par [Modèles](luis-concept-patterns.md). Utilisez des modèles pour améliorer la précision des prédictions en fournissant moins d’exemples d’apprentissage.

## <a name="phrase-list"></a><a name="phrase-list"></a>Liste d’expressions

Une [liste d’expressions](luis-concept-feature.md) est un groupe de valeurs (mots ou expressions) qui appartiennent à la même classe et doivent être traitées de la même façon (par exemple, des noms de villes ou de produits). Les éléments d’une liste interchangeable sont traités comme synonymes.

## <a name="prebuilt-domain"></a><a name="prebuilt-domains"></a>Domaine prédéfini

Un [domaine prédéfini](luis-how-to-use-prebuilt-domains.md) est une application LUIS configurée pour un domaine en particulier, par exemple, la domotique (HomeAutomation) ou les réservations de restaurants (RestaurantReservation). Les intentions, les énoncés et les entités sont configurés pour ce domaine.

## <a name="prebuilt-entity"></a><a name="prebuilt-entity"></a>Entité prédéfinie

Une [entité prédéfinie](luis-prebuilt-entities.md) est une entité fournie par LUIS pour les types d’informations courants, comme le nombre, l’URL et l’adresse électronique. Vous pouvez choisir d’en ajouter ou non à votre application.

## <a name="precision"></a><a name="precision"></a>Précision
Dans les [tests par lots](luis-interactive-test.md#batch-testing), la précision (également appelée coefficient de prévision d'un test positif) est la part d’énoncés pertinents parmi les énoncés récupérés.

## <a name="programmatic-key"></a><a name="programmatic-key"></a>Clé programmatique

Renommée [clé de création](#authoring-key).

## <a name="publish"></a><a name="publish"></a>Publication

La publication consiste à rendre disponible une version active de LUIS sur le [point de terminaison](#endpoint) de mise en lots ou de production.  

## <a name="quota"></a><a name="quota"></a>Quota

Le quota LUIS correspond à la limitation du [niveau d’abonnement Azure](https://aka.ms/luis-price-tier). Il peut être exprimé en demandes par seconde (état HTTP 429), en nombre total de demandes par mois (état HTTP 403) ou les deux.

## <a name="recall"></a><a name="recall"></a>Rappel
Dans les [tests par lots](luis-interactive-test.md#batch-testing), le rappel (également appelé sensibilité) correspond à la capacité de généralisation de LUIS.

## <a name="semantic-dictionary"></a><a name="semantic-dictionary"></a>Dictionnaire sémantique
Un dictionnaire sémantique est proposé sur la page Entité de liste ainsi que sur la page Liste d’expressions. Il propose des suggestions de mots en fonction de la portée actuelle.

## <a name="sentiment-analysis"></a><a name="sentiment-analysis"></a>Analyse des sentiments
L’analyse des sentiments attribue des valeurs positives ou négatives aux énoncés fournis par [l’Analyse de texte](https://azure.microsoft.com/services/cognitive-services/text-analytics/).

## <a name="speech-priming"></a><a name="speech-priming"></a>Préparation vocale

La préparation vocale permet de préparer votre service vocal avec votre modèle LUIS.

## <a name="spelling-correction"></a><a name="spelling-correction"></a>Correction orthographique

Activez la vérification orthographique Bing pour corriger les mots mal orthographiés dans les énoncés avant la prédiction.

## <a name="starter-key"></a><a name="starter-key"></a>Clé de démarrage

Une clé libre à utiliser lors du premier démarrage à l’aide de LUIS.

## <a name="structure"></a><a name="structure"></a>Structure

Ajoutez une structure à une entité issue de l’apprentissage automatique pour fournir des sous-composants avec des descripteurs (fonctionnalités) et des contraintes (expression régulière ou entités de listes).

## <a name="subscription-key"></a><a name="subscription-key"></a>Clé d’abonnement

La clé d’abonnement est la clé du **point de terminaison de prédiction** associée au service LUIS [créé dans Azure](luis-how-to-azure-subscription.md). Il ne s’agit pas de la [clé de création](#programmatic-key). Si vous avez une clé de point de terminaison, vous devrez l’utiliser pour toutes les demandes du point de terminaison au lieu de la clé de création. Vous pouvez voir votre clé de point de terminaison actuelle au sein de l’URL du point de terminaison en bas de la [page **Keys and endpoints (Clés et points de terminaison)** ](luis-how-to-azure-subscription.md) sur le site web [LUIS](luis-reference-regions.md). Il s’agit de la valeur de la paire nom/valeur **subscription-key**.

## <a name="test"></a><a name="test"></a>Test

[Tester](luis-interactive-test.md#test-your-app) une application LUIS signifie soumettre un énoncé à LUIS et afficher les résultats JSON.

## <a name="timezone-offset"></a><a name="timezoneoffset"></a>timezoneOffset

Le point de terminaison comporte timezoneOffset, qui correspond au nombre de minutes à ajouter ou supprimer de l’entité prédéfinie datetimeV2. Par exemple, si l’énoncé est « Quelle heure est-il maintenant ? », la valeur datetimeV2 retournée est l’heure actuelle de la demande du client. Si la demande de client provient d’un bot ou de toute autre application différente de l’utilisateur de votre bot, vous devrez indiquer le décalage entre le bot et l’utilisateur.

Voir [Changer le fuseau horaire de l’entité datetimeV2 prédéfinie](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a><a name="token"></a>Jeton
Un jeton est la plus petite unité pouvant être étiquetée dans une entité. La segmentation du texte en unités lexicales (tokenization) dépend de la [culture](luis-language-support.md#tokenization) de l’application.

## <a name="train"></a><a name="train"></a>Apprentissage

La formation est le processus consistant à enseigner à LUIS toutes les modifications apportées à la version active depuis la dernière formation.

## <a name="true-negative-tn"></a><a name="true-negative"></a>Vrai négatif (TN)

Dans les [tests par lots](luis-interactive-test.md#batch-testing), les points de données représentent les énoncés dans lesquels votre application a bien prédit l’absence de l’intention/entité cible.

## <a name="true-positive-tp"></a><a name="true-positive"></a>Vrai positif (TP)

Dans les [tests par lots](luis-interactive-test.md#batch-testing), les points de données représentent les énoncés dans lesquels votre application a bien prédit l’existence de l’intention/entité cible.

## <a name="utterance"></a><a name="utterance"></a>Énoncé

Un énoncé est une expression en langage naturel comme « Réserve 2 billets pour Seattle mardi prochain ». Des exemples d’énoncés sont ajoutés à l’intention.

## <a name="version"></a><a name="version"></a>Version

Une [version](luis-how-to-manage-versions.md) de LUIS est un modèle de données spécifique associé à un ID d’application LUIS et au point de terminaison publié. Chaque application LUIS a au moins une version.
