---
title: Fonctionnalités multimodèles
description: Azure SQL Database vous permet d’utiliser plusieurs modèles de données dans la même base de données.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/17/2018
ms.openlocfilehash: 2e8519fa8d96b7fe016b9da4ba84ce481a57d94e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73802815"
---
# <a name="multi-model-capabilities-of-azure-sql-database"></a>Fonctionnalités multimodèles d’Azure SQL Database

Des bases de données multimodèles vous permettent de stocker et d’utiliser des données représentées dans plusieurs formats, telles que des données relationnelles, des graphiques, des documents JSON/XML, des paires clé-valeur, etc.

## <a name="when-to-use-multi-model-capabilities"></a>Quand utiliser les fonctionnalités multimodèles ?

Azure SQL Database est conçu pour fonctionner avec le modèle relationnel qui offre les meilleures performances dans la plupart des cas pour diverses applications à usage général. Toutefois, Azure SQL Database n’est pas limité aux données relationnelles. Azure SQL Database vous permet d’utiliser divers formats non relationnels étroitement intégrés dans le modèle relationnel.
Envisagez d’utiliser les fonctionnalités multimodèles d’Azure SQL Database dans les cas suivants :
- Vous disposez d’informations ou de structures qui sont mieux adaptées aux modèles NoSQL et vous ne souhaitez pas utiliser de bases de données NoSQL distinctes.
- Une majorité de vos données est appropriée pour le modèle relationnel, et vous avez besoin de modéliser certaines parties de vos données dans le style NoSQL.
- Vous souhaitez tirer parti des nombreux langages Transact-SQL pour interroger et analyser à la fois les données relationnelles et les données NoSQL et les intégrer dans un large éventail d’outils et applications qui utilisent le langage SQL.
- Vous souhaitez appliquer les fonctionnalités de base de données comme les [technologies en mémoire](sql-database-in-memory.md) pour améliorer les performances de votre analytique ou le traitement de vos structures de données NoSQL : utilisez la [réplication transactionnelle](sql-database-managed-instance-transactional-replication.md) ou les [réplicas en lecture](sql-database-read-scale-out.md) pour créer une copie de vos données sur l’autre emplacement et décharger certaines charges de travail analytiques à partir de la base de données primaire.

## <a name="overview"></a>Vue d’ensemble

