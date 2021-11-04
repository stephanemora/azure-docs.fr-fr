---
title: Limites des ressources
titleSuffix: Azure SQL Managed Instance
description: Cet article fournit une vue d’ensemble des limites de ressources pour Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.custom: references_regions, ignite-fall-2021
ms.devlang: ''
ms.topic: reference
author: vladai78
ms.author: vladiv
ms.reviewer: mathoma, vladiv, sachinp, wiassaf
ms.date: 10/18/2021
ms.openlocfilehash: 5014c0b22d14f43baa99ad9eac3eb27ed6526c5b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473563"
---
# <a name="overview-of-azure-sql-managed-instance-resource-limits"></a>Vue d’ensemble des limites de ressources Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Cet article fournit une vue d’ensemble des caractéristiques techniques et des limites des ressources pour Azure SQL Managed Instance, ainsi que des informations sur la façon de demander une augmentation de ces limites.

> [!NOTE]
> Pour connaître les différences entre les fonctionnalités prises en charge et les instructions T-SQL, consultez [Différences de fonctionnalités](../database/features-comparison.md) et [Prise en charge des instructions T-SQL](transact-sql-tsql-differences-sql-server.md). Pour connaître les différences générales entre les niveaux de service pour Azure SQL Database et SQL Managed Instance, consultez [Comparaison des niveaux de service](../database/service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Caractéristiques de la génération du matériel

SQL Managed Instance a des caractéristiques et des limites de ressources qui dépendent de l’infrastructure et de l’architecture sous-jacentes. SQL Managed Instance peut être déployé sur plusieurs générations de matériel. 

> [!NOTE]
> La génération de matériel Gen5 a été renommée **série Standard (Gen5)** , et deux nouvelles générations de matériel ont été introduites en préversion limitée : la **série Premium** et la **série Premium à mémoire optimisée**.

Pour plus d’informations, consultez les [détails relatifs aux générations de matériel précédentes](#previous-generation-hardware) plus loin dans cet article. 

Les générations du matériel ont différentes caractéristiques, qui sont décrites dans le tableau suivant :

|    | **Série Standard (Gen5)** | **Série Premium (préversion)** | **Série Premium à mémoire optimisée (préversion)** | 
|:-- |:-- |:-- |:-- |
| **UC** |  Processeurs Intel&reg; E5-2673 v4 (Broadwell) 2,3 GHz, Intel&reg; SP-8160 (Skylake) et Intel&reg; 8272CL (Cascade Lake) 2,5 GHz | Processeurs Intel&reg; 8370C (Ice Lake) 2,8 GHz | Processeurs Intel&reg; 8370C (Ice Lake) 2,8 GHz |
| **Nombre de vCore** <BR>vCore = 1 LP (hyperthread) | 4-80 vCores | 4-80 vCores | 4-64 vCores |
| **Mémoire maximale (rapport mémoire/vCore)** | 5,1 Go par vCore<br/>Ajoutez plus de vCores pour obtenir davantage de mémoire. | 7 Go par vCore | 13,6 Go par vCore |
| **Mémoire OLTP maximale en mémoire** |  Limite de l’instance : 0,8 à 1,65 Go par vCore | Limite de l’instance : 1,1 à 2,3 Go par vCore | Limite de l’instance : 2,2 à 4,5 Go par vCore |
| **Stockage réservé d’instance maximal**\* | **Usage général :** jusqu’à 16 To<br/> **Critique pour l’entreprise :** jusqu’à 4 To | **Usage général :** jusqu’à 16 To<br/> **Critique pour l’entreprise :** jusqu’à 5,5 To | **Usage général :** jusqu’à 16 To <br/> **Critique pour l’entreprise :** jusqu’à 16 To |

\* Dépend du [nombre de vCores](#service-tier-characteristics).

### <a name="regional-support-for-premium-series-hardware-generations-preview"></a>Prise en charge régionale des générations de matériel de la série Premium (préversion)

Les générations de matériel de la série Premium (préversion publique) ne sont actuellement prises en charge que dans les régions spécifiques suivantes :


| Région | **Série Premium** | **Série Premium à mémoire optimisée** | 
|:--- |:--- |:--- |
| USA Centre | Oui |  | 
| USA Est  | Oui |  | 
| USA Est 2 | Oui | Oui | 
| Europe Nord |  | Oui | 
| Europe Ouest | Oui | Oui | 
| USA Ouest |  | Oui |  
| USA Ouest 2 | Oui | Oui | 


### <a name="in-memory-oltp-available-space"></a>Espace disponible OLTP en mémoire 

La quantité d’espace OLTP en mémoire au niveau de service [Critique pour l’entreprise](../database/service-tier-business-critical.md) dépend du nombre de vCores et de la génération de matériel. Le tableau suivant liste les limites de mémoire utilisable pour des objets OLTP en mémoire.

| **vCores** | **Série Standard (Gen5)** | **Série Premium** | **Série Premium à mémoire optimisée** | 
|:--- |:--- |:--- |:--- |
| 4 vCores    | 3,14 Go | 4,39 Go | 8,79 Go | 
| 8 vCores    | 6,28 Go | 8,79 Go | 22,06 Go |  
| 16 vCores | 15,77 Go | 22,06 Go | 57,58 Go |
| 24 vCores | 25,25 Go | 35,34 Go | 93,09 Go |
| 32 vCores | 37,94 Go | 53,09 Go | 128,61 Go |
| 40 vCores | 52,23 Go | 73,09 Go | 164,13 Go |
| 64 vCores | 99,9 Go | 139,82 Go | 288,61 Go |
| 80 vCores | 131,68 Go| 184,30 Go | N/A |

## <a name="service-tier-characteristics"></a>Caractéristiques du niveau de service

SQL Managed Instance a deux niveaux de service : [Usage général](../database/service-tier-general-purpose.md) et [Critique pour l’entreprise](../database/service-tier-business-critical.md). Ces niveaux offrent des [fonctionnalités différentes](../database/service-tiers-general-purpose-business-critical.md), comme décrit dans le tableau ci-dessous.

> [!Important]
> Le niveau de service Critique pour l’entreprise fournit une copie intégrée supplémentaire de l’instance managée SQL (réplica secondaire) qui peut être utilisée pour les charges de travail en lecture seule. Si vous pouvez séparer les requêtes de lecture-écriture des requêtes en lecture seule/analytique/de création de rapports, vous obtenez deux fois plus de vCores et de mémoire pour le même prix. Le réplica secondaire peut présenter un décalage de quelques secondes par rapport à l’instance principale. Il est donc conçu pour déplacer les charges de travail de création de rapports/analytiques qui n’ont pas besoin de l’état actuel exact des données. Dans le tableau ci-dessous, les **requêtes en lecture seule** sont les requêtes exécutées sur le réplica secondaire.

| **Fonctionnalité** | **Usage général** | **Critique pour l’entreprise** |
| --- | --- | --- |
| Nombre de vCores\* | 4, 8, 16, 24, 32, 40, 64, 80 |  **Série Standard (Gen5)**  : 4, 8, 16, 24, 32, 40, 64, 80 <BR> **Série Premium** : 4, 8, 16, 24, 32, 40, 64, 80 <BR> **Série Premium à mémoire optimisée** : 4, 8, 16, 24, 32, 40, 64<br/>\*Le même nombre de vCores est dédié pour les requêtes en lecture seule. |
| Mémoire maximale | **Série Standard (Gen5)**  : 20,4 Go à 408 Go (5,1 Go/vCore)<BR> **Série Premium** : 28 Go à 560 Go (7 Go/vCore)<BR> **Série Premium à mémoire optimisée** : 54,4 Go à 870,4 Go (13,6 Go/vCore) | **Série Standard (Gen5)**  : 20,4 Go à 408 Go (5,1 Go/vCore) sur chaque réplica<BR> **Série Premium** : 28 Go à 560 Go (7 Go/vCore) sur chaque réplica<BR> **Série Premium à mémoire optimisée** : 54,4 Go à 870,4 Go (13,6 Go/vCore) sur chaque réplica |
| Taille de stockage maximale d’instance (réservée) | - 2 To pour 4 vCores<br/>- 8 To pour 8 vCores<br/>- 16 To pour les autres tailles <BR> | **Série Standard (Gen5)**  : <br/>- 1 To pour 4, 8, 16 vCores<br/>- 2 To pour 24 vCores<br/>- 4 To pour 32, 40, 64, 80 vCores <BR> **Série Premium** : <BR>- 1 To pour 4, 8 vCores<br/>- 2 To pour 16, 24 vCores<br/>- 4 To pour 32 vCores<br/>- 5,5 To pour 40, 64, 80 vCores<br/> **Série Premium à mémoire optimisée** : <BR>- 1 To pour 4, 8 vCores<br/>- 2 To pour 16, 24 vCores<br/>- 4 To pour 32 vCores<br/>- 5,5 To pour 40 vCores<br/>- 16 To pour 64 vCores<br/> |
| Taille de base de données maximale | Jusqu’à la taille d’instance actuellement disponible (en fonction du nombre de vCores). | Jusqu’à la taille d’instance actuellement disponible (en fonction du nombre de vCores). |
| Taille maximale de tempDB | Limitée à 24 Go/vCore (96-1920 Go) et à la taille de stockage d’instance actuellement disponible.<br/>Ajoutez plus de vCores pour obtenir davantage d’espace TempDB.<br/> La taille du fichier journal est limitée à 120 Go.| Jusqu’à la taille de stockage d’instance actuellement disponible. |
| Nombre maximal de bases de données par instance | 100 bases de données utilisateur, sauf si la limite de taille de stockage d’instance a été atteinte. | 100 bases de données utilisateur, sauf si la limite de taille de stockage d’instance a été atteinte. |
| Nombre maximal de fichiers de base de données par instance | Jusqu’à 280, sauf si la limite de taille de stockage d’instance ou d’[espace d’allocation de stockage sur disque Premium Azure](doc-changes-updates-known-issues.md#exceeding-storage-space-with-small-database-files) a été atteinte. | 32 767 fichiers par base de données, sauf si la limite de taille de stockage d’instance a été atteinte. |
| Taille maximale du fichier de données | La taille maximale de chaque fichier de données est de 8 To. Utilisez au moins deux fichiers de données pour les bases de données de plus de 8 To. | Jusqu’à la taille d’instance actuellement disponible (en fonction du nombre de vCores). |
| Taille maximale du fichier journal | Limitée à 2 To et à la taille de stockage d’instance actuellement disponible. | Limitée à 2 To et à la taille de stockage d’instance actuellement disponible. |
| IOPS de données/journal (approximatives) | Jusqu’à 30-40 K IOPS par instance*, 500-7500 par fichier<br/>\*[Augmentez la taille de fichier pour obtenir davantage d’IOPS](#file-io-characteristics-in-general-purpose-tier)| 16 000 à 320 000 (4 000 IOPS/vCore)<br/>Ajoutez plus de vCores pour obtenir de meilleures performances d’E/S. |
| Limite du débit d’écriture du journal (par instance) | 3 Mo/s par vCore<br/>120 Mo/s max. par instance<br/>22-65 Mo/s par base de données (selon la taille du fichier journal)<br/>\*[Augmentez la taille de fichier pour obtenir de meilleures performances d’E/S](#file-io-characteristics-in-general-purpose-tier) | 4 Mo/s par vCore<br/>96 Mo/s max. |
| Débit de données (approximatif) | 100 - 250 Mo/s par fichier<br/>\*[Augmentez la taille de fichier pour obtenir de meilleures performances d’E/S](#file-io-characteristics-in-general-purpose-tier) | Non limité. |
| Latence d’E/S de stockage (approximative) | 5 - 10 ms | 1 - 2 ms |
| OLTP en mémoire | Non pris en charge | Disponible, [la taille dépend du nombre de vCores](#in-memory-oltp-available-space) |
| Nombre maximal de sessions | 30000 | 30000 |
| Nombre maximal d’ouvriers simultanés (demandes) | 105 * nombre de vCores + 800 | 105 * nombre de vCores + 800 |
| [Réplicas en lecture seule](../database/read-scale-out.md) | 0 | 1 (inclus dans le prix) |
| Isolation du calcul | Non pris en charge, car des instances Usage général peuvent partager du matériel physique avec d’autres instances| **Série Standard (Gen5)**  :<br/> Pris en charge pour 40, 64, 80 vCores<BR> **Série Premium** : pris en charge pour 64, 80 vCores <BR> **Série Premium à mémoire optimisée** : pris en charge pour 64 vCores |


Quelques considérations supplémentaires : 

- **La taille de stockage d’instance actuellement disponible** est la différence entre la taille d’instance réservée et l’espace de stockage utilisé.
- Les tailles des données et des fichiers journaux dans les bases de données utilisateur et système sont comprises dans la taille de stockage d’instance qui est comparée à la limite de taille de stockage maximale. Utilisez la vue système [sys.master_files](/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql) pour déterminer l’espace total utilisé par les bases de données. Les journaux d’activité d’erreurs ne sont ni conservés ni compris dans la taille. Les sauvegardes ne sont pas comprises dans la taille de stockage.
- Le débit et les IOPS dans le niveau Usage général dépendent également de la [taille de page](#file-io-characteristics-in-general-purpose-tier) qui n’est pas explicitement limitée par l’instance managée SQL.
  Vous pouvez créer un autre réplica lisible dans une région Azure différente à l’aide de [groupes de basculement automatique](../database/auto-failover-group-configure.md).
- Le nombre maximal d’IOPS d’instance dépend de la disposition des fichiers et de la distribution de la charge de travail. Par exemple, si vous créez sept fichiers de 1 To avec un maximum de 5 000 IOPS et sept petits fichiers (moins de 128 Go) avec 500 IOPS chacun, vous pouvez obtenir 38 500 IOPS par instance (7x5000+7x500) si votre charge de travail peut utiliser tous les fichiers. Notez que certaines IOPS sont également utilisées pour les sauvegardes automatiques.

Vous trouverez plus d’informations sur les [limites des ressources dans les pools SQL Managed Instance dans cet article](instance-pools-overview.md#resource-limitations).

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Caractéristiques d’E/S de fichier avec le niveau Usage général

Avec le niveau de service Usage général, chaque fichier de base de données reçoit des IOPS et un débit dédiés qui dépendent de la taille du fichier. Les fichiers plus volumineux reçoivent plus d’IOPS et de débit. Les caractéristiques d’E/S des fichiers de base de données sont indiquées dans le tableau suivant :

| **Taille du fichier** | **>= 0 et <= 128 Gio** | **> 128 et <= 512 Gio** | **> 0,5 et <= 1 Tio**    | **> 1 et <= 2 Tio**    | **> 2 et <= 4 Tio** | **> 4 et <= 8 Tio** | **> 8 et <= 16 Tio** |
|:--|:--|:--|:--|:--|:--|:--|:--|
| IOPS par fichier       | 500   | 2300              | 5 000  | 7500              | 7500              | 12 500   | |
| Débit par fichier | 100 Mio/s | 150 Mio/s | 200 Mio/s | 250 Mio/s| 250 Mio/s | 480 Mio/s |  |

Si vous constatez une latence élevée des E/S sur un fichier de base de données, ou si vous constatez que les IOPS ou le débit atteignent la limite, vous pouvez améliorer les performances en [augmentant la taille de fichier](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337).

Il existe également une limite au niveau de l’instance pour le débit maximal d’écriture dans le journal (voir ci-dessus pour les valeurs, p. ex., 22 Mo/s) ; ainsi, vous ne pourrez peut-être pas atteindre le débit de fichier maximal dans le fichier journal, car vous atteindrez la limite de débit de l’instance.

## <a name="supported-regions"></a>Régions prises en charge

SQL Managed Instance peut être créé uniquement dans des [régions prises en charge](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Si vous voulez créer une instance managée SQL dans une région qui n’est actuellement pas prise en charge, vous pouvez [envoyer une demande de support par le biais du portail Microsoft Azure](../database/quota-increase-request.md).

## <a name="supported-subscription-types"></a>Types d’abonnements pris en charge

SQL Managed Instance prend actuellement en charge le déploiement uniquement sur les types d’abonnements suivants :

- [Contrat Entreprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Fournisseur de services cloud (CSP)](/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Dev/Test – Paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Abonnements avec crédit Azure mensuel pour les abonnés Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Limitations des ressources régionales

> [!Note]
> Pour obtenir les dernières informations sur la disponibilité des régions pour les abonnements, veuillez d’abord [sélectionner une région](https://aka.ms/sqlcapacity).

Les types d’abonnements pris en charge peuvent contenir un nombre limité de ressources par région. SQL Managed Instance a deux limites par défaut par région Azure (qui peuvent être augmentées à la demande en créant une [demande spéciale de support dans le portail Azure](../database/quota-increase-request.md)) en fonction du type d’abonnement :

- **Limite de sous-réseaux** : nombre maximal de sous-réseaux sur lesquels des instances managées SQL sont déployées dans une seule et même région.
- **Limite d’unités de vCore** : nombre maximal d’unités de vCores qui peuvent être déployées parmi toutes les instances dans une seule région. Un vCore GP utilise une unité de vCore et un vCore BC prend quatre unités de vCore. Le nombre total d’instances n’est pas limité du moment qu’il se trouve dans la limite du nombre d’unités de vCores.

> [!Note]
> Ces limites sont des paramètres par défaut : il ne s’agit pas de limitations techniques. Ces limites peuvent être augmentées en créant une [demande de support spéciale dans le portail Microsoft Azure](../database/quota-increase-request.md) si vous avez besoin de plus d’instances dans la région actuelle. Vous pouvez aussi créer des instances managées SQL dans une autre région Azure sans envoyer de demandes de support.

Le tableau suivant montre les **limites régionales par défaut** pour les types d’abonnement pris en charge (les limites par défaut peuvent être étendues à l’aide de la demande de support décrite ci-dessous) :

|Type d’abonnement| Nombre maximal de sous-réseaux SQL Managed Instance | Nombre maximal d’unités de vCore* |
| :---| :--- | :--- |
|CSP |16 (30 dans certaines régions**)|960 (1 440 dans certaines régions**)|
|EA|16 (30 dans certaines régions**)|960 (1 440 dans certaines régions**)|
|Enterprise Dev/Test|6|320|
|Paiement à l’utilisation|6|320|
|Pay-As-You-Go Dev/Test|6|320|
|Pass Azure|3|64|
|BizSpark|3|64|
|BizSpark Plus|3|64|
|Microsoft Azure Sponsorship|3|64|
|Microsoft Partner Network|3|64|
|Visual Studio Enterprise (MPN)|3|64|
|Visual Studio Enterprise|3|32|
|Visual Studio Enterprise (BizSpark)|3|32|
|Visual Studio Professional|3|32|
|Plateformes MSDN|3|32|

\* Lors de la planification de déploiements, prenez en considération le fait que le niveau de service critique pour l’entreprise (BC) requiert quatre (4) fois plus de capacité vCore que le niveau de service usage général (GP). Exemple : 1 vCore GP = 1 unité vCore et 1 vCore BC = 4 vCore. Pour simplifier votre analyse de la consommation par rapport aux limites par défaut, récapitulez les unités vCore de tous les sous-réseaux de la région où SQL Managed Instance est déployé et comparez les résultats avec les limites d’unités d’instance pour votre type d’abonnement. La limite **Nombre maximal d’unités de vCore** s’applique à chaque abonnement dans une région. Il n’y a pas de limite par sous-réseau individuel sauf que la somme de tous les vCores déployés sur plusieurs sous-réseaux doit être inférieure ou égale à **nombre maximum d’unités vCore**.

\*\* Des limites de sous-réseau et de vCore plus importantes s’appliquent dans les régions suivantes : Australie Est, USA Est, USA Est 2, Europe Nord, USA Centre Sud, Asie Sud-Est, Royaume-Uni Sud, Europe Ouest, USA Ouest 2.

> [!IMPORTANT]
> Si la limite de vCore et de sous-réseau est 0, cela signifie que la limite régionale par défaut pour votre type d’abonnement n’est pas définie. Vous pouvez également utiliser la demande d’augmentation de quota pour obtenir l’accès à l’abonnement dans une région spécifique en suivant la même procédure, en fournissant les valeurs de vCore et de sous-réseau requises.

## <a name="request-a-quota-increase"></a>Demander une augmentation de quota

Si vous avez besoin de davantage d’instances dans vos régions actuelles, vous pouvez envoyer une demande de support pour étendre le quota par le biais du portail Azure. Pour plus d’informations, consultez [Demander des augmentations de quota pour Azure SQL Database](../database/quota-increase-request.md).

## <a name="previous-generation-hardware"></a>Générations de matériel précédentes

Cette section contient des informations détaillées sur les générations de matériel précédentes. Songez à [déplacer votre instance de SQL Managed Instance vers le matériel de la série Standard (Gen5)](../database/service-tiers-vcore.md) pour profiter d’une plus grande scalabilité en termes de vCore et de stockage, de performances réseau accélérées, d’un niveau de performance d’E/S optimal et d’une latence minimale.

- Gen4 est en cours de retrait et n’est pas disponible pour les nouveaux déploiements. 

### <a name="hardware-generation-characteristics"></a>Caractéristiques de la génération du matériel

|   | **Gen4** | 
| --- | --- | 
| **Matériel** | Processeurs Intel&reg; E5-2673 v3 (Haswell) 2,4 GHz, disque SSD attaché, vCore = 1 PP (cœur physique) |   
| **Nombre de vCore** | 8, 16, 24 vCores | 
| **Mémoire maximale (ratio mémoire/cœur)** | 7 Go par vCore<br/>Ajoutez plus de vCores pour obtenir davantage de mémoire. |  
| **Mémoire OLTP maximale en mémoire** |  Limite de l’instance : 1 à 1,5 Go par vCore |
| **Stockage réservé d’instances maximal** |  Usage général : 8 To <br/>Critique pour l’entreprise : 1 To | 

### <a name="in-memory-oltp-available-space"></a>Espace disponible OLTP en mémoire 

La quantité d’espace OLTP en mémoire au niveau de service [Critique pour l’entreprise](../database/service-tier-business-critical.md) dépend du nombre de vCores et de la génération du matériel. Le tableau suivant liste les limites de mémoire utilisable pour des objets OLTP en mémoire.

| Espace OLTP en mémoire    |  **Gen4** |
| --- |  --- |
| 8 vCores    | 8 Go |
| 16 vCores |  20 Go |
| 24 vCores |  36 Go |


### <a name="service-tier-characteristics"></a>Caractéristiques du niveau de service

| **Fonctionnalité** | **Usage général** | **Critique pour l’entreprise** |
| --- | --- | --- |
| Nombre de vCores\* | Gen4 : 8, 16, 24 | Gen4 : 8, 16, 24 <BR>\*Le même nombre de vCores est dédié pour les requêtes en lecture seule. |
| Mémoire maximale | Gen4 : 56-168 Go (7 Go/vCore)<br/>Ajoutez plus de vCores pour obtenir davantage de mémoire. | Gen4 : 56-168 Go (7 Go/vCore)<br/>+ 20,4 Go-408 Go (5,1 Go/vCore) supplémentaires pour les requêtes en lecture seule.<br/>Ajoutez plus de vCores pour obtenir davantage de mémoire. |
| Taille de stockage maximale d’instance (réservée) | Gen4 : 8 To | Gen4 : 1 To  |
| Taille de base de données maximale | Gen4 : jusqu’à la taille d’instance actuellement disponible (maximum 2-8 To en fonction du nombre de vCores). | Gen4 : jusqu’à la taille d’instance actuellement disponible (maximum 1-4 To en fonction du nombre de vCores). |
| Taille maximale de tempDB | Gen4 : limitée à 24 Go/vCore (96-1 920 Go) et à la taille de stockage d’instance actuellement disponible.<br/>Ajoutez plus de vCores pour obtenir davantage d’espace TempDB.<br/> La taille du fichier journal est limitée à 120 Go.| Gen4 : jusqu’à la taille de stockage d’instance actuellement disponible. |
| Nombre maximal de bases de données par instance | Gen4 : 100 bases de données utilisateur, sauf si la limite de taille de stockage d’instance a été atteinte. | Gen4 : 100 bases de données utilisateur, sauf si la limite de taille de stockage d’instance a été atteinte. |
| Nombre maximal de fichiers de base de données par instance | Gen4 : jusqu’à 280, sauf si la limite de taille de stockage d’instance ou d’[espace d’allocation de stockage sur disque Premium Azure](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files) a été atteinte. | Gen4 : 32 767 fichiers par base de données, sauf si la limite de taille de stockage d’instance a été atteinte. |
| Taille maximale du fichier de données | Gen4 : limitée à la taille de stockage d’instance actuellement disponible (maximum 2-8 To) et à l’[espace d’allocation de stockage sur disque Premium Azure](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files). Utilisez au moins deux fichiers de données pour les bases de données de plus de 8 To. | Gen4 : limitée à la taille de stockage d’instances actuellement disponible (jusqu’à 1-4 To). |
| Taille maximale du fichier journal | Gen4 : limitée à 2 To et à la taille de stockage d’instance actuellement disponible. | Gen4 : limitée à 2 To et à la taille de stockage d’instance actuellement disponible. |
| IOPS de données/journal (approximatives) | Gen4 : jusqu’à 30-40 000 IOPS par instance*, 500-7 500 par fichier<br/>\*[Augmentez la taille de fichier pour obtenir davantage d’IOPS](#file-io-characteristics-in-general-purpose-tier)| Gen4 : 16 000 à 320 000 (4 000 IOPS/vCore)<br/>Ajoutez plus de vCores pour obtenir de meilleures performances d’E/S. | 
| Limite du débit d’écriture du journal (par instance) | Gen4 : 3 Mo/s par vCore<br/>120 Mo/s max. par instance<br/>22 à 65 Mo/s par base de données<br/>\*[Augmentez la taille de fichier pour obtenir de meilleures performances d’E/S](#file-io-characteristics-in-general-purpose-tier) | Gen4 : 4 Mo/s par vCore<br/>96 Mo/s max. |
| Débit de données (approximatif) | Gen4: 100-250 Mo/s par fichier<br/>\*[Augmentez la taille de fichier pour obtenir de meilleures performances d’E/S](#file-io-characteristics-in-general-purpose-tier) | Gen4 : non limité. |
| Latence d’E/S de stockage (approximative) | Gen4 : 5-10 ms | Gen4 : 1-2 ms |
| OLTP en mémoire | Gen4 : non pris en charge | Gen4 : disponible, [la taille dépend du nombre de vCores](#in-memory-oltp-available-space) |
| Nombre maximal de sessions | Gen4 : 30 000 | Gen4 : 30 000 |
| Nombre maximal d’ouvriers simultanés (demandes) | Gen4 : 210 * nombre de vCores + 800 | Gen4 : 210 * nombre de vCores + 800 |
| [Réplicas en lecture seule](../database/read-scale-out.md) | Gen4 : 0 | Gen4 : 1 (inclus dans le prix) |
| Isolation du calcul | Gen4 : non pris en charge | Gen4 : non pris en charge |


## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur SQL Managed Instance, consultez [Présentation de SQL Managed Instance](sql-managed-instance-paas-overview.md).
- Pour obtenir des informations sur les prix, consultez [Tarification SQL Managed Instance](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Pour découvrir comment créer votre première instance managée SQL, consultez le [Guide de démarrage rapide](instance-create-quickstart.md).
