---
title: Fichier include
description: Fichier include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/28/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 83d70a8d4c5806120ddb4ea776a8f4a6f7e63857
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800255"
---
## <a name="list-information"></a>Lister les informations

Obtenez l’emplacement, l’état et d’autres informations sur les galeries d’images disponibles à l’aide de [az sig list](/cli/azure/sig#az_sig_list).

```azurecli-interactive 
az sig list -o table
```

Listez les définitions d’image dans une galerie, y compris les informations sur le type de système d’exploitation et l’état, avec [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list).

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Listez les versions d’image partagée dans une galerie avec [az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list).

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Obtenez l’ID d’une version d’image avec [az sig image-version show](/cli/azure/sig/image-version#az_sig_image_version_show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```
