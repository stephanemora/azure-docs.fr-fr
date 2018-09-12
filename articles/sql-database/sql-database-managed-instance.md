---
title: Présentation d’Azure SQL Database Managed Instance | Microsoft Docs
description: Cette rubrique décrit Azure SQL Database Managed Instance et explique son fonctionnement ainsi que ses différences par rapport à une base de données unique dans Azure SQL Database.
services: sql-database
author: bonova
ms.reviewer: carlrab
manager: craigg
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/30/2018
ms.author: bonova
ms.openlocfilehash: 2e1fd7c87931f804433708b6ac30a5960e6006ae
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43287661"
---
# <a name="what-is-a-managed-instance-preview"></a>Présentation de l’option Managed Instance (préversion)

Azure SQL Database Managed Instance (préversion) est une nouveau modèle de déploiement d’Azure SQL Database, presque 100 % compatible avec le moteur de base de données local SQL Server (Édition Entreprise) le plus récent, qui fournit une implémentation de [réseau virtuel (VNet)](../virtual-network/virtual-networks-overview.md) native qui traite les problèmes de sécurité courants, ainsi qu’un [modèle d’entreprise](https://azure.microsoft.com/pricing/details/sql-database/) favorable aux clients SQL Server locaux. Managed Instance permet aux clients SQL Server existants d’effectuer une migration « lift-and-shift » de leurs applications locales vers le cloud en apportant des modifications minimales aux applications et bases de données. En même temps, Managed Instance conserve toutes les fonctionnalités PaaS (correctifs et mises à jour de versions automatiques, [sauvegardes automatisées](sql-database-automated-backups.md), [haute disponibilité](sql-database-high-availability.md)), ce qui réduit considérablement le temps de gestion et le coût total de possession.

> [!IMPORTANT]
> Pour obtenir une liste de régions dans lesquelles Managed Instance est actuellement disponible, consultez [Migrate your databases to a fully managed service with Azure SQL Database Managed Instance](https://azure.microsoft.com/blog/migrate-your-databases-to-a-fully-managed-service-with-azure-sql-database-managed-instance/).
 
Le diagramme suivant présente les principales fonctionnalités de Managed Instance :

![fonctionnalités clés](./media/sql-database-managed-instance/key-features.png)

Azure SQL Database Managed Instance est conçu pour les clients cherchant à effectuer une migration d’un grand nombre d’applications locales ou provenant d’environnements IaaS, générés automatiquement ou fournis par un éditeur de logiciels indépendant, afin de gérer complètement un environnement cloud PaaS avec le moins d’efforts de migration possible. À l’aide du [service de migration des données](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) entièrement automatisé dans Azure, les clients peuvent effectuer une migration « lift-and-shift » de leur SQL Server local vers l’option Managed Instance qui offre une compatibilité avec SQL Server local et une isolation totale des instances des clients avec une prise en charge de réseau virtuel native.  Avec Software Assurance, vous pouvez échanger leurs licences existantes avec des tarifs réduits sur SQL Database Managed Instance en utilisant [Azure Hybrid Use Benefit pour SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  SQL Database Managed Instance est la meilleure destination de migration dans le cloud pour les instances SQL Server qui nécessitent une sécurité élevée et une surface de programmabilité riche. 

En disponibilité générale, Managed Instance a pour but d’offrir une compatibilité de la surface d’exposition proche de 100 % avec la dernière version de SQL Server locale par le biais d’un plan de mise en production intermédiaire. 

Pour choisir entre la base de données unique Azure SQL Database, Azure SQL Database Managed Instance et SQL Server IaaS hébergée dans une machine virtuelle, consultez [comment choisir la bonne version de SQL Server dans le cloud Azure](sql-database-paas-vs-sql-server-iaas.md).

## <a name="key-features-and-capabilities"></a>Fonctionnalités principales 

Azure SQL Database Managed Instance combine les meilleures fonctionnalités qui sont disponibles à la fois dans Azure SQL Database et le moteur de base de données SQL Server.

> [!IMPORTANT]
> Une instance Managed Instance s’exécute avec toutes les fonctionnalités de la version SQL Server la plus récente, notamment les opérations en ligne, les corrections de plan automatiques et autres améliorations des performances d’entreprise. 

| **Avantages PaaS** | **Continuité de l’activité** |
| --- | --- |
|Aucun achat ni gestion de matériel <br>Aucun temps de gestion à dédier à l’infrastructure sous-jacente <br>Provisionnement et mise à l’échelle du service rapides <br>Application automatisée de correctifs et de mises à niveau de version <br>Intégration à d’autres services de données PaaS |Contrat SLA à 99,99 % de durée de fonctionnement  <br>[Haute disponibilité](sql-database-high-availability.md) intégrée <br>Données protégées par des [sauvegardes automatisées](sql-database-automated-backups.md) <br>Période de rétention de sauvegarde configurable par le client (7 jours en préversion publique) <br>[Sauvegardes](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) lancées par l’utilisateur <br>Fonctionnalité de [limite de restauration dans le temps d’une base de données](sql-database-recovery-using-backups.md#point-in-time-restore) |
|**Sécurité et conformité** | **Gestion**|
|Environnement isolé ([intégration de réseau virtuel](sql-database-managed-instance-vnet-configuration.md), service de locataire unique, calcul et stockage dédiés) <br>[Chiffrement transparent des données (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>Prise en charge de l’[authentification unique Azure AD](sql-database-aad-authentication.md) <br>Conformité aux mêmes normes qu’une base de données Azure SQL <br>[Audit SQL](sql-database-managed-instance-auditing.md) <br>[Détection de menaces](sql-database-managed-instance-threat-detection.md) |API Azure Resource Manager pour automatiser le provisionnement et la mise à l’échelle des services <br>Fonctionnalités du portail Azure pour le provisionnement et la mise à l’échelle manuels des services <br>Service de migration des données 

## <a name="vcore-based-purchasing-model"></a>Modèle d’achat vCore

Le [modèle d’achat basé sur des vCores](sql-database-service-tiers-vcore.md) vous assure flexibilité, contrôle et transparence. Il permet de traduire de manière simple les exigences des charges de travail locales vers le cloud. Ce modèle permet de mettre à l’échelle le calcul, la mémoire et le stockage en fonction des besoins des charges de travail. Le modèle vCore permet également de réaliser jusqu’à 30 % d’économies avec [Azure Hybrid Use Benefit pour SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

Un vCore est l’UC logique qui permet de choisir parmi plusieurs générations de matériel.
- Les processeurs logiques Gen 4 sont basés sur des processeurs Intel E5-2673 v3 (Haswell) 2,4 GHz.
- Les processeurs logiques Gen 5 sont basés sur des processeurs Intel E5-2673 v4 (Broadwell) 2,3 GHz.

Le tableau suivant vous aidera à sélectionner la configuration optimale pour vos ressources de calcul, de mémoire, de stockage et d’E/S.

||Gen 4|Gen 5|
|----|------|-----|
|Matériel|Processeurs Intel E5-2673 v3 (Haswell) 2,4 GHz, disque SSD attaché vCore = 1 PP (cœur physique)|Processeurs Intel E5-2673 v4 (Broadwell) 2,3 GHz, disque SSD fast eNVM, vCore = 1 LP (hyperthread)|
|Niveaux de performances|8, 16, 24 vCores|8, 16, 24, 32, 40, 64, 80 vCores|
|Mémoire|7 Go par vCore|5,5 Go par vCore|
||||

## <a name="managed-instance-service-tiers"></a>Niveaux de service de Managed Instance

Managed Instance est disponible en deux niveaux de service :
- **Usage général** : conçu pour des applications avec des exigences de performances et de latence d’E/S standard.
- **Critique pour l’entreprise** : conçu pour les applications avec des exigences de latence d’E/S faible et un impact minimal des opérations de maintenance sous-jacentes sur la charge de travail.

Les deux niveaux de service garantissent une disponibilité de 99,99 % et vous permettent de sélectionner la taille de stockage et la capacité de calcul indépendamment. Pour plus d’informations sur l’architecture à haute disponibilité d’Azure SQL Database, voir [Haute disponibilité et Azure SQL Database](sql-database-high-availability.md).

> [!IMPORTANT]
> Le changement de niveau de service de Usage général à Critique pour l’entreprise, ou inversement n’est pas pris en charge dans la Préversion publique. Si vous souhaitez migrer vos bases de données vers une instance d’un autre niveau de service, vous pouvez créer une instance, restaurer les bases de données avec limite de restauration dans le temps à partir de l’instance d’origine, puis abandonner celle-ci si elle n’est plus nécessaire. 

### <a name="general-purpose-service-tier"></a>Niveau de service Usage général

La liste suivante décrit les principales caractéristiques du niveau de service Usage général : 

- Concevoir pour la majorité des applications métier avec des exigences de performances standard 
- Stockage Azure Premium à hautes performances (8 To) 
- 100 bases de données par instance 

La liste suivante décrit les principales caractéristiques du niveau de service Usage général :

|Fonctionnalité | Description|
|---|---|
| Nombre de vCores* | 8, 16, 24 (Gen 4)<br>8, 16, 24, 32, 40, 64, 80 (Gen 5)|
| Version/Build de SQL Server | Moteur de base de données SQL Server (dernière version stable) |
| Taille de stockage minimale | 32 Go |
| Taille de stockage maximale | 8 To |
| Espace de stockage maximal par base de données | Déterminé par la taille de stockage maximale par instance |
| IOPS de stockage attendues | De 500 à 7 500 IOPS par fichier de données (dépend du fichier de données) Consultez [Stockage Premium](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes). |
| Nombre de fichiers de données (ROWS) par base de données | Multiple | 
| Nombre de fichiers journaux (LOG) par base de données | 1 | 
| Sauvegardes automatisées gérées | Oui |
| Haute disponibilité | Données stockées dans Stockage Azure et [Azure Service Fabric](../service-fabric/service-fabric-overview.md) |
| Analyse et métriques des instances et bases de données intégrées | Oui |
| Mise à jour corrective automatique des logiciels | Oui |
| Réseau virtuel - Déploiement Azure Resource Manager | Oui |
| Réseau virtuel - Modèle de déploiement classique | Non  |
| Prise en charge du portail | Oui|
|||

\* Un vCore représente l’UC logique offerte avec un choix à opérer entre plusieurs générations de matériel. Les UC logiques de 4e génération sont basées sur des processeurs Intel E5-2673 v3 (Haswell) de 2,4 GHz, et celles de 5e génération sur des processeurs Intel E5-2673 v4 (Broadwell) de 2,3 GHz. 

Pour plus d’informations, consultez [Architecture et disponibilité standard/à usage général](sql-database-high-availability.md#standardgeneral-purpose-availability) dans Azure SQL Database.

### <a name="business-critical-service-tier"></a>Niveau de service critique pour l’entreprise

Le niveau de service Critique pour l’entreprise est conçu pour les applications dont les exigences en termes d’E/S sont élevées. Il offre la meilleure résilience aux échecs en utilisant plusieurs réplicas isolés Always On. 

La liste suivante décrit les principales caractéristiques du niveau de service Critique pour l’entreprise : 
-   Conçu pour les applications d’entreprise avec les exigences les plus hautes en matière de performances et de disponibilité 
-   Fourni avec un stockage SSD extrêmement rapide (jusqu’à 1 To sur Gen 4, jusqu’à 4 To sur Gen 5)
-   Prend en charge jusqu'à 100 bases de données par instance 
- Des instances en lecture seule intégrées supplémentaires qui peuvent être utilisées pour la création de rapports et d’autres charges de travail en lecture seule
- [OLTP en mémoire](sql-database-in-memory.md) qui peut être utilisé pour les charges de travail avec des exigences de haute performance  

|Fonctionnalité | Description|
|---|---|
| Nombre de vCores* | 8, 16, 24, 32 (Gen 4)<br>8, 16, 24, 32, 40, 64, 80 (Gen 5)|
| Version/Build de SQL Server | SQL Server (version la plus récente disponible) |
| Fonctionnalités supplémentaires | [OLTP en mémoire](sql-database-in-memory.md)<br> 1 réplica en lecture seule supplémentaire ([échelle horizontale en lecture](sql-database-read-scale-out.md))
| Taille de stockage minimale | 32 Go |
| Taille de stockage maximale | Gen 4 : 1 To (toutes tailles de vCore)<br> Gen 5 :<ul><li>1 To pour 8, 16 vCores</li><li>2 To pour 24 vCores</li><li>4 To pour 32, 40, 64, 80 vCores</ul>|
| Espace de stockage maximal par base de données | Déterminé par la taille de stockage maximale par instance |
| Nombre de fichiers de données (ROWS) par base de données | Multiple | 
| Nombre de fichiers journaux (LOG) par base de données | 1 | 
| Sauvegardes automatisées gérées | Oui |
| Haute disponibilité | Données stockées sur disque SSD local qui utilisent des [groupes de disponibilité AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) et [Azure Service Fabric](../service-fabric/service-fabric-overview.md) |
| Analyse et métriques des instances et bases de données intégrées | Oui |
| Mise à jour corrective automatique des logiciels | Oui |
| Réseau virtuel - Déploiement Azure Resource Manager | Oui |
| Réseau virtuel - Modèle de déploiement classique | Non  |
| Prise en charge du portail | Oui|
|||

Pour plus d’informations, consultez [Architecture et disponibilité Premium/critiques pour l'entreprise](sql-database-high-availability.md#premiumbusiness-critical-availability) dans Azure SQL Database.

## <a name="advanced-security-and-compliance"></a>Sécurité et conformité avancées 

Azure SQL Database Managed Instance combine les fonctionnalités de sécurité avancées fournies par le cloud Azure et le moteur de base de données SQL Server. 

### <a name="managed-instance-security-isolation-in-azure-cloud"></a>Isolation de la sécurité Managed Instance dans le cloud Azure 

Managed Instance offre une isolation de la sécurité supplémentaire par rapport aux autres locataires dans le cloud Azure. L’isolation de la sécurité inclut : 

- [Implémentation d’un réseau virtuel natif](sql-database-managed-instance-vnet-configuration.md) et sa connexion à votre environnement local à l’aide d’Azure Express Route ou d’une passerelle VPN 
- Exposition du point de terminaison SQL uniquement par le biais d’une adresse IP privée, ce qui permet de sécuriser la connexion à partir de réseaux Azure privés ou hybrides
- Locataire unique avec infrastructure sous-jacente dédiée (calcul, stockage)

Le diagramme suivant présente différentes options de connectivité pour vos applications : 

![haute disponibilité](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Pour plus d’informations sur l’intégration de réseau virtuel et les applications de stratégie de mise en réseau au niveau du sous-réseau, voir [Configurer un réseau virtuel pour Azure SQL Database Managed Instance](sql-database-managed-instance-vnet-configuration.md) et [Connecter votre application à Azure SQL Database Managed Instance](sql-database-managed-instance-connect-app.md). 

> [!IMPORTANT]
> Placez plusieurs instances gérées dans le même sous-réseau, partout où vos exigences de sécurité l’autorisent, car cela vous apportera des avantages supplémentaires. La colocalisation d’instances dans le même sous-réseau simplifie considérablement la maintenance de l’infrastructure réseau et réduit le temps d’approvisionnement instance, car une durée d’approvisionnement longue est associée au coût de déploiement de la première instance gérée dans un sous-réseau.

### <a name="azure-sql-database-security-features"></a>Fonctionnalités de sécurité d’Azure SQL Database

Azure SQL Database fournit un ensemble de fonctionnalités de sécurité avancées qui peuvent être utilisées pour protéger vos données.

- [L’audit Managed Instance](sql-database-managed-instance-auditing.md) suit les événements de base de données et les écrit dans un fichier journal d’audit placé dans votre compte de stockage Azure. L’audit permet de respecter une conformité réglementaire, de comprendre l’activité de la base de données et de découvrir des discordances et des anomalies susceptibles d’indiquer des problèmes pour l’entreprise ou des violations de la sécurité. 
- Chiffrement des données en mouvement : Managed Instance sécurise vos données par le biais d’un chiffrement des données en mouvement à l’aide du protocole TLS. En plus du protocole TLS, SQL Database Managed Instance offre une protection des données sensibles en vol, au repos et pendant le traitement des requêtes avec [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted est une nouveauté qui offre une protection inégalée des données contre les failles de sécurité impliquant le vol de données critiques. Par exemple, avec Always Encrypted, les numéros de carte de crédit sont toujours chiffrés dans la base de données, même pendant le traitement des requêtes, ce qui permet le déchiffrement au point d’utilisation par les applications ou le personnel autorisés qui doivent traiter ces données. 
- La [Détection de menaces](sql-database-managed-instance-threat-detection.md) complète l’[Audit Managed Instance](sql-database-managed-instance-auditing.md) en fournissant une couche supplémentaire d’informations de sécurité intégrée au service. Elle détecte les tentatives inhabituelles ou potentiellement dangereuses d’accès ou d’exploitation des failles de sécurité des bases de données. Vous êtes alerté en cas d’activités suspectes, de vulnérabilités potentielles, d’attaques par injection de code SQL et de modèles d’accès anormaux à la base de données. Les alertes Threat Detection peuvent être consultées à partir d’[Azure Security Center](https://azure.microsoft.com/services/security-center/). Elles fournissent des détails sur les activités suspectes et recommandent l’action à entreprendre pour analyser et prévenir la menace.  
- Le [masquage des données dynamiques](/sql/relational-databases/security/dynamic-data-masking) limite l’exposition des données sensibles en les masquant pour les utilisateurs sans privilège. Le masquage des données dynamique contribue à empêcher tout accès non autorisé aux données sensibles en vous permettant d’indiquer la quantité de données sensibles à révéler avec un impact minimal sur la couche Application. Il s’agit d’une fonctionnalité de sécurité basée sur des stratégies qui masque les données sensibles dans le jeu de résultats d’une requête dans les champs de la base de données désignés. Les données de la base de données ne sont pas modifiées. 
- La [sécurité au niveau des lignes](/sql/relational-databases/security/row-level-security) vous permet de contrôler l’accès aux lignes d’une table de base de données en fonction des caractéristiques de l’utilisateur qui exécute une requête (par exemple, appartenance à un groupe ou contexte d’exécution). La sécurité au niveau des lignes (RLS) simplifie la conception et le codage de la sécurité dans votre application. Elle vous permet d’implémenter des restrictions sur l’accès aux lignes de données. Par exemple, en s’assurant que les employés ne peuvent accéder qu’aux lignes de données utiles à leur service, ou en limitant l’accès aux données aux seules données pertinentes. 
- [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) : également appelé chiffrement des données au repos, chiffre les fichiers de données d’Azure SQL Managed Instance. TDE effectue le chiffrement et le déchiffrement d’E/S en temps réel des données et des fichiers journaux. Le chiffrement utilise une clé de chiffrement de base de données stockée dans l’enregistrement de démarrage de base de données à des fins de disponibilité lors de la récupération. Vous pouvez protéger toutes vos bases de données dans Managed Instance avec un chiffrement transparent des données. Il s’agit de la technologie de chiffrement au repos éprouvée de SQL, qui est requise par de nombreuses normes de conformité comme protection contre le vol d’un support de stockage. Durant la préversion publique, le modèle de gestion automatique des clés (effectuée par la plateforme PaaS) est pris en charge. 

La migration d’une base de données chiffrée vers SQL Managed Instance est prise en charge via Azure Database Migration Service (DMS) ou une restauration native. Si vous envisagez de migrer une base de données chiffrée en utilisant une restauration native, la migration du certificat TDE existant du serveur SQL Server local ou de la machine virtuelle SQL Server vers Managed instance est une étape obligatoire. Pour plus d’informations sur les options de migration, voir [Migration d’une instance SQL Server vers Azure SQL Database Managed Instance](sql-database-managed-instance-migrate.md).

## <a name="azure-active-directory-integration"></a>Intégration d'Azure Active Directory

Azure SQL Database Managed Instance prend en charge les connexions à des moteurs de base de données SQL Server traditionnelles et les connexions intégrées à Azure Active Directory (AAD). Les connexions AAD sont une version cloud Azure des connexions aux bases de données Windows que vous utilisez dans votre environnement local.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Intégration d’Azure Active Directory et authentification multifacteur 

Managed Instance vous permet de gérer de manière centralisée les identités d’utilisateur de base de données et d’autres services Microsoft avec l’[Intégration d’Azure Active Directory](sql-database-aad-authentication.md). Cette fonctionnalité simplifie la gestion des autorisations et améliore la sécurité. Azure Active Directory prend en charge l’[authentification multifacteur](sql-database-ssms-mfa-authentication-configure.md) (MFA) pour augmenter la sécurité des données et des applications, ainsi qu’un processus d’authentification unique. 

### <a name="authentication"></a>Authentification 
L’authentification Managed Instance fait référence au processus de validation de l’identité des utilisateurs lorsqu’ils se connectent à la base de données. Une base de données SQL prend en charge deux types d’authentification :  

- L’authentification SQL, qui utilise un nom d’utilisateur et un mot de passe.
- L’authentification Azure Active Directory, qui utilise des identités gérées par Azure Active Directory et qui est prise en charge pour des domaines gérés et intégrés. 

### <a name="authorization"></a>Authorization

Le terme autorisation fait référence aux actions qu’un utilisateur peut exécuter au sein d’Azure SQL Database. Celles-ci sont contrôlées par les appartenances aux rôles et les autorisations au niveau objet de la base de données de votre compte d’utilisateur. Managed Instance a les mêmes fonctionnalités d’autorisation que SQL Server 2017. 

## <a name="database-migration"></a>Migration de base de données 

Managed Instance cible des scénarios d’utilisateur impliquant une migration de base de données en masse depuis des implémentations locales ou IaaS. Managed Instance prend en charge plusieurs options de migration de base de données : 

### <a name="backup-and-restore"></a>Sauvegarde et restauration  

L’approche de la migration s’appuie sur les sauvegardes SQL dans Stockage Blob Azure. Les sauvegardes stockées dans Azure Storage Blob peuvent être directement restaurées dans Managed Instance à l’aide de la [commande T-SQL RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current). 
  - Pour suivre un tutoriel montrant comment restaurer le fichier de sauvegarde de base de données Wide World Importers - Standard, consultez [Restaurer un fichier de sauvegarde dans une instance gérée](sql-database-managed-instance-restore-from-backup-tutorial.md). Ce tutoriel vous montre que vous devez charger un fichier de sauvegarde dans Stockage Blob Azure et le sécuriser à l’aide d’une clé de signature d’accès partagé (SAP).
  - Pour plus d’informations sur la restauration à partir d’une URL, consultez [Restauration native à partir d’une URL](sql-database-managed-instance-migrate.md#native-restore-from-url).
  
### <a name="data-migration-service"></a>Service de migration des données

Azure Database Migration Service est un service entièrement géré conçu pour permettre des migrations transparentes de plusieurs sources de base de données vers des plateformes de données Azure avec un temps d’arrêt minime. Ce service simplifie les tâches nécessaires pour déplacer des bases de données SQL Server tierces existantes vers Azure. Les options de déploiement incluent Azure SQL Database, Managed Instance et SQL Server dans une machine virtuelle Azure en préversion publique. Consultez [Comment migrer votre base de données locale vers Managed Instance à l’aide de DMS](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Fonctionnalités SQL prises en charge 

Managed Instance vise à assurer une compatibilité de la surface d’exposition proche de 100 % avec SQL Server local par étapes jusqu’à la disponibilité générale du service. Pour obtenir la liste des fonctionnalités et des différences, consultez [Comparaison des fontionnalités SQL Database](sql-database-features.md). Pour obtenir la liste des différences de T-SQL entre Managed Instance et SQL Server, consultez [Différences de T-SQL entre Managed Instance et SQL Server](sql-database-managed-instance-transact-sql-information.md).
 
Managed Instance prend en charge la compatibilité descendante avec les bases de données SQL 2008. La migration directe à partir de serveurs de base de données SQL 2005 est prise en charge, le niveau de compatibilité des bases de données SQL 2005 qui ont migré est mis à jour vers SQL 2008. 
 
Le diagramme suivant illustre la compatibilité de la surface d’exposition dans Managed Instance :  

![migration](./media/sql-database-managed-instance/migration.png) 

### <a name="key-differences-between-sql-server-on-premises-and-managed-instance"></a>Principales différences entre SQL Server local et Managed Instance 

Managed Instance tire parti du fait d’être toujours à jour dans le cloud, ce qui signifie que certaines fonctionnalités de SQL Server local peuvent quant à elles être obsolètes, supprimées ou remplacées par d’autres. Dans certains cas, les outils ont besoin de reconnaître qu’une fonctionnalité particulière agit de façon légèrement différente ou que le service n’est pas exécuté dans un environnement que vous ne contrôlez pas entièrement : 

- La haute disponibilité est intégrée et préconfigurée. Les fonctionnalités de haute disponibilité Always On ne sont pas exposées de la même manière que sur des implémentations SQL IaaS. 
- Les sauvegardes sont automatisées et la restauration dans le temps limitée. Le client peut lancer des sauvegardes `copy-only` qui n’interfèrent pas avec la chaîne de sauvegarde automatique. 
- Managed Instance ne permet pas de spécifier des chemins physiques complets si bien que tous les scénarios correspondants doivent être pris en charge différemment : RESTORE DB ne prend pas en charge WITH MOVE, CREATE DB n’autorise pas de chemins physiques, BULK INSERT fonctionne avec les objets blob Azure uniquement, etc. 
- Managed Instance prend en charge l’[authentification Azure AD](sql-database-aad-authentication.md) en tant qu’alternative cloud à l’authentification Windows. 
- Managed Instance gère automatiquement le groupe de fichiers et les fichiers XTP des bases de données contenant des objets OLTP In-Memory.
- Managed Instance prend en charge SSIS (SQL Server Integration Services) et peut héberger le catalogue SSIS (SSISDB) qui stocke les packages SSIS, mais ceux-ci sont exécutés sur une instance Azure-SSIS IR (Integration Runtime) gérée dans ADF (Azure Data Factory). Consultez [Créer Azure-SSIS IR dans ADF](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime). Pour comparer les fonctionnalités SSIS dans SQL Database et Managed Instance, consultez [Comparer SQL Database et Managed Instance (préversion)](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-and-managed-instance-preview).

### <a name="managed-instance-administration-features"></a>Fonctionnalités administratives de Managed Instance  

Managed Instance permet aux administrateurs système de se concentrer sur ce qui importe le plus à l’entreprise. De nombreuses activités d’administrateur système/de base de données ne sont pas nécessaires ou sont très simples. Par exemple, l’installation et la mise à jour corrective d’un système d’exploitation/système de gestion de base de données relationnelle, le redimensionnement et la configuration d’une instance dynamique, les sauvegardes, la réplication de base de données (notamment des bases de données système), ainsi que la configuration des flux de données de surveillance de l’intégrité et des performances. 

> [!IMPORTANT]
> Pour obtenir la liste des fonctionnalités prises en charge, partiellement prises en charge et non prises en charge, consultez [Fonctionnalités de SQL Database](sql-database-features.md). Pour obtenir la liste des différences de T-SQL entre Managed Instance et SQL Server, consultez [Différences de T-SQL entre Managed Instance et SQL Server](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Comment identifier par programmation une option Managed Instance

Le tableau suivant montre plusieurs propriétés, accessibles par le biais de Transact SQL, que vous pouvez utiliser pour détecter que votre application fonctionne avec Managed Instance et récupérer des propriétés importantes.

|Propriété|Valeur|Commentaire|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Cette valeur est identique à celle indiquée dans SQL Database.|
|`SERVERPROPERTY ('Edition')`|SQL Azure|Cette valeur est identique à celle indiquée dans SQL Database.|
|`SERVERPROPERTY('EngineEdition')`|8|Cette valeur identifie Managed Instance de façon unique.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nom DNS d’instance complet au format suivant :<instanceName>.<dnsPrefix>.database.windows.net, où <instanceName> est le nom fourni par le client, tandis que <dnsPrefix> est une partie générée automatiquement du nom garantissant l’unicité des noms DNS globaux (par exemple, « wcus17662feb9ce98 »)|Exemple : my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment créer votre première Managed Instance, consultez [Guide de démarrage rapide](sql-database-managed-instance-get-started.md).
- Pour consulter la liste des fonctionnalités et les comparer, consultez [Fonctionnalités SQL communes](sql-database-features.md).
- Pour plus d’informations sur la configuration du réseau virtuel, consultez [Configure a VNet for Azure SQL Database Managed Instance](sql-database-managed-instance-vnet-configuration.md) (Configurer un réseau virtuel pour une option Azure SQL Database Managed Instance).
- Pour un tutoriel qui crée une option Managed Instance et restaure une base de données à partir d’un fichier de sauvegarde, consultez [Créer une option Managed Instance](sql-database-managed-instance-create-tutorial-portal.md).
- Pour un tutoriel utilisant le service Azure Database Migration Service (DMS) pour la migration, consultez [Migrer SQL vers Azure SQL Database Managed Instance](../dms/tutorial-sql-server-to-managed-instance.md).
- Pour plus d’informations sur la tarification, voir [Tarification de SQL Database Managed Instance](https://azure.microsoft.com/pricing/details/sql-database/managed/).
