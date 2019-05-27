---
title: Notes de publication Azure SQL Data Warehouse août 2018 | Microsoft Docs
description: Notes de publication pour Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/13/2018
ms.author: anjangsh
ms.reviewer: jrasnick
ms.openlocfilehash: f0840e9b91c81b8a99e8c736c3c5db082c92fe76
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65912212"
---
# <a name="whats-new-in-azure-sql-data-warehouse-august-2018"></a>Nouveautés dans Azure SQL Data Warehouse Août 2018
Azure SQL Data Warehouse reçoit continuellement des améliorations. Cet article décrit les nouvelles fonctionnalités et les modifications qui ont été introduites en août 2018.

## <a name="automatic-intelligent-insights"></a>Intelligent Insights automatique
Microsoft a introduit [Intelligent Insights automatique](https://azure.microsoft.com/blog/automatic-intelligent-insights-to-optimize-performance-with-sql-data-warehouse/) pour satisfaire à la promesse d’automatisation sur le cloud de votre entrepôt de données. Vous n’avez plus besoin d’analyser votre entrepôt de données pour obtenir des statistiques de tables non optimales et d’asymétrie des données. Sans coût supplémentaire, SQL Data Warehouse met en évidence les insights intelligents de toutes les instances Gen2. Avec une intégration à [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations), vous pouvez recevoir automatiquement les suggestions en matière de bonnes pratiques, afin d’améliorer les performances de vos charges de travail actives. SQL Data Warehouse analyse votre charge de travail et présente des suggestions pertinentes par rapport à votre utilisation. Cette analyse est effectuée quotidiennement, ce qui vous permet de contrôler les rapports d’utilisation et les suggestions en vue d’apporter des améliorations à votre charge de travail.

Vous pouvez afficher les suggestions dans le portail Azure Advisor : ![Suggestions du portail Azure Advisor pour Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/4e205b6d-df04-48db-8eec-d591f2592cf4.png)

Vous pouvez explorer chaque catégorie pour afficher les suggestions pour l’alerte spécifique : ![Détails des suggestions du portail Azure Advisor pour Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/3c42426e-6969-46e3-9025-c34c0755a302.png)


## <a name="bug-fixes"></a>Résolution des bogues

| Intitulé | Description  |
|:---|:---|
| **Échecs de requêtes potentiels lorsque le nombre de fragments dépasse la limite maximale autorisée** |Lorsque la limite maximale d’1 million de fragments de fichier a été dépassée, une exception non prise en charge a contraint le moteur SQL à vider la base de données et toutes les requêtes ont échoué. Ce correctif a permis de résoudre le problème en gérant de manière appropriée l’exception et en retournant une erreur sans provoquer l’échec des requêtes. |
| **Augmentation de la valeur ExternalMoveReadersPerNode par défaut pour améliorer les performances de chargement** |Ce problème est survenu suite à une erreur de synchronisation de la propriété ExternalMoveReadersPerNode avec le paramètre de structure de service. Cette régression a entraîné une dégradation des performances de chargement de deuxième génération. Le correctif rétablit les performances de chargement de deuxième génération avec des paramètres de conception optimisés.|


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
