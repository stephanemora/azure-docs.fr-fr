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
ms.openlocfilehash: 77b75b1134bbc8366478b1f9f4d14e86e9684f70
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2020
ms.locfileid: "91709310"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Catégories d’entité prises en charge dans l’API Analyse de texte v3

Utilisez cet article pour rechercher les catégories d’entité qui peuvent être retournées par la [Reconnaissance d’entité nommée](how-tos/text-analytics-how-to-entity-linking.md) (NER). Une préversion de NER v3.1 est également disponible, elle permet de détecter les informations personnelles (`PII`) et médicales (`PHI`). Cliquez également sur l’onglet **Intégrité** pour voir la liste des catégories prises en charge dans l’Analyse de texte pour l’intégrité.

## <a name="entity-categories"></a>Catégories d’entité

#### <a name="general"></a>[Généralités](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="pii"></a>[PII](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[Intégrité](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

***

## <a name="next-steps"></a>Étapes suivantes

* [Comment utiliser une reconnaissance d’entité nommée dans Analyse de texte](how-tos/text-analytics-how-to-entity-linking.md)
