---
title: Supprimer un sous-réseau après avoir supprimé une instance managée Azure SQL Database | Microsoft Docs
description: Découvrez comment supprimer un réseau virtuel Azure après la suppression d’une instance gérée Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: f6e0b55ad2fbd9b4c45dbd1facaebd4750314c63
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567530"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Supprimer un sous-réseau après avoir supprimé une instance managée Azure SQL Database

Cet article fournit des instructions sur la suppression manuelle du sous-réseau après la suppression de la dernière instance managée Azure SQL Database qui y réside.

SQL Database utilise un [cluster virtuel](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) pour contenir l’instance gérée supprimée. Le cluster virtuel persiste pendant 12 heures après la suppression de l’instance, pour vous permettre de créer rapidement des instances gérées dans le même sous-réseau. La conservation d’un cluster virtuel vide n’engendre pas de frais. Pendant cette période, le sous-réseau associé au cluster virtuel ne peut pas être supprimé.

Si vous ne souhaitez pas attendre 12 heures et préférez supprimer le cluster virtuel et son sous-réseau immédiatement, vous pouvez le faire manuellement. Supprimez le cluster virtuel manuellement à l’aide du portail Azure ou l’API de clusters virtuels.

> [!NOTE]
> Le cluster virtuel ne doit contenir aucune instance managée pour que la suppression aboutisse.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Supprimer le cluster virtuel à partir du portail Azure

Pour supprimer un cluster virtuel à l’aide du portail Azure, recherchez les ressources de cluster virtuel.

![Capture d’écran du portail Azure, avec la zone de recherche en surbrillance](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Une fois que vous avez localisé le cluster virtuel à supprimer, sélectionnez cette ressource et sélectionnez **Supprimer**. Vous êtes invité à confirmer la suppression du cluster virtuel.

![Capture d’écran du tableau de bord Clusters virtuels sur le portail Azure, avec l’option Supprimer en surbrillance](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

La confirmation de la suppression du cluster virtuel est fournie dans la zone de notifications du portail Azure. La suppression du cluster virtuel libère immédiatement le sous-réseau pour une réutilisation.

> [!TIP]
> Si aucune instance gérée ne s’affiche dans le cluster virtuel et que vous ne pouvez pas supprimer le cluster virtuel, assurez-vous que vous n’avez pas un déploiement de l’instance en cours. Cela inclut les déploiements démarrés et annulés qui sont toujours en cours. L’onglet Évaluation des déploiements du groupe de ressources sur lequel l’instance a été déployée indique tous les déploiements en cours d’exécution. Dans ce cas, attendez la fin du déploiement et supprimez l’instance gérée, puis le cluster virtuel.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Supprimer un cluster virtuel à l’aide de l’API

Pour supprimer un cluster virtuel via l’API, utilisez les paramètres d’URI spécifiés dans la [méthode de suppression de clusters virtuels](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble, consultez [Présentation de Managed Instance](sql-database-managed-instance.md).
- Découvrez l’[architecture de connectivité dans Managed Instance](sql-database-managed-instance-connectivity-architecture.md).
- Apprenez à [modifier un réseau virtuel existant pour Managed Instance](sql-database-managed-instance-configure-vnet-subnet.md).
- Pour accéder à un tutoriel montrant comment créer un réseau virtuel, créer une instance Managed Instance et restaurer une base de données à partir d’une sauvegarde, consultez [Créer une instance Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Pour les problèmes liés au DNS, consultez [Configuration d’un DNS personnalisé](sql-database-managed-instance-custom-dns.md).
