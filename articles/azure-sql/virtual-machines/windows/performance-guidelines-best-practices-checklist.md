---
title: 'Check-list : Bonnes pratiques et recommandations pour les performances'
description: Fournit une check-list rapide qui vous permet de passer en revue les bonnes pratiques et recommandations pour optimiser les performances de votre serveur SQL Server dans une instance de SQL Server sur les machines virtuelles Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: mathoma
ms.custom: contperf-fy21q3
ms.reviewer: jroth
ms.openlocfilehash: 0b88884576a47db871c78b874104d4973ee9ba9a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572213"
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

- Utilisez des tailles de machine virtuelle offrant au moins 4 processeurs virtuels, par exemple les séries [Standard_M8-4ms](/../../virtual-machines/m-series), [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series) et [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15), ou versions ultérieures. 
- Utilisez des tailles de machine virtuelle à [mémoire optimisée](../../../virtual-machines/sizes-memory.md) pour optimiser les performances des charges de travail SQL Server. 
- Les séries [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md), [M-](../../../virtual-machines/m-series.md) et [Mv2-](../../../virtual-machines/mv2-series.md) offrent le ratio mémoire/vCore nécessaire optimal pour les charges de travail OLTP. Les deux machines virtuelles de la série M offrent le ratio mémoire/vCore nécessaire le plus élevé pour les charges de travail critiques. Elles sont également idéales pour les charges de travail d’entrepôt de données. 
- Choisissez éventuellement un ratio mémoire/vCore plus élevé pour les charges de travail critiques et celles qui sont relatives aux entrepôt de données. 
- Utilisez les images de machine virtuelle de la Place de marché Azure, car les paramètres et les options de stockage de SQL Server sont configurés pour optimiser les performances de SQL Server. 
- Collectez les caractéristiques de performances de la charge de travail cible, et utilisez-les pour déterminer la taille de machine virtuelle appropriée pour votre entreprise.

Pour en savoir plus, consultez les [bonnes pratiques relatives à la taille d’une machine virtuelle](performance-guidelines-best-practices-vm-size.md). 

## <a name="storage"></a>Stockage

Voici une check-list rapide des bonnes pratiques relatives à la configuration du stockage pour l’exécution d’un serveur SQL Server dans une instance de SQL Server sur les machines virtuelles Azure : 

