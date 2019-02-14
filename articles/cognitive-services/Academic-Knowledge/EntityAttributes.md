---
title: Attributs d’entité Academic Graph – API Connaissances universitaires
titlesuffix: Azure Cognitive Services
description: Découvrez les attributs d’entité que vous pouvez utiliser avec Academic Graph dans l’API Connaissances universitaires.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: accc2803895f3892075cdd9877ca98344ab88bd1
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884815"
---
# <a name="entity-attributes"></a>Attributs d’entité

Le graphique académique est composé de 7 types d’entité. Toutes les entités se verront attribuer un ID d’entité et un type d'entité.

## <a name="common-entity-attributes"></a>Attributs d’entité courants
Nom    |Description                |Type       | Opérations
------- | ------------------------- | --------- | ----------------------------
ID      |L’ID d’entité                  |Int64      |Égal à
Ty      |Type d’entité                |enum   |Égal à

## <a name="entity-type-enum"></a>Entity type enum
Nom                                                            |value
----------------------------------------------------------------|-----
[Article](PaperEntityAttributes.md)                               |0
[Auteur](AuthorEntityAttributes.md)                             |1
[Journal](JournalEntityAttributes.md)                           |2
[Séries de conférences](JournalEntityAttributes.md)                 |3
[Instance de conférence](ConferenceInstanceEntityAttributes.md)    |4
[Affiliation](AffiliationEntityAttributes.md)                   |5.
[Champ d’étude](FieldsOfStudyEntityAttributes.md)                      |6.

