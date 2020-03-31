---
title: Docker pull du conteneur Extraction de phrases clés
titleSuffix: Azure Cognitive Services
description: Commande Docker pull pour le conteneur Extraction de phrases clés
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 59313528e4beb343f9ac1103d3f9f2faae5dfc16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966722"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker pull du conteneur Extraction de phrases clés

Utilisez la commande [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) pour télécharger une image conteneur à partir de Microsoft Container Registry.

Pour obtenir une description complète des balises disponibles pour les conteneurs Analyse de texte, consultez le conteneur [Extraction de phrases clés](https://go.microsoft.com/fwlink/?linkid=2018757) dans le Docker Hub.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
