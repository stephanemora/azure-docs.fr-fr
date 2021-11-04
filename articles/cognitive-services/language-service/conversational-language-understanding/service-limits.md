---
title: Limites de Compréhension du langage courant
titleSuffix: Azure Cognitive Services
description: Découvrez les limites en termes de données, de régions et de débit pour Compréhension du langage courant
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: ec9a32c4f73e174798a3422a1c152f3972bd076f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096829"
---
# <a name="service-limits-for-conversational-language-understanding"></a>Limites du service pour Compréhension du langage courant

Découvrez les limites en termes de données, de régions et de débit pour le service Compréhension du langage courant

## <a name="region-limits"></a>Limites de région

- Compréhension du langage courant est disponible seulement dans 2 régions : **USA Ouest 2** et **Europe Ouest**. 
    - Les projets de workflow d’orchestration vont activer les connexions à **Projets de conversation**, **QnA Maker** et **LUIS** dans la région Europe de l’Ouest.
    - Les projets de workflow d’orchestration vont activer les connexions à **Projets de conversation** et **QnA Maker** dans la région USA Ouest 2. Il n’existe pas de région USA Ouest 2 de création pour LUIS. 
- La seule référence SKU disponible pour accéder à Compréhension du langage courant est la ressource **Language** avec la référence SKU **S**.

## <a name="data-limits"></a>Limites de données

Les limites suivantes sont d’application pour le service Compréhension du langage courant.

|Article|Limite|
| --- | --- |
|Énoncés|15 000 par projet*|
|Intentions|500 par projet|
|Entities|100 par projet|
|Longueur de l’énoncé|500 caractères|
|Longueur du nom des intentions et des entités|50 caractères|
|Modèles|10 par projet|
|Projets|500 par ressource|
|Synonymes|20 000 par composant de liste|

\**Inclut seulement les énoncés ajoutés par l’utilisateur. Les données extraites pour les projets de workflow d’orchestration ne sont pas comptabilisées dans le total.*


## <a name="throughput-limits"></a>Limites de débit

|Article | Limite |
--- | --- 
|Appels de création| 60 demandes par minute|
|Appels de prédiction| 60 demandes par minute|

## <a name="quota-limits"></a>Limites de quota

| Article | Limite |
--- | --- 
|Appels de création| Illimité|
|Appels de prédiction| 100 000 par mois|

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de Compréhension du langage courant](overview.md)
