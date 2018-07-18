---
title: Attributs d’entité Auteur dans l’API Connaissances universitaires | Microsoft Docs
description: Découvrez les attributs que vous pouvez utiliser avec l’entité Auteur dans l’API Connaissances universitaires de Cognitive Services.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: afcc3257c49522a4631c4aae0e0c2b88373f9af1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35367824"
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