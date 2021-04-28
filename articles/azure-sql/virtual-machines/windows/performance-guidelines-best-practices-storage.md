---
title: 'Stockage : Meilleures pratiques et recommandations relatives aux performances'
description: Fournit les meilleures pratiques et les recommandations relatives au stockage pour optimiser les performances de votre SQL Server sur une machine virtuelle Azure.
services: virtual-machines-windows
documentationcenter: na
author: dplessMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 23e006c637285ad484e98b23b2a9f506156f519c
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389721"
---
# <a name="storage-performance-best-practices-for-sql-server-on-azure-vms"></a>Stockage : Meilleures pratiques sur les performances de SQL Server sur les machines virtuelles Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Cet article fournit les meilleures pratiques et les recommandations relatives au stockage pour optimiser les performances de votre SQL Server sur des machines virtuelles Azure.

Il existe généralement un compromis entre l’optimisation des coûts et l’optimisation des performances. Cette série de bonnes pratiques sur les performances vise à obtenir les *meilleures* performances possibles pour SQL Server sur les machines virtuelles Azure. Si votre charge de travail est moindre, vous n’aurez peut-être pas besoin de toutes les optimisations recommandées. Tenez compte de vos besoins de performances, des coûts et des modèles de charges de travail lors de l’évaluation de ces recommandations.

Pour plus d’informations, consultez les autres articles de cette série : [Liste de contrôle des performances](performance-guidelines-best-practices-checklist.md), [Taille de la machine virtuelle](performance-guidelines-best-practices-vm-size.md) et [Collecter une ligne de base](performance-guidelines-best-practices-collect-baseline.md). 

## <a name="checklist"></a>Check-list

Consultez la liste de contrôle suivante pour obtenir une vue d’ensemble des meilleures pratiques de stockage présentées plus en détail dans le reste de l’article : 

