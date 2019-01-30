---
title: Notes de publication Azure SQL Data Warehouse - Décembre 2018 | Microsoft Docs
description: Notes de publication pour Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 12/12/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 074276e4550b9b2e347e5cd30c597a1d09f6cb2f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54440083"
---
# <a name="whats-new-in-azure-sql-data-warehouse-version---100101060"></a>Nouveautés d’Azure SQL Data Warehouse version 10.0.10106.0
Azure SQL Data Warehouse (SQL DW) est en constante évolution. Cet article décrit les nouvelles fonctionnalités et les changements introduits dans SQL DW version 10.0.10106.0.

## <a name="query-restartability---ctas-and-insertselect"></a>Capacité de redémarrage des requêtes - CTAS et Insert/Select
Dans de rares cas (problèmes de connexion réseau intermittents, échecs liés aux nœuds), des requêtes exécutées dans Azure SQL DW peuvent échouer. Les instructions de plus longue durée, telles que les opérations [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-ctas) et INSERT-SELECT, sont davantage exposées à ce problème potentiel. Avec cette version, Azure SQL DW implémente la logique de nouvelle tentative pour les instructions CTAS et INSERT-SELECT (en plus des instructions SELECT annoncées précédemment), permettant ainsi au système de gérer de manière transparente ces problèmes transitoires et d’empêcher les requêtes d’échouer. Le nombre de nouvelles tentatives et la liste des erreurs transitoires gérées sont configurés par le système.

## <a name="return-order-by-optimization"></a>Ordre de retour par optimisation
Les requêtes SELECT…ORDER BY bénéficient d’un meilleur niveau de performance dans cette version.  Avant, le moteur d’exécution des requêtes ordonnait les résultats sur chaque nœud de calcul et les diffusait en continu au nœud de contrôle, lequel les fusionnait. Avec cette amélioration, tous les nœuds de calcul envoient désormais leurs résultats à un seul nœud de calcul, qui les fusionne et retourne les résultats triés à l’utilisateur par le biais du nœud de calcul.  Cela aboutit à un gain de performances significatif quand le jeu de résultats de la requête contient un grand nombre de lignes.

## <a name="data-movement-enhancements-for-partitionmove-and-broadcastmove"></a>Améliorations apportées au déplacement des données pour PartitionMove et BroadcastMove
Dans Azure SQL Data Warehouse Gen2, les étapes de déplacement des données de type ShuffleMove exploitent les techniques de déplacement instantané des données décrites dans le [blog sur l’amélioration des performances](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/).  Avec cette version, les types de déplacement des données PartitionMove et BroadcastMove bénéficient désormais des mêmes techniques de déplacement instantané des données.  Les requêtes utilisateur qui utilisent ces types d’étapes de déplacement des données voient leurs performances augmenter.  Aucun changement de code n’est nécessaire pour tirer parti de ces gains de performances.

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
[Forum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vidéos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
