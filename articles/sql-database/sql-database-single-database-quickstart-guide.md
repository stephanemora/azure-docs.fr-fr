---
title: Démarrage rapide – Bases de données uniques
description: Découvrez comment démarrer rapidement avec des bases de données uniques dans Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
ms.date: 07/29/2019
ms.openlocfilehash: 6070b53e5f906bc378402d98275b8f798f57b505
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78674431"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Bien démarrer avec des bases de données uniques dans Azure SQL Database

Une [base de données unique](sql-database-single-index.yml) est une base de données en tant que service PaaS entièrement managée qui constitue le moteur de stockage idéal pour les applications cloud modernes. Dans cette section, vous allez découvrir comment configurer et créer rapidement une base de données unique dans SQL Database.

## <a name="quickstart-overview"></a>Vue d’ensemble du guide de démarrage rapide

Cette section contient une vue d’ensemble des articles disponibles qui peuvent vous aider à démarrer rapidement avec des bases de données uniques. Les guides de démarrage rapide suivants vous permettent de créer une base de données unique, de configurer une règle de pare-feu de serveur de base de données, puis d’importer une base de données dans la nouvelle base de données unique à l’aide d’un fichier `.bacpac` :

- [Créez une base de données unique à l’aide du portail Azure](sql-database-single-database-get-started.md).
- Après avoir créé la base de données, vous devez [la sécuriser en configurant des règles de pare-feu](sql-database-server-level-firewall-rule.md).
- Si vous avez une base de données existante sur SQL Server que vous souhaitez migrer vers Azure, vous devez installer l’[Assistant Migration de données](https://www.microsoft.com/download/details.aspx?id=53595), qui analyse vos bases de données sur SQL Server et recherche les problèmes susceptibles de bloquer la migration vers l’option de déploiement Base de données unique. Si vous ne trouvez pas de problème, vous pouvez exporter votre base de données en tant que fichier `.bacpac` et [l’importer en utilisant le portail Azure ou SqlPackage](sql-database-import.md).

## <a name="automating-management-operations"></a>Automatisation des opérations de gestion

Vous pouvez utiliser PowerShell ou Azure CLI pour créer, configurer et mettre à l’échelle votre base de données.

- [Créer et configurer une base de données unique à l’aide de PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) ou d’[Azure CLI](scripts/sql-database-create-and-configure-database-cli.md)
- [Mettre à jour votre base de données unique et mettre à l’échelle vos ressources avec PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md) ou [Azure CLI](scripts/sql-database-monitor-and-scale-database-cli.md)

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migration vers une base de données unique avec un temps d’arrêt minimal

Ces guides de démarrage rapide vous permettent de créer ou d’importer votre base de données dans Azure avec un fichier `.bacpac`. Cependant, les fichiers `.bacpac` et `.dacpac` sont conçus pour déplacer rapidement des bases de données entre des versions différentes de SQL Server et des options de déploiement dans Azure SQL Database, ou pour implémenter l’intégration continue dans votre pipeline DevOps. Toutefois, cette méthode n’est pas conçue pour la migration de vos bases de données de production avec un temps d’arrêt minimal dans la mesure où vous devez cesser d’ajouter de nouvelles données, attendre la fin de l’exportation de la base de données source vers un fichier `.bacpac`, puis attendre la fin de l’importation dans Azure SQL Database. Toute cette attente entraîne des temps d’arrêt pour votre application, en particulier si les bases de données sont volumineuses. Pour déplacer votre base de données de production, vous devez trouver une meilleure méthode de migration garantissant un temps d’arrêt minimal. Pour cela, utilisez [Data Migration Service (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) pour migrer votre base de données avec un temps d’arrêt minimal. Pour y parvenir, DMS envoie (push) les changements effectués dans votre base de données source à la base de données unique en cours de restauration. De cette façon, vous pouvez basculer rapidement votre application de la base de données source vers la base de données cible avec un temps d’arrêt minimal.

## <a name="hands-on-learning-modules"></a>Modules d’apprentissage pratique

Les modules Microsoft Learn suivants vous aident à vous familiariser gratuitement avec Azure SQL Database.

- [Approvisionner une base de données Azure SQL pour stocker des données d’application](https://docs.microsoft.com/learn/modules/provision-azure-sql-db/)
- [Développer et configurer une application ASP.NET qui interroge une base de données Azure SQL Database](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)
- [Sécuriser votre base de données Azure SQL Database](https://docs.microsoft.com/learn/modules/secure-your-azure-sql-database/).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez une [liste générale des fonctionnalités prises en charge dans Azure SQL Database](sql-database-features.md).
- Découvrez comment [renforcer la sécurité de votre base de données](sql-database-security-tutorial.md).
- Consultez l’article sur l’[utilisation d’une base de données unique dans Azure SQL Database](sql-database-howto-single-database.md) pour accéder à des guides pratiques plus avancés.
- Découvrez plus d’exemples de scripts écrits en [PowerShell](sql-database-powershell-samples.md) et [Azure CLI](sql-database-cli-samples.md).
- Découvrez plus d’informations sur l’[API de gestion](sql-database-single-databases-manage.md) que vous pouvez utiliser pour configurer vos bases de données.
- [Identifiez la bonne référence SKU d’Azure SQL Database/Managed Instance pour votre base de données locale](/sql/dma/dma-sku-recommend-sql-db/).
