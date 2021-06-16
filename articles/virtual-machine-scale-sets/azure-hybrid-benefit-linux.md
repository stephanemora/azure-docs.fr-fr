---
title: Azure Hybrid Benefit pour les groupes de machines virtuelles identiques Linux
description: Découvrez comment Azure Hybrid Benefit peut appliquer un groupe de machines virtuelles identiques pour vous aider à réaliser des économies sur vos machines virtuelles Linux s’exécutant sur Azure.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machine-scale-sets
ms.subservice: azure-hybrid-benefit
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/20/2021
ms.author: mathapli
ms.openlocfilehash: 71d585d16f4026ac605e7a61e64af89df806fed8
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111950025"
---
# <a name="azure-hybrid-benefit-for-linux-virtual-machine-scale-set"></a>Azure Hybrid Benefit pour groupe de machines virtuelles identiques Linux

**Azure Hybrid Benefit pour groupe de machines virtuelles identiques Linux est désormais en disponibilité générale**. L’avantage AHB peut vous aider à réduire le coût d’exécution de vos [groupes de machines virtuelles identiques](./overview.md) RHEL et SLES.

Avec cet avantage, vous payez uniquement le coût d’infrastructure de votre groupe identique. L’avantage s’applique à toutes les images PAYG (paiement à l’accès) de la place de marché RHEL et SLES.


