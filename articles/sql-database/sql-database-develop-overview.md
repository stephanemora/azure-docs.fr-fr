---
title: Vue d’ensemble du développement de base de données SQL | Microsoft Docs
description: En savoir plus sur les bibliothèques de connectivité disponibles et les meilleures pratiques pour les applications qui utilisent une connexion à la base de données SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: stevestein
ms.author: sstein
ms.reviewer: genemi
manager: craigg
ms.date: 02/06/2019
ms.openlocfilehash: d9de6100e3bb7c3cc71a7a251d790df4907be5f2
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820349"
---
# <a name="sql-database-application-development-overview"></a>Vue d’ensemble du développement de base de données SQL

Cet article explique les aspects de base qu’un développeur doit prendre en compte lors de l’écriture de code permettant d’établir une connexion à la base de données SQL Azure. Cet article s’applique à tous les modèles de déploiement d’Azure SQL Database (base de données unique, pools élastiques, instance gérée).

> [!TIP]
> Recherchez [Base de données unique](sql-database-single-database-quickstart-guide.md) et [Instance gérée](sql-database-managed-instance-quickstart-guide.md) dans les guides de prise en main si vous devez configurer Azure SQL Database.
>

## <a name="language-and-platform"></a>Langage et plateforme

Vous pouvez utiliser différents [langages de programmation et plateformes](sql-database-connect-query.md) pour vous connecter à Azure SQL Database et l’interroger. Vous trouverez des [exemples d’application](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) qui peuvent vous permettre de vous connecter à Azure SQL Database.

Vous pouvez tirer parti des outils open source comme [cheetah](https://github.com/wunderlist/cheetah), [sql-cli](https://www.npmjs.com/package/sql-cli) et [VS Code](https://code.visualstudio.com/). En outre, Azure SQL Database fonctionne avec des outils Microsoft, tels que [Visual Studio](https://www.visualstudio.com/downloads/) et [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx). Le Portail Azure, PowerShell et les API REST peuvent également contribuer à accroître votre productivité.

## <a name="authentication"></a>Authentication

L’accès à Azure SQL Database est protégé par des connexions et des pare-feu. Azure SQL Database prend en charge les utilisateurs et les connexions de l’authentification SQL Server, ainsi que les utilisateurs et les connexions de [l’authentification Azure Active Directory (AAD)](sql-database-aad-authentication.md). Les connexions AAD ne sont disponibles que dans Managed Instance. 

En savoir plus sur la [gestion des accès et des connexions aux bases de données](sql-database-manage-logins.md).

## <a name="connections"></a>connexions

Dans votre logique de connexion client, définissez le délai d’expiration sur 30 secondes. La valeur par défaut de 15 secondes est trop courte pour les connexions qui reposent sur Internet.

Si vous utilisez un [pool de connexions](https://msdn.microsoft.com/library/8xx3tyca.aspx), veillez à fermer la connexion dès que votre programme ne l’utilise plus activement et qu’il ne se prépare pas à le réutiliser.

Évitez les transactions de longue durée, car tout échec de connexion ou d’infrastructure peut restaurer la transaction. Si possible, fractionnez la transaction en plusieurs transactions plus petites et utilisez le [traitement par lot pour améliorer les performances](sql-database-use-batching-to-improve-performance.md).

## <a name="resiliency"></a>Résilience

Azure SQL Database est un service cloud où vous pouvez attendre des erreurs temporaires qui se produisent dans l’infrastructure sous-jacente ou dans la communication entre les entités du cloud.
Bien qu’Azure SQL Database résiste aux échecs d’infrastructure transitive, ces échecs peuvent affecter votre connectivité. Lorsqu’une erreur temporaire se produit au moment de la connexion à SQL Database, votre code doit effectuer une [nouvelle tentative d’appel](sql-database-connectivity-issues.md). Nous vous recommandons d’utiliser une logique de nouvelle tentative basée sur une logique d’interruption, afin d’éviter que la base de données SQL ne soit inondée de tentatives simultanées de plusieurs clients. La logique de nouvelle tentative dépend des [messages d’erreur pour les programmes clients SQL Database](sql-database-develop-error-messages.md).

## <a name="network-considerations"></a>Considérations relatives au réseau

- Assurez-vous que le pare-feu de l’ordinateur qui héberge votre programme client autorise les communications TCP sortantes sur le port 1433.  Plus d’informations : [Règles de pare-feu Azure SQL Database et SQL Data Warehouse](sql-database-configure-firewall-settings.md).
- Si votre programme client se connecte à la base de données SQL pendant que votre client s’exécute sur une machine virtuelle Azure, vous devez ouvrir certaines plages de ports sur la machine virtuelle. Plus d’informations : [Ports au-delà de 1433 pour ADO .NET 4.5 et SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).
- Parfois, les connexions clientes à Azure SQL Database ignorent le proxy et interagissent directement avec la base de données. Les ports autres que le port 1433 deviennent importants. Pour plus d’informations, consultez [Azure SQL Database connectivity architecture](sql-database-connectivity-architecture.md) (Architecture de connectivité d’Azure SQL Database) et [Ports au-delà de 1433 pour ADO.NET 4.5 et SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

## <a name="next-steps"></a>Étapes suivantes

Explorez toutes les [fonctionnalités de la base de données SQL](sql-database-technical-overview.md).
