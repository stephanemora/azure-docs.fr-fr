---
title: Entités nommées générales
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: aahi
ms.openlocfilehash: e81a1de02c112abd5c52f0f83404a615d738e01c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506888"
---
## <a name="general-entity-types"></a>Types d’entités générales

### <a name="person"></a>Personne
Noms reconnus et autres personnes dans le texte.
Langages :
* Préversion publique : `English`

| Nom de sous-type | Description             |
|--------------|-------------------------|
| N/A          | Noms reconnus, par exemple `Bill Gates`, `Marie Curie` |

### <a name="location"></a>Location

Points de repère, structures et caractéristiques géographiques naturels et créés par l’homme.

Langages :


* Préversion publique : `English`

| Nom de sous-type | Description                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| N/A          | emplacements, par exemple `Atlantic Ocean`, `library`, `Eiffel Tower`, `Statue of Liberty` |

### <a name="organization"></a>Organisation  

Organisations, entreprises, agences et autres groupes de personnes reconnus. Par exemple : sociétés, groupes politiques, bandes musicales, clubs de sport, organismes gouvernementaux et organisations publiques. Les nationalités et les religions ne sont pas incluses dans ce type d’entité. Langages : 

* Préversion publique : `English`

| Nom de sous-type | Description                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| N/A          | les organisations, par exemple `Microsoft`, `NASA` `National Oceanic and Atmospheric Administration` |

### <a name="phone-number"></a>Numéro de téléphone

Numéros de téléphone 

Langages :


* Préversion publique : `English`

| Nom de sous-type | Description                                  |
|----------|----------------------------------------------|
| N/A         | Les numéros de téléphone, par exemple `+1 123-123-123`. |

### <a name="url"></a>URL

URL Internet

Langages :


* Préversion publique : `English`

| Nom de sous-type | Description                                           |
|----------|-------------------------------------------------------|
| N/A         | URL de sites web, par exemple `https://www.bing.com`. |

###  <a name="number"></a>Number

Nombres et quantités numériques. 

Langages :


* Préversion publique : `English`

| Nom de sous-type    | Exemples                     |
|-------------|------------------------------|
| N/A         | `6`, `six`                   |
| Pourcentage  | `50%`, `fifty percent`       |
| Ordinal     | `2nd`, `second`              |
| Devise    | `$10.99`, `€30.00`           |
| Dimension   | `10 miles`, `40 cm`          |
| Température | `32 degrees`, `10°C`         |
