---
title: Exemples de scénarios utilisateur pour l’API Analyse de texte
titleSuffix: Azure Cognitive Services
description: Cet article présente quelques scénarios courants pour l’intégration de l’API Analyse de texte à vos services et processus.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: 027e6ec829e9de9956451e48e5f9e1cdd749f9f7
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689332"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Exemples de scénarios utilisateur pour l’API Analyse de texte

L’API Analyse de texte est un service informatique qui fournit un traitement en langage naturel avancé sur le texte. Cet article décrit certains exemples de cas d’utilisation pour l’intégration de l’API à vos solutions et processus métier. 

## <a name="analyze-survey-results"></a>Analyser les résultats d’une enquête

Retirez des insights des résultats d’une enquête portant sur les clients et les employés en traitant les réponses de texte brut à l’aide de l’analyse des sentiments. Agrégez les résultats pour l’analyse, le suivi et la stimulation de l’engagement.

![Image montrant comment effectuer une analyse des sentiments à partir d’enquêtes portant sur les clients et les employés](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Analyser les appels de clients entrants enregistrés

Retirez des insights des appels de services clients à l’aide de la synthèse vocale, de l’analyse des sentiments et de l’extraction de phrases clés. Affichez les résultats dans un tableau de bord Power BI ou un portail pour mieux comprendre les clients, dégager des tendances du service client et stimuler l’engagement des clients. Envoyez les demandes API sous forme de lot en vue de la création d’un rapport ou en temps réel en vue d’une intervention. Consultez l’exemple de code [sur GitHub](https://github.com/rlagh2/callcenteranalytics).

![Image montrant comment automatiser la récupération d’insights à partir des appels de service client à l’aide de l’analyse des sentiments](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Traiter et classer les incidents de support

Utilisez l’extraction de phrases clés et la reconnaissance d’entité pour traiter les demandes de support soumises dans un format de texte non structuré. Utilisez les entités et expressions extraites pour classer les demandes en vue d’une planification des ressources et d’une analyse des tendances.

![Image montrant comment utiliser l’extraction de phrases clés et la reconnaissance d’entité pour classer les rapports d’incidents et les tendances](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>Superviser les flux de médias sociaux de votre produit

Supervisez les commentaires relatifs à votre produit sur la page Facebook ou twitter qui lui est consacrée. Utilisez les données pour analyser les sentiments des clients dans la perspective du lancement de nouveaux produits, extraire les phrases clés liées aux caractéristiques et aux demandes de caractéristiques ou traiter les plaintes des clients à mesure qu’elles se produisent. Consultez l’exemple [Modèle Microsoft Flow](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/).

![Image montrant comment superviser les commentaires portant sur votre produit et votre entreprise sur les réseaux sociaux à l’aide de l’extraction de phrases clés](media/use-cases/social-feed.svg)

## <a name="next-steps"></a>Étapes suivantes

* [Qu’est-ce que l’API Analyse de texte ?](overview.md)
* [Envoyer une demande à l’API Analyse de texte à l’aide de la bibliothèque de client](quickstarts/text-analytics-sdk.md)
