---
title: Attributs d’entité Créateur – API Connaissances universitaires
titlesuffix: Azure Cognitive Services
description: Découvrez les attributs que vous pouvez utiliser avec l’entité Créateur dans l’API Connaissances universitaires.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: e63e9d3f8f17a2473caf3d31b83e318ddb132b43
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705088"
---
# <a name="author-entity"></a>Entité Auteur
<sub> *Les attributs suivants sont spécifiques à l’entité de l’auteur. (Ty = '1') </sub>

Nom    |Description                            |Type       | Opérations
------- | ------------------------------------- | --------- | ----------------------------
Id      |L’ID d’entité                              |Int64      |Égal à
AuN     |Nom de l’auteur normalisé                 |Chaîne     |Égal à
DAuN    |Nom d’affichage de l’auteur                    |Chaîne     |Aucun
CC      |Nombre total de citations de l’auteur            |Int32      |Aucun  
ECC     |Estimation du nombre total de citations de l’auteur  |Int32      |Aucun
E       |Métadonnées étendues (voir le tableau « Attributs de métadonnées étendues »)  |Chaîne     |Aucun  


## <a name="extended-metadata-attributes"></a>Attributs de métadonnées étendues ##

Nom    | Description               
--------|---------------------------    
LKA.Afn     | nom d’affichage de la collaboration associée à l’auteur  
LKA.AfId        | ID d’entité de la collaboration associée à l’auteur
