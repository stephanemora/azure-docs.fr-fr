---
title: Modèle d’achat vCore
description: Le modèle d’achat vCore permet de mettre à l’échelle les ressources de calcul et de stockage indépendamment les unes des autres, d’égaler les performances en local et d’optimiser les coûts pour Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: performance
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sashan, moslake
ms.date: 05/18/2021
ms.openlocfilehash: 16782538918c0477b969f95b7730b38221f97f82
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111414819"
---
# <a name="azure-sql-managed-instance---compute-hardware-in-the-vcore-service-tier"></a>Azure SQL Managed Instance - Matériel de calcul dans le niveau de Service vCore
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Cet article passe en revue le modèle d’achat vCore pour [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md). Pour plus d’informations sur le choix entre les modèles d’achat vCore et DTU, consultez [choisir entre les modèles d’achat vCore et DTU](../database/purchasing-models.md).

Le modèle d’achat vCore utilisé par Azure SQL Managed Instance présente les caractéristiques suivantes :

- contrôle de la génération du matériel pour mieux répondre aux besoins de calcul et de mémoire de la charge de travail ;
- remises sur le tarif [d’Azure Hybrid Benefit (AHB)](../azure-hybrid-benefit.md) et de [l’Instance réservée (RI)](../database/reserved-capacity-overview.md) ;
- plus grande transparence des informations sur le matériel qui fait tourner les calculs, ce qui facilite la planification des migrations à partir de déploiements locaux.
- La [tarification des instances réservées](../database/reserved-capacity-overview.md) est disponible uniquement pour le modèle d’achat vCore. 

## <a name="service-tiers"></a><a id="compute-tiers"></a>Niveaux de service

Les options de niveau de service du modèle d’achat vCore sont les suivantes : usage général et critique pour l’entreprise. Le niveau de service définit généralement l’architecture de stockage, les limites d’espace et d’E/S et les options de continuité d’activité liées à la disponibilité et à la reprise d’activité.

