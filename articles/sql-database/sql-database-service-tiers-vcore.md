---
title: Vue d’ensemble du modèle vCore
description: Le modèle d’achat vCore permet de mettre à l’échelle les ressources de calcul et de stockage indépendamment les unes des autres, d’égaler les performances en local et d’optimiser les coûts.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/27/2019
ms.openlocfilehash: c5c7883295a30aa217e722abd905f54b982761d3
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547551"
---
# <a name="vcore-model-overview"></a>Vue d’ensemble du modèle vCore

Le modèle vCore (« cœur virtuel ») offre plusieurs avantages :

- limites de calcul, de mémoire, d’E/S et de stockage plus élevées ;
- contrôle de la génération du matériel pour mieux répondre aux besoins de calcul et de mémoire de la charge de travail ;
- remises sur le tarif [d’Azure Hybrid Benefit (AHB)](sql-database-azure-hybrid-benefit.md) et de [l’Instance réservée (RI)](sql-database-reserved-capacity.md) ;
- plus grande transparence des informations sur le matériel qui fait tourner les calculs, ce qui facilite la planification des migrations à partir de déploiements locaux.

## <a name="service-tiers"></a>Niveaux de service

Les options de niveau de service du modèle vCore sont les suivantes : usage général, critique pour l’entreprise et hyperscale. Le niveau de service définit généralement l’architecture de stockage, les limites d’espace et d’E/S et les options de continuité d’activité liées à la disponibilité et à la reprise d’activité.

