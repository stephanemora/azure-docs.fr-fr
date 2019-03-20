---
title: Notes de publication Azure SQL Data Warehouse septembre 2018 | Microsoft Docs
description: Notes de publication pour Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 10/08/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: bc559a1224aace2ee599c24c8dce07a6d55173fd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863710"
---
# <a name="whats-new-in-azure-sql-data-warehouse-september-2018"></a>Nouveautés dans Azure SQL Data Warehouse Septembre 2018
Azure SQL Data Warehouse reçoit continuellement des améliorations. Cet article décrit les nouvelles fonctionnalités et les changements introduits en septembre 2018.

## <a name="new-lower-entry-point-for-sql-data-warehouse-gen2"></a>Nouveau point d’entrée inférieur pour SQL Data Warehouse Gen2
En avril 2018, [Microsoft a annoncé](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) la sortie d’Azure SQL Data Warehouse Gen2, qui offre des performances multipliées par 5, une échelle de calcul multipliée par 5, une concurrence multipliée par 4 et un stockage illimité. Comme indiqué dans le [test d’évaluation de Data Warehouse dans le cloud](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/) réalisé par Gigaom, SQL Data Warehouse Gen2 **surclasse Amazon Redshift de 42 %**.

Gen2 est généralement disponible à un point d’entrée inférieur à celui de DWU500c, ce qui vous permet d’exécuter un entrepôt de données de plus petite taille ou des environnements de développement/test avec les dernières améliorations du service. Le nouveau point d’entrée conserve toutes les fonctionnalités de Gen2, notamment la [mise en cache adaptative](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/), la [lecture aléatoire ultra-rapide des données](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/) et la prise en charge d’un [entrepôt de données en temps réel](https://azure.microsoft.com/blog/enabling-real-time-data-warehousing-with-azure-sql-data-warehouse/).

## <a name="sql-vulnerability-assessment"></a>Évaluation des vulnérabilités SQL
L’[Évaluation des vulnérabilités SQL (VA)](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/25/sql-vulnerability-assessment-now-supports-azure-sql-data-warehouse-and-azure-sql-database-managed-instance/) est un service facile à utiliser qui surveille en permanence votre entrepôt de données. Il vous aide à assurer un niveau élevé de sécurité à tout moment et à faire en sorte que vos stratégies d’organisation soient respectées. Il fournit un rapport de sécurité complet ainsi que les étapes de correction actionnables pour chaque problème détecté. Ce rapport facilite la gestion proactive de la sécurité de votre base de données et attire votre attention sur les actions à impact maximal, même si vous n’êtes pas un expert en sécurité. Pour les environnements dynamiques où les modifications sont fréquentes et difficiles à suivre, VA est incontournable car il permet de détecter les paramètres qui peuvent laisser votre entrepôt de données vulnérables aux attaques.

## <a name="improved-availability-with-query-restartability"></a>Disponibilité améliorée avec redémarrage des requêtes
Bon nombre de problèmes peuvent se produire pendant l’exécution d’une requête et provoquer son échec. Une panne du réseau, une défaillance matérielle ou toute autre déconnexion peut entraîner une interruption. SQL Data Warehouse prend désormais en charge le redémarrage des requêtes SELECT au niveau des étapes ou des instructions. 

Avec cette fonctionnalité, une requête en cours d’exécution qui est interrompue à la suite d’un échec redémarre automatiquement. En fonction du nombre d’étapes, de la forme de la requête ou de l’endroit où la requête a été arrêtée durant son exécution, le moteur SQL Data Warehouse redémarre la requête complète ou la reprend à partir de la dernière étape terminée. Du point de vue de l’utilisateur final, la requête est simplement terminée. 

## <a name="maintenance-scheduling-preview"></a>Planification de la maintenance (préversion)
La planification de la maintenance dans Azure SQL Data Warehouse est désormais disponible en préversion. Cette nouvelle fonctionnalité intègre de manière transparente les notifications de maintenance planifiée de Service Health, le moniteur des vérifications de Resource Health et le service de planification de la maintenance d’Azure SQL Data Warehouse. La planification de la maintenance vous permet de recevoir les nouvelles fonctionnalités, les mises à niveau et les correctifs durant la fenêtre de temps qui vous convient.

En tirant parti d’Azure Monitor, la planification de la maintenance permet aux clients de choisir comment être informés des événements de maintenance imminents, et quels flux automatisés doivent être déclenchés pour gérer les temps d’arrêt et réduire l’impact sur les opérations. Les notifications peuvent inclure un e-mail ou un SMS. 

## <a name="wide-row-support-in-polybase"></a>Prise en charge des lignes larges dans Polybase
Durant le chargement de données dans SQL Data Warehouse, il est généralement recommandé d’utiliser [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading#options-for-loading-with-polybase) car il prend en charge le chargement des données en parallèle. Cette version prend en charge des colonnes plus larges comprises entre 32 Ko et 1 Mo, ce qui vous permet de charger des tables avec des lignes larges. Le processus de chargement des données pour les tables contenant des lignes larges s’en trouve donc simplifié.

> [!Note]
> La taille de ligne totale ne peut pas dépasser 1 Mo.

## <a name="additional-language-support"></a>Prise en charge de langages supplémentaires
Cette version introduit la prise en charge des éléments suivants du langage T-SQL :

### <a name="stringsplit"></a>STRING_SPLIT
La fonction [STRING_SPLIT](https://docs.microsoft.com/sql/t-sql/functions/string-split-transact-sql) divise une chaîne de caractères à l’aide du séparateur spécifié. STRING_SPLIT est utile dans les scénarios de chargement de données dans lesquels une colonne peut avoir plusieurs valeurs à analyser et à insérer dans une autre table.

#### <a name="example"></a>Exemples
```sql
DECLARE @tags NVARCHAR(400) = 'clothing,road,,touring,bike';

SELECT
    value
FROM
    STRING_SPLIT(@tags, ',')
WHERE
    RTRIM(value) <> '';
```

### <a name="compressdecompress-functions"></a>Fonctions COMPRESS/DECOMPRESS
Les fonctions [COMPRESS](https://docs.microsoft.com/sql/t-sql/functions/compress-transact-sql) / [DECOMPRESS](https://docs.microsoft.com/sql/t-sql/functions/decompress-transact-sql) vous permettent de compresser ou de décompresser une entrée sous forme de chaîne à l’aide de l’algorithme GZIP.

#### <a name="example"></a>Exemples

```sql
SELECT
    name [name_original]
    , COMPRESS(name) [name_compressed]
    , CAST(DECOMPRESS(COMPRESS(name)) AS NVARCHAR(MAX)) [name_decompressed]
FROM
    sys.objects;
```

### <a name="if-exists-clause-for-dropping-views"></a>Clause IF EXISTS pour la suppression de vues
L’ajout de la clause IF EXISTS dans l’instruction [DROP VIEW](https://docs.microsoft.com/sql/t-sql/statements/drop-view-transact-sql) simplifie le code T-SQL nécessaire pour supprimer une vue de l’entrepôt de données. Appliquée à une instruction DROP VIEW, la syntaxe IF EXISTS supprime la vue si elle existe ou ignore l’instruction si la vue n’existe pas.

#### <a name="example"></a>Exemples
```sql
DROP VIEW IF EXISTS dbo.TestView;
```
```
Message
--------------------------------------------------
Commands completed successfully.

```

## <a name="improved-compilation-time-for-singleton-inserts-and-ddl-statements"></a>Temps de compilation amélioré pour les insertions de singleton et les instructions DDL 
L’adhésion à un modèle ETL (extraction, transformation et chargement) traditionnel pour l’insertion de données conduit souvent à l’exécution d’une insertion de singleton ([INSERT-VALUES](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql)) dans une table dans la base de données. Cette version améliore les performances des opérations d’insertion de singleton en réduisant le temps de compilation nécessaire pour exécuter ce type d’instruction. Dans certains cas, la durée de compilation observée est jusqu’à 3 fois inférieure. Cette amélioration diminue le temps nécessaire pour exécuter une instruction d’insertion de singleton. 

L’amélioration profite également aux entrepôts de données recensant un grand nombre d’objets en réduisant de manière semblable la durée de compilation des requêtes pour les instructions DDL (Data Definition Language), notamment les opérations CREATE, ALTER et DELETE. 

Enfin, l’amélioration réduit le temps d’exécution global des instructions qui s’exécutent sur des tables larges (c’est-à-dire des tables contenant un grand nombre de colonnes). Il en résulte une réduction de la durée de l’étape de compilation des requêtes, ce qui réduit au final la durée d’exécution globale des requêtes.

## <a name="bug-fixes"></a>Résolution des bogues

| Intitulé | Description |
|:---|:---|
| **Correctif relatif à la création de statistiques sur des distributions pour des contraintes uniques** | Ce correctif résout une erreur qui se produit quand les utilisateurs exécutent UPDATE STATISTICS en spécifiant uniquement la table et que celle-ci n’a qu’une seule contrainte définie. |
| **Correctif relatif à la compilation de requêtes sur des tables externes** | Ce correctif résout un défaut qui affecte les temps de compilation des requêtes impliquant des tables externes.|
| **Correctif relatif à l’exécution d’une instruction avec un type large** | Corrige un défaut de compilation des instructions préparées contenant des paramètres déclarés en tant que type *large* (nvarchar(max), varchar(max) ou varbinary(max)). |
| **Correctif visant à résoudre une erreur qui se produit pour les requêtes profondément imbriquées** | Fournit un message d’erreur clair quand une requête profondément imbriquée dépasse les limites du système.|
| **Correctif visant à résoudre les erreurs de compilation qui se produisent quand une instruction contient une sous-requête corrélée et une constante au moment de l’exécution** |Corrige l’erreur de compilation liée aux requêtes avec une combinaison particulière de sous-requêtes corrélées et de constantes d’exécution (par exemple, GETDATE()).|
| **Correction de l’expiration liée à l’acquisition de verrous d’objets PDW et d’un emplacement de concurrence pour les statistiques automatiques** |Corrige l’expiration liée à l’ajout de verrous pour empêcher les requêtes de statistiques automatiques de bloquer les demandes d’origine pendant une longue période.|

## <a name="next-steps"></a>Étapes suivantes
À présent que vous en savez un peu plus sur SQL Data Warehouse, découvrez comment [créer rapidement un entrepôt SQL Data Warehouse][create a SQL Data Warehouse]. Si vous n’êtes pas encore familiarisé avec Azure, vous pouvez vous appuyer sur le [Glossaire Azure][Azure glossary] lorsque vous rencontrez de nouveaux termes. Ou bien, consultez ces autres ressources de SQL Data Warehouse.  

* [Témoignages de clients]
* [Blogs]
* [Demandes de fonctionnalités]
* [Vidéos]
* [Blogs de l’équipe de conseil clientèle]
* [Forum Stack Overflow]
* [Twitter]


[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs de l’équipe de conseil clientèle]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Témoignages de clients]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Demandes de fonctionnalités]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vidéos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
