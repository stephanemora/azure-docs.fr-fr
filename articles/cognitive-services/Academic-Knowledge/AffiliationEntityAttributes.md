---
title: Attributs d’entité Collaboration de l’API Connaissances universitaires
titlesuffix: Azure Cognitive Services
description: Découvrez les attributs que vous pouvez utiliser avec l’entité Collaboration de l’API Connaissances universitaires.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 82e6a5b66342e58e62da029d617cbd1d74c28149
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864364"
---
# <a name="affiliation-entity"></a>Entité Affiliation

<sub> *Les attributs suivants sont spécifiques à l’entité de la collaboration. (Ty = '5') </sub>

Nom    |Description                            |Type       | Opérations
------- | ------------------------------------- | --------- | ----------------------------
ID      |L’ID d’entité                              |Int64      |Égal à
AfN     |Nom normalisé de la collaboration        |Chaîne     |Égal à
DAfN    |Nom d’affichage de la collaboration       |Chaîne     |Aucun
CC      |Nombre total de citations de la collaboration           |Int32      |Aucun  
ECC     |Estimation du nombre total de citations la collaboration |Int32      |Aucun

## <a name="extended-metadata-attributes"></a>Attributs de métadonnées étendues ##

Nom    | Description               
--------|---------------------------    
PC      |Nombre de publications de la collaboration
