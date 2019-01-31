---
title: Attributs d’entité Créateur – API Connaissances universitaires
titlesuffix: Azure Cognitive Services
description: Découvrez les attributs que vous pouvez utiliser avec l’entité Créateur dans l’API Connaissances universitaires.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 48758ac9ec8c993bbdb490229ae20fcce1fb0a49
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175177"
---
# <a name="author-entity"></a>Entité Auteur
<sub> *Les attributs suivants sont spécifiques à l’entité de l’auteur. (Ty = '1') </sub>

NOM    |Description                            |Type       | Opérations
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
