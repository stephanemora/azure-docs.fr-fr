---
title: Exigences et suggestions relatives au conteneur
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/09/2021
ms.author: aahi
ms.openlocfilehash: 5cbf07ab97206ae3509701a6d6b0f71e961b0bf8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308769"
---
> [!NOTE]
> Les exigences et les suggestions sont basées sur des tests d’évaluation effectués avec une seule requête par seconde, à partir d’une image de 8 Mo d’une lettre d’entreprise analysée qui contient 29 lignes et 803 caractères au total.

Le tableau suivant décrit l'allocation de ressources minimale et recommandée pour chaque conteneur OCR Read.

| Conteneur | Minimum | Recommandé |
|-----------|---------|-------------|
| Read 2.0 (préversion) | 1 cœur, 8 Go de mémoire |    8 cœurs, 16 Go de mémoire |
| Read 3.2 | 8 cœurs, 16 Go de mémoire | 8 cœurs, 24 Go de mémoire |

* Chaque cœur doit être cadencé à au moins 2,6 gigahertz (GHz).

Le nombre de cœurs et la quantité de mémoire correspondent aux paramètres `--cpus` et `--memory` qui sont utilisés dans le cadre de la commande `docker run`.
