---
title: Prise en charge du déplacement des ressources Azure SQL entre différentes régions avec Azure Resource Mover
description: Passez en revue la prise en charge du déplacement des ressources Azure SQL entre différentes régions avec Azure Resource Mover.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 4925f6ffd2383c21f8ff9b0e3196d44fc15bb657
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652590"
---
# <a name="support-for-moving-azure-sql-resources-between-azure-regions"></a>Prise en charge du déplacement des ressources Azure SQL entre différentes régions Azure

Cet article résume la prise en charge et la configuration requise pour déplacer des ressources Azure SQL entre des régions Azure avec Azure Resource Mover.

## <a name="requirements"></a>Spécifications

Ces conditions sont résumées dans le tableau suivant.

**Fonctionnalité** | **Prise en charge/Non prise en charge** | **Détails**
--- | --- | ---
**Azure SQL Database Hyperscale** | Non pris en charge | Impossible de déplacer des bases de données dans le niveau de service Azure SQL Hyperscale avec Resource Mover.
**Redondance de zone** | Prise en charge |  Options de déplacement prises en charge :<br/><br/> – Entre les régions qui prennent en charge la redondance de zone.<br/><br/> – Entre les régions qui ne prennent pas en charge la redondance de zone.<br/><br/> – D’une région qui prend en charge la redondance de zone vers une région qui ne prend pas en charge la redondance de zone.<br/><br/> – D’une région qui ne prend pas en charge la redondance de zone vers une région qui prend en charge la redondance de zone. 
**Synchronisation des données** | Hub/synchroniser la base de données : Non pris en charge<br/><br/> Membre de synchronisation : Pris en charge. | Si un membre de synchronisation est déplacé, vous devez configurer la synchronisation des données avec la nouvelle base de données cible.
**Géoréplication existante** | Prise en charge | Les géoréplications existantes sont remappées au nouveau principal dans la région cible.<br/><br/> L’amorçage doit être initialisé après le déplacement. [En savoir plus](/azure/sql-database/sql-database-active-geo-replication-portal)
**Transparent Data Encryption (TDE) avec Bring Your Own Key (BYOK)** | Prise en charge | [En savoir plus](../key-vault/general/move-region.md) sur le déplacement des coffres de clés entre les régions.
**TDE avec clé gérée par le service** | Pris en charge. |  [En savoir plus](../key-vault/general/move-region.md) sur le déplacement des coffres de clés entre les régions.
**Règles de masquage dynamique des données** | Pris en charge. | Les règles sont copiées automatiquement dans la région cible dans le cadre du déplacement. [Plus d’informations](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started-portal)
**Advanced Data Security** | Non pris en charge. | Solution de contournement : Configuration au niveau de SQL Server dans la région cible. [Plus d’informations](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)
**Règles de pare-feu** | Non pris en charge. | Solution de contournement : Configuration des règles de pare-feu pour SQL Server dans la région cible. Les règles de pare-feu au niveau de la base de données sont copiées du serveur source au serveur cible. [Plus d’informations](https://docs.microsoft.com/azure/sql-database/sql-database-server-level-firewall-rule)
**Stratégies d’audit** | Non pris en charge. | Les stratégies sont réinitialisées aux valeurs par défaut après le déplacement. [En savoir plus](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) sur la réinitialisation.
**Rétention des sauvegardes** | Pris en charge. | Les stratégies de rétention des sauvegardes de la base de données source sont transférées à la base de données cible. [En savoir plus](/azure/sql-database/sql-database-long-term-backup-retention-configure) sur la modification des paramètres après le déplacement.
**Réglage automatique** | Non pris en charge. | Solution de contournement : Configuration des paramètres de réglage automatique après le déplacement. [Plus d’informations](https://docs.microsoft.com/azure/sql-database/sql-database-automatic-tuning-enable)
**Alertes de base de données** | Non pris en charge. | Solution de contournement : Configuration des alertes après le déplacement. [Plus d’informations](https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal)
**Instance stretch database Azure SQL Server** | Non pris en charge | Impossible de déplacer les instances stretch database SQL Server avec Resource Mover.
**Azure Synapse Analytics** | Non pris en charge | Impossible de déplacer Synapse Analytics (anciennement Azure SQL Data Warehouse) avec Resource Mover.
## <a name="next-steps"></a>Étapes suivantes

Essayez de déplacer des [ressources Azure SQL](tutorial-move-region-sql.md) vers une autre région avec Resource Mover.