SQL Azure offre les fonctionnalités multimodèles suivantes :
- Les [fonctionnalités de graphe](#graph-features) vous permettent de représenter vos données sous la forme d’un ensemble de nœuds et d’arêtes, ainsi que d’utiliser des requêtes Transact-SQL standard améliorées avec l’opérateur graphique `MATCH` pour interroger les données du graphe.
- Les [fonctionnalités JSON](#json-features) vous permettent d’insérer des documents JSON dans des tables, ainsi que de convertir des données relationnelles en documents JSON et vice versa. Vous pouvez utiliser le langage Transact-SQL standard amélioré avec des fonctions JSON pour l’analyse de documents, et utiliser des index non cluster, des index columnstore ou des tables à mémoire optimisée pour optimiser vos requêtes.
- Les [fonctions spatiales](#spatial-features) vous permettent de stocker des données géographiques et géométriques, de les indexer à l’aide d’index spatiaux, et de les récupérer à l’aide de requêtes spatiales.
- Les [fonctionnalités XML](#xml-features) vous permettent de stocker et indexer des données XML dans votre base de données, ainsi que d’utiliser des opérations XQuery/XPath natives pour travailler avec des données XML. Azure SQL Database dispose d’un moteur de requête XML spécialisé intégré qui traite les données XML.
- Les [paires clé-valeur](#key-value-pairs) ne sont pas explicitement prises en charge en tant que fonctionnalités spéciales dans la mesure où elles peuvent être modélisées en mode natif en tant que tables de deux colonnes.

  > [!Note]
  > Vous pouvez utiliser une expression de chemin d’accès JSON, des expressions XQuery/XPath, des fonctions spatiales et des expressions d’interrogation de graphique dans la même requête Transact-SQL pour accéder à toutes les données stockées dans la base de données. De plus, tout outil ou langage de programmation capable d’exécuter des requêtes Transact-SQL peut également utiliser cette interface de requête pour accéder à des données multimodèles. Il s’agit là de la principale différence par rapport aux bases de données multimodèles telles qu’[Azure Cosmos DB](/azure/cosmos-db/) qui fournit une API spécialisée pour les différents modèles de données.

Les sections suivantes décrivent les fonctionnalités multimodèles les plus importantes d’Azure SQL Database.

## <a name="graph-features"></a>Fonctionnalités de graphe

Azure SQL Database offre des fonctionnalités de base de données de graphe pour modéliser des relations de plusieurs à plusieurs dans une base de données. Un graphique est une collection de nœuds (ou sommets) et d’arêtes (ou relations). Un nœud représente une entité (par exemple, une personne ou une organisation) et une arête représente une relation entre deux nœuds qu’elle connecte (par exemple, des mentions j’aime ou des amis). Voici quelques fonctionnalités qui rendent une base de données de graphe unique :
- Les arêtes ou relations sont des entités de première classe dans une base de données de graphe, auxquelles des attributs ou des propriétés peuvent être associés.
- Un simple arête peut connecter de manière flexible plusieurs nœuds dans une base de données de graphe.
- Vous pouvez facilement exprimer des critères spéciaux et des requêtes de navigation sur plusieurs tronçons.
- Vous pouvez facilement exprimer une fermeture transitive et des requêtes polymorphes.

Les fonctionnalités de relations entre graphes et d’interrogation de graphes sont intégrées dans Transact-SQL. Elles bénéficient des avantages liés à l’utilisation de SQL Server en tant que le système fondamental de gestion de base de données.
Le [traitement de graphe](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) est la principale fonctionnalité du moteur de base de données SQL Server. Vous pouvez trouver des informations supplémentaires sur le traitement de graphe dans cette rubrique.

### <a name="when-to-use-a-graph-capability"></a>Quand utiliser une fonctionnalité de graphe

Il n’est rien qu’une base de données de graphe puisse accomplir qui ne puisse être accompli à l’aide d’une base de données relationnelle. Toutefois, une base de données de graphe peut faciliter l’expression de certaines requêtes. Votre décision de choisir l’une plutôt que l’autre peut dépendre des facteurs suivants :

- Vous devez modéliser des données hiérarchiques dans lesquelles un nœud peut avoir plusieurs parents, de sorte que HierarchyId ne peut pas être utilisé.
- Votre application a des relations de plusieurs à plusieurs complexes et, à mesure qu’elle évolue, de nouvelles relations sont ajoutées.
- Vous devez analyser des données et relations interconnectées.

## <a name="json-features"></a>Fonctionnalités JSON

Azure SQL Database vous permet d’analyser et d’interroger des données représentées au format JavaScript Object Notation [(JSON)](https://www.json.org/) , et d’exporter vos données relationnelles en tant que texte JSON.

JSON est un format de données largement répandu, utilisé pour l’échange de données dans des applications mobiles et web modernes. JSON est également utilisé pour stocker des données semi-structurées dans des fichiers journaux ou des bases de données NoSQL, par exemple [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Nombreux sont les services web REST qui retournent des résultats sous forme de texte JSON ou qui acceptent des données au format JSON. La plupart des services Azure tels que [Recherche cognitive Azure](https://azure.microsoft.com/services/search/), [Stockage Azure](https://azure.microsoft.com/services/storage/) et [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) ont des points de terminaison REST qui renvoient ou utilisent des données JSON.

Azure SQL Database vous permet de travailler facilement avec des données JSON, et d’intégrer votre base de données avec des services modernes. Azure SQL Database offre les fonctions suivantes pour manipuler des données JSON :

![Fonctions JSON](./media/sql-database-json-features/image_1.png)

Si vous avez du texte JSON, vous pouvez extraire des données de JSON ou vérifier que JSON est correctement formaté à l’aide des fonctions intégrées [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx) et [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). La fonction [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) permet de mettre à jour la valeur dans le texte JSON. Pour des opérations d’interrogation et d’analyse plus avancées, la fonction [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) peut transformer un tableau d’objets JSON en un ensemble de lignes. Toute requête SQL peut être exécutée sur le jeu de résultats retourné. Enfin, il existe une clause [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) qui vous permet de convertir les données stockées dans vos tables relationnelles au format de texte JSON.

Pour plus d’informations, voir [Prise en main des fonctionnalités JSON dans Azure SQL Database](sql-database-json-features.md).
[JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) est une fonctionnalité essentielle du moteur de base de données SQL Server. Vous pouvez trouver des informations supplémentaires à son sujet dans cette rubrique.

### <a name="when-to-use-a-json-capability"></a>Quand utiliser une fonctionnalité JSON

Vous pouvez utiliser des modèles de document au lieu de modèles relationnels dans certains cas bien spécifiques :
- Une normalisation élevée du schéma n’apporte pas d’avantages significatifs, car vous accédez à tous les champs d’objets en même temps, ou ne mettez jamais à jour les parties normalisées des objets. Par ailleurs, le modèle normalisé augmente la complexité de vos requêtes en raison du nombre important de tables que vous devez joindre pour obtenir les données.
- Vous travaillez avec des applications qui utilisent en mode natif des documents JSON qui sont des modèles de communication ou de données, et vous ne souhaitez pas introduire de couches supplémentaires qui transforment des données relationnelles en JSON et inversement.
- Vous devez simplifier votre modèle de données en dénormalisant les tables enfant ou les modèles Entité-Objet-Valeur.
- Vous devez charger ou exporter des données stockées au format JSON sans outil supplémentaire analysant les données.

## <a name="spatial-features"></a>Fonctionnalités spatiales

Données spatiales représentent des informations sur l’emplacement physique et la forme d’objets géométriques. Ces objets peuvent être des emplacements de points ou des objets plus complexes, tels que des pays/régions, des routes ou des lacs.

Azure SQL Database prend en charge deux types de données spatiales : les données de type géométrique et les données de type géographique.
- Le type géométrique représente des données dans un système de coordonnées euclidien (plat).
- Le type géographique représente des données dans un système de coordonnées terrestres.

Un certain nombre d’objets spatiaux peuvent être utilisés dans Azure SQL Database, tels que [Point](https://docs.microsoft.com/sql/relational-databases/spatial/point), [LineString](https://docs.microsoft.com/sql/relational-databases/spatial/linestring), [Polygon](https://docs.microsoft.com/sql/relational-databases/spatial/polygon).

Azure SQL Database fournit également des [index spatiaux](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-indexes-overview) spécialisés permettant d’améliorer les performances de vos requêtes spatiales.

La [prise en charge spatiale](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) est la principale fonctionnalité du moteur de base de données SQL Server. Vous pouvez trouver des informations sur la fonctionnalité spatiale dans cette rubrique.

## <a name="xml-features"></a>Fonctionnalités XML

SQL Server offre une plateforme puissante pour développer des applications riches pour la gestion de données semi-structurées. La prise en charge de XML est intégrée dans tous les composants de SQL Server et inclut ce qui suit :

- Le type de données XML. Les valeurs XML peuvent être stockées en mode natif dans une colonne de type de données XML qui peut être typée en fonction d’une collection de schémas XML, ou laissée non-typée. Vous pouvez indexer la colonne XML.
- La possibilité de spécifier une requête XQuery sur des données XML stockées dans des colonnes et des variables du type XML. Les fonctionnalités XQuery peuvent être utilisées dans n'importe quelle requête Transact-SQL pour accéder à un modèle de données que vous utilisez dans votre base de données.
- Possibilité d’indexer automatiquement tous les éléments figurant dans des documents XML à l’aide d’un [index XML primaire](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index), ou de spécifier les chemins d’accès exacts à indexer à l’aide d’un [index XML secondaire](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- OPENROWSET qui permet le chargement par lots de données XML.
- Possibilité de convertir des données relationnelles au format XML.

[XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server) est une fonctionnalité essentielle du moteur de base de données SQL Server. Vous pouvez trouver des informations supplémentaires à son sujet dans cette rubrique.

### <a name="when-to-use-an-xml-capability"></a>Quand utiliser une fonctionnalité XML

Vous pouvez utiliser des modèles de document au lieu de modèles relationnels dans certains cas bien spécifiques :
- Une normalisation élevée du schéma n’apporte pas d’avantages significatifs, car vous accédez à tous les champs d’objets en même temps, ou ne mettez jamais à jour les parties normalisées des objets. Par ailleurs, le modèle normalisé augmente la complexité de vos requêtes en raison du nombre important de tables que vous devez joindre pour obtenir les données.
- Vous travaillez avec des applications qui utilisent en mode natif des documents XML qui sont des modèles de communication ou de données, et vous ne souhaitez pas introduire de couches supplémentaires qui transforment des données relationnelles en XML et inversement.
- Vous devez simplifier votre modèle de données en dénormalisant les tables enfant ou les modèles Entité-Objet-Valeur.
- Vous devez charger ou exporter des données stockées au format XML sans outil supplémentaire analysant les données.

## <a name="key-value-pairs"></a>Paires clé-valeur

Azure SQL Database n’a pas de types ou structures spécialisés qui prennent en charge les paires clé-valeur, car les structures clé-valeur peuvent être représentées en mode natif sous forme de tables relationnelles standard :

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

Vous pouvez personnaliser cette structure clé-valeur selon vos besoins sans contrainte. Par exemple, la valeur peut être un document XML au lieu de type `nvarchar(max)`. Si la valeur est un document JSON, vous pouvez appliquer une contrainte `CHECK` qui vérifie la validité du contenu JSON. Vous pouvez placer un nombre quelconque de valeurs liées à une clé dans les colonnes supplémentaires, ajouter des colonnes et index calculés pour simplifier et optimiser l’accès aux données, définir la table en tant que table exclusivement de schéma en mémoire/optimisé pour obtenir de meilleures performances, etc.

Découvrez [comment BWin utilise la technologie OLTP en mémoire pour atteindre des performances et une échelle sans précédent ](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/) pour sa solution de mise en cache ASP.NET, qui a atteint 1 200 000 lots par seconde. Cet exemple montre à quel point un modèle relationnel peut être utilisé efficacement en tant que solution de paire clé-valeur dans la pratique.

## <a name="next-steps"></a>Étapes suivantes
Les fonctionnalités multimodèles des bases de données Azure SQL sont également les fonctionnalités essentielles du moteur de base de données SQL Server que partagent Azure SQL Database et SQL Server. Pour plus d’informations sur ces fonctionnalités, visitez les pages de documentation sur la base de données relationnelle SQL :

* [Traitement de graphe](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)
* [Données JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)
* [Prise en charge spatiale](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [Données XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)
