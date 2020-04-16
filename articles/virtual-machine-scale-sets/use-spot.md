---
title: Créer un groupe identique qui utilise des machines virtuelles Azure Spot
description: Découvrez comment créer des groupes identiques de machines virtuelles Azure qui utilisent des machines virtuelles Spot pour réaliser des économies sur les coûts.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: a7bd22032a554c83a2ea2323ffdb3ae52dfe4faf
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545935"
---
# <a name="azure-spot-vms-for-virtual-machine-scale-sets"></a>Machines virtuelles Azure Spot pour les groupes identiques de machines virtuelles 

L’utilisation d’Azure Spot sur des groupes identiques vous permet de tirer parti de notre capacité inutilisée en réalisant des économies significatives. Dès qu’Azure a besoin de récupérer toute la capacité, l’infrastructure Azure supprime les instances Spot. Les instances Spot sont donc appropriées pour les charges de travail capables de gérer les interruptions, comme les travaux de traitement par lots, les environnements de développement et de test, les charges de travail de calcul importantes, entre autres.

La capacité disponible dépend de divers facteurs, tels que la taille, la région, l’heure, etc. Lors du déploiement d’instances Spot sur des groupes identiques, Azure alloue l’instance uniquement si la capacité est disponible, mais qu’il n’existe aucun contrat SLA pour ces instances. Un groupe identique Spot est déployé dans un domaine d’erreur unique. Il n’offre aucune garantie de haute disponibilité.


## <a name="pricing"></a>Tarifs

Les tarifs des instances Spot sont variables, en fonction de la région et de la référence SKU. Pour plus d’informations, consultez les prix pour [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) et [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/). 


En raison de la variabilité des tarifs, vous avez la possibilité de définir un prix maximal en dollars américains (USD) ayant jusqu’à 5 décimales. Par exemple, la valeur `0.98765` correspond à un prix maximal de 0,98765 $ USD par heure. Si vous définissez `-1` comme prix maximal, l’instance n’est pas supprimée en fonction du prix. Le prix de l’instance sera le prix actuel de Spot ou le prix d’une instance standard, la valeur la plus faible étant retenue, à condition que la capacité et le quota soient disponibles.

## <a name="eviction-policy"></a>Stratégie d’éviction

Quand vous créez des groupes identiques Spot, vous pouvez affecter à la stratégie d’éviction la valeur*Libérer* (par défaut) ou *Supprimer*. 

La stratégie *Libérer* affecte à vos instances écartées l’état « arrêté-libéré », ce qui vous permet de redéployer les instances écartées. Toutefois, la réussite de l’allocation n’est pas garantie. Les machines virtuelles libérées sont comptabilisées dans votre quota d’instances de groupe identique, et vos disques sous-jacents vous sont facturés. 

Si vous souhaitez que les instances dans votre groupe identique Spot soient supprimées après avoir été écartées, affectez à la stratégie d’éviction la valeur *Supprimer*. Avec cette configuration, vous pouvez créer d’autres machines virtuelles en définissant la propriété du nombre d’instances du groupe identique à une valeur plus grande. Les machines virtuelles évincées sont supprimées en même temps que leurs disques sous-jacents. Vous n’êtes donc pas facturé pour le stockage. Vous pouvez également utiliser la fonctionnalité de mise à l’échelle automatique des groupes identiques pour essayer et compenser automatiquement les machines virtuelles évincées, mais sans garantie de réussite de l’allocation. Nous vous recommandons d’utiliser uniquement la mise à l’échelle automatique des groupes identiques Spot quand vous définissez la stratégie d’éviction avec suppression pour éviter la facturation de vos disques et le dépassement de vos limites de quota. 

Les utilisateurs peuvent s’abonner pour recevoir des notifications dans la machine virtuelle via [Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md). Vous serez ainsi informé si vos machines virtuelles sont en cours d’éviction, et vous aurez 30 secondes pour terminer vos tâches et arrêter la machine virtuelle avant que ne commence l’éviction. 


## <a name="deploying-spot-vms-in-scale-sets"></a>Déploiement de machines virtuelles Spot dans des groupes identiques

