---
title: Attributs d’entité Collaboration de l’API Connaissances universitaires
titlesuffix: Azure Cognitive Services
description: Découvrez les attributs que vous pouvez utiliser avec l’entité Collaboration de l’API Connaissances universitaires.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 177fe9da8bbe821a69eae02d89a225e5d4009331
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900476"
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