---
title: Azure CLI - Créer et vérifier une machine virtuelle dans un laboratoire
description: Ce script Azure CLI crée une machine virtuelle dans un laboratoire et vérifie qu’elle est disponible.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: c7625f62d7897d61903f864b216ccf9aa13648ea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198419"
---
# <a name="use-azure-cli-to-create-and-verify-availability-of-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Utilisez Azure CLI pour créer et vérifier la disponibilité d’une machine virtuelle dans un laboratoire personnalisé dans Azure DevTest Labs

Ce script Azure CLI crée une machine virtuelle dans un laboratoire. La machine virtuelle créée est basée sur une image de la Place du marché avec authentification SSH. Le script vérifie ensuite que la machine virtuelle est disponible pour être utilisée. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/create-verify-virtual-machine-in-lab/create-verify-virtual-machine-in-lab.sh "Create and verify availability of a VM")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes :

| Commande | Notes |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az lab vm create](/cli/azure/lab/vm#az-lab-vm-create) | Crée une machine virtuelle dans un laboratoire. |
| [az lab vm show](/cli/azure/lab/vm#az-lab-vm-show) | Affiche l’état de la machine virtuelle dans un laboratoire. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

D’autres exemples de scripts CLI Azure Lab Services sont disponibles dans les [exemples CLI pour Azure Lab Services](../samples-cli.md).
