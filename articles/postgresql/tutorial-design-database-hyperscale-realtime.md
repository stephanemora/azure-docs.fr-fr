---
title: 'Tutoriel : Concevoir un tableau de bord en temps réel avec Azure Database pour PostgreSQL – Hyperscale (Citus) (préversion)'
description: Ce tutoriel illustre comment créer, remplir et interroger des tables distribuées dans Azure Database pour PostgreSQL Hyperscale (Citus) (préversion).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: 9f3473d83678ffea888dad736a9620006b2961f7
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406391"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>Didacticiel : Concevoir un tableau de bord analytique en temps réel avec Azure Database pour PostgreSQL – Hyperscale (Citus) (préversion)

Dans ce tutoriel, vous utilisez Azure Database pour PostgreSQL - Hyperscale (Citus) (préversion) pour apprendre à :

> [!div class="checklist"]
> * Créer un groupe de serveurs Hyperscale (Citus)
> * Utiliser l’utilitaire psql pour créer un schéma
> * Partitionner des tables entre des nœuds
> * Générer un exemple de données
> * Effectuer des cumuls
> * Interroger des données brutes et agrégées
> * Faire expirer des données

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Créer une base de données Azure pour PostgreSQL

Pour créer un serveur de base de données Azure pour PostgreSQL, suivez les étapes ci-après :
1. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Bases de données** dans la page **Nouveau**, puis **Base de données Azure pour PostgreSQL** dans la page **Bases de données**.
3. Pour l’option de déploiement, cliquez sur le bouton **Créer** sous **Groupe de serveurs Hyperscale (Citus) - PRÉVERSION.**
4. Remplissez le formulaire de détails du nouveau serveur avec les informations suivantes :
   - Groupe de ressources : cliquez sur le lien **Créer** sous la zone de texte pour ce champ. Entrez un nom tel que **myresourcegroup**.
   - Nom du groupe de serveurs : **mydemoserver** (nom d’un serveur, qui correspond au nom DNS et doit être globalement unique).
   - Nom d’utilisateur administrateur : **myadmin** (utilisé plus tard pour la connexion à la base de données).
   - Mot de passe : doit contenir au moins huit caractères appartenant à trois des catégories suivantes : - lettres majuscules non accentuées, lettres minuscules non accentuées, chiffres (de 0 à 9) et caractères non alphanumériques (!, $, #, %, etc.).
   - Emplacement : utilisez l’emplacement le plus proche de vos utilisateurs pour leur donner l’accès le plus rapide possible aux données.

   > [!IMPORTANT]
   > La connexion d’administrateur serveur et le mot de passe que vous spécifiez ici seront requis plus loin dans ce tutoriel pour la connexion au serveur et à ses bases de données. Retenez ou enregistrez ces informations pour une utilisation ultérieure.

5. Cliquez sur **Configurer le groupe de serveurs**. Laissez inchangés les paramètres de cette section et cliquez sur **Enregistrer**.
6. Cliquez sur **Vérifier + créer**, puis sur **Créer** pour provisionner le serveur. Le provisionnement prend quelques minutes.
7. La page redirige vers la supervision du déploiement. Quand l’état passe de **Votre déploiement est en cours** à **Votre déploiement a été effectué**, cliquez sur l’élément de menu **Sorties** à gauche de la page.
8. La page des sorties contient un nom d’hôte de coordinateur avec un bouton en regard de celui-ci pour copier la valeur dans le Presse-papiers. Prenez note de ces informations pour une utilisation ultérieure.

## <a name="configure-a-server-level-firewall-rule"></a>Configurer une règle de pare-feu au niveau du serveur

Le service Azure Database pour PostgreSQL utilise un pare-feu au niveau du serveur. Par défaut, le pare-feu empêche tous les outils et les applications externes de se connecter au serveur et aux bases de données sur le serveur. Vous devez ajouter une règle afin d’ouvrir le pare-feu pour une plage d’adresses IP spécifique.

1. Dans la section **Sorties** où vous avez précédemment copié le nom d’hôte du nœud coordinateur, recliquez sur l’élément de menu **Vue d’ensemble**.

2. Recherchez le groupe de mise à l’échelle de votre déploiement dans la liste des ressources et cliquez dessus. (Son nom est précédé de « sg- ».)

3. Cliquez sur **Pare-feu** sous **Sécurité** dans le menu de gauche.

4. Cliquez sur le lien **+ Ajouter une règle de pare-feu pour l’adresse IP du client**. Enfin, cliquez sur le bouton **Enregistrer**.

5. Cliquez sur **Enregistrer**.

   > [!NOTE]
   > Le serveur Azure PostgreSQL communique sur le port 5432. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 5432 peut être bloqué par le pare-feu de votre réseau. Dans ce cas, vous ne pouvez pas vous connecter à votre serveur Azure SQL Database, sauf si votre service informatique ouvre le port 5432.
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>Se connecter à la base de données avec psql dans Cloud Shell

Nous allons maintenant utiliser l’utilitaire de ligne de commande [psql](https://www.postgresql.org/docs/current/app-psql.html) pour nous connecter au serveur Azure Database pour PostgreSQL.
1. Exécutez Azure Cloud Shell via l’icône de la console dans le volet de navigation supérieur.

   ![Base de données Azure pour PostgreSQL - Icône de la console Azure Cloud Shell](./media/tutorial-design-database-hyperscale-realtime/psql-cloud-shell.png)

2. Azure Cloud Shell s’ouvre dans votre navigateur, ce qui vous permet de saisir des commande bash.

   ![Base de données Azure pour PostgreSQL - Invite bash Azure Shell](./media/tutorial-design-database-hyperscale-realtime/psql-bash.png)

3. À l’invite Cloud Shell, connectez-vous à votre serveur de base de données Azure pour PostgreSQL en utilisant les commandes psql. Le format suivant est utilisé pour se connecter à un serveur de base de données Azure pour PostgreSQL avec l’utilitaire [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) :
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   Par exemple, la commande suivante se connecte à la base de données par défaut appelée **citus** sur votre serveur PostgreSQL **mydemoserver.postgres.database.azure.com** en utilisant les informations d’identification d’accès. À l’invite, entrez votre mot de passe d’administrateur du serveur.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

## <a name="use-psql-utility-to-create-a-schema"></a>Utiliser l’utilitaire psql pour créer un schéma

Une fois que vous êtes connecté à Azure Database pour PostgreSQL - Hyperscale (Citus) (préversion) à l’aide de psql, vous pouvez effectuer certaines tâches de base. Ce tutoriel vous guide lors de l’ingestion de données de trafic à partir d’analytiques web, puis du cumul des données pour fournir des tableaux de bord en temps réel basés sur ces données.

Vous allez créer une table qui consomme toutes vos données de trafic web brutes. Exécutez les commandes suivantes dans le terminal psql :

```sql
CREATE TABLE http_request (
  site_id INT,
  ingest_time TIMESTAMPTZ DEFAULT now(),

  url TEXT,
  request_country TEXT,
  ip_address TEXT,

  status_code INT,
  response_time_msec INT
);
```

Vous allez également créer une table qui conserve vos agrégats par minute et une table qui conserve la position de votre dernier cumul. Exécutez également la commande suivante dans psql :

```sql
CREATE TABLE http_request_1min (
  site_id INT,
  ingest_time TIMESTAMPTZ, -- which minute this row represents

  error_count INT,
  success_count INT,
  request_count INT,
  average_response_time_msec INT,
  CHECK (request_count = error_count + success_count),
  CHECK (ingest_time = date_trunc('minute', ingest_time))
);

CREATE INDEX http_request_1min_idx ON http_request_1min (site_id, ingest_time);

CREATE TABLE latest_rollup (
  minute timestamptz PRIMARY KEY,

  CHECK (minute = date_trunc('minute', minute))
);
```

Vous pouvez localiser les tables nouvellement créées dans la liste des tables avec cette commande psql :

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Partitionner des tables entre des nœuds

Un déploiement hyperscale stocke les lignes des tables sur des nœuds différents selon la valeur d’une colonne désignée par l’utilisateur. Cette « colonne de distribution » marque la façon dont les données sont partitionnées entre les nœuds.

Nous allons définir la colonne de distribution sur site\_id, la clé de partitionnement. Dans psql, exécutez les fonctions suivantes :

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

## <a name="generate-sample-data"></a>Générer un exemple de données

Votre groupe de serveurs doit à présent être prêt à recevoir des données. Vous pouvez exécuter ce qui suit localement à partir de votre connexion `psql` pour insérer des données en continu.

```sql
DO $$
  BEGIN LOOP
    INSERT INTO http_request (
      site_id, ingest_time, url, request_country,
      ip_address, status_code, response_time_msec
    ) VALUES (
      trunc(random()*32), clock_timestamp(),
      concat('https://example.com/', md5(random()::text)),
      ('{China,India,USA,Indonesia}'::text[])[ceil(random()*4)],
      concat(
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2)
      )::inet,
      ('{200,404}'::int[])[ceil(random()*2)],
      5+trunc(random()*150)
    );
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

La requête ajoute ensuite une ligne environ chaque quart de seconde. Les lignes sont stockées sur différents nœuds Worker, comme indiqué par la colonne de distribution, `site_id`.

   > [!NOTE]
   > Continuez à exécuter la requête de génération de données, puis ouvrez une deuxième connexion psql pour les commandes restantes dans ce tutoriel.
   >

## <a name="query"></a>Requête

L’option d’hébergement hyperscale permet à plusieurs nœuds de traiter des requêtes en parallèle, ce qui accélère la procédure. Par exemple, la base de données calcule des agrégats tels que SUM (SOMME) et COUNT (NOMBRE) sur les nœuds Worker et combine les résultats dans une réponse finale.

Voici une requête qui compte les requêtes web par minute ainsi que quelques statistiques.
Essayez de l’exécuter dans psql pour voir les résultats.

```sql
SELECT
  site_id,
  date_trunc('minute', ingest_time) as minute,
  COUNT(1) AS request_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
  SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
FROM http_request
WHERE date_trunc('minute', ingest_time) > now() - '5 minutes'::interval
GROUP BY site_id, minute
ORDER BY minute ASC;
```

## <a name="performing-rollups"></a>Exécution de cumuls

La requête ci-dessus fonctionne bien dans les premières étapes, mais ses performances diminuent à mesure que la quantité de données augmente. Même avec un traitement distribué, il est plus rapide de précalculer ces données que de les recalculer de façon répétée.

Vous pouvez vérifier que votre tableau de bord a conservé sa rapidité en cumulant régulièrement les données brutes dans une table d’agrégation. Dans ce cas, vous cumulez dans votre table d’agrégations de 1 minute, mais vous pouvez également avoir des agrégations de 5 minutes, 15 minutes, 1 heure et ainsi de suite.

Étant donné que vous allez exécuter en continu ce cumul, vous allez créer une fonction pour effectuer cette opération. Exécutez les commandes suivantes dans psql pour créer la fonction `rollup_http_request`.

```sql
-- initialize to a time long ago
INSERT INTO latest_rollup VALUES ('10-10-1901');

-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
  curr_rollup_time timestamptz := date_trunc('minute', now());
  last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
  INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec
  ) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
  FROM http_request
  -- roll up only data new since last_rollup_time
  WHERE date_trunc('minute', ingest_time) <@
          tstzrange(last_rollup_time, curr_rollup_time, '(]')
  GROUP BY 1, 2;

  -- update the value in latest_rollup so that next time we run the
  -- rollup it will operate on data newer than curr_rollup_time
  UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

Une fois votre fonction en place, exécutez-la pour cumuler les données :

```sql
SELECT rollup_http_request();
```

Avec vos données au format préagrégé, vous pouvez interroger la table de cumul pour obtenir le même rapport que précédemment. Exécutez la commande suivante :

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Expiration des données anciennes

Les cumuls accélèrent les requêtes, mais vous devez tout de même faire expirer les données anciennes pour éviter les coûts de stockage démesurés. Décidez simplement la durée pendant laquelle vous souhaitez conserver les données pour chaque granularité et utilisez des requêtes standard pour supprimer les données expirées. Dans l’exemple suivant, vous allez conserver les données brutes pendant un jour et les agrégations par minute pendant un mois :

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

En production, vous pouvez encapsuler ces requêtes dans une fonction et appeler celle-ci toutes les minutes dans un travail cron.

## <a name="clean-up-resources"></a>Supprimer des ressources

Au cours des étapes précédentes, vous avez créé des ressources Azure dans un groupe de serveurs. Si vous pensez ne pas avoir besoin de ces ressources à l’avenir, supprimez le groupe de serveurs. Appuyez sur le bouton *Supprimer* dans la page *Vue d’ensemble* de votre groupe de serveurs. Quand vous y êtes invité dans une page contextuelle, confirmez le nom du groupe de serveurs, puis cliquez sur le bouton *Supprimer*.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à configurer un groupe de serveurs Hyperscale (Citus). Vous vous y êtes connecté avec psql, vous avez créé un schéma et vous avez distribué les données. Vous avez appris à interroger des données sous leur forme brute, à agréger régulièrement ces données, à interroger les tables agrégées et à faire expirer les données anciennes.

Ensuite, découvrez les concepts de l’hyperscale.
> [!div class="nextstepaction"]
> [Types de nœuds de l’hyperscale](https://aka.ms/hyperscale-concepts)