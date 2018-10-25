---
title: Attributs d’entité Créateur – API Connaissances universitaires
titlesuffix: Azure Cognitive Services
description: Découvrez les attributs que vous pouvez utiliser avec l’entité Créateur dans l’API Connaissances universitaires.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 62e6da6d558a0494fb83115b1e307081099399d4
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900685"
---
# <a name="author-entity"></a>Entité Auteur
<sub> *Les attributs suivants sont spécifiques à l’entité de l’auteur. (Ty = '1') </sub>

NOM    |Description                            |type       | Opérations
------- | ------------------------------------- | --------- | ----------------------------
ID      |L’ID d’entité                              |Int64      |Égal à
AuN     |Nom de l’auteur normalisé                 |Chaîne     |Égal à
DAuN    |Nom d’affichage de l’auteur                    |Chaîne     |Aucun
CC      |Nombre total de citations de l’auteur            |Int32      |Aucun  
ECC     |Estimation du nombre total de citations de l’auteur  |Int32      |Aucun
E       |Métadonnées étendues (voir le tableau « Attributs de métadonnées étendues »)  |Chaîne     |Aucun  


## <a name="extended-metadata-attributes"></a>Attributs de métadonnées étendues ##

NOM    | Description               
--------|---------------------------    
LKA.Afn     | nom d’affichage de la collaboration associée à l’auteur  
LKA.AfId        | ID d’entité de la collaboration associée à l’auteur