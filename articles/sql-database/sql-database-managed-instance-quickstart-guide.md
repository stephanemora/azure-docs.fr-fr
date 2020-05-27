---
title: Démarrage rapide - Instance managée SQL
description: Découvrir comment démarrer rapidement avec une instance managée Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 07/11/2019
ms.openlocfilehash: b873588393ed765fa21b30dfb3a71486d055373b
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780467"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Bien démarrer avec une instance managée Azure SQL Database

L’option de déploiement [Managed Instance](sql-database-managed-instance-index.yml) crée une base de données presque 100 % compatible avec le moteur de base de données local SQL Server (Édition Entreprise) le plus récent. Elle fournit une implémentation de [réseau virtuel (VNet)](../virtual-network/virtual-networks-overview.md) native traitant les problèmes de sécurité courants, ainsi qu’un [modèle d’entreprise](https://azure.microsoft.com/pricing/details/sql-database/) favorable aux clients SQL Server locaux. Dans cet article, vous allez apprendre à créer et configurer rapidement une instance managée, puis à migrer vos bases de données.

## <a name="quickstart-overview"></a>Vue d’ensemble du guide de démarrage rapide

Les guides de démarrage rapide suivants vous permettent en un rien de temps de créer une instance managée, de configurer une machine virtuelle ou une connexion VPN point à site pour l’application cliente, et de restaurer une base de données sur votre nouvelle instance managée à l’aide d’un fichier `.bak`.

### <a name="configure-environment"></a>Configurer l’environnement

Dans un premier temps, vous devez créer votre première instance Managed Instance avec l’environnement réseau dans lequel elle va être placée, puis activer la connexion à partir de l’ordinateur ou de la machine virtuelle où vous exécutez les requêtes vers Managed Instance. Vous pouvez utiliser les guides suivants :

- [Créer une instance managée à l’aide du portail Azure](sql-database-managed-instance-get-started.md). Dans le portail Azure, vous configurez les paramètres nécessaires (nom d’utilisateur/mot de passe, nombre de cœurs et quantité de stockage maximal) pour créer automatiquement l’environnement réseau Azure, sans vous occuper des détails du réseau ni des besoins d’infrastructure. Vous devez simplement vérifier que vous avez un [type d’abonnement](sql-database-managed-instance-resource-limits.md#supported-subscription-types) actuellement autorisé à créer une instance managée. Si vous disposez de votre propre réseau à utiliser, ou que vous souhaitez personnaliser le réseau, consultez [Configurer un réseau virtuel existant pour Azure SQL Database Managed Instance](sql-database-managed-instance-configure-vnet-subnet.md) ou [Créer un réseau virtuel pour Azure SQL Database Managed Instance](sql-database-managed-instance-create-vnet-subnet.md).
- Une instance managée est créée dans son propre réseau virtuel sans aucun point de terminaison public. Pour l’accès aux applications clientes, vous pouvez **créer une machine virtuelle dans le même réseau virtuel (sous-réseau différent)** ou **créer une connexion VPN point à site au réseau virtuel à partir de votre ordinateur client**, à l’aide de l’un de ces guides de démarrage rapide :
  - Activez un [point de terminaison public](sql-database-managed-instance-public-endpoint-configure.md) sur votre instance gérée pour accéder à vos données directement à partir de votre environnement.
  - Créez une [machine virtuelle Azure dans le réseau virtuel de l’instance managée](sql-database-managed-instance-configure-vm.md) pour la connectivité aux applications clientes, notamment SQL Server Management Studio.
  - Configurez une [connexion VPN de point à site à votre instance managée](sql-database-managed-instance-configure-p2s.md) à partir de votre ordinateur client où se trouvent SQL Server Management Studio et d’autres applications de connectivité clientes. Il s’agit là de l’une des deux options disponibles pour établir la connectivité à votre instance managée et à son réseau virtuel.

  > [!NOTE]
  > - Vous pouvez également utiliser une connexion ExpressRoute ou de site à site à partir de votre réseau local, mais ces approches sortent du cadre de ces guides de démarrage rapide.
  > - Si vous remplacez la valeur 0 de la période de rétention (rétention illimitée) par une autre valeur, notez que la rétention s’appliquera uniquement aux journaux écrits après la modification de la valeur de rétention (les journaux écrits au cours de la période pendant laquelle la rétention était définie sur illimité sont conservés, même après activation de la rétention).

En guise d’alternative à la création manuelle d’instance gérée, vous pouvez utiliser [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md), [PowerShell avec le modèle Resource Manager](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md) ou [Azure CLI](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) pour générer des scripts et automatiser ce processus.

### <a name="migrate-your-databases"></a>Migrer vos bases de données

Après avoir créé une instance managée et configuré l’accès, vous pouvez commencer à migrer vos bases de données à partir d’un serveur SQL Server local ou de machines virtuelles Azure. La migration échoue si la base de données source à migrer contient des fonctionnalités non prises en charge. Pour éviter les échecs et vérifier la compatibilité, vous pouvez installer l’[Assistant Migration de données](https://www.microsoft.com/download/details.aspx?id=53595), qui analyse vos bases de données sur SQL Server et recherche les problèmes susceptibles de bloquer la migration vers une instance managée, comme l’existence de [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) ou de plusieurs fichiers journaux. Si vous résolvez ces problèmes, vous pouvez migrer vos bases de données vers une instance managée. L’[Assistant Expérimentation de base de données](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) est un autre outil pratique qui peut enregistrer votre charge de travail sur SQL Server et la réexécuter sur une instance managée afin de déterminer si vous allez rencontrer des problèmes de performances en cas de migration vers une instance managée.

Une fois que vous êtes sûr de pouvoir migrer votre base de données vers une instance managée, vous pouvez utiliser les fonctionnalités de restauration natives de SQL Server pour restaurer une base de données sur une instance managée à partir d’un fichier `.bak`. Vous pouvez utiliser cette méthode pour migrer des bases de données à partir du moteur de base de données SQL Server installé en local ou d’une machine virtuelle Azure. Pou accéder à un guide de démarrage rapide, consultez [Restaurer une sauvegarde sur une instance managée](sql-database-managed-instance-get-started-restore.md). Dans ce guide de démarrage rapide, vous effectuez une restauration à partir d’un fichier `.bak` situé dans le stockage Blob Azure à l’aide de la commande Transact-SQL `RESTORE`.

> [!TIP]
> Pour utiliser la commande Transact-SQL `BACKUP` afin de créer une sauvegarde de votre base de données dans le stockage Blob Azure, consultez [Sauvegarde SQL Server vers une URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Ces guides de démarrage rapide vous permettent de créer, de configurer et de restaurer une sauvegarde de base de données sur une instance managée. Dans certains scénarios, vous devez personnaliser ou automatiser le déploiement d’instances managées et de l’environnement réseau nécessaire. Ces scénarios sont décrits ci-dessous.

## <a name="customize-network-environment"></a>Personnaliser l’environnement réseau

Bien que le réseau virtuel/sous-réseau puisse être configuré automatiquement quand l’instance est créée avec [le portail Azure](sql-database-managed-instance-get-started.md), il peut être judicieux de le créer avant de commencer à créer des instances managées, car vous pouvez ainsi configurer les paramètres du réseau virtuel et du sous-réseau. Le moyen le plus simple pour créer et configurer l’environnement réseau consiste à utiliser le modèle de [déploiement Azure Resource Manager](sql-database-managed-instance-create-vnet-subnet.md), qui va créer et configurer votre réseau et votre sous-réseau où l’instance sera placée. Vous devez simplement cliquer sur le bouton de déploiement Azure Resource Manager et remplir le formulaire avec les paramètres.

Vous pouvez également utiliser ce [script PowerShell](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) pour automatiser la création du réseau.

Si vous avez déjà un réseau virtuel et un sous-réseau où vous souhaitez déployer votre instance managée, vous devez vérifier que votre réseau virtuel et votre sous-réseau répondent à la [configuration réseau demandée](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Utilisez ce [script PowerShell pour vérifier que votre sous-réseau est correctement configuré](sql-database-managed-instance-configure-vnet-subnet.md). Ce script vérifie votre réseau et signale les éventuels problèmes. Il indique aussi ce qui doit être changé et vous propose d’effectuer les modifications nécessaires dans votre réseau virtuel/sous-réseau. Exécutez ce script si vous ne souhaitez pas configurer votre réseau virtuel/sous-réseau manuellement. Vous pouvez également l’exécuter après toute reconfiguration majeure de votre infrastructure réseau. Si vous souhaitez créer et configurer votre propre réseau, lisez l’article consacré à l’[architecture de la connectivité](sql-database-managed-instance-connectivity-architecture.md) et ce [guide complet pour créer et configurer un environnement d’instance gérée](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="migrate-to-a-managed-instance"></a>Migrer vers une instance gérée

Les articles de ces guides de démarrage rapide vous permettent de configurer une instance managée et de déplacer vos bases de données à l’aide de la fonctionnalité `RESTORE` native. Il s’agit d’un bon point de départ si vous voulez effectuer une preuve de concept pour vérifier que votre solution peut fonctionner sur Managed Instance. 

Toutefois, pour migrer votre base de données de production ou même des bases de données de Dev/Test à utiliser pour effectuer des tests de performances, vous devez envisager d’utiliser des techniques supplémentaires, telles que :
- Test des performances : vous devez mesurer les performances de base sur votre instance SQL Server source et les comparer aux performances de l’instance gérée de destination où vous avez migré la base de données. Apprenez-en davantage sur les [meilleures pratiques en matière de comparaison des performances](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).
- Migration en ligne : avec la commande `RESTORE` native décrite dans cet article, vous devez attendre que les bases de données soient restaurées (et copiées vers Stockage Blob Azure si elles ne s’y trouvent pas déjà). Il en résulte des temps d’arrêt pour votre application, en particulier si les bases de données sont volumineuses. Pour déplacer votre base de données de production, utilisez [Database Migration Service (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) afin de migrer votre base de données avec un temps d’arrêt minimal. Pour y parvenir, DMS envoie (push) les changements effectués dans votre base de données source à la base de données de l’instance managée en cours de restauration. De cette façon, vous pouvez basculer rapidement votre application de la base de données source vers la base de données cible avec un temps d’arrêt minimal.

Apprenez-en davantage sur le [processus de migration recommandé](sql-database-managed-instance-migrate.md).

## <a name="next-steps"></a>Étapes suivantes

- Vous trouverez une [liste générale des fonctionnalités prises en charge dans Managed Instance ici](sql-database-features.md) et des [informations détaillées et problèmes connus ici](sql-database-managed-instance-transact-sql-information.md).
- Apprenez-en davantage sur les [caractéristiques techniques de Managed Instance](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).
- Consultez l’article sur l’[utilisation d’une instance managée dans Azure SQL Database](sql-database-howto-managed-instance.md) pour accéder à des guides pratiques plus avancés.
- [Identifiez la bonne référence SKU d’Azure SQL Database/Managed Instance pour votre base de données locale](/sql/dma/dma-sku-recommend-sql-db/).
