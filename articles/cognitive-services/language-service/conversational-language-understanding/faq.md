---
title: Forum Aux Questions (FAQ)
titleSuffix: Azure Cognitive Services
description: Utilisez cet article pour obtenir rapidement des réponses aux questions fréquentes sur Compréhension du langage courant
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2e699beb4bae6c31c73e2e5eebc508306deeb262
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096732"
---
# <a name="frequently-asked-questions-for-conversational-language-understanding"></a>FAQ sur Compréhension du langage courant

Utilisez cet article pour obtenir rapidement des réponses aux questions fréquentes sur Compréhension du langage courant

## <a name="how-do-i-create-a-project"></a>Comment créer un projet ?

Pour plus d’informations, consultez le [guide de démarrage rapide](./quickstart.md) pour créer rapidement votre premier projet ou le [guide pratique](./how-to/create-project.md). 

## <a name="how-do-i-connect-other-service-applications-in-orchestration-workflow-projects"></a>Comment connecter d’autres applications de service dans des projets de workflow d’orchestration ?

Consultez l’[article Créer un schéma](./how-to/build-schema.md#build-project-schema-for-orchestration-workflow-projects) pour plus d’informations sur la connexion d’un autre projet sous forme d’intention.

## <a name="which-luis-applications-can-i-connect-to-in-orchestration-workflow-projects"></a>Quelles sont les applications LUIS auxquelles je peux me connecter dans des projets de workflow d’orchestration ?

Vous pouvez vous connecter aux applications LUIS qui utilisent la ressource de langue comme ressource de création. Vous pouvez vous connecter uniquement aux applications LUIS qui appartiennent à la même ressource. Cette option est disponible uniquement pour les ressources en Europe Ouest, car il s’agit de la seule région commune disponible entre LUIS et CLU. Pour plus d’informations, consultez les [limites régionales](./service-limits.md#region-limits). 

## <a name="training-is-taking-a-long-time-is-this-expected"></a>L’entraînement prend beaucoup de temps, est-ce normal ?

Pour les projets de langage courant, les durées d’entraînement sont longues. En fonction du nombre d’exemples que vous avez, les durées d’entraînement peuvent varier de 5 minutes à 1 heure ou plus. 

## <a name="can-i-add-entities-to-orchestration-workflow-projects"></a>Puis-je ajouter des entités aux projets de workflow d’orchestration ?

Non. Les projets d’orchestration sont uniquement activés pour les intentions pouvant se connecter à d’autres projets pour le routage. 

## <a name="how-do-i-use-entity-components"></a>Comment utiliser les composants d’entité ?

Consultez l’article sur les [composants d’entité](./concepts/entity-components.md).

## <a name="which-languages-are-supported-in-this-feature"></a>Quelles sont les langues prises en charge dans cette fonctionnalité ?

Consultez l’article sur la [prise en charge des langues](./language-support.md).

## <a name="how-do-i-get-more-accurate-results-for-my-project"></a>Comment obtenir des résultats plus justes pour mon projet ?

Consultez les [instructions recommandées](./how-to/build-schema.md#guidelines-and-recommendations) pour plus d’informations sur l’amélioration de la justesse.

## <a name="how-do-i-get-predictions-in-different-languages"></a>Comment obtenir des prédictions dans différentes langues ?

Quand vous entraînez et déployez un projet de langage courant dans une langue, vous pouvez immédiatement essayer de l’interroger dans [plusieurs langues](./concepts/multiple-languages.md). Les résultats peuvent varier d’une langue à l’autre. Pour améliorer la justesse d’une langue, ajoutez des énoncés à votre projet dans la langue en question pour présenter au modèle entraîné plus de syntaxe de cette langue.

## <a name="how-many-intents-entities-utterances-can-i-add-to-a-project"></a>Combien d’intentions, d’entités, d’énoncés puis-je ajouter à un projet ?

Consultez l’article sur les [limites du service](./service-limits.md). 

## <a name="can-i-label-the-same-word-as-2-different-entities"></a>Puis-je étiqueter le même mot sous deux entités différentes ?

Contrairement à LUIS, vous ne pouvez pas étiqueter le même texte sous deux entités différentes. Les composants appris dans différentes entités s’excluent mutuellement et une seule étendue apprise est prédite pour chaque ensemble de caractères.

## <a name="can-i-import-a-luis-json-file-into-conversational-language-understanding"></a>Puis-je importer un fichier JSON LUIS dans Compréhension de langage courant ?

Oui, vous pouvez [importer n’importe quel fichier JSON d’application LUIS](./concepts/backwards-compatibility.md) à partir de la dernière version du service.

## <a name="can-i-import-a-luis-lu-file-into-conversational-language-understanding"></a>Puis-je importer un fichier `.LU` LUIS dans Compréhension de langage courant ?

Non, le service prend uniquement en charge le format JSON. Vous pouvez accéder à LUIS, importer le fichier `.LU` et l’exporter sous forme de fichier JSON. 

## <a name="is-there-any-sdk-support"></a>Existe-t-il un support du SDK ?

Oui, uniquement pour les prédictions et des [exemples sont disponibles](https://aka.ms/cluSampleCode). Actuellement, il n’y aucun support de création pour le SDK.

## <a name="are-there-apis-for-this-feature"></a>Existe-t-il des API pour cette fonctionnalité ?

Oui, toutes les API [sont disponibles](https://aka.ms/clu-apis).

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de Compréhension du langage courant](overview.md)
