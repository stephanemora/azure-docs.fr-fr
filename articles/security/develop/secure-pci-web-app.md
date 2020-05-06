---
title: Sécuriser une application web pour PCI DSS | Microsoft Docs
description: Cet exemple d’application met en œuvre les bonnes pratiques de sécurité qui améliorent votre application, de même que le niveau de sécurité de votre organisation lorsque vous développez sur Azure.
keywords: na
services: security
documentationcenter: na
author: fehase
manager: rkarlin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: terrylan
ms.openlocfilehash: 4ab3697824ff4a47e7b8f281b531cae610ffdc3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187577"
---
# <a name="develop-a-secure-infrastructure-for-a-pci-app"></a>Développer une infrastructure sécurisée pour une application PCI

## <a name="overview"></a>Vue d’ensemble

Cette infrastructure sécurisée pour une application PCI (Payment Card Industry) fournit des conseils pour déployer une plateforme PCI comme un environnement de service (PaaS) adapté à la collecte, au stockage et à la récupération des données de titulaires de carte. Cette solution automatise le déploiement et la configuration des ressources Azure pour une architecture de référence commune, illustrant diverses façons dont les clients peuvent satisfaire à des exigences de conformité et de sécurité spécifiques, et sert de base aux clients souhaitant générer et configurer leurs propres solutions sur Azure. La solution implémente un sous-ensemble d’exigences similaires aux normes de sécurité des données appliquées dans l’industrie des cartes de paiement (PCI DSS 3.2).

Cet exemple déploie automatiquement une architecture de référence d’application web PaaS avec des contrôles de sécurité préconfigurés pour aider les clients à se conformer aux exigences similaires aux normes PCI DSS 3.2. La solution se compose de modèles Azure Resource Manager et de scripts PowerShell qui guident le déploiement et la configuration des ressources.

Vous devez suivre les étapes décrites dans cet article de manière séquentielle pour vous assurer de la bonne configuration des composants de l’application. La base de données, Azure App Service, l'instance Azure Key Vault et l'instance Azure Application Gateway dépendent les uns des autres.

Les scripts de déploiement configurent l’infrastructure. Une fois les scripts de déploiement exécutés, vous devez procéder à une configuration manuelle dans le portail Azure pour lier les composants et services.

Cet exemple est destiné aux développeurs expérimentés sur Azure qui travaillent dans le secteur de la vente au détail et souhaitent créer une application de vente au détail avec une infrastructure Azure sécurisée.

> [!NOTE]
> Cette architecture constitue une base que les clients peuvent modifier en fonction de leurs besoins. Elle ne doit pas être utilisée telle quelle dans un environnement de production.

Le déploiement d’une application dans cet environnement, sans modification, ne suffit pas pour répondre entièrement aux exigences de PCI DSS 3.2. Notez les points suivants :

- Cette architecture est une base de référence qui permet aux clients d’utiliser Azure conformément aux exigences de PCI DSS 3.2.
- Les clients doivent réaliser des évaluations de sécurité et de conformité pour toute solution créée à l’aide de cette architecture, car les exigences peuvent varier selon leur implémentation.

Lors du développement et du déploiement de cette application, vous allez apprendre à :