||**Usage général**|**Critique pour l’entreprise**|**Hyperscale**|
|---|---|---|---|
|Idéal pour|La plupart des charges de travail d’entreprise. Propose des options de calcul et de stockage équilibrées, évolutives et économiques. |Offre aux applications métier la résilience la plus élevée aux défaillances en utilisant plusieurs réplicas isolés et assure les meilleures performances d’E/S par réplica de base de données.|La plupart des charges de travail métier avec des exigences de stockage et d’échelle lecture à haute scalabilité.  Offre une meilleure résilience aux défaillances en autorisant la configuration de plusieurs réplicas de base de données isolés. |
|Stockage|Utilise le stockage à distance.<br/>**Calcul provisionné pour une base de données et un pool élastique** :<br/>5 Go - 4 To<br/>**Calcul serverless** :<br/>5 Go - 3 To<br/>**Instance managée** : 32 Go - 8 To |Utilise le stockage SSD local.<br/>**Calcul provisionné pour une base de données et un pool élastique** :<br/>5 Go - 4 To<br/>**Instance managée** :<br/>32 Go - 4 To |Croissance automatique et flexible du stockage en fonction des besoins. Prend en charge jusqu’à 100 To de stockage. Utilise le stockage SSD local pour le cache du pool de mémoires tampons local et le stockage de données local. Utilise le stockage distant Azure comme banque de données finale à long terme. |
|Débit d’E/S (approximatif)|**Base de données et pool élastique** : 500 IOPS par vCore avec un maximum de 40 000 IOPS.<br/>**Instance managée** : Dépend de la [taille de fichier](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes).|5 000 IOPS par vCore avec un maximum de 320 000 IOPS|L’architecture hyperscale est une architecture à plusieurs niveaux avec une mise en cache sur plusieurs niveaux. L’efficacité des IOPS dépend de la charge de travail.|
|Disponibilité|1 réplica, réplicas sans échelle lecture|3 réplicas, 1 [réplica avec échelle lecture](sql-database-read-scale-out.md),<br/>haute disponibilité (HA) redondante interzone|1 réplica en lecture-écriture, plus 0 à 4 [réplicas avec échelle lecture](sql-database-read-scale-out.md)|
|Sauvegardes|[Stockage géo-redondant avec accès en lecture (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 à 35 jours (7 jours par défaut)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 à 35 jours (7 jours par défaut)|Sauvegardes basées sur des instantanés dans le stockage distant Azure. Les restaurations utilisent ces instantanés pour une récupération rapide. Les sauvegardes sont instantanées et n’ont aucun impact sur les performances d’E/S de calcul. Les restaurations sont rapides et ne sont pas des opérations à l’échelle des données (elles durent quelques minutes plutôt que quelques heures ou jours).|
|En mémoire|Non pris en charge|Pris en charge|Non pris en charge|
|||


### <a name="choosing-a-service-tier"></a>Choix d’un niveau de service

Pour plus d’informations sur la sélection d’un niveau de service pour une charge de travail spécifique, voir les articles suivants :

- [Quand choisir le niveau de service Usage général](sql-database-service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Quand choisir le niveau de service Critique pour l’entreprise](sql-database-service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Quand choisir le niveau de service Hyperscale](sql-database-service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Niveaux de calcul

Les options de niveau de calcul du modèle vCore sont les suivantes : provisionné et serverless.


### <a name="provisioned-compute"></a>Calcul provisionné

Le niveau de calcul provisionné fournit une quantité spécifique de ressources de calcul provisionnées en continu, indépendamment de l’activité de la charge de travail, la quantité de ressources provisionnées étant facturée à un tarif horaire fixe.


### <a name="serverless-compute"></a>Calcul serverless

Le [niveau de calcul serverless](sql-database-serverless.md) met automatiquement à l’échelle les ressources de calcul en fonction de l’activité de la charge de travail, la quantité de ressources utilisées étant facturée à la seconde.



## <a name="hardware-generations"></a>Génération du matériel

Les options de génération du matériel du modèle vCore sont les suivantes : Gen4/5, série M (préversion) et série Fsv2 (préversion). La génération du matériel définit généralement les limites de calcul et de mémoire, ainsi que d’autres caractéristiques qui ont un impact sur les performances de la charge de travail.

### <a name="gen4gen5"></a>Gen4/Gen5

- Le matériel Gen4/Gen5, qui fournit des ressources de calcul et de mémoire équilibrées, convient à la plupart des charges de travail de base de données qui n’ont pas besoin de la quantité de mémoire, du nombre de vCore et de la vitesse de vCore unique offerts par les séries Fsv2 et M.

Pour connaître les régions dans lesquelles Gen4/Gen5 est disponible, voir [Disponibilité Gen4/Gen5](#gen4gen5-1).

### <a name="fsv2-seriespreview"></a>Série Fsv2 (préversion)

- La série Fsv2 est une option matérielle optimisée pour le calcul qui assure une faible latence du processeur et une fréquence d’horloge élevée pour les charges de travail les plus exigeantes en ressources de processeur.
- En fonction de la charge de travail, la série Fsv2 peut fournir plus de performances de processeur par vCore que Gen5 ; la taille de 72 vCore peut en offrir plus à moindre coût que 80 vCore sur Gen5. 
- Dans la mesure où Fsv2 fournit moins de mémoire et de tempdb par vCore que d’autres matériels, Gen5 ou la série M peuvent être à envisager pour les charges de travail sensibles à ces limites.  

Pour connaître les régions dans lesquelles la série Fsv2 est disponible, voir [Disponibilité de la série Fsv2](#fsv2-series).


### <a name="m-seriespreview"></a>Série M (préversion)

- La série M est une option matérielle à mémoire optimisée destinée aux charges de travail réclamant plus de mémoire et des limites de calcul supérieures à celles fournies par Gen5.
- La série M fournit 29 Go par vCore et 128 vCore, ce qui multiplie par 8 la limite de mémoire par rapport à Gen5 (presque 4 To).

Pour activer le matériel de série M sur un abonnement et une région, il est nécessaire d’ouvrir une demande de support. Si elle est approuvée, l’expérience de sélection et de provisionnement de la série M suit le même modèle que pour les autres générations de matériel. Pour connaître les régions dans lesquelles la série M est disponible, voir [Disponibilité de la série M](#m-series).


### <a name="compute-and-memory-specifications"></a>Spécifications de calcul et de mémoire


|Génération du matériel  |Calcul  |Mémoire  |
|:---------|:---------|:---------|
|Gen4     |- Processeurs Intel E5-2673 v3 (Haswell) 2,4 GHz<br>- Provisionnement dans la limite de 24 vCore (1 vCore = 1 cœur physique)  |- 7 Go par vCore<br>- Provisionnement dans la limite de 168 Go|
|Gen5     |**Calcul provisionné**<br>- Processeurs Intel E5-2673 v4 (Broadwell) de 2,3 GHz et Intel SP-8160 (Skylake)<br>- Provisionnement dans la limite de 80 vCore (1 vCore = 1 hyper-thread)<br><br>**Calcul serverless**<br>- Processeurs Intel E5-2673 v4 (Broadwell) de 2,3 GHz et Intel SP-8160 (Skylake)<br>- Mise à l’échelle automatique dans la limite de 16 vCore (1 vCore = 1 hyper-thread)|**Calcul provisionné**<br>- 5,1 Go par vCore<br>- Provisionnement dans la limite de 408 Go<br><br>**Calcul serverless**<br>- Mise à l’échelle automatique dans la limite de 24 Go par vCore<br>- Mise à l’échelle automatique dans la limite de 48 Go|
|Série Fsv2     |- Processeurs Intel Xeon Platinum 8168 (Skylake)<br>- Fréquence d’horloge turbo tous cœurs prolongée de 3,4 GHz et fréquence d’horloge turbo monocœur maximale de 3,7 GHz<br>- Provisionnement de 72 vCore (1 vCore = 1 hyper-thread)|- 1,9 Go par vCore<br>- Provisionnement de 136 Go|
|Série M     |- Processeurs Intel Xeon E7-8890 v3 2,5 GHz<br>- Provisionnement de 128 vCore (1 vCore = 1 hyper-thread)|- 29 Go par vCore<br>- Provisionnement de 3,7 To|


Pour plus d’informations sur les limites de ressources, voir [Limites de ressources des bases de données uniques (vCore)](sql-database-vcore-resource-limits-single-databases.md)ou [Limites de ressources des pools élastiques (vCore)](sql-database-vcore-resource-limits-elastic-pools.md).

### <a name="selecting-a-hardware-generation"></a>Choisir une génération de matériel

Sur la Portail Azure, vous pouvez sélectionner la génération du matériel pour une base de données SQL ou un pool au moment de la création ou bien la modifier après la création.

**Sélectionner une génération de matériel lors de la création d’une base de données SQL ou d’un pool**

Pour plus d’informations, voir [Créer une base de données SQL](sql-database-single-database-get-started.md).

Sous l’onglet **Informations de base**, sélectionnez le lien **Configurer la base de données** dans la section **Calcul + Stockage**, puis le lien **Modifier la configuration** :

  ![Configurer la base de données](media/sql-database-service-tiers-vcore/configure-sql-database.png)

Sélectionnez la génération du matériel souhaitée :

  ![Sélectionner le matériel](media/sql-database-service-tiers-vcore/select-hardware.png)


**Pour modifier la génération du matériel d’une base de données SQL ou d’un pool existant**

Pour une base de données, sélectionnez le lien **Niveau tarifaire** sur la page Vue d’ensemble :

  ![Modifier le matériel](media/sql-database-service-tiers-vcore/change-hardware.png)

Pour un pool, sélectionnez **Configurer** sur la page Vue d’ensemble.

Suivez les étapes indiquées pour modifier la configuration, puis sélectionnez la génération du matériel comme dans la procédure précédente.

### <a name="hardware-availability"></a>Disponibilité matérielle

#### <a name="gen4gen5-1"></a> Gen4/Gen5

Les nouvelles bases de données Gen4 ne sont plus prises en charge dans les régions Australie Est et Brésil Sud. 

Gen5 est disponible dans la plupart des régions du monde.

#### <a name="fsv2-series"></a>Série Fsv2

La série Fsv2 est disponible dans les régions suivantes : Afrique du Sud Nord, Asie Est, Asie Sud-Est, Australie Centre, Australie Centre 2, Australie Est, Australie Sud-Est, Brésil Sud, Canada Centre, Corée Centre, Corée Sud, Europe Nord, Europe Ouest, France Centre, Inde Centre, Inde Ouest, Royaume-Uni Ouest, Royaume-Uni Sud, USA Est et USA Ouest 2.


#### <a name="m-series"></a>Série M

La série M est disponible dans les régions suivantes : Europe Nord, Europe Ouest, USA Est et USA Ouest 2.
Elle peut également être en disponibilité limitée dans d’autres régions. Vous pouvez demander une autre région que celles de la liste, mais l’approvisionnement ne sera pas forcément possible.

Pour activer la disponibilité de la série M dans un abonnement, il est nécessaire de demander l’accès en [déposant une nouvelle demande de support](#create-a-support-request-to-enable-m-series).


##### <a name="create-a-support-request-to-enable-m-series"></a>Créer une demande de support pour activer la série M 

1. Sélectionnez **Aide + support** sur le portail.
2. Sélectionnez **Nouvelle demande de support**.

Sur la page **Informations de base**, indiquez les informations suivantes :

1. Pour **Type de problème**, sélectionnez **Limites du service et des abonnements (quotas)** .
2. Dans **Abonnement**, sélectionnez l’abonnement pour lequel vous voulez activer la série M.
3. Dans **Type de quota**, sélectionnez **Base de données SQL**.
4. Sélectionnez **Suivant** pour accéder à la page **Détails**.

Sur la page **Détails**, indiquez les informations suivantes :

5. Dans la section **DÉTAILS DU PROBLÈME**, sélectionnez le lien **Indiquer des détails**. 
6. Dans **Type de quota SQL Database**, sélectionnez **Série M**.
7. Dans **Région**, sélectionnez la région pour laquelle vous voulez activer la série M.
    Pour connaître les régions dans lesquelles la série M est disponible, voir [Disponibilité de la série M](#m-series).

Les demandes de support approuvées sont généralement approvisionnées sous cinq jours ouvrables.


## <a name="next-steps"></a>Étapes suivantes

- Pour créer une base de données SQL, voir [Créer une base de données SQL avec le Portail Azure](sql-database-single-database-get-started.md).
- Pour plus d’informations sur les tailles de calcul et les tailles de stockage disponibles pour les bases de données uniques, consultez [Limites des ressources vCore SQL Database pour les bases de données uniques](sql-database-vcore-resource-limits-single-databases.md).
- Pour plus d’informations sur les tailles de calcul et les tailles de stockage disponibles pour les pools élastiques, consultez [Limites des ressources vCore SQL Database pour les pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md).
- Pour plus d’informations sur les tarifs, voir la [page des tarifs Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/).
