---
title: 'Démarrage rapide : Azure SQL Database Managed Instance | Microsoft Docs'
description: Découvrez comment démarrer rapidement avec Azure SQL Database - Managed Instance
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
ms.openlocfilehash: 77deed43c106a451d3de768989233c749e1280e1
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468170"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Bien démarrer avec Azure SQL Database Managed Instance

[Azure SQL Database Managed Instance](sql-database-managed-instance-index.yml) est une version PaaS entièrement managée de SQL Server, hébergée dans le cloud Azure et placée dans votre propre réseau virtuel avec une adresse IP privée. Dans cette section, vous allez découvrir comment configurer et créer rapidement une instance managée, puis comment migrer vos bases de données.

## <a name="quickstart-overview"></a>Vue d’ensemble du guide de démarrage rapide

Cette section contient une vue d’ensemble des articles disponibles qui peuvent vous aider à démarrer rapidement avec les instances managées. Le moyen le plus simple pour créer votre première instance managée est d’utiliser [le portail Azure](sql-database-managed-instance-get-started.md), où vous pouvez configurer les paramètres nécessaires (nom d’utilisateur/mot de passe, nombre de cœurs, stockage maximal) et créer automatiquement un environnement réseau Azure, sans vous occuper des détails du réseau ni des besoins d’infrastructure. Vérifiez simplement que vous avez un [type d’abonnement](sql-database-managed-instance-resource-limits.md#supported-subscription-types) autorisé à créer l’instance.

Si vous voulez utiliser votre propre réseau ou que vous voulez personnaliser le réseau, consultez [Configurer l’environnement réseau](#configure-network-environment) pour Managed Instance.

Quand vous créez votre instance managée, vous devez vous connecter à l’instance selon une des approches suivantes :

* Créez une [machine virtuelle Azure](sql-database-managed-instance-configure-vm.md) avec SQL Server Management Studio installé et d’autres applications qui peuvent être utilisées pour accéder à votre instance managée dans un sous-réseau au sein du même réseau virtuel où se trouve votre instance managée. La machine virtuelle ne peut pas être dans le même sous-réseau que vos instances managées.
* Configurez une [connexion point à site](sql-database-managed-instance-configure-p2s.md) sur votre ordinateur, qui vous permet de joindre votre ordinateur au réseau virtuel où se trouve l’instance managée et de l’utiliser comme tout autre serveur SQL Server dans votre réseau.

Comme alternative, vous pouvez utiliser une connexion ExpressRoute ou de site à site depuis votre réseau local, mais ces approches sont en dehors de l’objet de ces guides de démarrage rapide.

Quand vous avez créé une instance managée et que vous avez configuré l’accès, vous pouvez commencer à migrer vos bases de données placés sur un serveur SQL Server local ou sur des machines virtuelles Azure. Notez que la migration échoue si vous utilisez dans la base de données source à migrer certaines fonctionnalités non prises en charge. Pour éviter les échecs et vérifier la compatibilité, vous pouvez installer l’[Assistant Migration de données](https://www.microsoft.com/download/details.aspx?id=53595), qui analyse vos bases de données sur SQL Server et recherche les problèmes susceptibles de bloquer la migration vers Managed Instance, comme l’existence de FileStream ou de plusieurs fichiers journaux. Si vous résolvez ces problèmes, vos bases de données sont prêtes à passer à Managed Instance. L’[Assistant Expérimentation de base de données](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) est un autre outil pratique qui peut enregistrer votre charge de travail sur SQL Server et la réexécuter sur Managed Instance afin de déterminer si vous allez rencontrer des problèmes de performances en cas de migration vers Managed Instance.

Une fois que vous êtes sûr que vous pouvez migrer votre base de données vers Managed Instance, vous pouvez utiliser la fonctionnalité [RESTORE native](sql-database-managed-instance-get-started-restore.md), qui vous permet de créer une sauvegarde de votre base de données avec une commande Transact-SQL, de la charger dans un stockage d’objets blob Azure et d’utiliser une commande Transact-SQL RESTORE pour restaurer la base de données à partir du stockage d’objets blob.

Ces guides de démarrage rapide vous permettent de configurer, créer et placer rapidement des bases de données sur vos instances managées. Dans certains scénarios, vous devez personnaliser ou automatiser le déploiement de Managed Instance et de l’environnement réseau nécessaire. Ces scénarios sont décrits ci-dessous.

## <a name="customizing-network-environment"></a>Personnalisation de l’environnement réseau

Bien que le réseau virtuel/sous-réseau puisse être configuré automatiquement quand l’instance est créée avec [le portail Azure](sql-database-managed-instance-get-started.md), il peut être judicieux de le créer avant de commencer à créer des instances managées, car vous pouvez ainsi configurer les paramètres du réseau virtuel et du sous-réseau. Le moyen le plus simple pour créer et configurer l’environnement réseau consiste à utiliser le modèle de [déploiement Azure Resource Manager](sql-database-managed-instance-create-vnet-subnet.md), qui va créer et configurer votre réseau et votre sous-réseau où l’instance sera placée. Vous devez simplement cliquer sur le bouton de déploiement Azure Resource Manager et remplir le formulaire avec les paramètres. Vous pouvez aussi utiliser un [script PowerShell](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) pour automatiser la création du réseau.

Si vous avez déjà un réseau virtuel et un sous-réseau où vous voulez déployer votre instance managée, vous devez vérifier que votre réseau virtuel et votre sous-réseau répondent à la [configuration réseau requise](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Vous devez utiliser ce [script PowerShell pour vérifier que votre sous-réseau est correctement configuré](sql-database-managed-instance-configure-vnet-subnet.md). Ce script ne fait pas que vérifier votre réseau et signaler les problèmes : il vous indique aussi ce qui doit être changé et vous propose d’effectuer les modifications nécessaires dans votre réseau virtuel/sous-réseau. Exécutez ce script si vous ne voulez pas configurer votre réseau virtuel/sous-réseau manuellement ; il est également recommandé de l’exécuter après une reconfiguration majeure de votre infrastructure réseau. Si vous voulez créer et configurer votre propre réseau, lisez la [documentation de Managed Instance](sql-database-managed-instance-connectivity-architecture.md) et [ce guide](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="automating-creation-of-managed-instance"></a>Automatisation de la création d’une instance managée

 Si vous n’avez pas créé l’environnement réseau comme décrit dans l’étape précédente, le portail Azure peut le faire pour vous : le seul inconvénient est qu’il va le configurer avec certains paramètres par défaut que vous ne pouvez pas changer par la suite. Vous pouvez aussi utiliser [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/), [PowerShell avec le modèle Resource Manager](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md) ou [Azure CLI](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/11/14/create-azure-sql-managed-instance-using-azure-cli/).

## <a name="migrating-to-managed-instance-with-minimal-downtime"></a>Migration vers Managed Instance avec un temps d’arrêt minimal

Les articles de ces guides de démarrage rapide vous permettent de configurer rapidement Managed Instance et de déplacer vos bases de données. Cependant, avec la restauration native, vous devez attendre que les bases de données soient restaurées, ce qui peut entraîner un temps d’arrêt de votre application, en particulier si la base de données est plus grande. Si vous déplacez votre base de données de production, vous aurez probablement besoin d’une meilleure méthode de migration garantissant un temps d’arrêt minimal lors de la migration. [Data Migration Service](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) est un service de migration qui peut migrer votre base de données avec un temps d’arrêt minimal, en envoyant de façon incrémentielle les modifications effectuées dans votre base de données source vers une base de données que vous restaurez sur l’instance managée. De cette façon, vous pouvez basculer rapidement votre application de la base de données source vers la base de données cible avec un temps d’arrêt minimal.

## <a name="next-steps"></a>Étapes suivantes

* Vous trouverez une [liste générale des fonctionnalités prises en charge dans Managed Instance ici](sql-database-features.md) et [des informations détaillées et les problèmes connus ici](sql-database-managed-instance-transact-sql-information.md). 
* Découvrez plus d’informations sur les [caractéristiques techniques de Managed Instance](sql-database-managed-instance-resource-limits.md#instance-level-resource-limits). 
* Vous trouverez des tutoriels plus avancés dans la [section des guides pratiques](sql-database-howto-managed-instance.md). 
