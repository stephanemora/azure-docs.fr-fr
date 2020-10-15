---
title: Exemple de script Azure CLI - Créer une machine virtuelle avec un disque dur virtuel (VHD)
description: Exemple de script Azure CLI - Créez une machine virtuelle avec un disque dur virtuel (VHD).
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/09/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 6f5e30b0d6a072f9a40ae57ebe325461cdfe5bb1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87479610"
---
# <a name="create-a-vm-with-a-virtual-hard-disk"></a>Créer une machine virtuelle avec un disque dur virtuel (VHD)

Cet exemple crée une machine virtuelle à l’aide d’un disque dur virtuel (VHD).
Il crée un groupe de ressources, un compte de stockage et un conteneur, puis il crée une machine virtuelle en chargeant le disque dur virtuel (VHD) dans le conteneur.
Il remplace le clé publique SSH par votre clé publique afin que vous ayez accès à la machine virtuelle.

Vous devez disposer d’un VHD amorçable. Le script recherche `~/sample.vhd`.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-vhd/create-vm-vhd.sh "Create VM using a VHD")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurecli-interactive 
az group delete -n az-cli-vhd
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, une machine virtuelle, un groupe à haute disponibilité, un équilibreur de charge et toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](/cli/azure/group) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az storage account list](/cli/azure/storage/account) | Répertorie les comptes de stockage |
| [az storage account check-name](/cli/azure/storage/account) | Vérifie qu’un nom de compte de stockage est valide et qu’il n’existe pas déjà |
| [az storage account keys list](/cli/azure/storage/account/keys) | Répertorie les clés des comptes de stockage |
| [az storage blob exists](/cli/azure/storage/blob) | Vérifie si l’objet blob existe |
| [az storage container create](/cli/azure/storage/container) | Crée un conteneur dans un compte de stockage. |
| [az storage blob upload](/cli/azure/storage/blob) | Crée un objet blob dans le conteneur en chargeant le disque dur virtuel (VHD). |
| [az vm list](/cli/azure/vm) | Utilisée avec `--query` pour vérifier si le nom de la machine virtuelle est en cours d’utilisation. | 
| [az vm create](/cli/azure/vm/availability-set) | Crée les machines virtuelles. |
| [az vm list-ip-addresses](/cli/azure/vm#az-vm-list-ip-addresses) | Obtient l’adresse IP de la machine virtuelle qui a été créée. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle dans la [documentation relative aux machines virtuelles Linux Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
