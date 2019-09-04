---
title: Exemple de conteneur d’exécution de la commande d’exécution du docker
titleSuffix: Azure Cognitive Services
description: Commande Docker run pour le conteneur Détection de langue
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: b6aaa33667f85bedb4cc33fdb1c7b5f73a64d382
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051405"
---
### <a name="run-container-example-of-docker-run-command"></a>Exemple de conteneur d’exécution de la commande d’exécution du docker

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Cette commande :

* Exécute un conteneur *Détection de langue* à partir de l’image conteneur
* Alloue un cœur de processeur et 4 gigaoctets (Go) de mémoire.
* Expose le port TCP 5000 et alloue un pseudo-TTY pour le conteneur
* Supprime automatiquement le conteneur après sa fermeture. L’image conteneur est toujours disponible sur l’ordinateur hôte.