- Créer une instance Azure Key Vault, y stocker des secrets et en récupérer.
- Déployer Azure Database pour Azure SQL, sécuriser les données et en autoriser l’accès.
- Déployer l’application web Azure dans un environnement App Service dédié qui est isolé avec un accès au pare-feu front-end.
- Créer et configurer une instance Azure Application Gateway avec un pare-feu utilisant les [10 principaux ensembles de règles OWASP](https://coreruleset.org/).
- Activer le chiffrement des données en transit comme au repos à l’aide des services Azure.
- Configurer Azure Policy et Azure Blueprints pour évaluer la conformité.

Après avoir développé et déployé cette application, vous aurez configuré l’exemple d’application web suivant avec la configuration et les mesures de sécurité décrites.

## <a name="architecture-diagram-and-components"></a>Diagramme et composants de l’architecture
L’application est une application multiniveau type présentant trois niveaux. Le serveur frontal, le back end et la couche de base de données avec composants de surveillance et de gestion des secrets intégrés sont présentés ici :

![Architecture d’application](./media/secure-pci-web-app/architecture.png)

L’architecture est constituée des composants suivants :

- [App Service Environment v2](https://docs.microsoft.com/azure/app-service/environment/intro/). Fournit App Service Environment et l’équilibreur de charge pour l’architecture de l’application.
- [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/) : Fournit la passerelle et le pare-feu pour l'architecture de l'application.
- [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Fournit un service extensible de gestion des performances des applications (APM) sur de multiples plateformes.
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/). Stocke et chiffre les secrets de l’application et gère la création des stratégies d’accès qui s'y rapportent.
- [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis/). Fournit un service cloud de gestion des identités et des accès, la connexion et l’accès aux ressources.
- [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview). Fournit le service d’hébergement du domaine.
- [Équilibreur de charge Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview/). Met à l’échelle vos applications et fournit une haute disponibilité pour vos services.
- [Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction/). Fournit une solution adaptée aux scénarios de stockage de données d’aujourd’hui.
- [Azure Web App](https://docs.microsoft.com/azure/app-service/overview/).  Fournit un service HTTP pour l’hébergement des applications web.
- [Azure Database pour AzureSQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview/). Stocke en toute sécurité les données de l’application.
- [Azure Blueprints](https://docs.microsoft.com/azure/governance/blueprints/overview/). Fournit des spécifications et une conformité avec certaines normes et exigences.
- [Centre de sécurité Azure](https://docs.microsoft.com/azure/security-center/)
- [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview). Évalue vos ressources afin d’épingler toute non-conformité avec les stratégies affectées.

## <a name="threat-model"></a>Modèle de menace
La modélisation des menaces est le processus d’identification des menaces de sécurité potentielles pour votre entreprise et votre application, puis de vérification qu'un plan d'atténuation approprié, est en place.

Cet exemple a utilisé [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) pour implémenter la modélisation des menaces pour l’exemple d’application sécurisée. En reconstituant les composants et les flux de données, vous pouvez identifier les problèmes et menaces dès le début du processus de développement. Cela permet d’économiser ensuite du temps et de l’argent.

Voici le modèle de menace de l’exemple d’application :

![Modèle de menace](./media/secure-pci-web-app/threat-model.png)

Certains exemples de menaces et de vulnérabilités potentielles générés par l'outil de modélisation sont illustrés dans la capture d'écran suivante. Le modèle de menace offre une vue d’ensemble de la surface d’attaque exposée et invite les développeurs à réfléchir à la manière d’atténuer les problèmes.

![Sortie du modèle de menace](./media/secure-web-app/threat-model-output.png)

Par exemple, l’injection de code SQL dans la sortie du modèle de menace précédente est atténuée en nettoyant les entrées utilisateur et en utilisant des fonctions stockées dans Azure Database pour PostgreSQL. Cette atténuation empêche l’exécution arbitraire des requêtes lors des lectures et écritures de données.

Les développeurs améliorent la sécurité globale du système en atténuant chacune des menaces dans la sortie du modèle de menace.

## <a name="deployment"></a>Déploiement
### <a name="prerequisites"></a>Prérequis
Pour permettre le bon fonctionnement de l'application, vous devez installer les outils suivants :

- Un éditeur de code pour modifier et afficher le code de l’application. [Visual Studio Code](https://code.visualstudio.com/) est une solution open source.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest) sur votre ordinateur de développement.
- [Git](https://git-scm.com/) sur votre système. Git est utilisé pour cloner le code source localement.
- [jq](https://stedolan.github.io/jq/) est un outil UNIX pour interroger JSON de manière conviviale.

Cet exemple se compose de fichiers de configuration JSON et de scripts PowerShell qui sont gérés par le service API d’Azure Resource Manager pour déployer des ressources dans Azure. Des instructions détaillées sur le déploiement sont disponibles [ici](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM).

#### <a name="quickstart"></a>Démarrage rapide

1.  Clonez ou téléchargez [ce dépôt GitHub](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM.git) sur votre station de travail locale.
2.  Examinez 0-Setup-AdministrativeAccountAndPermission.md et exécutez les commandes fournies.
3.  Déployez une solution de test avec les exemples de données Contoso ou pilotez un environnement de production initial.

    Ce script déploie des ressources Azure pour la démonstration d’une boutique en ligne à l’aide d’exemples de données Contoso.

Dans cet exemple, vous exécutez le script de déploiement qui déploie l’application web sur App Service et crée les ressources.

Il existe de nombreuses manières de déployer des applications sur Azure, notamment :

- Modèles Microsoft Azure Resource Manager
- PowerShell
- Azure CLI
- Portail Azure
- Azure DevOps

## <a name="guidance-and-recommendations"></a>Instructions et recommandations

### <a name="network"></a>Réseau
L’architecture définit un réseau privé virtuel avec l’espace d’adressage 10.200.0.0/16.
 ![Virtual_Network](./media/secure-pci-web-app/virtual-network.png)

### <a name="network-security-groups"></a>Groupes de sécurité réseau
Les groupes de sécurité réseau (https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contiennent des listes de contrôle d’accès qui autorisent ou interdisent le trafic au sein d’un réseau virtuel. Les groupes de sécurité réseau peuvent être utilisés pour sécuriser le trafic au niveau d’un sous-réseau ou d’une machine virtuelle individuelle. Les groupes de sécurité réseau suivants existent :

- 1 groupe de sécurité réseau pour Application Gateway
- un groupe de sécurité réseau pour App Service Environment
- 1 groupe de sécurité réseau pour Azure SQL Database
- 1 groupe de sécurité réseau pour l’hôte bastion

Chaque groupe de sécurité réseau a des ports et protocoles spécifiques ouverts pour que la solution fonctionne correctement et de manière sécurisée. Par ailleurs, les configurations suivantes sont activées pour chaque groupe de sécurité réseau :

- Les événements et journaux de diagnostic (https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) sont activés et stockés dans un compte de stockage
- Les journaux d’Azure Monitor sont mis en lien avec les diagnostics du groupe de sécurité réseau (https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

### <a name="nsg-config"></a>Configuration des groupes de sécurité réseau (NSG)
Les groupes de sécurité réseau pour App Service Environment doivent avoir la même configuration que celle présentée dans l’image ci-dessous.

 ![NSG_Config](./media/secure-pci-web-app/nsg-config.png)

chaque sous-réseau est associé au groupe de sécurité réseau qui lui correspond.

### <a name="config"></a>Config
Les sous-réseaux sont configurés comme dans l’image ci-dessous.
 ![Config](./media/secure-pci-web-app/config.png)

### <a name="azure-dns"></a>Azure DNS
Le DNS (Domain Name System) se charge de traduire (ou de résoudre) un nom de site web ou de service en une adresse IP. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) est un service d’hébergement pour domaines DNS, qui assure la résolution de noms à l’aide de l’infrastructure Azure. En hébergeant des domaines dans Azure, les utilisateurs peuvent gérer des enregistrements DNS en utilisant les mêmes informations d’identification, API, outils et modes de facturation que pour d’autres services Azure. Azure DNS prend désormais également en charge les domaines DNS privés.

### <a name="protect-data"></a>Protection des données
Pour assurer la protection des données dans le cloud, vous devez tenir compte des états que les données peuvent présenter, mais aussi des contrôles disponibles pour ces états. Les bonnes pratiques pour la sécurité et le chiffrement des données Azure s’apparentent aux états des données suivants :

- Au repos : Ceci inclut tous les objets de stockage, conteneurs et types d’informations présents de manière statique sur un support physique, qu’il s’agisse d’un disque magnétique ou d’un disque optique.
- En transit : Quand des données sont transférées entre des composants, des emplacements ou des programmes, elles sont considérées comme étant « en transit ». Exemples : transfert sur le réseau, sur un bus des services (du réseau local au cloud et inversement, y compris les connexions hybrides telles que ExpressRoute), ou pendant un traitement d’entrée/sortie.

### <a name="azure-storage"></a>Stockage Azure
Pour répondre aux exigences de chiffrement des données au repos, l’ensemble du [stockage Azure](https://azure.microsoft.com/services/storage/) utilise Azure Key Vault pour conserver le contrôle des clés qui chiffrent les données et y accèdent. Cela permet de protéger et de sauvegarder les données de titulaire de carte dans le respect des engagements de l’organisation en matière de sécurité et des exigences de conformité définies par PCI DSS 3.2.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption utilise la fonctionnalité BitLocker de Windows pour chiffrer les volumes des disques de données. La solution s’intègre à Azure Key Vault pour faciliter le contrôle et la gestion des clés de chiffrement de disque.

### <a name="azure-sql-database"></a>Azure SQL Database
L’instance Azure SQL Database utilise les mesures suivantes pour la sécurité des bases de données :

- La solution [d’authentification et d’autorisation Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permet de gérer les identités des utilisateurs de bases de données et d’autres services Microsoft dans un emplacement central.
- L’[audit Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started/) suit les événements de base de données et consigne ceux-ci dans un journal d’audit conservé dans un compte de stockage Azure.
- Azure SQL Database est configuré pour utiliser la technologie [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) qui assure le chiffrement et le déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux de transactions afin de protéger les informations au repos. Transparent Data Encryption protège les données des accès non autorisés.
- Des [règles de pare-feu](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) empêchent tout accès aux serveurs de base de données sans autorisation appropriée. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande.
- La [détection des menaces SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permet de détecter et traiter les menaces potentielles à mesure qu’elles surviennent en déclenchant des alertes de sécurité en cas d’activités de base de données suspectes, de vulnérabilités potentielles, d’attaques par injection de code SQL et de modèles d’accès anormaux aux bases de données.
- Des [colonnes Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantissent que les données sensibles n’apparaissent jamais en tant que texte en clair à l’intérieur du système de base de données. Une fois le chiffrement des données activé, seules des applications clientes ou des serveurs d’applications ayant accès aux clés peuvent accéder aux données en texte clair.
- [SQL Database Dynamic Data Masking](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limite l’exposition des données sensibles en masquant celles-ci aux utilisateurs ou aux applications dépourvus de privilèges. Le masquage dynamique des données peut découvrir automatiquement les données potentiellement sensibles et suggérer les masques appropriés à appliquer. Cela permet d’identifier et de réduire l’accès aux données afin que celles-ci ne quittent pas la base de données via un accès non autorisé. Les clients doivent ajuster les paramètres de masquage dynamique des données pour respecter leur schéma de base de données.

### <a name="identity-management"></a>Gestion des identités
Les technologies suivantes offrent des fonctionnalités de gestion d’accès aux données de titulaire de carte dans l’environnement Azure :

- Azure Active Directory est le service cloud multilocataire de gestion des identités et des annuaires fourni par Microsoft. Tous les utilisateurs de cette solution sont créés dans Azure Active Directory, notamment ceux qui accèdent à Azure SQL Database.
- L’authentification auprès de l’application est effectuée à l’aide d’Azure Active Directory. Pour plus d’informations, consultez [Intégration d’applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration). En outre, le chiffrement des colonnes de base de données utilise Azure Active Directory pour authentifier l’application auprès d’Azure SQL Database. Pour plus d’informations, découvrez comment [protéger des données sensibles dans Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Le contrôle d’accès en fonction du rôle Azure permet aux administrateurs de définir des autorisations d’accès affinées pour n’accorder aux utilisateurs que l’accès dont ils ont besoin dans le cadre de leur travail. Au lieu d’accorder à tous les utilisateurs des autorisations illimitées sur les ressources Azure, les administrateurs peuvent autoriser seulement certaines actions pour accéder aux données de titulaire de carte. Par exemple, l’accès aux abonnements est limité à l’administrateur d’abonnements.
- Azure Active Directory Privileged Identity Management permet aux clients de restreindre le nombre d’utilisateurs qui ont accès à des informations spécifiques, comme les données de titulaires de carte. Les administrateurs peuvent utiliser Azure Active Directory Privileged Identity Management pour découvrir, restreindre et superviser les identités privilégiées et leur accès aux ressources. Cette fonctionnalité permet également d’appliquer un accès administratif juste-à-temps à la demande si nécessaire.
- La solution Azure Active Directory Identity Protection détecte les vulnérabilités risquant d’affecter les identités d’une organisation, configure des réponses automatiques aux actions suspectes détectées en lien avec les identités d’une organisation, et examine les incidents suspects afin de prendre les mesures appropriées pour résoudre ces derniers.

### <a name="secrets-management"></a>Gestion des secrets
La solution utilise Azure Key Vault pour gérer les clés et les secrets. Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. Les fonctionnalités Azure Key Vault ci-après aident les clients à protéger les données et à y accéder :

- Les stratégies d’accès avancées sont configurées en fonction des besoins.
- Les stratégies d’accès Key Vault sont définies avec des autorisations minimales requises pour les clés et les secrets.
- L’ensemble des clés et secrets figurant dans Key Vault présentent des dates d’expiration.
- Toutes les clés dans Key Vault sont protégées par des modules de sécurité matériels spécialisés. La clé est de type RSA 2 048 bits protégé par HSM.
- Avec Key Vault, vous pouvez chiffrer les clés et les secrets (tels que les clés d’authentification, les clés de compte de stockage, les clés de chiffrement de données, les fichiers .PFX et les mots de passe) à l’aide de clés protégées par des modules de sécurité matériels (HSM).
- Utilisez la fonctionnalité de contrôle d’accès en fonction du rôle (RBAC) pour attribuer des autorisations aux utilisateurs, groupes et applications à une certaine étendue.
- Utilisez Key Vault pour gérer vos certificats TLS avec renouvellement automatique.
- Les journaux de diagnostics pour Key Vault sont activés avec une période de rétention d’au moins 365 jours.
- Les opérations de chiffrement autorisées pour les clés sont restreintes à celles qui sont nécessaires.

### <a name="azure-security-center"></a>Azure Security Center
Avec Azure Security Center, les clients peuvent appliquer et gérer de façon centralisée des stratégies de sécurité sur les charges de travail, limiter l’exposition aux menaces ainsi que détecter les attaques et y répondre. Par ailleurs, Azure Security Center accède aux configurations existantes des services Azure pour fournir des recommandations en matière de configuration et de service. Cela contribue à l’amélioration du dispositif de sécurité et à la protection des données.

Azure Security Center utilise diverses fonctionnalités de détection pour avertir les clients des attaques potentielles qui ciblent leur environnement. Ces alertes fournissent de précieuses informations sur le déclencheur de l’alerte, les ressources ciblées et la source de l’attaque. Azure Security Center fournit un ensemble d’alertes de sécurité prédéfinies, qui sont déclenchées en cas de menace ou d’activité suspecte. Les règles d’alerte personnalisées d’Azure Security Center permettent aux clients de définir de nouvelles alertes de sécurité basées sur les données déjà collectées dans leur environnement.

Azure Security Center fournit des incidents et des alertes de sécurité hiérarchisés, ce qui permet aux clients de découvrir et de résoudre plus facilement les problèmes de sécurité potentiels. Un rapport de Threat Intelligence est généré pour chaque menace détectée afin d’aider les équipes chargées de répondre aux incidents à examiner et à contrecarrer les menaces.

### <a name="azure-application-gateway"></a>Azure Application Gateway
l’architecture réduit le risque de failles de sécurité en utilisant une passerelle d’application avec un pare-feu d’applications web Azure configuré et l’ensemble de règles OWASP activé. Les autres fonctionnalités incluent notamment :

- SSL de bout en bout
- Désactivation de TLS versions 1.0 et 1.1
- Activation de TLS version 1.2
- Pare-feu d’applications web (mode de prévention)
- Mode de prévention avec l’ensemble de règles OWASP 3.0
- Activer la journalisation des diagnostics
- Sondes d’intégrité personnalisées
- Azure Security Center et Azure Advisor fournissent une protection et des notifications supplémentaires. Azure Security Center fournit également un système de réputation.

### <a name="logging-and-auditing"></a>Journalisation et audit
Les services Azure assurent une journalisation complète de l’activité du système et des utilisateurs, ainsi que de l’intégrité du système :

- les [journaux d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fournissent des insights sur les opérations ayant été effectuées sur les ressources d’un abonnement. Les journaux d’activité peuvent aider à déterminer l’initiateur, l’heure d’exécution et l’état d’une opération.
- les [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluent l’ensemble des journaux d’activité générés par chaque ressource. Ils incluent les journaux système des événements Windows, les journaux d’activité de Stockage Azure, les journaux d’audit de Key Vault, ainsi que les journaux d’activité de pare-feu et d’accès d’Application Gateway. Tous les journaux de diagnostic sont consignés dans un compte de stockage Azure centralisé et chiffré pour l’archivage. L’utilisateur peut configurer la rétention jusqu’à 730 jours pour répondre aux exigences de rétention spécifiques de l’entreprise.

### <a name="azure-monitor-logs"></a>Journaux d’activité Azure Monitor
ces journaux sont regroupés dans [Journaux Azure Monitor](https://azure.microsoft.com/services/log-analytics/) à des fins de traitement, de stockage et de génération de rapports de tableau de bord. Une fois collectées, les données sont organisées dans différentes tables en fonction du type de données dans des espaces de travail Log Analytics. Toutes les données sont ainsi analysées ensemble, quelle que soit leur source d’origine. Par ailleurs, Azure Security Center s’intègre à Journaux Azure Monitor pour permettre aux clients d’utiliser des requêtes Kusto ; ils peuvent alors accéder à leurs données d’événement de sécurité et les combiner avec des données provenant d’autres services.

Les [solutions de supervision](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Azure suivantes sont incluses dans cette architecture :

- [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment) : la solution Active Directory Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit une liste hiérarchisée de suggestions spécifiques pour l’infrastructure de serveur déployée.
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment) : la solution SQL Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit aux clients une liste hiérarchisée de suggestions spécifiques pour l’infrastructure de serveur déployée.
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth) : cette solution de contrôle de l’intégrité des agents indique le nombre d’agents déployés et leur répartition géographique, ainsi que le nombre d’agents qui ne répondent pas ou qui envoient des données opérationnelles.
- [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity) : la solution Activity Log Analytics facilite l’analyse des journaux d’activité Azure de tous les abonnements Azure d’un client.

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) aide les utilisateurs à suivre les performances, garantir la sécurité et identifier les tendances en permettant aux organisations d’auditer, de créer des alertes et d’archiver des données, y compris le suivi des appels d’API dans leurs ressources Azure.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) est un service extensible de gestion des performances des applications destiné aux développeurs web sur de multiples plateformes. La solution Application Insights détecte des anomalies de performances et permet aux clients de surveiller leurs applications web dynamiques. Elle inclut de puissants outils d’analyse qui aident les clients à diagnostiquer les problèmes et à comprendre ce que les utilisateurs font réellement avec leurs applications. Elle a été conçue pour aider les clients à améliorer continuellement les performances et l’ergonomie des applications.

### <a name="azure-key-vault"></a>Azure Key Vault
Créez un coffre pour l’organisation dans lequel stocker les clés et avoir la responsabilité de tâches opérationnelles comme celles-ci :

- Les données stockées dans Key Vault sont les suivantes :

   - Clé Application Insights
   - Clé d’accès de stockage des données
   - Chaîne de connexion
   - Nom de la table de données
   - Informations d’identification de l’utilisateur

- Les stratégies d’accès avancées sont configurées en fonction des besoins.
- Les stratégies d’accès à Key Vault sont définies avec des autorisations minimales requises pour les clés et les secrets.
- Toutes les clés et tous les secrets dans Key Vault ont des dates d’expiration.
- Toutes les clés dans Key Vault sont protégées par HSM [Type de clé = clé RSA 2048 bits protégée par HSM]
- Les autorisations minimales requises sont accordées à l’ensemble des utilisateurs et identités à l’aide d’un contrôle d’accès en fonction du rôle (RBAC).
- Les applications ne partagent pas un coffre de clés, sauf si elles s’approuvent mutuellement et qu’elles ont besoin d’accéder aux mêmes secrets au moment de l’exécution.
- Les journaux de diagnostics pour Key Vault sont activés avec une période de rétention d’au moins 365 jours.
- Les opérations de chiffrement autorisées pour les clés sont limitées à celles qui sont nécessaires.

### <a name="vpn-and-expressroute"></a>VPN et ExpressRoute
Un tunnel VPN sécurisé ou un routage [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) doivent être configurés pour établir une connexion sécurisée aux ressources déployées en lien avec cette architecture de référence d’application web PaaS. En configurant correctement un VPN ou un routage ExpressRoute, les clients peuvent ajouter une couche de protection au niveau des données en transit.

En implémentant un tunnel VPN sécurisé avec Azure, il est possible de créer une connexion privée virtuelle entre un réseau local et un réseau virtuel Azure. Cette connexion via Internet permet aux clients de « tunneler » des informations en toute sécurité par le biais d’une liaison chiffrée entre leur réseau et Azure. La technologie de réseau privé virtuel de site à site, aussi sécurisée qu’éprouvée, est déployée par des entreprises de toutes tailles depuis des décennies. Le mode tunnel IPsec est utilisé dans cette option comme mécanisme de chiffrement.

Étant donné que le trafic à l’intérieur du tunnel VPN transite par Internet avec un VPN de site à site, Microsoft offre une autre option de connexion encore plus sécurisée. Azure ExpressRoute est une liaison réseau étendu dédiée entre Azure et un emplacement local ou un fournisseur d’hébergement Exchange. Les connexions ExpressRoute ne transitent pas par Internet, et offrent de meilleurs niveaux de fiabilité, de rapidité, de latence et de sécurité que les connexions classiques via Internet. En outre, comme il s’agit d’une connexion directe du fournisseur de télécommunications du client, les données ne circulent pas sur Internet et n’y sont donc pas exposées.

Les meilleures pratiques concernant l’implémentation d’un réseau hybride sécurisé qui étend un réseau local à Azure sont [décrites ici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="cost-considerations"></a>Considérations relatives au coût
Si vous n'avez pas encore de compte Azure, vous pouvez en créer un gratuitement. Pour commencer, accédez à la [page de compte gratuit](https://azure.microsoft.com/free/), voyez ce que vous pouvez faire avec un compte Azure gratuit et découvrez les produits gratuits pendant 12 mois.

Pour déployer les ressources dans l’exemple d’application avec les fonctionnalités de sécurité, vous devez payer certaines fonctionnalités Premium. À mesure que l'application évolue et que les niveaux et essais gratuits offerts par Azure doivent être mis à niveau pour répondre aux besoins de l’application, les coûts sont susceptibles d'augmenter. Utiliser la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/) pour estimer les coûts.

## <a name="next-steps"></a>Étapes suivantes
Les articles suivants peuvent vous aider à concevoir, développer et déployer des applications sécurisées.

- [Conception](secure-design.md)
- [Développement](secure-develop.md)
- [Déployer](secure-deploy.md)
