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
ms.openlocfilehash: 5a06e26e5f1640024e343c714db3df134422115c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488648"
---
La version 3 de l’API Analyse de texte vous permet de choisir le modèle d’analyse de texte utilisé sur vos données. Utilisez le paramètre facultatif `model-version` pour sélectionner une version du modèle dans vos demandes. Si ce paramètre n’est pas spécifié, l’API prend par défaut la valeur `latest`, la dernière version de modèle stable.

Versions de modèle disponibles :
* `2019-10-01` (`latest`)

Chaque réponse des points de terminaison v3 comprend un champ `model-version` spécifiant la version de modèle utilisée.

```json
{
    “documents”: […]
    “errors”: []
    “model-version”: “2019-10-01”
}
```
