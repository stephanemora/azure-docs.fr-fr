---
title: Recommandations de performances pour SQL Server dans Azure | Microsoft Docs
description: Présente les recommandations pour optimiser les performances de SQL Server dans les machines virtuelles Microsoft Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/18/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d720fdd05a4356b8fec2c007a4e5a3d1ea1cf00b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91293303"
---
# <a name="performance-guidelines-for-sql-server-on-azure-virtual-machines"></a>Recommandations de performances pour SQL Server sur les machines virtuelles Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Cet article donne des conseils pour optimiser les performances de SQL Server dans les machines virtuelles Microsoft Azure.

## <a name="overview"></a>Vue d’ensemble

 Quand vous exécutez SQL Server sur des machines virtuelles Azure, nous vous recommandons de continuer à utiliser les mêmes options de réglage des performances de base de données qui s’appliquent à SQL Server dans des environnements serveur locaux. Toutefois, les performances d’une base de données relationnelle dans un cloud public dépendent de nombreux facteurs, comme la taille de la machine virtuelle et la configuration des disques de données.

Les [images SQL Server provisionnées dans le portail Azure](sql-vm-create-portal-quickstart.md) suivent les bonnes pratiques générales en matière de configuration du stockage. Pour plus d’informations sur la configuration du stockage, consultez [Configuration du stockage pour les machines virtuelles SQL Server](storage-configuration.md). Après l’approvisionnement, vous pouvez appliquer les autres optimisations présentées dans cet article. Faites votre choix en fonction de votre charge de travail et vérifiez en effectuant des tests.

> [!TIP]
> Il existe généralement un compromis entre l’optimisation des coûts et l’optimisation des performances. Cet article est axé sur l’obtention des *meilleures* performances pour SQL Server sur les machines virtuelles Azure. Si votre charge de travail est moindre, vous n’aurez peut-être pas besoin de toutes les optimisations suivantes. Tenez compte de vos besoins de performances, des coûts et des modèles de charges de travail lors de l’évaluation de ces recommandations.

## <a name="quick-checklist"></a>Liste de vérification rapide

Voici une liste de vérification rapide pour optimiser les performances de SQL Server sur machines virtuelles Azure :

