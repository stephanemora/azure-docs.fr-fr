---
title: 'Taille de machine virtuelle : Meilleures pratiques et recommandations relatives aux performances'
description: Fournit les meilleures pratiques et les recommandations relatives à la taille des machines virtuelles pour optimiser les performances de votre instance de SQL Server sur les machines virtuelles Azure.
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
ms.date: 03/25/2021
ms.author: dpless
ms.reviewer: jroth
ms.openlocfilehash: 9427ae1b9bd68f63df40d24122cc13b5460fbc27
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572243"
---
# <a name="vm-size-performance-best-practices-for-sql-server-on-azure-vms"></a>Taille de machine virtuelle : Meilleures pratiques relatives aux performances de SQL Server sur les machines virtuelles Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Cet article fournit les meilleures pratiques et les recommandations relatives à la taille des machines virtuelles pour optimiser les performances de votre instance de SQL Server sur les machines virtuelles Azure.

Il existe généralement un compromis entre l’optimisation des coûts et l’optimisation des performances. Cette série de bonnes pratiques sur les performances vise à obtenir les *meilleures* performances possibles pour SQL Server sur les machines virtuelles Azure. Si votre charge de travail est moindre, vous n’aurez peut-être pas besoin de toutes les optimisations recommandées. Tenez compte de vos besoins de performances, des coûts et des modèles de charges de travail lors de l’évaluation de ces recommandations.


## <a name="checklist"></a>Check-list

Consultez la liste de contrôle suivante pour obtenir une vue d’ensemble des meilleures pratiques en matière de taille des machines virtuelles présentées plus en détail dans le reste de l’article : 

- Utilisez des tailles de machine virtuelle offrant au moins 4 processeurs virtuels, telles que les séries [Standard_M8-4ms](/../../virtual-machines/m-series), [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series) ou [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15), ou versions ultérieures. 
- Utilisez des tailles de machine virtuelle [à mémoire optimisée](../../../virtual-machines/sizes-memory.md) pour optimiser les performances des charges de travail SQL Server. 
- Les séries [DSv2 11 à 15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md), [M-](../../../virtual-machines/m-series.md) et [Mv2-](../../../virtual-machines/mv2-series.md) offrent le ratio optimal mémoire/vCore requis pour les charges de travail OLTP. Les machines virtuelles des deux séries M offrent le plus haut ratio mémoire/vCore requis pour les charges de travail critiques. Elles sont également idéales pour les charges de travail d’entrepôt de données. 
- Envisagez un ratio mémoire/vCore plus élevé pour les charges de travail critiques et celles d’entrepôt de données. 
- Tirez parti des images du marketplace Machines virtuelles Azure, car les paramètres et les options de stockage de SQL Server sont configurés pour optimiser les performances de SQL Server. 
- Collectez les caractéristiques de performances de la charge de travail cible, et utilisez-les pour déterminer la taille de machine virtuelle appropriée pour votre entreprise.

Pour comparer la liste de contrôle de la taille des machines virtuelles avec les autres, consultez [Liste de contrôle des meilleures pratiques relatives aux performances](performance-guidelines-best-practices-checklist.md). 

## <a name="overview"></a>Vue d’ensemble

Lorsque vous créez une instance de SQL Server sur machine virtuelle Azure, réfléchissez soigneusement au type de charge de travail nécessaire. Si vous migrez un environnement existant, [collectez une ligne de base des performances](performance-guidelines-best-practices-collect-baseline.md) pour déterminer les exigences de votre instance de SQL Server sur machine virtuelle Azure. S’il s’agit d’une nouvelle machine virtuelle, créez votre machine virtuelle SQL Server en fonction des exigences de votre fournisseur. 

Si vous créez une machine virtuelle SQL Server avec une nouvelle application conçue pour le cloud, vous pouvez facilement dimensionner votre machine virtuelle SQL Server à mesure que vos besoins en matière de données et d’utilisation évoluent.
Démarrez les environnements de développement avec des machines la série D de niveau inférieur, de la série B ou la série Av-2, et développez votre environnement au fil du temps. 

Utilisez les images de la place de marché de machines virtuelles SQL Server avec la configuration de stockage dans le portail. Cela facilitera la création des pools de stockage nécessaires pour obtenir la taille, les IOPS et le débit dont vos charges de travail ont besoin. Il est important de choisir des machines virtuelles SQL Server qui prennent en charge le Stockage Premium et la mise en cache du Stockage Premium. Pour en savoir plus, consultez l’article sur le [stockage](performance-guidelines-best-practices-storage.md). 