- Supervisez l’application et [déterminez les besoins du stockage en bande passante et en latence](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) pour les fichiers de données, fichiers journaux et fichiers tempdb de SQL Server avant de choisir le type de disque. 
- Pour optimiser les performances du stockage, prévoyez les IOPS non mises en cache les plus élevées disponibles, et utilisez la mise en cache des données en tant que fonctionnalité de performance pour les lectures de données, tout en évitant les [plafonnements/limitations applicables à la machine virtuelle et aux disques](../../../virtual-machines/premium-storage-performance.md#throttling).
- Placez les fichiers de données, les fichiers journaux et les fichiers tempdb sur des lecteurs distincts.
    - Pour le lecteur de données, utilisez uniquement des [disques premium P30 et P40](../../../virtual-machines/disks-types.md#premium-ssd) afin de garantir la disponibilité de la prise en charge du cache
    - Pour le lecteur de journaux, prévoyez une capacité suffisante et testez les performances par rapport aux coûts quand vous évaluez les [disques premium P30 - P80](../../../virtual-machines/disks-types.md#premium-ssd).
      - Si le stockage nécessite une latence inférieure à la milliseconde, utilisez des [disques Ultra Azure](../../../virtual-machines/disks-types.md#ultra-disk) pour le journal des transactions. 
      - Pour les déploiements de machines virtuelles de la série M, utilisez l’[accélérateur d’écriture](../../../virtual-machines/how-to-enable-write-accelerator.md) à la place des disques Ultra Azure.
    - Placez [tempdb](/sql/relational-databases/databases/tempdb-database) sur le lecteur `D:\` SSD éphémère local pour la plupart des charges de travail SQL Server, après avoir choisi la taille de machine virtuelle optimale. 
      - Si la capacité du lecteur local n’est pas suffisante pour tempdb, effectuez un redimensionnement de la machine virtuelle en augmentant sa taille. Pour plus d’informations, consultez [Stratégies de mise en cache des fichiers de données](performance-guidelines-best-practices-storage.md#data-file-caching-policies).
- Agrégez plusieurs disques de données Azure à l’aide des [espaces de stockage](/windows-server/storage/storage-spaces/overview) pour augmenter la bande passante d’E/S jusqu’aux limites d’IOPS et de débit de la machine virtuelle cible.
- Définissez la [mise en cache de l’hôte](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) en lecture seule pour les disques de fichiers de données.
- Ne définissez aucune [mise en cache](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) de l’hôte pour les disques de fichiers journaux.
    - N’activez pas la mise en cache en lecture/écriture sur les disques qui contiennent des fichiers SQL Server. 
    - Arrêtez toujours le service SQL Server avant de changer les paramètres de cache de votre disque.
- Pour les charges de travail de développement et test, utilisez le service Stockage Standard. Il n’est pas recommandé d’utiliser les disques HDD/SDD Standard pour les charges de travail de production.
- Le [bursting de disque basé sur les crédits](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) doit être envisagé uniquement pour les charges de travail dev/test et les systèmes départementaux plus petits.
- Provisionnez le compte de stockage dans la même région que la machine virtuelle SQL Server. 
- Désactivez le stockage géoredondant Azure (géoréplication), et utilisez le stockage LRS (stockage localement redondant) pour le compte de stockage.
- Formatez votre disque de données afin d’utiliser une taille d’unité d’allocation de 64 Ko pour tous les fichiers de données placés sur un lecteur autre que le lecteur `D:\` temporaire (dont la valeur par défaut est de 4 Ko). Les machines virtuelles SQL Server déployées via la Place de marché Azure sont fournies avec des disques de données formatés selon une taille d’unité d’allocation et un entrelacement pour le pool de stockage de 64 Ko. 

Pour en savoir plus, consultez les [bonnes pratiques relatives au stockage](performance-guidelines-best-practices-storage.md). 


## <a name="azure--sql-feature-specific"></a>Spécificités relatives aux fonctionnalités Azure et SQL

Voici une check-list rapide des bonnes pratiques relatives aux configurations SQL Server et Azure pour l’exécution d’un serveur SQL Server dans une instance de SQL Server sur les machines virtuelles Azure : 

- Inscrivez votre machine virtuelle SQL Server auprès de l’[extension d’agent SQL IaaS](sql-agent-extension-manually-register-single-vm.md) pour accéder à un certain nombre d’[avantages liés aux fonctionnalités](sql-server-iaas-agent-extension-automate-management.md#feature-benefits). 
- Activez la compression des pages de base de données.
- Activer l’initialisation de fichiers instantanée pour les fichiers de données.
- Limiter la croissance automatique de la base de données.
- Désactiver la réduction automatique de la base de données.
- Déplacer toutes les bases de données vers des disques de données, y compris les bases de données système.
- Déplacer les répertoires des journaux d’erreurs et des fichiers de trace SQL Server vers des disques de données.
- Configurer les emplacements par défaut des fichiers de sauvegarde et de base de données.
- [Activez les pages verrouillées en mémoire](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows).
- Évaluer et appliquer les [dernières mises à jour cumulatives](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server) pour la version installée de SQL Server.
- Sauvegarder directement vers le stockage Blob Azure.
- Utiliser plusieurs fichiers [tempdb](/sql/relational-databases/databases/tempdb-database#optimizing-tempdb-performance-in-sql-server), 1 fichier par cœur, jusqu’à 8 fichiers au maximum.



## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez les autres articles de cette série :
- [Taille de la machine virtuelle](performance-guidelines-best-practices-vm-size.md)
- [Stockage](performance-guidelines-best-practices-storage.md)
- [Collecter une base de référence](performance-guidelines-best-practices-collect-baseline.md)

Pour connaître les meilleures pratiques en matière de sécurité, consultez [Considérations relatives à la sécurité de SQL Server sur les machines virtuelles Azure](security-considerations-best-practices.md).

Consultez d’autres articles relatifs aux machines virtuelles avec SQL Server à la page [Vue d’ensemble de SQL Server sur les machines virtuelles Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Si vous avez des questions sur les machines virtuelles SQL Server, consultez le [Forum aux Questions](frequently-asked-questions-faq.md).
