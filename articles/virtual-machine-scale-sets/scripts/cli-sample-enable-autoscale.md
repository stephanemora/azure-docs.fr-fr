---
title: Exemples Azure CLI - Activer la mise à l’échelle basée sur l’hôte
description: Ce script crée un groupe de machines virtuelles identiques exécutant Ubuntu et utilise des métriques basées sur l’hôte pour mettre à l’échelle automatiquement tandis que la charge du processeur change.
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.date: 03/27/2018
ms.author: jushiman
ms.custom: mvc
ms.openlocfilehash: 0b934e327a4c1ed18c682f909b2d82e85d61125f
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008401"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli"></a>Effectuer une mise à l’échelle automatique d’un groupe de machines virtuelles identiques avec Azure CLI
Ce script crée un groupe de machines virtuelles identiques exécutant Ubuntu et utilise des métriques basées sur l’hôte pour mettre à l’échelle automatiquement tandis que la charge du processeur change.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/auto-scale-host-metrics/auto-scale-host-metrics.sh "Automatically scale a virtual machine scale set")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement
Exécutez la commande suivante pour supprimer le groupe de ressources, le groupe identique et toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script
Ce script utilise les commandes suivantes pour créer un groupe de ressources, un groupe de machines virtuelles identiques, ainsi que toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](/cli/azure/ad/group) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az vmss create](/cli/azure/vmss) | Crée le groupe de machines virtuelles identiques et le connecte au réseau virtuel, au sous-réseau et au groupe de sécurité réseau. Un équilibreur de charge est également créé pour distribuer le trafic vers les différentes instances de machine virtuelle. Cette commande spécifie aussi l’image de machine virtuelle à utiliser ainsi que les informations d’identification d’administration.  |
| [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings) | Crée et applique des règles de mise à l’échelle automatique à un groupe de machines virtuelles identiques. |
| [az group delete](/cli/azure/ad/group) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts Azure CLI de groupes de machines virtuelles identiques dans la [documentation relative aux groupes de machines virtuelles identiques Azure](../cli-samples.md).
