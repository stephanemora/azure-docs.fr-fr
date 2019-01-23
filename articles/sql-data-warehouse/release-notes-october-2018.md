---
title: Notes de publication Azure SQL Data Warehouse octobre 2018 | Microsoft Docs
description: Notes de publication pour Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 12/04/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 9160a5f4e3a452682787ff500199e43e7fad0c77
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54213687"
---
# <a name="whats-new-in-azure-sql-data-warehouse-october-2018"></a>Nouveautés dans Azure SQL Data Warehouse Octobre 2018
Azure SQL Data Warehouse reçoit continuellement des améliorations. Cet article décrit les nouvelles fonctionnalités et les modifications qui ont été introduites en octobre 2018.

## <a name="devops-for-data-warehousing"></a>DevOps pour l’entrepôt de données
La fonctionnalité très demandée pour SQL Data Warehouse (SQL DW) est désormais disponible en préversion avec la prise en charge de SQL Server Data Tool (SSDT) dans Visual Studio ! Les équipes de développeurs peuvent désormais collaborer sur une base de code unique, avec contrôle de version, et déployer rapidement les modifications apportées à n’importe quelle instance dans le monde. Vous voulez participer ? Cette fonctionnalité est disponible en préversion dès aujourd’hui ! Vous pouvez vous inscrire en consultant [SQL Data Warehouse Visual Studio SQL Server Data Tools (SSDT) - Formulaire d’inscription à la préversion](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u). Étant donné la forte demande, nous gérons l’admission à la préversion pour garantir la meilleure expérience à nos clients. Une fois que vous vous inscrivez, notre objectif est de vérifier votre statut dans les sept jours qui suivent.

## <a name="row-level-security-generally-available"></a>Sécurité au niveau des lignes mise à la disposition générale
Azure SQL Data Warehouse (SQL DW) prend désormais en charge la sécurité au niveau des lignes (RLS), ajoutant ainsi une fonctionnalité puissante pour sécuriser vos données sensibles. Avec l’introduction de la sécurité au niveau des lignes, vous pouvez implémenter des stratégies de sécurité pour contrôler l’accès aux lignes de vos tables, en indiquant qui peut accéder à quelles lignes. La sécurité au niveau des lignes active ce contrôle d’accès affiné sans avoir à repenser votre entrepôt de données. Cela simplifie le modèle de sécurité global, car la logique de restriction d’accès se situe dans la couche de base de données plutôt qu’à l’écart des données d’une autre application. Cette fonctionnalité élimine également le besoin d’introduire des vues pour filtrer les lignes pour la gestion du contrôle d’accès. Il n’existe aucun coût supplémentaire pour cette fonctionnalité de sécurité de niveau entreprise pour tous nos clients.

## <a name="advanced-advisors"></a>Conseillers avancés
Le réglage avancé pour Azure SQL Data Warehouse (SQL DW) a été simplifié avec les métriques et les recommandations supplémentaires pour l’entrepôt de données. Des recommandations supplémentaires en matière de performances avancées sont disponibles dans Azure Advisor, notamment :
1.  Cache adaptatif : vous êtes averti lorsqu’il est nécessaire de procéder à une mise à l'échelle pour optimiser l’utilisation du cache.
2.  Distribution de table : vous déterminez le moment de la réplication des tables pour réduire le déplacement des données et accroître les performances de la charge de travail. 
3.  Tempdb : vous savez quand procéder à une mise à l'échelle et configurer des classes de ressources pour réduire le conflit de tempdb.

Les métriques de l’entrepôt de données sont mieux intégrées à [Azure Monitor](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/) avec, par exemple, un graphique de supervision amélioré personnalisable pour des métriques en quasi temps réel dans le panneau de vue d’ensemble. Il n’est plus nécessaire de quitter le panneau de vue d’ensemble de l’entrepôt de données pour accéder aux métriques d’Azure Monitor en cas de supervision de l’utilisation, ou de validation et d’application de recommandations de l’entrepôt de données. En outre, de nouvelles métriques sont disponibles, comme l’utilisation de tempdb et du cache adaptatif, en guise de complément à vos recommandations en matière de performances.

## <a name="advanced-tuning-with-integrated-advisors"></a>Réglage avancé avec les conseillers intégrés
Le réglage avancé pour Azure SQL Data Warehouse (SQL DW) a été simplifié avec les métriques et les recommandations supplémentaires pour l’entrepôt de données, ainsi qu’une refonte du panneau de présentation du portail qui fournit une expérience intégrée avec Azure Advisor et Azure Monitor.

