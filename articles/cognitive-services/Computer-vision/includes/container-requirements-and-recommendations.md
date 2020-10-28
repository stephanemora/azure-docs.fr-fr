---
title: Exigences et suggestions relatives au conteneur
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a441f677687789729b96011f8bf98606418ca659
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677203"
---
> [!NOTE]
> Les exigences et les suggestions sont basées sur des tests d’évaluation effectués avec une seule requête par seconde, à partir d’une image de 8 Mo d’une lettre d’entreprise analysée qui contient 29 lignes et 803 caractères au total.

Le tableau suivant indique l’allocation de ressources minimale et recommandée pour chaque conteneur Lire.

| Conteneur | Minimum | Recommandé |
|-----------|---------|-------------|
| Read 2.0 (préversion) | 1 cœur, 8 Go de mémoire |  8 cœurs, 16 Go de mémoire |
| Read 3.1-preview | 8 cœurs, 16 Go de mémoire | 8 cœurs, 24 Go de mémoire |

* Chaque cœur doit être cadencé à au moins 2,6 gigahertz (GHz).

Le nombre de cœurs et la quantité de mémoire correspondent aux paramètres `--cpus` et `--memory` qui sont utilisés dans le cadre de la commande `docker run`.
