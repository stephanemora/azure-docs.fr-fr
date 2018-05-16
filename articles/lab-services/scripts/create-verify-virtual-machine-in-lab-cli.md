---
title: Exemple de script Azure CLI - Créer et vérifier une machine virtuelle dans un laboratoire personnalisé | Microsoft Docs
description: Ce script Azure CLI crée une machine virtuelle dans un laboratoire personnalisé et vérifie qu’elle est disponible.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2018
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: bd564cda7b4d5c2158b8499b48b8faa68309b461
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="use-azure-cli-to-create-and-verify-availability-of-a-virtual-machine-in-a-custom-lab"></a>Utilisez Azure CLI pour créer et vérifier la disponibilité d’une machine virtuelle dans un laboratoire personnalisé

Ce script Azure CLI crée une machine virtuelle dans un laboratoire personnalisé. La machine virtuelle créée est basée sur une image de la Place du marché avec authentification SSH. Le script vérifie ensuite que la machine virtuelle est disponible pour être utilisée. 

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
| [az group create](/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az lab vm create ](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-create) | Crée une machine virtuelle dans un laboratoire personnalisé. |
| [az lab vm show](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-show) | Affiche l’état de la machine virtuelle dans un laboratoire personnalisé. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

D’autres exemples de scripts PowerShell pour Azure Lab Services sont disponibles dans les [exemples CLI pour Azure Lab Services](../samples-cli.md).