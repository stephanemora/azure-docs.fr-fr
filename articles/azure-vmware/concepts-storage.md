---
title: Concepts – Stockage
description: En savoir plus sur la capacité de stockage, les stratégies de stockage, la tolérance de panne et l’intégration du stockage dans les clouds privés Azure VMware Solution.
ms.topic: conceptual
ms.custom: contperf-fy21q4
ms.date: 07/28/2021
ms.openlocfilehash: ae37e0147ea03f91c2af68b8733a1702a02f81f3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524305"
---
# <a name="azure-vmware-solution-storage-concepts"></a>Concepts de stockage pour Azure VMware Solution

Les clouds privés Azure VMware Solution fournissent un stockage natif à l’échelle du cluster avec VMware vSAN. Le stockage local de chaque hôte d’un cluster est utilisé dans un magasin de données vSAN, et un chiffrement des données au repos est disponible et activé par défaut. Vous pouvez utiliser les ressources de Stockage Azure pour étendre les capacités de stockage de vos clouds privés.

## <a name="vsan-clusters"></a>Clusters vSAN

Un stockage local dans chaque hôte de cluster est utilisé en lien avec un magasin de données vSAN. Tous les groupes de disques utilisent une couche cache NVMe de 1,6 To avec la capacité brute sur SDD par ordinateur hôte de 15,4 To. La taille de la couche de capacité brute d’un cluster est égale à la capacité par hôte multipliée par le nombre d’hôtes. Par exemple, un cluster de quatre hôtes offre une capacité brute de 61,6 To dans la couche de capacité vSAN.

Le stockage local dans les hôtes du cluster est utilisé dans un magasin de stockage vSAN à l’échelle du cluster. Tous les magasins de données sont créés dans le cadre d’un déploiement de cloud privé et immédiatement utilisables. L’utilisateur **cloudadmin** et tous les utilisateurs affectés au rôle CloudAdmin peuvent gérer les magasins de données avec les privilèges vSAN suivants :

- Datastore.AllocateSpace
- Datastore.Browse
- Datastore.Config
- Datastore.DeleteFile
- Datastore.FileManagement
- Datastore.UpdateVirtualMachineMetadata

>[!IMPORTANT]
>Vous ne pouvez pas modifier le nom des magasins de données ou des clusters. Vous pouvez sélectionner un nom de cluster autre que « Cluster-n » où n > 1 lors de l’approvisionnement depuis un autre endroit que le portail (Azure CLI ou PowerShell).

## <a name="storage-policies-and-fault-tolerance"></a>Stratégies de stockage et tolérance de panne

Cette stratégie de stockage par défaut est définie sur RAID-1 (Mise en miroir), FTT-1 et l’approvisionnement statique.  À moins que vous n’ajustiez la stratégie de stockage ou que vous appliquiez une nouvelle stratégie, le cluster continue de croître avec cette configuration. Dans un cluster à trois hôtes, FTT-1 prend en charge la défaillance d’un seul hôte. Microsoft régit régulièrement les défaillances et remplace le matériel lorsque des événements sont détectés du point de vue de l’architecture.

:::image type="content" source="media/concepts/vsphere-vm-storage-policies.png" alt-text="Capture d’écran montrant les stratégies de stockage de machines virtuelles du client vSphere.":::


|Type d’approvisionnement  |Description  |
|---------|---------|
|**Statique**      | Espace de stockage réservé ou pré-alloué. Il protège les systèmes en leur permettant de fonctionner même si le magasin de données vSAN est plein, car l’espace est déjà réservé. Par exemple, si vous créez un disque virtuel de 10 Go avec l’approvisionnement statique, la capacité totale de stockage du disque virtuel est pré-allouée sur la mémoire physique du disque virtuel et utilise tout l’espace qui lui est alloué dans le magasin de données. Il ne permettra pas à d’autres machines virtuelles de partager l’espace du magasin de données.         |
|**Dynamique**      | Consomme l’espace dont il a besoin initialement et croît jusqu’à la demande d’espace de données utilisé dans le magasin de données. En dehors de la configuration par défaut (approvisionnement statique), vous pouvez créer des machines virtuelles avec l’allocation dynamique FTT-1. Pour la configuration de la déduplication, utilisez l’approvisionnement dynamique pour votre modèle de machine virtuelle.         |

