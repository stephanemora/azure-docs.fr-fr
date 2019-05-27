---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/20/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1e78109472668c0f9a73af6430253a0d709979af
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66156170"
---
## <a name="using-rbac-to-share-images"></a>Utilisation de RBAC pour partager des images

Vous pouvez partager des images sur les abonnements à l’aide du contrôle d’accès en fonction du rôle (RBAC). Tout utilisateur avec des autorisations de lecture sur une version d’image, même dans plusieurs abonnements, peut déployer une machine virtuelle à partir de la version d’image.

Pour plus d’informations sur le partage de ressources à l’aide de RBAC, consultez [Gérer l’accès à l’aide de RBAC et Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).


## <a name="list-information"></a>Lister les informations

Obtenez l’emplacement, l’état et d’autres informations sur les galeries d’images disponibles à l’aide de [az sig list](/cli/azure/sig#az-sig-list).

```azurecli-interactive 
az sig list -o table
```

Listez les définitions d’image dans une galerie, y compris les informations sur le type de système d’exploitation et l’état, avec [az sig image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list).

```azurecli-interactive 
az sig image-definition list -g myGalleryRG -r myGallery -o table
```

Listez les versions d’image partagée dans une galerie avec [az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list).

```azurecli-interactive
az sig image-version list -g myGalleryRG -r myGallery -i myImageDefinition -o table
```

Obtenez l’ID d’une version d’image avec [az sig image-version show](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   -g myGalleryRG \
   -r myGallery \
   -i myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```