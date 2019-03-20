---
title: Blueprint sur la sécurité et la conformité d’Azure - Hébergement d’application web PaaS pour les charges de travail « UK OFFICIAL »
description: Blueprint sur la sécurité et la conformité d’Azure - Hébergement d’application web PaaS pour les charges de travail « UK OFFICIAL »
services: security
author: jomolesk
ms.assetid: 446105ad-a863-44f5-a964-6ead1dac4787
ms.service: security
ms.topic: article
ms.date: 07/13/2018
ms.author: jomolesk
ms.openlocfilehash: 7ac210d9b30a05fd5dbfd46c41c3e62a1d610357
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57891101"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-hosting-for-uk-official-workloads"></a>Blueprint de sécurité et de conformité Azure : Hébergement d’applications web PaaS pour les charges de travail « UK OFFICIAL »

## <a name="azure-security-and-compliance-blueprints"></a>Schémas de sécurité et de conformité Azure

Les blueprints Azure sont constitués de documents de conseils et de modèles d’automatisation qui déploient des architectures cloud pour offrir des solutions à des scénarios ayant des spécifications particulières en matière d’accréditation ou de conformité. Les blueprints Azure sont des collections de conseils et de modèles d’automatisation qui permettent aux clients de Microsoft Azure d’accélérer la mise en œuvre de leurs objectifs métier via une architecture de base, qui peut être étendue pour répondre à toute spécification supplémentaire.

## <a name="overview"></a>Présentation

Ce blueprint sur la sécurité et la conformité d’Azure fournit des conseils et des scripts d’automatisation pour mettre en œuvre une architecture d’applications web hébergées sur une [plateforme PaaS (Platform as a Service)](https://azure.microsoft.com/overview/what-is-paas/) Microsoft Azure permettant de gérer des charges de travail classifiées [UK OFFICIAL](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/715778/May-2018_Government-Security-Classifications-2.pdf). Cette classification de sécurité englobe la plupart des informations créées ou traitées par le secteur public. Ceci inclut des opérations et des services métier de routine qui, s’ils étaient perdus, volés ou publiés dans les médias, pourraient pour certains avoir des conséquences néfastes. Le profil typique des menaces pour la classification « OFFICIAL » est très similaires à celui d’une entreprise privée qui fournit des informations et des services ayant une certaine valeur. « UK OFFICIAL » anticipe sur la nécessité de défendre les données ou les services du secteur public du Royaume-Uni contre les menaces ou les compromissions par des attaquants avec des moyens et des ressources limités, comme (mais non limités à) des hactivistes, des groupes de pression pour un problème spécifique, des journalistes d’investigation, des hackers individuels compétents, et la majorité des personnes et des groupes commettant des délits.

Ce blueprint a été revu par le NCSC (National Cyber Security Centre) du Royaume-Uni et s’aligne sur les 14 principes de sécurité cloud du NCSC.

L’architecture utilise des composants [PaaS](https://azure.microsoft.com/overview/what-is-paas/) Azure pour fournir un environnement qui permet aux clients d’éviter les dépenses et la complexité liées à l’achat de licences de logiciels, de gestion de l’infrastructure d’application sous-jacente et des intergiciels (middleware), ou des outils de développement et d’autres ressources. Les clients gèrent les applications et les services qu’ils développent, en se concentrant sur la mise à disposition d’une valeur métier, tandis que Microsoft Azure gère les autres ressources Azure, comme les machines virtuelles, le stockage et la mise en réseau, en confiant, dans le cadre de la [répartition des responsabilités](https://docs.microsoft.com/azure/security/security-paas-deployments#division-of-responsibility), la plus grande partie de la responsabilité de la gestion de l’infrastructure à la plateforme Azure. [Azure App Services](https://azure.microsoft.com/services/app-service/) offre comme services par défaut une mise à l’échelle automatique et une haute disponibilité, prend en charge Windows et Linux, et permet des déploiements automatisés à partir de GitHub, d’Azure DevOps ou de n’importe quel référentiel Git. Grâce à l’utilisation d’App Services, les développeurs peuvent se concentrer sur la mise à disposition d’une valeur commerciale métier sans devoir s’occuper de la gestion de l’infrastructure. Il est possible de créer des applications web Java, PHP, Node.js, Python, HTML ou C# entièrement nouvelles, ou de migrer des applications web cloud ou locales existantes vers Azure App Services (une attention et des tests approfondis étant néanmoins nécessaires pour vérifier les performances).

Ce blueprint est principalement consacré au provisionnement d’une interface web [PaaS](https://azure.microsoft.com/overview/what-is-paas/) de base sécurisée, destinée au public ainsi qu’aux utilisateurs de back office. Ce scénario de conception du blueprint considère l’utilisation de services web hébergés dans Azure, où un utilisateur public peut envoyer, visualiser et gérer des données sensibles de façon sécurisée, et où un opérateur de back office ou du secteur public peut traiter de façon sécurisée les données sensibles que l’utilisateur public a envoyées. Voici des cas d’utilisation de ce scénario :

- Un utilisateur envoyant une déclaration d’impôts, avec un opérateur d’une administration traitant l’envoi.
- Un utilisateur demandant un service via une application web, avec un utilisateur de back office validant et fournissant le service.
- Un utilisateur recherchant et consultant des informations d’aide du domaine public concernant un service d’un organisme public.

En utilisant des modèles [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) et des scripts Azure CLI, le blueprint déploie un environnement qui s’aligne sur les 14 [principes de sécurité cloud](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) du NCSC (National Cyber Security Centre), ainsi que sur les [contrôles de sécurité critiques](https://www.cisecurity.org/critical-controls.cfm) du CIS (Center for Internet Security) pour le Royaume-Uni. Le NCSC recommande aux clients d’utiliser ses principes de sécurité cloud dans le but d’évaluer les propriétés de sécurité du service, et de mieux comprendre la répartition des responsabilités entre le client et le fournisseur. Microsoft a fourni des informations sur chacun de ces principes pour vous aider à mieux comprendre la répartition des responsabilités. Cette architecture et les modèles Azure Resource Manager correspondants sont accompagnés du livre blanc de Microsoft concernant [les 14 contrôles de sécurité du cloud à l’aide de Microsoft Azure au Royaume-Uni](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). Cette architecture a été revue par le NCSC et s’aligne sur les 14 principes de sécurité cloud du NCSC, permettant ainsi aux organisations du secteur public de répondre plus rapidement à leurs obligations de conformité, en utilisant les services cloud de Microsoft Azure dans le monde entier et au Royaume-Uni. Ce modèle déploie l’infrastructure de la charge de travail. Le code de l’application, ainsi que les logiciels prenant en charge le niveau métier et le niveau données, doivent être installés et configurés par les clients. Des instructions détaillées sur le déploiement sont disponibles [ici](https://aka.ms/ukofficial-paaswa-repo/).

Ce blueprint est une architecture de base. Nos clients peuvent utiliser ce blueprint comme base pour leurs charges de travail web de classification « OFFICIAL » et développer les modèles et les ressources avec leurs propres spécifications. Ce blueprint s’appuie sur les principes du [blueprint sur les applications web IaaS à trois niveaux « UK-OFFICIAL »](https://aka.ms/ukofficial-iaaswa) pour offrir à nos clients les deux choix d’implémentation [IaaS](https://azure.microsoft.com/overview/what-is-iaas/) et PaaS pour l’hébergement de charges de travail web.

Pour déployer ce blueprint, vous avez besoin d’un abonnement Azure. Si vous n’avez pas d’abonnement Azure, vous pouvez vous inscrire rapidement, facilement et gratuitement : Bien démarrer avec Azure. Cliquez [ici](https://aka.ms/ukofficial-paaswa-repo/) pour obtenir des instructions de déploiement.

## <a name="architecture-and-components"></a>Architecture et composants

Ce blueprint fournit une solution d’hébergement d’applications web dans un environnement cloud Azure qui prend en charge les charges de travail « UK OFFICIAL ». L’architecture fournit un environnement sécurisé qui tire parti des fonctionnalités PaaS d’Azure. Dans l’environnement, deux applications web App Service sont déployées (une pour les utilisateurs publics et l’autre pour les utilisateurs de back office) avec un niveau d’application d’API pour fournir les services métier au frontend web. Une base de données Azure SQL est déployée comme magasin de données relationnelles managé pour l’application. La connectivité à ces composants depuis l’extérieur de la plateforme et entre tous ces composants est chiffrée via TLS 1.2 pour la confidentialité des données pendant le transport, avec un accès authentifié par Azure Active Directory.

![Diagramme de l’architecture de référence Hébergement d’applications web PaaS pour les charges de travail « UK OFFICIAL »](images/ukofficial-paaswa-architecture.png?raw=true "Diagramme de l’architecture de référence Hébergement d’applications web PaaS pour les charges de travail « UK OFFICIAL »")

Dans le cadre de l’architecture de déploiement, un provisionnement de stockage sécurisé, une surveillance et une journalisation, une gestion de la sécurité unifiée et une protection avancée contre les menaces, ainsi que des fonctionnalités de gestion, sont également déployés, de façon à garantir que les clients ont tous les outils nécessaires pour sécuriser et surveiller leur environnement pour cette solution.

Cette solution utilise les services Azure suivants. Les détails de l’architecture de déploiement figurent dans la section [Architecture de déploiement](#deployment-architecture).

- Azure Active Directory
- App Service
- Application web
- Application API
- Azure DNS
- Key Vault
- Azure Monitor (journaux)
- Application Insights
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Stockage Azure

## <a name="deployment-architecture"></a>Architecture de déploiement

La section ci-après décrit en détail les éléments nécessaires au déploiement et à l’implémentation.

### <a name="security"></a>Sécurité

#### <a name="identity-and-authentication"></a>Identité et authentification

Ce blueprint garantit que l’accès aux ressources est protégé via des services de gestion des annuaires et des identités. Cette architecture exploite pleinement [l’identité comme périmètre de sécurité](https://docs.microsoft.com/azure/security/security-paas-deployments). 

Les technologies suivantes fournissent des fonctionnalités de gestion des identités dans l’environnement Azure :

- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) est le service Microsoft de gestion des annuaires et des identités, basé sur le cloud, multilocataire. Tous les utilisateurs de la solution ont été créés dans Azure Active Directory, y compris ceux qui accèdent à la base de données SQL.
- L’authentification auprès de l’application web pour les opérateurs et de l’accès pour l’administration des ressources Azure est effectuée avec Azure AD. Pour plus d’informations, consultez [Intégration d’applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- Le chiffrement des colonnes de base de données utilise Azure AD pour authentifier l’application auprès d’Azure SQL Database. Pour plus d’informations, consultez [Always Encrypted : Protéger les données sensibles dans SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- L’application web pour les citoyens est configurée pour un accès public. Pour permettre la création et l’authentification des comptes via Active Directory ou des fournisseurs d’identité de réseau social, [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) peut être intégré si nécessaire.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) détecte les vulnérabilités potentielles et les comptes à risques, fournit des recommandations pour améliorer le comportement des identités de votre organisation quant à la sécurité, et configure les réponses automatiques aux actions suspectes détectées qui sont liées aux identités de votre organisation. Il examine aussi les incidents suspects et prend les mesures nécessaires pour les résoudre.
- Le [contrôle d’accès en fonction du rôle (RBAC) Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) permet une gestion précise de l’accès pour Azure. L’accès aux abonnements est limité à l’administrateur des abonnements, et l’accès à Azure Key Vault est limité aux seuls utilisateurs pour lesquels un accès à la gestion des clés est nécessaire.
- Grâce à l’utilisation de [l’accès conditionnel Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal), les clients peuvent appliquer des contrôles de sécurité supplémentaires sur l’accès aux applications ou sur les utilisateurs dans leur environnement, en fonction de conditions spécifiques comme les risques liés à l’emplacement, à l’appareil, à l’état et à la connexion.
- [Azure DDoS Protection](https://docs.microsoft.com/azure/security/security-paas-deployments#security-advantages-of-a-paas-cloud-service-model), combiné avec des bonnes pratiques de conception des applications, assure une défense contre les attaques DDoS, avec une surveillance permanente du trafic et une limitation en temps réel des attaques courantes au niveau du réseau. Avec une architecture PaaS, la protection DDoS au niveau de la plateforme est transparente pour le client et incorporée à la plateforme, mais il est important de noter que la responsabilité de la conception de la sécurité des applications incombe au client.

#### <a name="data-in-transit"></a>Données en transit

Les données en transit provenant de l’extérieur et entre les composants Azure sont protégées avec [TLS/SSL (Transport Layer Security/Secure Sockets Layer)](https://www.microsoft.com/TrustCenter/Security/Encryption), qui utilise le chiffrement symétrique basé sur un secret partagé pour chiffrer les communications quand elles transitent sur le réseau. Par défaut, le trafic réseau est sécurisé avec TLS 1.2.

#### <a name="security-and-malware-protection"></a>Sécurité et protection contre les programmes malveillants

[Azure Security Center](https://azure.microsoft.com/services/security-center/) vous offre une vue centralisée de l’état de sécurité de toutes vos ressources Azure. Vous pouvez vérifier d’un coup d’œil que les contrôles de sécurité appropriés sont en place et configurés correctement, et vous pouvez identifier rapidement les ressources nécessitant votre attention.

[Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) est un conseiller personnalisé basé dans le cloud qui décrit les bonnes pratiques à suivre pour optimiser vos déploiements Azure. Il analyse votre télémétrie de configuration et d’utilisation des ressources, puis recommande des solutions qui peuvent vous aider à améliorer la rentabilité, les performances, la haute disponibilité et la sécurité de vos ressources Azure.

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) est une fonctionnalité de protection en temps réel qui permet d’identifier et de supprimer les virus, les logiciels espions et d’autres logiciels malveillants. Il est installé par défaut sur l’infrastructure de machines virtuelles PaaS sous-jacente et est géré de façon transparente par l’infrastructure Azure, garantissant

### <a name="paas-services-in-this-blueprint"></a>Services PaaS de ce blueprint

#### <a name="azure-app-service"></a>Azure App Service

Azure App Service fournit un environnement d’hébergement web entièrement managé pour les applications web développées en Java, PHP, Node.js, Python, HTML et C#, sans la nécessité de gérer l’infrastructure. Il offre une mise à l’échelle automatique et une haute disponibilité, prend en charge à la fois Windows et Linux, et permet des déploiements automatisés à partir d’[Azure DevOps](https://azure.microsoft.com/services/visual-studio-team-services/) ou de tout référentiel Git.

App Service est [conforme à ISO, SOC et PCI](https://www.microsoft.com/TrustCenter/), et peut authentifier les utilisateurs avec [Azure Active Directory](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) ou avec une connexion de réseau social (authentification [Google](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-google), [Facebook](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-facebook), [Twitter](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-twitter) et [Microsoft](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-microsoft).

Les plans De base, Standard et Premium sont destinés aux charges de travail de production. Ils s’exécutent sur des instances dédiées de machine virtuelle. Chaque instance peut prendre en charge plusieurs applications et domaines. Les services d’application prennent également en charge les [limitations d’adresses IP](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions) pour sécuriser le trafic vers des adresses IP approuvées si nécessaire, ainsi que les [identités managées pour les ressources Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity) pour sécuriser la connexion vers d’autres services PaaS, comme [Key Vault](https://azure.microsoft.com/services/key-vault/) et [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Là où une sécurité supplémentaire est nécessaire, notre plan isolé héberge vos applications dans un environnement Azure privé dédié. Il est idéal pour les applications nécessitant des connexions sécurisées avec votre réseau local, ou des performances et une évolutivité supplémentaires.

Ce modèle déploie les fonctionnalités App Service suivantes :

- Niveau de plan App Service [Standard](https://docs.microsoft.com/azure/app-service/overview-hosting-plans)
- Plusieurs [emplacements de déploiement](https://docs.microsoft.com/azure/app-service/deploy-staging-slots) App Service : Développement, Préversion, AQ, UAT et bien sûr Production (emplacement par défaut).
- Des [identités managées pour les ressources Azure](https://docs.microsoft.com/azure/app-service/overview-managed-identity) pour se connecter à [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) (ceci peut être utilisé aussi pour fournir un accès à [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 
- Intégration à [Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-azure-web-apps) pour surveiller les performances
- [Journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) 
- [Alertes](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) de métrique 
- [Azure API Apps](https://azure.microsoft.com/services/app-service/api/) 

#### <a name="azure-sql-database"></a>Azure SQL Database

SQL Database est un service administré de bases de données relationnelles à usage général de Microsoft Azure qui prend en charge des structures telles que les données relationnelles, JSON, les données spatiales et XML. SQL Database offre des bases de données SQL uniques managées, des bases de données SQL managées dans un [pool élastique](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool) et des [instances managées](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) (Managed Instance) SQL (en préversion publique). Il offre des [performances évolutives dynamiques] (https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers) et des options comme les [index columnstore](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) pour la génération de rapports et d’analytique avancés, et [OLTP en mémoire](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory) pour le traitement transactionnel à hautes performances. Microsoft gère toutes les applications de correctifs et mises à jour de la base de code SQL en toute transparence et élimine toute la gestion de l’infrastructure sous-jacente.

Azure SQL Database dans ce blueprint

L’instance Azure SQL Database utilise les mesures suivantes pour la sécurité des bases de données :

- [Règles de pare-feu au niveau du serveur et au niveau de la base de données](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure), ou via des [points de terminaison de service de réseau virtuel](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) avec des [règles de réseau virtuel](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview).
- [Chiffrement transparent des données](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), qui protège Azure SQL Database et Azure Data Warehouse contre les menaces d’activités malveillantes. Il assure le chiffrement et le déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux des transactions au repos, sans que cela nécessite de modifier l’application.
- [Authentification Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) : vous pouvez gérer de façon centralisée les identités des utilisateurs de base de données et d’autres services Microsoft à un emplacement centralisé. La gestion centralisée des ID fournit un emplacement unique pour gérer les utilisateurs de la base de données et simplifie la gestion des autorisations.
- Utilisation d’Azure Active Directory pour l’administration des bases de données
- [Journaux d’audit](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) des comptes de stockage
- [Alertes](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) de métrique pour les échecs de connexion de base de données
- [Détection des menaces SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Colonnes Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

### <a name="azure-storage"></a>Stockage Azure

[Stockage Microsoft Azure](https://azure.microsoft.com/services/storage/) est un service cloud géré par Microsoft, qui fournit un stockage à haute disponibilité, sécurisé, durable, évolutif et redondant. Le stockage Azure se compose du stockage d’objets blob, du stockage de fichiers et du stockage de files d’attente.

#### <a name="azure-storage-in-this-blueprint"></a>Stockage Azure dans ce blueprint

Ce modèle utilise les composants Stockage Azure suivants :

- [Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 
- Autorise seulement les connexions HTTPS

#### <a name="data-at-rest"></a>Données au repos

Via [Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption), toutes les données écrites dans Stockage Azure sont chiffrées avec le chiffrement AES 256 bits, un des chiffrements par blocs parmi les plus forts. Vous pouvez utiliser des clés de chiffrement gérées par Microsoft avec SSE, ou bien utiliser [vos propres clés de chiffrement](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

Les comptes de stockage peuvent être sécurisés via des [points de terminaison de service de réseau virtuel](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) avec des [règles de réseau virtuel](https://docs.microsoft.com/azure/storage/common/storage-network-security).

Vous pouvez trouver des informations détaillées sur la sécurisation de Stockage Azure dans le [Guide de sécurité](https://docs.microsoft.com/azure/storage/common/storage-security-guide).


### <a name="secrets-management"></a>Gestion des secrets

#### <a name="azure-key-vault"></a>Azure Key Vault

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) est utilisé pour sécuriser les clés et les secrets des applications, pour garantir qu’ils ne sont pas accessibles par des tiers. Elle n’est pas destinée à faire office de magasin des mots de passe utilisateur. Key Vault vous permet de créer plusieurs conteneurs sécurisés, qui sont appelés « coffres ». Ces coffres sont secondés par des modules de sécurité matériels (HSM). Les coffres contribuent à réduire les risques de perte accidentelle des informations de sécurité en centralisant le stockage des secrets d’application. En outre, les coffres de clés contrôlent et journalisent l’accès à l’ensemble de leur contenu. Azure Key Vault peut traiter les requêtes et le renouvellement de certificats TLS et vous offre ainsi les fonctionnalités requises pour l’obtention d’une solution robuste en matière de gestion du cycle de vie des certificats.

#### <a name="azure-key-vault-in-this-blueprint"></a>Azure Key Vault dans ce blueprint

- Contient la clé d’accès du stockage, avec accès en lecture accordé à [l’identité managée](https://docs.microsoft.com/azure/app-service/overview-managed-identity) de l’application web utilisée par le client
- Contient le mot de passe de l’administrateur de base de données du serveur SQL Server (dans un coffre distinct)
- Journalisation des diagnostics

### <a name="monitoring-logging-and-audit"></a>Surveillance, journalisation et audit

#### <a name="azure-monitor-logs"></a>Journaux Azure Monitor

[Journaux d’analyse Azure](https://azure.microsoft.com/services/log-analytics/) est un service dans Azure qui vous permet de collecter et analyser les données générées par les ressources de votre cloud et les environnements locaux.

#### <a name="azure-monitor-logs-in-this-blueprint"></a>Journaux d’analyse Azure dans cette solution blueprint

- SQL Assessment
- Diagnostics Key Vault
- Connexion Application Insights
- Journaux d’activité

#### <a name="application-insights"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) est un service extensible de gestion des performances des applications (APM) destiné aux développeurs web sur de multiples plateformes. Utilisé pour surveiller les applications web dynamiques, il détecte automatiquement les anomalies des performances, analyse les performances et diagnostique les problèmes, et aide à comprendre comment les utilisateurs interagissent avec l’application. Application Insights peut être déployé sur des plateformes, notamment .NET, Node.js et Java EE, hébergées localement ou dans le cloud. Il s’intègre à votre processus DevOps et offre des points de connexion à un large éventail d’outils de développement.

#### <a name="application-insights-in-this-blueprint"></a>Application Insights dans ce blueprint

Ce modèle utilise les composants Application Insights suivants :

- Tableau de bord Application Insights par site (opérateur, client et API)

#### <a name="azure-activity-logs"></a>Journaux d’activité Azure

Le [Journal d’activité Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs#what-you-can-do-with-the-activity-log) audite les événements du plan de contrôle pour vos abonnements. Avec le journal d’activité, vous pouvez déterminer « quoi, qui et quand » pour toutes les opérations d’écriture (PUT, POST, DELETE) sur les ressources de votre abonnement. Vous pouvez également comprendre l’état de l’opération et d’autres propriétés pertinentes.

#### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) active la surveillance globale des services Azure en permettant la collecte de métriques, de journaux d’activité et de journaux de diagnostic. Azure Monitor fournit des métriques de niveau de base d’infrastructure et des journaux pour la plupart des services Microsoft Azure.

## <a name="threat-model"></a>Modèle de menace

Le diagramme de flux de données de cette architecture de référence figure ci-dessous et est également disponible en [téléchargement](https://aka.ms/ukofficial-paaswa-tm). Ce modèle peut aider les clients à comprendre les points de risque potentiel de l’infrastructure du système lors de l’apport de modifications.

![Hébergement d’applications PaaS Web pour le modèle de menaces des charges de travail « UK OFFICIAL »](images/ukofficial-paaswa-threat-model.png?raw=true "Hébergement d’applications PaaS Web pour le modèle de menaces des charges de travail « UK OFFICIAL »")

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>Documentation sur la conformité aux principes de sécurité cloud NCSC

Le Crown Commercial Service (l’agence chargée d’améliorer les activités commerciales et d’approvisionnement du secteur public) a mis à jour le classement G-Cloud des services cloud d’entreprise de Microsoft vers la version 6, classification qui couvre toutes ses offres de catégorie « OFFICIAL ». Pour obtenir des informations détaillées sur Azure et la classification G-Cloud, consultez le [résumé de l’évaluation de sécurité Azure UK G-Cloud](https://www.microsoft.com/trustcenter/compliance/uk-g-cloud).

Ce blueprint s’aligne sur les 14 principes de sécurité cloud documentés dans [Cloud Security Principles](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) du NCSC, qui permettent de garantir qu’un environnement est adapté aux charges de travail classifiées « UK OFFICIAL ».

Le document [Blueprint sur la sécurité et la conformité d’Azure - Matrice de responsabilité des clients « UK OFFICIAL »](https://aka.ms/ukofficial-crm) (classeur Excel) liste les 14 principes de sécurité cloud et indique, pour chaque principe (ou sous-principe), si son implémentation est de la responsabilité de Microsoft, du client, ou des deux.

Le document [Blueprint sur la sécurité et la conformité d’Azure - Matrice d’implémentation des principes des applications web PaaS pour « UK OFFICIAL »](https://aka.ms/ukofficial-paaswa-pim) (classeur Excel) liste les 14 principes de sécurité cloud, et indique, pour chaque principe (ou sous-principe) qui est de la responsabilité du client selon la Matrice de responsabilités des clients, (1) si le blueprint implémente le principe et (2) une description de la façon dont l’implémentation s’aligne avec les exigences du principe.  

En outre, le CSA (Cloud Security Alliance) a publié une matrice de contrôle cloud pour aider les clients à évaluer les fournisseurs cloud et à se poser les questions nécessaires avant de passer à des services cloud. En réponse, Microsoft Azure a répondu au questionnaire [CSA CAIQ](https://www.microsoft.com/TrustCenter/Compliance/CSA) (CSA Consensus Assessment Initiative Questionnaire), en décrivant comment Microsoft apporte une réponse aux principes suggérés.

## <a name="third-party-assessment"></a>Évaluation par des tiers

Ce blueprint ont été revu par le NCSC (National Cyber Security Centre) du Royaume-Uni et s’aligne sur les 14 principes de sécurité cloud du NCSC.

Les modèles d’automatisation ont été testés par l’équipe « UK Customer Success Unit Azure Cloud Solution Architect » et par notre partenaire Microsoft, [Ampliphae](https://www.ampliphae.com/).


## <a name="deploy-the-solution"></a>Déployer la solution

Cette solution Azure Security and Compliance Blueprint Automation se compose de fichiers de configuration JSON et de scripts PowerShell qui sont gérés par le service d’API d’Azure Resource Manager pour déployer des ressources dans Azure. Des instructions détaillées sur le déploiement sont disponibles [ici](https://aka.ms/ukofficial-paaswa-repo).

Trois approches ont été fournies pour le déploiement : une approche [Azure CLI 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) « express » simple pour créer rapidement un environnement de test ; une approche [Azure CLI 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) paramétrable qui fournit une configuration plus avancée pour les environnements de charge de travail ; un déploiement basé sur le portail Azure, où l’opérateur peut spécifier les paramètres de déploiement via le portail Azure. 

1.  Clonez ou téléchargez [ce dépôt GitHub](https://aka.ms/ukofficial-paaswa-repo) sur votre station de travail locale.
2.  Passez en revue [Méthode 1 : Azure CLI 2 (version Express)](https://aka.ms/ukofficial-paaswa-repo/#method-1-azure-cli-2-express-version) et exécutez les commandes fournies.
3.  Passez en revue [Méthode 1a : Azure CLI 2 (configuration du déploiement à l’aide d’arguments de script)](https://aka.ms/ukofficial-paaswa-repo/#method-1a-azure-cli-2-configuring-the-deployment-via-script-arguments) et exécutez les commandes fournies
4.  Passez en revue [Méthode 2 : Processus de déploiement via le Portail Microsoft Azure](https://aka.ms/ukofficial-paaswa-repo/#method-2-azure-portal-deployment-process) et exécutez les commandes listées

## <a name="guidance-and-recommendations"></a>Instructions et recommandations

### <a name="api-management"></a>Gestion des API

[Gestion des API Azure](https://azure.microsoft.com/services/api-management/) peut être utilisé devant l’API App Service pour fournir des couches supplémentaires de sécurité, de limitation et de contrôles pour exposer, mettre en cache et protéger des API.

### <a name="azure-b2c"></a>Azure B2C

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) peut être implémenté en tant que contrôle pour permettre aux utilisateurs de s’inscrire, de créer une identité, et d’activer les autorisations et le contrôle d’accès pour l’application web publique.

## <a name="disclaimer"></a>Clause d'exclusion de responsabilité

- Ce document est fourni à titre d’information uniquement. MICROSOFT N’ACCORDE AUCUNE GARANTIE EXPRESSE, IMPLICITE OU STATUTAIRE EN LIEN AVEC LES INFORMATIONS CONTENUES DANS CE DOCUMENT. Ce document est fourni « en l’état ». Les informations et les points de vue exprimés dans ce document, y compris les URL et autres références à des sites web, peuvent être modifiés sans préavis. Les clients qui lisent ce document assument les risques liés à son utilisation.
- Ce document n’accorde aux clients aucun droit légal de propriété intellectuelle sur les produits ou solutions Microsoft.
- Les clients peuvent copier et utiliser ce document pour un usage interne, à titre de référence.
- Certaines recommandations contenues dans ce document peuvent entraîner une augmentation des taux d’utilisation des données, des réseaux ou des ressources de calcul dans Azure, débouchant sur des coûts de licence ou d’abonnement supplémentaires.
- Cette architecture constitue une base que les clients peuvent modifier en fonction de leurs besoins. Elle ne doit pas être utilisée telle quelle dans un environnement de production.
- Développé en tant que référence, ce document ne doit pas être utilisé pour définir tous les moyens par lesquels un client peut satisfaire à des réglementations et exigences de conformité spécifiques. Les clients doivent rechercher une assistance juridique auprès de leur organisation pour connaître les implémentations client approuvées.
