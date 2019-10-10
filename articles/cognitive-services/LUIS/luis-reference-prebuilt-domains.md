---
title: Référence de domaine prédéfini - LUIS
titleSuffix: Azure Cognitive Services
description: Référence pour les domaines prédéfinis, qui sont des collections prédéfinies d’intentions et d’entités de LUIS (Language Understanding Intelligent Service).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 17d72ad51974bb0fb741ae19ebb19f313e646c62
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677650"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Référence de domaine prédéfinie pour votre application LUIS
Cette référence fournit des informations sur les [domaines prédéfinis](luis-how-to-use-prebuilt-domains.md), qui sont des collections prédéfinies d’intentions et d’entités proposées par LUIS.

Les [domaines personnalisés](luis-how-to-start-new-app.md), en revanche, commencent sans intentions ni modèles. Vous pouvez ajouter des intentions et entités de domaine prédéfini à un modèle personnalisé.

## <a name="custom-domains-per-language"></a>Domaines personnalisés par langue

Le tableau ci-dessous résume les domaines actuellement pris en charge. La prise en charge de l’anglais est généralement plus complète que les autres langues. 

| Type d’entité       | EN-US      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| Calendrier  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
|Communication  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Email     | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| HomeAutomation          | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Notes     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Lieux   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| RestaurantReservation  | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| ToDo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Services      | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Météo        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |
| Web    | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | ✓  | ✓      | ✓    | ✓    | ✓     | ✓  |

Les domaines prédéfinis ne sont pas **pris en charge** en :

* Français (Canada)
* Hindi
* Espagnol (Mexique)

## <a name="next-steps"></a>Étapes suivantes

Découvrez l’[entité simple](reference-entity-simple.md). 