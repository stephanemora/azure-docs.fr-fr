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
ms.openlocfilehash: c62044582cb488a5ef2d20b3f407c0865b3994ba
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877970"
---
> [!NOTE]
> Les exigences et les suggestions sont basées sur des tests d’évaluation effectués avec une seule requête par seconde, à partir d’une image de 8 Mo d’une lettre d’entreprise analysée qui contient 29 lignes et 803 caractères au total.

Le tableau suivant indique l’allocation de ressources minimale et recommandée pour chaque conteneur Lire.

| Conteneur | Minimum | Recommandé |TPS<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
| Lire | 1 cœur, 8 Go de mémoire, 0,24 TPS | 8 cœurs, 16 Go de mémoire, 1,17 TPS | 0,24 ; 1,17 |

* Chaque cœur doit être cadencé à au moins 2,6 gigahertz (GHz).
* TPS - transactions par seconde.

Le nombre de cœurs et la quantité de mémoire correspondent aux paramètres `--cpus` et `--memory` qui sont utilisés dans le cadre de la commande `docker run`.
