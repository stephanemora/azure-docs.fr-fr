---
title: Attributs de l’entité Série de conférence – API Connaissances universitaires
titlesuffix: Azure Cognitive Services
description: Découvrez les attributs que vous pouvez utiliser avec l’entité Série de conférence.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: f9f28afd7005d7a61aa0d2f4dba69ca598034b52
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900746"
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