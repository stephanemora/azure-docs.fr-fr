---
title: Spécifier la version du modèle dans Analyse de texte v3
titleSuffix: Azure Cognitive Services
description: Découvrez comment spécifier le modèle d’API Analyse de texte utilisé sur vos données.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 04/21/2020
ms.author: aahi
ms.openlocfilehash: 9431ff862dd987a1a806087053014e7c880bf801
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84143258"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>Contrôle de version de modèle dans l’API Analyse de texte

La version 3 de l’API Analyse de texte vous permet de choisir la version de modèle utilisée sur vos données. Utilisez le paramètre facultatif `model-version` pour sélectionner la version du modèle dans vos demandes d’API. Par exemple : `<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`. Si ce paramètre n’est pas spécifié, l’API a la dernière version stable par défaut. 

## <a name="available-versions"></a>Versions disponibles

Utilisez le tableau ci-dessous pour rechercher les versions de modèle prises en charge par chaque point de terminaison.


| Point de terminaison                        | Versions prises en charge                       | version la plus récente |
|---------------------------------|------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`               | `2020-04-01`   |
| `/languages`                    | `2019-10-01`                             | `2019-10-01`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`               | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01` | `2020-04-01`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01` | `2020-04-01`   |
| `/keyphrases`                   | `2019-10-01`                             | `2019-10-01`   |


Pour plus d’informations sur les mises à jour de ces modèles, consultez la section [Nouveautés](../whats-new.md).

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble d’Analyse de texte](../overview.md)
* [Analyse des sentiments](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Reconnaissance d’entités](../how-tos/text-analytics-how-to-entity-linking.md)