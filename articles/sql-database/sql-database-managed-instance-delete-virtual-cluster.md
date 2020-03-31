---
title: Supprimer un sous-réseau après avoir supprimé une instance managée
description: Découvrez comment supprimer un réseau virtuel Azure après la suppression d’une instance gérée Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 06/26/2019
ms.openlocfilehash: 496d67a73207fd17182c31c5adad25c1fbe60c4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820459"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-database-managed-instance"></a>Supprimer un sous-réseau après avoir supprimé une instance managée Azure SQL Database

Cet article fournit des instructions sur la suppression manuelle du sous-réseau après la suppression de la dernière instance managée Azure SQL Database qui y réside.

Les instances managées sont déployées dans des [clusters virtuels](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture). Chaque cluster virtuel est associé à un sous-réseau. Par défaut, le cluster virtuel persiste pendant 12 heures après la dernière suppression d’instance, ce qui vous permet de créer plus rapidement des instances managées dans le même sous-réseau. La conservation d’un cluster virtuel vide n’engendre pas de frais. Pendant cette période, le sous-réseau associé au cluster virtuel ne peut pas être supprimé.

Si vous ne souhaitez pas attendre 12 heures et préférez supprimer le cluster virtuel et son sous-réseau avant ce délai, vous pouvez le faire manuellement. Supprimez le cluster virtuel manuellement à l’aide du portail Azure ou l’API de clusters virtuels.

> [!IMPORTANT]
> - Le cluster virtuel ne doit contenir aucune instance managée pour que la suppression aboutisse. 
> - La suppression d’un cluster virtuel est une opération longue qui dure environ 1,5 heure (consultez [Opérations de gestion des instances managées](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) pour avoir des informations à jour sur le délai de suppression d’un cluster virtuel) au cours de laquelle le cluster virtuel reste visible sur le portail tant que le processus n’est pas terminé.

## <a name="delete-virtual-cluster-from-the-azure-portal"></a>Supprimer le cluster virtuel à partir du portail Azure

Pour supprimer un cluster virtuel à l’aide du portail Azure, recherchez les ressources de cluster virtuel.

![Capture d’écran du portail Azure, avec la zone de recherche en surbrillance](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Une fois que vous avez localisé le cluster virtuel à supprimer, sélectionnez cette ressource et sélectionnez **Supprimer**. Vous êtes invité à confirmer la suppression du cluster virtuel.

![Capture d’écran du tableau de bord Clusters virtuels sur le portail Azure, avec l’option Supprimer en surbrillance](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Les notifications du portail Azure vous confirment que la demande de suppression du cluster virtuel a bien été soumise. L’opération de suppression proprement dite dure environ 1,5 heures, durée pendant laquelle le cluster virtuel reste visible sur le portail. Une fois le processus terminé, le cluster virtuel n’est plus visible et le sous-réseau qui lui est associé est libéré pour être réutilisé.

> [!TIP]
> Si aucune instance gérée ne s’affiche dans le cluster virtuel et que vous ne pouvez pas supprimer le cluster virtuel, assurez-vous que vous n’avez pas un déploiement de l’instance en cours. Cela inclut les déploiements démarrés et annulés qui sont toujours en cours. Cela s’explique par le fait que ces opérations continuent d’utiliser le cluster virtuel, ce qui empêche sa suppression. L’onglet Évaluation des déploiements du groupe de ressources sur lequel l’instance a été déployée indique tous les déploiements en cours d’exécution. Dans ce cas, attendez la fin du déploiement, supprimez l’instance managée et ensuite le cluster virtuel.

## <a name="delete-virtual-cluster-by-using-the-api"></a>Supprimer un cluster virtuel à l’aide de l’API

Pour supprimer un cluster virtuel via l’API, utilisez les paramètres d’URI spécifiés dans la [méthode de suppression de clusters virtuels](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble, consultez [Présentation de Managed Instance](sql-database-managed-instance.md).
- Découvrez l’[architecture de connectivité dans Managed Instance](sql-database-managed-instance-connectivity-architecture.md).
- Apprenez à [modifier un réseau virtuel existant pour Managed Instance](sql-database-managed-instance-configure-vnet-subnet.md).
- Pour accéder à un tutoriel montrant comment créer un réseau virtuel, créer une instance Managed Instance et restaurer une base de données à partir d’une sauvegarde, consultez [Créer une instance Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Pour les problèmes liés au DNS, consultez [Configuration d’un DNS personnalisé](sql-database-managed-instance-custom-dns.md).
