---
title: 'Blueprint sur la sécurité et la conformité Azure : application web IaaS pour PCI DSS'
description: 'Blueprint sur la sécurité et la conformité Azure : application web IaaS pour PCI DSS'
services: security
author: meladie
ms.assetid: 9c825380-2abe-4fdd-800c-59d1fed1780b
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: 361d7683790128aafb11d6581c6e672b68ad893f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60609224"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-pci-dss"></a>Blueprint de sécurité et de conformité Azure : Application Web de IaaS pour la norme PCI DSS

## <a name="overview"></a>Présentation

Ce Blueprint sur la sécurité et la conformité Azure fournit des conseils pour déployer un environnement d’infrastructure en tant que service (IaaS) conforme aux normes PCI DSS 3.2 (Payment Card Industry Data Security Standards ), adapté à la collecte, au stockage et à la récupération des données de titulaire de cartes. Il présente une architecture de référence courante et montre comment gérer les données de carte de crédit (notamment le numéro de carte, la date d’expiration et les données de vérification) dans un environnement multiniveau, conforme et sécurisé. Ce plan est un exemple de solution de bout en bout qui répond aux besoins des entreprises souhaitant une approche basée sur le cloud visant à réduire la charge et les coûts liés aux déploiements.

L’architecture de référence, le guide d’implémentation et le modèle de risque constituent une base pour se conformer aux exigences de PCI DSS 3.2. Cette solution fournit une base de référence pour aider les clients à déployer des charges de travail sur Azure conformément à PCI DSS 3.2. Toutefois, elle ne doit pas être utilisée en l’état dans un environnement de production, pour lequel une configuration supplémentaire est nécessaire.

La certification de conformité PCI DSS d’une solution cliente de production est délivrée par un évaluateur de sécurité qualifié (QSA) agréé. Il incombe aux clients d’évaluer de façon appropriée la sécurité et la conformité de toute solution basée sur cette architecture, car les exigences peuvent varier en fonction des spécificités de chaque implémentation.

## <a name="architecture-diagram-and-components"></a>Diagramme et composants de l’architecture

Cette solution déploie une architecture de référence pour une application web IaaS avec un serveur principal SQL Server. L’architecture inclut une couche web, une couche Données, une infrastructure Active Directory, une passerelle d’application (Application Gateway) et un équilibreur de charge (Load Balancer). Les machines virtuelles déployées sur les couches web et données sont configurées dans un groupe à haute disponibilité, tandis que les instances SQL Server sont configurées dans un groupe de disponibilité AlwaysOn pour une haute disponibilité. Les machines virtuelles sont jointes au domaine, et des stratégies de groupe Active Directory sont utilisées pour appliquer des configurations de sécurité et de conformité au niveau du système d’exploitation. Un hôte bastion de gestion fournit une connexion sécurisée permettant aux administrateurs d’accéder aux ressources déployées. **Azure recommande de configurer une connexion VPN ou ExpressRoute pour la gestion et l’importation de données dans le sous-réseau de l’architecture de référence.**

![Diagramme de l’architecture de référence d’application web IaaS pour PCI DSS](images/pcidss-iaaswa-architecture.png "Diagramme de l’architecture de référence d’application web IaaS pour PCI DSS")

