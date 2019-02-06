---
title: Vue d’ensemble du développement de base de données SQL | Microsoft Docs
description: En savoir plus sur les bibliothèques de connectivité disponibles et les meilleures pratiques pour les applications qui utilisent une connexion à la base de données SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 7473f89b711e804dbe96d299bc6f47adaceb6859
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55465212"
---
# <a name="sql-database-application-development-overview"></a>Vue d’ensemble du développement de base de données SQL

Cet article explique les aspects de base qu’un développeur doit prendre en compte lors de l’écriture de code permettant d’établir une connexion à la base de données SQL Azure.

> [!TIP]
> Pour savoir comment créer un serveur ou un pare-feu basé sur un serveur, afficher les propriétés d’un serveur, se connecter à l’aide de SQL Server Management Studio, interroger la base de données master, créer un exemple de base de données et une base de données vide, demander les propriétés d’une base de données, se connecter à l’aide de SQL Server Management Studio et interroger l’exemple de base de données, consultez le didacticiel [Prise en main des serveurs Azure SQL Database, des bases de données et des règles de pare-feu à l’aide du portail Azure et de SQL Server Management Studio](sql-database-get-started-portal.md).
>

## <a name="language-and-platform"></a>Langage et plateforme
Plusieurs exemples de code sont à votre disposition pour divers langages et plateformes de programmation. Vous trouverez des liens vers des exemples de code dans les articles suivants :

Plus d’informations : [Bibliothèques et frameworks de connectivité pour SQL Server](sql-database-libraries.md).

## <a name="tools"></a>Outils

Vous pouvez tirer parti des outils open source comme [cheetah](https://github.com/wunderlist/cheetah), [sql-cli](https://www.npmjs.com/package/sql-cli) et [VS Code](https://code.visualstudio.com/). En outre, Azure SQL Database fonctionne avec des outils Microsoft, tels que [Visual Studio](https://www.visualstudio.com/downloads/) et [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).  Vous pouvez également utiliser le portail de gestion Azure, PowerShell et les API REST pour accroître votre productivité.

## <a name="resource-limitations"></a>Limitations des ressources

Azure SQL Database gère les ressources disponibles dans une base de données selon deux mécanismes différents : Gouvernance des ressources et application des limites. Pour plus d'informations, consultez les pages suivantes :

- [Limites de modèle de ressources basées sur des unités DTU : base de données unique](sql-database-dtu-resource-limits-single-databases.md)
- [Limites de modèle de ressources basées sur des unités DTU - Pools élastiques](sql-database-dtu-resource-limits-elastic-pools.md)
- [Limites de modèle de ressources basées sur des vCore : base de données unique](sql-database-vcore-resource-limits-single-databases.md)
- [Limites de ressources basées sur des vCore - Pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md)

## <a name="security"></a>Sécurité

La base de données SQL Azure fournit des ressources permettant de limiter l’accès, de protéger les données et de surveiller les activités sur une base de données SQL.

* Plus d’informations : [Une vue d’ensemble des fonctionnalités de sécurité d’Azure SQL Database](sql-database-security-overview.md).

## <a name="authentication"></a>Authentification

- La base de données SQL Azure prend en charge les utilisateurs et les connexions de l’authentification SQL Server, ainsi que les utilisateurs et les connexions de [l’authentification Azure Active Directory](sql-database-aad-authentication.md) .
- Vous devez définir une base de données spécifique, au lieu de la base de données *MASTER* par défaut.
- Vous ne pouvez pas utiliser l’instruction **USE myDatabaseName;** Transact-SQL sur une base de données SQL pour basculer vers une autre base de données.
- Plus d’informations : [Sécurité SQL Database : gérer la sécurité de l’accès aux bases de données et des connexions](sql-database-manage-logins.md).

## <a name="resiliency"></a>Résilience

Lorsqu’une erreur temporaire se produit au moment de la connexion à la base de données SQL, votre code doit exécuter une nouvelle tentative d’appel.  Nous vous recommandons d’utiliser une logique de nouvelle tentative basée sur une logique d’interruption, afin d’éviter que la base de données SQL ne soit inondée de tentatives simultanées de plusieurs clients.

- Exemples de code :  Pour obtenir des exemples de code qui illustrent des logique de nouvelle tentative, consultez les exemples correspondant à la langue de votre choix dans la rubrique suivante : [Bibliothèques et frameworks de connectivité pour SQL Server](sql-database-libraries.md).
- Plus d’informations : [Codes d’erreur SQL pour les applications clientes SQL Database : erreurs de connexion de base de données et autres problèmes](sql-database-develop-error-messages.md).

## <a name="managing-connections"></a>Gestion des connexions

- Dans votre logique de connexion client, définissez le délai d’expiration sur 30 secondes.  La valeur par défaut de 15 secondes est trop courte pour les connexions qui reposent sur Internet.
- Si vous utilisez un [pool de connexions](https://msdn.microsoft.com/library/8xx3tyca.aspx), veillez à fermer la connexion dès que votre programme ne l’utilise plus activement et qu’il ne se prépare pas à le réutiliser.

## <a name="network-considerations"></a>Considérations relatives au réseau

- Assurez-vous que le pare-feu de l’ordinateur qui héberge votre programme client autorise les communications TCP sortantes sur le port 1433.  Plus d’informations : [Règles de pare-feu Azure SQL Database et SQL Data Warehouse](sql-database-configure-firewall-settings.md).
- Si votre programme client se connecte à la base de données SQL pendant que votre client s’exécute sur une machine virtuelle Azure, vous devez ouvrir certaines plages de ports sur la machine virtuelle. Plus d’informations : [Ports au-delà de 1433 pour ADO .NET 4.5 et SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
- Parfois, les connexions clientes à Azure SQL Database ignorent le proxy et interagissent directement avec la base de données. Les ports autres que le port 1433 deviennent importants. Pour plus d’informations, consultez [Azure SQL Database connectivity architecture](sql-database-connectivity-architecture.md) (Architecture de connectivité d’Azure SQL Database) et [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="data-sharding-with-elastic-scale"></a>Partitionnement de données avec mise à l’échelle élastique

La mise à l’échelle élastique simplifie le processus d’augmentation/diminution (scaling out/in). 

- [Modèles de conception pour les applications SaaS mutualisées avec Microsoft Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).
- [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md).
- [Bien démarrer avec l’infrastructure élastique de Microsoft Azure SQL Database (préversion)](sql-database-elastic-scale-get-started.md).

## <a name="next-steps"></a>Étapes suivantes

Explorez toutes les [fonctionnalités de la base de données SQL](sql-database-technical-overview.md).
