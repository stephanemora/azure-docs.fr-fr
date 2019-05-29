---
title: Fichier Include
description: Fichier Include
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 03/20/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: da63a5418ab94623f6ce3c9f35a085dd8b198d1a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66148997"
---
Pour suivre ce didacticiel, vous devez satisfaire aux exigences suivantes :

**Azure CLI** : Azure CLI version 2.0.29 ou ultérieure doit être installée sur votre ordinateur local. Exécutez `az --version` pour trouver la version. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer l’interface de ligne de commande Microsoft Azure][azure-cli-install].

**Docker** : ce tutoriel présuppose une compréhension de base des concepts Docker essentiels, tels que les conteneurs, les images conteneur et les commandes `docker` de base. Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker][docker-get-started].

**Moteur Docker** : pour suivre ce tutoriel, le moteur Docker doit être installé en local. Docker fournit des packages qui configurent l’environnement de Docker sur [macOS][docker-mac], [Windows][docker-windows] et [Linux][docker-linux].

> [!IMPORTANT]
> Étant donné qu’Azure Cloud Shell n’inclut pas le démon Docker, vous *devez* installer Azure CLI et le moteur Docker sur votre *ordinateur local* pour suivre ce didacticiel. Vous ne pouvez pas utiliser Azure Cloud Shell pour ce didacticiel.

<!-- LINKS - External -->
[docker-get-started]: https://docs.docker.com/engine/docker-overview/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
