---
title: Attributs d’entité Créateur – API Connaissances universitaires
titlesuffix: Azure Cognitive Services
description: Découvrez les attributs que vous pouvez utiliser avec l’entité Créateur dans l’API Connaissances universitaires.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: d5fc770c380397f605f8810fa41d3a8907f2358e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146495"
---
# <a name="author-entity"></a>Entité Auteur

> [!NOTE]
> Les attributs suivants sont spécifiques à l’entité de l’auteur. (Ty = '1')

Nom | Description | Type | Opérations
--- | --- | --- | ---
Id      | L’ID d’entité                             |Int64      |Égal à
AuN     | Nom de l’auteur normalisé                    |Chaîne     |Égal à
CC      | Nombre total de citations de l’auteur           |Int32      |Aucun  
DAuN    | Nom d’affichage de l’auteur                   |Chaîne     |Aucun
E | Métadonnées étendues</br></br>**IMPORTANT** : Cet attribut est déprécié et uniquement pris en charge pour les applications héritées. Une demande individuelle de cet attribut (c’est-à-dire, attributes=Id,Ti,E) entraîne le retour de tous les attributs des métadonnées étendues dans une *chaîne JSON sérialisée*.</br></br>Tous les attributs contenus dans les métadonnées étendues sont désormais disponibles sous la forme d’un attribut de niveau supérieur et peuvent être demandés en tant que tel (c’est-à-dire, attributes=Id,Ti,DOI,IA). | [Étendu](#extended) | Aucun
ECC     | Estimation du nombre total de citations de l’auteur |Int32      |Aucun
LKA.AfId | ID d’entité de la dernière affiliation connue trouvée pour l’auteur | Int64 | Aucun
LKA.AfN | Nom normalisé de la dernière affiliation connue trouvée pour l’auteur | Chaîne | Aucun
PC | Nombre total de publications de l’auteur | Int32 | Aucun

## <a name="extended"></a>Étendu

> [!IMPORTANT]
> Cet attribut est déprécié et uniquement pris en charge pour les applications héritées. Une demande individuelle de cet attribut (c’est-à-dire, attributes=Id,Ti,E) entraîne le retour de tous les attributs des métadonnées étendues dans une *chaîne JSON sérialisée*.</br></br>Tous les attributs contenus dans les métadonnées étendues sont désormais disponibles sous la forme d’un attribut de niveau supérieur et peuvent être demandés en tant que tel (c’est-à-dire, attributes=Id,Ti,DOI,IA).

> [!IMPORTANT]
> Prise en charge de la demande d’attributs étendus individuels à l’aide de l’étendue « E. », c.-à-d. que « E.DN » est déprécié. Même si elle est toujours techniquement prise en charge, la demande d’attributs étendus individuels à l’aide de l’étendue « E. » entraîne le retour de la valeur d’attribut à deux emplacements dans la réponse JSON, à savoir en tant qu’élément de l’objet « E » et en tant qu’attribut de niveau supérieur.

Nom | Description | Type | Opérations
--- | --- | --- | ---
LKA.AfId | ID d’entité de la dernière affiliation connue trouvée pour l’auteur | Int64 | Aucun
LKA.AfN | Nom normalisé de la dernière affiliation connue trouvée pour l’auteur | Chaîne | Aucun
PC | Nombre total de publications de l’auteur | Int32 | Aucun
