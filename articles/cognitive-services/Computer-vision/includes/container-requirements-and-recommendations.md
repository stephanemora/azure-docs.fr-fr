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
ms.openlocfilehash: 81e86eda454bdbd262f6c38a4766ee2a899d225d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122336031"
---
> [!NOTE]
> Les exigences et les suggestions sont basées sur des tests d’évaluation effectués avec une seule requête par seconde, à partir d’une image de 523 Mo d’une lettre d’entreprise analysée qui contient 29 lignes et 803 caractères au total. La configuration recommandée a entraîné une réponse environ 2 fois plus rapide par rapport à la configuration minimale.

Le tableau suivant décrit l'allocation de ressources minimale et recommandée pour chaque conteneur OCR Read.

| Conteneur | Minimum | Recommandé |
|-----------|---------|-------------|
| Read 2.0 (préversion) | 1 cœur, 8 Go de mémoire |    8 cœurs, 16 Go de mémoire |
| Read 3.2 | 4 cœurs, 16 Go de mémoire | 8 cœurs, 24 Go de mémoire |

* Chaque cœur doit être cadencé à au moins 2,6 gigahertz (GHz).

Le nombre de cœurs et la quantité de mémoire correspondent aux paramètres `--cpus` et `--memory` qui sont utilisés dans le cadre de la commande `docker run`.
