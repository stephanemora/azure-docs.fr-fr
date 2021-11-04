---
title: Prise en charge linguistique de Reconnaissance d’entité nommée (NER)
titleSuffix: Azure Cognitive Services
description: Cet article décrit les langages naturels pris en charge par la fonctionnalité NER d’Azure Cognitive Service for Language.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-ner, ignite-fall-2021
ms.openlocfilehash: 7f80599fe9b0a43b7b472bd6eff71ef11b62fe13
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097961"
---
# <a name="named-entity-recognition-ner-language-support"></a>Prise en charge linguistique de Reconnaissance d’entité nommée (NER) 

Utilisez cet article pour connaître les langages naturels pris en charge par la fonctionnalité NER d’Azure Cognitive Service for Language.

> [!NOTE]
> * Les langues sont ajoutées à mesure que de nouvelles [versions de modèle](how-to-call.md#specify-the-ner-model) sont publiées. 
> * Les entités « Person », « Location » et « Organization » sont retournées pour les autres langues marquées d’un astérisque *.
> * La version actuelle du modèle pour NER est `2021-06-01`.

## <a name="ner-language-support"></a>Prise en charge linguistique de NER

| Langage              | Code langue | À partir de la version de modèle : | Notes              |
|:----------------------|:-------------:|:----------------------------:|:------------------:|
| Arabe*               | `ar`          | 2019-10-01                   |                    |
| Chinois simplifié    | `zh-hans`     | 15-01-2021                   | `zh` également accepté |
| Chinois - traditionnel*  | `zh-hant`     | 2019-10-01                   |                    |
| Tchèque*                | `cs`          | 2019-10-01                   |                    |
| Danois*               | `da`          | 2019-10-01                   |                    |
| Néerlandais*                | `nl`          | 2019-10-01                   |                    |
| Anglais               | `en`          | 2019-10-01                   |                    |
| Finnois*              | `fi`          | 2019-10-01                   |                    |
| Français                | `fr`          | 15-01-2021                   |                    |
| Allemand                | `de`          | 15-01-2021                   |                    |
| Hébreu*               | `he`          | 2019-10-01                   |                    |
| Hongrois*            | `hu`          | 2019-10-01                   |                    |
| Italien               | `it`          | 15-01-2021                   |                    |
| Japonais              | `ja`          | 15-01-2021                   |                    |
| Coréen                | `ko`          | 15-01-2021                   |                    |
| Norvégien (Bokmål)*  | `no`          | 2019-10-01                   | `nb` également accepté |
| Polonais*               | `pl`          | 2019-10-01                   |                    |
| Portugais (Brésil)   | `pt-BR`       | 15-01-2021                   |                    |
| Portugais (Portugal) | `pt-PT`       | 15-01-2021                   | `pt` également accepté |
| Russe*              | `ru`          | 2019-10-01                   |                    |
| Espagnol               | `es`          | 2020-04-01                   |                    |
| Suédois*              | `sv`          | 2019-10-01                   |                    |
| Turc*              | `tr`          | 2019-10-01                   |                    |

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de la fonctionnalité PII](overview.md)
