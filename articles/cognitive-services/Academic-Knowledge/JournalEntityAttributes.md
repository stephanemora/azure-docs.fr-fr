---
title: Attributs d’entité Journal – API Connaissances universitaires
titlesuffix: Azure Cognitive Services
description: Découvrez les attributs que vous pouvez utiliser avec l’entité Journal dans l’API Connaissances universitaires de Cognitive Services.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 45b2860b56e79c197d96998350131a142326157a
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902805"
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