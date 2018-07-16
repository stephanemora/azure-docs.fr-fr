---
title: Attributs d’entité Champ d’étude dans l’API Connaissances universitaires | Microsoft Docs
description: Découvrez les attributs que vous pouvez utiliser avec l’entité Champ d’étude dans l’API Connaissances universitaires de Cognitive Services.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: a8176370f5b2f63b7741f7e892ed5a8c775f19c1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35367828"
---
# <a name="field-of-study-entity"></a>Entité Champ d’étude

<sub> *Les attributs suivants sont spécifiques à l’entité du champ d’étude. (Ty = '6') </sub>

NOM    |Description                            |type       | Opérations
------- | ------------------------------------- | --------- | ----------------------------
ID      |L’ID d’entité                              |Int64      |Égal à
FN      |Nom normalisé du champ d’étude         |Chaîne     |Égal à
DFN     |Nom d'affichage du champ d’étude            |Chaîne     |Aucun
CC      |Nombre de citations total du champ d’étude    |Int32      |Aucun  
ECC     |Estimation du nombre total de citations du champ d’étude|Int32      |Aucun
FL      |Niveau dans la hiérarchie du champ d’étude     |Int32      |Equals, <br/>IsBetween
FP.FN   |Nom du champ d’étude parent             |Chaîne     |Égal à
FP.FId  |ID du champ d’étude parent               |Int64      |Égal à
FC.FN   |Nom du champ d’étude enfant              |Chaîne     |Égal à
FC.FId  |ID du champ d’étude enfant                |Int64      |Égal à