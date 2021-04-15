---
title: Configurer le stockage pour les machines virtuelles SQL Server | Microsoft Docs
description: Cette rubrique décrit comment Azure configure le stockage pour les machines virtuelles SQL Server lors du provisionnement (modèle de déploiement Azure Resource Manager). Elle explique également comment vous pouvez configurer le stockage pour vos machines virtuelles SQL Server existantes.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 982bd9239c5e95c9b7af09b5f54c5a09067ca7c6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565414"
---
# <a name="configure-storage-for-sql-server-vms"></a>Configurer le stockage pour les machines virtuelles SQL Server
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Cet article vous explique comment configurer votre stockage pour votre instance de SQL Server sur les machines virtuelles Azure.

Les machines virtuelles SQL Server déployées à l’aide d’images du marketplace respectent automatiquement les [meilleures pratiques de stockage](performance-guidelines-best-practices-storage.md) par défaut, qui peuvent être modifiées pendant le déploiement. Certains de ces paramètres de configuration peuvent être modifiés après le déploiement. 


## <a name="prerequisites"></a>Prérequis

Pour utiliser les paramètres de configuration automatisée du stockage, votre machine virtuelle doit répondre aux conditions suivantes :

* Est approvisionnée avec une [image de la galerie SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) ou est inscrite avec l’[extension SQL IaaS]() ;
* Utilise le [modèle de déploiement Resource Manager](../../../azure-resource-manager/management/deployment-models.md);
* Utilise des [SSD premium](../../../virtual-machines/disks-types.md).

## <a name="new-vms"></a>Nouvelles machines virtuelles

Les sections suivantes décrivent comment configurer le stockage pour les nouvelles machines virtuelles SQL Server.

### <a name="azure-portal"></a>Portail Azure

Lors de l’approvisionnement d’une machine virtuelle Azure à l’aide d’une image de galerie SQL Server, sélectionnez **Modifier la configuration** dans l'onglet **Paramètres de SQL Server** pour ouvrir la page Configuration du stockage à performances optimisées. Vous pouvez conserver les valeurs par défaut ou modifier le type de configuration de disque selon vos besoins en matière de charge de travail. 

![Capture d’écran mettant en surbrillance l’onglet Paramètres SQL Server et l’option Modification de la configuration.](./media/storage-configuration/sql-vm-storage-configuration-provisioning.png)

Sélectionnez le type de charge de travail pour lequel vous déployez SQL Server sous **Optimisation du stockage**. Avec l’option d’optimisation **Général**, par défaut, vous disposez d’un disque de données avec 5000 E/S par seconde max, et utilisez ce même disque pour vos données, journal des transactions et stockage TempDB. 

