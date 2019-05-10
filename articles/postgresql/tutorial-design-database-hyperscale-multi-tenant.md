---
title: 'Tutoriel : Concevoir une base de données multilocataire avec Azure Database pour PostgreSQL – Hyperscale (Citus) (préversion)'
description: Ce tutoriel illustre comment créer, remplir et interroger des tables distribuées dans Azure Database pour PostgreSQL Hyperscale (Citus) (préversion).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: b135baf73e21cd524b6e8fad35452362f36cf0c0
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079544"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>Tutoriel : Concevoir une base de données multilocataire avec Azure Database pour PostgreSQL – Hyperscale (Citus) (préversion)

Dans ce tutoriel, vous utilisez Azure Database pour PostgreSQL - Hyperscale (Citus) (préversion) pour apprendre à :

> [!div class="checklist"]
> * Créer un groupe de serveurs Hyperscale (Citus)
> * Utiliser l’utilitaire psql pour créer un schéma
> * Partitionner les tables entre les nœuds
> * Ingérer des exemples de données
> * Interroger les données de locataire
> * Partager des données entre les locataires
> * Personnaliser le schéma par locataire

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
   - Nom du groupe de serveurs : entrez un nom unique pour le nouveau groupe de serveurs, qui sera également utilisé pour un sous-domaine de serveur.
   - Nom d’utilisateur de l’administrateur : entrez un nom d’utilisateur unique ; il sera utilisé plus tard pour la connexion à la base de données.
   - Mot de passe : doit contenir au moins huit caractères appartenant à trois des catégories suivantes : - lettres majuscules non accentuées, lettres minuscules non accentuées, chiffres (de 0 à 9) et caractères non alphanumériques (!, $, #, %, etc.).
   - Emplacement : utilisez l’emplacement le plus proche de vos utilisateurs pour leur donner l’accès le plus rapide possible aux données.

   > [!IMPORTANT]
   > La connexion d’administrateur serveur et le mot de passe que vous spécifiez ici seront requis plus loin dans ce tutoriel pour la connexion au serveur et à ses bases de données. Retenez ou enregistrez ces informations pour une utilisation ultérieure.

5. Cliquez sur **Configurer le groupe de serveurs**. Laissez inchangés les paramètres de cette section et cliquez sur **Enregistrer**.
6. Cliquez sur **Vérifier + créer**, puis sur **Créer** pour provisionner le serveur. Le provisionnement prend quelques minutes.
7. La page redirige vers la supervision du déploiement. Quand l’état passe de **Votre déploiement est en cours** à **Votre déploiement a été effectué**, cliquez sur l’élément de menu **Sorties** à gauche de la page.
8. La page des sorties contient un nom d’hôte de coordinateur avec un bouton en regard de celui-ci pour copier la valeur dans le Presse-papiers. Prenez note de ces informations pour une utilisation ultérieure.

## <a name="configure-a-server-level-firewall-rule"></a>Configurer une règle de pare-feu au niveau du serveur

Le service Azure Database pour PostgreSQL utilise un pare-feu au niveau du serveur. Par défaut, le pare-feu empêche tous les outils et les applications externes de se connecter au serveur et aux bases de données sur le serveur. Nous devons ajouter une règle pour ouvrir le pare-feu pour une plage d’adresses IP spécifiques.

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

   ![Base de données Azure pour PostgreSQL - Icône de la console Azure Cloud Shell](./media/tutorial-design-database-hyperscale-multi-tenant/psql-cloud-shell.png)

2. Azure Cloud Shell s’ouvre dans votre navigateur, ce qui vous permet de saisir des commande bash.

   ![Base de données Azure pour PostgreSQL - Invite bash Azure Shell](./media/tutorial-design-database-hyperscale-multi-tenant/psql-bash.png)

3. À l’invite Cloud Shell, connectez-vous à votre serveur de base de données Azure pour PostgreSQL en utilisant les commandes psql. Le format suivant est utilisé pour se connecter à un serveur de base de données Azure pour PostgreSQL avec l’utilitaire [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) :
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   Par exemple, la commande suivante connecte à la base de données par défaut appelée **citus** sur votre serveur PostgreSQL **mydemoserver.postgres.database.azure.com** en utilisant les informations d’identification d’accès. À l’invite, entrez votre mot de passe d’administrateur du serveur.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

## <a name="use-psql-utility-to-create-a-schema"></a>Utiliser l’utilitaire psql pour créer un schéma

Une fois connecté à Azure Database pour PostgreSQL - Hyperscale (Citus) (préversion) à l’aide de psql, vous pouvez effectuer certaines tâches de base. Ce tutoriel vous explique comment créer une application web qui autorise les annonceurs à effectuer le suivi de leurs campagnes.

Plusieurs entreprises peuvent utiliser l’application. Nous allons donc créer une table pour stocker les entreprises et une autre pour leurs campagnes. Dans la console psql, exécutez les commandes suivantes :

```sql
CREATE TABLE companies (
  id bigserial PRIMARY KEY,
  name text NOT NULL,
  image_url text,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL
);

CREATE TABLE campaigns (
  id bigserial,
  company_id bigint REFERENCES companies (id),
  name text NOT NULL,
  cost_model text NOT NULL,
  state text NOT NULL,
  monthly_budget bigint,
  blacklisted_site_urls text[],
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id)
);
```

Chaque campagne paiera pour afficher des publicités. Ajoutez également une table pour les publicités, en exécutant le code suivant dans psql après le code ci-dessus :

```sql
CREATE TABLE ads (
  id bigserial,
  company_id bigint,
  campaign_id bigint,
  name text NOT NULL,
  image_url text,
  target_url text,
  impressions_count bigint DEFAULT 0,
  clicks_count bigint DEFAULT 0,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, campaign_id)
    REFERENCES campaigns (company_id, id)
);
```

Enfin, nous effectuerons le suivi des statistiques relatives aux clics et aux impressions pour chaque publicité :

```sql
CREATE TABLE clicks (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  clicked_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_click_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);

CREATE TABLE impressions (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  seen_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_impression_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);
```

Vous pouvez localiser les tables nouvellement créées dans la liste des tables dans psql en exécutant :

```postgres
\dt
```

Les applications multilocataires peuvent imposer l’unicité seulement par client. C’est pourquoi toutes les clés primaires et étrangères incluent l’ID de l’entreprise.

## <a name="shard-tables-across-nodes"></a>Partitionner les tables entre les nœuds

Un déploiement hyperscale stocke les lignes des tables sur des nœuds différents selon la valeur d’une colonne désignée par l’utilisateur. Cette « colonne de distribution » désigne quel locataire possède quelles lignes.

Nous allons définir la colonne de distribution en tant que company\_id, l’identificateur du locataire. Dans psql, exécutez les fonctions suivantes :

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

## <a name="ingest-sample-data"></a>Ingérer des exemples de données

En dehors de psql à présent, dans la ligne de commande normale, téléchargez des exemples de jeux de données :

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

De nouveau dans psql, effectuez le chargement en masse des données. Veillez à exécuter psql dans le répertoire où vous avez téléchargé les fichiers de données.

```sql
\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

Ces données sont désormais réparties entre les nœuds worker.

## <a name="query-tenant-data"></a>Interroger les données de locataire

Lorsque l’application demande des données pour un locataire individuel, la base de données peut exécuter la requête sur un nœud worker unique. Les requêtes de locataire unique filtrent par un ID de locataire unique. Par exemple, la requête suivante filtre `company_id = 5` pour les publicités et les impressions. Essayez de l’exécuter dans psql pour voir les résultats.

```sql
SELECT a.campaign_id,
       RANK() OVER (
         PARTITION BY a.campaign_id
         ORDER BY a.campaign_id, count(*) desc
       ), count(*) as n_impressions, a.id
  FROM ads as a
  JOIN impressions as i
    ON i.company_id = a.company_id
   AND i.ad_id      = a.id
 WHERE a.company_id = 5
GROUP BY a.campaign_id, a.id
ORDER BY a.campaign_id, n_impressions desc;
```

## <a name="share-data-between-tenants"></a>Partager des données entre les locataires

Jusqu'à présent, toutes les tables ont été distribuées par `company_id`, mais certaines données « n’appartiennent » par nature à aucun locataire en particulier et peuvent être partagées. Par exemple, toutes les entreprises figurant dans l’exemple de plateforme de publicité peuvent souhaiter obtenir des informations géographiques sur leur public ciblé en fonction des adresses IP.

Créez une table pour stocker les informations géographiques partagées. Exécutez ce code dans psql :

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Définissez ensuite `geo_ips` comme « table de référence » pour stocker une copie de la table sur chaque nœud worker.

```sql
SELECT create_reference_table('geo_ips');
```

Chargez-la avec des exemples de données. Pensez à exécuter ce code dans psql à partir du répertoire où vous avez téléchargé le jeu de données.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

La jointure de la table de clics avec geo\_ips est efficace sur tous les nœuds.
Voici une jointure fournissant les emplacements de toutes les personnes qui ont cliqué sur la publicité
290. Essayez d’exécuter cette requête dans psql.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>Personnaliser le schéma par locataire

Chaque locataire peut avoir besoin de stocker des informations spéciales dont les autres utilisateurs n’ont pas besoin. Toutefois, tous les locataires partagent une infrastructure commune avec un schéma de base de données identique. Où peuvent aller les données supplémentaires ?

Une astuce consiste à utiliser un type de colonne de durée indéterminée comme le type JSONB de PostgreSQL.  Notre schéma a un champ JSONB dans `clicks` appelé `user_data`.
Une entreprise (par exemple l’entreprise 5) peut utiliser cette colonne pour déterminer si l’utilisateur est sur un appareil mobile.

Voici une requête permettant de déterminer qui effectue le plus de clics : les visiteurs mobiles ou traditionnels.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

Nous pouvons optimiser cette requête pour une entreprise individuelle en créant un [index partiel](https://www.postgresql.org/docs/current/static/indexes-partial.html).

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

Plus généralement, nous pouvons créer des [index GIN](https://www.postgresql.org/docs/current/static/gin-intro.html) sur chaque clé et chaque valeur de la colonne.

```sql
CREATE INDEX click_user_data
ON clicks USING gin (user_data);

-- this speeds up queries like, "which clicks have
-- the is_mobile key present in user_data?"

SELECT id
  FROM clicks
 WHERE user_data ? 'is_mobile'
   AND company_id = 5;
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Au cours des étapes précédentes, vous avez créé des ressources Azure dans un groupe de serveurs. Si vous pensez ne pas avoir besoin de ces ressources à l’avenir, supprimez le groupe de serveurs. Appuyez sur le bouton *Supprimer* dans la page *Vue d’ensemble* de votre groupe de serveurs. Quand vous y êtes invité dans une page contextuelle, confirmez le nom du groupe de serveurs, puis cliquez sur le bouton *Supprimer*.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à configurer un groupe de serveurs Hyperscale (Citus). Vous vous y êtes connecté avec psql, vous avez créé un schéma et vous avez distribué les données. Vous avez appris à exécuter des requêtes de données au sein et entre les locataires, ainsi qu’à personnaliser le schéma par locataire.

Ensuite, découvrez les concepts de l’hyperscale.
> [!div class="nextstepaction"]
> [Types de nœuds de l’hyperscale](https://aka.ms/hyperscale-concepts)
