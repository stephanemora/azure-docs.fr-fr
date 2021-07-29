---
title: Modèle d’achat vCore
description: Le modèle d’achat vCore permet de mettre à l’échelle les ressources de calcul et de stockage indépendamment les unes des autres, d’égaler les performances en local et d’optimiser les coûts pour Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sashan, moslake
ms.date: 06/02/2021
ms.custom: references_regions
ms.openlocfilehash: a78622aaccfaeb372d67ef4575e2bd923ab6cb58
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111415187"
---
# <a name="vcore-purchase-model-overview---azure-sql-database"></a>Vue générale du modèle d’achat vCore - Azure SQL Database 
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Cet article passe en revue le modèle d’achat vCore pour [Azure SQL Database](sql-database-paas-overview.md). Pour plus d’informations sur le choix entre les modèles d’achat vCore et DTU, consultez [choisir entre les modèles d’achat vCore et DTU](purchasing-models.md).

Le modèle d’achat Virtual Core (vCore) utilisé par Azure SQL Database offre plusieurs avantages par rapport au modèle d’achat DTU :

- limites de calcul, de mémoire, d’E/S et de stockage plus élevées ;
- contrôle de la génération du matériel pour mieux répondre aux besoins de calcul et de mémoire de la charge de travail ;
- Remises tarifaires pour [Azure Hybrid Benefit (AHB)](../azure-hybrid-benefit.md).
- plus grande transparence des informations sur le matériel qui fait tourner les calculs, ce qui facilite la planification des migrations à partir de déploiements locaux.
- La [tarification des instances réservées](reserved-capacity-overview.md) est disponible uniquement pour le modèle d’achat vCore. 

## <a name="service-tiers"></a>Niveaux de service

Les options de niveau de service du modèle d’achat vCore sont les suivantes : usage général, critique pour l’entreprise et hyperscale. Le niveau de service définit généralement l’architecture de stockage, les limites d’espace et d’E/S et les options de continuité d’activité liées à la disponibilité et à la reprise d’activité.

