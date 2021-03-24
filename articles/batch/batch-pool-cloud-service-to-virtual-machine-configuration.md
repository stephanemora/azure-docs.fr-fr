---
title: Migrer la configuration des pools Batch de Services cloud vers Machines Virtuelles
description: Découvrez comment mettre à jour votre configuration de pools selon la configuration recommandée la plus récente.
ms.topic: how-to
ms.date: 03/11/2021
ms.openlocfilehash: a176c4df1737a340a546b4ab7926447cd821350d
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200555"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machine"></a>Migrer la configuration des pools Batch de Services cloud vers une machine virtuelle

Vous pouvez actuellement créer des pools Batch à l'aide de [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) ou de [cloudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration). Nous vous recommandons d'utiliser « virtualMachineConfiguration » car cette configuration prend en charge l'intégralité des fonctionnalités de Batch.

Les pools « cloudServiceConfiguration » ne prennent pas en charge certaines des fonctionnalités actuelles de Batch, et ne prendront en charge aucune des futures fonctionnalités. [Après le 29 février 2024](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/), vous ne pourrez plus créer de pools « CloudServiceConfiguration » ni ajouter de nouveaux nœuds aux pools existants.

Si vos solutions Batch utilisent actuellement des pools « cloudServiceConfiguration », nous vous recommandons de passer au plus vite à « virtualMachineConfiguration ». Cela vous permet de bénéficier de toutes les fonctionnalités Batch, telles qu’une [sélection élargie de séries de machines virtuelles](batch-pool-vm-sizes.md), de machines virtuelles Linux, de [conteneurs](batch-docker-container-workloads.md), de [réseaux virtuels Azure Resource Manager](batch-virtual-network.md) et du [chiffrement des disques de nœuds](disk-encryption.md).

## <a name="create-a-pool-using-virtual-machine-configuration"></a>Créer un pool à l'aide de « virtualMachineConfiguration »

Vous ne pouvez pas basculer un pool actif existant qui utilise « cloudServiceConfiguration » vers « virtualMachineConfiguration ». Pour cela, vous devez créer de nouveaux pools. Une fois que vous avez créé vos nouveaux pools « virtualMachineConfiguration » et répliqué tous vos travaux et tâches, vous pouvez supprimer les anciens pools « cloudServiceConfiguration » que vous n'utilisez plus.

Toutes les API Batch ainsi que les outils de ligne de commande, le portail Azure et l'interface utilisateur de Batch Explorer vous permettent de créer des pools à l'aide de « virtualMachineConfiguration ».

Pour savoir comment créer des pools qui utilisent « virtualMachineConfiguration », consultez le [Tutoriel .NET](tutorial-parallel-dotnet.md) ou le [Tutoriel Python](tutorial-parallel-python.md).

## <a name="pool-configuration-differences"></a>Différences de configuration des pools

Voici quelques-unes des principales différences entre les deux configurations :

- Les nœuds du pool « cloudServiceConfiguration » utilisent uniquement le système d'exploitation Windows. Les pools « virtualMachineConfiguration » peuvent utiliser le système d'exploitation Linux ou Windows.
- Par rapport aux pools « cloudServiceConfiguration », les pools « virtualMachineConfiguration » offrent un ensemble plus riche de capacités, telles que la prise en charge des conteneurs, les disques de données et le chiffrement de disque.
- Les heures de démarrage et de suppression des pools et des nœuds peuvent différer légèrement entre les pools « cloudServiceConfiguration » et « virtualMachineConfiguration ».
- Les nœuds de pool « virtualMachineConfiguration » utilisent des disques de système d’exploitation managés. Le [type de disque managé](../virtual-machines/disks-types.md) utilisé pour chaque nœud dépend de la taille de machine virtuelle choisie pour le pool. Si la taille de machine virtuelle « s » est spécifiée pour le pool, par exemple « Standard_D2s_v3 », un SSD Premium est utilisé. Si une taille de machine virtuelle « non s » est spécifiée, par exemple « Standard_D2_v3 », un HDD Standard est utilisé.

   > [!IMPORTANT]
   > Comme pour les machines virtuelles et les groupe de machines virtuelles identiques, le disque managé du système d’exploitation utilisé pour chaque nœud entraîne un coût, qui s’ajoute à celui des machines virtuelles. Le disque du système d'exploitation n'engendre aucun coût pour les nœuds « cloudServiceConfiguration » car il est créé sur le disque SSD local des nœuds.

## <a name="azure-data-factory-custom-activity-pools"></a>Pools d’activités personnalisées Azure Data Factory

Vous pouvez utiliser des pools Azure Batch pour exécuter des activités personnalisées Data Factory. Tout pool « cloudServiceConfiguration » utilisé pour exécuter des activités personnalisées doit être supprimé et des pools « virtualMachineConfiguration » sont créés.

Lorsque vous créez vos pools pour exécuter des activités personnalisées Data Factory, procédez comme suit :

- Suspendez tous les pipelines avant de créer les nouveaux pools et de supprimer les anciens afin de veiller à ce qu'aucune exécution ne soit interrompue.
- Le même ID de pool peut être utilisé pour éviter les modifications de configuration de service lié.
- Reprenez les pipelines après la création des pools.

Pour plus d'informations sur l'utilisation d'Azure Batch pour exécuter des activités personnalisées Data Factory, consultez [Service lié Azure Batch](../data-factory/compute-linked-services.md#azure-batch-linked-service) et [Activités personnalisées dans un pipeline Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [configurations de pools](nodes-and-pools.md#configurations).
- En savoir plus sur les [meilleures pratiques en matière de pool](best-practices.md#pools).
- Consultez les informations de référence sur l'API REST pour l'[ajout de pools](/rest/api/batchservice/pool/add) et [virtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration).