---
title: Supprimer le réseau virtuel après avoir supprimé l’instance managée Azure SQL Database | Microsoft Docs
description: Découvrez comment supprimer le réseau virtuel après la suppression de l’instance managée Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: management
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: 61f6c25031c4906e65c2f75a7679600741e8311a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65791371"
---
# <a name="delete-subnet-after-deleting-azure-sql-database-managed-instance"></a>Supprimer le sous-réseau après avoir supprimé l’instance managée Azure SQL Database

Cet article fournit des instructions sur la suppression manuelle du sous-réseau après la suppression de la dernière instance managée Azure SQL Database qui y réside.

Le [cluster virtuel](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) qui contenait l’instance managée supprimée est conservé pendant 12 heures après la suppression de l’instance. Ce cluster virtuel reste actif par défaut pour permettre la création plus rapide d’instances managées dans le même sous-réseau. Conserver un cluster virtuel vide est gratuit. Pendant cette période, le sous-réseau associé au cluster virtuel ne peut pas être supprimé.

La libération immédiate du sous-réseau utilisé par un cluster virtuel vide est possible grâce à la suppression manuelle du cluster virtuel. La suppression du cluster virtuel peut être effectuée via le portail Azure ou l’API de clusters virtuels.

> [!NOTE]
> Le cluster virtuel ne doit contenir aucune instance managée pour que la suppression aboutisse.

## <a name="delete-virtual-cluster-from-azure-portal"></a>Supprimer le cluster virtuel à partir du portail Azure

Pour supprimer un cluster virtuel à l’aide du portail Azure, recherchez les ressources de cluster virtuel à l’aide de la recherche intégrée.

![Recherchez le cluster virtuel.](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Une fois que vous avez localisé le cluster virtuel à supprimer, sélectionnez cette ressource et choisissez l’option Supprimer. Vous êtes invité à confirmer la suppression du cluster virtuel.

![Supprimez le cluster virtuel.](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

La confirmation de la suppression du cluster virtuel est fournie dans les notifications du portail Azure. La suppression du cluster virtuel libère immédiatement le sous-réseau pour une réutilisation ultérieure.

## <a name="delete-virtual-cluster-using-api"></a>Supprimer le cluster virtuel via l’API

Pour supprimer un cluster virtuel via l’API, utilisez les paramètres d’URI spécifiés dans la [méthode de suppression de clusters virtuels](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble, consultez [Présentation de Managed Instance](sql-database-managed-instance.md).
- Découvrez l’[architecture de connectivité dans Managed Instance](sql-database-managed-instance-connectivity-architecture.md).
- Apprenez à [modifier un réseau virtuel existant pour Managed Instance](sql-database-managed-instance-configure-vnet-subnet.md).
- Pour accéder à un tutoriel montrant comment créer un réseau virtuel, créer une instance Managed Instance et restaurer une base de données à partir d’une sauvegarde, consultez [Créer une instance Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Pour les problèmes liés au DNS, consultez [Configuration d’un DNS personnalisé](sql-database-managed-instance-custom-dns.md).
