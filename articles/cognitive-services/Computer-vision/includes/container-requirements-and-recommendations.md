---
title: Exigences et suggestions relatives au conteneur
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 47c889a428ec2916dc8cad73e30ef5ff630372a7
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129817"
---
> [!NOTE]
> Les exigences et les suggestions sont basées sur des tests d’évaluation effectués avec une seule requête par seconde, à partir d’une image de 8 Mo d’une lettre d’entreprise analysée qui contient 29 lignes et 803 caractères au total.

#### <a name="readtabread"></a>[Lire](#tab/read)

Le tableau suivant indique l’allocation de ressources minimale et recommandée pour chaque conteneur Lire.

| Conteneur | Minimale | Recommandé |TPS<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
| Lire | 1 cœur, 8 Go de mémoire, 0,24 TPS | 8 cœurs, 16 Go de mémoire, 1,17 TPS | 0,24 ; 1,17 |

#### <a name="recognize-texttabrecognize-text"></a>[Reconnaître le texte](#tab/recognize-text)

Le tableau suivant indique l’allocation de ressources minimale et recommandée pour chaque conteneur Reconnaître le texte.

| Conteneur | Minimale | Recommandé |TPS<br>(Minimum, Maximum)|
|-----------|---------|-------------|--|
| Reconnaître le texte | 1 cœur, 8 Go de mémoire, 0,12 TPS | 8 cœurs, 16 Go de mémoire, 0,60 TPS | 0,12 ; 0,60 |

***

* Chaque cœur doit être cadencé à au moins 2,6 gigahertz (GHz).
* TPS - transactions par seconde.

Le nombre de cœurs et la quantité de mémoire correspondent aux paramètres `--cpus` et `--memory` qui sont utilisés dans le cadre de la commande `docker run`.
