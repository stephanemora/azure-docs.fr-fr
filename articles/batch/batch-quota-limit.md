---
title: Quotas et limites du service
description: En savoir plus sur les contraintes, les limites et les quotas par défaut d’Azure Batch, et comment demander une augmentation de quota
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: 4c13df8b537d701400a22cd2871e7f8362f02455
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84417274"
---
# <a name="batch-service-quotas-and-limits"></a>Quotas et limites du service Batch

Comme avec d’autres services Azure, il existe des limites concernant certaines ressources associées au service Batch. La plupart de ces limites représentent des quotas par défaut appliqués par Azure au niveau de l’abonnement ou du compte.

Gardez ces quotas à l’esprit quand vous concevez et que vous augmentez vos charges de travail Batch. Par exemple, si votre pool n’atteint pas le nombre cible de nœuds de calcul que vous avez spécifié, vous avez peut-être atteint la limite du quota de cœurs de votre compte Batch.

Vous pouvez exécuter plusieurs charges de travail Batch dans un compte Batch ou répartir vos charges de travail entre plusieurs comptes Batch se trouvant dans le même abonnement mais dans différentes régions Azure.

Si vous envisagez d’exécuter des charges de travail de production dans Batch, vous devrez peut-être affecter à un ou plusieurs des quotas une valeur supérieure à la valeur par défaut. Si vous souhaitez augmenter un quota, vous pouvez ouvrir une [demande de service clientèle](#increase-a-quota) en ligne gratuitement.

## <a name="resource-quotas"></a>Quotas de ressources

Un quota est une limite de crédit, pas une garantie de capacité. Si vous avez des besoins de capacité à grande échelle, contactez le support Azure.

Notez également que les quotas ne sont pas des valeurs garanties. Les quotas peuvent varier en fonction des modifications du service Batch ou d’une demande d’utilisateur visant à modifier une valeur de quota.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="cores-quotas-in-user-subscription-mode"></a>Quotas de cœurs en mode Abonnement utilisateur

Si vous avez créé un [compte Batch](accounts.md) avec le mode d’allocation de pool défini sur **abonnement utilisateur**, les quotas sont appliqués de manière différente. Dans ce mode, les machines virtuelles Batch et les autres ressources sont créées directement dans votre abonnement quand un pool est créé. Les quotas de cœurs Azure Batch ne s’appliquent pas à un compte créé dans ce mode. Seuls s’appliquent les quotas de votre abonnement imposés aux cœurs de calcul régionaux et aux autres ressources.

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

Limites supplémentaires définies par le service Batch. Contrairement aux [quotas des ressources](#resource-quotas), ces valeurs ne peuvent pas être modifiées.

| **Ressource** | **Limite maximale** |
| --- | --- |
| [Tâches simultanées](batch-parallel-node-tasks.md) par nœud de calcul | 4 x nombre de cœurs de nœud |
| [Applications](batch-application-packages.md) par compte Batch | 20 |
| Packages d’applications par application | 40 |
| Packages d’applications par pool | 10 |
| Durée de vie maximale de la tâche | 180 jours<sup>1</sup> |
| [Montages](virtual-file-mount.md) par nœud de calcul | 10 |

<sup>1</sup> La durée de vie maximale d’une tâche, entre le moment où elle est ajoutée au travail et la fin de son exécution, est de 180 jours. Les tâches terminées sont conservées pendant sept jours. Les données de tâches non terminées pendant la durée de vie maximale ne sont pas accessibles.

## <a name="view-batch-quotas"></a>Afficher les quotas Batch

Pour voir vos quotas de compte Batch dans le [portail Azure](https://portal.azure.com) :

1. Sélectionnez **Comptes Batch**, puis le compte Batch qui vous intéresse.
1. Sélectionnez **Quotas** dans le menu du compte Batch.
1. Affichez les quotas actuellement appliqués au compte Batch.

    ![Quotas de compte Batch][account_quotas]

## <a name="increase-a-quota"></a>Augmenter un quota

Vous pouvez demander une augmentation de quota pour votre compte Batch ou votre abonnement à l’aide du [portail Azure](https://portal.azure.com). Le type d’augmentation de quota varie selon le mode d’allocation de pool de votre compte Batch. Pour demander une augmentation de quota, vous devez inclure la série de machines virtuelles dont vous souhaitez augmenter le quota. Lorsque l’augmentation de quota est appliquée, elle l’est pour toutes les séries de machines virtuelles.

1. Sélectionnez la mosaïque **Aide + Support** dans le tableau de bord du portail, ou le point d’interrogation ( **?** ) dans le coin supérieur droit du portail.
1. Sélectionnez **Nouvelle demande de support** > **De base**.
1. Dans **De base** :
   
    1. **Type de problème** > **Limites du service et des abonnements (quotas)**
   
    1. Sélectionnez votre abonnement.
   
    1. **Type de quota** > **Batch**
      
       Sélectionnez **Suivant**.
    
1. Dans **Détails** :
      
    1. Dans **Fournir des détails**, spécifiez l’emplacement, le type de quota et le compte Batch.
    
       ![Augmentation de quota Batch][quota_increase]

       Types de quotas :

       * **Par compte Batch**  
         Valeurs propres à un seul et même compte Batch, incluant les cœurs dédiés et basse priorité ainsi que le nombre de travaux et de pools.
        
       * **Par région**  
         Valeurs qui s’appliquent à tous les comptes Batch d’une région et qui incluent le nombre de comptes Batch par région et par abonnement.

       Un quota basse priorité est une valeur unique dans toutes les séries de machines virtuelles. Si vous avez besoin de références SKU avec contraintes, vous devez sélectionner **Cœurs basse priorité** et inclure les familles de machines virtuelles à la demande.

    1. Sélectionnez un **niveau de gravité** en fonction de [l’impact sur votre activité](https://aka.ms/supportseverity).

       Sélectionnez **Suivant**.

1. Dans **Informations de contact** :
   
    1. Sélectionnez une **méthode de contact préférée**.
   
    1. Vérifiez et entrez les informations de contact requises.
   
       Sélectionnez **Créer** pour envoyer la demande de support.

Une fois que vous avez envoyé votre demande de support, le support Azure vous contactera. Les demandes de quota peuvent être effectuées en quelques minutes ou jusqu’à deux jours ouvrables.

## <a name="related-quotas-for-vm-pools"></a>Quotas associés pour les pools de machines virtuelles

Les pools Batch dans la configuration de machine virtuelle déployés dans un réseau virtuel Azure allouent automatiquement des ressources de mise en réseau Azure supplémentaires. Les ressources suivantes sont nécessaires pour chacun des 50 nœuds de pools dans un réseau virtuel :

- Un seul [groupe de sécurité réseau](../virtual-network/security-overview.md#network-security-groups)
- Une seule [adresse IP publique](../virtual-network/public-ip-addresses.md)
- Un seul [équilibreur de charge](../load-balancer/load-balancer-overview.md)

Ces ressources sont allouées dans l’abonnement contenant le réseau virtuel fourni lors de la création du pool Batch. Ces ressources sont limitées par les [quotas de ressources](../azure-resource-manager/management/azure-subscription-service-limits.md) de l’abonnement. Si vous planifiez des déploiements de pools de grande taille dans un réseau virtuel, vérifiez les quotas de l’abonnement pour ces ressources. Si nécessaire, demandez une augmentation sur le portail Azure en sélectionnant **Aide et support**.

## <a name="next-steps"></a>Étapes suivantes

* [Créer un compte Azure Batch à l’aide du portail Azure](batch-account-create-portal.md).
* Apprenez-en davantage sur le [flux de travail et les ressources principales du service Batch](batch-service-workflow-features.md), telles que les pools, les nœuds, les travaux et les tâches.
* En savoir plus sur [Abonnement Azure et limites, quotas et contraintes de service](../azure-resource-manager/management/azure-subscription-service-limits.md).

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png
