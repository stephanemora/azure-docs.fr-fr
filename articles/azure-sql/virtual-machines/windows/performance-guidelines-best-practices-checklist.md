---
title: 'Check-list : Bonnes pratiques et recommandations pour les performances'
description: Fournit une check-list rapide qui vous permet de passer en revue les bonnes pratiques et recommandations pour optimiser les performances de votre serveur SQL Server dans une instance de SQL Server sur les machines virtuelles Azure.
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/06/2021
ms.author: dpless
ms.custom: contperf-fy21q3
ms.reviewer: jroth
ms.openlocfilehash: 3649c593fcc7a0d27ed7326efa03b1325763649d
ms.sourcegitcommit: a9f131fb59ac8dc2f7b5774de7aae9279d960d74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110190899"
---
# <a name="checklist-performance-best-practices-for-sql-server-on-azure-vms"></a>Check-list : Bonnes pratiques relatives aux performances pour SQL Server sur les machines virtuelles Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Cet article fournit une check-list rapide sous la forme d’une série de bonnes pratiques et de recommandations pour optimiser les performances de votre serveur SQL Server dans une instance de SQL Server sur les machines virtuelles Azure. 

Pour plus d’informations, consultez les autres articles de cette série : [Taille d’une machine virtuelle](performance-guidelines-best-practices-vm-size.md), [Stockage](performance-guidelines-best-practices-storage.md) et [Collecter une base de référence](performance-guidelines-best-practices-collect-baseline.md). 


## <a name="overview"></a>Vue d’ensemble

Quand vous exécutez SQL Server sur les machines virtuelles Azure, utilisez les options de réglage des performances de base de données qui s’appliquent à SQL Server dans les environnements serveur locaux. Toutefois, les performances d’une base de données relationnelle dans un cloud public dépendent de nombreux facteurs, par exemple la taille d’une machine virtuelle et la configuration des disques de données.

Il existe généralement un compromis entre l’optimisation des coûts et l’optimisation des performances. Cette série de bonnes pratiques sur les performances vise à obtenir les *meilleures* performances possibles pour SQL Server sur les machines virtuelles Azure. Si votre charge de travail est moindre, vous n’aurez peut-être pas besoin de toutes les optimisations recommandées. Tenez compte de vos besoins de performances, des coûts et des modèles de charges de travail lors de l’évaluation de ces recommandations.

## <a name="vm-size"></a>Taille de la machine virtuelle

Voici une check-list rapide des bonnes pratiques relatives à la taille d’une machine virtuelle pour l’exécution d’un serveur SQL Server dans une instance de SQL Server sur les machines virtuelles Azure : 

- Utilisez des tailles de machine virtuelle offrant au moins 4 processeurs virtuels, par exemple les séries [Standard_M8-4ms](../../../virtual-machines/m-series.md), [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series) et [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15), ou versions ultérieures. 
- Utilisez des tailles de machine virtuelle [à mémoire optimisée](../../../virtual-machines/sizes-memory.md) pour optimiser les performances des charges de travail SQL Server. 
- Les séries [DSv2 11 à 15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md), [M-](../../../virtual-machines/m-series.md) et [Mv2-](../../../virtual-machines/mv2-series.md) offrent le ratio optimal mémoire/vCore requis pour les charges de travail OLTP. Les machines virtuelles des deux séries M offrent le plus haut ratio mémoire/vCore requis pour les charges de travail critiques. Elles sont également idéales pour les charges de travail d’entrepôt de données. 
- Choisissez éventuellement un ratio mémoire/vCore plus élevé pour les charges de travail critiques et celles qui sont relatives aux entrepôt de données. 
- Utilisez les images de machine virtuelle de la Place de marché Azure, car les paramètres et les options de stockage de SQL Server sont configurés pour optimiser les performances de SQL Server. 
- Collectez les caractéristiques de performances de la charge de travail cible, et utilisez-les pour déterminer la taille de machine virtuelle appropriée pour votre entreprise.