>[!NOTE]
> Cet article décrit les Azure Hybrid Benefit pour Linux VMSS. Il existe un autre [article disponible [ici AHB pour les machines virtuelles Linux](../virtual-machines/linux/azure-hybrid-benefit-linux.md), qui est déjà disponible pour les clients Azure depuis novembre 2020.

## <a name="benefit-description"></a>Description de l’avantage
Azure Hybrid vous permet d’utiliser les licences d’accès cloud existantes de Red Hat ou SUSE et de convertir de manière flexible les instances de groupe de machines virtuelles identiques pour la facturation BYOS. 

Le groupe de machines virtuelles identiques déployé à partir d’images de la place de marché PAYG facture les frais d’infrastructure et de logiciel. Avec AHB, les instances PAYG peuvent être converties en un modèle de facturation BYOS sans redéploiement.

:::image type="content" source="./media/azure-hybrid-benefit-linux/azure-hybrid-benefit-linux-cost.png" alt-text="Visualisation des coûts Azure Hybrid Benefit sur les machines virtuelles Linux.":::

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux"></a>Étendue de l’éligibilité Azure Hybrid Benefit pour Linux
Azure Hybrid Benefit est disponible pour toutes les images PAYG RHEL et SLES de Place de marché Azure. L’avantage n’est pas encore disponible pour les images BYOS RHEL ou SLES ou pour les images personnalisées de la Place de marché Azure.

Les instances d’Azure Dedicated Host et les avantages hybrides SQL ne sont pas éligibles à Azure Hybrid Benefit si vous utilisez déjà l’avantage avec des machines virtuelles Linux.

## <a name="get-started"></a>Bien démarrer

### <a name="red-hat-customers"></a>Clients Red Hat

Azure Hybrid Benefit pour RHEL est disponible pour les clients Red Hat qui répondent à ces deux critères :

- Avoir des abonnements RHEL actifs ou inutilisés qui peuvent être utilisés dans Azure
- Avoir activé un ou plusieurs de ces abonnements pour une utilisation dans Azure avec le programme [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access)

> [!IMPORTANT]
> Assurez-vous que l’abonnement approprié a été activé sur le programme [cloud-access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access).

Pour commencer à utiliser l’avantage pour Red Hat :

1. Activez vos abonnements RHEL éligibles dans Azure à l’aide de l’[interface client de Red Hat Cloud Access](https://access.redhat.com/management/cloud).

   Les abonnements Azure que vous fournissez pendant le processus d’activation de Red Hat Cloud Access seront alors autorisés à utiliser la fonctionnalité Azure Hybrid Benefit.
1. Appliquez Azure Hybrid Benefit à l’un de vos groupes de machines virtuelles identiques RHEL PAYG existants et nouveaux. Vous pouvez utiliser le portail Azure ou Azure CLI pour activer l’avantage.
1. Effectuez les [étapes recommandées suivantes](https://access.redhat.com/articles/5419341) pour configurer des sources de mise à jour pour les machines virtuelles RHEL et tenir compte des consignes de conformité des abonnements RHEL.


### <a name="suse-customers"></a>Clients SUSE

Pour commencer à utiliser l’avantage pour SUSE :

1. Inscrivez-vous au programme de cloud public SUSE.
1. Appliquez l’avantage à votre groupe de machines virtuelles identiques nouvellement créé ou existant via le portail Azure ou Azure CLI.
1. Inscrivez vos machines virtuelles recevant l’avantage auprès d’une source de mises à jour distincte.


## <a name="enable-and-disable-the-benefit-on-azure-portal"></a>Activer et désactiver l’avantage sur le Portail Azure 
### <a name="azure-portal-example-to-enable-the-benefit-during-creation"></a>Exemple pour activer l’avantage pendant la création via le portail Azure :
1. Visitez le [portail Microsoft Azure](https://portal.azure.com/)
1. Accédez à la page « Créer un groupe de machines virtuelles identiques » sur le portail.
 ![AHB lors de la création de VMSS](./media/azure-hybrid-benefit-linux/create-vmss-ahb.png)
1. Cochez la case pour activer la conversion AHB et utiliser des licences d’accès Cloud.
 ![Case à cocher AHB lors de la création de VMSS](./media/azure-hybrid-benefit-linux/create-vmss-ahb-checkbox.png)
1. Créer un groupe de machines virtuelles identiques en suivant les instructions suivantes
1. Vérifiez le panneau **Configuration** et l’option activée s’affiche. 
![Panneau du système d’exploitation AHB après la création](./media/azure-hybrid-benefit-linux/create-vmss-ahb-os-blade.png)

### <a name="azure-portal-example-to-enable-the-benefit-for-an-existing-virtual-machine-scale-set"></a>Exemple pour activer l’avantage d’un groupe de machines virtuelles identiques existant via le portail Azure :
1. Visitez le [portail Microsoft Azure](https://portal.azure.com/)
1. Ouvrez la page « Groupe de machines virtuelles identiques » sur laquelle vous souhaitez appliquer la conversion.
1. Accédez à l’option **Système d’exploitation** sur la gauche. La section Licences s’affiche. Pour activer la conversion AHB, cochez la case d’option « Oui », puis cochez la case confirmation.
![Panneau de configuration AHB après la création](./media/azure-hybrid-benefit-linux/create-vmss-ahb-os-blade.png)



## <a name="enable-and-disable-the-benefit-using-azure-cli"></a>Activer et désactiver l’avantage avec Azure CLI

Vous pouvez utiliser la commande `az vmss update` pour mettre à jour des machines virtuelles existantes. Pour les machines virtuelles RHEL, exécutez la commande avec le paramètre `--license-type` défini sur `RHEL_BYOS`. Pour les machines virtuelles SLES, exécutez la commande avec le paramètre `--license-type` défini sur `SLES_BYOS`.

### <a name="cli-example-to-enable-the-benefit"></a>Exemple CLI pour activer l’avantage
```azurecli
# This will enable the benefit on a RHEL VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>Exemple CLI pour désactiver l’avantage
Pour désactiver l’avantage, utilisez la valeur `None` pour `--license-type` :

```azurecli
# This will disable the benefit on a VM
az vmss update -g myResourceGroup -n myVmName --license-type None
```

>[!NOTE]
> Les groupes identiques ont une [« stratégie de mise à niveau »](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) qui détermine la façon dont les machines virtuelles sont mises à jour à l’aide du dernier modèle du groupe identique. Par conséquent, si votre VMSS a une stratégie de mise à niveau « automatique », l’avantage AHB est appliqué automatiquement à mesure que les instances de machine virtuelle sont mises à jour. Si les VMSS ont une stratégie de mise à niveau « propagée », en fonction des mises à jour planifiées, AHB est appliqué.
Dans le cas d’une stratégie de mise à niveau « manuelle », vous devez effectuer une « mise à niveau manuelle » de chaque machine virtuelle existante.  

### <a name="cli-example-to-upgrade-virtual-machine-scale-set-instances-in-case-of-manual-upgrade-policy"></a>Exemple de CLI pour mettre à niveau des instances de groupe de machines virtuelles identiques en cas de stratégie « Mise à niveau manuelle » 
```azurecli
# This will bring VMSS instances up to date with latest VMSS model 
az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
```

## <a name="apply-the-azure-hybrid-benefit-at-virtual-machine-scale-set-create-time"></a>Appliquer Azure Hybrid Benefit à l’heure de création du groupe de machines virtuelles identiques 
En plus d’appliquer Azure Hybrid Benefit au groupe de machines virtuelles identiques de paiement à l’utilisation existant, vous pouvez appeler l’avantage au moment de la création du groupe de machines virtuelles identiques. Les avantages de cette méthode sont triples :
- Vous pouvez approvisionner des instances de groupe de machines virtuelles identiques PAYG et BYOS en utilisant la même image et le même processus.
- Cela permet de changer de mode de licence à l’avenir, ce qui n’est pas possible avec une image BYOS uniquement.
- Par défaut, les instances de groupe de machines virtuelles identiques seront connectées à Red Hat Update Infrastructure (RHUI) afin de garantir qu’elles restent à jour et sécurisées. Vous pouvez modifier le mécanisme de mise à jour à tout moment après le déploiement.

### <a name="cli-example-to-create-virtual-machine-scale-set-with-ahb-benefit"></a>Exemple de CLI pour créer un groupe de machines virtuelles identiques avec l’avantage AHB
```azurecli
# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type RHEL_BYOS 

# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type SLES_BYOS
```

## <a name="next-steps"></a>Étapes suivantes
* [Découvrez comment créer et mettre à jour des machines virtuelles et ajouter des types de licences (RHEL_BYOS, SLES_BYOS) pour Azure Hybrid Benefit à l’aide d’Azure CLI.](/cli/azure/vmss)