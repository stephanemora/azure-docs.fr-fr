---
title: Attributs de l’entité Champ d’étude – API Connaissances universitaires
titlesuffix: Azure Cognitive Services
description: Découvrez les attributs que vous pouvez utiliser avec l’entité Champ d’étude dans l’API Connaissances universitaires.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: b6bb5e0fd822541a0a4c10ef5f275d46664f8e10
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146479"
---
# <a name="field-of-study-entity"></a>Entité Champ d’étude

> [!NOTE]
> Les attributs suivants sont spécifiques à l’entité du champ d’étude. (Ty = '6')

Nom | Description | Type | Opérations
--- | --- | --- | ---
CC      |Nombre de citations total du champ d’étude    |Int32      |Aucun  
DFN     |Nom d'affichage du champ d’étude            |Chaîne     |Aucun
ECC     |Estimation du nombre total de citations du champ d’étude|Int32      |Aucun
FL      |Niveau dans la hiérarchie du champ d’étude     |Int32      |Equals, IsBetween
FN      |Nom normalisé du champ d’étude         |Chaîne     |Égal à
FC.FId  |ID du champ d’étude enfant                |Int64      |Égal à
FC.FN   |Nom du champ d’étude enfant              |Chaîne     |Égal à
FP.FId  |ID du champ d’étude parent               |Int64      |Égal à
FP.FN   |Nom du champ d’étude parent             |Chaîne     |Égal à
Id      |L’ID d’entité                              |Int64      |Égal à
PC    | Nombre total de publications du champ d’étude | Int32 | Aucun
