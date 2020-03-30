---
title: Gestion des versions des modèles
titleSuffix: Azure Cognitive Services
description: Spécifier les versions de modèle dans les points de terminaison v3
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 4cd8d0901ce23fb227bb6919dee18f5aa3d063ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77088989"
---
La version 3 de l’API Analyse de texte vous permet de choisir la version du modèle la plus récente pour vos données. Utilisez le paramètre facultatif `model-version` pour sélectionner la version du modèle souhaitée pour vos requêtes. Si ce paramètre n’est pas spécifié, l’API prend par défaut la valeur `latest`, la dernière version stable. Même si vous pouvez utiliser la version du modèle la plus récente dans toute requête, seules certaines fonctionnalités sont mises à jour dans chaque version. Le tableau ci-dessous décrit les fonctionnalités qui ont été mises à jour dans chaque version du modèle :

| Version du modèle           | Fonctionnalités mises à jour         | Version la plus récente pour :           |
|-------------------------|--------------------------|--------------------------|
| `2020-02-01`            | Reconnaissance d’entité                      | Reconnaissance d’entité                      |
| `2019-10-01`            | Reconnaissance d’entité, Analyse des sentiments  | Détection de la langue, Extraction d’expressions clés, Analyse des sentiments|


Chaque réponse des points de terminaison v3 comprend un champ `model-version` spécifiant la version de modèle utilisée.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
Pour plus d’informations sur les mises à jour de ces versions du modèle, consultez [Nouveautés](../whats-new.md).
