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
ms.openlocfilehash: 549ecf940c53267db6b3caa576c4c24db414337a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90906087"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Docker pull du conteneur Analyse des sentiments v3

Le conteneur Analyse des sentiments v3 est disponible dans plusieurs langues. Pour télécharger la version anglaise du conteneur, utilisez la commande ci-dessous. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
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