- Surveillez l’application et [déterminez les besoins en bande passante et de latence de stockage](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements) pour les fichiers de données, de journal et tempdb SQL Server avant de choisir le type de disque. 
- Pour optimiser les performances de stockage, planifiez les IOPS non mises en cache les plus élevées disponibles et utilisez la mise en cache des données en tant que fonctionnalité de performances pour les lectures de données tout en évitant l'[encapsulation des machines virtuelles et des disques](../../../virtual-machines/premium-storage-performance.md#throttling).
- Placez les fichiers de données, de journal et tempdb sur des lecteurs distincts.
    - Pour le lecteur de données, utiliser uniquement les [disques Premium P30 et P40](../../../virtual-machines/disks-types.md#premium-ssd) pour garantir la disponibilité de la prise en charge du cache
    - Pour le plan du lecteur de journal pour les performances de capacité et de test par rapport au coût lors de l’évaluation des [disques Premium P30 - P80](../../../virtual-machines/disks-types.md#premium-ssd)
      - Si la latence de stockage en millisecondes est requise, utilisez des [Disques Ultra Azure](../../../virtual-machines/disks-types.md#ultra-disk) pour le journal des transactions. 
      - Pour les déploiements de machines virtuelles de la série M, envisagez l'[accélérateur d’écriture](../../../virtual-machines/how-to-enable-write-accelerator.md) à l’aide des Disques Ultra Azure.
    - Placez [tempdb](/sql/relational-databases/databases/tempdb-database) sur le lecteur SSD éphémère local `D:\` pour la plupart des charges de travail SQL Server après avoir choisi la taille de machine virtuelle optimale. 
      - Si la capacité du lecteur local n’est pas suffisante pour tempdb, pensez à redimensionner la machine virtuelle. Consultez les [Stratégies de mise en cache de fichiers de données](#data-file-caching-policies) pour plus d’informations.
- Entrelacez plusieurs disques de données Azure à l’aide d'[espaces de stockage](/windows-server/storage/storage-spaces/overview) pour augmenter la bande passante d’E/S jusqu’aux limites de débit et d’IOPS de la machine virtuelle cible.
- Définissez la [mise en cache de l’hôte](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) en lecture seule pour les disques de fichiers de données.
- Définissez la [mise en cache de l’hôte](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits) sur aucun pour les disques de fichiers journaux.
    - N’activez pas la mise en cache en lecture/écriture sur les disques qui contiennent des fichiers SQL Server. 
    - Arrêtez toujours le service SQL Server avant de modifier les paramètres de cache de votre disque.
- Pour les charges de travail de développement et de test, et l’archivage de sauvegarde à long terme, pensez à utiliser le stockage standard. Il n’est pas recommandé d’utiliser HDD/SDD Standard pour les charges de travail de production.
- Le [bursting de disque basé sur les crédits](../../../virtual-machines/disk-bursting.md#credit-based-bursting) (P1-P20) ne doit être pris en compte que pour les charges de travail de dev/test et les systèmes départementaux plus petits.
- Configurez le compte de stockage dans la même région que la machine virtuelle SQL Azure. 
- Désactivez le stockage géoredondant Azure (géoréplication) et utilisez LRS (stockage local redondant) sur le compte de stockage.
- Formatez votre disque de données afin d’utiliser une taille de bloc (taille d’unité d’allocation) de 64 Ko pour tous les fichiers de données placés sur un lecteur autre que le lecteur `D:\` temporaire (dont la valeur par défaut est de 4 Ko). Les machines virtuelles SQL Server déployées via la Place de marché Azure sont fournies avec des disques de données formatés selon une taille de bloc et un entrelacement pour le pool de stockage de 64 Ko. 

Pour comparer la liste de contrôle de stockage avec les autres, consultez la [Liste de contrôle des meilleures pratiques relatives aux performances](performance-guidelines-best-practices-checklist.md). 

## <a name="overview"></a>Vue d’ensemble

Pour trouver la configuration la plus efficace pour les charges de travail SQL Server sur une machine virtuelle Azure, commencez par [mesurer les performances de stockage de votre application métier](performance-guidelines-best-practices-collect-baseline.md#storage). Une fois les exigences de stockage connues, sélectionnez une machine virtuelle qui prend en charge les IOPS et le débit nécessaires avec le ratio mémoire-vCore approprié. 

Choisissez une taille de machine virtuelle avec suffisamment d’extensibilité de stockage pour votre charge de travail et un mélange de disques (généralement dans un pool de stockage) qui répondent aux besoins de votre entreprise en matière de capacité et de performances. 

Le type de disque dépend à la fois du type de fichier hébergé sur le disque et de vos exigences de performances maximales.

> [!TIP]
> L’approvisionnement d’une machine virtuelle SQL Server via le Portail Azure vous guide tout au long du processus de configuration du stockage et implémente la plupart des meilleures pratiques de stockage, telles que la création de pools de stockage distincts pour vos fichiers de données et de journaux, le ciblage de tempdb sur le lecteur `D:\`, et l’activation de la stratégie de mise en cache optimale. Pour plus d’informations sur l’approvisionnement et la configuration du stockage, consultez [Configuration du stockage de machines virtuelles SQL](storage-configuration.md). 

## <a name="vm-disk-types"></a>Type de disque de machine virtuelle

Vous avez le choix entre plusieurs niveaux de performances pour vos disques. Les types de disques managés disponibles en tant que stockage sous-jacent (répertoriés par des fonctionnalités de performances accrues) sont les lecteurs de disque dur standard (HDD), les disques SSD standard, les disques SSD (Solid-State Drives) Premium et les disques Ultra. 

Les performances du disque augmentent avec la capacité, regroupées par [étiquettes de disque Premium](../../../virtual-machines/disks-types.md#premium-ssd) telles que P1 avec 4 Gio d’espace et 120 IOPS au P80 avec 32 Tio de stockage et 20 000 IOPS. Le stockage Premium prend en charge un cache de stockage qui permet d’améliorer les performances de lecture et d’écriture pour certaines charges de travail. Pour plus d’informations, consultez [Vue d’ensemble de la fonctionnalité Disques managés](../../../virtual-machines/managed-disks-overview.md). 

Il existe également trois [types de disques](../../../virtual-machines/managed-disks-overview.md#disk-roles) principaux à prendre en compte pour votre SQL Server sur une machine virtuelle Azure : un disque de système d’exploitation, un disque temporaire et vos disques de données. Choisissez soigneusement ce qui est stocké sur le lecteur du système d’exploitation `(C:\)` et le lecteur temporaire éphémère `(D:\)`. 

### <a name="operating-system-disk"></a>Disque de système d’exploitation

Un disque de système d’exploitation est un disque dur virtuel (VHD) qui peut être amorcé et monté comme version d’exécution d’un système d’exploitation. Il est désigné par la lettre de lecteur `C:\`. Lorsque vous créez une machine virtuelle Azure, la plateforme attache au moins un disque à la machine virtuelle pour le disque de système d’exploitation. Le lecteur `C:\` est l’emplacement par défaut pour les installations d’applications et la configuration de fichiers. 

Pour les environnements SQL Server de production, n’utilisez pas le disque de système d’exploitation pour les fichiers de données, les fichiers journaux et les journaux d’erreurs. 

### <a name="temporary-disk"></a>Disque temporaire

De nombreuses machines virtuelles Azure contiennent un autre type de disque appelé disque temporaire (portant le nom de lecteur `D:\`). Selon la série et la taille de la machine virtuelle, la capacité de ce disque varie. Le disque temporaire est éphémère, ce qui signifie que le stockage sur disque est recréé (comme dans, il est libéré et réalloué), lorsque la machine virtuelle est redémarrée ou déplacée vers un autre hôte (par exemple, pour la [réparation de service](/troubleshoot/azure/virtual-machines/understand-vm-reboot)). 

Le lecteur de stockage temporaire n’est pas conservé dans le stockage distant et ne doit donc pas stocker les fichiers de base de données utilisateur, les fichiers journaux des transactions ou tout ce qui doit être préservé. 

Placez tempdb sur le lecteur SSD temporaire local `D:\` pour les charges de travail SQL Server, sauf si la consommation du cache local est un problème. Si vous utilisez une machine virtuelle qui [n’a pas de disque temporaire](../../../virtual-machines/azure-vms-no-temp-disk.md), il est recommandé de placer la base de données tempdb sur son propre disque isolé ou pool de stockage dont la mise en cache est définie sur lecture seule. Pour plus d’informations, consultez les [stratégies de mise en cache des données tempdb](performance-guidelines-best-practices-storage.md#data-file-caching-policies).

### <a name="data-disks"></a>Disques de données

Les disques de données sont des disques de stockage à distance qui sont souvent créés dans des [pools de stockage](/windows-server/storage/storage-spaces/overview) afin de dépasser la capacité et les performances qu’un seul disque peut fournir à la machine virtuelle.

Attachez le nombre minimal de disques qui satisfait aux besoins en IOPS, en débit et en capacité de votre charge de travail. Ne dépassez pas le nombre maximal de disques de données de la plus petite machine virtuelle que vous envisagez de redimensionner.

Placez les fichiers de données et les fichiers journaux sur les disques de données approvisionnés en fonction de vos exigences de performances. 

Formatez votre disque de données de façon à utiliser une taille d’unité d’allocation de 64 Ko pour tous les fichiers de données placés sur un lecteur autre que le lecteur temporaire `D:\` (dont la taille par défaut est de 4 Ko). Les machines virtuelles SQL Server déployées via la Place de marché Azure sont fournies avec des disques de données formatés avec une taille d’unité d’allocation et un entrelacement pour le pool de stockage défini sur 64 Ko. 

## <a name="premium-disks"></a>Disques Premium

Utilisez des disques SSD Premium pour les fichiers de données et les fichiers journaux pour les charges de travail de production SQL Server. Les IOPS et la bande passante SSD Premium varient en fonction de la [taille et du type de disque](../../../virtual-machines/disks-types.md). 

Pour les charges de travail de production, utilisez les disques P30 et/ou P40 pour les fichiers de données SQL Server pour garantir la prise en charge de la mise en cache et utilisez le P30 jusqu’à P80 pour les fichiers journaux des transactions SQL Server.  Pour optimiser le coût total de possession, commencez par des P30 (5 000 IOPS/200 Mbits/s) pour les fichiers de données et les fichiers journaux et choisissez uniquement des capacités supérieures lorsque vous avez besoin de contrôler le nombre de disques de la machine virtuelle.

Pour les charges de travail OLTP, faites correspondre les IOPS cibles par disque (ou pool de stockage) à vos besoins de performances à l’aide des charges de travail aux heures de pointe et des compteurs de performances `Disk Reads/sec` + `Disk Writes/sec`. Pour les charges de travail d’entrepôt de données et de création de rapports, faites correspondre le débit cible à l’aide des charges de travail aux heures de pointe et `Disk Read Bytes/sec` + `Disk Write Bytes/sec`. 

Utilisez les espaces de stockage pour atteindre des performances optimales, configurer deux pools, une pour le ou les fichiers journaux et l’autre pour les fichiers de données. Si vous n’utilisez pas l’entrelacement de disques, utilisez deux disques SSD premium mappés sur des lecteurs distincts, un pour le fichier journal et l’autre pour les données.

Les [IOPS approvisionnées et le débit](../../../virtual-machines/disks-types.md#premium-ssd) par disque qui sont utilisés dans le cadre de votre pool de stockage. Les fonctionnalités combinées d’IOPS et de débit des disques sont la capacité maximale pour les limites de débit de la machine virtuelle.

La meilleure pratique consiste à utiliser le moins de disques possibles tout en répondant aux conditions minimales requises pour les IOPS (et le débit) et la capacité. Toutefois, l’équilibre entre prix et performances a tendance à être mieux adapté à un grand nombre de petits disques plutôt qu’à un petit nombre de disques de grande taille.

### <a name="scaling-premium-disks"></a>Mise à l’échelle des disques Premium

Lorsqu’un disque managé Azure est déployé pour la première fois, le niveau de performance de ce disque est basé sur la taille du disque approvisionné. Désignez le niveau de performance au moment du déploiement ou modifiez-le ultérieurement, sans changer de taille du disque. Si la demande augmente, vous pouvez augmenter le niveau de performance pour répondre aux besoins de votre entreprise. 

La modification du niveau de performance permet aux administrateurs de préparer le disque et de répondre à une demande plus importante sans dépendre du [bursting de disque](../../../virtual-machines/disk-bursting.md#credit-based-bursting). 

Utilisez les performances supérieures aussi longtemps que nécessaire, pour lesquelles la facturation est conçue pour atteindre le niveau de performance de stockage. Mettez à niveau le niveau pour qu’il corresponde aux exigences en matière de performances sans augmenter la capacité. Revenez au niveau d’origine lorsque les performances supplémentaires ne sont plus nécessaires.

Cette expansion économique et temporaire des performances est un cas d’utilisation fort pour les événements ciblés, tels que les achats, les tests de performances, les événements de formation et d’autres fenêtres courtes, où des performances supérieures sont nécessaires uniquement pour une courte période. 

Pour plus d’informations, consultez [Niveaux de performances pour les disques managés](../../../virtual-machines/disks-change-performance.md). 

## <a name="azure-ultra-disk"></a>Disque Ultra Azure

Si vous avez besoin de temps de réponse de demi-milliseconde avec une latence réduite, envisagez d’utiliser un [Disk Ultra Azure](../../../virtual-machines/disks-types.md#ultra-disk) pour le lecteur de journalisation SQL Server, ou même le lecteur de données pour les applications extrêmement sensibles à la latence d’E/S. 

Le disque Ultra peut être configuré lorsque la capacité et les IOPS peuvent se mettre à l’échelle de façon indépendante. Avec le disque Ultra, les administrateurs peuvent approvisionner un disque avec les exigences de capacité, d’IOPS et de débit en fonction des besoins de l’application. 

Le disque Ultra n’est pas pris en charge sur toutes les séries de machines virtuelles et présente d’autres limitations, telles que la disponibilité des régions, la redondance et la prise en charge de Sauvegarde Azure. Pour plus d’informations, consultez [Utilisation de disques Ultra Azure](../../../virtual-machines/disks-enable-ultra-ssd.md) pour obtenir une liste complète des limitations. 

## <a name="standard-hdds-and-ssds"></a>Disques HDD standard et SSD

Les [disques HDD standard](../../../virtual-machines/disks-types.md#standard-hdd) et les disques SSD possèdent différents temps de latence et une bande passante variable. Ils sont recommandés uniquement pour les charges de travail de dev/test. Les charges de production doivent utiliser des disques SSD premium. Si vous utilisez un disque SSD Standard (scénarios dev/test), nous vous recommandons d’ajouter le nombre maximal de disques de données pris en charge par la [taille de votre machine virtuelle](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json) et d’utiliser l’entrelacement de disques avec les espaces de stockage pour des performances optimales.

## <a name="caching"></a>Mise en cache

Les machines virtuelles qui prennent en charge la mise en cache de stockage Premium peuvent bénéficier d’une fonctionnalité supplémentaire appelée Azure BlobCache ou la mise en cache d’hôte pour étendre les fonctionnalités d’IOPS et de débit d’une machine virtuelle. Les machines virtuelles activées pour le stockage Premium et la mise en cache du stockage Premium disposent de deux limites de bande passante de stockage différentes qui peuvent être utilisées ensemble pour améliorer les performances de stockage.

Le débit d’IOPS et Mbits/s sans mise en cache est compté sur les limites de débit de disque non mis en cache d’une machine virtuelle. Les limites maximales mises en cache fournissent une mémoire tampon supplémentaire pour les lectures, ce qui permet de gérer la croissance et les pics inattendus.

Activez la mise en cache Premium dès que l’option est prise en charge pour améliorer de manière significative les performances des lectures sur le lecteur de données sans coût supplémentaire. 

Les lectures et les écritures dans le BlobCache Azure (IOPS et débit mis en cache) ne sont pas comptabilisées dans les limites d’IOPS et de débit non mises en cache de la machine virtuelle.

> [!NOTE]
> La mise en cache du disque n’est pas prise en charge pour les disques de 4 Tio et supérieurs (P50 et supérieur). Si plusieurs disques sont attachés à votre machine virtuelle, chaque disque d’une taille inférieure à 4 Tio prend en charge la mise en cache. Pour plus d'informations, consultez [Mise en cache du disque](../../../virtual-machines/premium-storage-performance.md#disk-caching). 

### <a name="uncached-throughput"></a>Débit non mis en cache

Les IOPS et le débit maximum du disque non mis en cache sont la limite maximale de stockage à distance que la machine virtuelle est en mesure de gérer. Cette limite est définie sur la machine virtuelle et n’est pas une limite du stockage sur disque sous-jacent. Cette limite s’applique uniquement à l’E/S sur les lecteurs de données attachés à distance à la machine virtuelle, et non à l’E/S locale sur le lecteur temporaire (lecteur `D:\`) ou le lecteur de système d’exploitation.

La quantité d’IOPS et de débit non mis en cache qui est disponible pour une machine virtuelle peut être vérifiée dans la documentation de votre machine virtuelle.

Par exemple, la documentation de la [série M](../../../virtual-machines/m-series.md) indique que le débit maximal non mis en cache pour la machine virtuelle Standard_M8ms est de 5 000 IOPS et de 125 Mbits/s de débit de disque non mis en cache. 

![Capture d’écran montrant la documentation sur le débit de disque non mis en cache de la série M.](./media/performance-guidelines-best-practices/m-series-table.png)

De même, vous pouvez voir que la machine virtuelle Standard_M32ts prend en charge 20 000 IOPS de disque sans mise en cache et un débit de disque de 500 Mbits/s sans mise en cache. Cette limite est régie par le niveau de la machine virtuelle, quel que soit le stockage sur disque Premium sous-jacent.

Pour plus d’informations, consultez les [limitations avec et sans mise en cache](../../../virtual-machines/linux/disk-performance-linux.md#virtual-machine-uncached-vs-cached-limits).


### <a name="cached-and-temp-storage-throughput"></a>Débit de stockage temporaire et mis en cache max

Le débit maximal mis en cache et temporaire est une limite distincte de la limite de débit non mise en cache sur la machine virtuelle. Le BlobCache Azure se compose d’une combinaison de la mémoire d’accès aléatoire de l’hôte de machine virtuelle et du disque SSD attaché localement. Le lecteur temporaire (lecteur `D:\`) de la machine virtuelle est également hébergé sur ce disque SSD local.

La limite du débit maximal de stockage mis en cache et temporaire régit les E/S sur le lecteur local temporaire (lecteur `D:\`) et Azure BlobCache **uniquement si** la mise en cache de l’hôte est activée. 

Lorsque la mise en cache est activée sur le stockage Premium, les machines virtuelles peuvent évoluer au-delà des limites des IOPS et des limites de débit des machines virtuelles non mises en cache du stockage à distance.  

Seules certaines machines virtuelles prennent en charge le stockage Premium et la mise en cache du stockage Premium (qui doivent être vérifiées dans la documentation de la machine virtuelle). Par exemple, la documentation de la [série M](../../../virtual-machines/m-series.md) indique que le stockage Premium et la mise en cache du stockage Premium sont pris en charge : 

![Capture d’écran montrant la prise en charge du stockage Premium de la série M.](./media/performance-guidelines-best-practices/m-series-table-premium-support.png)

Les limites du cache varient en fonction de la taille de la machine virtuelle. Par exemple, la machine virtuelle Standard_M8ms prend en charge 10 000 IOPS de cache de disque et 1 000 Mbits/s de débit de cache de disque avec une taille de cache totale de 793 Gio. De même, la machine virtuelle Standard_M32ts prend en charge 40 000 IOPS de cache de disque et 400 Mbits/s de débit de cache de disque avec une taille de cache totale de 3 174 Gio. 

![Capture d’écran montrant la documentation sur le débit de disque mis en cache de la série M.](./media/performance-guidelines-best-practices/m-series-table-cached-temp.png)

Vous pouvez activer manuellement la mise en cache de l’hôte sur une machine virtuelle existante. Arrêtez toutes les charges de travail d’application et les services SQL Server avant toute modification apportée à la stratégie de mise en cache de votre machine virtuelle. La modification des paramètres du cache de la machine virtuelle entraîne le détachement et la réattachement du disque cible après l’application des paramètres.

### <a name="data-file-caching-policies"></a>Stratégies de mise en cache des fichiers de données

Votre stratégie de mise en cache de stockage varie selon le type de fichiers de données SQL Server hébergés sur le lecteur. 

Le tableau suivant fournit un résumé des stratégies de mise en cache recommandées en fonction du type de données SQL Server : 

|Disque SQL Server |Recommandation |
|---------|---------|
| **Disque de données** | Activez la mise en cache `Read-only` pour les disques qui hébergent des fichiers de données SQL Server. <br/> Les lectures à partir du cache sont plus rapides que les lectures non mises en cache du disque de données. <br/> Les IOPS et le débit non mis en cache, plus les IOPS et le débit mis en cache, entraînent des performances totales disponibles à partir de la machine virtuelle dans les limites des machines virtuelles, mais les performances réelles varient en fonction de la capacité de la charge de travail à utiliser le cache (taux de correspondance dans le cache). <br/>|
|**Disque du journal des transactions**|Définissez la stratégie de mise en cache sur `None` pour les disques qui hébergent le journal des transactions.  Il n’existe aucun avantage en matière de performances pour activer la mise en cache pour le disque du journal des transactions. Effectivement, si la mise en cache `Read-only` ou `Read/Write` est activée sur le lecteur de journal, cela peut nuire aux performances des écritures sur le lecteur et réduire la quantité de cache disponible pour les lectures sur le lecteur de données.  |
|**Disque du système d’exploitation** | La stratégie de mise en cache par défaut peut être `Read-only` ou `Read/write` pour le lecteur du système d’exploitation. <br/> Il n’est pas recommandé de modifier le niveau de mise en cache du lecteur de système d’exploitation.  |
| **tempdb**| Si tempdb ne peut pas être placée sur le lecteur éphémère `D:\` pour des raisons de capacité, vous pouvez soit redimensionner la machine virtuelle pour obtenir un lecteur éphémère plus grand, soit placer tempdb sur un lecteur de données distinct avec la mise en cache `Read-only` configurée. <br/> Le cache de la machine virtuelle et le lecteur éphémère utilisent tous deux le disque SSD local. Vous devez donc garder cela à l’esprit lorsque le dimensionnement des E/S de tempdb est compté par rapport aux limites d’IOPS et de débit des machines virtuelles mis en cache en cas d’hébergement sur le lecteur éphémère.| 
| | | 


Pour plus d’informations, consultez [Mise en cache du disque](../../../virtual-machines/premium-storage-performance.md#disk-caching). 


## <a name="disk-striping"></a>Entrelacement de disques

Analysez le débit et la bande passante requis pour vos fichiers de données SQL pour déterminer le nombre de disques de données, y compris le fichier journal et tempdb. Les limites de débit et de bande passante varient en fonction de la taille de machine virtuelle. Pour en savoir plus, consultez [Taille de la machine virtuelle](../../../virtual-machines/sizes.md)

Ajoutez des disques de données et utilisez l’entrelacement de disques pour plus de débit. Par exemple, une application qui a besoin de 12 000 IOPS et de d’un débit de 180 Mo/s peut utiliser trois disques P30 répartis pour fournir un débit de 15 000 IOPS et 600 Mo/s. 

Pour configurer l’entrelacement de disques, consultez [entrelacement de disques](storage-configuration.md#disk-striping). 

## <a name="disk-capping"></a>Encapsulation de disque 

Il existe des limites de débit au niveau du disque et de la machine virtuelle. Les limites d’IOPS par machine virtuelle et par disque sont différentes et indépendantes les unes des autres.

Les applications qui consomment des ressources au-delà de ces limites sont limitées (également appelées « encapsulées »). Sélectionnez une machine virtuelle et une taille de disque dans un entrelacement de disques qui répond aux exigences de l’application et ne conviendra pas aux limitations de l’encapsulation. Pour résoudre l’encapsulation, utilisez la mise en cache ou paramétrez l’application afin d’exiger un débit inférieur.

Par exemple, une application qui nécessite 12 000 IOPS et 180 Mo/s peut : 
- Utiliser la machine virtuelle [Standard_M32ms](../../../virtual-machines/m-series.md) dont le débit de disque maximal non mis en cache est de 20 000 IOPS et 500 Mbits/s.
- Entrelacez trois disques P30 pour fournir un débit de 15 000 IOPS et 600 Mo/s.
- Utilisez une machine virtuelle [Standard_M16ms](../../../virtual-machines/m-series.md) et utilisez la mise en cache de l’hôte pour utiliser le cache local au-delà du débit consommateur. 

Les machines virtuelles configurées pour monter en puissance pendant les périodes de forte utilisation doivent approvisionner le stockage avec un nombre d’IOPS et un débit suffisants pour prendre en charge la taille de machine virtuelle maximale tout en conservant le nombre total de disques inférieurs ou égaux au nombre maximal pris en charge par la plus petite référence SKU de machine virtuelle à utiliser.

Pour plus d’informations sur les limitations de l’encapsulation de disque et l’utilisation de la mise en cache pour éviter l’encapsulation, consultez [Encapsulation d’E/S des disques](../../../virtual-machines/disks-performance.md).

> [!NOTE] 
> L’encapsulation de certains disques peut toujours aboutir à des performances satisfaisantes pour les utilisateurs. Ajustez et gérez les charges de travail plutôt que de les redimensionner sur une machine virtuelle plus grande pour équilibrer la gestion des coûts et des performances de l’entreprise. 


## <a name="write-acceleration"></a>Accélération d’écriture

L’accélération d’écriture est une fonctionnalité de disque qui est uniquement disponible pour les machines virtuelles de la [série M](https://docs.microsoft.com/azure/virtual-machines/m-series). L’objectif de l’accélération d’écriture est d’améliorer la latence d’E/S des écritures sur le stockage Premium Azure lorsque vous avez besoin d’une latence d’E/S à un chiffre en raison de charges de travail OLTP critiques ou d’environnements d’entrepôts de données de grande envergure. 

Utilisez l’accélération d’écriture pour améliorer la latence d’écriture sur le lecteur qui héberge les fichiers journaux. N’utilisez pas l’accélération d’écriture pour les fichiers de données SQL Server. 

Les disques avec Accélérateur d’écriture partagent la même limite d’IOPS que la machine virtuelle. Les disques attachés ne peuvent pas dépasser la limite d’IOPS d’Accélérateur d’écriture pour une machine virtuelle.  

Le tableau suivant présente le nombre de disques de données et d’IOPS pris en charge par machine virtuelle : 

| Référence de la machine virtuelle  | Nombre de disques avec Accélérateur d’écriture  | IOPS de disque avec Accélérateur d’écriture par machine virtuelle  |
|---|---|---|
| M416ms_v2, M416s_v2  | 16  | 20000  |
| M128ms, M128s  | 16  | 20000  |
| M208ms_v2, M208s_v2  | 8  | 10000  |
| M64ms, M64ls, M64s  |  8 | 10000 |
| M32ms, M32ls, M32ts, M32s  | 4  | 5 000  |
| M16ms, M16s  | 2 | 2 500 |
| M8ms, M8s  | 1 | 1250 |

L’utilisation de l’accélération d’écriture présente un certain nombre de restrictions. Pour plus d’informations, consultez [Restrictions lors de l’utilisation de Accélérateur d’écriture](../../../virtual-machines/how-to-enable-write-accelerator.md#restrictions-when-using-write-accelerator).


### <a name="comparing-to-azure-ultra-disk"></a>Comparaison avec le disque Ultra Azure

La plus grande différence entre l’accélération d’écriture et les disques Ultra Azure est que l’accélération d’écriture est une fonctionnalité de machine virtuelle uniquement disponible pour la série M et les disques Ultra Azure est une option de stockage. L’accélération d’écriture est un cache optimisé en écriture avec ses propres limitations en fonction de la taille de la machine virtuelle. Les disques Ultra Azure sont une option de stockage sur disque à faible latence pour les machines virtuelles Azure. 

Si possible, utilisez l’accélération d’écriture sur les disques Ultra pour le disque du journal des transactions. Pour les machines virtuelles qui ne prennent pas en charge l’accélération d’écriture, mais qui nécessitent une faible latence dans le journal des transactions, utilisez les disques Ultra Azure. 

## <a name="monitor-storage-performance"></a>Surveiller les performances de stockage

Pour évaluer les besoins de stockage et déterminer le bon fonctionnement du stockage, vous devez comprendre ce qu’il faut mesurer, ainsi que la signification de ces indicateurs. 

Les [IOPS (Entrée/Sortie par seconde)](../../../virtual-machines/premium-storage-performance.md#iops) sont le nombre de requêtes que l’application effectue pour le stockage par seconde. Mesurez les IOPS à l’aide des compteurs de l’analyseur de performances `Disk Reads/sec` et `Disk Writes/sec`. Les applications [OLTP (traitement transactionnel en ligne)](/azure/architecture/data-guide/relational-data/online-transaction-processing) doivent effectuer des IOPS supérieures pour obtenir des performances optimales. Les applications telles que les systèmes de traitement des paiements, les achats en ligne et les systèmes de point de vente au détail sont des exemples d’applications OLTP.

Le [débit](../../../virtual-machines/premium-storage-performance.md#throughput) est le volume de données envoyées au stockage sous-jacent, souvent mesuré en mégaoctets par seconde. Mesurez le débit avec les compteurs de l’analyseur de performances `Disk Read Bytes/sec` et `Disk Write Bytes/sec`. L'[entreposage de données](/azure/architecture/data-guide/relational-data/data-warehousing) est optimisé pour maximiser le débit sur les IOPS. Les applications telles que les magasins de données pour l’analyse, la création de rapports, les flux de travail ETL et d’autres cibles décisionnelles sont des exemples d’applications d’entreposage de données.

Les tailles des unités d’E/S influencent les IOPS et les capacités de débit, car les tailles d’E/S plus petites fournissent des IOPS supérieures et des tailles d’E/S plus élevées. SQL Server choisit automatiquement la taille optimale des E/S. Pour plus d’informations à ce sujet, consultez [Optimiser les IOPS, le débit et la latence de vos applications](../../../virtual-machines/premium-storage-performance.md#optimize-iops-throughput-and-latency-at-a-glance). 

Il existe des métriques Azure Monitor spécifiques qui sont très utiles pour la découverte de l’encapsulation au niveau de la machine virtuelle et du disque, ainsi que de la consommation et de l’intégrité du cache AzureBlob. Pour identifier les compteurs de clés à ajouter à votre solution de surveillance et au tableau de bord du Portail Azure, consultez [Métriques d’utilisation du stockage](../../../virtual-machines/disks-metrics.md#storage-io-utilization-metrics). 

> [!NOTE]
> Azure Monitor ne fournit actuellement pas de métriques au niveau du disque pour le lecteur temporaire éphémère `(D:\)`. Le pourcentage de consommation d’IOPS mis en cache et le pourcentage de bande passante mise en cache pour les machines virtuelles reflètent les IOPS et le débit du lecteur temporaire éphémère `(D:\)` et de la mise en cache de l’hôte.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les meilleures pratiques en matière de performances, consultez les autres articles de cette série :
- [Liste de vérification rapide](performance-guidelines-best-practices-checklist.md)
- [Taille de la machine virtuelle](performance-guidelines-best-practices-vm-size.md)
- [Collecter une ligne de base](performance-guidelines-best-practices-collect-baseline.md)

Pour connaître les meilleures pratiques en matière de sécurité, consultez [Considérations relatives à la sécurité de SQL Server sur les machines virtuelles Azure](security-considerations-best-practices.md).

Le détail des tests de performances de SQL Server sur les machines virtuelles Azure avec les benchmarks TPC-E et TPC_C est consultable sur le billet de blog relatif à l’[optimisation des performances OLTP](https://techcommunity.microsoft.com/t5/sql-server/optimize-oltp-performance-with-sql-server-on-azure-vm/ba-p/916794).

Consultez d’autres articles relatifs aux machines virtuelles avec SQL Server à la page [Vue d’ensemble de SQL Server sur les machines virtuelles Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Si vous avez des questions sur les machines virtuelles SQL Server, consultez le [Forum aux Questions](frequently-asked-questions-faq.md).
