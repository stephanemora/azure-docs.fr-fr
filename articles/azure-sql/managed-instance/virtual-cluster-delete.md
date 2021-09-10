---
title: Supprimer un sous-réseau après avoir supprimé une instance gérée SQL
description: Découvrez comment supprimer un réseau virtuel Azure après la suppression d’une instance gérée SQL Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: mathoma
ms.date: 08/20/2021
ms.openlocfilehash: d61a3f5a3dac8cfb6215bdfd6ff1c457c4e14150
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123214429"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-managed-instance"></a>Supprimer un sous-réseau après avoir supprimé une instance gérée SQL Azure
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Cet article fournit des instructions sur la suppression manuelle d’un sous-réseau après la suppression de la dernière instance gérée SQL Azure qui y réside.

Les instances gérées SQL sont déployées dans des [clusters virtuels](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture). Chaque cluster virtuel est associé à un sous-réseau et déployé avec la première création d’instance. De la même façon, un cluster virtuel est automatiquement supprimé en même temps que la suppression de la dernière instance, laissant le sous-réseau vide et prêt à être supprimé. Aucune action manuelle n’est nécessaire sur le cluster virtuel pour libérer le sous-réseau. Une fois le dernier cluster virtuel supprimé, vous pouvez supprimer le sous-réseau

Dans de rares cas, la création d’une opération peut échouer et aboutir à un cluster virtuel vide déployé. En outre, comme la création d’instance [peut être annulée](management-operations-cancel.md), il est possible qu’un cluster virtuel soit déployé avec des instances qui y résident, en état d’échec. La suppression du cluster virtuel sera automatiquement lancée dans ces situations et annulée en arrière-plan.

> [!NOTE]
> Aucuns frais ne sont facturés pour la conservation d’une instance ou d’un cluster virtuel vide qui n’a pas pu être créé.

> [!IMPORTANT]
> - Le cluster virtuel ne doit contenir aucune instance gérée SQL pour que la suppression aboutisse. Cela n’inclut pas les instances qui n’ont pas pu être créées. 
> - La suppression d’un cluster virtuel est une opération longue qui dure environ 1,5 heure (pour avoir des informations à jour sur le délai de suppression d’un cluster virtuel, consultez [Opérations de gestion de SQL Managed Instance](management-operations-overview.md)). Le cluster virtuel reste visible sur le portail tant que le processus n’est pas terminé.
> - Une seule opération de suppression peut être exécutée sur le cluster virtuel. Toutes les demandes de suppression ultérieures initiées par le client généreront une erreur, car l’opération de suppression est déjà en cours.

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>Supprimer un cluster virtuel à partir du portail Azure

> [!IMPORTANT]
> À partir du 1er septembre 2021. tous les clusters virtuels sont automatiquement supprimés lors de la suppression de la dernière instance du cluster. La suppression manuelle du cluster virtuel n’est plus nécessaire.

Pour supprimer un cluster virtuel à l’aide du portail Azure, recherchez les ressources de cluster virtuel.

> [!div class="mx-imgBorder"]
> ![Capture d’écran du portail Azure, avec la zone de recherche en surbrillance](./media/virtual-cluster-delete/virtual-clusters-search.png)

Une fois que vous avez localisé le cluster virtuel à supprimer, sélectionnez cette ressource et sélectionnez **Supprimer**. Vous êtes invité à confirmer la suppression du cluster virtuel.

> [!div class="mx-imgBorder"]
> ![Capture d’écran du tableau de bord Clusters virtuels sur le portail Azure, avec l’option Supprimer en surbrillance](./media/virtual-cluster-delete/virtual-clusters-delete.png)

Les notifications du portail Azure vous confirment que la demande de suppression du cluster virtuel a bien été soumise. L’opération de suppression proprement dite dure environ 1,5 heure, durée pendant laquelle le cluster virtuel reste visible sur le portail. Une fois le processus terminé, le cluster virtuel n’est plus visible et le sous-réseau qui lui est associé est libéré pour être réutilisé.

> [!TIP]
> Si aucune instance gérée SQL ne figure dans le cluster virtuel et si vous ne pouvez pas supprimer celui-ci, assurez-vous qu’il n’y a pas de déploiement d’instance en cours. Cela inclut les déploiements démarrés et annulés qui sont toujours en cours. Cela s’explique par le fait que ces opérations continuent d’utiliser le cluster virtuel, ce qui empêche sa suppression. Vous pouvez consulter l’onglet **Déploiements** du groupe de ressources sur lequel l’instance a été déployée pour voir tous les déploiements en cours d’exécution. Dans ce cas, attendez la fin du déploiement, puis supprimez l’instance managée SQL. Le cluster virtuel sera supprimé de façon synchrone dans le cadre de la suppression de l’instance.

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>Supprimer un cluster virtuel à l’aide de l’API

Pour supprimer un cluster virtuel via l’API, utilisez les paramètres d’URI spécifiés dans la [méthode de suppression de clusters virtuels](/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble, consultez [Présentation d’Azure SQL Managed Instance](sql-managed-instance-paas-overview.md).
- Découvrez l’[architecture de connectivité dans SQL Managed Instance](connectivity-architecture-overview.md).
- Apprenez à [modifier un réseau virtuel existant pour SQL Managed Instance](vnet-existing-add-subnet.md).
- Pour accéder à un tutoriel montrant comment créer un réseau virtuel, créer une instance gérée Azure SQL et restaurer une base de données à partir d’une sauvegarde, consultez [Créer une instance gérée Azure SQL (portail)](instance-create-quickstart.md).
- Pour les problèmes liés au DNS, consultez [Configurer un DNS personnalisé](custom-dns-configure.md).
