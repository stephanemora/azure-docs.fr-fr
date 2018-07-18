---
title: Attributs d’entité Journal dans l’API Connaissances universitaires | Microsoft Docs
description: Découvrez les attributs que vous pouvez utiliser avec l’entité Journal dans l’API Connaissances universitaires de Cognitive Services.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: e782c57b8ac57028e070c16382c53ec76666e94d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35367817"
---
# <a name="journal-entity"></a>Entité de journal

<sub> *Les attributs suivants sont spécifiques à l’entité du journal. (Ty = '2') </sub>

NOM    |Description                            |type       | Opérations
------- | ------------------------------------- | --------- | ----------------------------
ID      |L’ID d’entité                              |Int64      |Égal à
DJN     |Nom du journal normalisé                |Chaîne     |Aucun
JN      |Nom d’affichage du journal                   |Chaîne     |Égal à
CC      |Nombre total de citations du journal           |Int32      |Aucun  
ECC     |Estimation du nombre total de citations du journal |Int32      |Aucun