## <a name="accelerated-database-recovery-adr"></a>Récupération de base de données accélérée (ADR)
La Récupération de base de données accélérée (ADR) Azure SQL Data Warehouse est désormais disponible en préversion publique. ADR est un nouveau moteur SQL Server qui améliore considérablement la disponibilité des bases de données, en particulier en présence de transactions d’une durée d’exécution longue, grâce à une toute nouvelle conception du processus de récupération actuel. Les principaux avantages d’ADR sont la récupération rapide et cohérente de la base de données et la restauration des transactions instantanée.

## <a name="azure-monitor-diagnostics-logs"></a>Journaux de diagnostics Azure Monitor
SQL Data Warehouse (SQL DW) propose maintenant des aperçus améliorés des charges de travail d’analytique grâce à une intégration directe des journaux de diagnostic Azure Monitor. Grâce à cette nouvelle fonctionnalité, les développeurs peuvent analyser le comportement de la charge de travail pendant une période étendue et prendre des décisions informées sur l’optimisation des requêtes ou la gestion de la capacité. Nous avons maintenant introduit un processus externe de journalisation via [les journaux de diagnostic Azure Monitor](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json#logs) qui fournissent des insights supplémentaires sur la charge de travail de votre entrepôt de données. Avec un seul clic sur un bouton, vous pouvez maintenant configurer les journaux de diagnostic pour obtenir les fonctionnalités de résolution des problèmes de performances des requêtes historiques à l’aide de [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries). Les journaux de diagnostic Azure Monitor prennent en charge les périodes de rétention personnalisables en enregistrant les journaux dans un compte de stockage à des fins d’audit, la fonctionnalité de diffusion en continu de journaux dans les Event Hubs pour des aperçus de télémétrie en quasi temps réel et la possibilité d’analyser des journaux à l’aide de Log Analytics avec les requêtes de journal. Les journaux de diagnostic se composent de vues de télémétrie de votre entrepôt de données équivalentes aux vues de gestion dynamique (DMV) de résolution des problèmes de performance les plus fréquentes pour SQL Data Warehouse. Pour cette première version, nous avons activé les vues pour les vues de gestion dynamique du système suivantes :

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql)

## <a name="columnstore-memory-management"></a>Gestion de la mémoire Columnstore
À mesure que le nombre de groupes de lignes de la banque des colonnes compressée augmente, la mémoire requise pour gérer les métadonnées du segment de colonne interne pour ces groupes de lignes augmente.  Par conséquent, les performances des requêtes et les requêtes exécutées sur certaines vues de gestion dynamique (DMV) de Columnstore peuvent se dégrader.  Des améliorations ont été apportées dans cette version afin d’optimiser la taille des métadonnées internes dans ces cas particuliers, ce qui améliore à la fois l’expérience et les performances avec de telles requêtes. 

## <a name="azure-data-lake-storage-gen2-integration-ga"></a>Intégration d’Azure Data Lake Storage Gen2 (Disponibilité générale)
Azure SQL Data Warehouse (SQL DW) bénéficie maintenant de l’intégration native avec Azure Data Lake Storage Gen2. Les clients peuvent désormais charger les données à l’aide de tables externes à partir d’ABFS dans SQL DW. Cette fonctionnalité permet aux clients d’intégrer avec leurs Data Lakes dans Data Lake Storage Gen2. 

## <a name="bug-fixes"></a>Résolution des bogues

| Intitulé | Description |
|:---|:---|
| **Échecs CETAS à Parquet dans les petites classes de ressources sur les entrepôts de données de DW2000 et plus** | Ce correctif identifie correctement une référence null dans le chemin d’accès au code Create External Table As to Parquet. |
|**La valeur de colonne d’identité peut se perdre dans une opération CTAS** | La valeur d’une colonne d’identité peut ne pas être conservée en cas d’opération CTAS vers une autre table. Signalé dans un blog : [https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/). |
| **Erreur interne dans certains cas lorsqu’une session est interrompue alors qu’une requête est en cours d’exécution** | Cette correction déclenche InvalidOperationException si une session est interrompue lorsque la requête est en cours d’exécution. |
| **(Déployée en novembre 2018) Les clients ont rencontré des performances réduites lors de la tentative de chargement de plusieurs petits fichiers depuis ADLS (Gen1) à l’aide de Polybase.** | Les performances du système se sont trouvées dans un goulot d’étranglement lors de la validation du jeton de sécurité AAD. Les problèmes de performances ont été réduits en activant la mise en cache des jetons de sécurité. |


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
