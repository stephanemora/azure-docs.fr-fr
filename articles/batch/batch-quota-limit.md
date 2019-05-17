---
title: Quotas et limites du service - Azure Batch | Microsoft Docs
description: En savoir plus sur les contraintes, les limites et les quotas par défaut d’Azure Batch, et comment demander une augmentation de quota
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/13/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 820eddff7da3bb52ca94ea0cb7e2361d89892a4a
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595309"
---
# <a name="batch-service-quotas-and-limits"></a>Quotas et limites du service Batch

Comme avec d’autres services Azure, il existe des limites concernant certaines ressources associées au service Batch. La plupart de ces limites représentent des quotas par défaut appliqués par Azure au niveau de l’abonnement ou du compte. Cet article décrit ces paramètres par défaut, et comment vous pouvez demander une augmentation de ces quotas.

Gardez ces quotas à l’esprit quand vous concevez et que vous augmentez vos charges de travail Batch. Par exemple, si votre pool n’atteint pas le nombre cible de nœuds de calcul que vous avez spécifié, vous avez peut-être atteint la limite du quota de cœurs de votre compte Batch.

Vous pouvez exécuter plusieurs charges de travail Batch dans un compte Batch ou répartir vos charges de travail entre plusieurs comptes Batch se trouvant dans le même abonnement mais dans différentes régions Azure.

Si vous envisagez d’exécuter des charges de travail de production dans Batch, vous devrez peut-être affecter à un ou plusieurs des quotas une valeur supérieure à la valeur par défaut. Si vous souhaitez augmenter un quota, vous pouvez ouvrir une [demande de service clientèle](#increase-a-quota) en ligne gratuitement.

> [!NOTE]
> Un quota est une limite de crédit, pas une garantie de capacité. Si vous avez des besoins de capacité à grande échelle, contactez le support Azure.

## <a name="resource-quotas"></a>Quotas de ressources
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]


### <a name="cores-quotas-in-user-subscription-mode"></a>Quotas de cœurs en mode Abonnement utilisateur