|**Cas d’usage**|**Usage général**|**Critique pour l’entreprise**|**Hyperscale**|
|---|---|---|---|
|Idéal pour|La plupart des charges de travail d’entreprise. Propose des options de calcul et de stockage équilibrées, évolutives et économiques. |Offre aux applications métier la résilience la plus élevée aux défaillances en utilisant plusieurs réplicas isolés et assure les meilleures performances d’E/S par réplica de base de données.|La plupart des charges de travail métier avec des exigences de stockage et d’échelle lecture à haute scalabilité.  Offre une meilleure résilience aux défaillances en autorisant la configuration de plusieurs réplicas de base de données isolés. |
|Stockage|Utilise le stockage à distance.<br/>**Calcul provisionné par SQL Database** :<br/>5 Go - 4 To<br/>**Calcul serverless** :<br/>5 Go - 3 To|Utilise le stockage SSD local.<br/>**Calcul provisionné par SQL Database** :<br/>5 Go - 4 To|Croissance automatique et flexible du stockage en fonction des besoins. Prend en charge jusqu’à 100 To de stockage. Utilise le stockage SSD local pour le cache du pool de mémoires tampons local et le stockage de données local. Utilise le stockage distant Azure comme banque de données finale à long terme. |
|IOPS et débit (approximatif)|**SQL Database** : Consultez les limites de ressources pour les [bases de données uniques](resource-limits-vcore-single-databases.md) et les [pools élastiques](resource-limits-vcore-elastic-pools.md).|Consultez les limites de ressources pour les [bases de données uniques](resource-limits-vcore-single-databases.md) et les [pools élastiques](resource-limits-vcore-elastic-pools.md).|L’architecture hyperscale est une architecture à plusieurs niveaux avec une mise en cache sur plusieurs niveaux. L’IOPS et le débit réels dépendront de la charge de travail.|
|Disponibilité|1 réplica, réplicas sans échelle lecture|3 réplicas, 1 [réplica avec échelle lecture](read-scale-out.md),<br/>haute disponibilité (HA) redondante interzone|1 réplica en lecture-écriture, plus 0 à 4 [réplicas avec échelle lecture](read-scale-out.md)|
|Sauvegardes|[Stockage géoredondant avec accès en lecture (RA-GRS)](../../storage/common/geo-redundant-design.md), 1 à 35 jours (7 jours par défaut)|[RA-GRS](../..//storage/common/geo-redundant-design.md), 1 à 35 jours (7 jours par défaut)|Sauvegardes basées sur des instantanés dans le stockage distant Azure. Les restaurations utilisent ces instantanés pour une récupération rapide. Les sauvegardes sont instantanées et n’ont aucun impact sur les performances d’E/S de calcul. Les restaurations sont rapides et ne sont pas des opérations à l’échelle des données (elles durent quelques minutes plutôt que quelques heures ou jours).|
|En mémoire|Non pris en charge|Prise en charge|Prise en charge partielle. Les types de tables optimisées en mémoire, les variables de table et les modules compilés en mode natif sont pris en charge.|
|||


### <a name="choosing-a-service-tier"></a>Choix d’un niveau de service

Pour plus d’informations sur la sélection d’un niveau de service pour une charge de travail spécifique, voir les articles suivants :

- [Quand choisir le niveau de service Usage général](service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Quand choisir le niveau de service Critique pour l’entreprise](service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Quand choisir le niveau de service Hyperscale](service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Niveaux de calcul

Les options de niveau de calcul du modèle vCore sont les suivantes : provisionné et serverless.


### <a name="provisioned-compute"></a>Calcul provisionné

Le niveau de calcul provisionné fournit une quantité spécifique de ressources de calcul provisionnées en continu, indépendamment de l’activité de la charge de travail, la quantité de ressources provisionnées étant facturée à un tarif horaire fixe.


### <a name="serverless-compute"></a>Calcul serverless

Le [niveau de calcul serverless](serverless-tier-overview.md) met automatiquement à l’échelle les ressources de calcul en fonction de l’activité de la charge de travail, la quantité de ressources utilisées étant facturée à la seconde.



## <a name="hardware-generations"></a>Génération du matériel

Les options de génération du matériel du modèle vCore sont les suivantes : Gen 4/5, série M, série Fsv2 et série DC. La génération du matériel définit généralement les limites de calcul et de mémoire, ainsi que d’autres caractéristiques qui ont un impact sur les performances de la charge de travail.

### <a name="gen4gen5"></a>Gen4/Gen5

- Le matériel Gen4/Gen5, qui fournit des ressources de calcul et de mémoire équilibrées, convient à la plupart des charges de travail de base de données qui n’ont pas besoin de la quantité de mémoire, du nombre de vCore et de la vitesse de vCore unique offerts par les séries Fsv2 et M.

Pour connaître les régions dans lesquelles Gen4/Gen5 est disponible, voir [Disponibilité Gen4/Gen5](#gen4gen5-1).

### <a name="fsv2-series"></a>Série Fsv2

- La série Fsv2 est une option matérielle optimisée pour le calcul qui assure une faible latence du processeur et une fréquence d’horloge élevée pour les charges de travail les plus exigeantes en ressources de processeur.
- En fonction de la charge de travail, la série Fsv2 peut fournir plus de performances de processeur par vCore que Gen5 ; la taille de 72 vCore peut en offrir plus à moindre coût que 80 vCore sur Gen5. 
- Dans la mesure où Fsv2 fournit moins de mémoire et de tempdb par vCore que d’autres matériels, Gen5 ou la série M peuvent être à envisager pour les charges de travail sensibles à ces limites.  

La série Fsv2 n’est pas prise en charge dans le niveau Usage général. Pour connaître les régions dans lesquelles la série Fsv2 est disponible, voir [Disponibilité de la série Fsv2](#fsv2-series-1).

### <a name="m-series"></a>Série M

- La série M est une option matérielle à mémoire optimisée destinée aux charges de travail réclamant plus de mémoire et des limites de calcul supérieures à celles fournies par Gen5.
- La série M fournit 29 Go par vCore et jusqu’à 128 vCores, ce qui multiplie par 8 la limite de mémoire par rapport à Gen5 (presque 4 To).

La série M est prise en charge uniquement dans le niveau Critique pour l’entreprise et ne prend pas en charge la redondance de zone.  Pour connaître les régions dans lesquelles la série M est disponible, voir [Disponibilité de la série M](#m-series-1).

#### <a name="azure-offer-types-supported-by-m-series"></a>Types d’offres Azure pris en charge par la série M

Pour accéder à la série M, l’abonnement doit être un type d’offre payante comme Paiement à l’utilisation ou Contrat Entreprise (EA).  Pour obtenir la liste complète des types d’offres Azure pris en charge par la série M, consultez [offres actuelles sans limites de dépense](https://azure.microsoft.com/support/legal/offer-details).

<!--
To enable M-series hardware for a subscription and region, a support request must be opened. The subscription must be a paid offer type including Pay-As-You-Go or Enterprise Agreement (EA).  If the support request is approved, then the selection and provisioning experience of M-series follows the same pattern as for other hardware generations. For regions where M-series is available, see [M-series availability](#m-series).
-->

### <a name="dc-series"></a>Série DC

> [!NOTE]
> La série DC est actuellement en **préversion publique**.

- Le matériel de la série DC utilise des processeurs Intel avec la technologie Software Guard Extensions (Intel SGX).
- La série DC est nécessaire pour [Always Encrypted avec enclaves sécurisées](/sql/relational-databases/security/encryption/always-encrypted-enclaves), ce qui n’est pas pris en charge avec d’autres configurations matérielles.
- La série DC est conçue pour les charges de travail qui traitent des données sensibles et exigent des fonctionnalités de traitement des requêtes confidentielles, fournies par Always Encrypted avec enclaves sécurisées.
- Le matériel de la série DC fournit des ressources de calcul et de mémoire équilibrées.

La série DC est uniquement prise en charge pour le calcul provisionné (le mode serverless n’est pas pris en charge) et ne prend pas en charge la redondance de zone. Pour connaître les régions dans lesquelles la série DC est disponible, voir [Disponibilité de la série DC](#dc-series-1).

#### <a name="azure-offer-types-supported-by-dc-series"></a>Types d’offres Azure pris en charge par la série DC

Pour accéder à la série DC, l’abonnement doit être un type d’offre payante comme Paiement à l’utilisation ou Contrat Entreprise (EA).  Pour obtenir la liste complète des types d’offres Azure pris en charge par la série DC, consultez [offres actuelles sans limites de dépense](https://azure.microsoft.com/support/legal/offer-details).

### <a name="compute-and-memory-specifications"></a>Spécifications de calcul et de mémoire


|Génération du matériel  |Calcul  |Mémoire  |
|:---------|:---------|:---------|
|Gen4     |- Processeurs Intel&reg; E5-2673 v3 (Haswell) 2,4 GHz<br>- Provisionnement dans la limite de 24 vCore (1 vCore = 1 cœur physique)  |- 7 Go par vCore<br>- Provisionnement dans la limite de 168 Go|
|Gen5     |**Calcul provisionné**<br>- Processeurs Intel&reg; E5-2673 v4 (Broadwell) 2,3 GHz, Intel&reg; SP-8160 (Skylake)\* et Intel&reg; 8272CL (Cascade Lake) 2,5 GHz\*<br>- Provisionnement dans la limite de 80 vCore (1 vCore = 1 hyper-thread)<br><br>**Calcul serverless**<br>- Processeurs Intel&reg; E5-2673 v4 (Broadwell) cadencé à 2.3 GHz et Intel&reg; SP-8160 (Skylake)*<br>- Mise à l’échelle automatique dans la limite de 40 vCores (1 vCore = 1 hyper-thread)|**Calcul provisionné**<br>- 5,1 Go par vCore<br>- Provisionnement dans la limite de 408 Go<br><br>**Calcul serverless**<br>- Mise à l’échelle automatique dans la limite de 24 Go par vCore<br>- Mise à l’échelle automatique dans la limite de 120 Go|
|Série Fsv2     |- Processeurs Intel&reg; 8168 (Skylake)<br>- Fréquence d’horloge turbo tous cœurs prolongée de 3,4 GHz et fréquence d’horloge turbo monocœur maximale de 3,7 GHz<br>- Provisionnement dans la limite de 72 vCores (1 vCore = 1 hyper-thread)|- 1,9 Go par vCore<br>- Provisionnement dans la limite de 136 Go|
|Série M     |- Processeurs Intel&reg; E7-8890 v3 2,5 GHz et Intel&reg; 8280M 2,7 GHz (Cascade Lake)<br>- Provisionnement dans la limite de 128 vCores (1 vCore = 1 hyper-thread)|- 29 Go par vCore<br>- Provisionnement dans la limite de 3,7 To|
|Série DC     | - Processeurs Intel XEON E-2288G<br>- Avec Intel Software Guard Extension (Intel SGX)<br>- Provisionnement dans la limite de 8 vCore (1 vCore = 1 cœur physique) | 4,5 Go par vCore |

\* Dans la vue de gestion dynamique [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database), la génération de matériel pour les bases de données utilisant des processeurs Intel&reg; SP-8160 (Skylake) apparaît comme Gen6, tandis que la génération de matériel pour les bases de données utilisant des processeurs Intel&reg; 8272CL (Cascade Lake) apparaît comme Gen7. Les limites de ressources pour toutes les bases de données Gen5 sont identiques, quel que soit le type de processeur (Broadwell, Skylake ou Cascade Lake).

Pour plus d’informations sur les limites de ressources, voir [Limites de ressources des bases de données uniques (vCore)](resource-limits-vcore-single-databases.md)ou [Limites de ressources des pools élastiques (vCore)](resource-limits-vcore-elastic-pools.md).

### <a name="selecting-a-hardware-generation"></a>Choisir une génération de matériel

Dans le portail Azure, vous pouvez sélectionner la génération de matériel pour une base de données ou un pool dans SQL Database au moment de la création, ou bien modifier la génération de matériel d’une base de données ou un pool existants.

**Pour sélectionner une génération de matériel lors de la création d’une base de données SQL ou d’un pool**

Pour plus d’informations, consultez [Créer une base de données SQL](single-database-create-quickstart.md).

Sous l’onglet **Informations de base**, sélectionnez le lien **Configurer la base de données** dans la section **Calcul + Stockage**, puis le lien **Modifier la configuration** :

:::image type="content" source="./media/service-tiers-vcore/configure-sql-database.png" alt-text="configurer base de données SQL" loc-scope="azure-portal":::

Sélectionnez la génération du matériel souhaitée :

:::image type="content" source="./media/service-tiers-vcore/select-hardware.png" alt-text="sélectionner le matériel pour la base de données SQL" loc-scope="azure-portal":::

**Pour changer la génération du matériel d’une base de données SQL ou d’un pool existant**

Pour une base de données, sélectionnez le lien **Niveau tarifaire** sur la page Vue d’ensemble :

:::image type="content" source="./media/service-tiers-vcore/change-hardware.png" alt-text="changer le matériel pour la base de données SQL" loc-scope="azure-portal":::

Pour un pool, sélectionnez **Configurer** sur la page Vue d’ensemble.

Suivez les étapes indiquées pour modifier la configuration, puis sélectionnez la génération du matériel comme dans la procédure précédente.

### <a name="hardware-availability"></a>Disponibilité matérielle

#### <a name="gen4gen5"></a><a id="gen4gen5-1"></a> Gen4/Gen5

Le matériel Gen4 est [en cours de retrait](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) et n’est plus disponible pour les nouveaux déploiements. Toutes les nouvelles bases de données doivent être déployées sur le matériel Gen5.

Gen5 est disponible dans toutes les régions publiques du monde entier.

#### <a name="fsv2-series"></a>Série Fsv2

La série Fsv2 est disponible dans les régions suivantes : Afrique du Sud Nord, Asie Est, Asie Sud-Est, Australie Centre, Australie Centre 2, Australie Est, Australie Sud-Est, Brésil Sud, Canada Centre, Corée Centre, Corée Sud, Europe Nord, Europe Ouest, France Centre, Inde Centre, Royaume-Uni Ouest, Royaume-Uni Sud, USA Est et USA Ouest 2.

#### <a name="m-series"></a>Série M

La série M est disponible dans les régions suivantes : Europe Nord, Europe Ouest, USA Est et USA Ouest 2.
<!--
M-series may also have limited availability in additional regions. You can request a different region than listed here, but fulfillment in a different region may not be possible.

To enable M-series availability in a subscription, access must be requested by [filing a new support request](#create-a-support-request-to-enable-m-series).


##### Create a support request to enable M-series: 

1. Select **Help + support** in the portal.
2. Select **New support request**.

On the **Basics** page, provide the following:

1. For **Issue type**, select **Service and subscription limits (quotas)**.
2. For **Subscription** = select the subscription to enable M-series.
3. For **Quota type**, select **SQL database**.
4. Select **Next** to go to the **Details** page.

On the **Details** page, provide the following:

1. In the **PROBLEM DETAILS** section select the **Provide details** link. 
2. For **SQL Database quota type** select **M-series**.
3. For **Region**, select the region to enable M-series.
    For regions where M-series is available, see [M-series availability](#m-series).

Approved support requests are typically fulfilled within 5 business days.
-->

#### <a name="dc-series"></a>Série DC

> [!NOTE]
> La série DC est actuellement en **préversion publique**.

La série DC est disponible dans les régions suivantes : Canada Centre, Canada Est, USA Est, Europe Nord, Royaume-Uni Sud, Europe Ouest, USA Ouest.

Si vous avez besoin d’une série DC dans une région actuellement non prise en charge, [soumettez un ticket de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) en suivant les instructions de la rubrique [Demander des augmentations de quota pour Azure SQL Database et SQL Managed Instance](quota-increase-request.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour commencer, consultez [Création d’une base de données SQL à l’aide du Portail Azure](single-database-create-quickstart.md)
- Pour plus d’informations sur les tarifs, consultez la [page des tarifs Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/)
- Pour plus de détails sur les tailles spécifiques de calcul et de stockage disponibles, consultez :
    - [Limites de ressources vCore pour Azure SQL Database](resource-limits-vcore-single-databases.md)
    - [Limites de ressources vCore pour une instance Azure SQL Database mise en pool](resource-limits-vcore-elastic-pools.md)
