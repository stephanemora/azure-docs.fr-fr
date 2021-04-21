---
title: Fichier include
description: Fichier include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/24/2020
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: fc35f277bfafa80f6239ef807f1a83591646a503
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799895"
---
## <a name="create-an-image-gallery"></a>Créer une galerie d’images 

Une galerie d’images est la ressource principale utilisée pour activer le partage d’image. 

Les caractères autorisés pour le nom de galerie sont les lettres majuscules ou minuscules, les chiffres et les points. Le nom de galerie ne peut pas contenir de tirets.   Les noms de galerie doivent être uniques dans votre abonnement. 

Créez une galerie d’images à l’aide de [az sig create](/cli/azure/sig#az_sig_create). L’exemple suivant crée un groupe de ressources nommé *myGalleryRG* dans *USA Est* et une galerie nommée *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="share-the-gallery"></a>Partager la galerie

Vous pouvez partager des images entre abonnements à l’aide du contrôle d’accès en fonction du rôle (RBAC). Vous pouvez partager des images au niveau de la galerie, de la définition d’image ou de la version d’image. Tout utilisateur disposant d’autorisations de lecture sur une version d’image, même dans plusieurs abonnements, peut déployer une machine virtuelle à partir de la version d’image.

Nous vous recommandons de partager l’accès avec d’autres utilisateurs au niveau de la galerie. Pour obtenir l’ID d’objet de votre galerie, utilisez [az sig show](/cli/azure/sig#az_sig_show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Utilisez l’ID d’objet en tant qu’étendue, ainsi qu’une adresse e-mail et [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) pour donner aux utilisateurs un accès à la galerie d’images partagées. Remplacez `<email-address>` et `<gallery iD>` par vos propres informations.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Pour plus d’informations sur le partage de ressources à l’aide de RBAC, consultez [Gérer l’accès à l’aide de RBAC et Azure CLI](../articles/role-based-access-control/role-assignments-cli.md).