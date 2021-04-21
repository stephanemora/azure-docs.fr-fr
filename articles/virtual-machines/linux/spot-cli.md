---
title: Utiliser l’interface CLI pour déployer des machines virtuelles Azure Spot
description: Découvrez comment utiliser l’interface CLI pour déployer des machines virtuelles Azure Spot et réaliser des économies sur les coûts.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 8e8bdaa7a812d8c7accfea59b58b75a58d50e21e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789606"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Déployer des machines virtuelles Azure Spot à l’aide d’Azure CLI

L’utilisation de [machines virtuelles Azure Spot](../spot-vms.md) vous permet de disposer de notre capacité inutilisée en réalisant des économies significatives. Dès qu’Azure aura besoin de récupérer la capacité, l’infrastructure Azure supprimera les machines virtuelles Azure Spot. Les machines virtuelles Azure Spot sont donc appropriées pour les charges de travail capables de gérer les interruptions, comme les travaux de traitement par lots, les environnements de développement et de test, les charges de travail de calcul importantes, entre autres.

Les tarifs des machines virtuelles Azure Spot sont variables, en fonction de la région et de la référence SKU. Pour plus d’informations, consultez les prix des machines virtuelles pour [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) et [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Vous avez la possibilité de définir un prix maximal que vous êtes prêt à payer, par heure, pour la machine virtuelle. Le prix maximal d’une machine virtuelle Azure Spot peut être défini en dollars américains (USD), en utilisant jusqu’à 5 décimales. Par exemple, la valeur `0.98765` correspond à un prix maximal de 0,98765 $ USD par heure. Si vous définissez `-1` comme prix maximal, la machine virtuelle n’est pas supprimée en fonction du prix. Le prix de la machine virtuelle sera le prix actuel de Spot ou le prix d’une machine virtuelle standard, la valeur la plus faible étant retenue, à condition que la capacité et le quota soient disponibles. Pour plus d’informations sur la définition du prix maximal, consultez [Machines virtuelles Azure Spot - Tarifs](../spot-vms.md#pricing).

Le processus de création d’une machine virtuelle Azure Spot à l’aide d’Azure CLI est le même que celui décrit dans l’[article de démarrage rapide](./quick-create-cli.md). Ajoutez simplement le paramètre « --priority Spot », définissez `--eviction-policy` avec Deallocate (par défaut) ou `Delete`, puis indiquez un prix maximal ou `-1`. 


## <a name="install-azure-cli"></a>Installation de l’interface de ligne de commande Azure

Pour créer des machines virtuelles Azure Spot, vous devez exécuter Azure CLI version 2.0.74 ou ultérieure. Pour déterminer la version, exécutez la commande **az --version**. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli). 

Connectez-vous à Azure avec la commande [az login](/cli/azure/reference-index#az_login).

```azurecli-interactive
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Créer une machine virtuelle Azure Spot

Cet exemple montre comment déployer une machine virtuelle Azure Spot Linux qui ne sera pas supprimée en fonction du prix. La stratégie d’éviction est définie pour désallouer la machine virtuelle, afin qu’elle puisse être redémarrée ultérieurement. Si vous souhaitez supprimer la machine virtuelle et le disque sous-jacent quand la machine virtuelle est supprimée, définissez `--eviction-policy` avec `Delete`.

```azurecli-interactive
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



Une fois la machine virtuelle créée, vous pouvez effectuer une requête pour voir le prix de facturation maximal pour toutes les machines virtuelles du groupe de ressources.

```azurecli-interactive
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>Simuler une éviction

Vous pouvez simuler l’éviction d’une machine virtuelle spot Azure à l’aide de REST, de PowerShell ou de CLI, afin de tester l’efficacité de la réponse de votre application à une éviction soudaine.

Dans la plupart des cas, vous pouvez utiliser l’API REST [Machines virtuelles – Simuler une éviction](/rest/api/compute/virtualmachines/simulateeviction) pour faciliter le test automatisé des applications. Pour REST, un `Response Code: 204` signifie que l’éviction simulée a réussi. Vous pouvez combiner des évictions simulées avec le [service d’événement planifié](scheduled-events.md) pour automatiser la manière dont votre application répondra lors de l’éviction de la machine virtuelle.

Pour voir les événements planifiés en action, regardez [Azure Friday – Utilisation d’événements planifiés Azure pour préparer la maintenance de machines virtuelles](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance).


### <a name="quick-test"></a>Test rapide

Pour un test rapide montrant le fonctionnement d’une éviction simulée, nous allons examiner l’interrogation du service d’événement planifié pour voir à quoi ressemble la simulation d’une éviction à l’aide d’Azure CLI.

Le service d’événement planifié est activé pour votre service la première fois que vous effectuez une demande d’événements. 

Accédez à distance à votre machine virtuelle, puis ouvrez une invite de commandes. 

Dans l’invite de commandes sur votre machine virtuelle, tapez :

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Cette première réponse peut prendre jusqu’à 2 minutes. À partir de maintenant, ils devraient afficher la sortie presque immédiatement.

À partir d’un ordinateur sur lequel Azure CLI est installé (comme votre ordinateur local), simulez une éviction à l’aide de la commande [az vm simulation-eviction](https://docs.microsoft.com/cli/azure/vm#az_vm_simulate_eviction). Remplacez le nom du groupe de ressources et le nom de la machine virtuelle par les vôtres 

```azurecli-interactive
az vm simulate-eviction --resource-group mySpotRG --name mySpot
```

La sortie de la réponse contiendra `Status: Succeeded` si la demande a été effectuée avec succès.

Revenez rapidement à votre connexion à distance à votre machine virtuelle spot et interrogez à nouveau le point de terminaison Scheduled Events. Répétez la commande suivante jusqu’à obtenir une sortie contenant plus d’informations :

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Lorsque le service d’événement planifié reçoit la notification d’éviction, vous obtenez une réponse ressemblant à ceci :

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

Vous pouvez voir `"EventType":"Preempt"`, et la ressource est la ressource de machine virtuelle `"Resources":["myspotvm"]`. 

Vous pouvez également voir à quel moment la machine virtuelle sera supprimée en vérifiant la valeur `"NotBefore"`. La machine virtuelle ne sera pas supprimée avant l’heure indiquée. Cela indique la fenêtre pendant laquelle votre application peut se fermer normalement.


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également créer une machine virtuelle Azure Spot à l’aide d’[Azure PowerShell](../windows/spot-powershell.md), du [portail](../spot-portal.md) ou d’un [modèle](spot-template.md).

Interroger les informations de tarification actuelles à l’aide de l’[API des prix de vente au détail d’Azure](/rest/api/cost-management/retail-prices/azure-retail-prices) pour plus d’informations la machine virtuelle Azure Spot. Les `meterName` et `skuName` contiennent tous les deux `Spot`.

Si vous rencontrez une erreur, consultez [Codes d’erreur](../error-codes-spot.md).
