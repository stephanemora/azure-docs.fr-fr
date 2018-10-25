---
title: Vue d’ensemble d’Azure Database Migration Service | Microsoft Docs
description: Vue d’ensemble d’Azure Database Migration Service, qui fournit des migrations transparentes de nombreuses sources de base de données vers des plateformes de données Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: ''
ms.reviewer: douglasl
ms.service: database-migration
ms.workload: data-services
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: 064d7ca2f544dbf1c968310842e322d8c0db2ca1
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48884440"
---
# <a name="what-is-the-azure-database-migration-service"></a>Qu’est-ce qu’Azure Database Migration Service ?
Azure Database Migration Service est un service entièrement géré conçu pour permettre des migrations transparentes de plusieurs sources de base de données vers des plateformes de données Azure avec un temps d’arrêt minime (migration en ligne).

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>Migrer des bases de données vers Azure avec des outils bien connus
Azure Database Migration Service intègre des fonctionnalités de nos services et outils existants. Il fournit aux clients une solution complète hautement disponible. Le service utilise [l’Assistant de migration des données](http://aka.ms/dma) pour générer des rapports d’évaluation qui fournissent des recommandations concernant les modifications requises avant d’effectuer une migration. Libre à vous d’effectuer ou non une correction nécessaire. Quand vous êtes prêt à commencer le processus de migration, Azure Database Migration Service effectue toutes les étapes associées. Vous pouvez lancer vos projets de migration et ne plus vous en occuper, car vous savez que le processus s’appuie sur les bonnes pratiques déterminées par Microsoft.

> [!NOTE]
> Effectuer une migration en ligne à l’aide d’Azure Database Migration Service nécessite la création d’une instance en fonction du niveau tarifaire critique pour l’entreprise (préversion).

## <a name="regional-availability"></a>Disponibilité régionale
Azure Database Migration Service est actuellement disponible dans les régions suivantes :

![Disponibilité régionale d’Azure Database Migration Service](media\overview\dms-regional-availability.png)

Pour accéder aux informations les plus récentes sur la disponibilité régionale d’Azure Database Migration Service, sur le site consacré à l’infrastructure mondiale d’Azure, consultez la rubrique [Produits disponibles par région](https://azure.microsoft.com/global-infrastructure/services/).

## <a name="next-steps"></a>Étapes suivantes
- [Créer une instance d’Azure Database Migration Service à l’aide du portail Azure](quickstart-create-data-migration-service-portal.md).
- [Migrer SQL Server vers Azure SQL Database](tutorial-sql-server-to-azure-sql.md).
- [Vue d’ensemble des prérequis pour l’utilisation d’Azure Database Migration Service](pre-reqs.md).
- [FAQ sur l’utilisation d’Azure Database Migration Service](faq.md).
