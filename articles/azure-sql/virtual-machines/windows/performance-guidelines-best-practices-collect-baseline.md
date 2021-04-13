---
title: 'Collecter une ligne de base : Meilleures pratiques et instructions relatives aux performances'
description: Fournit la procédure de collecte d’une ligne de base des performances pour optimiser les performances de votre SQL Server sur une machine virtuelle Azure.
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
ms.date: 03/25/2021
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b1909d5fd5e3c02f104c73acb515740cb6ff65f6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572212"
---
# <a name="collect-baseline-performance-best-practices-for-sql-server-on-azure-vm"></a>Collecter une ligne de base : Meilleures pratiques relatives aux performances de SQL Server sur une machine virtuelle Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Cet article fournit des informations pour collecter une ligne de base des performances sous la forme d’une série de meilleures pratiques et d’instructions pour optimiser les performances de votre SQL Server sur des machines virtuelles Azure.

Il existe généralement un compromis entre l’optimisation des coûts et l’optimisation des performances. Cette série sur les meilleures pratiques relatives aux performances est axée sur l’obtention des *meilleures* performances pour SQL Server sur les machines virtuelles Azure. Si votre charge de travail est moindre, vous n’aurez peut-être pas besoin de toutes les optimisations recommandées. Tenez compte de vos besoins de performances, des coûts et des modèles de charges de travail lors de l’évaluation de ces recommandations.

## <a name="overview"></a>Vue d’ensemble

Pour une approche prescriptive, recueillez les compteurs de performances à l’aide de PerfMon/LogMan, et capturez les statistiques d’attente de SQL Server pour mieux comprendre les pressions générales et les goulots d’étranglement potentiels de l’environnement source. 

