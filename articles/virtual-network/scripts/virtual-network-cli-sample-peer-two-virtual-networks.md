---
title: Exemple de script Azure CLI - Homologuer deux réseaux virtuels | Documents Microsoft
description: Exemple de script Azure CLI - Homologuer deux réseaux virtuels.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.openlocfilehash: 82f4f06cd4e615df38f2251be9aef9e8578b9df4
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64726781"
---
# <a name="peer-two-virtual-networks-script-sample"></a>Exemple de script : Homologuer deux réseaux virtuels

Cet exemple de script crée et connecte deux réseaux virtuels situés dans la même région via le réseau Azure. Après exécution du script, vous obtenez une homologation entre deux réseaux virtuels.

Vous pouvez exécuter le script à partir d’Azure [Cloud Shell](https://shell.azure.com/bash) ou à partir d’une installation Azure CLI locale. Si vous utilisez l’interface CLI localement, ce script nécessite que vous exécutiez la version 2.0.28 ou une version ultérieure. Pour trouver la version installée, exécutez `az --version`. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli). Si vous exécutez la CLI localement, vous devez également exécuter `az login` pour créer une connexion avec Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Peer two networks")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées :

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, une machine virtuelle et toutes les ressources associées. Chaque commande du tableau suivant renvoie à une documentation spécifique :

| Commande | Notes |
|---|---|
| [az group create](/cli/azure/group) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az network vnet create](/cli/azure/network/vnet) | Crée un réseau virtuel et un sous-réseau Azure. |
| [az network vnet peering create](/cli/azure/network/vnet/peering) | Crée une homologation entre deux réseaux virtuels.  |
| [az group delete](/cli/azure/vm/extension) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

Vous trouverez des exemples supplémentaires de scripts CLI pour réseau virtuel dans [Exemples CLI pour réseau virtuel](../cli-samples.md).
