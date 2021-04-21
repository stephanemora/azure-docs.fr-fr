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
ms.openlocfilehash: 32a550e120331a8255281d51725d2d5fc8ca1e05
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564615"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Docker pull du conteneur Analyse des sentiments v3

Le conteneur Analyse des sentiments v3 est disponible dans plusieurs langues. Pour télécharger la version anglaise du conteneur, utilisez la commande ci-dessous. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

Pour télécharger le conteneur dans une autre langue, remplacez `en` par l’un des codes de langue ci-dessous. 

| Conteneur Analyse de texte | Code langue |
|--|--|
| Chinois simplifié    |   `zh-hans`   |
| Chinois traditionnel   |   `zh-hant`   |
| Néerlandais                 |     `nl`      |
| Anglais               |     `en`      |
| Français                |     `fr`      |
| Allemand                |     `de`      |
| Hindi                 |    `hi`       |
| Italien               |     `it`      |
| Japonais              |     `ja`      |
| Coréen                |     `ko`      |
| Norvégien (Bokmål)   |     `no`      |
| Portugais (Brésil)   |    `pt-BR`    |
| Portugais (Portugal) |    `pt-PT`    |
| Espagnol               |     `es`      |
| Turc               |     `tr`      |

Pour obtenir une description complète des balises disponibles pour le conteneur Analyse de texte, consultez [Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654).
