---
title: Modèles Microsoft Azure Resource Manager
description: Utilisez des modèles Azure Resource Manager pour créer et configurer Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: b254621cc414fb9b2b76263957adc80da6e9c22d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79214001"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database"></a>Modèles Azure Resource Manager pour Azure SQL Database

Les modèles Azure Resource Manager vous permettent de définir votre infrastructure sous forme de code et de déployer vos solutions sur le cloud Azure.

## <a name="single-database--elastic-pool"></a>[Base de données unique et pool élastique](#tab/single-database)

Le tableau suivant inclut des liens vers des modèles Azure Resource Manager pour Azure SQL Database.

| |  |
|---|---|
| [Base de données unique](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Ce modèle Azure Resource Manager crée une base de données Azure SQL unique avec un serveur logique et configure des règles de pare-feu. |
| [Serveur logique](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Ce modèle Azure Resource Manager crée un serveur logique pour Azure SQL Database. |
| [Pool élastique](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Ce modèle vous permet de déployer un nouveau pool élastique avec son nouveau serveur SQL Server associé et ses nouvelles bases de données SQL à lui assigner. |
| [Groupes de basculement](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Ce modèle crée deux serveurs logiques SQL Azure, une base de données SQL et un groupe de basculement.|
| [Détection de menaces](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Ce modèle vous permet de déployer un serveur logique Azure SQL et un ensemble de bases de données Azure SQL avec la détection des menaces activée, ainsi qu’une adresse e-mail pour les alertes de chaque base de données. La détection de menaces fait partie de l’offre SQL Advanced Threat Protection (ATP) qui fournit une couche de sécurité en réponse aux menaces potentielles qui pèsent sur les bases de données et les serveurs SQL.|
| [Audit dans Stockage Blob Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Ce modèle vous permet de déployer un serveur logique SQL Azure avec audit activé pour écrire des journaux d’audit dans un stockage d’objets blob. L’audit pour Azure SQL Database suit les événements de base de données et les écrit dans un journal d’audit que vous pouvez placer dans votre compte de stockage Azure, votre espace de travail OMS ou Event Hubs.|
| [Audit pour Azure Event Hub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Ce modèle vous permet de déployer un serveur SQL Azure avec audit activé pour écrire des journaux d’audit dans un hub d’événements existant. Pour envoyer des événements d’audit à Event Hub, définissez des paramètres d’audit avec `Enabled` `State` et affectez à `IsAzureMonitorTargetEnabled` la valeur `true`. De plus, configurez des paramètres de diagnostic avec la catégorie de journal `SQLSecurityAuditEvents` sur la base de données `master` (pour l’audit au niveau du serveur). L’audit pour Azure SQL Database et SQL Data Warehouse suit les événements de base de données et les écrit dans un journal d’audit que vous pouvez placer dans votre compte de stockage Azure, votre espace de travail OMS ou Event Hubs.|
| [Application web Azure avec SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Cet exemple crée une application web Azure avec SQL Database gratuite au niveau de service « De base ».|
| [Application web Azure et Cache Redis avec SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Ce modèle crée une application web, un cache Redis et une base de données SQL dans le même groupe de ressources, puis il crée deux chaînes de connexion dans l’application web pour la base de données SQL et le cache Redis.|
| [Importer des données à partir d’un stockage d’objets blob avec ADF V2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Ce modèle Azure Resource Manager crée un service Azure Data Factory V2 qui copie des données depuis Stockage Blob Azure vers SQL Database.|
| [Cluster HDInsight avec SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Ce modèle vous permet de créer un cluster HDInsight, un serveur SQL Database, une base de données SQL et deux tables. Il est utilisé dans l’article [Utiliser Sqoop avec Hadoop dans HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop). |
| [Application logique Azure qui exécute une procédure stockée SQL selon une planification](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Ce modèle vous permet de créer une application logique qui exécute une procédure stockée SQL selon une planification. Tous les arguments relatifs à la procédure peuvent être placés dans la section du corps du modèle.|

## <a name="managed-instance"></a>[Managed Instance](#tab/managed-instance)

Le tableau suivant inclut des liens vers des modèles Azure Resource Manager pour Azure SQL Database - Managed Instance.

| |  |
|---|---|
| [Instance managée dans un nouveau réseau virtuel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Ce modèle Azure Resource Manager crée un réseau virtuel Azure configuré et une instance managée dans ce réseau virtuel. |
| [Environnement réseau pour instance managée](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Ce déploiement crée un réseau virtuel Azure configuré avec deux sous-réseaux : un réseau dédié à vos instances managées et un autre dans lequel vous pouvez placer d’autres ressources (par exemple des machines virtuelles, des environnements App Service, etc.). Ce modèle crée un environnement réseau correctement configuré, où vous pouvez déployer des instances managées. |
| [Instance managée avec connexion P2S](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Ce déploiement crée un réseau virtuel Azure avec deux sous-réseaux `ManagedInstance` et `GatewaySubnet`. L’option Managed Instance est déployée dans le sous-réseau ManagedInstance. Une passerelle de réseau virtuel est créée dans le sous-réseau `GatewaySubnet` et configurée pour la connexion VPN point à site. |
| [Instance managée avec machine virtuelle](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Ce déploiement crée un réseau virtuel Azure avec deux sous-réseaux `ManagedInstance` et `Management`. L’option Managed Instance est déployée dans le sous-réseau `ManagedInstance`. La machine virtuelle dotée de la dernière version de SQL Server Management Studio (SSMS) est déployée dans le sous-réseau `Management`. |

---
