---
title: Attributs de l’entité Academic Graph pour l’API Connaissances universitaires | Microsoft Docs
description: Découvrez les attributs d’entité que vous pouvez utiliser avec Academic Graph dans l’API Connaissances universitaires.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: f771969c3c6f05e5d2c99b1fbf593ae039ccb12c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35367820"
---
# <a name="entity-attributes"></a>Attributs d’entité

Le graphique académique est composé de 7 types d’entité. Toutes les entités se verront attribuer un ID d’entité et un type d'entité.

## <a name="common-entity-attributes"></a>Attributs d’entité courants
NOM    |Description                |type       | Opérations
------- | ------------------------- | --------- | ----------------------------
ID      |L’ID d’entité                  |Int64      |Égal à
Ty      |Type d’entité                |enum   |Égal à

## <a name="entity-type-enum"></a>Entity type enum
NOM                                                            |value
----------------------------------------------------------------|-----
[Article](PaperEntityAttributes.md)                               |0
[Auteur](AuthorEntityAttributes.md)                             |1
[Journal](JournalEntityAttributes.md)                           |2
[Séries de conférences](JournalEntityAttributes.md)                 |3
[Instance de conférence](ConferenceInstanceEntityAttributes.md)    |4
[Affiliation](AffiliationEntityAttributes.md)                   |5
[Champ d’étude](FieldsOfStudyEntityAttributes.md)                      |6.

