---
title: Azure Hybrid Benefit
description: Utilisez des licences SQL Server existantes pour les remises SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: d1a59e7ad86191bcc30b7d898d00f327c20fbc5e
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945621"
---
# <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

Dans le niveau de calcul provisionné du modèle d’achat vCore, vous pouvez échanger vos licences existantes avec des tarifs réduits sur SQL Database, en utilisant [Azure Hybrid Benefit pour SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Cet avantage Azure vous permet d’économiser jusqu’à 30 % voire plus sur Azure SQL Database en utilisant vos licences SQL Server locales avec la Software Assurance. Utilisez la calculatrice Azure Hybrid Benefit en suivant le lien mentionné précédemment pour obtenir des valeurs précises. 

> [!NOTE]
> Le passage à Azure Hybrid Benefit n’implique aucun temps d’arrêt.

![Prix](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>Choisir un modèle de licence

Avec Azure Hybrid Benefit, vous pouvez choisir de payer uniquement pour l’infrastructure Azure sous-jacente en utilisant votre licence SQL Server existante pour le moteur de base de données SQL lui-même (tarification de calcul de base) ou de payer à la fois pour l’infrastructure sous-jacente et pour la licence SQL Server (tarification avec licence incluse).

Vous pouvez choisir ou modifier votre modèle de licence en utilisant le Portail Azure ou l’une des API suivantes :

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Pour définir ou mettre à jour le type de licence à l’aide de PowerShell :

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour définir ou mettre à jour le type de licence à l’aide d’Azure CLI :

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [az sql mi create](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-apitabrest"></a>[REST API](#tab/rest)

Pour définir ou mettre à jour le type de licence à l’aide de l’API REST :

- [Bases de données : Create ou Update](/rest/api/sql/databases/createorupdate)
- [Bases de données - Mettre à jour](/rest/api/sql/databases/update)
- [Instances managées - Créer ou Mettre à jour](/rest/api/sql/managedinstances/createorupdate)
- [Instances managées - Mettre à jour](/rest/api/sql/managedinstances/update)

* * *

## <a name="next-steps"></a>Étapes suivantes

- Pour choisir entre les options de déploiement de SQL Database, consultez [Choisir l’option de déploiement appropriée dans Azure SQL](sql-database-paas-vs-sql-server-iaas.md).
- Pour une comparaison des fonctionnalités de SQL Database, consultez [Fonctionnalités d’Azure SQL Database](sql-database-features.md).
