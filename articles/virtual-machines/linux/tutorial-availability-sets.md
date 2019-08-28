---
title: 'Didacticiel : haute disponibilité pour les machines virtuelles Linux dans Azure | Microsoft Docs'
description: Avec ce tutoriel, vous allez apprendre à utiliser Azure CLI pour déployer des machines virtuelles hautement disponibles dans les groupes à haute disponibilité
documentationcenter: ''
services: virtual-machines-linux
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: tutorial
ms.date: 08/24/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 718f2e3391fe89bcc64426c37401f9bf91643201
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69641149"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-the-azure-cli"></a>Didacticiel : Créer et déployer des machines virtuelles hautement disponibles avec Azure CLI

Ce didacticiel explique comment améliorer la disponibilité et la fiabilité de vos solutions de machine virtuelle sur Azure en utilisant une fonctionnalité appelée Groupes à haute disponibilité. Les groupes à haute disponibilité veillent à ce que les machines virtuelles que vous déployez sur Azure soient distribuées sur plusieurs clusters matériels isolés. Leur utilisation garantit qu’en cas de défaillance matérielle ou logicielle dans Azure, seul un sous-ensemble de vos machines virtuelles est affecté et que votre solution globale reste disponible et opérationnelle.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un groupe à haute disponibilité
> * Créer une machine virtuelle dans un groupe à haute disponibilité
> * Vérifier les tailles de machines virtuelles disponibles

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, ce didacticiel nécessite que vous exécutiez Azure CLI version 2.0.30 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="high-availability-in-azure-overview"></a>Vue d’ensemble de la haute disponibilité dans Azure
La haute disponibilité dans Azure peut être créée de nombreuses façons différentes. Deux options s’offrent à vous : les groupes à haute disponibilité et les zones de disponibilité. Si vous utilisez des groupes à haute disponibilité, vos machines virtuelles sont protégées des défaillances qui peuvent se produire dans un centre de données, notamment les défaillances matérielles et les défaillances logicielles Azure. Si vous utilisez des zones de disponibilité, vos machines virtuelles sont placées sur une infrastructure physiquement séparée sans ressources partagées. Elles sont donc complètement protégées des défaillances du centre de données.

Utilisez des groupes à haute disponibilité ou des zones de disponibilité quand vous souhaitez déployer des solutions fiables basées sur des machines virtuelles dans Azure.

### <a name="availability-set-overview"></a>Vue d’ensemble des groupes à haute disponibilité

Un groupe à haute disponibilité est une fonctionnalité de regroupement logique que vous pouvez utiliser dans Azure pour vous assurer que les ressources de machine virtuelle que vous y incluez sont isolées les unes des autres lors de leur déploiement dans un centre de données Azure. Azure veille à ce que les machines virtuelles que vous placez dans un groupe à haute disponibilité s’exécutent sur plusieurs serveurs physiques, racks de calcul, unités de stockage et commutateurs réseau. En cas de défaillance matérielle ou logicielle dans Azure, seul un sous-ensemble de vos machines virtuelles est affecté et votre application globale reste opérationnelle et continue d’être disponible pour vos clients. Les groupes à haute disponibilité sont une fonctionnalité essentielle pour créer des solutions cloud fiables.

Prenons l’exemple d’une solution basée sur une machine virtuelle classique dans laquelle vous disposez de quatre serveurs web frontaux et utilisez deux machines virtuelles principales hébergeant une base de données. Avec Azure, vous pouvez définir deux groupes à haute disponibilité avant de déployer vos machines virtuelles : l’un pour le niveau « web » et l’autre pour le niveau « base de données ». Lorsque vous créez une machine virtuelle, vous pouvez spécifier le groupe à haute disponibilité en tant que paramètre pour la commande az vm create, de sorte qu’Azure veille automatiquement à ce que les machines virtuelles que vous créez dans le groupe disponible soient isolées sur plusieurs ressources matérielles. Si le matériel sur lequel s’exécute l’une de vos machines virtuelles serveur web ou serveur de base de données rencontre un problème, vous savez que les autres instances de votre serveur web et de vos machines virtuelles de base de données continueront à s’exécuter parce qu’elles se trouvent sur d’autres éléments matériels.

