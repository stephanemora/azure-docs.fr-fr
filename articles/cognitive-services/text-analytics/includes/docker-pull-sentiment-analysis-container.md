---
title: Docker pull du conteneur Analyse des sentiments
titleSuffix: Azure Cognitive Services
description: Commande Docker pull du conteneur Analyse des sentiments
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: 0954ef88f6917159156fbb73103b7b7af3283c00
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051249"
---
## <a name="pull-the-sentiment-analysis-container"></a>Tirer le conteneur Analyse des sentiments

Images conteneur pour Analyse de texte disponibles dans Microsoft Container Registry.

| Conteneur | Nom de registre de conteneurs / référentiel / image |
|-----------|------------|
| Analyse des sentiments | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

Utilisez la commande [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) pour télécharger une image conteneur à partir de Microsoft Container Registry.

Pour obtenir une description complète des balises disponibles pour les conteneurs Analyse de texte, consultez le conteneur [Analyse des sentiments](https://go.microsoft.com/fwlink/?linkid=2018654) dans le Docker Hub.

### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Docker pull du conteneur Analyse des sentiments

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
