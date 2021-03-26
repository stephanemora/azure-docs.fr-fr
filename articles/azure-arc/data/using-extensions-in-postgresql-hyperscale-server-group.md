---
title: Utiliser les extensions PostgreSQL
description: Utiliser les extensions PostgreSQL
titleSuffix: Azure Arc enabled data services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e247e372237572586e5a4647d24d9ed6067ea823
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104949785"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Utiliser les extensions PostgreSQL dans votre groupe de serveurs Azure Arc enabled PostgreSQL Hyperscale

PostgreSQL est optimal quand vous l’utilisez avec des extensions. En fait, un élément clé de notre propre fonctionnalité Hyperscale est l’extension `citus` fournie par Microsoft qui est installée par défaut et permet à Postgres de partitionner les données sur plusieurs nœuds de façon transparente.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="supported-extensions"></a>Extensions prises en charge
Les extensions [`contrib`](https://www.postgresql.org/docs/12/contrib.html) standard et les extensions suivantes sont déjà déployées dans les conteneurs de votre groupe de serveurs PostgreSQL Hyperscale avec Azure Arc :
- [`citus`](https://github.com/citusdata/citus), v: 9.4. L’extension Citus par [Citus Data](https://www.citusdata.com/) est chargée par défaut, car elle apporte la fonctionnalité Hyperscale au moteur PostgreSQL. La suppression de l’extension Citus de votre groupe de serveurs PostgreSQL Hyperscale avec Azure Arc n’est pas prise en charge.
- [`pg_cron`](https://github.com/citusdata/pg_cron), v: 1.2
- [`pgaudit`](https://www.pgaudit.org/), v: 1.4
- plpgsql, v: 1.0
- [`postgis`](https://postgis.net), v: 3.0.2
- [`plv8`](https://plv8.github.io/), v: 2.3.14

Cette liste sera mise à jour au fil du temps.

> [!IMPORTANT]
> Bien que vous puissiez importer dans votre groupe de serveurs une extension autre que celle indiquée ci-dessus, dans cette préversion, elle ne sera pas conservée sur votre système. Cela signifie qu’elle ne sera pas disponible après un redémarrage du système et que vous auriez besoin de l’importer à nouveau.

Ce guide se base sur un scénario d’utilisation de deux de ces extensions :
- [`PostGIS`](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)

## <a name="which-extensions-need-to-be-added-to-the-shared_preload_libraries-and-created"></a>Quelles extensions doivent être ajoutées à shared_preload_libraries et créées ?

|Extensions   |Ajout nécessaire à shared_preload_libraries  |Création nécessaire |
|-------------|--------------------------------------------------|---------------------- |
|`pg_cron`      |Non       |Oui        |
|`pg_audit`     |Oui       |Oui        |
|`plpgsql`      |Oui       |Oui        |
|`postgis`      |Non       |Oui        |
|`plv8`      |Non       |Oui        |

## <a name="add-extensions-to-the-shared_preload_libraries"></a>Ajouter des extensions à shared_preload_libraries
Pour plus d’informations sur shared_preload_libraries, veuillez lire la documentation PostgreSQL [ici](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SHARED-PRELOAD-LIBRARIES) :
- Cette étape n’est pas nécessaire pour les extensions qui font partie de `contrib`.
- Cette étape n’est pas requise pour les extensions qui ne doivent pas être préchargées par shared_preload_libraries. Pour ces extensions, vous pouvez passer au paragraphe suivant intitulé [Créer des extensions](#create-extensions).

### <a name="add-an-extension-at-the-creation-time-of-a-server-group"></a>Ajouter une extension au moment de la création d’un groupe de serveurs
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
### <a name="add-an-extension-to-an-instance-that-already-exists"></a>Ajouter une extension à une instance qui existe déjà
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```




## <a name="show-the-list-of-extensions-added-to-shared_preload_libraries"></a>Afficher la liste des extensions ajoutées à shared_preload_libraries
Exécutez une des commandes suivantes.

### <a name="with-an-azdata-cli-command"></a>Avec la commande de l’interface CLI azdata
```console
azdata arc postgres server show -n <server group name>
```
Faites défiler la sortie et notez les sections moteur\extensions dans les spécifications de votre groupe de serveurs. Exemple :
```console
"engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_cron"
        }
      ]
    },
```
### <a name="with-kubectl"></a>Avec kubectl
```console
kubectl describe postgresql-12s/postgres02
```
Faites défiler la sortie et notez les sections moteur\extensions dans les spécifications de votre groupe de serveurs. Exemple :
```console
Engine:
    Extensions:
      Name:  citus
      Name:  pg_cron
```


## <a name="create-extensions"></a>Créer des extensions
Connectez-vous à votre groupe de serveurs avec l’outil client de votre choix, et exécutez la requête PostgreSQL standard :
```console
CREATE EXTENSION <extension name>;
```

## <a name="show-the-list-of-extensions-created"></a>Afficher la liste des extensions créées
Connectez-vous à votre groupe de serveurs avec l’outil client de votre choix, et exécutez la requête PostgreSQL standard :
```console
select * from pg_extension;
```

## <a name="drop-an-extension"></a>Supprimer une extension
Connectez-vous à votre groupe de serveurs avec l’outil client de votre choix, et exécutez la requête PostgreSQL standard :
```console
drop extension <extension name>;
```

## <a name="the-postgis-extension"></a>Extension `PostGIS`
Vous n’avez pas besoin d’ajouter l’extension `PostGIS` à `shared_preload_libraries`.
Procurez-vous l’[exemple de données](http://duspviz.mit.edu/tutorials/intro-postgis/) du Department of Urban Studies & Planning du MIT. Exécutez `apt-get install unzip` pour installer unzip si nécessaire.

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

Nous allons nous connecter à notre base de données, puis créer l’extension `PostGIS` :

```console
CREATE EXTENSION postgis;
```

> [!NOTE]
> Si vous voulez utiliser une des extensions du package `postgis` (par exemple `postgis_raster`, `postgis_topology`, `postgis_sfcgal`, `fuzzystrmatch`...), vous devez d’abord créer l’extension postgis, puis créer l’autre extension. Par exemple : `CREATE EXTENSION postgis`; `CREATE EXTENSION postgis_raster`;

Vous créez ensuite le schéma :

```sql
CREATE TABLE coffee_shops (
  id serial NOT NULL,
  name character varying(50),
  address character varying(50),
  city character varying(50),
  state character varying(50),
  zip character varying(10),
  lat numeric,
  lon numeric,
  geom geometry(POINT,4326)
);
CREATE INDEX coffee_shops_gist ON coffee_shops USING gist (geom);
```

Maintenant, nous pouvons combiner `PostGIS` avec la fonctionnalité de scale-out, en faisant de coffee_shops une table distribuée :

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

Nous allons charger des données :

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

Ensuite, nous renseignons le champ `geom` avec la latitude et la longitude encodées correctement dans le type de données `PostGIS` `geometry` :

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

Nous pouvons maintenant lister les cafés les plus proches du MIT (77 Massachusetts Ave aux coordonnées 42,359055, -71,093500) :

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


## <a name="the-pg_cron-extension"></a>Extension `pg_cron`

Nous allons maintenant activer `pg_cron` sur notre groupe de serveurs PostgreSQL en l’ajoutant à shared_preload_libraries :

```console
azdata postgres server update -n pg2 -ns arc --extensions pg_cron
```

Votre groupe de serveurs redémarrera une fois les extensions installées. Cette opération peut prendre 2 à 3 minutes.

Nous pouvons maintenant nous reconnecter et créer l’extension `pg_cron` :

```sql
CREATE EXTENSION pg_cron;
```

À des fins de test, créons une table `the_best_coffee_shop` qui prend un nom aléatoire à partir de notre table `coffee_shops` précédente et qui insère le contenu de la table :

```sql
CREATE TABLE the_best_coffee_shop(name text);
```

Nous pouvons utiliser `cron.schedule` plus quelques instructions SQL pour obtenir un nom de table aléatoire (remarquez l’utilisation d’une table temporaire pour stocker le résultat d’une requête distribuée) et nous le stockons dans `the_best_coffee_shop` :

```sql
SELECT cron.schedule('* * * * *', $$
  TRUNCATE the_best_coffee_shop;
  CREATE TEMPORARY TABLE tmp AS SELECT name FROM coffee_shops ORDER BY random() LIMIT 1;
  INSERT INTO the_best_coffee_shop SELECT * FROM tmp;
  DROP TABLE tmp;
$$);
```

Et maintenant, une fois par minute, nous obtenons un nom différent :

```sql
SELECT * FROM the_best_coffee_shop;
```

```console
      name
-----------------
 B & B Snack Bar
(1 row)
```

Pour plus de détails sur la syntaxe, consultez le fichier [README de pg_cron](https://github.com/citusdata/pg_cron).


## <a name="next-steps"></a>Étapes suivantes
- Lire la documentation relative à [`plv8`](https://plv8.github.io/)
- Lire la documentation relative à [`PostGIS`](https://postgis.net/)
- Lire la documentation relative à [`pg_cron`](https://github.com/citusdata/pg_cron)