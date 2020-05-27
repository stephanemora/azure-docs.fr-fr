---
title: Questions fréquentes (FAQ) sur les instances managées
description: Forum aux questions sur les instances gérées SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 3ffa4bc905a08c1757865db7bab828193ff3c7ea
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770140"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>Forum aux questions sur les instances gérées SQL Database

Cet article contient plusieurs des questions les plus courantes sur les [instances gérées SQL Database](sql-database-managed-instance.md).

## <a name="supported-features"></a>Fonctionnalités prises en charge

**Où puis-je trouver une liste de fonctionnalités prises en charge sur une instance managée ?**

Pour obtenir la liste des fonctionnalités prises en charge dans l’instance gérée, consultez [Azure SQL Database par rapport à SQL Server](sql-database-features.md).

Pour connaître les différences de syntaxe et de comportement entre l’instance gérée Azure SQL Database et SQL Server local, consultez [Différences de T-SQL de SQL Server](sql-database-managed-instance-transact-sql-information.md).


## <a name="tech-spec--resource-limits"></a>Spécifications techniques et limites de ressources
 
**Où puis-je trouver des informations sur les caractéristiques techniques et les limites de ressources pour l’instance managée ?**

Pour les caractéristiques des générations de matériel disponible, consultez [Différences techniques dans les générations de matériel](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
Pour les niveaux de service disponibles et leurs caractéristiques, consultez [Différences techniques entre les niveaux de service](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="known-issues--bugs"></a>Problèmes connus et bogues

**Où puis-je trouver des informations sur les problèmes connus et les bogues ?**

Pour les bogues et les problèmes connus, consultez [problèmes connus](sql-database-release-notes.md#known-issues).

## <a name="new-features"></a>Nouvelles fonctionnalités

**Où puis-je trouver les fonctionnalités les plus récentes et les fonctionnalités disponibles en préversion publique ?**

Pour les fonctionnalités nouvelles et d’évaluation, consultez les [notes de publication](sql-database-release-notes.md?tabs=managed-instance).

## <a name="deployment-times"></a>Temps de déploiement 

**Combien de temps faut-il pour créer ou mettre à jour une instance ou pour restaurer une base de données ?**

Le temps prévu pour créer une instance managée ou changer le niveau de service (vCores, stockage) dépend de plusieurs facteurs. Jetez un coup d’œil aux [opérations de gestion](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations). 

## <a name="naming-convention"></a>Conventions d’affectation de noms

**Est-ce qu’une instance managée peut avoir le même nom qu’un serveur SQL Server local ?**

La modification du nom de l’instance managée n’est pas prise en charge.

La zone DNS par défaut de l’instance managée *.database.windows.net* peut être modifiée. 

Pour utiliser une autre zone DNS au lieu de la valeur par défaut, par exemple, *.contoso.com* : 
- Utilisez CliConfig pour définir un alias. L’outil étant simplement un wrapper de paramètres du registre, cela peut aussi se faire à l'aide d'une stratégie de groupe ou d'un script.
- Utilisez *CNAME* avec l’option *TrustServerCertificate = true*.

## <a name="move-db-from-mi"></a>Déplacer la base de données de l’instance managée 

**Comment puis-je déplacer la base de données d’une instance managée vers SQL Server ou Azure SQL Database ?**

Vous pouvez [exporter la base de données vers BACPAC](sql-database-export.md), puis [importer le fichier BACPAC]( sql-database-import.md). Cette approche est recommandée si votre base de données est inférieure à 100 Go.

La réplication transactionnelle peut être utilisée si toutes les tables dans la base de données ont des clés primaires.

Les sauvegardes natives `COPY_ONLY` prises à partir d’une instance gérée ne peuvent pas être restaurées sur SQL Server car l’instance gérée a une version de base de données supérieure à celle de SQL Server.

## <a name="migrate-instance-db"></a>Migrer la base de données d’instance

**Comment puis-je migrer la base de données de mon instance vers une base de données Azure SQL Database unique ?**

Une option consiste à [exporter la base de données vers un fichier BACPAC](sql-database-export.md), puis à [importer le fichier BACPAC](sql-database-import.md). 

Cette approche est recommandée si votre base de données est inférieure à 100 Go. La réplication transactionnelle peut être utilisée si toutes les tables dans la base de données ont des clés primaires.

## <a name="switch-hardware-generation"></a>Basculer la génération de matériel 

**Puis-je basculer la génération de matériel de mon instance managée entre Gen 4 et Gen 5 en ligne ?**

La commutation en ligne automatisée entre les générations de matériel est possible si les deux générations de matériel sont disponibles dans la même région où votre instance gérée est approvisionnée. Dans ce cas, vous pouvez consulter la [page Présentation du modèle vCore](sql-database-service-tiers-vcore.md) qui explique comment basculer entre les générations de matériel.

Il s’agit d’une opération de longue durée car la nouvelle instance gérée sera approvisionnée en arrière-plan et les bases de données seront automatiquement transférées entre l’ancienne et la nouvelle instance, avec un rapide basculement au terme du processus. 

**Que se passe-t-il si les deux générations de matériel ne sont pas prises en charge dans la même région ?**

Si les deux générations de matériel ne sont pas prises en charge dans la même région, la modification de la génération de matériel est possible mais doit être effectuée manuellement. Pour cela, vous devez approvisionner une nouvelle instance dans la région où la génération de matériel souhaitée est disponible, et sauvegarder et restaurer manuellement les données entre l’ancienne et la nouvelle instance.

**Que se passe-t-il s’il n’y a pas suffisamment d’adresses IP pour effectuer une opération de mise à jour ?**

Au cas où il n’y a pas suffisamment d’adresses IP dans le sous-réseau où votre instance gérée est provisionnée, vous devez créer un nouveau sous-réseau et une nouvelle instance gérée dans celui-ci. Nous suggérons également que le nouveau sous-réseau soit créé avec davantage d’adresses IP allouées afin que les opérations de mise à jour futures évitent une situation similaire (pour la taille de sous-réseau appropriée, vérifiez [comment déterminer la taille du sous-réseau d’un réseau virtuel](sql-database-managed-instance-determine-size-vnet-subnet.md)). Une fois la nouvelle instance provisionnée, vous pouvez sauvegarder et restaurer manuellement les données entre l’ancienne instance et la nouvelle, ou effectuer une [restauration dans le temps](sql-database-managed-instance-point-in-time-restore.md?tabs=azure-powershell) entre les instances. 


## <a name="tune-performance"></a>Régler les performances

**Comment régler les performances de mon instance gérée ?**

L’instance gérée à usage général utilise le stockage à distance en raison de la taille des données et des fichiers journaux importants pour le niveau de performance. Pour plus d’informations, consultez [Impact of log file size on General Purpose Managed Instance performance](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Si votre charge de travail est constituée d’un grand nombre de petites transactions, envisagez de passer le type de connexion du mode proxy au mode redirection.

## <a name="maximum-storage-size"></a>Taille de stockage maximale

**Quelle est la taille de stockage maximale pour l’instance managée ?**

La taille de stockage pour l’instance gérée dépend du niveau de service sélectionné (usage général ou critique pour l’entreprise). Pour les restrictions de stockage de ces niveaux de service, consultez [Caractéristique de niveau de service](sql-database-service-tiers-general-purpose-business-critical.md).

## <a name="back-up-storage-cost"></a>Coût de stockage de sauvegarde 

**Le stockage de sauvegarde est-il déduit de l’espace de stockage de mon instance managée ?**

Non, le stockage de sauvegarde n’est pas déduit de votre espace de stockage d’instance gérée. Le stockage de sauvegarde est indépendant de l’espace de stockage d’instance et n’est pas limité en taille. Le stockage de sauvegarde est limité par la durée de rétention de la sauvegarde de vos bases de données d’instance configurables, de 7 à 35 jours. Pour plus d’informations. consultez [Sauvegardes automatisées](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).

## <a name="track-billing"></a>Suivre la facturation

**Existe-t-il un moyen de suivre le coût de facturation pour mon instance managée ?**

Vous pouvez le faire à l’aide de la [solution Azure Cost Management](/azure/cost-management/). Accédez à **Abonnements** dans le [portail Azure](https://portal.azure.com) et sélectionnez **Analyse des coûts**. 

Utilisez l’option **Coûts cumulés**, puis filtrez en fonction du **type de ressource** comme `microsoft.sql/managedinstances`. 
  
## <a name="inbound-nsg-rules"></a>Règles de groupe de sécurité réseau entrantes

**Comment puis-je définir des règles de groupe de sécurité réseau entrantes sur les ports de gestion ?**

Le plan de contrôle de l’instance managée gère les règles NSG qui protègent les ports de gestion.

Voici pourquoi les ports de gestion sont utilisés :

Les ports 9000 et 9003 sont utilisés par l’infrastructure de Service Fabric. Le rôle de principal de Service Fabric consiste à garder le cluster virtuel et conserver l’état d’objectif en termes de nombre de réplicas de composant.

Les ports 1438, 1440 et 1452 sont utilisés par l’agent de nœud. L’agent de nœud est une application qui s’exécute à l’intérieur du cluster et est utilisée par le plan de contrôle pour exécuter des commandes de gestion.

En plus des règles NSG, le pare-feu intégré protège l’instance sur la couche réseau. Sur la couche application, la communication est protégée à l’aide des certificats.
  
Pour plus d’informations et pour savoir comment vérifier le pare-feu intégré, voir [Pare-feu intégré de l’instance gérée Azure SQL Database](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).


## <a name="mitigate-data-exfiltration-risks"></a>Atténuer les risques liés à l’exfiltration de données  

**Comment atténuer les risques liés à l’exfiltration de données ?**

Afin d’atténuer les risques liés à l’exfiltration de données, il est recommandé aux clients d’appliquer un ensemble de paramètres et de contrôles de sécurité :

- Activez [Transparent Data Encryption (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) sur toutes les bases de données.
- Désactivez Common Language Runtime (CLR). Cela est recommandé localement également.
- Utilisez uniquement Authentification Azure Active Directory (AAD).
- Accédez à l'instance avec un compte à faibles privilèges DBA.
- Configurez l’accès au serveur de rebond JiT pour le compte sysadmin.
- Activez l’[audit SQL](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) et intégrez-le à des mécanismes d’alerte.
- Activez la [détection des menaces](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) à partir de la suite [Advanced Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="cost-saving-use-cases"></a>Cas d’utilisation avec économies de coûts

**Où puis-je trouver des cas d’utilisation et les économies de coûts qui en résultent avec l’instance managée ?**

Études de cas d’instance gérée :

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)   
Pour mieux comprendre les avantages, les coûts et les risques associés au déploiement d’une instance gérée d’Azure SQL Database, une étude de Forrester est également disponible : [Répercussions économiques totales MI](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns-refresh"></a>Actualiser DNS 

**Puis-je actualiser DNS ?**

Actuellement, nous ne fournissons pas une fonctionnalité pour actualiser la configuration du serveur DNS pour l’instance gérée.

La configuration DNS est actualisée par la suite :

- Lorsque le bail DHCP expire.
- À la mise à niveau de la plateforme.

Comme solution de contournement, rétrogradez l’instance gérée à 4 vCore et mettez-la à niveau à nouveau par la suite. Ceci a pour effet secondaire d’actualiser la configuration DNS.


## <a name="ip-address"></a>Adresse IP

**Puis-je me connecter à une instance managée à l’aide d’une adresse IP ?**

La connexion à une instance managée à l’aide d’une adresse IP n’est pas prise en charge. Le nom d’hôte de l’instance managée est mappé à l’équilibreur de charge devant le cluster virtuel de l’instance managée. Comme un cluster virtuel peut héberger plusieurs instances managées, la connexion ne peut pas être acheminée vers l’instance managée appropriée si son nom n’est pas spécifié.

Pour plus d’informations sur l’architecture de cluster virtuel d’instance managée, consultez [Architecture de la connectivité du cluster virtuel](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture).

**Une instance managée peut-elle avoir une adresse IP statique ?**

Dans des situations rares mais nécessaires, nous pourrions avoir besoin de faire une migration en ligne d’une instance gérée vers un nouveau cluster virtuel. Si nécessaire, cette migration est due à des changements dans notre pile technologique visant à améliorer la sécurité et la fiabilité du service. La migration vers un nouveau cluster virtuel entraîne la modification de l’adresse IP associée au nom d’hôte de l’instance gérée. Le service d’instance gérée ne réclame pas le support des adresses IP statiques et se réserve le droit de les modifier sans préavis dans le cadre des cycles de maintenance réguliers.

Pour cette raison, nous déconseillons fortement de se fier à l’immuabilité de l’adresse IP car cela pourrait causer des temps d’arrêt inutiles.

## <a name="change-time-zone"></a>Changer le fuseau horaire

**Puis-je changer le fuseau horaire d’une instance managée existante ?**

La configuration de fuseau horaire peut être définie lorsqu’une instance gérée est configurée pour la première fois. Le fuseau horaire de l’instance gérée existante ne peut pas être modifié. Pour plus d’informations, consultez [limitations de fuseau horaire](sql-database-managed-instance-timezone.md#limitations).

Les solutions de contournement comprennent la création d’une nouvelle instance gérée avec le fuseau horaire approprié, puis soit une sauvegarde et une restauration manuelles, soit, ce que nous recommandons, une [restauration à un moment donné d’une autre instance](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="resolve-performance-issues"></a>Résoudre les problèmes de performances

**Comment résoudre les problèmes de performances avec mon instance managée ?**

Pour une comparaison des performances entre une instance gérée et SQL Server, commencez par consulter l'article [Meilleures pratiques pour comparer les performances entre une instance gérée Azure SQL et SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).

Les chargements de données sont souvent plus lents sur les instances gérées que dans SQL Server en raison du modèle de récupération complète obligatoire et des [limites](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) du débit d’écriture du journal des transactions. Parfois, cela peut être contourné en chargeant des données transitoires dans tempdb au lieu d’une base de données utilisateur, ou en utilisant un magasin de colonnes en cluster ou des tables optimisées en mémoire.


## <a name="restore-encrypted-backup"></a>Restaurer la sauvegarde chiffrée

**Puis-je restaurer ma base de données chiffrée en instance managée ?**

Oui, vous n’avez pas besoin de décrypter votre base de données pour pouvoir la restaurer en instance gérée. Vous devez fournir à l’instance gérée un certificat/une clé utilisée comme protecteur de clé de cryptage dans le système source pour pouvoir lire les données du fichier de sauvegarde crypté. Il existe deux façons d'effectuer cette opération :

- *Téléchargez le protecteur de certificat dans l’instance gérée*. Cela est uniquement possible à l’aide de PowerShell. L’[exemple de script](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) décrit l’ensemble du processus.
- *Téléchargez le protecteur de clé asymétrique dans Azure Key Vault (AKV) et pointez l'instance gérée sur celui-ci.* . Cette approche ressemble au cas d’utilisation TDE de Bring-your-own-key (BYOK) qui utilise également l’intégration AKV pour stocker la clé de cryptage. Si vous ne souhaitez pas utiliser la clé en tant que protecteur de clé de chiffrement, mais simplement la mettre à disposition de l'instance gérée pour restaurer les bases de données chiffrées, suivez les instructions pour [configurer BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption), et ne cochez pas la case *Définir la clé sélectionnée comme protecteur TDE par défaut*.

Une fois que vous mettez le protecteur de cryptage à la disposition de l’instance gérée, vous pouvez procéder à la procédure standard de restauration de la base de données.

## <a name="migrate-from-single-db"></a>Migrer à partir d’une seule base de données 

**Comment puis-je migrer d’un pool unique ou élastique Azure SQL Database vers une instance managée ?**

L’instance gérée offre les mêmes niveaux de performance par calcul et taille de stockage que les autres options de déploiement d’Azure SQL Database. Si vous souhaitez consolider les données sur une seule instance ou si vous avez simplement besoin d’une fonctionnalité prise en charge exclusivement dans une instance gérée, vous pouvez migrer vos données en utilisant la fonctionnalité d’export/import (BACPAC).
