---
title: Créer des utilisateurs - Hyperscale (Citus) - Azure Database pour PostgreSQL
description: Cet article décrit comment vous pouvez créer des comptes d’utilisateurs pour interagir avec un serveur Azure Database pour PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d093d4c23fcc44e7e9f3461f875607926f4b612d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977571"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Créer des utilisateurs dans Azure Database pour PostgreSQL - Hyperscale (Citus)

Cet article décrit comment créer des utilisateurs dans un groupe de serveurs Hyperscale (Citus). Pour en savoir plus sur les utilisateurs de l’abonnement Azure et leurs privilèges, consultez l’article [Contrôle d’accès en fonction du rôle (RBAC) Azure](../role-based-access-control/built-in-roles.md) ou lisez [comment personnaliser les rôles](../role-based-access-control/custom-roles.md).

## <a name="the-server-admin-account"></a>Compte d’administrateur de serveur

Un groupe de serveurs Hyperscale (Citus) nouvellement créé est fourni avec plusieurs rôles prédéfinis :

* Les [rôles PostgreSQL par défaut](https://www.postgresql.org/docs/current/default-roles.html)
* *azure_pg_admin*
* *postgres*
* *citus*

Le moteur PostgreSQL utilise des privilèges pour contrôler l’accès aux objets de base de données, comme indiqué dans la [documentation du produit PostgreSQL](https://www.postgresql.org/docs/current/static/sql-createrole.html).
Votre utilisateur administrateur de serveur *citus* est un membre du rôle *azure_pg_admin*.
Toutefois, il ne fait pas partie du rôle *postgre* (super utilisateur).  Étant donné qu’Hyperscale est un service PaaS géré, seul Microsoft fait partie du rôle de super utilisateur. L’utilisateur *citus* dispose d’autorisations limitées et ne peut pas créer de nouvelles bases de données.

## <a name="how-to-create-additional-users"></a>Comment créer des utilisateurs supplémentaires

Le compte administrateur *citus* ne dispose pas des autorisations nécessaires pour créer des utilisateurs supplémentaires. Pour ajouter un utilisateur, utilisez le portail Azure.

1. Accédez à la page **Rôles** pour votre groupe de serveurs Hyperscale, puis cliquez sur **+ Ajouter** :

   ![La page Rôles](media/howto-hyperscale-create-users/1-role-page.png)

2. Saisissez le nom de rôle et le mot de passe. Cliquez sur **Enregistrer**.

   ![Ajouter un rôle](media/howto-hyperscale-create-users/2-add-user-fields.png)

L’utilisateur est créé sur le nœud coordinateur du groupe de serveurs et est propagé à tous les nœuds Worker.

## <a name="how-to-delete-a-user-or-change-their-password"></a>Suppression d’un utilisateur ou modification de son mot de passe

Accédez à la page **Rôles** pour votre groupe de serveurs Hyperscale, puis cliquez sur l’ellipse **...** à côté d’un utilisateur. L’ellipse ouvre un menu pour supprimer l’utilisateur ou réinitialiser son mot de passe.

   ![Modifier un rôle](media/howto-hyperscale-create-users/edit-role.png)

Le rôle *citus* est privilégié et ne peut pas être supprimé.

## <a name="how-to-modify-privileges-for-role"></a>Comment modifier des privilèges pour un rôle

De nouveaux rôles sont couramment utilisés pour fournir un accès à la base de données avec des privilèges restreints. Pour modifier des privilèges d’utilisateur, utilisez des commandes PostgreSQL standard, à l’aide d’un outil tel que PgAdmin ou psql. (Voir [Connexion avec psql](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql) dans le démarrage rapide Hyperscale (Citus).)

Par exemple, pour autoriser *db_user* à lire *mytable*, accordez l’autorisation :

```sql
GRANT SELECT ON mytable TO db_user;
```

Hyperscale (Citus) propage les instructions GRANT sur une table unique à travers l’ensemble du cluster, en les appliquant à tous les nœuds Worker. Toutefois, les allocations à l’échelle du système (par exemple, pour toutes les tables d’un schéma) doivent être exécutées sur chaque nœud.  Utilisez la fonction d’assistance *run_command_on_workers()* :

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="next-steps"></a>Étapes suivantes

Ouvrez le pare-feu pour les adresses IP des machines des nouveaux utilisateurs afin de leur permettre de se connecter : [Créer et gérer des règles de pare-feu Hyperscale (Citus) à l’aide du portail Azure](howto-hyperscale-manage-firewall-using-portal.md).

Pour plus d’informations sur la gestion des comptes d’utilisateur de base de données, consultez la documentation de PostgreSQL :

* [Rôles et privilèges de base de données](https://www.postgresql.org/docs/current/static/user-manag.html)
* [Syntaxe GRANT](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [Privilèges](https://www.postgresql.org/docs/current/static/ddl-priv.html)