Si vous sélectionnez **Traitement transactionnel** (OLTP) or **Entreposage de données**, vous créez un disque distinct pour les données, le journal des transactions et utilisez le disque SSD local pour TempDB. En matière de stockage, il n’existe aucune différence entre **Traitement transactionnel** et **Entreposage de données**, mais cela modifie votre [configuration de bandes et les indicateurs de trace](#workload-optimization-settings). Opter pour le stockage Premium définit la mise en cache sur *Readonly* pour le lecteur de données, et *Aucune* pour le lecteur de journal conformément aux [meilleures pratiques en matière de performances de machine virtuelle SQL Server](performance-guidelines-best-practices.md). 

![Configuration du stockage des machines virtuelles SQL Server lors de la configuration](./media/storage-configuration/sql-vm-storage-configuration.png)

La configuration du disque est entièrement personnalisable pour vous permettre de configurer la topologie de stockage, le type de disque et les E/S par seconde dont vous avez besoin pour votre charge de travail de machine virtuelle SQL Server. Vous avez également la possibilité d’utiliser UltraSSD (préversion) comme option pour le **type de disque** si votre machine virtuelle SQL Server se trouve dans l’une des régions prises en charge (USA Est 2, Asie Sud-Est et Europe Nord) et si vous avez activé les [disques Ultra pour votre abonnement](../../../virtual-machines/disks-enable-ultra-ssd.md).  

En outre, il vous est possible de définir la mise en cache pour les disques. Les machines virtuelles Azure disposent d’une technologie de mise en cache à plusieurs niveaux appelée [Cache Blob](../../../virtual-machines/premium-storage-performance.md#disk-caching) lorsqu'elle est utilisée avec des [disques Premium](../../../virtual-machines/disks-types.md#premium-ssd). Cache Blob combine la RAM de la machine virtuelle et le SSD local pour la mise en cache. 

La mise en cache du disque pour SSD Premium peut être *ReadOnly*, *ReadWrite* ou *Aucune*. 

- La mise en cache *ReadOnly* s'avère particulièrement intéressante pour les fichiers de données SQL Server stockés sur le stockage Premium. La mise en cache *ReadOnly* offre une faible latence de lecture, de très hautes performances d'E/S et de débit en cas de lectures à partir du cache, dans la mémoire de la machine virtuelle ou du SSD local. Ces lectures sont nettement plus rapides que les lectures à partir du disque de données, provenant du stockage Blob Azure. Le stockage Premium ne tient pas compte des lectures traitées à partir du cache pour le calcul du nombre d’E/S par seconde et du débit du disque. Dès lors, votre application peut offrir de meilleures performances totales en termes d’E/S par seconde et de débit. 
- La configuration de cache *Aucune* doit être utilisée pour les disques hébergeant le fichier journal SQL Server, car ce fichier journal est écrit de manière séquentielle et ne tire pas parti de la mise en cache *ReadOnly*. 
- La mise en cache *ReadWrite* ne doit pas être utilisée pour héberger des fichiers SQL Server, car SQL Server ne prend pas en charge la cohérence des données avec le cache *ReadWrite*. Les écritures gaspillent la capacité du cache blob *ReadOnly* et les latences augmentent légèrement si les écritures interviennent dans les couches du cache blob *ReadOnly*. 


   > [!TIP]
   > Vérifiez que votre configuration de stockage correspond aux limitations imposées par la taille de machine virtuelle sélectionnée. Opter pour des paramètres de stockage dépassant le seuil des performances de taille de la machine virtuelle génère un avertissement : `The desired performance might not be reached due to the maximum virtual machine disk performance cap`. Diminuez le nombre d’E/S par seconde en modifiant le type de disque ou augmentez le seuil de performances en augmentant la taille de la machine virtuelle. Cela n’arrête pas le provisionnement. 


En fonction de votre choix, Azure exécute les tâches de configuration du stockage suivantes après avoir créé la machine virtuelle :

* Crée et attache des disques SSD Premium à la machine virtuelle.
* Configure les disques de données pour que SQL Server puisse y accéder.
* Configure les disques de données dans un pool de stockage basé sur les critères indiqués pour la taille et les performances (E/S par seconde et débit).
* Associe le pool de stockage à un nouveau lecteur sur la machine virtuelle.
* Optimise ce nouveau lecteur en fonction du type de charge de travail que vous avez spécifié (entrepôt de données, traitement transactionnel ou général).

Pour une procédure pas à pas détaillée de la création d’une machine virtuelle SQL Server dans le portail Azure, consultez [le tutoriel de provisionnement](../../../azure-sql/virtual-machines/windows/create-sql-vm-portal.md).

### <a name="resource-manager-templates"></a>Modèles Resource Manager

Si vous utilisez les modèles Resource Manager suivants, deux disques de données premium sont attachés par défaut, sans aucune configuration de pool de stockage. Toutefois, vous pouvez personnaliser ces modèles afin de modifier le nombre de disques de données premium attachés à la machine virtuelle.

* [Créer des machines virtuelles avec sauvegarde automatisée](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Créer des machines virtuelles avec mise à jour corrective automatisée](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Créer des machines virtuelles avec AKV Integration](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>Modèle de démarrage rapide

Vous pouvez utiliser le modèle de démarrage rapide suivant pour déployer une machine virtuelle SQL Server à l’aide de l’optimisation du stockage. 

* [Créer une machine virtuelle avec optimisation du stockage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [Créer une machine virtuelle à l’aide de UltraSSD](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>Machines virtuelles existantes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Pour les machines virtuelles SQL Server existantes, vous pouvez modifier certains paramètres de stockage dans le portail Azure. Ouvrez votre [ressource Machines virtuelles SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource), puis sélectionnez **Vue d’ensemble**. La page Vue d’ensemble de SQL Server montre l’utilisation actuelle du stockage de votre machine virtuelle. Tous les lecteurs qui existent sur votre machine virtuelle apparaissent dans ce graphique. Pour chaque lecteur, l’espace de stockage s’affiche dans quatre sections :

* Données SQL
* Journal SQL
* Autre (stockage non-SQL)
* Disponible

Pour modifier les paramètres de stockage, sélectionnez **Configurer** sous **Paramètres**. 

![Capture d’écran mettant en surbrillance l’option Configurer et la section Utilisation du stockage.](./media/storage-configuration/sql-vm-storage-configuration-existing.png)

Vous pouvez modifier les paramètres de disque des lecteurs configurés lors du processus de création de la machine virtuelle SQL Server. Sélectionnez **Étendre le lecteur** pour ouvrir la page de modification du lecteur afin de modifier le type de disque et d'ajouter des disques supplémentaires. 

![Configurer le stockage pour les machines virtuelles SQL Server existantes](./media/storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="automated-changes"></a>Modifications automatisées

Cette section contient des informations de référence sur les modifications de configuration de stockage qu’Azure effectue automatiquement lors du provisionnement des machines virtuelles SQL Server dans le portail Azure.

* Azure configure un pool de stockage à partir du stockage sélectionné à partir de votre machine virtuelle. La prochaine section de cette rubrique fournit les détails de la configuration du pool de stockage.
* La configuration automatique du stockage utilise toujours des [disques de données P30 premium](../../../virtual-machines/disks-types.md). Par conséquent, il existe un mappage 1:1 entre le nombre de téraoctets sélectionné et le nombre de disques de données attachés à votre machine virtuelle.

Pour plus d’informations sur les tarifs, consultez la page [Tarifs de Storage](https://azure.microsoft.com/pricing/details/storage) dans l’onglet **Stockage sur disque** .

### <a name="creation-of-the-storage-pool"></a>Création du pool de stockage

Azure utilise les paramètres suivants pour créer le pool de stockage sur les machines virtuelles SQL Server.

| Paramètre | Valeur |
| --- | --- |
| Taille de l’entrelacement |256 Ko (entrepôt de données) ; 64 Ko (transactionnel) |
| Tailles du disque |1 To chacun |
| Cache |Lire |
| Taille d'allocation |Taille d'unité d'allocation NTFS = 64 Ko |
| Récupération | Récupération simple (sans résilience) |
| Nombre de colonnes |Nombre de disques de données, 8 maximum<sup>1</sup> |


<sup>1</sup> Une fois le pool de stockage créé, vous ne pouvez pas modifier le nombre de colonnes qu’il contient.


### <a name="workload-optimization-settings"></a>Paramètres d’optimisation de la charge de travail

Le tableau suivant décrit les trois options de type de charge de travail disponibles, ainsi que leurs optimisations :

| Type de charge de travail | Description | Optimisations |
| --- | --- | --- |
| **Généralités** |Paramètre par défaut qui prend en charge la plupart des charges de travail |None |
| **Traitement transactionnel** |Optimise le stockage pour les charges de travail OLTP de base de données traditionnelles |Indicateur de trace 1117<br/>Indicateur de trace 1118 |
| **Entrepôt de données** |Optimise le stockage pour les charges de travail d’analyse et de création de rapports |Indicateur de trace 610<br/>Indicateur de trace 1117 |

> [!NOTE]
> Vous pouvez uniquement spécifier le type de charge de travail si vous provisionnez une machine virtuelle SQL Server en la sélectionnant à l’étape de configuration du stockage.

## <a name="enable-caching"></a>Activer la mise en cache 

Modifiez la stratégie de mise en cache au niveau du disque. Vous pouvez le faire à l’aide du portail Azure, de [PowerShell](/powershell/module/az.compute/set-azvmdatadisk) ou d’[Azure CLI](/cli/azure/vm/disk). 

Pour modifier votre stratégie de mise en cache dans le portail Azure, procédez comme suit :

1. Arrêtez votre service SQL Server. 
1. Connectez-vous au [portail Azure](https://portal.azure.com). 
1. Accédez à votre machine virtuelle, sélectionnez **Disques** sous **Paramètres**. 
   
   ![Capture d’écran montrant le panneau de configuration du disque de machine virtuelle dans le portail Azure.](./media/storage-configuration/disk-in-portal.png)

1. Sélectionnez la stratégie de mise en cache appropriée pour votre disque dans la liste déroulante. 

   ![Capture d’écran montrant la configuration de la stratégie de mise en cache du disque dans le portail Azure.](./media/storage-configuration/azure-disk-config.png)

1. Une fois la modification prise en compte, redémarrez la machine virtuelle SQL Server et démarrez le service SQL Server. 


## <a name="enable-write-accelerator"></a>Activer l’Accélérateur des écritures

L’accélération d’écriture est une fonctionnalité de disque qui est uniquement disponible pour les machines virtuelles de la série M. L’objectif de l’accélération d’écriture est d’améliorer la latence d’E/S des écritures sur Stockage Premium Azure lorsque vous avez besoin d’une latence d’E/S à un chiffre en raison de charges de travail OLTP critiques ou d’environnements d’entrepôts de données de grande envergure. 

Arrêtez toutes les activités de SQL Server et arrêtez le service SQL Server avant d’apporter des modifications à votre stratégie d’accélération d’écriture. 

Si vos disques sont agrégés par bandes, activez l’accélération d’écriture pour chaque disque individuellement, et votre machine virtuelle Azure doit être arrêtée avant d’effectuer toute modification. 

Pour activer l’accélération d’écriture à l’aide du portail Azure, procédez comme suit :

1. Arrêtez votre service SQL Server. Si vos disques sont agrégés par bandes, arrêtez la machine virtuelle. 
1. Connectez-vous au [portail Azure](https://portal.azure.com). 
1. Accédez à votre machine virtuelle, sélectionnez **Disques** sous **Paramètres**. 
   
   ![Capture d’écran montrant le panneau de configuration du disque de machine virtuelle dans le portail Azure.](./media/storage-configuration/disk-in-portal.png)

1. Choisissez l’option de cache avec **Accélérateur d’écriture** pour votre disque dans la liste déroulante. 

   ![Capture d’écran montrant la stratégie de mise en cache d’Accélérateur d’écriture.](./media/storage-configuration/write-accelerator.png)

1. Une fois la modification prise en compte, démarrez la machine virtuelle et le service SQL Server. 

## <a name="disk-striping"></a>Entrelacement de disques

Pour augmenter le débit, vous pouvez ajouter des disques de données et utiliser l’entrelacement de disques. Pour déterminer le nombre de disques de données, analysez le débit et la bande passante nécessaires pour vos fichiers de données SQL Server, y compris le journal et tempdb. Les limites de débit et de bande passante varient en fonction de la taille de machine virtuelle. Pour en savoir plus, consultez [Taille de la machine virtuelle](../../../virtual-machines/sizes.md)


* Pour Windows 8/Windows Server 2012 ou version ultérieure, utilisez des [espaces de stockage](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)) en respectant les consignes suivantes :

  1. Définissez l’entrelacement (taille de la bande) à 64 ko (65 536 octets) pour éviter tout impact sur les performances dû au mauvais alignement des partitions. Ces paramètres doivent être définis avec PowerShell.

  2. Nombre de colonnes définies = nombre de disques physiques. Utilisez PowerShell lorsque vous configurez plus de 8 disques (et non l’interface utilisateur du gestionnaire de serveur).

Par exemple, le code PowerShell suivant crée un pool de stockage avec un entrelacement de 64 Ko et un nombre de colonnes équivalent à la quantité de disques physiques dans le pool de stockage :

  ```powershell
  $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
  
  New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
      -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
      -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
      –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
      -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
      -AllocationUnitSize 65536 -Confirm:$false 
  ```

  * Pour Windows 2008 R2 ou version antérieure, vous pouvez utiliser des disques dynamiques (volumes entrelacés de système d’exploitation). La taille de l’entrelacement restera 64 Ko. Cette option est déconseillée à partir de Windows 8/Windows Server 2012. Pour plus d’informations, consultez les instructions du support dans [Transition du service de disque virtuel vers l’API de gestion de stockage Windows](https://docs.microsoft.com/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api).
 
  * Si vous utilisez les [espaces de stockage direct (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) avec des [instances de cluster de basculement SQL Server](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure), vous devez configurer un pool unique. Même si vous créez différents volumes sur ce pool unique, tous partageront les mêmes caractéristiques, par exemple la même stratégie de mise en cache.
 
  * Déterminez le nombre de disques associés à votre pool de stockage en fonction de vos attentes en matière de charge. N’oubliez pas que les différentes tailles de machines virtuelles autorisent différents nombres de disques de données attachés. Pour plus d’informations, consultez [Tailles des machines virtuelles](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json).


## <a name="next-steps"></a>Étapes suivantes

Pour d’autres rubriques relatives à l’utilisation de SQL Server sur des machines virtuelles Azure, voir [SQL Server sur les machines virtuelles Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).
