---
title: Attributs d’entité Collaboration dans l’API Connaissances universitaires | Microsoft Docs
description: Découvrez les attributs que vous pouvez utiliser avec l’entité Collaboration dans l’API Connaissances universitaires de Cognitive Services.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: a0ec67cb811ca207b3d038028491da2516028f0b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35367821"
---
# <a name="affiliation-entity"></a>Entité Affiliation

<sub> *Les attributs suivants sont spécifiques à l’entité de la collaboration. (Ty = '5') </sub>

NOM    |Description                            |type       | Opérations
------- | ------------------------------------- | --------- | ----------------------------
ID      |L’ID d’entité                              |Int64      |Égal à
AfN     |Nom normalisé de la collaboration        |Chaîne     |Égal à
DAfN    |Nom d’affichage de la collaboration       |Chaîne     |Aucun
CC      |Nombre total de citations de la collaboration           |Int32      |Aucun  
ECC     |Estimation du nombre total de citations la collaboration |Int32      |Aucun

## <a name="extended-metadata-attributes"></a>Attributs de métadonnées étendues ##

NOM    | Description               
--------|---------------------------    
PC      |Nombre de publications de la collaboration