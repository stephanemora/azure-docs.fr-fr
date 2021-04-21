---
title: Déployer des machines virtuelles dans un groupe à haute disponibilité à l’aide d’Azure CLI
description: Avec ce tutoriel, vous allez apprendre à utiliser Azure CLI pour déployer des machines virtuelles hautement disponibles dans les groupes à haute disponibilité
documentationcenter: ''
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 3/8/2021
ms.author: mimckitt
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 7c45f08a339ca8878bb9e2840faa8a412f3e60e0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765968"
---
# <a name="create-and-deploy-virtual-machines-in-an-availability-set-using-azure-cli"></a>Créer et déployer des machines virtuelles dans un groupe à haute disponibilité à l’aide d’Azure CLI

Ce didacticiel explique comment améliorer la disponibilité et la fiabilité de vos solutions de machine virtuelle sur Azure en utilisant une fonctionnalité appelée Groupes à haute disponibilité. Les groupes à haute disponibilité veillent à ce que les machines virtuelles que vous déployez sur Azure soient distribuées sur plusieurs clusters matériels isolés. Leur utilisation garantit qu’en cas de défaillance matérielle ou logicielle dans Azure, seul un sous-ensemble de vos machines virtuelles est affecté et que votre solution globale reste disponible et opérationnelle.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un groupe à haute disponibilité
> * Créer une machine virtuelle dans un groupe à haute disponibilité
> * Vérifier les tailles de machines virtuelles disponibles

Ce tutoriel utilise l’interface CLI disponible dans [Azure Cloud Shell](../../cloud-shell/overview.md), qui est constamment mise à jour vers la dernière version. Pour ouvrir Cloud Shell, sélectionnez **Essayer** en haut d’un bloc de code.

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, ce didacticiel nécessite que vous exécutiez Azure CLI version 2.0.30 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-an-availability-set"></a>Créer un groupe à haute disponibilité

Vous pouvez créer un groupe à haute disponibilité à l’aide de la commande [az vm availability-set create](/cli/azure/vm/availability-set). Dans cet exemple, le nombre de domaines de mise à jour et d’erreur est défini sur *2* pour le groupe à haute disponibilité nommé *myAvailabilitySet* au sein du groupe de ressources *myResourceGroupAvailability*.

Commencez par créer un groupe de ressources avec [az group create](/cli/azure/group#az_group_create), puis créez le groupe à haute disponibilité :

```azurecli-interactive
az group create --name myResourceGroupAvailability --location eastus

az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Les groupes à haute disponibilité vous permettent d’isoler des ressources dans des domaines d’erreur et des domaines de mise à jour. Un **domaine d’erreur** représente une collection isolée comportant un serveur, un réseau et des ressources de stockage. Dans l’exemple précédent, le groupe à haute disponibilité est distribué sur deux domaines d’erreur au minimum lors du déploiement des machines virtuelles. Le groupe à haute disponibilité est également distribué sur deux **domaines de mise à jour**. Deux domaines de mise à jour garantissent que, au moment où Azure effectue des mises à jour logicielles, les ressources des machines virtuelles sont isolées, ce qui évite la mise à jour simultanée de tous les logiciels en cours d’exécution sur la machine virtuelle.


## <a name="create-vms-inside-an-availability-set"></a>Créer des machines virtuelles dans un groupe à haute disponibilité

Vous devez créer des machines virtuelles au sein du groupe à haute disponibilité pour vous assurer qu’elles sont correctement réparties dans le matériel. Il est impossible d’ajouter une machine virtuelle existante à un groupe à haute disponibilité après sa création.

Quand une machine virtuelle est créée à l’aide de la commande [az vm create](/cli/azure/vm), utilisez le paramètre `--availability-set` pour spécifier le nom du groupe à haute disponibilité.

```azurecli-interactive
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --vnet-name myVnet \
     --subnet mySubnet \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys
done
```

Le groupe à haute disponibilité comporte désormais deux machines virtuelles. Ces machines virtuelles se trouvant dans le même groupe à haute disponibilité, Azure veille à ce que celles-ci et toutes leurs ressources (dont les disques de données) soient distribuées sur des éléments matériels isolés. Cette distribution contribue à assurer une disponibilité sensiblement plus importante de la solution globale de machine virtuelle.

La distribution du groupe à haute disponibilité peut être affichée dans le portail sur Groupes de ressources > myResourceGroupAvailability > myAvailabilitySet. Les machines virtuelles sont distribuées sur les deux domaines d’erreur et de mise à jour, comme indiqué dans l’exemple suivant :

![Groupe à haute disponibilité dans le portail](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Vérifier les tailles de machines virtuelles disponibles

Des machines virtuelles supplémentaires peuvent être ajoutées au groupe à haute disponibilité ultérieurement, lorsque les tailles de machine virtuelle sont disponibles sur le matériel. Utilisez [az vm availability-set list-sizes](/cli/azure/vm/availability-set#az_vm_availability_set_list_sizes) afin de répertorier toutes les tailles disponibles sur le cluster matériel pour le groupe à haute disponibilité :

```azurecli-interactive
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un groupe à haute disponibilité
> * Créer une machine virtuelle dans un groupe à haute disponibilité
> * Vérifier les tailles de machines virtuelles disponibles

Passez au didacticiel suivant pour en savoir plus sur les groupes de machines virtuelles identiques.

> [!div class="nextstepaction"]
> [Créer un groupe de machines virtuelles identiques](tutorial-create-vmss.md)

* Pour en savoir plus sur les zones de disponibilité, consultez la [documentation sur les zones de disponibilité](../../availability-zones/az-overview.md).
* Vous trouverez de la documentation supplémentaire sur les groupes à haute disponibilité et les zones de disponibilité [ici](../availability.md).
* Pour tester les zones de disponibilité, consultez [Créer une machine virtuelle Linux dans une zone de disponibilité avec l’interface de ligne de commande Azure](./create-cli-availability-zone.md).