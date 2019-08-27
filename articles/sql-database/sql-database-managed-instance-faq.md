---
title: Instance gérée SQL Database | Microsoft Docs
description: Forum aux questions sur les instances gérées SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 2efeb37f8b98f4cf9a29ec8a6976146b81aab26b
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69641071"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>Forum aux questions sur les instances gérées SQL Database

Cet article contient plusieurs des questions les plus courantes sur les [instances gérées SQL Database](sql-database-managed-instance.md).

## <a name="where-can-i-find-a-list-of-features-supported-on-managed-instance"></a>Où puis-je trouver la liste des fonctionnalités prises en charge sur une instance gérée ?

Pour obtenir la liste des fonctionnalités prises en charge dans l’instance gérée, consultez [Azure SQL Database par rapport à SQL Server](sql-database-features.md).

Pour connaître les différences de syntaxe et de comportement entre l’instance gérée Azure SQL Database et SQL Server local, consultez [Différences de T-SQL de SQL Server](sql-database-managed-instance-transact-sql-information.md).


## <a name="where-can-i-find-technical-characteristics-and-resource-limits-for-managed-instance"></a>Où puis-je trouver de l’information sur les caractéristiques techniques et les limites de ressources pour l’instance gérée ?

Pour les caractéristiques des générations de matériel disponible, consultez [Différences techniques dans les générations de matériel](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
Pour les niveaux de service disponibles et leurs caractéristiques, consultez [Différences techniques entre les niveaux de service](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="where-can-i-find-known-issues-and-bugs"></a>Où puis-je trouver de l’information sur les problèmes connus et les bogues ?

Pour les bogues et les problèmes connus, consultez [problèmes connus](sql-database-managed-instance-transact-sql-information.md#Issues).

## <a name="can-a-managed-instance-have-the-same-name-as-on-premises-sql-server"></a>Est-ce qu’une instance gérée peut avoir le même nom que SQL Server local ?

L’instance gérée doit avoir un nom qui se termine par *database.windows.net*. Pour utiliser une autre zone DNS au lieu de la valeur par défaut, par exemple, **mi-another-name**.contoso.com : 
- Utilisez CliConfig pour définir un alias. L’outil étant simplement un wrapper de paramètres du registre, cela peut aussi se faire à l'aide d'une stratégie de groupe ou d'un script.
- Utilisez *CNAME* avec l’option *TrustServerCertificate = true*.


## <a name="how-can-i-move-database-from-managed-instance-back-to-sql-server-or-azure-sql-database"></a>Comment puis-je déplacer la base de données d’une instance gérée vers SQL Server ou Azure SQL Database ?

Vous pouvez [exporter la base de données vers BACPAC](sql-database-export.md), puis [importer le fichier BACPAC]( sql-database-import.md). Cette approche est recommandée si votre base de données est inférieure à 100 Go.

La réplication transactionnelle peut être utilisée si toutes les tables dans la base de données ont des clés primaires.

Les sauvegardes natives `COPY_ONLY` prises à partir d’une instance gérée ne peuvent pas être restaurées sur SQL Server car l’instance gérée a une version de base de données supérieure à celle de SQL Server.

## <a name="how-can-i-migrate-my-instance-database-to-a-single-azure-sql-database"></a>Comment puis-je migrer ma base de données d’instance à une base de données Azure SQL Database unique ?

Une option consiste à [exporter la base de données vers un fichier BACPAC](sql-database-export.md), puis à [importer le fichier BACPAC]( sql-database-import.md). 

Cette approche est recommandée si votre base de données est inférieure à 100 Go. La réplication transactionnelle peut être utilisée si toutes les tables dans la base de données ont des clés primaires.

## <a name="how-do-i-choose-between-gen-4-and-gen-5-hardware-generation-for-managed-instance"></a>Comment choisir entre la génération de matériel Gen 4 et Gen 5 pour une instance gérée ?

Cela dépend de votre charge de travail car certaines générations de matériel sont plus adaptées à certains types de charges de travail que d’autres. Bien que le sujet de la performance soit plutôt complexe à simplifier, les différences suivantes entre les générations de matériel affectent la performance de la charge de travail :
- Gen 4 fournit une meilleure prise en charge de calcul puisqu’elle est basée sur des processeurs physiques, et Gen 5 basée sur des processeurs vCore. Cela peut s'avérer plus avantageux pour calculer des charges de travail intensives.
- La Gen 5 prend en charge la mise en réseau accélérée, ce qui se traduit par une meilleure bande passante d’E/S vers le stockage distant. Cela peut s'avérer avantageux pour les charges de travail intensives d’E/S sur les niveaux de service usage général. Gen 5 utilise des disques locaux SSD plus rapides par rapport à Gen 4. Cela peut s'avérer avantageux pour les charges de travail intensives d’E/S sur les niveaux de service critiques de l’entreprise.

Il est vivement conseillé de tester le niveau de performance des charges de travail réelles destinées à la production avant la mise en production afin de déterminer quelle génération de matériel fonctionnera le mieux dans votre cas.

## <a name="can-i-switch-my-managed-instance-hardware-generation-between-gen-4-and-gen-5-online"></a>Puis-je basculer ma génération de matériel d’instance gérée entre Gen 4 et Gen 5 en ligne ? 

La commutation en ligne automatisée entre les générations de matériel est possible si les deux générations de matériel sont disponibles dans la même région où votre instance gérée est approvisionnée. Dans ce cas, vous avez une option dans la section de niveau tarifaire du portail Azure pour basculer entre les générations de matériel.

Il s’agit d’une opération de longue durée car la nouvelle instance gérée sera approvisionnée en arrière-plan et les bases de données seront automatiquement transférées entre l’ancienne et la nouvelle instance, avec un rapide basculement au terme du processus. 

Si les deux générations de matériel ne sont pas prises en charge dans la même région, la modification de la génération de matériel est possible mais doit être effectuée manuellement. Pour cela, vous devez approvisionner une nouvelle instance dans la région où la génération de matériel souhaitée est disponible, et sauvegarder et restaurer manuellement les données entre l’ancienne et la nouvelle instance.


## <a name="how-do-i-tune-performance-of-my-managed-instance"></a>Comment régler les performances de mon instance gérée ? 

L’instance gérée à usage général utilise le stockage à distance en raison de la taille des données et des fichiers journaux importants pour le niveau de performance. Pour plus d’informations, consultez [Impact of log file size on General Purpose Managed Instance performance](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Pour les charges de travail intensives E/S, envisagez d’utiliser du matériel Gen 5, plutôt que Gen 4 pour les calculer. Pour plus d’informations, consultez [Comment choisir entre Gen 4 et Gen 5](#how-do-i-choose-between-gen-4-and-gen-5-hardware-generation-for-managed-instance).

Si votre charge de travail est constituée d’un grand nombre de petites transactions, envisagez de passer le type de connexion du mode proxy au mode redirection.

## <a name="what-is-the-maximum-storage-size-for-managed-instance"></a>Qu’est la taille de stockage maximale pour l’instance gérée ? 

La taille de stockage pour l’instance gérée dépend du niveau de service sélectionné (usage général ou critique pour l’entreprise). Pour les restrictions de stockage de ces niveaux de service, consultez [Caractéristique de niveau de service](sql-database-service-tiers-general-purpose-business-critical.md).

## <a name="is-the-backup-storage-deducted-from-my-managed-instance-storage"></a>Le stockage de sauvegarde est-il déduit à partir de mon espace de stockage d’instance gérée ? 

Non, le stockage de sauvegarde n’est pas déduit de votre espace de stockage d’instance gérée. Le stockage de sauvegarde est indépendant de l’espace de stockage d’instance et n’est pas limité en taille. Le stockage de sauvegarde est limité par la durée de rétention de la sauvegarde de vos bases de données d’instance configurables, de 7 à 35 jours. Pour plus d’informations. consultez [Sauvegardes automatisées](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
  
## <a name="how-can-i-set-inbound-nsg-rules-on-management-ports"></a>Comment puis-je définir des règles de groupe de sécurité réseau entrants sur les ports de gestion ?

La fonctionnalité pare-feu intégré configure le pare-feu de Windows sur toutes les machines virtuels du cluster pour autoriser les connexions entrantes à partir de plages d’adresses IP associés uniquement aux machines de déploiement de la gestion de Microsoft et les stations de travail administrateur sécurisées et empêcher ainsi que intrusions via la couche réseau.

Voici pourquoi les ports sont utilisés :

Les ports 9000 et 9003 sont utilisés par l’infrastructure de Service Fabric. Le rôle de principal de Service Fabric consiste à garder le cluster virtuel et conserver l’état d’objectif en termes de nombre de réplicas de composant.

Les ports 1438, 1440 et 1452 sont utilisés par l’agent de nœud. L’agent de nœud est une application qui s’exécute à l’intérieur du cluster et est utilisée par le plan de contrôle pour exécuter des commandes de gestion.

En plus du pare-feu intégré sur la couche réseau, la communication est également protégée par des certificats.
  
Pour plus d’informations et pour savoir comment vérifier le pare-feu intégré, voir [Pare-feu intégré de l’instance gérée Azure SQL Database](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).


## <a name="how-can-i-mitigate-networking-risks"></a>Comment puis-je atténuer les risques de mise en réseau ? 

Afin d’atténuer les risques liés à la mise en réseau, il est recommandé aux clients d’appliquer un ensemble de paramètres et de contrôles de sécurité :

- Activez [Transparent Data Encryption (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) sur toutes les bases de données.
- Désactivez Common Language Runtime (CLR). Cela est recommandé localement également.
- Utilisez uniquement Authentification Azure Active Directory (AAD).
- Accédez à l'instance avec un compte à faibles privilèges DBA.
- Configurez l’accès au serveur de rebond JiT pour le compte sysadmin.
- Activez l’[audit SQL](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) et intégrez-le à des mécanismes d’alerte.
- Activez la [détection des menaces](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) à partir de la suite [Advanced Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="where-can-i-find-use-cases-and-resulting-cost-savings-with-managed-instance"></a>Où puis-je trouver des cas d’utilisation et les économies de coûts qui en résultent avec l’instance gérée ?

Études de cas d’instance gérée :

- [Komatsu](http://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [powerdetails](http://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](http://customers.microsoft.com/story/allscripts-partner-professional-services-azure)   
Pour mieux comprendre les avantages, les coûts et les risques associés au déploiement d’une instance gérée d’Azure SQL Database, une étude de Forrester est également disponible : [Répercussions économiques totales MI](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="can-i-do-dns-refresh"></a>Puis-je actualiser DNS ? 
  
Actuellement, nous ne fournissons pas une fonctionnalité pour actualiser la configuration du serveur DNS pour l’instance gérée.

La configuration DNS est actualisée par la suite :

- Lorsque le bail DHCP expire.
- À la mise à niveau de la plateforme.

Comme solution de contournement, rétrogradez l’instance gérée à 4 vCore et mettez-la à niveau à nouveau par la suite. Ceci a pour effet secondaire d’actualiser la configuration DNS.


## <a name="can-a-managed-instance-have-a-static-ip-address"></a>Une instance gérée peut-elle avoir une adresse IP statique ?

Dans des situations rares mais nécessaires, nous pourrions avoir besoin de faire une migration en ligne d’une instance gérée vers un nouveau cluster virtuel. Si nécessaire, cette migration est due à des changements dans notre pile technologique visant à améliorer la sécurité et la fiabilité du service. La migration vers un nouveau cluster virtuel entraîne la modification de l’adresse IP associée au nom d’hôte de l’instance gérée. Le service d’instance gérée ne réclame pas le support des adresses IP statiques et se réserve le droit de les modifier sans préavis dans le cadre des cycles de maintenance réguliers.

Pour cette raison, nous déconseillons fortement de se fier à l’immuabilité de l’adresse IP car cela pourrait causer des temps d’arrêt inutiles.

## <a name="can-i-move-a-managed-instance-or-its-vnet-to-another-resource-group"></a>Puis-je déplacer une instance gérée ou son réseau virtuel vers un autre groupe de ressources?

Non, il s’agit de la limite actuelle de la plateforme. Après la création d’une instance gérée, le déplacement de l’instance gérée ou du réseau virtuel vers un autre groupe de ressources ou vers un autre abonnement n’est pas pris en charge.

## <a name="can-i-change-the-time-zone-for-an-existing-managed-instance"></a>Puis-je changer le fuseau horaire d’une instance gérée existante ?

La configuration de fuseau horaire peut être définie lorsqu’une instance gérée est configurée pour la première fois. Le fuseau horaire de l’instance gérée existante ne peut pas être modifié. Pour plus d’informations, consultez [limitations de fuseau horaire](sql-database-managed-instance-timezone.md#limitations).

Les solutions de contournement comprennent la création d’une nouvelle instance gérée avec le fuseau horaire approprié, puis soit une sauvegarde et une restauration manuelles, soit, ce que nous recommandons, une [restauration à un moment donné d’une autre instance](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="how-do-i-resolve-performance-issues-with-my-managed-instance"></a>Comment résoudre les problèmes de performances avec mon instance gérée ?

Pour une comparaison des performances entre une instance gérée et SQL Server, commencez par consulter l'article [Meilleures pratiques pour comparer les performances entre une instance gérée Azure SQL et SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).

Les chargements de données sont souvent plus lents sur les instances gérées que dans SQL Server en raison du modèle de récupération complète obligatoire et des [limites](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) du débit d’écriture du journal des transactions. Parfois, cela peut être contourné en chargeant des données transitoires dans tempdb au lieu d’une base de données utilisateur, ou en utilisant un magasin de colonnes en cluster ou des tables optimisées en mémoire.


## <a name="can-i-restore-my-encrypted-database-to-managed-instance"></a>Puis-je restaurer ma base de données chiffrée de l’instance gérée ?

Oui, vous n’avez pas besoin de décrypter votre base de données pour pouvoir la restaurer en instance gérée. Vous devez fournir à l’instance gérée un certificat/une clé utilisée comme protecteur de clé de cryptage dans le système source pour pouvoir lire les données du fichier de sauvegarde crypté. Il existe deux façons d'effectuer cette opération :

- *Téléchargez le protecteur de certificat dans l’instance gérée*. Cela est uniquement possible à l’aide de PowerShell. L’[exemple de script](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) décrit l’ensemble du processus.
- *Téléchargez le protecteur de clé asymétrique dans Azure Key Vault (AKV) et pointez l'instance gérée sur celui-ci.* . Cette approche ressemble au cas d’utilisation TDE de Bring-your-own-key (BYOK) qui utilise également l’intégration AKV pour stocker la clé de cryptage. Si vous ne souhaitez pas utiliser la clé en tant que protecteur de clé de chiffrement, mais simplement la mettre à disposition de l'instance gérée pour restaurer les bases de données chiffrées, suivez les instructions pour [configurer BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption-in-the-azure-portal), et ne cochez pas la case *Définir la clé sélectionnée comme protecteur TDE par défaut*.

Une fois que vous mettez le protecteur de cryptage à la disposition de l’instance gérée, vous pouvez procéder à la procédure standard de restauration de la base de données.

## <a name="how-can-i-migrate-from-azure-sql-database-single-or-elastic-pool-to-managed-instance"></a>Comment puis-je migrer d’un pool unique ou élastique d’Azure SQL Database vers une instance gérée ? 

L’instance gérée offre les mêmes niveaux de performance par calcul et taille de stockage que les autres options de déploiement d’Azure SQL Database. Si vous souhaitez consolider les données sur une seule instance ou si vous avez simplement besoin d’une fonctionnalité prise en charge exclusivement dans une instance gérée, vous pouvez migrer vos données en utilisant la fonctionnalité d’export/import (BACPAC).
