---
title: Exemple de script Azure CLI - Arrêter et supprimer une machine virtuelle dans un laboratoire personnalisé | Microsoft Docs
description: Ce script Azure CLI arrête et supprime une machine virtuelle dans un laboratoire personnalisé.
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
ms.openlocfilehash: 0da99bb5e029f4d1c6be74d09ad11c6017121a7c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-custom-lab"></a>Utiliser Azure CLI pour arrêter et supprimer une machine virtuelle dans un laboratoire personnalisé

Ce script Azure CLI arrête et supprime une machine virtuelle dans un laboratoire personnalisé. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a custom lab")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes :

| Commande | Notes |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-stop) | Arrête une machine virtuelle dans un laboratoire personnalisé. Cet opérateur peut prendre un certain temps. |
| [az lab vm delete](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-delete) | Supprime une machine virtuelle dans un laboratoire personnalisé. Cet opérateur peut prendre un certain temps. |


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure).

D’autres exemples de scripts PowerShell pour Azure Lab Services sont disponibles dans les [exemples CLI pour Azure Lab Services](../samples-cli.md).