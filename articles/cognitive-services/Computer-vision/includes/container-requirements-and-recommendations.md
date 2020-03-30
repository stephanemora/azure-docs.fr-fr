---
title: Exigences et suggestions relatives au conteneur
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/20/2019
ms.author: dapine
ms.openlocfilehash: 2fa97a3a18741f506e10e5e4d61da286db3f744c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481776"
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
