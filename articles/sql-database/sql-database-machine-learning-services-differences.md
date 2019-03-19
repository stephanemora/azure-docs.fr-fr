---
title: Principales différences pour Azure SQL Database Machine Learning Services (version préliminaire)
description: Cette rubrique décrit les principales différences entre Azure SQL Database Machine Learning Services (avec R) et SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 57ea52c179376e8378680f436d396ffaf9357f68
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57771848"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Principales différences entre les Services Machine Learning dans Azure SQL Database (version préliminaire) et SQL Server

La fonctionnalité de base de données de SQL Azure Machine Learning Services (avec R) (version préliminaire) est similaire à [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Voici quelques différences clés.

> [!IMPORTANT]
> Services d’apprentissage Machine de base de données SQL Azure est actuellement en version préliminaire publique.
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
- Les packages ne peuvent pas effectuer d’appels réseau sortants. Cette limitation est similaire à la [par défaut des règles de pare-feu pour Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) dans SQL Server, mais ne peut pas être modifiée dans la base de données SQL.
- Les packages qui dépendent de runtimes externes (comme Java) ou dont l’installation ou l’utilisation impliquent un accès à des API de système d’exploitation ne sont pas pris en charge.

## <a name="resource-governance"></a>Gouvernance des ressources

Il n’est pas possible de limiter les ressources R avec [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) et les listes de ressources externes.

Pendant la préversion publique, les ressources R sont définies sur un maximum de 20 % des ressources de base de données SQL et varient dans le niveau de service que vous choisissez. Pour plus d’informations, voir [Modèles d’achat Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Erreur de mémoire insuffisante

Si la mémoire est insuffisante pour R, vous obtiendrez un message d’erreur. Messages d’erreur courants sont :

- Impossible de communiquer avec le runtime de script « R » pour l’id de demande : ***. Vérifiez la configuration requise du runtime « R »
- Erreur de script « R » s’est produite pendant l’exécution de « sp_execute_external_script » avec HRESULT 0 x 80004004. ... une erreur de script externe s’est produite : «.. n’a pas pu allouer la mémoire (0 Mo) dans la fonction C 'R_AllocStringBuffer' »
- Une erreur de script externe s’est produite : Erreur : Impossible d’allouer le vecteur de taille.

Utilisation dépend de la quantité de mémoire est utilisée dans vos scripts R et le nombre de requêtes parallèles en cours d’exécution. Si vous recevez les erreurs ci-dessus, vous pouvez faire évoluer votre base de données à un niveau de service supérieur pour résoudre ce problème.

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir des informations générales, voir la documentation [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics).
- Pour apprendre à utiliser Machine Learning Services (avec R) dans Azure SQL Database, voir [Guide de démarrage rapide](sql-database-connect-query-r.md).
- Pour plus d’informations, voir [Tutoriels du langage R avec SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials).