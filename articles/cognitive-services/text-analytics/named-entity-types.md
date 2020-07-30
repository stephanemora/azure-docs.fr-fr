---
title: Catégories prises en charge pour la reconnaissance d’entité nommée
titleSuffix: Azure Cognitive Services
description: Découvrez plus d’informations sur les catégories d’entité prises en charge dans l’API Analyse de texte.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: f66ed9a7d26a0c2d7def7d17820379b8e3460460
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373065"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Catégories d’entité prises en charge dans l’API Analyse de texte v3

Utilisez cet article pour rechercher les catégories d’entité qui peuvent être retournées par la [Reconnaissance d’entité nommée](how-tos/text-analytics-how-to-entity-linking.md) (NER). Une préversion de NER v3.1 est également disponible, elle permet de détecter les informations personnelles (`PII`) et médicales (`PHI`). Cliquez également sur l’onglet **Intégrité** pour voir la liste des catégories prises en charge dans l’Analyse de texte pour l’intégrité.

## <a name="entity-categories"></a>Catégories d’entité

#### <a name="general"></a>[Généralités](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="personal"></a>[Données personnelles](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[Intégrité](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

***

## <a name="next-steps"></a>Étapes suivantes

* [Comment utiliser une reconnaissance d’entité nommée dans Analyse de texte](how-tos/text-analytics-how-to-entity-linking.md)
