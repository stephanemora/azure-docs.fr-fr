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
ms.date: 08/11/2021
ms.author: aahi
ms.openlocfilehash: 9d63fc9f90408fd60331f77079fdd8f15ca4aad6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524524"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Catégories d’entité prises en charge dans l’API Analyse de texte v3

Utilisez cet article pour rechercher les catégories d’entité qui peuvent être retournées par la [Reconnaissance d’entité nommée](how-tos/text-analytics-how-to-entity-linking.md) (NER). NER exécute un modèle prédictif pour identifier et classer les entités nommées à partir d’un document d’entrée.

NER v3.1 est également disponible. Il permet de détecter les informations personnelles (`PII`) et médicales (`PHI`). Cliquez également sur l’onglet **Intégrité** pour voir la liste des catégories prises en charge dans l’Analyse de texte pour l’intégrité. 

Vous trouverez la liste des types retournés par la version 2.1 dans le [guide de migration](migration-guide.md?tabs=named-entity-recognition)

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
