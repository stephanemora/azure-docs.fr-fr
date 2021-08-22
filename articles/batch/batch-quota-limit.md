---
title: Quotas et limites du service
description: En savoir plus sur les contraintes, les limites et les quotas par défaut d’Azure Batch, et comment demander une augmentation de quota
ms.topic: conceptual
ms.date: 07/20/2021
ms.custom: seodec18
ms.openlocfilehash: ad5701c65f9f2068a1a68084b755f8d0a013b8c8
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114465571"
---
# <a name="batch-service-quotas-and-limits"></a>Quotas et limites du service Batch

Comme avec d’autres services Azure, il existe des limites concernant certaines ressources associées au service Batch. La plupart de ces limites représentent des quotas par défaut appliqués par Azure au niveau de l’abonnement ou du compte.

Gardez ces quotas à l’esprit quand vous concevez et que vous augmentez vos charges de travail Batch. Par exemple, si votre pool n’atteint pas le nombre cible de nœuds de calcul que vous avez spécifié, vous avez peut-être atteint la limite du quota de cœurs de votre compte Batch.

Vous pouvez exécuter plusieurs charges de travail Batch dans un compte Batch ou répartir vos charges de travail entre plusieurs comptes Batch se trouvant dans le même abonnement mais dans différentes régions Azure.

