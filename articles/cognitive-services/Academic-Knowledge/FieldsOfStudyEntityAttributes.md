---
title: Attributs de l’entité Champ d’étude – API Connaissances universitaires
titlesuffix: Azure Cognitive Services
description: Découvrez les attributs que vous pouvez utiliser avec l’entité Champ d’étude dans l’API Connaissances universitaires.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: d54f3c72462e6702b09068092b7c18ea50f12048
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704988"
---
# <a name="field-of-study-entity"></a>Entité Champ d’étude

<sub> *Les attributs suivants sont spécifiques à l’entité du champ d’étude. (Ty = '6') </sub>

Nom    |Description                            |Type       | Opérations
------- | ------------------------------------- | --------- | ----------------------------
Id      |L’ID d’entité                              |Int64      |Égal à
FN      |Nom normalisé du champ d’étude         |Chaîne     |Égal à
DFN     |Nom d'affichage du champ d’étude            |Chaîne     |Aucun
CC      |Nombre de citations total du champ d’étude    |Int32      |Aucun  
ECC     |Estimation du nombre total de citations du champ d’étude|Int32      |Aucun
FL      |Niveau dans la hiérarchie du champ d’étude     |Int32      |Equals, <br/>IsBetween
FP.FN   |Nom du champ d’étude parent             |Chaîne     |Égal à
FP.FId  |ID du champ d’étude parent               |Int64      |Égal à
FC.FN   |Nom du champ d’étude enfant              |Chaîne     |Égal à
FC.FId  |ID du champ d’étude enfant                |Int64      |Égal à