| Domaine | Optimisations |
| --- | --- |
| [Taille de la machine virtuelle](#vm-size-guidance) | - Utilisez des tailles de machine virtuelle à 4 processeurs virtuels ou plus comme [E4S_v3](../../../virtual-machines/ev3-esv3-series.md) ou supérieur, ou [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md) ou supérieur.<br/><br/> Les - [séries Es, EAS, DS et Das](../../../virtual-machines/sizes-general.md) sont optimales en termes de rapport mémoire/processeurs virtuels pour les performances de la charge de travail OLTP. <br/><br/> La - [série M](../../../virtual-machines/m-series.md) offre le meilleur rapport mémoire/processeurs virtuels pour les performances stratégiques et s’avère idéale pour les charges de travail d’entrepôt de données. <br/><br/> - Identifiez les besoins de la charge de travail cible en termes d’[IOPS](../../../virtual-machines/windows/premium-storage-performance.md#iops), de [débit](../../../virtual-machines/windows/premium-storage-performance.md#throughput) et de [latence](../../../virtual-machines/windows/premium-storage-performance.md#latency) aux heures de pointe en suivant la [liste de vérification des exigences de performances applicatives](../../../virtual-machines/windows/premium-storage-performance.md#application-performance-requirements-checklist), puis choisissez la [taille de machine virtuelle](../../../virtual-machines/sizes-general.md) qui peut s’adapter aux exigences de performances de votre charge de travail.|
| [Stockage](#storage-guidance) | - Le détail des tests de performances de SQL Server sur les machines virtuelles Azure avec les benchmarks TPC-E et TPC_C est consultable sur le billet de blog relatif à l’[optimisation des performances OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). <br/><br/> - Pour bénéficier du meilleur rapport prix/performances, utilisez des [SSD Premium](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). Configurez un [cache en lecture seule](../../../virtual-machines/windows/premium-storage-performance.md#disk-caching) pour les fichiers de données et aucun cache pour le fichier journal. <br/><br/> - Utilisez des [disques Ultra](../../../virtual-machines/disks-types.md#ultra-disk) si la charge de travail nécessite des latences de stockage de moins de 1 ms. Pour plus d’informations, consultez [Migration vers un disque Ultra](storage-migrate-to-ultradisk.md). <br/><br/> - Identifiez les besoins en latence de stockage pour les fichiers de données, le fichier journal et les fichiers TempDB SQL Server en [analysant l’application](../../../virtual-machines/windows/premium-storage-performance.md#application-performance-requirements-checklist) avant de choisir le type de disque. Si des latences de stockage inférieures à 1 ms sont nécessaires, utilisez des disques Ultra. Sinon, utilisez des SSD Premium. Si de faibles latences sont exigées uniquement pour le fichier journal et non pour les fichiers de données, [provisionnez le disque Ultra](../../../virtual-machines/disks-enable-ultra-ssd.md) aux niveaux d’IOPS et de débit exigés uniquement pour le fichier journal. <br/><br/> Les -  [partages de fichiers Premium](failover-cluster-instance-premium-file-share-manually-configure.md) sont recommandés en tant que stockage partagé pour une instance de cluster de basculement SQL Server. Les partages de fichiers Premium ne prennent pas en charge la mise en cache et offrent des performances limitées par rapport aux disques SSD Premium. Choisissez des disques managés SSD Premium sur des partages de fichiers Premium pour les instances SQL autonomes ; en revanche, utilisez des partages de fichiers Premium pour le stockage partagé de l’instance de cluster de basculement pour privilégier la facilité de maintenance et la souplesse de scalabilité. <br/><br/> - Le stockage standard est préconisé uniquement pour le développement et les tests ou pour les fichiers de sauvegarde. Il ne doit pas être utilisé pour les charges de travail de production. <br/><br/> - Conservez le [compte de stockage](../../../storage/common/storage-create-storage-account.md) et la machine virtuelle SQL Server dans la même région.<br/><br/> - Désactivez le [stockage géoredondant](../../../storage/common/storage-redundancy.md) (géoréplication) Azure sur le compte de stockage.  |
| [Disques](#disks-guidance) | - Utilisez au moins 2 [disques SSD Premium](../../../virtual-machines/disks-types.md#premium-ssd) (1 pour le fichier journal et 1 pour les fichiers de données). <br/><br/> - Pour les charges de travail nécessitant des latences d’E/S de moins de 1 ms, activez l’accélérateur d’écriture pour la série M et envisagez d’utiliser des disques SSD Ultra pour les séries ES et DS. <br/><br/> - Activez la [mise en cache en lecture seule](../../../virtual-machines/windows/premium-storage-performance.md#disk-caching) sur le ou les disques hébergeant les fichiers de données.<br/><br/> - Ajoutez 20 % de capacité d’IOPS/débit Premium en plus de celle exigée par votre charge de travail pendant la [configuration du stockage pour les fichiers de données, le fichier journal et les fichiers TempDB SQL Server](storage-configuration.md). <br/><br/> - Évitez d’utiliser des disques de système d’exploitation ou temporaires pour le stockage ou la journalisation des bases de données.<br/><br/> - N’activez pas la mise en cache sur le ou les disques hébergeant le fichier journal.  **Important !** Arrêtez le service SQL Server quand vous changez le paramètre de cache d’un disque Machines virtuelles Azure.<br/><br/> - Entrelacez plusieurs disques de données Azure pour bénéficier d’un débit de stockage accru.<br/><br/> - Formatez avec des tailles d’allocation documentées. <br/><br/> - Placez TempDB sur le lecteur `D:\` SSD local pour les charges de travail SQL Server stratégiques (après avoir choisi la taille de machine virtuelle appropriée). Si vous créez la machine virtuelle à partir du portail Azure ou de modèles de démarrage rapide Azure et que vous [placez TempDB sur le disque local](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583), vous n’avez rien d’autre à faire. Pour tous les autres cas, suivez les étapes décrites dans le billet de blog relatif à l’[utilisation de disques SSD pour le stockage de TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) pour éviter des défaillances après les redémarrages. Si la capacité du lecteur local n’est pas suffisante par rapport à la taille de vos fichiers TempDB, transférez ces derniers d’un pool de stockage [supprimé](../../../virtual-machines/windows/premium-storage-performance.md) vers des disques SSD Premium avec la [mise en cache en lecture seule](../../../virtual-machines/windows/premium-storage-performance.md#disk-caching). |
| [E/S](#io-guidance) |- Activez la compression des pages de base de données.<br/><br/> - Activez l’initialisation de fichiers instantanée pour les fichiers de données.<br/><br/> - Limitez la croissance automatique sur la base de données.<br/><br/> - Désactivez la réduction automatique sur la base de données.<br/><br/> - Déplacez toutes les bases de données vers des disques de données, y compris les bases de données système.<br/><br/> - Déplacez les répertoires des journaux d’erreurs et des fichiers de trace SQL Server vers des disques de données.<br/><br/> - Configurez les emplacements par défaut des fichiers de sauvegarde et de base de données.<br/><br/> - [Activez les pages verrouillées en mémoire](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows?view=sql-server-2017).<br/><br/> - Appliquez les correctifs de performances de SQL Server. |
| [Spécifique aux fonctionnalités](#feature-specific-guidance) | - Sauvegardez directement dans le stockage Blob Azure.<br/><br/>- Utilisez des [sauvegardes d’instantanés de fichiers](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) pour les bases de données de plus de 12 To. <br/><br/>- Utilisez plusieurs fichiers TempDB, 1 fichier par cœur, 8 fichiers au maximum.<br/><br/>- Définissez la mémoire maximum du serveur à 90 % ou jusqu’à 50 Go réservés au système d’exploitation. <br/><br/>- Activez le NUMA logiciel. |

Pour plus d’informations sur *comment* et *pourquoi* effectuer ces optimisations, passez en revue les détails et les instructions fournies dans les sections suivantes.

## <a name="vm-size-guidance"></a>Conseils liés à la taille des machines virtuelles

Commencez par identifier les besoins de la charge de travail en termes de débit de processeur, de mémoire et de stockage aux heures de pointe. Les compteurs de performances \LogicalDisk\Lectures disque/s et \LogicalDisk\Écritures disque/s peuvent être utilisés pour identifier les besoins en IOPS des lectures et écritures ; le compteur \LogicalDisk\Octets disque/s peut être utilisé pour identifier les [besoins en débit de stockage](../../../virtual-machines/premium-storage-performance.md#disk-caching) pour les fichiers de données, le fichier journal et les fichiers TempDB. Une fois que les besoins maximum en IOPS et débit ont été définis, évaluez les tailles de machine virtuelle qui offrent cette capacité. Par exemple, si votre charge de travail nécessite 20 000 IOPS de lecture et 10 000 IOPS d’écriture au maximum, vous pouvez choisir E16s_v3 (avec jusqu’à 32 000 mis en cache et 25 600 non mis en cache) ou M16_s (avec jusqu’à 20 000 mis en cache et 10 000 non mis en cache) avec 2 disques P30. Veillez à bien identifier les besoins de la charge de travail en termes de débit et d’IOPS, car les machines virtuelles ont des limites d’échelle différentes pour les IOPS et le débit.<br/><br/>Les séries [DSv_3](../../../virtual-machines/dv3-dsv3-series.md) et [Es_v3](../../../virtual-machines/ev3-esv3-series.md) sont hébergées sur du matériel à usage général avec des processeurs Intel Haswell ou Broadwell. La [série M](../../../virtual-machines/m-series.md) offre le nombre de processeurs virtuels et la quantité de mémoire les plus élevés pour les charges de travail SQL Server les plus importantes hébergées sur du matériel à mémoire optimisée avec la famille de processeurs Skylake. Cette série de machines virtuelles prend en charge le stockage Premium, qui est recommandé pour des performances optimales avec le cache de lecture au niveau de l’hôte. Les séries Es_v3 et M sont aussi disponibles dans des [tailles de cœur restreintes](../../../virtual-machines/constrained-vcpu.md), ce qui réduit le coût des charges de travail qui ont des besoins en capacité supérieurs pour le stockage que pour le calcul. 

## <a name="storage-guidance"></a>Conseils liés au stockage

Le détail des tests de performances de SQL Server sur les machines virtuelles Azure avec les benchmarks TPC-E et TPC_C est consultable sur le billet de blog relatif à l’[optimisation des performances OLTP](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794). 

Le cache Blob Azure avec disques SSD Premium est recommandé pour toutes les charges de travail de production. 

> [!WARNING]
> Les disques durs standard et les disques SSD possèdent différents temps de latence et une bande passante variable. Ils sont recommandés uniquement pour les charges de travail de développement/de test. Les charges de production doivent utiliser des disques SSD premium.

Par ailleurs, nous vous recommandons de créer votre compte de stockage Azure dans le même centre de données que vos machines virtuelles SQL Server afin de réduire les délais de transfert. Lors de la création d’un compte de stockage, désactivez la géo-réplication, étant donné que la cohérence de l’ordre d’écriture sur différents disques n’est pas garantie. Envisagez plutôt de configurer une technologie de récupération d’urgence de SQL Server entre deux centres de données Azure. Pour plus d’informations, voir [Haute disponibilité et récupération d’urgence pour SQL Server dans Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="disks-guidance"></a>Conseils liés aux disques

Il existe trois types de disques principaux sur les machines virtuelles Azure :

* **Disque de système d’exploitation** : Quand vous créez une machine virtuelle Azure, la plateforme attache au moins un disque (désigné par la lettre **C**) à la machine virtuelle en tant que disque de système d’exploitation. Ce disque est un disque dur virtuel (VHD) stocké en tant qu’objet blob de pages dans le stockage.
* **Disque temporaire** : Les machines virtuelles Azure contiennent un autre disque, appelé disque temporaire (portant le nom de lecteur **D**:). C’est un disque du nœud qui peut être utilisé comme un espace de travail temporaire.
* **Disques de données** : Vous pouvez également attacher des disques supplémentaires à votre machine virtuelle en tant que disques de données ; ces disques sont également stockés en tant qu’objets blob de pages.

Les sections suivantes décrivent des recommandations pour l’utilisation de ces disques différents.

### <a name="operating-system-disk"></a>Disque de système d’exploitation

Un disque de système d’exploitation est un disque dur virtuel (VHD) que vous pouvez amorcer et monter comme version d’exécution d’un système d’exploitation. Il est désigné par la lettre de lecteur **C**.

La stratégie de mise en cache par défaut sur le disque du système d’exploitation est **Lecture/Écriture**. Pour les applications sensibles aux performances, nous vous recommandons d’utiliser le disque de données au lieu du disque du système d’exploitation. Reportez-vous à la section sur les disques de données ci-dessous.

### <a name="temporary-disk"></a>Disque temporaire

Le lecteur de stockage temporaire, désigné par la lettre **D**, n’est pas persistant dans le Stockage Blob Azure. Ne stockez pas vos fichiers de base de données utilisateur ou vos fichiers journaux des transactions utilisateur sur le lecteur **D**:.

Placez TempDB sur le lecteur `D:\` SSD local pour les charges de travail SQL Server stratégiques (après avoir choisi la bonne taille de machine virtuelle). Si vous créez la machine virtuelle à partir du portail Azure ou de modèles de démarrage rapide Azure et que vous [placez TempDB sur le disque local](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583), vous n’avez rien d’autre à faire. Pour tous les autres cas, suivez les étapes décrites dans le billet de blog relatif à l’[utilisation de disques SSD pour le stockage de TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) pour éviter des défaillances après les redémarrages. Si la capacité du lecteur local n’est pas suffisante par rapport à la taille de vos fichiers TempDB, transférez ces derniers d’un pool de stockage [supprimé](../../../virtual-machines/premium-storage-performance.md) vers des disques SSD Premium avec la [mise en cache en lecture seule](../../../virtual-machines/premium-storage-performance.md#disk-caching).

Pour des machines virtuelles qui prennent en charge les SSD Premium, vous pouvez aussi stocker TempDB sur un disque qui prend en charge les SSD Premium avec la mise en cache en lecture activée.


### <a name="data-disks"></a>Disques de données

* **Utilisez des disques de données pour les fichiers journaux et de données** : si vous n’utilisez pas l’entrelacement de disques, utilisez deux disques SSD premium (un pour le fichier journal et l’autre pour les données). Chaque SSD Premium offre un nombre d’IOPS et une bande passante (Mo/s) qui varient en fonction de sa taille, comme décrit dans l’article [Sélectionner un type de disque](../../../virtual-machines/disks-types.md). Si vous utilisez une technique d’entrelacement de disques, comme des espaces de stockage, vous obtenez des performances optimales en ayant deux pools, un pour les fichiers journaux et l’autre pour les fichiers de données. Cependant, si vous envisagez d’utiliser des instances de cluster de basculement SQL Server, vous devez configurer un pool ou utiliser des [partages de fichiers Premium](failover-cluster-instance-premium-file-share-manually-configure.md).

   > [!TIP]
   > - Pour obtenir les résultats des tests sur différentes configurations de disque et de charge de travail, consultez le billet de blog suivant : [Storage Configuration Guidelines for SQL Server on Azure Virtual Machines](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/).
   > - Pour les performances stratégiques des serveurs SQL Server nécessitant ~50 000 E/S par seconde, envisagez de remplacer 10 disques -P30 par un disque SSD Ultra. Pour plus d’informations, consultez le billet de blog suivant : [Performances stratégiques avec un disque SSD Ultra](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/).

   > [!NOTE]
   > Lorsque vous configurez une machine virtuelle SQL Server dans le portail, vous avez la possibilité de modifier votre configuration de stockage. Selon votre configuration, Azure configure un ou plusieurs disques. Plusieurs disques sont combinés en un pool de stockage unique par entrelacement. Les fichiers journaux et de données se trouvent dans cette configuration. Pour plus d’informations, voir [Configuration du stockage pour les machines virtuelles SQL Server](storage-configuration.md).

* **Entrelacement de disques** : Pour augmenter le débit, vous pouvez ajouter des disques de données et utiliser l’entrelacement de disques. Pour déterminer le nombre de disques de données, vous devez analyser le nombre d’opérations d’E/S par seconde et de bande passante disponibles pour vos fichiers journaux, vos données et vos fichiers TempDB. Notez que les différentes tailles de machine virtuelle sont associées à des limites spécifiques concernant la bande passante et le nombre d’opérations d’E/S par seconde pris en charge. Pour en savoir plus, consultez les tableaux relatifs aux nombre d’opérations d’E/S par seconde en fonction des [tailles de machine virtuelle](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Respectez les recommandations suivantes :

  * Pour Windows 8/Windows Server 2012 ou version ultérieure, utilisez des [espaces de stockage](https://technet.microsoft.com/library/hh831739.aspx) en respectant les consignes suivantes :

      1. Définissez l’intervalle (taille de bande) sur 64 Ko (65 536 octets) pour les charges de travail OLTP et sur 256 Ko (262 144 octets) pour les charges de travail d’entrepôt de données, afin d’éviter qu’un alignement de partition incorrect n’affecte les performances. Ces paramètres doivent être définis avec PowerShell.
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

  * Pour Windows 2008 R2 ou version antérieure, vous pouvez utiliser des disques dynamiques (volumes entrelacés de système d’exploitation). La taille de l’entrelacement restera 64 Ko. Cette option est déconseillée à partir de Windows 8/Windows Server 2012. Pour plus d’informations, consultez les instructions du support dans [Transition du service de disque virtuel vers l’API de gestion de stockage Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Si vous utilisez les [espaces de stockage direct (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) avec des [instances de cluster de basculement SQL Server](failover-cluster-instance-storage-spaces-direct-manually-configure.md), vous devez configurer un pool unique. Même si vous créez différents volumes sur ce pool unique, tous partageront les mêmes caractéristiques, par exemple la même stratégie de mise en cache.

  * Déterminez le nombre de disques associés à votre pool de stockage en fonction de vos attentes en matière de charge. N’oubliez pas que les différentes tailles de machines virtuelles autorisent différents nombres de disques de données attachés. Pour plus d’informations, consultez [Tailles des machines virtuelles](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Si vous n’utilisez pas les SSD premium (scénarios de développement/de test), nous vous recommandons d’ajouter le nombre maximal de disques de données pris en charge par la [taille de votre machine virtuelle](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) et d’utiliser l’entrelacement de disques.

* **Stratégie de mise en cache** : Notez les recommandations suivantes pour la stratégie de mise en cache en fonction de la configuration de votre stockage.

  * Si vous utilisez des disques différents pour les fichiers journaux et de données, activez la mise en cache de lecture sur les disques de données hébergeant vos fichiers de données et les fichiers de données TempDB. Cela devrait vous permettre d’obtenir un gain de performances significatif. N’activez pas la mise en cache sur le disque contenant le fichier journal, car cela entraîne une légère diminution des performances.

  * Si vous utilisez l’entrelacement de disques dans un pool de stockage unique, la plupart des charges de travail peuvent tirer parti de la mise en cache en lecture. Si vous avez des pools de stockage distincts pour les fichiers journaux et de données, activez la mise en cache en lecture uniquement sur le pool de stockage pour les fichiers de données. Avec certaines charges de travail nécessitant beaucoup d’écritures, il est possible d’améliorer les performances sans mise en cache. Cela ne peut être déterminé que par le biais de test.

  * Les suggestions précédentes s’appliquent aux disques SSD premium. Si vous n’utilisez pas des disques SSD premium, n’activez aucune mise en cache sur les disques de données.

  * Pour obtenir des instructions sur la configuration de la mise en cache des disques, consultez les articles suivants. Pour plus d’informations sur le modèle de déploiement classique (ASM), consultez : [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) et [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx). Pour plus d’informations sur le modèle de déploiement Azure Resource Manager, consultez : [Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) et [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk).

     > [!WARNING]
     > Arrêtez le service SQL Server quand vous changez le paramètre de cache de disques de machines virtuelles Azure pour éviter toute altération de la base de données.

* **Taille d’unité d’allocation NTFS** : Lors du formatage du disque de données, il est recommandé d’utiliser une taille d’unité d’allocation de 64 Ko pour les fichiers de données et les fichiers journaux, ainsi que pour TempDB. Si TempDB est placée sur le disque temporaire (lecteur D:\), les performances obtenues en tirant parti de ce lecteur l’emportent sur la nécessité d’une taille d’unité d’allocation de 64 Ko. 

* **Bonnes pratiques de gestion des disques** : Lors de la suppression d’un disque de données ou de la modification de son type de cache, arrêtez le service SQL Server. Lorsque les paramètres de mise en cache sont modifiés sur le disque du système d’exploitation, Azure arrête la machine virtuelle, modifie le type de cache et redémarre la machine virtuelle. Lorsque les paramètres du cache d’un disque de données sont modifiés, la machine virtuelle n’est pas arrêtée, mais le disque de données est détaché de la machine virtuelle lors de la modification, puis rattaché.

  > [!WARNING]
  > Si le service SQL Server n’est pas arrêté au cours de ces opérations, cela peut provoquer une altération de la base de données.


## <a name="io-guidance"></a>Recommandations liées aux E/S

* Pour obtenir les meilleurs résultats avec les disques SSD premium, vous devez paralléliser vos applications et vos demandes. Les disques SSD Premium sont conçus pour les scénarios où la profondeur de file d’attente est supérieure à 1. Les améliorations seront donc minimes ou inexistantes pour les demandes de série à thread unique (même si elles sont gourmandes en stockage). Par exemple, cela peut affecter les résultats des tests à thread unique des outils d’analyses de performances, par exemple SQLIO.

* Envisagez d’utiliser un [compression des pages de base de données](https://msdn.microsoft.com/library/cc280449.aspx) , car elle peut améliorer les performances de charges de travail gourmandes en E/S. Toutefois, la compression de données peut augmenter la consommation d’UC sur le serveur de base de données.

* Envisagez d’activer l’initialisation instantanée des fichiers pour réduire le temps requis pour l’allocation initiale des fichiers. Pour tirer parti de l’initialisation instantanée des fichiers, vous devez accorder l’autorisation SE_MANAGE_VOLUME_NAME au compte de service SQL Server (MSSQLSERVER) et l’ajouter à la stratégie de sécurité **Effectuer des tâches de maintenance sur les volumes**. Si vous utilisez une image de plateforme SQL Server pour Azure, le compte de service par défaut (NT Service\MSSQLSERVER) n’est pas ajouté à la stratégie de sécurité **Effectuer des tâches de maintenance sur les volumes**. En d’autres termes, l’initialisation instantanée des fichiers n’est pas activée dans une image de plateforme SQL Server pour Azure. Après avoir ajouté le compte de service SQL Server à la stratégie de sécurité **Effectuer les tâches de maintenance de volume** , redémarrez le service SQL Server. Des considérations de sécurité liées à l’utilisation de cette fonctionnalité peuvent exister. Pour plus d’informations, consultez [Initialisation des fichiers de base de données](https://msdn.microsoft.com/library/ms175935.aspx).

* Gardez à l’esprit qu’**autogrow** est considéré comme un simple plan d’urgence en cas de croissance plus rapide que prévu. Ne gérez pas la croissance de vos données et journaux quotidiennement avec la croissance automatique. En cas d’utilisation de la croissance automatique, augmentez préalablement le fichier à l’aide du commutateur Taille.

* Vérifiez que la fonctionnalité de réduction automatique ( **autoshrink** ) est désactivée afin d’éviter une surcharge inutile susceptible d’affecter négativement les performances.

* Déplacer toutes les bases de données vers des disques de données, y compris les bases de données système. Pour plus d’informations, consultez [Déplacer des bases de données système](https://msdn.microsoft.com/library/ms345408.aspx).

* Déplacer les répertoires des journaux d’erreurs et des fichiers de trace SQL Server vers des disques de données. Cette opération peut être effectuée dans le Gestionnaire de configuration SQL Server en double-cliquant sur l’instance de SQL Server et en sélectionnant des propriétés. Il est possible de modifier les paramètres du fichier journal et du fichier de trace sous l’onglet **Paramètres de démarrage** . Le répertoire de vidage est spécifié sous l’onglet **Avancé** . La capture d’écran suivante indique où rechercher le paramètre de démarrage du journal des erreurs.

    ![Capture d’écran du journal des erreurs SQL](./media/performance-guidelines-best-practices/sql_server_error_log_location.png)

* Configurez les emplacements par défaut du fichier de sauvegarde et du fichier de base de données. Utilisez les recommandations de cet article, puis apportez les modifications souhaitées dans la fenêtre Propriétés du serveur. Pour obtenir des instructions, consultez [Afficher ou modifier les emplacements par défaut des fichiers de données et des fichiers journaux (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). La capture d’écran suivante montre comment effectuer ces modifications.

    ![Journal des données et fichiers de sauvegarde SQL](./media/performance-guidelines-best-practices/sql_server_default_data_log_backup_locations.png)
* Activez les pages verrouillées pour réduire les activités d’E/S et de pagination. Pour plus d’informations, consultez [Activer l’option Lock Pages in Memory (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* Si vous exécutez SQL Server 2012, installez la mise à jour cumulative 10 Service Pack 1. Elle contient le correctif qui permet de résoudre les problèmes de performances d’E/S lorsque vous exécutez une sélection dans l’instruction de table temporaire dans SQL Server 2012. Pour plus d’informations, consultez cet [article de la Base de connaissances](https://support.microsoft.com/kb/2958012).

* Envisagez de compresser tous les fichiers de données lors des transferts vers et depuis Azure.

## <a name="feature-specific-guidance"></a>Conseils spécifiques aux fonctionnalités

Certains déploiements peuvent bénéficier de plus grands avantages en termes de performances à l’aide de techniques de configuration avancées. La liste suivante présente certaines fonctionnalités SQL Server qui peuvent vous aider à améliorer les performances :

### <a name="back-up-to-azure-storage"></a>Sauvegarder sur Stockage Azure
Durant la création de sauvegardes pour un serveur SQL Server s’exécutant sur des machines virtuelles Azure, vous pouvez vous référer à [Sauvegarde de SQL Server vers une URL](https://msdn.microsoft.com/library/dn435916.aspx). Cette fonctionnalité est disponible à partir de SQL Server 2012 SP1 CU2, et recommandée pour la sauvegarde vers les disques de données attachés. Lors de la sauvegarde ou la restauration vers/depuis Stockage Azure, suivez les recommandations indiquées dans [Meilleures pratiques et résolution des problèmes pour la sauvegarde de SQL Server vers une URL et Restauration à partir de sauvegardes stockées dans Azure Storage](https://msdn.microsoft.com/library/jj919149.aspx). Vous pouvez également automatiser ces sauvegardes en utilisant la [Sauvegarde automatisée pour SQL Server sur les machines virtuelles Azure](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md).

Pour les versions antérieures à SQL Server 2012, vous pouvez utiliser l’ [outil de sauvegarde SQL Server vers Azure](https://www.microsoft.com/download/details.aspx?id=40740). Cet outil peut augmenter le débit de sauvegarde à l’aide de plusieurs cibles d’entrelacement de sauvegarde.

### <a name="sql-server-data-files-in-azure"></a>Fichiers de données SQL Server dans Azure

Cette nouvelle fonctionnalité, [Fichiers de données SQL Server dans Azure](https://msdn.microsoft.com/library/dn385720.aspx), est disponible à partir de SQL Server 2014. L’exécution de SQL Server avec des fichiers de données dans Azure offre des caractéristiques de performances comparables à l’utilisation de disques de données Azure.

### <a name="failover-cluster-instance-and-storage-spaces"></a>Instance de cluster de basculement et espaces de stockage

Si vous utilisez des espaces de stockage, lors de l’ajout de nœuds dans le cluster sur la page **Confirmation**, décochez la case **Ajouter la totalité du stockage disponible au cluster**. 

![Décochez la case du stockage éligible](./media/performance-guidelines-best-practices/uncheck-eligible-cluster-storage.png)

Si vous utilisez des espaces de stockage et que vous ne désactivez pas la case à cocher **Ajouter la totalité du stockage disponible au cluster**, Windows détache les disques virtuels pendant le processus de mise en cluster. Par conséquent, ils n’apparaissent pas dans le Gestionnaire de disque ou dans l’Explorateur, jusqu’à ce que les espaces de stockage soient supprimés du cluster et rattachés à l’aide de PowerShell. Les espaces de stockage regroupent plusieurs disques dans des pools de stockage. Pour plus d’informations, consultez la page [Espaces de stockage](/windows-server/storage/storage-spaces/overview).

## <a name="multiple-instances"></a>Plusieurs instances 

Tenez compte des meilleures pratiques suivantes lorsque vous déployez plusieurs instances SQL sur une seule machine virtuelle : 

- Définissez la mémoire maximale du serveur pour chaque instance SQL, en vous assurant qu’il reste de la mémoire pour le système d’exploitation. Veillez à mettre à jour les restrictions de mémoire pour les instances SQL si vous modifiez la quantité de mémoire allouée à la machine virtuelle. 
- Ayez des numéros d’unité logique distincts pour les données, les journaux et TempDB puisqu’ils ont tous des modèles de charge de travail différents et que vous ne souhaitez pas qu’ils aient un impact les uns sur les autres. 
- Testez minutieusement votre environnement avec des charges de travail importantes semblables à celle de production pour vous assurer qu’il peut gérer la capacité de pointe conformément au contrat de niveau de service de l’application. 

Les signes d’un système surchargé incluent notamment l’épuisement des threads de travail, des temps de réponse longs et/ou le blocage de la mémoire système du répartiteur. 




## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le stockage et le niveau de performance, consultez [Instructions de configuration du stockage pour SQL Server sur les machines virtuelles Azure](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/).

Pour connaître les meilleures pratiques en matière de sécurité, consultez [Considérations relatives à la sécurité de SQL Server sur les machines virtuelles Azure](security-considerations-best-practices.md).

Consultez d’autres articles relatifs aux machines virtuelles avec SQL Server à la page [Vue d’ensemble de SQL Server sur les machines virtuelles Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Si vous avez des questions sur les machines virtuelles SQL Server, consultez le [Forum aux Questions](frequently-asked-questions-faq.md).