### <a name="availability-zone-overview"></a>Vue d’ensemble des zones de disponibilité

Les Zones de disponibilité constituent une offre à haute disponibilité qui protège vos applications et données contre les pannes des centres de données. Les Zones de disponibilité sont des emplacements physiques uniques au sein d’une région Azure. Chaque zone de disponibilité est composée d’un ou de plusieurs centres de données équipés d’une alimentation, d’un système de refroidissement et d’un réseau indépendants. Pour garantir la résilience, il existe un minimum de trois zones distinctes dans toutes les régions activées. La séparation physique des Zones de disponibilité dans une région protège les applications et les données des défaillances dans le centre de données. Les services redondants interzone répliquent vos applications et données entre des Zones de disponibilité pour les protéger contre des points uniques de panne. Avec les zones de disponibilité, Azure propose des contrats de niveau de service de durée de fonctionnement des machines virtuelles de pointe de 99,99 %.

Comme nous l’avons fait pour les groupes à haute disponibilité, prenons l’exemple d’une solution basée sur une machine virtuelle classique dans laquelle vous disposez de quatre serveurs web front-end et utilisez deux machines virtuelles back-end hébergeant une base de données. Comme vous l’avez fait avec les groupes à haute disponibilité, vous allez déployer vos machines virtuelles dans deux zones de disponibilité distinctes : une pour le niveau « web » et une pour le niveau de « base de données ». Quand vous créez une machine virtuelle et spécifiez la zone de disponibilité en tant que paramètre pour la commande az vm create, Azure veille automatiquement à ce que les machines virtuelles créées soient isolées sur des zones de disponibilité totalement distinctes. Si un problème affecte l’ensemble du centre de données sur lequel s’exécute l’une de vos machines virtuelles de serveur web ou de serveur de base de données, vous savez que les autres instances de ces machines virtuelles continueront à s’exécuter. En effet, elles s’exécutent sur des centres de données totalement distincts.

## <a name="create-an-availability-set"></a>Créer un groupe à haute disponibilité

Vous pouvez créer un groupe à haute disponibilité à l’aide de la commande [az vm availability-set create](/cli/azure/vm/availability-set). Dans cet exemple, le nombre de domaines de mise à jour et d’erreur est défini sur *2* pour le groupe à haute disponibilité nommé *myAvailabilitySet* au sein du groupe de ressources *myResourceGroupAvailability*.

Commencez par créer un groupe de ressources avec [az group create](/cli/azure/group#az-group-create), puis créez le groupe à haute disponibilité :

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

Des machines virtuelles supplémentaires peuvent être ajoutées au groupe à haute disponibilité ultérieurement, lorsque les tailles de machine virtuelle sont disponibles sur le matériel. Utilisez [az vm availability-set list-sizes](/cli/azure/vm/availability-set#az-vm-availability-set-list-sizes) afin de répertorier toutes les tailles disponibles sur le cluster matériel pour le groupe à haute disponibilité :

```azurecli-interactive
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créer un groupe à haute disponibilité
> * Créer une machine virtuelle dans un groupe à haute disponibilité
> * Vérifier les tailles de machines virtuelles disponibles

Passez au didacticiel suivant pour en savoir plus sur les groupes de machines virtuelles identiques.

> [!div class="nextstepaction"]
> [Créer un groupe de machines virtuelles identiques](tutorial-create-vmss.md)

* Pour en savoir plus sur les zones de disponibilité, consultez la [documentation sur les zones de disponibilité](../../availability-zones/az-overview.md).
* Vous trouverez de la documentation supplémentaire sur les groupes à haute disponibilité et les zones de disponibilité [ici](./manage-availability.md).
* Pour tester les zones de disponibilité, consultez [Créer une machine virtuelle Linux dans une zone de disponibilité avec l’interface de ligne de commande Azure](./create-cli-availability-zone.md).