Pour déployer des machines virtuelles Spot dans des groupes identiques, définissez le nouvel indicateur *Priority* sur *Spot*. Toutes les machines virtuelles dans votre groupe identique sont alors configurées sur Spot. Pour créer un groupe identique avec des machines virtuelles Spot, utilisez l’une des méthodes suivantes :
- [Azure portal](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Modèles Microsoft Azure Resource Manager](#resource-manager-templates)

## <a name="portal"></a>Portail

Le processus de création d’un groupe identique qui utilise des machines virtuelles Spot est le même que celui décrit dans l’[article de démarrage rapide](quick-create-portal.md). Lorsque vous déployez un groupe identique, vous pouvez choisir de définir l’indicateur Spot et la stratégie d’éviction : ![Créer un groupe identique avec des machines virtuelles Spot](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

Le processus de création d’un groupe identique avec des machines virtuelles Spot est le même que celui décrit dans l’[article de démarrage rapide](quick-create-cli.md). Ajoutez simplement « --Priority Spot » et `--max-price`. Dans cet exemple, nous utilisons `-1` pour `--max-price` afin que l’instance ne soit pas supprimée en fonction du prix.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

Le processus de création d’un groupe identique avec des machines virtuelles Spot est le même que celui décrit dans l’[article de démarrage rapide](quick-create-powershell.md).
Ajoutez simplement « -Priority Spot » et fournissez une valeur `-max-price` pour [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>Modèles Resource Manager

Le processus de création d’un groupe identique qui utilise des machines virtuelles Spot est le même que celui décrit dans l’article de démarrage rapide pour [Linux](quick-create-template-linux.md) ou [Windows](quick-create-template-windows.md). 

Pour les déploiements de machines virtuelles Spot à l’aide d’un modèle, utilisez `"apiVersion": "2019-03-01"` ou version ultérieure. Ajoutez les propriétés `priority`, `evictionPolicy` et `billingProfile` à la section `"virtualMachineProfile":` de votre modèle : 

```json
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
```

Pour supprimer l’instance après son exclusion, remplacez le paramètre `evictionPolicy` par `Delete`.

## <a name="faq"></a>Questions fréquentes (FAQ)

**Q :** Une fois créée, une instance Spot est-elle identique à une instance standard ?

**R :** Oui, sauf qu’il n’existe aucun contrat SLA pour les machines virtuelles Spot et qu’elles peuvent être supprimées à tout moment.


**Q :** Que faire quand votre machine virtuelle est supprimée alors que vous avez encore besoin de capacité ?

**R :** Nous vous recommandons d’utiliser des machines virtuelles standard au lieu de machines virtuelles Spot si vous avez besoin de capacité immédiatement.


**Q :** Comment les quotas sont-ils gérés pour Spot ?

**R :** Les instances Spot et les instances standard auront des pools de quotas distincts. Le quota Spot est partagé entre les machines virtuelles et les instances de groupe identique. Pour plus d’informations, consultez [Abonnement Azure et limites, quotas et contraintes de service](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**Q :** Puis-je demander une augmentation de mon quota pour Spot ?

**R :** Oui, vous pouvez demander une augmentation de votre quota pour les machines virtuelles Spot via la [procédure de demande de quota standard](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).


**Q :** Puis-je convertir des groupes identiques existants en groupes identiques Spot ?

**R :** Non. La définition de l’indicateur `Spot` n’est prise en charge qu’au moment de la création.


**Q :** Si j’utilisais `low` pour les groupes identiques basse priorité, dois-je commencer à utiliser `Spot` à la place ?

**R :** Pour le moment, `low` et `Spot` fonctionnent, mais vous devez commencer à passer à l’utilisation de `Spot`.


**Q :** Puis-je créer un groupe identique avec des machines virtuelles normales et Spot ?

**R :** Non. Un groupe identique ne prend en charge qu’un seul type de priorité.


**Q :**  Puis-je utiliser la mise à l’échelle automatique avec des groupes identiques Spot ?

**R :** Oui. Vous pouvez définir des règles de mise à l’échelle automatique sur votre groupe identique Spot. Si vos machines virtuelles sont supprimées, la mise à l’échelle automatique peut essayer de créer des machines virtuelles Spot. N’oubliez pas que cette fonctionnalité n’est pas garantie. 


**Q :**  La mise à l’échelle automatique fonctionne-t-elle avec les deux stratégies d’éviction (Libérer et Supprimer) ?

**R :** Il est recommandé de choisir la stratégie d’éviction Supprimer avec la mise à l’échelle automatique. En effet, le nombre d’instances libérées est soustrait de la capacité sur le groupe identique. Quand vous utilisez la mise à l’échelle automatique, le nombre d’instances cibles est souvent rapidement atteint en raison des instances libérées et écartées. 


**Q :** Quels sont les canaux qui prennent en charge les machines virtuelles Spot ?

**R :** Consultez le tableau ci-dessous pour connaître la disponibilité des machines virtuelles Spot.

<a name="channel"></a>

| Canaux Azure               | Disponibilité des machines virtuelles Azure Spot       |
|------------------------------|-----------------------------------|
| Contrat Entreprise         | Oui                               |
| Paiement à l’utilisation                | Oui                               |
| Fournisseur de services cloud (CSP) | [Contactez votre partenaire](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Avantages                     | Non disponible                     |
| Sponsorisé                    | Non disponible                     |
| Version d’évaluation gratuite                   | Non disponible                     |


**Q :** Où puis-je poster des questions ?

**R :** Vous pouvez poster et étiqueter vos questions avec `azure-spot` sur [Questions et réponses](https://docs.microsoft.com/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Étapes suivantes

Consultez la page [Tarification des groupes identiques de machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) pour connaître les tarifs appliqués.
