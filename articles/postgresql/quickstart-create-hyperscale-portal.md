---
title: Azure Database pour PostgreSQL – Démarrage rapide d’Hyperscale (Citus) (préversion)
description: Guide de démarrage rapide pour créer et interroger des tables distribuées sur Azure Database pour PostgreSQL Hyperscale (Citus) (préversion).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/06/2019
ms.openlocfilehash: 30de4da43569abf4d7bd668fd0fa481ecac23f4d
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080027"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-preview-in-the-azure-portal"></a>Démarrage rapide : Créer une base de données Azure Database pour PostgreSQL - Hyperscale (Citus) dans le portail Azure

Azure Database pour PostgreSQL est un service administré que vous utilisez pour exécuter, gérer et mettre à l’échelle des bases de données PostgreSQL hautement disponibles dans le cloud. Ce guide de démarrage rapide vous montre comment créer un groupe de serveurs Azure Database pour PostgreSQL - Hyperscale (Citus) (préversion) avec le portail Azure. Vous allez explorer les données distribuées : partitionnement de tables entre les nœuds, ingestion d’exemples de données et exécution de requêtes qui s’exécutent sur plusieurs nœuds.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Créer une base de données Azure pour PostgreSQL

Pour créer un serveur de base de données Azure pour PostgreSQL, suivez les étapes ci-après :
1. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Bases de données** dans la page **Nouveau**, puis **Base de données Azure pour PostgreSQL** dans la page **Bases de données**.
3. Pour l’option de déploiement, cliquez sur le bouton **Créer** sous **Groupe de serveurs Hyperscale (Citus) - PRÉVERSION.**
4. Remplissez le formulaire de détails du nouveau serveur avec les informations suivantes :
   - Groupe de ressources : cliquez sur le lien **Créer** sous la zone de texte pour ce champ. Entrez un nom comme **myresourcegroup**.
   - Nom du groupe de serveurs : entrez un nom unique pour le nouveau groupe de serveurs, qui sera également utilisé pour un sous-domaine de serveur.
   - Nom d’utilisateur de l’administrateur : entrez un nom d’utilisateur unique, il sera utilisé plus tard pour se connecter à la base de données.
   - Mot de passe : doit contenir au moins huit caractères appartenant à trois des catégories suivantes : - lettres majuscules non accentuées, lettres minuscules non accentuées, chiffres (de 0 à 9) et caractères non alphanumériques (!, $, #, %, etc.).
   - Emplacement : utilisez l’emplacement le plus proche de vos utilisateurs pour leur donner l’accès le plus rapide possible aux données.

   > [!IMPORTANT]
   > La connexion d’administrateur serveur et le mot de passe que vous spécifiez ici seront requis plus loin dans ce guide de démarrage rapide pour la connexion au serveur et à ses bases de données. Retenez ou enregistrez ces informations pour une utilisation ultérieure.

5. Cliquez sur **Configurer le groupe de serveurs**. Laissez inchangés les paramètres de cette section et cliquez sur **Enregistrer**.
6. Cliquez sur **Vérifier + créer**, puis sur **Créer** pour provisionner le serveur. Le provisionnement prend quelques minutes.
7. La page redirige vers la supervision du déploiement. Quand l’état passe de **Votre déploiement est en cours** à **Votre déploiement a été effectué**, cliquez sur l’élément de menu **Sorties** à gauche de la page.
8. La page des sorties contient un nom d’hôte de coordinateur avec un bouton en regard de celui-ci pour copier la valeur dans le Presse-papiers. Prenez note de ces informations pour une utilisation ultérieure.

## <a name="configure-a-server-level-firewall-rule"></a>Configurer une règle de pare-feu au niveau du serveur

Le service Azure Database pour PostgreSQL - Hyperscale (Citus) (préversion) utilise un pare-feu au niveau du serveur. Par défaut, le pare-feu empêche les outils et les applications externes de se connecter au nœud coordinateur et aux bases de données qui s’y trouvent. Nous devons ajouter une règle pour ouvrir le pare-feu pour une plage d’adresses IP spécifiques.

1. Dans la section **Sorties** où vous avez précédemment copié le nom d’hôte du nœud coordinateur, recliquez sur l’élément de menu **Vue d’ensemble**.

2. Le nom du groupe de mise à l’échelle de votre déploiement sera préfixé de « sg- ». Recherchez-le dans la liste de ressources, puis cliquez sur celui-ci.

3. Cliquez sur **Pare-feu** sous **Sécurité** dans le menu de gauche.

4. Cliquez sur le lien **+ Ajouter une règle de pare-feu pour l’adresse IP du client**. Enfin, cliquez sur le bouton **Enregistrer**.

5. Cliquez sur **Enregistrer**.

   > [!NOTE]
   > Le serveur Azure PostgreSQL communique sur le port 5432. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 5432 peut être bloqué par le pare-feu de votre réseau. Dans ce cas, vous ne pouvez pas vous connecter à votre serveur Azure SQL Database, sauf si votre service informatique ouvre le port 5432.
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>Se connecter à la base de données avec psql dans Cloud Shell

Nous allons maintenant utiliser l’utilitaire de ligne de commande [psql](https://www.postgresql.org/docs/current/app-psql.html) pour nous connecter au serveur Azure Database pour PostgreSQL.
1. Exécutez Azure Cloud Shell via l’icône de la console dans le volet de navigation supérieur.

   ![Base de données Azure pour PostgreSQL - Icône de la console Azure Cloud Shell](./media/quickstart-create-hyperscale-portal/psql-cloud-shell.png)

2. Azure Cloud Shell s’ouvre dans votre navigateur, ce qui vous permet de saisir des commande bash.

   ![Base de données Azure pour PostgreSQL - Invite bash Azure Shell](./media/quickstart-create-hyperscale-portal/psql-bash.png)

3. À l’invite Cloud Shell, connectez-vous à votre serveur de base de données Azure pour PostgreSQL en utilisant les commandes psql. Le format suivant est utilisé pour se connecter à un serveur de base de données Azure pour PostgreSQL avec l’utilitaire [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) :
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   Par exemple, la commande suivante connecte à la base de données par défaut appelée **citus** sur votre serveur PostgreSQL **mydemoserver.postgres.database.azure.com** en utilisant les informations d’identification d’accès. À l’invite, entrez votre mot de passe d’administrateur du serveur.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

## <a name="create-and-distribute-tables"></a>Créer et distribuer des tables

Une fois connecté au nœud coordinateur Hyperscale avec psql, vous pouvez effectuer certaines tâches de base.

Il existe trois types de tables dans les serveurs Hyperscale :

- Tables distribuées ou partitionnées (réparties dans le but de faciliter la scalabilité pour les performances et la parallélisation)
- Tables de référence (plusieurs copies gérées)
- Tables locales (souvent utilisées pour les tables d’administration interne)

Dans ce guide de démarrage rapide, nous abordons principalement les tables distribuées.

Le modèle de données avec lequel nous allons travailler est simple : des données utilisateur et des données d’événements provenant de GitHub. Les événements incluent la création de duplication (fork), des commits git liés à une organisation, etc.

Une fois que vous vous êtes connecté via psql, nous créons nos tables. Dans la console psql, exécutez :

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

Le champ `payload` de `github_events` a le type de données JSONB. JSONB est le type de données JSON sous forme binaire dans Postgres. Ceci facilite le stockage d’un schéma plus flexible dans une seule colonne.

Postgres peut créer un index `GIN` sur ce type, qui indexe chaque clé et chaque valeur qui s’y trouve. Avec un index, il devient rapide et facile d’interroger la charge utile avec différentes conditions. Poursuivons et créons deux index avant de charger nos données. Dans psql :

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Ensuite, nous allons placer ces tables Postgres sur le nœud coordinateur et indiquer à Hyperscale de les partitionner sur les workers. Pour cela, nous allons exécuter une requête pour chaque table, en spécifiant la clé sur laquelle la partitionner. Dans notre exemple, nous allons partitionner la table des événements et des utilisateurs sur `user_id` :

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

Nous sommes prêts à charger des données. Téléchargez les deux exemples de fichiers [users.csv](https://examples.citusdata.com/users.csv) et [events.csv](https://examples.citusdata.com/events.csv). Après avoir téléchargé les fichiers, connectez-vous à la base de données avec psql, en veillant à exécuter psql à partir du répertoire contenant les fichiers que vous avez téléchargés. Chargez les données avec la commande `\copy` :

```sql
\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Exécuter des requêtes

Il est maintenant temps d’exécuter quelques requêtes. Commençons par une simple instruction `count (*)` pour voir la quantité de données que nous avons chargées :

```sql
SELECT count(*) from github_events;
```

Ceci a parfaitement fonctionné. Nous reviendrons sous peu à ce genre d’agrégation, mais pour l’instant, nous allons examiner quelques autres requêtes. Dans la colonne `payload` JSONB se trouvent différentes données, mais elles varient selon le type d’événement. Les événements `PushEvent` contiennent une taille qui inclut le nombre de commits distincts pour l’envoi (push). Nous pouvons l’utiliser pour trouver le nombre total de commits par heure :

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Jusqu’à présent, les requêtes impliquaient exclusivement github\_events, mais nous pouvons combiner ces informations avec github\_users. Comme nous avons partitionné les utilisateurs et les événements sur le même identificateur (`user_id`), les lignes des deux tables avec des ID d’utilisateur correspondants sont [colocalisées](http://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation) sur les mêmes nœuds de base de données et peuvent facilement faire l’objet d’une jointure.

Si nous faisons une jointure sur `user_id`, Hyperscale peut procéder à l’exécution de la jointure au niveau des partitions pour permettre une exécution en parallèle sur les nœuds worker. Par exemple, recherchons les utilisateurs qui ont créé le plus grand nombre de dépôts :

```sql
SELECT login, count(*)
FROM github_events ge
JOIN github_users gu
ON ge.user_id = gu.user_id
WHERE event_type = 'CreateEvent' AND
      payload @> '{"ref_type": "repository"}'
GROUP BY login
ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Au cours des étapes précédentes, vous avez créé des ressources Azure au sein d’un groupe de serveurs. Si vous pensez ne pas avoir besoin de ces ressources à l’avenir, supprimez le groupe de serveurs. Cliquez sur le bouton **Supprimer** dans la page **Vue d’ensemble** pour votre groupe de serveurs. Quand vous y êtes invité dans une page contextuelle, confirmez le nom du groupe de serveurs, puis cliquez sur le bouton **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez découvert comment configurer un groupe de serveurs Hyperscale (Citus). Vous vous êtes connecté avec psql, vous avez créé un schéma et vous avez distribué des données.

Suivez maintenant un tutoriel pour créer des applications multilocataires scalables.
> [!div class="nextstepaction"]
> [Concevoir une base de données multilocataire](https://aka.ms/hyperscale-tutorial-multi-tenant)
