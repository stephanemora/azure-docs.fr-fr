---
title: Docker pull du conteneur Analyse des sentiments
titleSuffix: Azure Cognitive Services
description: Commande Docker pull du conteneur Analyse des sentiments
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a3db0e2ffdd4a75f02634ca2227c3c41416d4f65
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588378"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Docker pull du conteneur Analyse des sentiments v3

Le conteneur Analyse des sentiments v3 est disponible dans plusieurs langues. Pour télécharger la version anglaise du conteneur, utilisez la commande ci-dessous. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en
```

Pour télécharger le conteneur dans une autre langue, remplacez `en` par l’un des codes de langue ci-dessous. 

| Conteneur Analyse de texte | Code langue |
|--|--|
| Anglais | `en` |
| Espagnol | `es` |
| Français | `fr` |
| Italien | `it` |
| Allemand | `de` |
| Chinois - simplifié | `zh` |
| Chinois - traditionnel | `zht` |
| Japonais | `ja` |
| Portugais | `pt` |
| Néerlandais | `nl` |

Pour obtenir une description complète des balises disponibles pour le conteneur Analyse de texte, consultez [Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654).