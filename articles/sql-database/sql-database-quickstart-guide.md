---
title: Démarrage rapide - Bases de données uniques dans Azure SQL Database | Microsoft Docs
description: Découvrez comment démarrer rapidement avec des bases de données uniques dans Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 72ffdb357b5469b71da7655104add1135bf114fb
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55464806"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Bien démarrer avec des bases de données uniques dans Azure SQL Database

[Une base de donnée unique](sql-database-single-index.yml) dans Azure SQL Database est une base de données DbaaS (Database as a Service) PaaS entièrement managée, qui est le moteur de stockage idéal pour les applications cloud modernes. Dans cette section, vous découvrez comment configurer et créer rapidement une base de données SQL Database.

## <a name="quickstart-overview"></a>Vue d’ensemble du guide de démarrage rapide

Cette section contient une vue d’ensemble des articles disponibles qui peuvent vous aider à démarrer rapidement avec des bases de données uniques. Le moyen le plus simple pour créer votre première base de données SQL Database consiste à utiliser [le portail Azure](sql-database-get-started-portal.md), où vous pouvez configurer les paramètres nécessaires.
Après la création, vous devez [sécuriser votre base de données en configurant des règles de pare-feu](sql-database-get-started-portal-firewall.md). 

Si vous avez une base de données existante sur SQL Server que vous voulez migrer vers Azure, vous devez installer l’[Assistant Migration de données](https://www.microsoft.com/download/details.aspx?id=53595), qui analyse vos bases de données sur SQL Server et recherche les problèmes susceptibles de bloquer la migration vers une base de données unique. Si vous ne trouvez pas de problème, vous pouvez exporter votre base de données en tant que fichier `.bacpac` et [l’importer en utilisant le portail Azure ou SqlPackage](sql-database-import.md).

Ces guides de démarrage rapide vous permettent de configurer, créer et importer rapidement vos bases de données dans le cloud Azure.

## <a name="automating-management-operations"></a>Automatisation des opérations de gestion

Le portail Azure vous permet de créer et de modifier facilement votre base de données unique. Vous pouvez aussi utiliser [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) ou [Azure CLI](scripts/sql-database-create-and-configure-database-cli.md) pour créer des bases de données.
Vous pouvez également mettre à jour votre base de données unique et mettre à l’échelle vos ressources avec [PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md) ou [Azure CLI](scripts/sql-database-monitor-and-scale-database-cli.md).

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>Migration vers une base de données unique avec un temps d’arrêt minimal

Les articles de ces guides de démarrage rapide vous permettent de créer et d’importer rapidement vos bases de données dans Azure avec `.bacpac`. Cependant, les fichiers `.bacpac` et `.dacpack` sont conçus pour déplacer rapidement une base de données entre des versions différentes de SQL Server et d’Azure SQL Database (base de données unique, pool élastique ou Managed Instance), ou pour implémenter l’intégration continue dans votre pipeline DevOps. Cette méthode n’est cependant pas conçue pour la migration de vos bases de données de production avec un temps d’arrêt minimal, car vous devez attendre d’exporter la base de données source en tant que fichier `.bacpac` et l’importer dans Azure SQL Database, ce qui provoque un temps d’arrêt de votre application, en particulier si la base de données est plus grande. Si vous déplacez votre base de données de production, vous avec probablement besoin d’une meilleure méthode de migration garantissant un temps d’arrêt minimal lors de la migration. [Data Migration Service](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) est un service qui peut migrer votre base de données avec un temps d’arrêt minimal. De cette façon, vous pouvez basculer rapidement votre application de la base de données source vers la base de données cible avec un temps d’arrêt minimal.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez une [liste générale des fonctionnalités prises en charge dans Azure SQL Database](sql-database-features.md). 
* Découvrez comment [renforcer la sécurité de votre base de données](sql-database-security-tutorial.md). 
* Découvrez des tutoriels plus avancés dans la [section des guides pratiques](sql-database-howto-single-database.md). 
* Découvrez plus d’exemples de scripts écrits en [PowerShell](sql-database-powershell-samples.md) et [Azure CLI](sql-database-cli-samples.md). 
* Découvrez plus d’informations sur l’[API de gestion](sql-database-single-databases-manage.md) que vous pouvez utiliser pour configurer vos bases de données. 