La configuration minimale recommandée pour un environnement OLTP de production est 4 vCore, 32 Go de mémoire et un ratio mémoire/vCore de 8. Pour de nouveaux environnements, commencez avec 4 machines vCore et augmentez l’échelle jusqu’à 8, 16, 32 vCore ou davantage lorsque vos besoins en matière de données et de calcul évoluent. Pour le débit OLTP, ciblez des machines virtuelles SQL Server offrant une capacité de 5000 IOPS pour chaque vCore. 

L’entrepôt de données et les environnements stratégiques de SQL Server doivent souvent évoluer au-delà du ratio mémoire/vCore de 8. Pour des environnements de taille moyenne, vous pouvez choisir un ratio mémoire/vCore de 16 et, pour les environnements d’entrepôt de données plus volumineux, un ratio mémoire/vCore de 32. 

Les environnements d’entrepôt de données SQL Server tirent souvent bénéfice du traitement parallèle de machines de plus grande taille. C’est la raison pour laquelle les séries M et Mv2 sont des options solides pour des environnements d’entrepôt de données volumineux.

Utilisez la configuration de processeur virtuel et de mémoire de votre machine source comme ligne de base pour la migration d’une base de données SQL Server locale existante vers SQL Server sur machines virtuelles Azure. Intégrez votre licence principale à Azure pour tirer parti d’[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) et réaliser des économies sur les coûts de licences SQL Server.

## <a name="memory-optimized"></a>Mémoire optimisée

Les [tailles de machine virtuelle à mémoire optimisée](../../../virtual-machines/sizes-memory.md) constituent une cible primaire pour les machines virtuelles SQL Server et le choix recommandé par Microsoft. Les machines virtuelles à mémoire optimisée offrent des options plus solides en matière de ratio mémoire/processeur et de cache de taille moyenne à grande. 

### <a name="m-mv2-and-mdsv2-series"></a>Séries M, Mv2 et Mdsv2

La [série M](../../../virtual-machines/m-series.md) offre des nombres de vCore et une quantité de mémoire appropriés pour certaines des charges de travail SQL Server les plus volumineuses.  

La [série Mv2](../../../virtual-machines/mv2-series.md) offre les nombres de vCore et la quantité de mémoire les plus élevés. Elle est recommandée pour les charges de travail stratégiques et d’entrepôt de données. Les instances de la série Mv2 sont des tailles de machine virtuelle à mémoire optimisée fournissant des performances de calcul inégalées pour prendre en charge des bases de données et des charges de travail en mémoire volumineuses, avec un ratio mémoire/processeur élevé parfait pour les serveurs de base de données relationnelle, les caches volumineux et l’analytique en mémoire.

Par exemple, les machines [Standard_M64ms](../../../virtual-machines/m-series.md) offrent un ratio mémoire/vCore de 28.

