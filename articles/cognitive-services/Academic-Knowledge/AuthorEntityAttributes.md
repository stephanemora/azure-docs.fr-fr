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
ms.openlocfilehash: d4b33c06ab023023aadf403cf0ef0b08c2bafc5f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60609759"
---
# <a name="author-entity"></a>Entité Auteur
<sub> *Les attributs suivants sont spécifiques à l’entité de l’auteur. (Ty = '1') </sub>

Nom    |Description                            |Type       | Opérations
------- | ------------------------------------- | --------- | ----------------------------
ID      |L’ID d’entité                              |Int64      |Égal à
AuN     |Nom de l’auteur normalisé                 |String     |Égal à
DAuN    |Nom d’affichage de l’auteur                    |String     |Aucun
CC      |Nombre total de citations de l’auteur            |Int32      |Aucun  
ECC     |Estimation du nombre total de citations de l’auteur  |Int32      |Aucun
E       |Métadonnées étendues (voir le tableau « Attributs de métadonnées étendues »)  |String     |Aucun  


## <a name="extended-metadata-attributes"></a>Attributs de métadonnées étendues ##

Nom    | Description               
--------|---------------------------    
LKA.Afn     | nom d’affichage de la collaboration associée à l’auteur  
LKA.AfId        | ID d’entité de la collaboration associée à l’auteur
