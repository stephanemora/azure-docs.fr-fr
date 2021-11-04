---
title: Présentation de l’analyse des sentiments et de l’exploration des opinions dans Azure Cognitive Service for Language
titleSuffix: Azure Cognitive Services
description: Vue d’ensemble de la fonctionnalité d’analyse des sentiments d’Azure Cognitive Services, qui aide à identifier ce que les gens pensent d’un thème donné en explorant du texte pour trouver des indices.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-sentiment-opinion-mining, ignite-fall-2021
ms.openlocfilehash: 8807b12f9920fd62901aebed862ecba483e77bb0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097504"
---
# <a name="what-is-sentiment-analysis-and-opinion-mining-in-azure-cognitive-service-for-language"></a>Présentation de l’analyse des sentiments et de l’exploration des opinions dans Azure Cognitive Service for Language

L’analyse des sentiments et l’exploration des opinions constituent des fonctionnalités proposées par [Azure Cognitive Service for Language](../overview.md), un ensemble d’algorithmes de Machine Learning et d’IA dans le cloud pour le développement d’applications intelligentes qui impliquent du langage écrit. Ces fonctionnalités vous aident à identifier ce que pensent les gens de votre marque ou d’un thème donné en explorant du texte pour trouver des indices sur le sentiment (positif ou négatif). Elles peuvent les associer à des aspects spécifiques du texte. 

* Les [**Démarrages rapides**](quickstart.md) sont des instructions de prise en main qui vous guident dans la formulation de vos requêtes au service.
* Les [**Guides pratiques**](how-to/call-api.md) contiennent des instructions sur l’utilisation du service de manière plus spécifique ou personnalisée.

## <a name="sentiment-analysis"></a>analyse de sentiments 

La fonctionnalité d’analyse des sentiments fournit des étiquettes de sentiment (par exemple « négatif », « neutre » et « positif ») en fonction du score de confiance le plus élevé trouvé par le service au niveau de la phrase et du document. Cette fonctionnalité retourne également des scores de confiance compris entre 0 et 1 pour chaque document, ainsi que les phrases qu’il contient pour les sentiments positif, neutre et négatif. 

### <a name="deploy-on-premises-using-docker-containers"></a>Déployer localement en utilisant des conteneurs Docker

Utilisez le conteneur Docker disponible pour [déployer l’analyse des sentiments en local](how-to/use-containers.md). Ces conteneurs Docker vous donnent la possibilité de rapprocher le service de vos données à des fins de conformité, de sécurité ou pour d’autres raisons opérationnelles.

## <a name="opinion-mining"></a>Exploration des opinions

L’exploration des opinions constitue une fonctionnalité de l’analyse des sentiments. Également connue sous le nom d’analyse des sentiments basée sur l’aspect dans le domaine du traitement automatique des langues, cette fonctionnalité fournit des informations plus précises sur les opinions liées aux mots (notamment les attributs de produits ou de services) dans le texte.

[!INCLUDE [Typical workflow for pre-configured language features](../includes/overview-typical-workflow.md)]

## <a name="responsible-ai"></a>Intelligence artificielle responsable 

Un système d’IA comprend non seulement la technologie, mais aussi ses utilisateurs, les personnes concernées et l’environnement dans lequel elle est déployée. Pour plus d’informations sur l’utilisation et le déploiement d’une IA responsable dans vos systèmes, consultez la [Note de transparence pour l’analyse des sentiments](/legal/cognitive-services/language-service/transparency-note-sentiment-analysis?context=/azure/cognitive-services/language-service/context/context). Vous pouvez également consulter les articles suivants pour en savoir plus :

[!INCLUDE [Responsible AI links](../includes/overview-responsible-ai-links.md)]

## <a name="next-steps"></a>Étapes suivantes

Il existe deux façons de commencer à utiliser la fonctionnalité de liaison d’entités :
* [Language Studio](../language-studio.md), qui est une plateforme basée sur le web qui vous permet d’essayer plusieurs fonctionnalités Azure Cognitive Service for Language sans avoir à écrire de code.
* L’[article de démarrage rapide](quickstart.md) pour obtenir des instructions sur la création de requêtes au service à l’aide de l’API REST et du kit SDK de la bibliothèque de client.  
