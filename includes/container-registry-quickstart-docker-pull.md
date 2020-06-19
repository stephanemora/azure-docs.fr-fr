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
ms.openlocfilehash: d7c813c748d2af60ace71f61ba3efa43a18da00d
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/12/2020
ms.locfileid: "84752168"
---
## <a name="run-image-from-registry"></a>Exécuter l’image à partir du registre

Vous pouvez à présent extraire (pull) et exécuter l’image conteneur `hello-world:v1` à partir de votre registre de conteneurs à l’aide de [docker run][docker-run] :

```
docker run <login-server>/hello-world:v1  
```

Exemple de sortie : 

```
Unable to find image 'mycontainerregistry007.azurecr.io/hello-world:v1' locally
v1: Pulling from hello-world
Digest: sha256:662dd8e65ef7ccf13f417962c2f77567d3b132f12c95909de6c85ac3c326a345
Status: Downloaded newer image for mycontainerregistry007.azurecr.io/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.

[...]
```

<!-- LINKS - External -->
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
