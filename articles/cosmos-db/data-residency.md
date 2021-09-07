---
title: Comment répondre aux exigences de résidence des données dans Azure Cosmos DB
description: Découvrez comment répondre aux exigences de résidence des données dans Azure Cosmos DB afin que vos données et sauvegardes restent dans une seule région.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 869b28c8c65c402936dcb7b7641a6e4af8db3f47
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123100895"
---
# <a name="how-to-meet-data-residency-requirements-in-azure-cosmos-db"></a>Comment répondre aux exigences de résidence des données dans Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Dans Azure Cosmos DB, vous pouvez configurer vos données et sauvegardes pour qu’elles restent dans une seule région afin de répondre aux [exigences de résidence.](https://azure.microsoft.com/global-infrastructure/data-residency/)

## <a name="residency-requirements-for-data"></a>Exigences de résidence pour les données

Dans Azure Cosmos DB, vous devez configurer explicitement la réplication des données entre régions. Découvrez comment configurer la géoréplication via le [portail Azure](how-to-manage-database-account.md#addremove-regions-from-your-database-account), à l’aide d’[Azure CLI](scripts/cli/common/regions.md). Pour répondre aux besoins de résidence des données, vous pouvez créer une définition Azure Policy qui permet à certaines régions d’empêcher la réplication de données sur des régions non souhaitées.

## <a name="residency-requirements-for-backups"></a>Exigences de résidence pour les sauvegardes

**Sauvegardes en mode continu** : ces sauvegardes sont résidentes par défaut, car elles sont stockées dans un stockage localement redondant ou redondant interzone. Pour en savoir plus, consultez l’article [Sauvegarde continue](provision-account-continuous-backup.md).

**Sauvegardes en mode périodique** : par défaut, les sauvegardes de compte en mode périodique sont stockées dans un stockage géoredondant. Pour les modes de sauvegarde périodiques, vous pouvez configurer la redondance des données au niveau du compte. Il existe trois options de redondance pour le stockage de sauvegarde. Il s’agit de la redondance locale, de la redondance interzone et de la géoredondance. Pour en savoir plus, découvrez comment [configurer la redondance de sauvegarde](configure-periodic-backup-restore.md#configure-backup-interval-retention) à l’aide du portail.

## <a name="use-azure-policy-to-enforce-the-residency-requirements"></a>Utiliser Azure Policy pour appliquer les exigences de résidence

Si vous avez des exigences en matière de résidence des données, qui vous obligent à conserver toutes vos données dans une seule région Azure, vous pouvez appliquer des sauvegardes redondantes interzones ou localement redondantes pour votre compte à l’aide d’Azure Policy.  Vous pouvez également appliquer une stratégie en vertu de laquelle les comptes Azure Cosmos DB ne sont pas géo-répliqués vers d’autres régions.

Azure Policy est un service que vous pouvez utiliser pour créer, attribuer et gérer des stratégies qui appliquent des règles à des ressources Azure. Lorsque vous utilisez Azure Policy, ces ressources restent conformes à vos normes d’entreprise et contrats de niveau de service. Pour plus d’informations, découvrez comment utiliser [Azure Policy](policy.md) pour implémenter la gouvernance et les contrôles pour les ressources Azure Cosmos DB

## <a name="next-steps"></a>Étapes suivantes

* Configurer et gérer une stratégie de sauvegarde périodique à l’aide du [portail Azure](configure-periodic-backup-restore.md)
* Provisionnez la sauvegarde continue avec le [portail Azure](provision-account-continuous-backup.md#provision-portal), [PowerShell](provision-account-continuous-backup.md#provision-powershell), l’interface [CLI](provision-account-continuous-backup.md#provision-cli) ou [Azure Resource Manager](provision-account-continuous-backup.md#provision-arm-template).
* Restaurez le compte de sauvegarde continue avec le [portail Azure](restore-account-continuous-backup.md#restore-account-portal), [PowerShell](restore-account-continuous-backup.md#restore-account-powershell), l’interface [CLI](restore-account-continuous-backup.md#restore-account-cli) ou [Azure Resource Manager](restore-account-continuous-backup.md#restore-arm-template).
* [Migrer vers un compte à partir d’une sauvegarde périodique vers une sauvegarde continue](migrate-continuous-backup.md).

