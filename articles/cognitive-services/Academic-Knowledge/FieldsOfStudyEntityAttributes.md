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
ms.openlocfilehash: e9d6badf76efd03c0520a728af7b3e47b25f200a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61339583"
---
# <a name="field-of-study-entity"></a>Entité Champ d’étude

<sub> *Les attributs suivants sont spécifiques à l’entité du champ d’étude. (Ty = '6') </sub>

Nom    |Description                            |Type       | Opérations
------- | ------------------------------------- | --------- | ----------------------------
ID      |L’ID d’entité                              |Int64      |Égal à
FN      |Nom normalisé du champ d’étude         |String     |Égal à
DFN     |Nom d'affichage du champ d’étude            |String     |Aucun
CC      |Nombre de citations total du champ d’étude    |Int32      |Aucun  
ECC     |Estimation du nombre total de citations du champ d’étude|Int32      |Aucun
FL      |Niveau dans la hiérarchie du champ d’étude     |Int32      |Equals, <br/>IsBetween
FP.FN   |Nom du champ d’étude parent             |String     |Égal à
FP.FId  |ID du champ d’étude parent               |Int64      |Égal à
FC.FN   |Nom du champ d’étude enfant              |String     |Égal à
FC.FId  |ID du champ d’étude enfant                |Int64      |Égal à
