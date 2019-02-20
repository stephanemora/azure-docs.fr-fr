---
title: Notes de publication Azure SQL Data Warehouse | Microsoft Docs
description: Notes de publication pour Azure SQL Data Warehouse.
services: sql-data-warehouse
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/09/2019
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
manager: craigg
ms.openlocfilehash: 51932ebf7d5bdc6830098ce7136a3eee7255ffe1
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245505"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Notes de publication pour Azure SQL Data Warehouse
Cet article résume les nouvelles fonctionnalités et les améliorations présentes dans les versions récentes de [SQL Server sur des machines virtuelles Azure](sql-data-warehouse-overview-what-is.md). Il répertorie également les mises à jour de contenu importantes qui ne sont pas directement associées à la version, mais qui ont été publiées en même temps. Pour connaître les améliorations apportées aux autres services Azure, consultez [Mises à jour des services](https://azure.microsoft.com/updates)

## <a name="sql-data-warehouse-version-100101060-january"></a>SQL Data Warehouse Version 10.0.10106.0 (janvier)

### <a name="service-improvements"></a>Améliorations du service

| Améliorations du service | Détails |
| --- | --- |
| **Capacité de redémarrage des requêtes – CTAS et Insert/Select** | Dans de rares cas (problèmes de connexion réseau intermittents, échecs liés aux nœuds), une requête exécutée dans Azure SQL DW peut échouer. Les instructions de plus longue durée, telles que les opérations [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-ctas) et INSERT-SELECT, sont davantage exposées à ce problème potentiel. Avec cette mise en production, Azure SQL Data Warehouse implémente une logique de nouvelle tentative pour les instructions CTAS et INSERT-SELECT, en plus des instructions SELECT annoncées précédemment. Les modifications permettent au service de gérer des problèmes temporaires en toute transparence et de prévenir les échecs de requêtes. Le nombre de nouvelles tentatives et la liste des erreurs transitoires gérées sont configurés par le système.|
|**Ordre de retour par optimisation**|Les requêtes SELECT…ORDER BY bénéficient d’un meilleur niveau de performance dans cette version.   Désormais, tous les nœuds de calcul envoient leurs résultats à un seul nœud de calcul, qui fusionne et trie les résultats qui sont renvoyés à l’utilisateur via le nœud de calcul.  Fusion par un seul nœud de calcul aboutit à un gain de performances significatif quand le jeu de résultats de la requête contient un grand nombre de lignes. Avant, le moteur d’exécution des requêtes ordonnait les résultats sur chaque nœud de calcul et les diffusait en continu au nœud de contrôle, lequel les fusionnait.|
|**Améliorations apportées au déplacement des données pour PartitionMove et BroadcastMove**|Dans Azure SQL Data Warehouse Gen2, les étapes de déplacement des données de type ShuffleMove exploitent les techniques de déplacement instantané des données décrites dans le [blog sur l’amélioration des performances](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/). Avec cette version, les types de déplacement des données PartitionMove et BroadcastMove bénéficient désormais des mêmes techniques de déplacement instantané des données. Les performances d’exécution des requêtes utilisateur qui utilisent ces types d’étapes de déplacement des données sont améliorées. Aucun changement de code n’est nécessaire pour tirer parti de ces gains de performances.|

### <a name="documentation-improvements"></a>Améliorations de la documentation

| Améliorations de la documentation | Détails |
| --- | --- |
|Aucun | |
| | |

## <a name="next-steps"></a>Étapes suivantes
[Créer une base de données SQL Data Warehouse](./create-data-warehouse-portal.md)

## <a name="more-information"></a>Plus d’informations
- [Blog – Azure SQL Data Warehouse](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Blogs de l’équipe de conseil clientèle](https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/)
- [Témoignages de clients](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Forum Stack Overflow](http://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [Vidéos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Glossaire Azure](../azure-glossary-cloud-terminology.md)