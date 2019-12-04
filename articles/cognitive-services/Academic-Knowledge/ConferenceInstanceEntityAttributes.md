---
title: Attributs de l’entité Instance de conférence - API Connaissances universitaires
titlesuffix: Azure Cognitive Services
description: Découvrez les attributs que vous pouvez utiliser avec l’entité Instance de conférence de l’API Connaissances universitaires.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: c6761206a58724dae96d9dc6f6234658892d4d18
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146568"
---
# <a name="conference-instance-entity"></a>Entité Instance de conférence

> [!NOTE]
> Les attributs suivants sont spécifiques à l’entité Instance de conférence. (Ty = '4')

Nom | Description | Type | Opérations
--- | --- | --- | ---
CC      |Nombre total de citations de l’instance de conférence           |Int32      |Aucun  
CD.D    |Date d’un événement d’instance de conférence    |Date       |Equals, IsBetween
CD.T    |Titre d’un événement d’instance de conférence   |Date       |Equals, IsBetween
CIARD   |Date d’échéance pour l’inscription de l’abstract de l’instance de conférence  |Date       |Equals, IsBetween
CIED    |Date de fin de l’instance de conférence    |Date       |Equals, IsBetween
CIFVD   |Date d’échéance pour la version finale de l’instance de conférence  |Date       |Equals, IsBetween
CIL     |Emplacement de l’instance de conférence    |Chaîne     |Equals, StartsWith
CIN     |Nom normalisé de l’instance de conférence |Chaîne        |Égal à
CINDD   |Date de notification de l’instance de conférence   |Date       |Equals, IsBetween
CISD    |Date de début de l’instance de conférence  |Date       |Equals, IsBetween
CISDD   |Date d’échéance pour l’envoi de l’instance de conférence     |Date       |Equals, IsBetween
DCN     |Nom d’affichage de l’instance de conférence  |Chaîne      |Aucun
E | Métadonnées étendues</br></br>**IMPORTANT** : Cet attribut est déprécié et uniquement pris en charge pour les applications héritées. Une demande individuelle de cet attribut (c’est-à-dire, attributes=Id,Ti,E) entraîne le retour de tous les attributs des métadonnées étendues dans une *chaîne JSON sérialisée*.</br></br>Tous les attributs contenus dans les métadonnées étendues sont désormais disponibles sous la forme d’un attribut de niveau supérieur et peuvent être demandés en tant que tel (c’est-à-dire, attributes=Id,Ti,DOI,IA). | [Étendu](#extended) | Aucun
ECC     |Estimation du nombre total de citations de l’instance de conférence |Int32      |Aucun
FN | Nom complet de l’instance de conférence | Chaîne | Aucun
Id      |L’ID d’entité                              |Int64      |Égal à
PC | Nombre total de publications de l’instance de conférence | Int32 | Aucun
PCS.CN  |Nom de la série de conférences parent de l’instance |Chaîne  |Égal à
PCS.CId |ID de la série de conférences parent de l’instance |Int64     |Égal à

## <a name="extended"></a>Étendu

> [!IMPORTANT]
> Cet attribut est déprécié et uniquement pris en charge pour les applications héritées. Une demande individuelle de cet attribut (c’est-à-dire, attributes=Id,Ti,E) entraîne le retour de tous les attributs des métadonnées étendues dans une *chaîne JSON sérialisée*.</br></br>Tous les attributs contenus dans les métadonnées étendues sont désormais disponibles sous la forme d’un attribut de niveau supérieur et peuvent être demandés en tant que tel (c’est-à-dire, attributes=Id,Ti,DOI,IA).

> [!IMPORTANT]
> Prise en charge de la demande d’attributs étendus individuels à l’aide de l’étendue « E. », c.-à-d. que « E.DN » est déprécié. Même si elle est toujours techniquement prise en charge, la demande d’attributs étendus individuels à l’aide de l’étendue « E. » entraîne le retour de la valeur d’attribut à deux emplacements dans la réponse JSON, à savoir en tant qu’élément de l’objet « E » et en tant qu’attribut de niveau supérieur.

Nom | Description | Type | Opérations
--- | --- | --- | ---
FN | Nom complet de l’instance de conférence | Chaîne | Aucun
PC | Nombre total de publications de l’instance de conférence | Int32 | Aucun
