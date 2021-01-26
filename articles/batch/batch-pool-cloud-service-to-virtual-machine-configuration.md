---
title: Migrer la configuration des pools Batch de Services cloud vers Machines Virtuelles
description: Découvrez comment mettre à jour votre configuration de pools selon la configuration recommandée la plus récente.
ms.topic: how-to
ms.date: 1/6/2021
ms.openlocfilehash: d987a185efb6593fd541dd14fa74b6c4d3ca41be
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234305"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machines"></a>Migrer la configuration des pools Batch de Services cloud vers Machines Virtuelles

Vous pouvez créer des pools Batch à l’aide de [cloudServiceConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) ou [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration). « virtualMachineConfiguration » est la configuration recommandée, car elle prend en charge toutes les fonctionnalités Batch. Les pools « cloudServiceConfiguration » ne prennent pas en charge toutes les fonctionnalités, et aucune nouvelle fonctionnalité n’est prévue.

Si vous utilisez des pools « cloudServiceConfiguration », il est fortement recommandé de passer à l’utilisation des pools « virtualMachineConfiguration ». Cela vous permet de bénéficier de toutes les fonctionnalités Batch, telles qu’une [sélection élargie de séries de machines virtuelles](batch-pool-vm-sizes.md), de machines virtuelles Linux, de [conteneurs](batch-docker-container-workloads.md), de [réseaux virtuels Azure Resource Manager](batch-virtual-network.md) et du [chiffrement des disques de nœuds](disk-encryption.md).

Cet article explique comment migrer vers « virtualMachineConfiguration ».

## <a name="new-pools-are-required"></a>De nouveaux pools sont requis

Les pools actifs existants ne peuvent pas être mis à jour de « cloudServiceConfiguration » à « virtualMachineConfiguration », de nouveaux pools doivent être créés. La création de pools à l’aide de « virtualMachineConfiguration » est prise en charge par toutes les API Batch, les outils en ligne de commande, le portail Azure et l’interface utilisateur Batch Explorer.

**Les tutoriels [.NET](tutorial-parallel-dotnet.md) et [Python](tutorial-parallel-python.md) fournissent des exemples de création de pool à l’aide de « virtualMachineConfiguration ».**

## <a name="pool-configuration-differences"></a>Différences de configuration des pools

Les éléments suivants doivent être pris en compte lors de la mise à jour de la configuration des pools :

- Les nœuds de pool « cloudServiceConfiguration » sont toujours des systèmes d’exploitation Windows, tandis que les pools « virtualMachineConfiguration » peuvent être des systèmes d’exploitation Linux ou Windows.
- Par rapport aux pools « cloudServiceConfiguration », les pools « virtualMachineConfiguration » offrent un ensemble plus riche de capacités, telles que la prise en charge des conteneurs, les disques de données et le chiffrement de disque.
- Les nœuds de pool « virtualMachineConfiguration » utilisent des disques de système d’exploitation managés. Le [type de disque managé](../virtual-machines/disks-types.md) utilisé pour chaque nœud dépend de la taille de machine virtuelle choisie pour le pool. Si la taille de machine virtuelle « s » est spécifiée pour le pool, par exemple « Standard_D2s_v3 », un SSD Premium est utilisé. Si une taille de machine virtuelle « non s » est spécifiée, par exemple « Standard_D2_v3 », un HDD Standard est utilisé.

   > [!IMPORTANT]
   > Comme pour les machines virtuelles et les groupe de machines virtuelles identiques, le disque managé du système d’exploitation utilisé pour chaque nœud entraîne un coût, qui s’ajoute à celui des machines virtuelles. Le disque du système d’exploitation n’engendre aucun coût pour les nœuds « cloudServiceConfiguration », car le disque du système d’exploitation est créé sur le SSD local des nœuds.

- Les heures de démarrage et de suppression des pools et des nœuds peuvent différer légèrement entre les pools « cloudServiceConfiguration » et « virtualMachineConfiguration ».

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [configurations de pools](nodes-and-pools.md#configurations).
- En savoir plus sur les [meilleures pratiques en matière de pool](best-practices.md#pools).
- Informations de référence sur l’API REST pour l’[ajout de pools](https://docs.microsoft.com/rest/api/batchservice/pool/add) et [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration).