Si vous envisagez d’exécuter des charges de travail de production dans Batch, vous devrez peut-être affecter à un ou plusieurs des quotas une valeur supérieure à la valeur par défaut. Pour augmenter un quota, vous pouvez [demander une augmentation de quota](#increase-a-quota) gratuitement.

## <a name="resource-quotas"></a>Quotas de ressources

Un quota est une limite, pas une garantie de capacité. Si vous avez des besoins de capacité à grande échelle, contactez le support Azure.

Notez également que les quotas ne sont pas des valeurs garanties. Les quotas peuvent varier en fonction des modifications du service Batch ou d’une demande d’utilisateur visant à modifier une valeur de quota.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="core-quotas"></a>Quota de cœurs

### <a name="cores-quotas-in-batch-service-mode"></a>Quotas de cœurs en mode de service Batch

Des quotas de cœurs existent pour chaque série de machines virtuelles prise en charge par Batch. Ces quotas de cœurs sont affichés dans la page **Quotas** du portail Azure. Les limites de quota de la série de machines virtuelles peuvent être mises à jour avec une demande de support, comme indiqué ci-dessous. Pour les nœuds dédiés, Batch applique une limite de quota de cœurs pour chaque série de machines virtuelles ainsi qu’une limite de quota de cœurs total pour l’ensemble du compte Batch. Pour les nœuds de basse priorité, Batch applique uniquement un quota de cœurs total pour le compte Batch, sans aucune distinction entre les différentes séries de machines virtuelles.

### <a name="cores-quotas-in-user-subscription-mode"></a>Quotas de cœurs en mode Abonnement utilisateur

Si vous avez créé un [compte Batch](accounts.md) en mode d’allocation de pool défini sur **abonnement utilisateur**, les machines virtuelles Batch et les autres ressources, telles que les comptes de stockage, sont créées directement dans votre abonnement lors de la création d’un pool. Les quotas de cœurs Azure Batch ne s’appliquent pas et les quotas de votre abonnement pour les cœurs de calcul régionaux, les cœurs de calcul par série et d’autres ressources sont utilisés et appliqués.

Pour en savoir plus sur ces quotas, consultez [Abonnement Azure et limites, quotas et contraintes de service](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Limites de taille de pool

Des limites de taille de pool sont définies par le service Batch. Contrairement aux [quotas des ressources](#resource-quotas), ces valeurs ne peuvent pas être modifiées. Seuls les pools présentant une communication entre nœuds et des images personnalisées possèdent des restrictions différentes du quota standard.

| **Ressource** | **Limite maximale** |
| --- | --- |
| **Nœuds de calcul dans un [pool prenant en charge la communication entre nœuds](batch-mpi.md)**  ||
| Mode d’allocation de pool du service Batch | 100 |
| Mode d’allocation de pool de l'abonnement Batch | 80 |
| **Nœuds de calcul dans un [pool créé avec une ressource d’image managée](batch-custom-images.md)** <sup>1</sup> ||
| Nœuds dédiés | 2000 |
| Nœuds de faible priorité | 1 000 |

<sup>1</sup> Pour les pools ne prenant pas en charge la communication entre nœuds.

## <a name="other-limits"></a>Autres limites

Ces limites supplémentaires sont définies par le service Batch. Contrairement aux [quotas des ressources](#resource-quotas), ces valeurs ne peuvent pas être modifiées.

| **Ressource** | **Limite maximale** |
| --- | --- |
| [Tâches simultanées](batch-parallel-node-tasks.md) par nœud de calcul | 4 x nombre de cœurs de nœud |
| [Applications](batch-application-packages.md) par compte Batch | 200 |
| Packages d’applications par application | 40 |
| Packages d’applications par pool | 10 |
| Durée de vie maximale de la tâche | 180 jours<sup>1</sup> |
| [Montages](virtual-file-mount.md) par nœud de calcul | 10 |
| Certificats par pool | 12 |

<sup>1</sup> La durée de vie maximale d’une tâche, entre le moment où elle est ajoutée au travail et la fin de son exécution, est de 180 jours. Les tâches terminées sont conservées pendant sept jours. Les données de tâches non terminées pendant la durée de vie maximale ne sont pas accessibles.

## <a name="view-batch-quotas"></a>Afficher les quotas Batch

Pour voir vos quotas de compte Batch dans le [portail Azure](https://portal.azure.com) :

1. Sélectionnez **Comptes Batch**, puis le compte Batch qui vous intéresse.
1. Sélectionnez **Quotas** dans le menu du compte Batch.
1. Affichez les quotas actuellement appliqués au compte Batch.

:::image type="content" source="./media/batch-quota-limit/account-quota-portal.png" alt-text="Capture d’écran montrant les quotas de compte Batch dans le portail Azure.":::

## <a name="increase-a-quota"></a>Augmenter un quota

Vous pouvez demander une augmentation de quota pour votre compte Batch ou votre abonnement à l’aide du [portail Azure](https://portal.azure.com) ou de l’[API REST de quota Azure](#azure-quota-rest-api).

Le type d’augmentation de quota varie selon le mode d’allocation de pool de votre compte Batch. Pour demander une augmentation de quota, vous devez inclure la série de machines virtuelles dont vous souhaitez augmenter le quota. Lorsque l’augmentation de quota est appliquée, elle l’est pour toutes les séries de machines virtuelles.

Une fois que vous avez envoyé votre demande de support, le support Azure vous contactera. Les demandes de quota peuvent être effectuées en quelques minutes ou jusqu’à deux jours ouvrables.

### <a name="azure-portal"></a>Portail Azure

1. Dans la page **Quotas**, sélectionnez **Demander une augmentation de quota**. Vous pouvez également sélectionner la vignette **Aide + support** dans le tableau de bord de votre portail (ou à partir du point d’interrogation ( **?** ) en haut à droite du portail), puis sélectionner **Nouvelle demande de support.**

1. Dans **De base** :

    1. Pour **Type de problème**, sélectionnez **Limites du service et des abonnements (quotas)** .
    1. Sélectionnez votre abonnement.
    1. Pour **Type de quota**, sélectionnez **Batch**.
    1. Sélectionnez **Suivant** pour continuer.

1. Dans **Détails** :

    1. Dans la section **Fournir des détails**, spécifiez l’emplacement, le type de quota et le compte Batch (le cas échéant), puis sélectionnez le ou les quotas à augmenter.

       :::image type="content" source="media/batch-quota-limit/quota-increase.png" alt-text="Capture d’écran de la page Détails du quota lors d’une demande d’augmentation du quota.":::

       Types de quotas :

       - **Par compte Batch**  
         Utilisez cette option pour demander des augmentations de quota spécifiques à un seul compte Batch, notamment les cœurs dédiés et les cœurs à faible priorité ainsi que le nombre de travaux et de pools.

         Si vous sélectionnez cette option, spécifiez le compte Batch auquel cette requête doit s’appliquer, puis sélectionnez le ou les quotas à mettre à jour. Indiquez la nouvelle limite que vous demandez pour chaque ressource.

         Un quota basse priorité est une valeur unique dans toutes les séries de machines virtuelles. Si vous avez besoin de références SKU avec contraintes, vous devez sélectionner **Cœurs basse priorité** et inclure les familles de machines virtuelles à la demande.

       - **Tous les comptes de cette région**  
         Utilisez cette option pour demander des augmentations de quota qui s’appliquent à tous les comptes Batch d’une région, par exemple le nombre de comptes Batch par région et par abonnement.

    1. Dans **Méthode de support**, sélectionnez une **Gravité** en fonction de l’[impact commercial](https://aka.ms/supportseverity) ainsi que du mode de contact et de la langue de support par défaut.

    1. Dans **Informations de contact**, vérifiez et entrez les coordonnées nécessaires.

1. Sélectionnez **Vérifier + créer**, puis sélectionnez **Créer** pour envoyer la demande de support.

### <a name="azure-quota-rest-api"></a>API REST de quota Azure

Vous pouvez utiliser l’API REST de quota Azure pour demander une augmentation de quota au niveau de l’abonnement ou du compte Batch.

Pour obtenir des détails et des exemples, consultez [Demander une augmentation de quota à l’aide de l’API REST du support Azure](/rest/api/support/quota-payload#azure-batch).

## <a name="related-quotas-for-vm-pools"></a>Quotas associés pour les pools de machines virtuelles

Les [pools Batch présents dans la configuration de machine virtuelle et déployés sur un réseau virtuel Azure](batch-virtual-network.md) allouent automatiquement des ressources Azure Networking supplémentaires. Ces ressources sont créées dans l’abonnement qui contient le réseau virtuel spécifié au moment de la création du pool Batch.

Les ressources suivantes sont créées pour chaque nœud de 100 pools dans un réseau virtuel :

- Un seul [groupe de sécurité réseau](../virtual-network/network-security-groups-overview.md#network-security-groups)
- Une seule [adresse IP publique](../virtual-network/public-ip-addresses.md)
- Un seul [équilibreur de charge](../load-balancer/load-balancer-overview.md)

Ces ressources sont limitées par les [quotas de ressources](../azure-resource-manager/management/azure-subscription-service-limits.md) de l’abonnement. Si vous planifiez des déploiements de grands pools dans un réseau virtuel, vous devrez peut-être demander une augmentation de quota pour une ou plusieurs de ces ressources.

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur le [flux de travail et les ressources principales du service Batch](batch-service-workflow-features.md), telles que les pools, les nœuds, les travaux et les tâches.
- En savoir plus sur [Abonnement Azure et limites, quotas et contraintes de service](../azure-resource-manager/management/azure-subscription-service-limits.md).