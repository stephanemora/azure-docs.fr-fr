---
title: Attributs de l’entité Instance de conférence - API Connaissances universitaires
titlesuffix: Azure Cognitive Services
description: Découvrez les attributs que vous pouvez utiliser avec l’entité Instance de conférence de l’API Connaissances universitaires.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: db025f377a3fab2f788252db0c8e3555837a6de8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55196104"
---
# <a name="conference-instance-entity"></a>Entité Instance de conférence

<sub> * Les attributs suivants sont spécifiques à l’entité Instance de conférence. (Ty = '4') </sub>

NOM    |Description                            |Type       | Opérations
------- | ------------------------------------- | --------- | ----------------------------
ID      |L’ID d’entité                              |Int64      |Égal à
CIN     |Nom normalisé de l’instance de conférence ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |Chaîne     |Égal à
DCN     |Nom d’affichage de l’instance de conférence ({ConferenceSeriesName} : {ConferenceInstanceYear})       |Chaîne     |Aucun
CIL     |Emplacement de l’instance de conférence    |Chaîne     |Equals,<br/>StartsWith
CISD    |Date de début de l’instance de conférence  |Date       |Equals,<br/>IsBetween
CIED    |Date de fin de l’instance de conférence    |Date       |Equals,<br/>IsBetween
CIARD   |Date d’échéance pour l’inscription de l’abstract de l’instance de conférence  |Date       |Equals,<br/>IsBetween
CISDD   |Date d’échéance pour l’envoi de l’instance de conférence     |Date       |Equals,<br/>IsBetween
CIFVD   |Date d’échéance pour la version finale de l’instance de conférence  |Date       |Equals,<br/>IsBetween
CINDD   |Date de notification de l’instance de conférence   |Date       |Equals,<br/>IsBetween
CD.T    |Titre d’un événement d’instance de conférence   |Date       |Equals,<br/>IsBetween
CD.D    |Date d’un événement d’instance de conférence    |Date       |Equals,<br/>IsBetween
PCS.CN  |Nom de la série de conférences de l’instance |Chaîne     |Égal à
PCS.CId |ID de la série de conférences de l’instance |Int64    |Égal à
CC      |Nombre total de citations de l’instance de conférence           |Int32      |Aucun  
ECC     |Estimation du nombre total de citations de l’instance de conférence |Int32      |Aucun


## <a name="extended-metadata-attributes"></a>Attributs de métadonnées étendues ##

NOM    | Description               
--------|---------------------------    
FN      | Nom complet de l’instance de conférence
