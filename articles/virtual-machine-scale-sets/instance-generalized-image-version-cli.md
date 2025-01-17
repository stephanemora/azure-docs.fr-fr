---
title: Créer un groupe identique à partir d’une image généralisée avec Azure CLI
description: Créez un groupe identique à l’aide d’une image généralisée dans Shared Image Gallery en utilisant l’interface de ligne de commande Azure.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: mimckitt
ms.openlocfilehash: a9ac2bd794ee4aa9f531ad9fcd2f07bb744d02c8
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691248"
---
# <a name="create-a-scale-set-from-a-generalized-image-with-azure-cli"></a>Créer un groupe identique à partir d’une image généralisée avec Azure CLI

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques uniformes

Créez un groupe identique à partir d’une version d’image généralisée stockée dans une [galerie d’images partagées](../virtual-machines/shared-image-galleries.md) à l’aide d’Azure CLI. Si vous souhaitez créer un groupe identique à l’aide d’une version d’image spécialisée, consultez [Créer des instances de groupe identique à partir d’une image spécialisée](instance-specialized-image-version-cli.md).

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, ce tutoriel demande au minimum la version 2.4.0 d’Azure CLI. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli).

Remplacez les noms de ressources en fonction des besoins dans cet exemple. 

Répertoriez les définitions d’images d’une galerie avec la commande [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) pour voir le nom et l’ID des définitions.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Créez le groupe identique à l’aide de [`az vmss create`](/cli/azure/vmss#az_vmss_create). 

Utilisez l’ID de définition d’image pour `--image` afin de créer les instances de groupe identique à partir de la version la plus récente de l’image disponible. Vous pouvez également créer les instances de groupe identique à partir d’une version spécifique en fournissant l’ID de version de l’image pour `--image`. Souvenez-vous que l’utilisation d’une version d’image spécifique signifie que l’automatisation peut échouer si cette version d’image spécifique n’est pas disponible car elle a été effacée ou supprimée de la région. Nous vous recommandons d’utiliser l’ID de définition d’image pour créer votre machine virtuelle, sauf si une version d’image spécifique est requise.

Dans cet exemple, nous créons des instances à partir de la dernière version de l’image *myImageDefinition*.

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" 
   --admin-username azureuser \
   --generate-ssh-keys
```

La création et la configuration des l’ensemble des ressources et des machines virtuelles du groupe identique prennent quelques minutes.

## <a name="next-steps"></a>Étapes suivantes
[Générateur d’image Azure (préversion)](../virtual-machines/image-builder-overview.md) permet d’automatiser la création d’une version de l’image. Vous pouvez même l’utiliser pour mettre à jour et [créer une nouvelle version de l’image à partir d’une image existante](../virtual-machines/linux/image-builder-gallery-update-image-version.md). 

Vous pouvez également créer la ressource de galerie d’images partagées à l’aide de modèles. Plusieurs modèles de démarrage rapide Azure sont disponibles : 

- [Créer une galerie d’images partagées](https://azure.microsoft.com/resources/templates/sig-create/)
- [Créer une définition d’image dans une galerie d’images partagées](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [Créer une version d’image dans une galerie d’images partagées](https://azure.microsoft.com/resources/templates/sig-image-version-create/)

Pour plus d’informations sur les galeries d’images partagées, consultez la [vue d’ensemble](../virtual-machines/shared-image-galleries.md). Si vous rencontrez des problèmes, consultez [Résoudre les problèmes des galeries d’images partagées](../virtual-machines/troubleshooting-shared-images.md).