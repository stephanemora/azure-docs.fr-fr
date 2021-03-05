---
title: Azure CLI - Arrêter et supprimer une machine virtuelle dans un laboratoire
description: Cet article fournit un script Azure CLI qui arrête et supprime une machine virtuelle dans un laboratoire dans Azure DevTest Labs.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 3f3802837685281339f0ca355c677e1a0ceac067
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198198"
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Utiliser Azure CLI pour arrêter et supprimer une machine virtuelle dans un laboratoire dans Azure DevTest Labs

Ce script Azure CLI arrête et supprime une machine virtuelle dans un laboratoire. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a lab")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes :

| Commande | Notes |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm#az-lab-vm-stop) | Arrête une machine virtuelle dans un laboratoire. Cet opérateur peut prendre un certain temps. |
| [az lab vm delete](/cli/azure/lab/vm#az-lab-vm-delete) | Supprime une machine virtuelle dans un laboratoire. Cet opérateur peut prendre un certain temps. |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](/cli/azure).

D’autres exemples de scripts CLI Azure Lab Services sont disponibles dans les [exemples CLI pour Azure Lab Services](../samples-cli.md).