Si vous avez créé un compte Batch avec le mode d’allocation de pool défini sur **abonnement utilisateur**, les quotas sont appliqués de manière différente. Dans ce mode, les machines virtuelles Batch et les autres ressources sont créées directement dans votre abonnement quand un pool est créé. Les quotas de cœurs Azure Batch ne s’appliquent pas à un compte créé dans ce mode. Seuls s’appliquent les quotas de votre abonnement imposés aux cœurs de calcul régionaux et aux autres ressources. Pour en savoir plus sur ces quotas, consultez [Abonnement Azure et limites, quotas et contraintes de service](../azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Limites de taille de pool

| **Ressource** | **Limite maximale** |
| --- | --- |
| **Nœuds de calcul dans un [pool prenant en charge la communication entre nœuds](batch-mpi.md)**  ||
| Mode d’allocation de pool du service Batch | 100 |
| Mode d’allocation de pool de l'abonnement Batch | 80 |
| **Nœuds de calcul dédiés dans un [pool créé avec une image de machine virtuelle personnalisée](batch-custom-images.md)**<sup>1</sup> ||
| Nœuds dédiés | 2000 |
| Nœuds basse priorité | 1 000 |

<sup>1</sup> Pour les pools ne prenant pas en charge la communication entre nœuds.

## <a name="other-limits"></a>Autres limites

| **Ressource** | **Limite maximale** |
| --- | --- |
| [Tâches simultanées](batch-parallel-node-tasks.md) par nœud de calcul | 4 x nombre de cœurs de nœud |
| [Applications](batch-application-packages.md) par compte Batch | 20 |
| Packages d’applications par application | 40 |
| Packages d’applications par pool | 10 |
| Durée de vie maximale de la tâche | 180 jours<sup>1</sup> |

<sup>1</sup> La durée de vie maximale d’une tâche, entre le moment où elle est ajoutée au travail et la fin de son exécution, est de 180 jours. Tâches terminées sont conservées pendant sept jours ; les données de tâches non terminées pendant la durée de vie maximale ne sont pas accessibles.

## <a name="view-batch-quotas"></a>Afficher les quotas Batch

Affichez vos quotas de compte Batch dans le [portail Azure][portal].

1. Sélectionnez **Comptes Batch** dans le portail, puis sélectionnez le compte Batch qui vous intéresse.
1. Sélectionnez **Quotas** dans le menu du compte Batch.
1. Afficher les quotas actuellement appliqués au compte Batch

    ![Quotas de compte Batch][account_quotas]

## <a name="increase-a-quota"></a>Augmenter un quota

Suivez ces étapes pour demander une augmentation de quota pour votre compte Batch ou votre abonnement à l’aide du [portail Azure][portal]. Le type d’augmentation de quota varie selon le mode d’allocation de pool de votre compte Batch. Pour demander une augmentation du quota, vous devez inclure la série de machine virtuelle que vous souhaitez augmenter le quota pour. Lorsque l’augmentation du quota est appliquée, il est appliqué à toutes les séries de machines virtuelles.

### <a name="increase-cores-quota-in-batch"></a>Augmenter le quota de cœurs dans Batch 

1. Sélectionnez la mosaïque **Aide + Support** dans le tableau de bord du portail, ou le point d’interrogation (**?**) dans le coin supérieur droit du portail.
1. Sélectionnez **Nouvelle demande de support** > **De base**.
1. Dans **De base** :
   
    a. **Type de problème** > **limites de Service et d’abonnement (quotas)**
   
    b. Sélectionnez votre abonnement.
   
    c. **Type de quota** > **Batch**
      
    Sélectionnez **Suivant**.
    
1. Dans **Détails** :
      
    a. Dans **fournissent des détails**, spécifiez l’emplacement, le type de quota et compte Batch.
    
    ![Augmentation du quota par lots][quota_increase]

    Types de quota sont les suivantes :

    * **Par compte Batch**  
        Valeurs spécifiques à un seul lot de compte, y compris les cœurs dédiés et à faible priorité et le nombre des travaux et des pools.
        
    * **Par région**  
        Valeurs qui s’appliquent à tous les comptes Batch dans une région et incluent le nombre de comptes Batch par région et par abonnement.

    Quota de faible priorité est une valeur unique pour toutes les séries de machine virtuelle. Si vous avez besoin de références SKU de contrainte, vous devez sélectionner **cœurs à priorité basse** et incluent les familles de machine virtuelle à la demande.

    b. Sélectionnez un **niveau de gravité** en fonction de [l’impact sur votre activité][support_sev].

    Sélectionnez **Suivant**.

1. Dans **Informations de contact** :
   
    a. Sélectionnez une **méthode de contact préférée**.
   
    b. Vérifiez et entrez les informations de contact requises.
   
    Sélectionnez **créer** pour envoyer la demande de prise en charge.

Une fois que vous avez envoyé votre demande de support, le support Azure vous contactera. Les demandes de quota peuvent être effectuées dans quelques minutes ou jusqu'à deux jours ouvrables.

## <a name="related-quotas-for-vm-pools"></a>Quotas associés pour les pools de machines virtuelles

Les pools Batch dans la configuration de machine virtuelle déployés dans un réseau virtuel Azure allouent automatiquement des ressources de mise en réseau Azure supplémentaires. Les ressources suivantes sont nécessaires pour chacun des 50 nœuds de pools dans un réseau virtuel :

* Un [groupe de sécurité réseau](../virtual-network/security-overview.md#network-security-groups)
* Un seul [adresse IP publique](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* Un seul [équilibreur de charge](../load-balancer/load-balancer-overview.md)

Ces ressources sont allouées dans l’abonnement contenant le réseau virtuel fourni lors de la création du pool Batch. Ces ressources sont limitées par les [quotas de ressources](../azure-subscription-service-limits.md) de l’abonnement. Si vous planifiez des déploiements de pools de grande taille dans un réseau virtuel, vérifiez les quotas de l’abonnement pour ces ressources. Si nécessaire, demandez une augmentation sur le portail Azure en sélectionnant **Aide et support**.


## <a name="related-topics"></a>Rubriques connexes
* [Création et gestion d’un compte Azure Batch dans le portail Azure](batch-account-create-portal.md)
* [Vue d'ensemble des fonctionnalités d'Azure Batch](batch-api-basics.md)
* [Abonnement Azure et limites, quotas et contraintes de service](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: https://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png
