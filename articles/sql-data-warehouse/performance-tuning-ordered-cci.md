---
title: Réglage des performances avec un index columnstore cluster ordonné Azure SQL Data Warehouse | Microsoft Docs
description: Recommandations et points à prendre en compte quand vous utilisez un index columnstore cluster ordonné pour améliorer les performances de vos requêtes.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: 41fbebcf4b85f6e48babba30c2d05fedb3e7a5c7
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70985268"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Réglage des performances avec un index columstore cluster ordonné  

Quand des utilisateurs interrogent une table columnstore dans Azure SQL Data Warehouse, l’optimiseur vérifie les valeurs minimales et maximales stockées dans chaque segment.  Les segments en dehors des limites du prédicat de la requête ne sont pas lus à partir du disque vers la mémoire.  Une requête peut voir ses performances accélérer si le nombre de segments à lire et leur taille totale sont petits.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Index columnstore cluster ordonné et non ordonné 
Par défaut, pour chaque table Azure Data Warehouse créée sans option d’index, un composant interne (générateur d’index) crée un index columnstore cluster non ordonné.  Les données incluses dans chaque colonne sont compressées dans un segment de rowgroup distinct de l’index columnstore cluster.  Des métadonnées existent sur la plage de valeurs de chaque segment, si bien que les segments qui se trouvent en dehors des limites du prédicat de la requête ne sont pas lus à partir du disque pendant l’exécution de la requête.  Un index columnstore cluster offre le niveau de compression de données le plus élevé et réduit la taille des segments à lire si bien que les requêtes peuvent s’exécuter plus rapidement. En revanche, étant donné que le générateur d’index ne trie pas les données avant de les compresser dans des segments, les segments peuvent avoir des plages de valeurs qui se chevauchent, ce qui oblige les requêtes à lire plus de segments à partir du disque et donc à durer plus longtemps.  

Lors de la création d’un index columnstore cluster ordonné, le moteur Azure SQL Data Warehouse trie les données en mémoire par clé d’ordre avant que le générateur d’index ne les compresse dans des segments d’index.  Avec les données triées, le chevauchement de segments est réduit, ce qui permet aux requêtes d’éliminer plus efficacement des segments et donc d’accélérer leurs performances, car le nombre de segments à lire à partir du disque est plus petit.  Si toutes les données peuvent être triées en mémoire simultanément, le chevauchement de segments peut être évité.  Étant donné la taille volumineuse des données dans les tables d’entrepôt de données, ce scénario ne se produit pas souvent.  

Pour vérifier les plages de segments d’une colonne, exécutez cette commande avec le nom de la table et le nom de la colonne :

```sql
SELECT o.name, pnp.index_id, pnp.rows, pnp.data_compression_desc, pnp.pdw_node_id, 
pnp.distribution_id, cls.segment_id, cls.column_id, cls.min_data_id, cls.max_data_id, cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
   JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<table_name>' and c.name = '<column_name>'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id
```

## <a name="data-loading-performance"></a>Performances du chargement des données

Les performances du chargement des données dans une table d’index columnstore cluster ordonné est similaire au chargement des données dans une table partitionnée.  
Le chargement des données dans une table d’index columnstore cluster ordonné peut prendre plus de temps que dans une table d’index columnstore cluster non ordonné en raison du tri des données.  

Voici un exemple de comparaison des performances du chargement des données dans des tables avec des schémas différents.
![Performance_comparison_data_loading](media/performance-tuning-ordered-cci/cci-data-loading-performance.png)
 
## <a name="reduce-segment-overlapping"></a>Réduire le chevauchement de segments
Voici les options permettant de réduire encore le chevauchement de segments lors de la création d’un index columnstore cluster ordonné sur une nouvelle table par le biais de CTAS ou sur une table existante avec des données :

- Utilisez une classe de ressources plus grande pour permettre le tri simultané d’une plus grande quantité de données en mémoire avant que le générateur d’index ne les compresse dans des segments.  Une fois dans un segment d’index, l’emplacement physique des données n’est plus modifiable.  Il n’y a pas de tri des données au sein d’un segment ou entre des segments.  

- Utilisez un degré de parallélisme inférieur (égal à 1, par exemple).  Chaque thread utilisé pour la création d’un index columnstore cluster ordonné fonctionne sur un sous-ensemble des données et les trie localement.  Il n’y a pas de tri global sur les données triées par différents threads.  L’utilisation de threads parallèles peut réduire le temps nécessaire à la création d’un index columnstore cluster ordonné, mais génère plus de segments qui se chevauchent que l’utilisation d’un thread unique. 
- Pré-triez les données par la ou les clés de tri avant de les charger dans des tables Azure SQL Data Warehouse.

## <a name="create-ordered-cci-on-large-tables"></a>Créer un index columnstore cluster ordonné sur des tables volumineuses
La création d’un index columnstore cluster ordonné est une opération hors connexion.  Pour des tables sans partitions, les données ne sont pas accessibles aux utilisateurs tant que le processus de création de l’index columnstore cluster ordonné n’est pas terminé.   Pour des tables partitionnées, dans la mesure où le moteur crée la partition d’index columnstore cluster ordonné par partition, les utilisateurs peuvent quand même accéder aux données dans les partitions pour lesquelles la création du index columnstore cluster ordonné n’est pas en cours.   Vous pouvez utiliser cette option pour minimiser le temps d’arrêt lors de la création d’un index columnstore cluster ordonné sur des tables volumineuses : 

1.  Créez des partitions sur la grande table cible (appelée Table A).
2.  Créez une table d’index columnstore cluster ordonné vide (appelée Table B) avec la même table et le même schéma de partition que Table A.
3.  Faites passer une partition de Table A à Table B.
4.  Exécutez ALTER INDEX <Ordered_CCI_Index> REBUILD sur Table B pour reconstruire la partition.  
5.  Répétez les étapes 3 et 4 pour chaque partition de Table A.
6.  Une fois que toutes les partitions sont passées de Table A à Table B et ont été reconstruites, supprimez Table A, puis renommez Table B en l’appelant Table A. 

## <a name="examples"></a>Exemples

**A. Pour vérifier les colonnes ordonnées et le numéro d’ordre :**
```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. Pour modifier un numéro de colonne, ajouter ou supprimer des colonnes dans la liste des ordres ou passer d’un index columnstore cluster à un index columnstore cluster ordonné :**
```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, consultez l’article [Vue d’ensemble sur le développement SQL Data Warehouse](sql-data-warehouse-overview-develop.md).
