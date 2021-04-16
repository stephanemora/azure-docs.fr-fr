---
title: Comment répondre aux exigences de résidence des données dans Azure Cosmos DB
description: Découvrez comment répondre aux exigences de résidence des données dans Azure Cosmos DB afin que vos données et sauvegardes restent dans une seule région.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: c5f8a4361774368e3934d1e2b16c8311876f5caa
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491863"
---
# <a name="how-to-meet-data-residency-requirements-in-azure-cosmos-db"></a>Comment répondre aux exigences de résidence des données dans Azure Cosmos DB

Dans Azure Cosmos DB, vous pouvez configurer vos données et sauvegardes pour qu’elles restent dans une seule région afin de répondre aux [exigences de résidence.](https://azure.microsoft.com/en-us/global-infrastructure/data-residency/)

## <a name="residency-requirements-for-data"></a>Exigences de résidence pour les données

Dans Azure Cosmos DB, vous devez configurer explicitement la réplication des données entre régions. Découvrez comment configurer la géoréplication via le [portail Azure](how-to-manage-database-account.md#addremove-regions-from-your-database-account), à l’aide d’[Azure CLI](scripts/cli/common/regions.md). Pour répondre aux exigences de résidence des données, vous pouvez créer une stratégie Azure qui permet à certaines régions d’empêcher la réplication de données vers des régions indésirables.

## <a name="residency-requirements-for-backups"></a>Exigences de résidence pour les sauvegardes

**Sauvegardes en mode continu** : ces sauvegardes sont résidentes par défaut, car elles sont stockées dans un stockage localement redondant ou redondant interzone. Pour en savoir plus, consultez l’article [Sauvegarde continue](continuous-backup-restore-portal.md).

**Sauvegardes en mode périodique** : par défaut, les sauvegardes de compte en mode périodique sont stockées dans un stockage géoredondant. Pour les modes de sauvegarde périodiques, vous pouvez configurer la redondance des données au niveau du compte. Il existe trois options de redondance pour le stockage de sauvegarde. Il s’agit de la redondance locale, de la redondance interzone et de la géoredondance. Pour en savoir plus, découvrez comment [configurer la redondance de sauvegarde](configure-periodic-backup-restore.md#configure-backup-interval-retention) à l’aide du portail.

## <a name="use-azure-policy-to-enforce-the-residency-requirements"></a>Utiliser Azure Policy pour appliquer les exigences de résidence

Si vous avez des exigences en matière de résidence des données, qui vous obligent à conserver toutes vos données dans une seule région Azure, vous pouvez appliquer des sauvegardes redondantes interzones ou localement redondantes pour votre compte à l’aide d’Azure Policy.  Vous pouvez également appliquer une stratégie en vertu de laquelle les comptes Azure Cosmos DB ne sont pas géo-répliqués vers d’autres régions.

Azure Policy est un service que vous pouvez utiliser pour créer, attribuer et gérer des stratégies qui appliquent des règles à des ressources Azure. Lorsque vous utilisez Azure Policy, ces ressources restent conformes à vos normes d’entreprise et contrats de niveau de service. Pour plus d’informations, découvrez comment utiliser [Azure Policy](policy.md) pour implémenter la gouvernance et les contrôles pour les ressources Azure Cosmos DB

## <a name="next-steps"></a>Étapes suivantes

* Configurer et gérer une stratégie de sauvegarde périodique à l’aide du [portail Azure](configure-periodic-backup-restore.md)
* Configurez et gérez la sauvegarde continue en utilisant [Portail Azure](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md) ou [Azure Resource Manager](continuous-backup-restore-template.md).