>[!TIP]
>Si vous n’êtes pas certain que le cluster atteindra quatre hôtes ou plus, effectuez le déploiement en utilisant la stratégie par défaut.  Si vous êtes sûr que votre cluster va croître, au lieu d’étendre le cluster après votre déploiement initial, nous vous recommandons de déployer les hôtes supplémentaires au cours du déploiement. Lorsque les machines virtuelles sont déployées sur le cluster, modifiez la stratégie de stockage du disque dans les paramètres de la machine virtuelle en choisissant RAID-5 FTT-1 ou RAID-6 FTT-2. 
>
>:::image type="content" source="media/concepts/vsphere-vm-storage-policies-2.png" alt-text="Capture d’écran avec les options RAID-5 FTT-1 et RAID-6 FTT-2 mises en évidence.":::


## <a name="data-at-rest-encryption"></a>Chiffrement des données au repos

Par défaut, les magasins de données vSAN utilisent le chiffrement des données au repos à l’aide de clés stockées dans Azure Key Vault. La solution de chiffrement est basée sur un service de gestion de clés et prend en charge les opérations de vCenter pour la gestion des clés.  Lorsqu’un hôte est supprimé d’un cluster, les données sur disques SSD sont immédiatement invalidées.

## <a name="azure-storage-integration"></a>Intégration du Stockage Azure

Vous pouvez utiliser les services de stockage Azure dans des charges de travail qui s’exécutent dans votre cloud privé. Les services de Stockage Azure incluent les Comptes de stockage, le Stockage Table et le Stockage Blob. La connexion des charges de travail aux services de Stockage Azure ne traverse pas Internet. Cette connectivité offre une sécurité supplémentaire et vous permet d’utiliser les services Stockage Azure basés sur un Contrat de niveau de service (SLA) dans vos charges de travail de cloud privé.

## <a name="alerts-and-monitoring"></a>Alertes et supervision

Microsoft déclenche des alertes lorsque la consommation de capacité dépasse 75 %.  Vous pouvez surveiller les métriques de consommation de capacité qui sont intégrées à Azure Monitor. Pour plus d’informations, consultez [Configurer des alertes Azure dans Azure VMware Solution](configure-alerts-for-azure-vmware-solution.md).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez abordé les concepts de stockage d’Azure VMware Solution, vous pouvez en apprendre davantage sur les sujets suivants :

- [Attacher des pools de disques à des hôtes Azure VMware Solution (préversion)](attach-disk-pools-to-azure-vmware-solution-hosts.md) : vous pouvez utiliser des disques comme stockage persistant pour Azure VMware Solution afin d’optimiser le coût et les performances.
- [Mettre à l’échelle des clusters dans le cloud privé][tutorial-scale-private-cloud] : vous pouvez mettre à l’échelle les clusters et les hôtes d’un cloud privé en fonction des besoins de la charge de travail de vos applications. Les limitations de performances et de disponibilité pour des services spécifiques doivent être traitées au cas par cas.
- [Azure NetApp Files avec Azure VMware Solution](netapp-files-with-azure-vmware-solution.md) : vous pouvez utiliser Azure NetApp pour migrer et exécuter les charges de travail de fichiers d’entreprise les plus exigeantes dans le cloud (bases de données, SAP et applications informatiques hautes performances) sans aucune modification du code. 
- [Contrôle d’accès en fonction du rôle vSphere pour Azure VMware Solution](concepts-identity.md) : vous utilisez vCenter pour gérer les charges de travail de machine virtuelle et NSX-T Manager pour gérer et étendre le cloud privé. La gestion des accès et des identités utilise le rôle CloudAdmin pour vCenter et des droits d’administrateur restreints pour NSX-T Manager.


<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorial-scale-private-cloud]: ./tutorial-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md