|**Cas d’usage**|**Usage général**|**Critique pour l’entreprise**|
|---|---|---|
|Idéal pour|La plupart des charges de travail d’entreprise. Propose des options de calcul et de stockage équilibrées, évolutives et économiques. |Offre aux applications métier la résilience la plus élevée aux défaillances en utilisant plusieurs réplicas isolés et assure les meilleures performances d’E/S.|
|Stockage|Utilise le stockage à distance. 32 Go - 8 To |Utilise le stockage SSD local. 32 Go - 4 To |
|IOPS et débit (approximatif)|Consultez [Vue d’ensemble des limites de ressources Azure SQL Managed Instance](../managed-instance/resource-limits.md#service-tier-characteristics).|Consultez [Vue d’ensemble des limites de ressources Azure SQL Managed Instance](../managed-instance/resource-limits.md#service-tier-characteristics).|
|Disponibilité|1 réplica, réplicas sans échelle lecture|3 réplicas, 1 [réplica avec échelle lecture](../database/read-scale-out.md),<br/>haute disponibilité (HA) redondante interzone|
|Sauvegardes|[Stockage géoredondant avec accès en lecture (RA-GRS)](../../storage/common/geo-redundant-design.md), 1 à 35 jours (7 jours par défaut)|[RA-GRS](../../storage/common/geo-redundant-design.md), 1 à 35 jours (7 jours par défaut)|
|En mémoire|Non pris en charge|Pris en charge|
||||

### <a name="choosing-a-service-tier"></a>Choix d’un niveau de service

Pour plus d’informations sur la sélection d’un niveau de service pour une charge de travail spécifique, voir les articles suivants :

- [Quand choisir le niveau de service Usage général](../database/service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Quand choisir le niveau de service Critique pour l’entreprise](../database/service-tier-business-critical.md#when-to-choose-this-service-tier)

## <a name="compute"></a>Compute

Le calcul SQL Managed Instance fournit une quantité spécifique de ressources de calcul provisionnées en continu, indépendamment de l’activité de la charge de travail, la quantité de ressources provisionnées étant facturée à un tarif horaire fixe.

## <a name="hardware-generations"></a>Génération du matériel

Les options de génération de matériel du modèle vCore incluent la série Gen 5. La génération du matériel définit généralement les limites de calcul et de mémoire, ainsi que d’autres caractéristiques qui ont un impact sur les performances de la charge de travail.

### <a name="compute-and-memory-specifications"></a>Spécifications de calcul et de mémoire

|Génération du matériel  |Calcul  |Mémoire  |
|:---------|:---------|:---------|
|Gen4     |- Processeurs Intel&reg; E5-2673 v3 (Haswell) 2,4 GHz<br>- Provisionnement dans la limite de 24 vCore (1 vCore = 1 cœur physique)  |- 7 Go par vCore<br>- Provisionnement dans la limite de 168 Go|
|Gen5     |- Processeurs Intel&reg; E5-2673 v4 (Broadwell) 2,3 GHz, Intel&reg; SP-8160 (Skylake)\* et Intel&reg; 8272CL (Cascade Lake) 2,5 GHz\*<br>- Provisionnement dans la limite de 80 vCore (1 vCore = 1 hyper-thread)|5,1 Go par vCore<br>- Provisionnement dans la limite de 408 Go|

\* Dans la vue de gestion dynamique [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database), la génération de matériel pour les instances utilisant des processeurs Intel&reg; SP-8160 (Skylake) apparaît comme Gen6, tandis que la génération de matériel pour les instances utilisant des processeurs Intel&reg; 8272CL (Cascade Lake) apparaît comme Gen7. Les limites de ressources pour toutes les instances Gen5 sont identiques, quel que soit le type de processeur (Broadwell, Skylake ou Cascade Lake).

### <a name="selecting-a-hardware-generation"></a>Choisir une génération de matériel

Dans le portail Azure, vous pouvez sélectionner la génération de matériel au moment de la création, ou bien la modifier pour une instance managée SQL existante.

**Pour sélectionner une génération de matériel lors de la création d’une instance SQL Managed Instance**

Pour plus d’informations, consultez [Créer une instance SQL Managed Instance](../managed-instance/instance-create-quickstart.md).

Sous l'onglet **Informations de base**, sélectionnez le lien **Configurer la base de données** dans la section **Calcul + Stockage**, puis sélectionnez la génération de matériel souhaitée :

:::image type="content" source="../database/media/service-tiers-vcore/configure-managed-instance.png" alt-text="Configurer une instance SQL Managed Instance"  loc-scope="azure-portal":::
  
**Pour changer la génération du matériel d’une instance SQL Managed Instance existante**

#### <a name="the-azure-portal"></a>[Le portail Azure](#tab/azure-portal)

Dans la page SQL Managed Instance, sélectionnez le lien **Niveau tarifaire** situé sous la section Paramètres

:::image type="content" source="../database/media/service-tiers-vcore/change-managed-instance-hardware.png" alt-text="Modifier le matériel d’une instance SQL Managed Instance"  loc-scope="azure-portal":::

Dans la page Niveau tarifaire, vous pouvez modifier la génération du matériel comme décrit dans les étapes précédentes.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilisez le script PowerShell suivant :

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Pour plus d’informations, consultez la rubrique relative à la commande [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance).

#### <a name="the-azure-cli"></a>[L’interface de ligne de commande Microsoft Azure](#tab/azure-cli)

Utilisez la commande CLI suivante :

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Pour plus d’informations, consultez la rubrique relative à la commande [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update).

---

### <a name="hardware-availability"></a>Disponibilité matérielle

#### <a name="gen4gen5"></a><a id="gen4gen5-1"></a> Gen4/Gen5

Le matériel Gen4 est [en cours de retrait](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) et n’est plus disponible pour les nouveaux déploiements. Toutes les nouvelles instances doivent être déployées sur le matériel Gen5.

Gen5 est disponible dans toutes les régions publiques du monde entier.

## <a name="next-steps"></a>Étapes suivantes

- Pour commencer, consultez [Créer une instance managée SQL à l’aide du portail Azure](instance-create-quickstart.md).
- Pour les détails de la tarification, consultez 
    - [Page de tarification d’Azure SQL Managed Instance](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/single/)
    - [Page de tarification des pools d’Azure SQL Managed Instance](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/pools/)
- Pour obtenir des informations détaillées sur les tailles de calcul et de stockage spécifiques disponibles dans les niveaux de service usage général et critique pour l’entreprise, consultez [Limites de ressources vCore pour Azure SQL Managed Instance](resource-limits.md).
