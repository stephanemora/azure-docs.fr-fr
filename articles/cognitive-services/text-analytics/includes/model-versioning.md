---
title: Gestion des versions des modèles
titleSuffix: Azure Cognitive Services
description: Spécifier les versions de modèle dans les points de terminaison v3
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/25/2019
ms.author: aahi
ms.openlocfilehash: 29850cb9cb40eae0829b5d8c2b58b5f9518f18d5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021083"
---
La version 3 de l’API Analyse de texte vous permet de choisir le modèle d’analyse de texte utilisé sur vos données. Utilisez le paramètre facultatif `model-version` pour sélectionner une version du modèle dans vos demandes. Si ce paramètre n’est pas spécifié, l’API prend par défaut la valeur `latest`, la dernière version de modèle stable.

Versions de modèle disponibles :
* `2019-10-01` (`latest`)

Chaque réponse des points de terminaison v3 comprend un champ `model-version` spécifiant la version de modèle utilisée.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
