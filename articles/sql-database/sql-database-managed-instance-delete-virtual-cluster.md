---
title: Supprimer le réseau virtuel après la suppression d’Azure SQL Database managed instance | Microsoft Docs
description: Découvrez comment supprimer le réseau virtuel après que la suppression d’Azure SQL Database managed instance.
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
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791371"
---
# <a name="delete-subnet-after-deleting-azure-sql-database-managed-instance"></a>Supprimer le sous-réseau après que la suppression d’Azure SQL Database managed instance

Cet article fournit des instructions sur la façon de supprimer manuellement le sous-réseau après que la suppression de la dernière base de données SQL Azure managed instance qui y résident.

Le [cluster virtuel](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) qui a contenu supprimés instance managée est conservée pendant 12 heures à partir de la suppression de l’instance. Le cluster virtuel est maintenu actif par défaut pour permettre la création plus rapide d’instances gérées dans le même sous-réseau. En conservant un cluster virtuel vide est gratuit. Pendant cette période, le sous-réseau associé au cluster virtuel ne peut pas être supprimé.

Libération immédiate du sous-réseau utilisé par un cluster virtuel vide est possible grâce à la suppression manuelle du cluster virtuel. Suppression du cluster virtuel peut être obtenue via le portail Azure ou de clusters virtuels API.

> [!NOTE]
> Le cluster virtuel ne doit contenir aucune instance managée pour la suppression aboutisse.

## <a name="delete-virtual-cluster-from-azure-portal"></a>Supprimer le cluster virtuel à partir du portail Azure

Pour supprimer un cluster virtuel à l’aide du portail Azure, recherchez les ressources de cluster virtuel à l’aide de la recherche intégrée.

![Recherchez le cluster virtuel.](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Une fois que vous avez localisé le cluster virtuel que vous souhaitez supprimer, sélectionnez cette ressource et sélectionnez l’option de suppression. Vous êtes invité à confirmer la suppression de cluster virtuel.

![Supprimer le cluster virtuel.](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Confirmation que le cluster virtuel a été supprimé est fournie dans les notifications du portail Azure. Suppression réussie du cluster virtuel libère immédiatement le sous-réseau pour une réutilisation ultérieure.

## <a name="delete-virtual-cluster-using-api"></a>Supprimer le cluster virtuel à l’aide des API

Pour supprimer une machine virtuelle de cluster via l’utilisation de l’API les paramètres de l’URI spécifiés dans le [clusters virtuels delete, méthode](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble, consultez [Présentation de Managed Instance](sql-database-managed-instance.md).
- Découvrez l’[architecture de connectivité dans Managed Instance](sql-database-managed-instance-connectivity-architecture.md).
- Apprenez à [modifier un réseau virtuel existant pour Managed Instance](sql-database-managed-instance-configure-vnet-subnet.md).
- Pour accéder à un tutoriel montrant comment créer un réseau virtuel, créer une instance Managed Instance et restaurer une base de données à partir d’une sauvegarde, consultez [Créer une instance Azure SQL Database Managed Instance](sql-database-managed-instance-get-started.md).
- Pour les problèmes liés au DNS, consultez [Configuration d’un DNS personnalisé](sql-database-managed-instance-custom-dns.md).