Cette solution utilise les services Azure suivants. Les informations détaillées concernant l’architecture de déploiement se trouvent dans la section [Architecture de déploiement](#deployment-architecture).

- Groupes à haute disponibilité
    - (1) Contrôleurs de domaine Active Directory
    - (1) Nœuds de cluster SQL
    - (1) Web/IIS
- Azure Active Directory
- Azure Application Gateway
    - (1) Pare-feu d’applications web
        - Mode de pare-feu : prévention
        - Ensemble de règles : OWASP 3.0
        - Écouteur : 443
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Stockage Azure
    - (7) Comptes de stockage géoredondant
- Machines virtuelles Azure
    - (1) Gestion/bastion (Windows Server 2016 Datacenter)
    - (2) Contrôleur de domaine Active Directory (Windows Server 2016 Datacenter)
    - (2) Nœud de cluster SQL Server (SQL Server 2017 sur Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Réseau virtuel Azure
    - (1) réseau /16
    - (5) réseaux /24
    - (5) groupes de sécurité réseau
- Témoin cloud
- Coffre Recovery Services

## <a name="deployment-architecture"></a>Architecture de déploiement

La section ci-après décrit en détail les éléments nécessaires au déploiement et à l’implémentation.

**Hôte bastion** : point d’entrée unique qui permet aux utilisateurs d’accéder aux ressources déployées dans cet environnement. L’hôte bastion fournit une connexion sécurisée à des ressources déployées en autorisant uniquement le trafic distant provenant d’adresses IP publiques figurant sur une liste verte. Pour autoriser le trafic RDP (Remote Desktop Protocol), la source du trafic doit être définie dans le groupe de sécurité réseau.

Cette solution crée une machine virtuelle en tant qu’hôte bastion joint au domaine avec les configurations suivantes :
-   [Extension Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Extension Diagnostics Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) avec Azure Key Vault
-   [Stratégie d’arrêt automatique](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) pour réduire la consommation des ressources de machine virtuelle non utilisées
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) activé afin que les informations d’identification et autres secrets soient utilisés dans un environnement protégé, isolé du système d’exploitation en cours d’exécution

### <a name="virtual-network"></a>Réseau virtuel

L’architecture définit un réseau privé virtuel avec l’espace d’adressage 10.200.0.0/16.

**Groupes de sécurité réseau** : Cette solution déploie des ressources dans une architecture où figurent un sous-réseau web, un sous-réseau de base de données, un sous-réseau Active Directory et un sous-réseau de gestion à l’intérieur d’un réseau virtuel. Des règles de groupe de sécurité réseau appliquées aux différents sous-réseaux permettent de séparer ces derniers logiquement afin de limiter le trafic entre eux au seul trafic nécessaire pour les fonctionnalités système et de gestion.

Consultez la configuration des [groupes de sécurité réseau](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) déployés avec cette solution. Les organisations peuvent configurer des groupes de sécurité réseau en modifiant le fichier ci-dessus et en suivant [cette documentation](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).

Chaque sous-réseau dispose d’un groupe de sécurité réseau dédié :
- 1 groupe de sécurité réseau pour Application Gateway (LBNSG)
- 1 groupe de sécurité réseau pour le bastion (MGTNSG)
- 1 groupe de sécurité réseau pour les contrôleurs de domaine principaux et secondaires (ADNSG)
- 1 groupe de sécurité réseau pour les serveurs SQL Server et le Témoin cloud (SQLNSG)
- 1 groupe de sécurité réseau pour la couche web (WEBNSG)

### <a name="data-in-transit"></a>Données en transit

Par défaut, Azure chiffre toutes les communications avec les centres de données Azure. De plus, toutes les transactions en direction du Stockage Azure passant par le portail Azure se produisent via HTTPS.

### <a name="data-at-rest"></a>Données au repos

L’architecture protège les données au repos à l’aide de diverses mesures, dont le chiffrement et l’audit de base de données.

**Stockage Azure** : pour répondre aux exigences du chiffrement des données au repos, l’ensemble du [Stockage Azure](https://azure.microsoft.com/services/storage/) utilise la fonctionnalité [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Cela permet de protéger et de sauvegarder les données de titulaire de carte dans le respect des engagements de l’organisation en matière de sécurité et des exigences de conformité définies par PCI DSS 3.2.

**Azure Disk Encryption** : Azure Disk Encryption permet de chiffrer les disques de machines virtuelles IaaS Windows. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) exploite la fonctionnalité BitLocker de Windows pour fournir le chiffrement des volumes des disques de données et du système d’exploitation. La solution est intégrée à Azure Key Vault pour faciliter le contrôle et la gestion des clés de chiffrement de disque.

**SQL Server** : l’instance SQL Server utilise les mesures suivantes pour la sécurité des bases de données :
- La solution [d’authentification et d’autorisation Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permet de gérer les identités des utilisateurs de bases de données et d’autres services Microsoft dans un emplacement central.
-   L’[audit Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) suit les événements de base de données et consigne ceux-ci dans un journal d’audit conservé dans un compte de stockage Azure.
-   SQL Database est configuré pour utiliser la technologie [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) qui assure le chiffrement et le déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux de transactions afin de protéger les informations au repos. Transparent Data Encryption protège les données de détenteur de carte contre les accès non autorisés.
-   Des [règles de pare-feu](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) empêchent tout accès aux serveurs de base de données sans autorisation appropriée. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande.
-   La [détection des menaces SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permet de détecter et traiter les menaces potentielles à mesure qu’elles surviennent en déclenchant des alertes de sécurité en cas d’activités de base de données suspectes, de vulnérabilités potentielles, d’attaques par injection de code SQL et de modèles d’accès anormaux aux bases de données.
-   Des [colonnes Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantissent que les données de détenteurs de cartes sensibles n’apparaissent jamais en tant que texte en clair à l’intérieur du système de base de données. Une fois le chiffrement des données activé, seules des applications clientes ou des serveurs d’applications ayant accès aux clés peuvent accéder aux données en texte clair.
- La fonctionnalité [Propriétés étendues](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) permet aux utilisateurs d’interrompre le traitement d’objets de données en ajoutant des propriétés personnalisées à ces objets et en balisant des données comme « Abandonnées » pour aider la logique d’application à empêcher le traitement des données de détenteur de carte associées.
- La [Sécurité au niveau des lignes](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permet aux utilisateurs de définir des stratégies de restriction d’accès aux données afin d’interrompre le traitement de celles-ci.
- [SQL Database Dynamic Data Masking](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limite l’exposition des données de détenteur de carte sensibles en masquant celles-ci aux utilisateurs ou aux applications dépourvus de privilèges. Le masquage dynamique des données peut découvrir automatiquement les données potentiellement sensibles et suggérer les masques appropriés à appliquer. Cela permet d’identifier et de réduire l’accès aux données de détenteur de carte afin que celles-ci ne quittent pas la base de données via un accès non autorisé. Les clients doivent ajuster les paramètres de masquage dynamique des données pour respecter leur schéma de base de données.

### <a name="identity-management"></a>Gestion des identités

Les technologies suivantes offrent des fonctionnalités de gestion de l’accès aux données dans l’environnement Azure :

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) est le répertoire cloud multi-locataire et le service de gestion des identités de Microsoft. Tous les utilisateurs de cette solution sont créés dans Azure Active Directory, notamment ceux qui accèdent à Azure SQL Database.
- L’authentification auprès de l’application est effectuée à l’aide d’Azure Active Directory. Pour plus d’informations, consultez [Intégration d’applications à Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). En outre, le chiffrement des colonnes de base de données utilise Azure Active Directory pour authentifier l’application auprès d’Azure SQL Database. Pour plus d’informations, découvrez comment [protéger des données sensibles dans Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Le contrôle d’accès en fonction du rôle Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permet aux administrateurs de définir des autorisations affinées pour accorder uniquement le nombre d’accès dont les utilisateurs ont besoin pour leur travail. Au lieu d’accorder à tous les utilisateurs des autorisations illimitées sur les ressources Azure, les administrateurs peuvent autoriser seulement certaines actions pour accéder aux données. Par exemple, l’accès aux abonnements est limité à l’administrateur d’abonnements.
- La solution [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) permet aux clients de minimiser le nombre d’utilisateurs qui ont accès à des informations spécifiques. Les administrateurs peuvent utiliser Azure Active Directory Privileged Identity Management pour découvrir, restreindre et superviser les identités privilégiées et leur accès aux ressources. Cette fonctionnalité permet également d’appliquer un accès administratif juste-à-temps à la demande si nécessaire.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) détecte les vulnérabilités risquant d’affecter les identités d’une organisation, configure des réponses automatiques aux actions suspectes détectées en lien avec les identités d’une organisation, et examine les incidents suspects afin de prendre les mesures appropriées pour résoudre ces derniers.

### <a name="security"></a>Sécurité

**Gestion des secrets** : la solution utilise [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pour gérer les clés et les secrets. Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. Les fonctionnalités Azure Key Vault ci-après aident les clients à protéger les données et à y accéder :

- Les stratégies d’accès avancées sont configurées en fonction des besoins.
- Les stratégies d’accès Key Vault sont définies avec des autorisations minimales requises pour les clés et les secrets.
- L’ensemble des clés et secrets figurant dans Key Vault présentent des dates d’expiration.
- Toutes les clés dans Key Vault sont protégées par des modules de sécurité matériels spécialisés. La clé est de type RSA 2 048 bits protégé par HSM.
- Des autorisations minimales requises sont accordées à tous les utilisateurs et à toutes les identités à l’aide du contrôle d’accès en fonction rôle.
- Les journaux de diagnostics pour Key Vault sont activés avec une période de rétention d’au moins 365 jours.
- Les opérations de chiffrement autorisées pour les clés sont restreintes à celles qui sont nécessaires.
- La solution est intégrée à Azure Key Vault pour gérer les secrets et clés de chiffrement de disque de machine virtuelle IaaS.

**Gestion des correctifs** : les machines virtuelles Windows déployées dans le cadre de cette architecture de référence sont configurées par défaut pour recevoir des mises à jour automatiques du service Windows Update. Cette solution inclut également le service [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) qui permet de créer des déploiements mis à jour pour corriger des machines virtuelles si nécessaire.

**Protection contre les programmes malveillants** : [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) pour Machines Virtuelles fournit une protection en temps réel qui permet d’identifier et de supprimer les virus, les logiciels espions et autres logiciels malveillants grâce à des alertes configurables vous avertissant quand des logiciels malveillants ou indésirables connus tentent de s’installer ou de s’exécuter sur des machines virtuelles protégées.

**Azure Security Center** : avec [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), les clients peuvent appliquer et gérer de façon centralisée des stratégies de sécurité sur les charges de travail, limiter l’exposition aux menaces ainsi que détecter les attaques et y répondre. Par ailleurs, Azure Security Center accède aux configurations existantes des services Azure pour fournir des recommandations en matière de configuration et de service. Cela contribue à l’amélioration du dispositif de sécurité et à la protection des données.

Azure Security Center utilise diverses fonctionnalités de détection pour avertir les clients des attaques potentielles qui ciblent leur environnement. Ces alertes fournissent de précieuses informations sur le déclencheur de l’alerte, les ressources ciblées et la source de l’attaque. Azure Security Center a un ensemble [d’alertes de sécurité prédéfinies](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), qui sont déclenchées en cas de menace ou d’activité suspecte. Les [règles d’alerte personnalisées](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) d’Azure Security Center permettent aux clients de définir de nouvelles alertes de sécurité basées sur les données déjà collectées dans leur environnement.

Azure Security Center fournit des incidents et des alertes de sécurité hiérarchisés, ce qui permet aux clients de découvrir et de résoudre plus facilement les problèmes de sécurité potentiels. Un [rapport de Threat intelligence](https://docs.microsoft.com/azure/security-center/security-center-threat-report) est généré pour chaque menace détectée afin d’aider les équipes chargées de répondre aux incidents à examiner et à contrecarrer les menaces.

**Azure Application Gateway** : l’architecture réduit le risque de failles de sécurité en utilisant une passerelle d’application avec un pare-feu d’applications web Azure configuré et l’ensemble de règles OWASP activé. Les autres fonctionnalités incluent notamment :

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Activation du [déchargement SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Désactivation de [TLS versions 1.0 et 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Pare-feu d’applications web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (mode de prévention)
- [Mode Prévention](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) avec l’ensemble de règles OWASP 3.0
- Activation de la [journalisation des diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Sondes d’intégrité personnalisées](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) et [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fournissent une protection supplémentaire et des notifications. Azure Security Center fournit également un système de réputation.

### <a name="business-continuity"></a>Continuité de l’activité

**Haute disponibilité** : La solution déploie toutes les machines virtuelles sur un [groupe à haute disponibilité](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Les groupes à haute disponibilité veillent à ce que les machines virtuelles soient distribuées sur plusieurs clusters matériels isolés pour améliorer la disponibilité. Au moins une machine virtuelle est disponible pendant un événement de maintenance planifié ou non, ce qui est conforme au contrat de niveau de service Azure garantissant une disponibilité de 99,95 %.

**Coffre Recovery Services** : le [coffre Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) héberge les données de sauvegarde et protège toutes les configurations des machines virtuelles Azure dans cette architecture. Grâce au coffre Recovery Services, les clients peuvent restaurer des fichiers et dossiers d’une machine virtuelle IaaS sans avoir à restaurer l’intégralité de celle-ci, ce qui permet d’accélérer les temps de restauration.

**Témoin cloud** : le [Témoin cloud](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) est un type de témoin de quorum de cluster de basculement dans Windows Server 2016, qui utilise Azure comme point d’arbitrage. Comme n’importe quel autre témoin de quorum, le Témoin cloud obtient un vote et peut être utilisé dans les calculs de quorum, mais il utilise le stockage Blob Azure standard disponible publiquement. Cela élimine le travail de maintenance supplémentaire des machines virtuelles hébergées dans un cloud public.

### <a name="logging-and-auditing"></a>Journalisation et audit

Les services Azure assurent une journalisation complète de l’activité du système et des utilisateurs, ainsi que de l’intégrité du système :
- **Journaux d’activité** : les [journaux d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fournissent des insights sur les opérations ayant été effectuées sur les ressources d’un abonnement. Les journaux d’activité peuvent aider à déterminer l’initiateur, l’heure d’exécution et l’état d’une opération.
- **Journaux de diagnostic** : les [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluent l’ensemble des journaux générés par chaque ressource. Ils incluent les journaux des événements système de Windows, les journaux d’activité de Stockage Azure, les journaux d’audit du Key Vault, ainsi que les journaux d’activité de pare-feu et d’accès d’Application Gateway. Tous les journaux de diagnostic sont consignés dans un compte de stockage Azure centralisé et chiffré pour l’archivage. L’utilisateur peut configurer la rétention jusqu’à 730 jours pour répondre aux exigences de rétention spécifiques de l’entreprise.

**Journaux Azure Monitor** : Ces journaux sont consolidés dans [Azure Monitor enregistre](https://azure.microsoft.com/services/log-analytics/) pour le traitement, le stockage et la création de rapports de tableau de bord. Une fois collectées, les données sont organisées dans différentes tables en fonction du type de données dans des espaces de travail Log Analytics. Toutes les données sont ainsi analysées ensemble, quelle que soit leur source d’origine. En outre, Azure Security Center s’intègre avec les journaux Azure Monitor permet aux clients d’utiliser des requêtes de Kusto pour accéder à leurs données d’événement de sécurité et de les combiner avec des données provenant d’autres services.

Azure suivant [solutions de surveillance](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) sont inclus dans le cadre de cette architecture :
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment) : la solution Active Directory Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit une liste hiérarchisée de suggestions spécifiques pour l’infrastructure de serveur déployée.
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment) : la solution SQL Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit aux clients une liste hiérarchisée de suggestions spécifiques pour l’infrastructure de serveur déployée.
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth) : cette solution de contrôle d’intégrité des agents indique le nombre d’agents déployés et leur répartition géographique, ainsi que le nombre d’agents qui ne répondent pas ou qui envoient des données opérationnelles.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity) : la solution Activity Log Analytics facilite l’analyse des journaux d’activité Azure de tous les abonnements Azure d’un client.

**Azure Automation** : [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) stocke, exécute et gère les runbooks. Dans cette solution, les runbooks aident à collecter les journaux d’activité d’Azure SQL Database. La solution Automation [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) permet aux clients d’identifier facilement les changements dans l’environnement.

**Azure Monitor** : [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) aide les utilisateurs à suivre les performances, garantir la sécurité et identifier les tendances en permettant aux organisations d’auditer, de créer des alertes et d’archiver des données, y compris le suivi des appels d’API dans leurs ressources Azure.

## <a name="threat-model"></a>Modèle de menace

Le diagramme de flux de données (DFD) pour cette architecture de référence figure ci-dessous et est également disponible en [téléchargement](https://aka.ms/pcidss-iaaswa-tm). Ce modèle peut aider les clients à comprendre les points de risque potentiel de l’infrastructure du système lors de l’apport de modifications.

![Diagramme de l’architecture de référence d’application web IaaS pour PCI DSS](images/pcidss-iaaswa-threat-model.png "Diagramme de l’architecture de référence d’application web IaaS pour PCI DSS")

## <a name="compliance-documentation"></a>Documentation sur la conformité

Le [Blueprint de sécurité et de conformité Azure – Matrice de responsabilités des clients pour PCI DSS](https://aka.ms/pcidss-crm) répertorie les responsabilités pour toutes les exigences de PCI DSS 3.2.

Le [Blueprint de sécurité et de conformité Azure - Matrice d’implémentation d’application web IaaS pour PCI DSS](https://aka.ms/pcidss-iaaswa-cim) fournit des informations sur les exigences de PCI DSS 3.2 que prend en compte l’architecture d’application web IaaS, notamment une description détaillée de la façon dont l’implémentation répond aux exigences de chaque article traité.

## <a name="guidance-and-recommendations"></a>Instructions et recommandations

### <a name="vpn-and-expressroute"></a>VPN et ExpressRoute

Un tunnel VPN sécurisé ou un routage [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) doivent être configurés pour établir une connexion sécurisée aux ressources déployées en lien avec cette architecture de référence d’application web IaaS. En configurant correctement un VPN ou un routage ExpressRoute, les clients peuvent ajouter une couche de protection des données en transit.

En implémentant un tunnel VPN sécurisé avec Azure, il est possible de créer une connexion privée virtuelle entre un réseau local et un réseau virtuel Azure. Cette connexion via Internet permet aux clients de « tunneler » des informations en toute sécurité par le biais d’une liaison chiffrée entre leur réseau et Azure. La technologie de réseau privé virtuel de site à site, aussi sécurisée qu’éprouvée, est déployée par des entreprises de toutes tailles depuis des décennies. Le [mode tunnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) est utilisé dans cette option comme un mécanisme de chiffrement.

Étant donné que le trafic à l’intérieur du tunnel VPN transite par Internet avec un VPN de site à site, Microsoft offre une autre option de connexion encore plus sécurisée. Azure ExpressRoute est une liaison réseau étendu dédiée entre Azure et un emplacement local ou un fournisseur d’hébergement Exchange. Les connexions ExpressRoute ne transitent pas par Internet, et offrent de meilleurs niveaux de fiabilité, de rapidité, de latence et de sécurité que les connexions classiques via Internet. En outre, comme il s’agit d’une connexion directe du fournisseur de télécommunications du client, les données ne circulent pas sur Internet et n’y sont donc pas exposées.

Les meilleures pratiques pour l’implémentation d’un réseau hybride sécurisé qui étend un réseau local à Azure sont [disponibles ici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Clause d'exclusion de responsabilité

- Ce document est fourni à titre d’information uniquement. MICROSOFT N’ACCORDE AUCUNE GARANTIE EXPRESSE, IMPLICITE OU STATUTAIRE EN LIEN AVEC LES INFORMATIONS CONTENUES DANS CE DOCUMENT. Ce document est fourni « en l’état ». Les informations et les points de vue exprimés dans ce document, y compris les URL et autres références à des sites web, peuvent être modifiés sans préavis. Les clients qui lisent ce document assument les risques liés à son utilisation.
- Ce document n’accorde aux clients aucun droit légal de propriété intellectuelle sur les produits ou solutions Microsoft.
- Les clients peuvent copier et utiliser ce document pour un usage interne, à titre de référence.
- Certaines recommandations contenues dans ce document peuvent entraîner une augmentation des taux d’utilisation des données, des réseaux ou des ressources de calcul dans Azure, débouchant sur des coûts de licence ou d’abonnement supplémentaires.
- Cette architecture constitue une base que les clients peuvent modifier en fonction de leurs besoins. Elle ne doit pas être utilisée telle quelle dans un environnement de production.
- Développé en tant que référence, ce document ne doit pas être utilisé pour définir tous les moyens par lesquels un client peut satisfaire à des réglementations et exigences de conformité spécifiques. Les clients doivent rechercher une assistance juridique auprès de leur organisation pour connaître les implémentations client approuvées.