La [série Mdsv2 à mémoire moyenne](../../..//virtual-machines/msv2-mdsv2-series.md) est une nouvelle série M qui est actuellement en [préversion](https://aka.ms/Mv2MedMemoryPreview) et offre une gamme de machines virtuelles Azure de niveau M avec une offre de mémoire intermédiaire. Ces machines sont parfaitement adaptées aux charges de travail SQL Server avec la prise en charge d’un ratio mémoire/vCore de 10 à 30.

Parmi les caractéristiques des séries M et Mv2 attrayantes pour les performances de SQL Server, citons la prise en charge du [Stockage Premium](../../../virtual-machines/premium-storage-performance.md) et de la [mise en cache du Stockage Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching), la prise en charge des [disques Ultra](../../../virtual-machines/disks-enable-ultra-ssd.md) et l’[accélération d’écriture](../../../virtual-machines/how-to-enable-write-accelerator.md).

### <a name="edsv4-series"></a>Série Edsv4

La [série Edsv4](../../../virtual-machines/edv4-edsv4-series.md) est conçue pour les applications gourmandes en mémoire. Ces machines virtuelles disposent d’une grande capacité de stockage local sur SSD, une forte IOPS de disque local et jusqu’à 504 Gio de RAM. La plupart de ces machines virtuelles disposent de 8 Gio de mémoire par vCore, ce qui est idéal pour des charges de travail SQL Server standard. 

Il existe une nouvelle machine virtuelle dans ce groupe avec la [Standard_E80ids_v4](../../../virtual-machines/edv4-edsv4-series.md) qui offre 80 vCores, 504 Go de mémoire et un ratio mémoire/vCore de 6. Cette machine virtuelle est remarquable, car elle est [isolée](../../../virtual-machines/isolation.md), ce qui signifie qu’il est garanti qu’elle est la seule machine virtuelle en cours d’exécution sur l’hôte et qu’elle est donc isolée des autres charges de travail du client. Elle a un ratio mémoire/vCore inférieur à ce qui est recommandé pour SQL Server, elle ne doit donc être utilisée que si l’isolation est requise.

Les machines virtuelles de la série Edsv4 prennent en charge le [Stockage Premium](../../../virtual-machines/premium-storage-performance.md) et la [mise en cache du Stockage Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching).

### <a name="dsv2-series-11-15"></a>Série DSv2 11-15

La [série DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) offre les mêmes configurations de mémoire et disque que la série D précédente. Cette série offre un ratio mémoire/processeur cohérent de 7 sur toutes les machines virtuelles. Il s’agit de la plus petite des séries à mémoire optimisée et elle constitue une option bon marché pour les charges de travail SQL Server d’entrée de gamme.

La [série DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) prend en charge le [Stockage Premium](../../../virtual-machines/premium-storage-performance.md) et la [mise en cache du Stockage Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching), ce qui est fortement recommandé pour des performances optimales.

## <a name="general-purpose"></a>Usage général

Les [tailles de machines virtuelles à usage général](../../../virtual-machines/sizes-general.md) sont conçues pour offrir des ratios mémoire/vCore équilibrés pour des charges de travail d’entrée de gamme plus petites, telles que le développement et les tests, les serveurs web et les serveurs de base de données plus petits. 

En raison des moindres ratios mémoire/vCore des machines virtuelles à usage général, il est important de surveiller attentivement les compteurs de performances basés sur la mémoire pour s’assurer que SQL Server est en mesure d’obtenir la mémoire cache tampon dont il a besoin. Pour plus d’informations, consultez la [ligne de base des performances de la mémoire](performance-guidelines-best-practices-collect-baseline.md#memory). 

Étant donné que la recommandation de départ pour les charges de travail de production est un ratio mémoire/vCore de 8, la configuration minimale recommandée pour une machine virtuelle à usage général exécutant SQL Server est de 32 Go de mémoire et 4 processeurs virtuels. 

### <a name="ddsv4-series"></a>Série Ddsv4

La [série Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) offre une juste combinaison de processeurs virtuels, de mémoire et de disque temporaire, mais avec un moindre ratio mémoire/vCore. 

Les machines virtuelles de la série Ddsv4 incluent un stockage local à latence plus faible et plus rapide.

Ces machines sont idéales pour des déploiements côte à côte de SQL et d’applications, qui requièrent un accès rapide au stockage temporaire et aux bases de données relationnelles de services. L’ensemble des machines virtuelles de cette série offrent un ratio mémoire/vCore standard de 4. 

C’est pourquoi, il est recommandé d’utiliser comme machine virtuelle de démarrage dans cette série le modèle D8ds_v4 qui offre 8 vCore et 32 Go de mémoire. La plus grande machine est le modèle D64ds_v4, avec 64 vCore et 256 Go de mémoire.

Les machines virtuelles de la série [Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) prennent en charge le [Stockage Premium](../../../virtual-machines/premium-storage-performance.md) et la [mise en cache du Stockage Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE]
> La [série Ddsv4](../../../virtual-machines/ddv4-ddsv4-series.md) n’offre pas le ratio mémoire/vCore de 8 recommandé pour les charges de travail SQL Server. Par conséquent, l’utilisation de ces machines virtuelles ne doit être envisagée que pour des charges de travail d’application et de développement relativement modestes.

### <a name="b-series"></a>Série B

Les tailles de machine virtuelle de la [série B burstable](../../../virtual-machines/sizes-b-series-burstable.md) sont idéales pour des charges de travail nécessitant pas de performances soutenues, par exemple, pour une preuve de concept ou de très petits serveurs d’applications et de développement. 

La plupart des machines virtuelles de la [série B burstable](../../../virtual-machines/sizes-b-series-burstable.md) ont un ratio mémoire/vCore de 4. La plus grande de ces machines est le modèle [Standard_B20ms](../../../virtual-machines/sizes-b-series-burstable.md) doté de 20 vCore et de 80 Go de mémoire.

Cette série est unique, car elle permet aux applications d’opérer par **rafales** pendant les heures de bureau avec des crédits burstables variables en fonction de la taille de la machine. 

Une fois ces crédits épuisés, la machine virtuelle revient aux performances de base.

L’avantage de la série B a trait aux économies de calcul que vous pouvez réaliser par rapport aux tailles de machines virtuelles d’autres séries, en particulier si vous avez besoin d’une puissance de traitement modérée tout au long de la journée.

Cette série prend en charge le [Stockage Premium](../../../virtual-machines/premium-storage-performance.md), mais **ne prend pas en charge** la [mise en cache du Stockage Premium](../../../virtual-machines/premium-storage-performance.md#disk-caching).

> [!NOTE] 
> La [série B burstable](../../../virtual-machines/sizes-b-series-burstable.md) n’offre pas le ratio mémoire/vCore de 8 recommandé pour les charges de travail SQL Server. Par conséquent, n’envisagez d’utiliser ces machines virtuelles que pour des applications, serveurs web et charges de travail de développement de taille modeste.

### <a name="av2-series"></a>Série Av2

Les machines virtuelles de la [série AV2](../../../virtual-machines/av2-series.md) sont idéales pour des charges de travail d’entrée de gamme, par exemple, le développement et les tests, les serveurs web à faible trafic, les bases de données d’applications petites à moyennes et les preuves de concept.

Seuls les modèles [Standard_A2m_v2](../../../virtual-machines/av2-series.md) (2 vCore et 16 Go de mémoire), [Standard_A4m_v2](../../../virtual-machines/av2-series.md) (4 vCore et 32 Go de mémoire) et [Standard_A8m_v2](../../../virtual-machines/av2-series.md) (8 vCore et 64 Go de mémoire) offrent un bon ratio mémoire/vCore de 8. 

Ces machines virtuelles constituent de bonnes options pour les machines SQL Server de développement et de test de taille modeste. 

Le modèle [Standard_A8m_v2](../../../virtual-machines/av2-series.md) à 8 vCore peut également être une bonne option pour des serveurs web et d’applications de petite taille.

> [!NOTE] 
> La série AV2 ne prenant pas en charge le Stockage Premium, elle n’est pas recommandée pour des charges de travail SQL Server de production, même avec les machines virtuelles offrant un ratio mémoire/vCore de 8.

## <a name="storage-optimized"></a>Optimisé pour le stockage

Les [tailles de machine virtuelle à stockage optimisé](../../../virtual-machines/sizes-storage.md) sont destinées à des cas d’usage spécifiques. Ces machines virtuelles sont spécialement conçues avec un débit de disque et des E/S optimisés. 

### <a name="lsv2-series"></a>Série Lsv2

La [série Lsv2](../../../virtual-machines/lsv2-series.md) offre un débit élevé, une faible latence et un stockage NVMe local. Les machines virtuelles de la série Lsv2 sont optimisées pour utiliser le disque local du nœud attaché directement à la machine virtuelle au lieu d’utiliser des disques de données durables. 

Ces machines virtuelles constituent des options solides pour des charges de travail de Big Data, d’entrepôt de données, de création de rapports et d’extraction, transformation et chargement (ETL). Le débit élevé et l’IOPS du stockage NVMe local sont appropriés pour traiter des fichiers qui seront chargés dans votre base de données et d’autres scénarios dans lesquels les données peuvent être recréées à partir du système source ou d’autres référentiels tels que Stockage Blob Azure ou Azure Data Lake. Les machines virtuelles de la [série Lsv2](../../../virtual-machines/lsv2-series.md) peuvent également augmenter en rafale leurs performances de disque pendant jusqu’à 30 minutes.

Ces machines virtuelles offrent une taille de 8 à 80 processeurs virtuels, avec 8 Gio de mémoire par processeur et 1,92 To de disque SSD NVMe par groupe de 8 processeurs virtuels. Cela signifie que la plus grande machine virtuelle de cette série, le modèle [L80s_v2](../../../virtual-machines/lsv2-series.md), est dotée de 80 processeurs virtuels, de 640 Gio de mémoire et de 10 x 1,92 To de stockage NVMe.  Toutes ces machines virtuelles offrent un ratio mémoire/vCore cohérent de 8.

Le stockage NVMe est éphémère, ce qui signifie que les données seront perdues sur ces disques si vous libérez votre machine virtuelle ou si celle-ci est déplacée vers un autre hôte pour la réparation de service.

Les séries Lsv2 et Ls prennent en charge le [Stockage Premium](../../../virtual-machines/premium-storage-performance.md), mais pas la mise en cache du Stockage Premium. La création d’un cache local pour augmenter les IOPS n’est pas prise en charge. 

> [!WARNING]
> Le stockage de vos fichiers de données sur le stockage NVMe éphémère peut entraîner une perte de données lors de la désallocation de la machine virtuelle. 

## <a name="constrained-vcores"></a>VCore contraints

Les charges de travail SQL Server hautes performances nécessitent souvent davantage de mémoire, d’E/S et de débit, sans compter le nombre plus élevé de vCores. 

La plupart des charges de travail OLTP sont des bases de données d’application pilotées par de grands nombres de transactions plus petites. Avec des charges de travail OLTP, seule une petite quantité des données sont lues ou modifiées, mais les volumes des transactions pilotées par l’utilisateur sont beaucoup plus importants. Il est important d’avoir la mémoire SQL Server disponible pour mettre en cache des plans, stocker des données récemment consultées à des fins de performances et de veiller à ce que les lectures physiques puissent être lues rapidement en mémoire. 

Ces environnements OLTP ont besoin de davantage de mémoire, d’un stockage rapide et de la bande passante d’E/S nécessaire pour une exécution optimale. 

Pour maintenir ce niveau de performances sans exposer des coûts de licences SQL Server plus élevés, Azure propose des tailles de machine virtuelle avec des [processeurs virtuels en nombre limité](../../../virtual-machines/constrained-vcpu.md). 

Cela permet de contrôler les coûts de licences en réduisant les vCore disponibles tout en conservant les mémoire, stockage et bande passante d’E/S de la machine virtuelle parente.

Le nombre de processeurs virtuels peut être contraint à la moitié ou au quart de la taille de la machine virtuelle d’origine. En réduisant le nombre de vCores disponibles pour la machine virtuelle, les ratios mémoire/vCore sont plus élevés, mais le coût de calcul reste le même.

Pour faciliter leur identification, ces nouvelles tailles de machine virtuelle sont assorties d’un suffixe indiquant le nombre de processeurs virtuels actifs. 

Par exemple, le modèle [M64-32ms](../../../virtual-machines/constrained-vcpu.md) requiert des licences uniquement pour 32 vCores SQL Server avec la mémoire, les E/S et le débit du modèle [M64ms](../../../virtual-machines/m-series.md), et le modèle [M64-16ms](../../../virtual-machines/constrained-vcpu.md) requiert des licences uniquement pour 16 vCores.  Bien que le modèle [M64-16ms](../../../virtual-machines/constrained-vcpu.md) soit proposé au quart du coût de licence SQL Server du modèle M64ms, le coût de calcul de la machine virtuelle est le même.

> [!NOTE] 
> - Les charges de travail d’entrepôt de données de taille moyenne à grande peuvent toujours tirer bénéfice des [machines virtuelles vCore contraintes](../../../virtual-machines/constrained-vcpu.md), mais les charges de travail d’entrepôt de données sont généralement caractérisées par un moindre nombre d’utilisateurs et de processus traitant de grandes quantités de données via des plans de requête s’exécutant en parallèle. 
> - Le coût de calcul, qui inclut la licence du système d’exploitation, reste identique à celui de la machine virtuelle parente. 



## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez les autres articles de cette série :
- [Liste de vérification rapide](performance-guidelines-best-practices-checklist.md)
- [Stockage](performance-guidelines-best-practices-storage.md)
- [Collecter une ligne de base](performance-guidelines-best-practices-collect-baseline.md)

Pour connaître les meilleures pratiques en matière de sécurité, consultez [Considérations relatives à la sécurité de SQL Server sur les machines virtuelles Azure](security-considerations-best-practices.md).

Consultez d’autres articles relatifs aux machines virtuelles avec SQL Server à la page [Vue d’ensemble de SQL Server sur les machines virtuelles Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Si vous avez des questions sur les machines virtuelles SQL Server, consultez le [Forum aux Questions](frequently-asked-questions-faq.md).
