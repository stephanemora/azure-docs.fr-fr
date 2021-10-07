---
title: Utiliser des nœuds de disque de système d’exploitation éphémère pour les pools Azure Batch
description: Découvrez comment et pourquoi créer un pool batch qui utilise des nœuds de disque de système d’exploitation éphémères.
ms.topic: how-to
ms.date: 09/03/2021
ms.openlocfilehash: 760e2848917c6b1c502ac6ba9aae00d5ddd89c8c
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544433"
---
# <a name="use-ephemeral-os-disk-nodes-for-azure-batch-pools"></a>Utiliser des nœuds de disque de système d’exploitation éphémère pour les pools Azure Batch

Certaines séries de machines virtuelles Azure prennent en charge l’utilisation de [disques de système d’exploitation éphémères](../virtual-machines/ephemeral-os-disks.md), qui créent le disque du système d’exploitation sur le stockage local de l’ordinateur virtuel du noeud. La configuration par défaut du pool Batch utilise des [disques managés Azure](../virtual-machines/managed-disks-overview.md) pour le disque du système d’exploitation du nœud, où le disque managé est comme un disque physique, mais virtualisé et conservé dans le Stockage Azure distant.

Concernant les charges de travail Batch, les principaux avantages de l’utilisation éphémère des disques du système d’exploitation sont des frais réduits associés aux pools, un temps de démarrage de nœud potentiellement plus rapide et une performance d’application améliorée en raison d’une meilleure performance du disque du système d’exploitation. Lorsque vous choisissez si les disques du système d’exploitation éphémères doivent être utilisés pour votre charge de travail, prenez en compte les éléments suivants :

- Il y a une latence de lecture/écriture inférieure sur les disques de système d’exploitation éphémères, ce qui peut entraîner une amélioration des performances de l’application.
- Il n’y a aucun coût de stockage pour les disques de système d’exploitation éphémères, alors qu’il y a un coût pour chaque disque de système d’exploitation managé.
- La réinitialisation du nœud, quand il est pris en charge par Batch, est plus rapide pour les disques éphémères que pour les disques managés.
- Le temps de démarrage du nœud peut être légèrement plus rapide lorsque des disques de système d’exploitation éphémères sont utilisés.
- Les disques de système d’exploitation éphémères ne sont pas hautement durables et disponibles. Quand une machine virtuelle est supprimée pour une raison quelconque, le disque du système d’exploitation est perdu. Étant donné que les charges de travail par lots sont fondamentalement sans état et ne s’appuient pas sur des modifications apportées au disque du système d’exploitation, les disques de système d’exploitation éphémères conviennent à l’utilisation pour la plupart des charges de travail par lots.
- L’utilisation d’un disque de système d’exploitation éphémère n’est actuellement pas prise en charge par toutes les séries de machines virtuelles Azure. Si une taille de machine virtuelle ne prend pas en charge un disque de système d’exploitation éphémère, un disque de système d’exploitation managé doit être utilisé.

> [!NOTE]
> La configuration du disque de système d’exploitation éphémère s’applique uniquement aux pools « virtualMachineConfiguration » et n’est pas prise en charge par les pools « cloudServiceConfiguration ». Nous vous recommandons d’utiliser « virtualMachineConfiguration » pour vos pools batch, car les pools « cloudServiceConfiguration » ne prennent pas en charge toutes les fonctionnalités et aucune nouvelle fonctionnalité n’est planifiée. [Après le 29 février 2024](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/), vous ne pourrez plus créer de pools « cloudServiceConfiguration » ou ajouter de nouveaux nœuds à des pools existants. Pour plus d’informations, consultez [Migrer la configuration des pools Batch des Services cloud vers une machine virtuelle](batch-pool-cloud-service-to-virtual-machine-configuration.md).

## <a name="vm-series-support"></a>Prise en charge des séries de machines virtuelles

Pour déterminer si une série de machines virtuelles prend en charge les disques de système d’exploitation éphémères, consultez la documentation de chaque instance de machine virtuelle. Par exemple, les [séries Ddv4 et Ddsv4](../virtual-machines/ddv4-ddsv4-series.md) prennent en charge les disques de système d’exploitation éphémères.

Vous pouvez également effectuer une requête par programme pour vérifier la fonctionnalité « EphemeralOSDiskSupported ». Vous trouverez un exemple d’applet de commande PowerShell pour interroger cette fonctionnalité dans le [Forum aux questions sur le disque de système d’exploitation éphémère](../virtual-machines/ephemeral-os-disks.md#frequently-asked-questions).

## <a name="create-a-pool-that-uses-ephemeral-os-disks"></a>Créer un pool qui utilise des disques de système d’exploitation éphémères

La propriété `EphemeralOSDiskSettings` n’est pas définie par défaut. Vous devez définir cette propriété afin de configurer l’utilisation du disque de système d’exploitation éphémère sur les nœuds du pool.

L’exemple suivant montre comment créer un pool batch dans lequel les nœuds utilisent des disques de système d’exploitation éphémères et non des disques managés.

### <a name="batch-net-api"></a>API .NET Batch

```csharp
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: nodeAgentSku
        );
virtualMachineConfiguration.OSDisk = new OSDisk();
virtualMachineConfiguration.OSDisk.EphemeralOSDiskSettings = new DiffDiskSettings();
virtualMachineConfiguration.OSDisk.EphemeralOSDiskSettings.Placement = DiffDiskPlacement.CacheDisk;
```

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur le [workflow et les ressources principales du service Batch](batch-service-workflow-features.md), telles que les pools, les nœuds, les travaux et les tâches.
- En savoir plus sur [les coûts qui peuvent être associés aux charges de travail Azure Batch](budget.md).
