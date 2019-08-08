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
ROBOTS: NOINDEX
ms.openlocfilehash: 333875472d9b859196c4d828061301b9d32c4d5a
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705110"
---
# <a name="affiliation-entity"></a>Entité Affiliation

<sub> *Les attributs suivants sont spécifiques à l’entité de la collaboration. (Ty = '5') </sub>

Nom    |Description                            |Type       | Opérations
------- | ------------------------------------- | --------- | ----------------------------
Id      |L’ID d’entité                              |Int64      |Égal à
AfN     |Nom normalisé de la collaboration        |Chaîne     |Égal à
DAfN    |Nom d’affichage de la collaboration       |Chaîne     |Aucun
CC      |Nombre total de citations de la collaboration           |Int32      |Aucun  
ECC     |Estimation du nombre total de citations la collaboration |Int32      |Aucun

## <a name="extended-metadata-attributes"></a>Attributs de métadonnées étendues ##

Nom    | Description               
--------|---------------------------    
PC      |Nombre de publications de la collaboration
