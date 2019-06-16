---
title: Attributs de l’entité Série de conférence – API Connaissances universitaires
titlesuffix: Azure Cognitive Services
description: Découvrez les attributs que vous pouvez utiliser avec l’entité Série de conférence.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 38b4aa4c899668a68041f042ce6981ddd8c58219
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61340194"
---
# <a name="conference-series-entity"></a>Entité Série de conférences

<sub> *Les attributs suivants sont spécifiques à l’entité Série de conférence. (Ty = '3') </sub>

Nom    |Description                            |Type       | Opérations
------- | ------------------------------------- | --------- | ----------------------------
ID      |L’ID d’entité                              |Int64      |Égal à
CN      |Nom normalisé de la série de conférences      |Chaîne     |Égal à
DCN     |Nom d’affichage de la série de conférences         |Chaîne     |Aucun
CC      |Nombre total de citations de la série de conférences         |Int32      |Aucun  
ECC     |Estimation du nombre total de citations de la série de conférences   |Int32      |Aucun
F.FId   |Champ de l’ID d’entité de l’étude associé à la série de conférences |Int64  | Égal à
F.FN    |Champ du nom de l’étude associé à la série de conférences  | Equals,<br/>StartsWith
