---
title: Docker pull du conteneur Détection de langue
titleSuffix: Azure Cognitive Services
description: Commande Docker pull pour le conteneur Détection de langue
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: da6f04f1042d1a02178f345c5fe67387ae0a7d0f
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051292"
---
## <a name="pull-the-language-detection-container"></a>Tirer le conteneur Détection de langue

Images conteneur pour Analyse de texte disponibles dans Microsoft Container Registry.

| Conteneur | Nom de registre de conteneurs / référentiel / image |
|-----------|------------|
| Détection de la langue | `mcr.microsoft.com/azure-cognitive-services/language` |

Utilisez la commande [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) pour télécharger une image conteneur à partir de Microsoft Container Registry.

Pour obtenir une description complète des balises disponibles pour les conteneurs Analyse de texte, consultez le conteneur [Détection de langue](https://go.microsoft.com/fwlink/?linkid=2018759) dans le Docker Hub.


### <a name="docker-pull-for-the-language-detection-container"></a>Docker pull du conteneur Détection de langue

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
