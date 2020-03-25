---
title: 'Tutoriel : Créer des images de machine virtuelle personnalisées avec l’interface de ligne de commande Azure'
description: Dans ce tutoriel, vous allez apprendre à utiliser Azure CLI pour créer une image de machine virtuelle personnalisée dans Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: e629c605c0ffd3a7e0e1e53c3d661642b9dd01b7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74034495"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>Tutoriel : Créer une image personnalisée d’une machine virtuelle Azure avec Azure CLI

Les images personnalisées sont comme des images de la Place de marché, sauf que vous les créez vous-même. Les images personnalisées peuvent être utilisées pour amorcer des configurations comme le préchargement des applications, les configurations d’application et d’autres configurations de système d’exploitation. Ce didacticiel explique comment créer votre propre image personnalisée d’une machine virtuelle Azure. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Annuler le déploiement de machines virtuelles et généraliser des machines virtuelles
> * Créer une image personnalisée
> * Créer une machine virtuelle à partir d’une image personnalisée
> * Répertorier toutes les images dans votre abonnement
> * Supprimer une image

Ce tutoriel utilise l’interface CLI disponible dans [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), qui est constamment mise à jour vers la dernière version. Pour ouvrir Cloud Shell, sélectionnez **Essayer** en haut d’un bloc de code.

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, ce didacticiel nécessite que vous exécutiez Azure CLI version 2.0.30 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Avant de commencer

Les étapes ci-dessous expliquent comment prendre une machine virtuelle existante et la transformer en une image personnalisée réutilisable que vous pouvez utiliser pour créer de nouvelles instances de machines virtuelles.

Pour exécuter l’exemple dans ce didacticiel, vous devez disposer d’une machine virtuelle. Si nécessaire, cet [exemple de script](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md) peut en créer une pour vous. Au cours du didacticiel, remplacez les noms du groupe de ressources et de la machine virtuelle si nécessaire.

## <a name="create-a-custom-image"></a>Créer une image personnalisée

Pour créer une image de machine virtuelle, vous devez préparer la machine virtuelle en annulant l’approvisionnement, en la libérant et en la marquant comme généralisée. Une fois la machine virtuelle préparée, vous pouvez créer une image.

### <a name="deprovision-the-vm"></a>Annuler l’approvisionnement de la machine virtuelle 

L’annulation de l’approvisionnement généralise la machine virtuelle en supprimant les informations spécifiques à la machine. Cette généralisation permet de déployer plusieurs machines virtuelles à partir d’une image unique. Au cours de l’annulation de l’approvisionnement, le nom d’hôte est réinitialisé sur *localhost.localdomain*. Les clés d’hôte SSH, les configurations de serveur de noms, le mot de passe racine et les baux DHCP mis en cache sont également supprimés.

> [!WARNING]
> Le déprovisionnement et le marquage de la machine virtuelle en tant que machine généralisée rendent la machine virtuelle source inutilisable et impossible à redémarrer. 

Pour annuler l’approvisionnement de la machine virtuelle, utilisez l’agent de machine virtuelle Azure (waagent). L’agent de machine virtuelle Azure est installé sur la machine virtuelle et gère l’approvisionnement et l’interaction avec le contrôleur de structure Azure. Pour plus d’informations, consultez le [Guide d’utilisateur de l’agent Linux Azure](../extensions/agent-linux.md).

Connectez-vous à votre machine virtuelle à l’aide du protocole SSH et exécutez la commande pour annuler l’approvisionnement de la machine virtuelle. Avec l’argument `+user`, le dernier compte d’utilisateur approvisionné et les données associées sont également supprimés. Remplacez l’exemple d’adresse IP par l’adresse IP publique de votre machine virtuelle.

Utilisez une clé SSH sur la machine virtuelle.
```bash
ssh azureuser@52.174.34.95
```
Annulez l’approvisionnement de la machine virtuelle.

```bash
sudo waagent -deprovision+user -force
```
Fermez la session SSH.

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Libérer la machine virtuelle et la marquer comme généralisée

Pour créer une image, la machine virtuelle doit être libérée. Libérez la machine virtuelle à l’aide de [az vm deallocate](/cli//azure/vm). 
   
```azurecli-interactive 
az vm deallocate --resource-group myResourceGroup --name myVM
```

Enfin, définissez l’état de la machine virtuelle comme généralisé avec [az vm generalize](/cli//azure/vm) afin que la plateforme Azure sache que la machine virtuelle a été généralisée. Vous pouvez uniquement créer une image à partir d’une machine virtuelle généralisée.
   
```azurecli-interactive 
az vm generalize --resource-group myResourceGroup --name myVM
```

### <a name="create-the-image"></a>Création de l’image

Créez à présent une image de la machine virtuelle à l’aide de [az image create](/cli//azure/image). L’exemple suivant crée une image nommée *myimage* à partir d’une machine virtuelle nommée *myimage*.
   
```azurecli-interactive 
az image create \
    --resource-group myResourceGroup \
    --name myImage \
    --source myVM
```
 
## <a name="create-vms-from-the-image"></a>Créer des machines virtuelles à partir de l’image

Maintenant que vous avez une image, vous pouvez créer une ou plusieurs nouvelles machines virtuelles à partir de l’image à l’aide de [az vm create](/cli/azure/vm). L’exemple suivant crée une machine virtuelle nommée *myVMfromImage* à partir de l’image nommée *myImage*.

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVMfromImage \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Nous vous recommandons de limiter le nombre de déploiements simultanés à 20 machines virtuelles provenant d’une seule image. Si vous planifiez des déploiements simultanés à grande échelle de plus de 20 machines virtuelles à partir de la même image personnalisée, utilisez une galerie [Shared Image Gallery](shared-image-galleries.md) avec plusieurs réplicas d’images. 

## <a name="image-management"></a>Gestion d’image 

Voici quelques exemples de tâches de gestion d’image courantes et comment les exécuter à l’aide d’Azure CLI.

Répertoriez toutes les images par nom dans un format de tableau.

```azurecli-interactive 
az image list \
    --resource-group myResourceGroup
```

Supprimez une image. Cet exemple supprime l’image nommée *myOldImage* à partir du groupe *myResourceGroup*.

```azurecli-interactive 
az image delete \
    --name myOldImage \
    --resource-group myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous montré comment créer une image de machine virtuelle. Vous avez appris à :

> [!div class="checklist"]
> * Annuler le déploiement de machines virtuelles et généraliser des machines virtuelles
> * Créer une image personnalisée
> * Créer une machine virtuelle à partir d’une image personnalisée
> * Répertorier toutes les images dans votre abonnement
> * Supprimer une image

Pour découvrir les machines virtuelles hautement disponibles, passez au didacticiel suivant.

> [!div class="nextstepaction"]
> [How to use availability sets](tutorial-availability-sets.md) (Comment utiliser des groupes à haute disponibilité).

