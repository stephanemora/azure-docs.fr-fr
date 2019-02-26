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
ms.openlocfilehash: 2de7c335e56117f2a99db5150575ed94616467e9
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56455583"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Notes de publication pour Azure SQL Data Warehouse
Cet article résume les nouvelles fonctionnalités et améliorations des versions récentes d'[Azure SQL Data Warehouse](sql-data-warehouse-overview-what-is.md). Il répertorie également les mises à jour de contenu importantes qui ne sont pas directement associées à la version, mais qui ont été publiées en même temps. Pour connaître les améliorations apportées aux autres services Azure, consultez [Mises à jour des services](https://azure.microsoft.com/updates)

## <a name="sql-data-warehouse-version-100101060-january"></a>SQL Data Warehouse Version 10.0.10106.0 (janvier)

### <a name="service-improvements"></a>Améliorations du service

| Améliorations du service | Détails |
| --- | --- |
|**Ordre de retour par optimisation**|Les requêtes SELECT…ORDER BY bénéficient d’un meilleur niveau de performance dans cette version.   Désormais, tous les nœuds de calcul envoient leurs résultats à un seul nœud de calcul, qui fusionne et trie les résultats qui sont renvoyés à l’utilisateur via le nœud de calcul.  Fusion par un seul nœud de calcul aboutit à un gain de performances significatif quand le jeu de résultats de la requête contient un grand nombre de lignes. Avant, le moteur d’exécution des requêtes ordonnait les résultats sur chaque nœud de calcul et les diffusait en continu au nœud de contrôle, lequel les fusionnait.|
|**Améliorations apportées au déplacement des données pour PartitionMove et BroadcastMove**|Dans Azure SQL Data Warehouse Gen2, les étapes de déplacement des données de type ShuffleMove exploitent les techniques de déplacement instantané des données décrites dans le [blog sur l’amélioration des performances](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/). Avec cette version, les types de déplacement des données PartitionMove et BroadcastMove bénéficient désormais des mêmes techniques de déplacement instantané des données. Les performances d’exécution des requêtes utilisateur qui utilisent ces types d’étapes de déplacement des données sont améliorées. Aucun changement de code n’est nécessaire pour tirer parti de ces gains de performances.|
|**Bogues notables**|Version incorrecte d'Azure SQL Data Warehouse - « SELECT @@VERSION » peut renvoyer la version incorrecte, 10.0.9999.0. La version correcte est actuellement la version 10.0.10106.0. Ce bogue a été signalé et est en cours d'évaluation.

### <a name="documentation-improvements"></a>Améliorations de la documentation

| Améliorations de la documentation | Détails |
| --- | --- |
|Aucun | |
| | |

## <a name="next-steps"></a>Étapes suivantes
- [Créer une base de données SQL Data Warehouse](./create-data-warehouse-portal.md)

## <a name="more-information"></a>Plus d’informations
- [Blog – Azure SQL Data Warehouse](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Blogs de l’équipe de conseil clientèle](https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/)
- [Témoignages de clients](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Forum Stack Overflow](http://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [Vidéos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Glossaire Azure](../azure-glossary-cloud-terminology.md)