Commencez par collecter le processeur, la mémoire, les [IOPS](../../../virtual-machines/premium-storage-performance.md#iops), le [débit](../../../virtual-machines/premium-storage-performance.md#throughput) et la [latence](../../../virtual-machines/premium-storage-performance.md#latency) de la charge de travail source aux heures de pointe en suivant la [liste de contrôle des performances de l’application](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist). 

Recueillez des données pendant les heures de pointe, telles que les charges de travail d’une journée de travail ordinaire, mais aussi d’autres processus de charge élevée, tels que le traitement de fin de journée et les charges de travail ETL effectuées pendant le weekend. Envisagez d’augmenter l’échelle de vos ressources pour des charges de travail anormalement lourdes, telles qu’un traitement de fin de trimestre, puis de réduire l’échelle une fois la charge de travail accomplie. 

Utilisez l’analyse des performances pour sélectionner la [taille de machine virtuelle](../../../virtual-machines/sizes-memory.md) pouvant adapter son échelle aux exigences de performances de votre charge de travail.


## <a name="storage"></a>Stockage

Les performances de SQL Server dépendent fortement du sous-système d’E/S et les performances de stockage sont mesurées par IOPS et débit. Si votre base de données ne tient pas dans la mémoire physique, SQL Server place en permanence des pages de base de données dans et hors du pool de mémoires tampons. Les fichiers de données pour SQL Server doivent être traités différemment. L’accès aux fichiers journaux est séquentiel, sauf quand une transaction doit être restaurée à l’emplacement où des fichiers de données, dont la base de données tempdb, font l’objet d’un accès aléatoire. Si vous disposez d’un sous-système d’E/S lent, vos utilisateurs peuvent rencontrer des problèmes de performances, tels que des temps de réponse lents et des tâches qui ne se terminent pas en raison de délais d’expiration. 

Les machines virtuelles de la Place de marché Azure ont des fichiers journaux sur un disque physique séparé des fichiers de données par défaut. Le nombre et la taille des fichiers de données de la base de données tempdbB sont conformes aux meilleures pratiques et ciblent le lecteur éphémère `D:\`. 

Les compteurs PerfMon suivants peuvent vous aider à valider le débit d’E/S que requiert votre SQL Server : 
* **\LogicalDisk\Disk Reads/Sec** (IOPS de lecture)
* **\LogicalDisk\Disk Writes/Sec** (IOPS d’écriture) 
* **\LogicalDisk\Disk Read Bytes/Sec** (débit de lecture requis pour les fichiers de données, de journal et de base de données tempdb)
* **\LogicalDisk\Disk Write Bytes/Sec** (débit d’écriture requis pour les fichiers de données, de journal et de base de données tempdb)

À l’aide des exigences d’IOPS et de débit aux niveaux de pointe, évaluez les tailles de machine virtuelle correspondant à la capacité de vos mesures. 

Si votre charge de travail nécessite 20 000 IOPS de lecture et 10 000 IOPS d’écriture, vous pouvez choisir le modèle E16s_v3 (jusqu’à 32 000 IOPS mises en cache et 25 600 non mises en cache) ou le modèle M16_s (jusqu’à 20 000 IOPS mises en cache et 10 000 non mises en cache) avec 2 disques P30 agrégés par bandes à l’aide d’espaces de stockage. 

Veillez à bien identifier les besoins de la charge de travail en termes de débit et d’IOPS, car les machines virtuelles ont des limites d’échelle différentes pour les IOPS et le débit.

## <a name="memory"></a>Mémoire

Suivez la mémoire externe que le système d’exploitation utilise, ainsi que la mémoire que SQL Server utilise en interne. L’identification de la pression sur chaque composant vous aidera à dimensionner les machines virtuelles et à identifier des opportunités de réglage. 

Les compteurs PerfMon suivants peuvent vous aider à valider l’intégrité de la mémoire d’une machine virtuelle SQL Server : 
* [\Memory\Available MBytes](/azure/monitoring/infrastructure-health/vmhealth-windows/winserver-memory-availmbytes)
* [\SQLServer:Memory Manager\Target Server Memory (Ko)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Memory Manager\Total Server Memory (Ko)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Buffer Manager\Lazy writes/sec](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Buffer Manager\Page life expectancy](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

## <a name="compute"></a>Compute

Le calcul dans Azure est géré autrement que dans l’environnement local. Les serveurs locaux sont conçus pour durer plusieurs années sans mise à niveau en raison de la surcharge de gestion et du coût d’acquisition de nouveau matériel. La virtualisation atténue certains de ces problèmes mais les applications sont optimisées pour tirer le meilleur parti du matériel sous-jacent, ce qui signifie que toute modification significative de l’utilisation des ressources requiert un rééquilibrage de l’ensemble de l’environnement physique. 

Ce n’est pas un problème dans Azure où une nouvelle machine virtuelle sur une série différente de matériel, voire dans une région différente, est facile à obtenir. 

Dans Azure, vous souhaitez exploiter le plus possible les ressources des machines virtuelles. Par conséquent, les machines virtuelles Azure doivent être configurées pour maintenir l’utilisation moyenne du processeur la plus élevée possible sans que cela ait une incidence sur la charge de travail. 

Les compteurs PerfMon suivants peuvent vous aider à valider l’intégrité de calcul d’une machine virtuelle SQL Server :
* **\Processor Information(_Total)\% Temps processeur**
* **\Process(sqlservr)\% Temps processeur**

> [!NOTE] 
> Dans l’idéal, essayez d’atteindre une utilisation à 80 % de votre capacité de calcul, avec des pics supérieurs à 90 %, mais sans atteindre 100 % pendant une période prolongée. Fondamentalement, vous souhaitez seulement approvisionner la capacité de calcul dont l’application a besoin, puis planifier l’augmentation ou la diminution d’échelle en fonction des besoins de l’entreprise. 


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez les autres articles de cette série :
- [Liste de vérification rapide](performance-guidelines-best-practices-checklist.md)
- [Taille de la machine virtuelle](performance-guidelines-best-practices-vm-size.md)
- [Stockage](performance-guidelines-best-practices-storage.md)


Pour connaître les meilleures pratiques en matière de sécurité, consultez [Considérations relatives à la sécurité de SQL Server sur les machines virtuelles Azure](security-considerations-best-practices.md).

Consultez d’autres articles relatifs aux machines virtuelles avec SQL Server à la page [Vue d’ensemble de SQL Server sur les machines virtuelles Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Si vous avez des questions sur les machines virtuelles SQL Server, consultez le [Forum aux Questions](frequently-asked-questions-faq.md).
