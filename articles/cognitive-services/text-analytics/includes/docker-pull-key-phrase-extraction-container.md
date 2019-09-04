---
title: Docker pull du conteneur Extraction de phrases clés
titleSuffix: Azure Cognitive Services
description: Commande Docker pull pour le conteneur Extraction de phrases clés
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: e71db5455a3d6726cd6567d568a13fcdcf9cb520
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051346"
---
## <a name="pull-the-key-phrase-extraction-container"></a>Tirer le conteneur Extraction de phrases clés

Images conteneur pour Analyse de texte disponibles dans Microsoft Container Registry.

| Conteneur | Nom de registre de conteneurs / référentiel / image |
|-----------|------------|
| Extraction d’expressions clés | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |

Utilisez la commande [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) pour télécharger une image conteneur à partir de Microsoft Container Registry.

Pour obtenir une description complète des balises disponibles pour les conteneurs Analyse de texte, consultez le conteneur [Extraction de phrases clés](https://go.microsoft.com/fwlink/?linkid=2018757) dans le Docker Hub.

### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker pull du conteneur Extraction de phrases clés

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
