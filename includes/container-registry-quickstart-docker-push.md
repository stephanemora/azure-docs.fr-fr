---
title: Fichier Include
description: Fichier Include
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 01/23/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 09eaf9465ec3912dea6e1f3ee1693f6bfed50abc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "67177619"
---
## <a name="push-image-to-registry"></a>Envoyer l’image au registre

Pour envoyer une image dans un registre Azure Container Registry, vous devez tout d’abord disposer d’une image. Si vous n’avez pas encore d’images conteneur locales, exécutez la commande [docker pull][docker-pull] suivante pour extraire (pull) une image existante du Docker Hub. Pour cet exemple, tirez l’image `hello-world`.

```
docker pull hello-world
```

Avant de pousser (push) une image vers le registre, vous devez la marquer avec le nom complet de votre serveur de connexion ACR. Le nom du serveur de connexion est au format *\<nom-registre\>.azurecr.io* (tout en minuscules). Par exemple : *mycontainerregistry007.azurecr.io*.

Étiquetez l’image en utilisant la commande [docker tag][docker-tag]. Remplacez `<acrLoginServer>` par le nom du serveur de connexion de votre instance ACR.

```
docker tag hello-world <acrLoginServer>/hello-world:v1
```

Enfin, utilisez la commande [docker push][docker-push] pour envoyer l’image à l’instance ACR. Remplacez `<acrLoginServer>` par le nom du serveur de connexion de votre instance ACR. Cet exemple crée le référentiel **hello-world** qui contient l’image `hello-world:v1`.

```
docker push <acrLoginServer>/hello-world:v1
```

Après avoir envoyé (push) l’image à votre registre de conteneurs, supprimez l’image `hello-world:v1` de votre environnement Docker local. (Notez que cette commande [docker rmi][docker-rmi] ne supprime pas l’image du référentiel **hello-world** dans votre registre de conteneurs Azure.)

```
docker rmi <acrLoginServer>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