Pour en savoir plus, consultez les [bonnes pratiques relatives à la taille d’une machine virtuelle](performance-guidelines-best-practices-vm-size.md). 

## <a name="storage"></a>Stockage

Voici une check-list rapide des bonnes pratiques relatives à la configuration du stockage pour l’exécution d’un serveur SQL Server dans une instance de SQL Server sur les machines virtuelles Azure : 

- Surveillez l’application et [déterminez les besoins en bande passante et de latence de stockage](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) pour les fichiers de données, de journal et tempdb SQL Server avant de choisir le type de disque. 
- Pour optimiser les performances du stockage, prévoyez les IOPS non mises en cache les plus élevées disponibles, et utilisez la mise en cache des données en tant que fonctionnalité de performance pour les lectures de données, tout en évitant les [plafonnements/limitations applicables à la machine virtuelle et aux disques](../../../virtual-machines/premium-storage-performance.md#throttling).
- Placez les fichiers de données, de journal et tempdb sur des lecteurs distincts.
    - Pour le lecteur de données, utiliser uniquement les [disques Premium P30 et P40](../../../virtual-machines/disks-types.md#premium-ssd) pour garantir la disponibilité de la prise en charge du cache
    - Pour le lecteur de journaux, prévoyez une capacité suffisante et testez les performances par rapport aux coûts quand vous évaluez les [disques premium P30 - P80](../../../virtual-machines/disks-types.md#premium-ssd).
      - Si la latence de stockage en millisecondes est requise, utilisez des [Disques Ultra Azure](../../../virtual-machines/disks-types.md#ultra-disk) pour le journal des transactions. 
      - Pour les déploiements de machines virtuelles de la série M, utilisez l’[accélérateur d’écriture](../../../virtual-machines/how-to-enable-write-accelerator.md) à la place des disques Ultra Azure.
    - Placez [tempdb](/sql/relational-databases/databases/tempdb-database) sur le lecteur SSD éphémère local `D:\` pour la plupart des charges de travail SQL Server après avoir choisi la taille de machine virtuelle optimale. 
      - Si la capacité du lecteur local n’est pas suffisante pour tempdb, pensez à redimensionner la machine virtuelle. Consultez les [Stratégies de mise en cache de fichiers de données](performance-guidelines-best-practices-storage.md#data-file-caching-policies) pour plus d’informations.
- Entrelacez plusieurs disques de données Azure à l’aide d'[espaces de stockage](/windows-server/storage/storage-spaces/overview) pour augmenter la bande passante d’E/S jusqu’aux limites de débit et d’IOPS de la machine virtuelle cible.
- Définissez la [mise en cache de l’hôte](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) en lecture seule pour les disques de fichiers de données.
- Définissez la [mise en cache de l’hôte](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) sur aucun pour les disques de fichiers journaux.
    - N’activez pas la mise en cache en lecture/écriture sur les disques qui contiennent des fichiers SQL Server. 
    - Arrêtez toujours le service SQL Server avant de modifier les paramètres de cache de votre disque.
- Pour les charges de travail de développement et test, utilisez le service Stockage Standard. Il n’est pas recommandé d’utiliser HDD/SDD Standard pour les charges de travail de production.
- Le [bursting de disque basé sur les crédits](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) ne doit être pris en compte que pour les charges de travail de dev/test et les systèmes départementaux plus petits.
- Formatez votre disque de données de façon à utiliser une taille d’unité d’allocation de 64 Ko pour tous les fichiers de données placés sur un lecteur autre que le lecteur temporaire `D:\` (dont la taille par défaut est de 4 Ko). Les machines virtuelles SQL Server déployées via la Place de marché Azure sont fournies avec des disques de données formatés avec une taille d’unité d’allocation et un entrelacement pour le pool de stockage défini sur 64 Ko. 

Pour en savoir plus, consultez les [bonnes pratiques relatives au stockage](performance-guidelines-best-practices-storage.md). 

## <a name="sql-server-features"></a>fonctionnalités SQL Server

Voici une liste de vérification rapide des meilleures pratiques pour les paramètres de configuration de SQL Server lorsque vous exécutez vos instances SQL dans une machine virtuelle Azure en production : 

- Activer la [compression des pages de la base de données](/sql/relational-databases/data-compression/data-compression), le cas échéant.
- Activer la [compression des sauvegardes](/sql/relational-databases/backup-restore/backup-compression-sql-server).
- Activer l’[initialisation instantanée de fichiers](/sql/relational-databases/databases/database-instant-file-initialization) pour les fichiers de données.
- Limiter la [croissance automatique](/troubleshoot/sql/admin/considerations-autogrow-autoshrink#considerations-for-autogrow) de la base de données.
- Désactiver la [réduction automatique](/troubleshoot/sql/admin/considerations-autogrow-autoshrink#considerations-for-auto_shrink) de la base de données.
- Désactiver la fermeture automatique de la base de données.
- Déplacer toutes les bases de données vers des disques de données, y compris les [bases de données système](/sql/relational-databases/databases/move-system-databases).
- Déplacer les répertoires des journaux d’erreurs et des fichiers de trace SQL Server vers des disques de données.
- Configurer les emplacements par défaut des fichiers de sauvegarde et de base de données.
- Définir la [limite de mémoire SQL Server](/sql/database-engine/configure-windows/server-memory-server-configuration-options#use-) maximale afin de laisser suffisamment de mémoire pour le système d’exploitation. ([Utilisez Mémoire\Octets disponibles](/sql/relational-databases/performance-monitor/monitor-memory-usage) pour surveiller l’intégrité de la mémoire du système d’exploitation.)
- Activer le [verrouillage des pages en mémoire](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).
- Activer l’[optimisation des charges de travail ad hoc](/sql/database-engine/configure-windows/optimize-for-ad-hoc-workloads-server-configuration-option) pour les environnements OLTP lourds.
- Évaluer et appliquer les [dernières mises à jour cumulatives](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) pour les versions installées de SQL Server.
- Activer [Magasin des requêtes](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) sur toutes les bases de données SQL Server de production [suivant les meilleures pratiques](/sql/relational-databases/performance/best-practice-with-the-query-store).
- Activer le [réglage automatique](/sql/relational-databases/automatic-tuning/automatic-tuning) sur les bases de données d’application critiques.
- S’assurer que toutes les [meilleures pratiques tempdb](/sql/relational-databases/databases/tempdb-database#optimizing-tempdb-performance-in-sql-server) sont respectées.
- Placer tempdb sur le lecteur éphémère D:/.
- [Utiliser le nombre de fichiers recommandé](/troubleshoot/sql/performance/recommendations-reduce-allocation-contention#resolution), à l’aide de plusieurs fichiers de données tempdb en commençant par 1 fichier par cœur, et jusqu’à 8 fichiers.
- Planifier des tâches SQL Server Agent pour exécuter les tâches [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql#a-checking-both-the-current-and-another-database), [index reorganize](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes#reorganize-an-index), [index rebuild](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes#rebuild-an-index) et [update statistics](/sql/t-sql/statements/update-statistics-transact-sql#examples).
- Surveiller et gérer l’intégrité et la taille du [fichier journal de transactions](/sql/relational-databases/logs/manage-the-size-of-the-transaction-log-file#Recommendations) de SQL Server.
- Tirer parti de toutes les nouvelles [fonctionnalités de SQL Server](/sql/sql-server/what-s-new-in-sql-server-ver15) disponibles pour la version utilisée.
- Connaître les différences de [fonctionnalités prises en charge](/sql/sql-server/editions-and-components-of-sql-server-version-15) entre les éditions que vous envisagez de déployer.

## <a name="azure-features"></a>Fonctionnalités Azure

Voici une liste de vérification rapide des meilleures pratiques pour obtenir une aide spécifique à Azure lorsque vous exécutez votre instance SQL Server sur machines virtuelles Azure :

- S’inscrire auprès de l’[extension d’agent SQL IaaS](sql-agent-extension-manually-register-single-vm.md) pour déverrouiller un certain nombre d’[avantages liés aux fonctionnalités](sql-server-iaas-agent-extension-automate-management.md#feature-benefits).
- Tirer parti de la meilleure [stratégie de sauvegarde et de restauration](backup-restore.md#decision-matrix) pour votre charge de travail SQL Server.
- S’assurer que les [performances réseau accélérées sont activées](../../../virtual-network/create-vm-accelerated-networking-cli.md#portal-creation) sur la machine virtuelle.
- Tirer parti d’[Azure Security Center](../../../security-center/index.yml) pour améliorer la posture de sécurité globale de votre déploiement.
- Tirer parti d’[Azure Defender](../../../security-center/azure-defender.md), intégré à [Azure Security Center](https://azure.microsoft.com/services/security-center/), pour une [couverture spécifique des machines virtuelles SQL Server](../../../security-center/defender-for-sql-introduction.md), notamment des évaluations des vulnérabilités et un accès juste-à-temps, ce qui réduit la surface d’attaque tout en permettant aux utilisateurs légitimes d’accéder aux machines virtuelles, le cas échéant. Pour plus d’informations, consultez [Évaluation des vulnérabilités](../../../security-center/defender-for-sql-on-machines-vulnerability-assessment.md), [Activer l’évaluation des vulnérabilités pour les machines virtuelles SQL Server](sql-vulnerability-assessment-enable.md) et [Accès juste-à-temps](../../../security-center/just-in-time-explained.md). 
- Tirer parti d’[Azure Advisor](../../../advisor/advisor-overview.md) pour répondre aux recommandations en matière de [performances](../../../advisor/advisor-performance-recommendations.md), de [coûts](../../../advisor/advisor-cost-recommendations.md), de [fiabilité](../../../advisor/advisor-high-availability-recommendations.md), d’[excellence opérationnelle](../../../advisor/advisor-operational-excellence-recommendations.md) et de[ sécurité](../../../advisor/advisor-security-recommendations.md).
- Tirer parti d’[Azure Monitor](../../../azure-monitor/vm/quick-monitor-azure-vm.md) pour collecter les données de télémétrie de votre environnement SQL Server, les analyser et agir en conséquence. Cela comprend l’identification des problèmes d’infrastructure avec [VM insights](../../../azure-monitor/vm/vminsights-overview.md) et la surveillance des données avec [Log Analytics](../../../azure-monitor/logs/log-query-overview.md) pour des diagnostics plus approfondis.
- Activer l’[arrêt automatique](../../../automation/automation-solution-vm-management.md) pour les environnements de développement et de test. 
- Implémenter une solution de haute disponibilité et récupération d’urgence (HADR) qui répond à vos SLA en matière de continuité des activités. Consultez les [options HADR](business-continuity-high-availability-disaster-recovery-hadr-overview.md#deployment-architectures) disponibles pour SQL Server sur machines virtuelles Azure. 
- Utiliser le portail Azure (support + dépannage) pour évaluer l’historique et l’[intégrité des ressources](../../../service-health/resource-health-overview.md) ; envoyer de nouvelles demandes de support le cas échéant.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez les autres articles de cette série :
- [Taille de la machine virtuelle](performance-guidelines-best-practices-vm-size.md)
- [Stockage](performance-guidelines-best-practices-storage.md)
- [Collecter une ligne de base](performance-guidelines-best-practices-collect-baseline.md)

Pour connaître les meilleures pratiques en matière de sécurité, consultez [Considérations relatives à la sécurité de SQL Server sur les machines virtuelles Azure](security-considerations-best-practices.md).

Consultez d’autres articles relatifs aux machines virtuelles avec SQL Server à la page [Vue d’ensemble de SQL Server sur les machines virtuelles Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Si vous avez des questions sur les machines virtuelles SQL Server, consultez le [Forum aux Questions](frequently-asked-questions-faq.md).
