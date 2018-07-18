---
title: Attributs de l’entité Série de conférences dans l’API Connaissances universitaires | Microsoft Docs
description: Découvrez les attributs que vous pouvez utiliser avec l’entité Série de conférence dans Cognitive Services.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 332736c927bdaa00334546f626a6eabb8e11d3b5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35367829"
---
# <a name="conference-series-entity"></a>Entité Série de conférences

<sub> *Les attributs suivants sont spécifiques à l’entité Série de conférence. (Ty = '3') </sub>

NOM    |Description                            |type       | Opérations
------- | ------------------------------------- | --------- | ----------------------------
ID      |L’ID d’entité                              |Int64      |Égal à
CN      |Nom normalisé de la série de conférences      |Chaîne     |Égal à
DCN     |Nom d’affichage de la série de conférences         |Chaîne     |Aucun
CC      |Nombre total de citations de la série de conférences         |Int32      |Aucun  
ECC     |Estimation du nombre total de citations de la série de conférences   |Int32      |Aucun
F.FId   |Champ de l’ID d’entité de l’étude associé à la série de conférences |Int64  | Égal à
F.FN    |Champ du nom de l’étude associé à la série de conférences  | Equals,<br/>StartsWith