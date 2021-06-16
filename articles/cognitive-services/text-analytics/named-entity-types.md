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
ms.date: 06/03/2021
ms.author: aahi
ms.openlocfilehash: 9c2cdcaa5aaadf9b1314b436fce4ab747bebdf8c
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2021
ms.locfileid: "111527601"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Catégories d’entité prises en charge dans l’API Analyse de texte v3

Utilisez cet article pour rechercher les catégories d’entité qui peuvent être retournées par la [Reconnaissance d’entité nommée](how-tos/text-analytics-how-to-entity-linking.md) (NER). NER exécute un modèle prédictif pour identifier et classer les entités nommées à partir d’un document d’entrée.

Une préversion de NER v3.1 est également disponible, elle permet de détecter les informations personnelles (`PII`) et médicales (`PHI`). Cliquez également sur l’onglet **Intégrité** pour voir la liste des catégories prises en charge dans l’Analyse de texte pour l’intégrité. 

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
