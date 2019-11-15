---
title: Principales différences de Machine Learning Services (préversion)
description: Cette rubrique décrit les principales différences entre Azure SQL Database Machine Learning Services (avec R) et SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 1397f5d81ddf63740d733111b965a0517a2b917f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827465"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Principales différences entre Machine Learning Services dans Azure SQL Database (préversion) et Machine Learning Services dans SQL Server

La fonctionnalité de Machine Learning Services (avec R) dans Azure SQL Database (préversion) est similaire à la fonctionnalité [Machine Learning Services dans SQL Server](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Voici cependant quelques différences importantes.

> [!IMPORTANT]
> Machine Learning Services dans Azure SQL Database est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="language-support"></a>Support multilingue

SQL Server prend en charge R et Python grâce à [Extensibility Framework](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework), tandis que SQL Database ne gère pas les deux langages :

- R est le seul langage pris en charge dans SQL Database. Il n’existe aucune prise en charge de Python pour l’instant.
- La version de R est 3.4.4.
- Il n’est pas nécessaire de configurer `external scripts enabled` avec `sp_configure`. Après [inscription](sql-database-machine-learning-services-overview.md#signup), le Machine Learning est activé pour la base de données SQL concernée.

## <a name="package-management"></a>Gestion des packages

L’installation et la gestion des packages R fonctionnent différemment pour SQL Database et pour SQL Server :

- Les packages R s’installent avec [sqlmlutils](https://github.com/Microsoft/sqlmlutils) ou [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Les packages ne peuvent pas effectuer d’appels réseau sortants. Cette limitation est similaire aux [règles de pare-feu par défaut de Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) dans SQL Server, mais elle n’est pas modifiable dans SQL Database.
- Les packages qui dépendent de runtimes externes (comme Java) ou dont l’installation ou l’utilisation impliquent un accès à des API de système d’exploitation ne sont pas pris en charge.

## <a name="writing-to-a-temporary-table"></a>Écriture dans une table temporaire

Si vous utilisez RODBC dans Azure SQL Database, vous ne pouvez pas écrire dans une table temporaire, y compris si elle est créée à l’intérieur ou en dehors de la session `sp_execute_external_script`. La solution de contournement consiste à utiliser [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) et [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (avec overwrite=FALSE et append="rows") pour écrire dans une table temporaire globale créée avant la requête `sp_execute_external_script`.

## <a name="resource-governance"></a>Gouvernance des ressources

Il n’est pas possible de limiter les ressources R avec [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) et les listes de ressources externes.

Pendant la préversion publique, les ressources R englobent au maximum 20 % des ressources SQL Database, et le pourcentage précis dépend du niveau de service choisi. Pour plus d’informations, voir [Modèles d’achat Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).
### <a name="insufficient-memory-error"></a>Erreur de mémoire insuffisante

Si la mémoire est insuffisante pour R, un message d’erreur s’affiche. Les messages d’erreur courants sont les suivants :

- Impossible de communiquer avec le runtime pour le script « R » et l’ID de demande *******. Vérifiez les exigences du runtime « R »
- Une erreur de script « R » s’est produite lors de l’exécution de « sp_execute_external_script » avec HRESULT 0x80004004. ...Une erreur de script externe s’est produite : « Impossible d’allouer de la mémoire (0 Mb) dans la fonction C 'R_AllocStringBuffer' »
- Une erreur de script externe s’est produite : Erreur : impossible d’allouer un vecteur de taille.

L’utilisation de la mémoire dépend de la quantité de mémoire utilisée dans vos scripts R et du nombre de requêtes parallèles en cours d’exécution. Si les messages d’erreur ci-dessus s’affichent, vous pouvez augmenter l’échelle votre base de données pour l’adapter à un niveau de service supérieur afin de résoudre ce problème.

## <a name="next-steps"></a>Étapes suivantes

- Lisez la vue d’ensemble de [Machine Learning Services avec R dans Azure SQL Database (préversion)](sql-database-machine-learning-services-overview.md).
- Pour savoir comment utiliser R pour interroger Machine Learning Services dans Azure SQL Database (version préliminaire), voir le [Guide de démarrage rapide](sql-database-connect-query-r.md).
- Pour commencer avec des scripts R simples, voir [Créer et exécuter des scripts R simples dans Azure SQL Database Machine Learning Services (préversion)](sql-database-quickstart-r-create-script.md).
