---
title: Gérer les réplicas en lecture - Portail Azure - Azure Database pour PostgreSQL - Hyperscale (Citus)
description: Découvrez comment gérer les réplicas en lecture pour Azure Database pour PostgreSQL - Hyperscale (Citus) depuis le portail Azure.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 08/03/2021
ms.openlocfilehash: 74c9df5ee9eb6cd6a61104b4c9cce91b284dbb29
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524213"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---hyperscale-citus-from-the-azure-portal"></a>Créez et gérez les réplicas en lecture dans Azure Database pour PostgreSQL - Hyperscale (Citus) depuis le portail Azure.

Dans cet article, vous allez apprendre à créer et gérer des réplicas en lecture dans Hyperscale (Citus) à partir du portail Azure. Pour en savoir plus sur les réplicas en lecture, consultez [vue d’ensemble](concepts-hyperscale-read-replicas.md).


## <a name="prerequisites"></a>Prérequis

Un [groupe de serveurs Hyperscale (Citus)](quickstart-create-hyperscale-portal.md) en tant que groupe principal.

## <a name="create-a-read-replica"></a>Créer un réplica en lecture

Pour créer un réplica en lecture, effectuez les étapes suivantes :

1. Sélectionnez un groupe de serveurs Azure Database pour PostgreSQL à utiliser en tant que serveur principal. 

2. Dans la barre latérale du groupe de serveurs, sous **Gestion des groupes de serveurs**, sélectionnez **Réplication**.

3. Sélectionnez **Ajouter un réplica**.

4. Entrez un nom pour le réplica en lecture. 

5. Sélectionnez **OK** pour confirmer la création du réplica.

Une fois le réplica en lecture créé, vous pouvez le voir dans la fenêtre **Réplication**.

> [!IMPORTANT]
>
> Consultez la [section de considérations relatives à la vue d’ensemble de réplica en lecture](concepts-hyperscale-read-replicas.md#considerations).
>
> Avant de modifier un paramètre du groupe de serveurs principal, remplacez la valeur du paramètre du réplica par une valeur supérieure ou égale. Vous garantissez ainsi l’alignement du réplica sur les changements apportés au serveur maître.

## <a name="delete-a-primary-server-group"></a>Supprimer un groupe de serveurs principal

Pour supprimer un groupe de serveurs principal, suivez les mêmes étapes que celles qui permettent de supprimer un groupe de serveurs autonomes Hyperscale (Citus). 

> [!IMPORTANT]
>
> Quand vous supprimez un groupe de serveurs principal, la réplication est arrêtée sur tous les réplicas en lecture. Les réplicas en lecture deviennent des groupes de serveurs autonomes qui prennent désormais en charge les lectures et les écritures.

Pour supprimer un groupe de serveurs du portail Azure, effectuez les étapes suivantes :

1. Dans le portail Azure, sélectionnez votre groupe de serveurs Azure Database pour PostgreSQL principal.

2. Ouvrez la page **Vue d’ensemble** du groupe de serveurs. Sélectionnez **Supprimer**.
 
3. Entrez le nom du groupe de serveurs principal à supprimer. Sélectionnez **Supprimer** pour confirmer la suppression du groupe de serveurs principal.
 

## <a name="delete-a-replica"></a>Supprimer un réplica

Vous pouvez supprimer un réplica en lecture de la même façon que vous supprimez un groupe de serveurs principal.

- Dans le portail Azure, ouvrez la page **Vue d’ensemble** du réplica en lecture. Sélectionnez **Supprimer**.
 
Vous pouvez également supprimer le réplica en lecture de la fenêtre **Réplication** en effectuant les étapes suivantes :

1. Dans le Portail Azure, sélectionnez votre groupe de serveurs Hyperscale (Citus) principal.

2. Dans le menu du groupe de serveurs, sous **Gestion des groupes de serveurs**, sélectionnez **Réplication**.

3. Sélectionnez le réplica en lecture à supprimer.
 
4. Sélectionnez **Supprimer le réplica**.
 
5. Entrez le nom du réplica à supprimer. Sélectionnez **Supprimer** pour confirmer la suppression du réplica.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez-en plus sur les [réplicas en lecture dans Azure Database pour PostgreSQL - Hyperscale (Citus)](concepts-hyperscale-read-replicas.md).
