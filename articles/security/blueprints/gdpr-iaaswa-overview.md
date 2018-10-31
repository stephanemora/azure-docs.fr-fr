---
title: Azure Blueprint Sécurité et conformité - Application web IaaS pour le RGPD
description: Azure Blueprint Sécurité et conformité - Application web IaaS pour le RGPD
services: security
author: jomolesk
ms.assetid: 04d5239c-fff0-4c2d-9379-1fa79ddbec78
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: 509c33f2774e2d16bb8a96c50d1fb53962578dee
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49409320"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-gdpr"></a>Azure Blueprint Sécurité et conformité - Application web IaaS pour le RGPD

## <a name="overview"></a>Vue d’ensemble
Le Règlement général sur la protection des données (RGPD) contient de nombreuses prescriptions concernant la collecte, le stockage et l’utilisation des informations personnelles, notamment la façon dont les organisations identifient et sécurisent les données personnelles, respectent les exigences de transparence, détectent et signalent les violations de données personnelles, et forment leur personnel, en particulier celui chargé de veiller sur la confidentialité des données. Le RGPD offre aux individus un plus grand contrôle sur leurs données personnelles, et impose de nombreuses obligations nouvelles aux organisations qui collectent, traitent ou analysent ces données. Le RGPD impose de nouvelles règles aux organisations qui proposent des biens et des services aux citoyens de l’Union européenne (UE), ou qui recueillent et analysent des données les concernant. Le RGPD s’applique, quel que soit l’endroit où se trouvent les organisations.

Microsoft a conçu Azure en y intégrant des mesures de sécurité et des politiques de confidentialité de pointe pour protéger les données dans le cloud, notamment les catégories de données personnelles visées par le RGPD. Les [clauses contractuelles](http://aka.ms/Online-Services-Terms) de Microsoft contraignent l’entreprise à respecter certaines exigences applicables aux processeurs.

Cet Azure Blueprint Sécurité et conformité fournit des conseils pour déployer un environnement d’infrastructure en tant que service (IaaS, Infrastructure-as-a-Service) adapté pour une application web simple accessible sur Internet. Cette solution montre aux clients comment respecter les exigences de sécurité et de conformité spécifiques du RGPD, et leur sert de base pour construire et configurer leurs propres solutions applicatives web IaaS dans Azure. Les clients peuvent utiliser cette architecture de référence et suivre le [processus en quatre étapes](https://aka.ms/gdprebook) préconisé par Microsoft pour leur parcours vers la mise en conformité avec le RGPD :
1. Découvrir : identifier les données personnelles existantes, ainsi que l’emplacement où elles résident.
2. Gérer : régir la manière dont les données personnelles peuvent être consultées et utilisées.
3. Protéger : établir des contrôles de sécurité pour prévenir, détecter et traiter les vulnérabilités et les violations de données.
4. Rapporter : conserver la documentation requise et gérer les demandes de données ainsi que les notifications de violation.

Cette architecture de référence, ainsi que le guide de mise en œuvre et le modèle de menace associés, sont destinés à servir de base aux clients qui doivent les adapter à leurs besoins spécifiques et non les utiliser tels quels en environnement de production. Notez les points suivants :
- L’architecture constitue un point de départ pour aider les clients à déployer des charges de travail sur Azure d’une manière conforme au RGPD.
- Il incombe aux clients de d’évaluer de façon appropriée la sécurité et la conformité de toute solution basée sur cette architecture, car les exigences peuvent varier en fonction des spécificités de son implémentation.

## <a name="architecture-diagram-and-components"></a>Diagramme et composants de l’architecture
Cette solution déploie une architecture de référence pour une application web IaaS avec un serveur principal SQL Server. L’architecture inclut une couche web, une couche Données, une infrastructure Active Directory, une passerelle d’application (Application Gateway) et un équilibreur de charge (Load Balancer). Les machines virtuelles déployées sur les couches web et données sont configurées dans un groupe à haute disponibilité, tandis que les instances de SQL Server sont configurées dans un groupe de disponibilité AlwaysOn pour une haute disponibilité. Les machines virtuelles sont jointes au domaine, et des stratégies de groupe Active Directory sont utilisées pour appliquer des configurations de sécurité et de conformité au niveau du système d’exploitation. Un hôte bastion de gestion fournit une connexion sécurisée permettant aux administrateurs d’accéder aux ressources déployées. **Azure recommande de configurer une connexion VPN ou ExpressRoute pour la gestion et l’importation de données dans le sous-réseau de l’architecture de référence.**

![Diagramme de l’architecture de référence de l’application web IaaS pour le RGPD](images/gdpr-iaaswa-architecture.png?raw=true "Diagramme de l’architecture de référence de l’application web IaaS pour le RGPD")

Cette solution utilise les services Azure suivants. Les informations détaillées concernant l’architecture de déploiement se trouvent dans la section [Architecture de déploiement](#deployment-architecture).

- Machines virtuelles Azure
    - (1) Gestion/bastion (Windows Server 2016 Datacenter)
    - (2) Contrôleur de domaine Active Directory (Windows Server 2016 Datacenter)
    - (2) Nœud de cluster SQL Server (SQL Server 2017 sur Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Groupes à haute disponibilité
    - (1) Contrôleurs de domaine Active Directory
    - (1) Nœuds de cluster SQL
    - (1) Web/IIS
- Réseau virtuel Azure
    - (1) /16 réseaux virtuels
    - (5) /24 sous-réseaux
    - Les paramètres DNS sont définis sur les deux contrôleurs de domaine
- Azure Load Balancer
- Azure Application Gateway
    - (1) WAF Application Gateway activé
        - mode de pare-feu : prévention
        - ensemble de règles : OWASP 3.0
        - écouteur : port 443
- Stockage Azure
    - (7) Comptes de stockage géoredondant
- Témoin cloud
- Coffre Recovery Services
- Azure Key Vault
- Azure Active Directory (AAD)
- Azure Resource Manager
- Log Analytics
- Azure Security Center

## <a name="deployment-architecture"></a>Architecture de déploiement
La section ci-après décrit en détail les éléments nécessaires au déploiement et à l’implémentation.

**Hôte bastion** : point d’entrée unique qui permet aux utilisateurs d’accéder aux ressources déployées dans cet environnement. L’hôte bastion fournit une connexion sécurisée à des ressources déployées en autorisant uniquement le trafic distant à partir d’adresses IP publiques figurant sur une liste verte. Pour autoriser le trafic RDP (Remote Desktop Protocol), la source du trafic doit être définie dans le groupe de sécurité réseau (NSG).

Cette solution crée une machine virtuelle en tant qu’hôte bastion joint au domaine avec les configurations suivantes :
-   [Extension Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Extension Log Analytics](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Extension Diagnostics Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) avec Azure Key Vault
-   [Stratégie d’arrêt automatique](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) pour réduire la consommation des ressources de machine virtuelle non utilisées
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) activé afin que les informations d’identification et autres secrets soient utilisés dans un environnement protégé, isolé du système d’exploitation en cours d’exécution

### <a name="virtual-network"></a>Réseau virtuel
L’architecture définit un réseau privé virtuel avec l’espace d’adressage 10.200.0.0/16.

**Groupes de sécurité réseau** : cette solution déploie des ressources dans une architecture où figurent un sous-réseau web, un sous-réseau de base de données, un sous-réseau Active Directory et un sous-réseau de gestion à l’intérieur d’un réseau virtuel. Des règles de groupe de sécurité réseau appliquées aux différents sous-réseaux permettent de séparer ces derniers logiquement afin de limiter le trafic entre eux au seul trafic nécessaire pour les fonctionnalités système et de gestion.

Consultez la configuration des [groupes de sécurité réseau](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) déployés avec cette solution. Les organisations peuvent configurer des groupes de sécurité réseau en modifiant le fichier ci-dessus et en suivant [cette documentation](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).

Chaque sous-réseau dispose d’un groupe de sécurité réseau (NSG) dédié :
- 1 NSG pour la passerelle d’application (LBNSG)
- 1 NSG pour l’hôte bastion (MGTNSG)
- 1 NSG pour les contrôleurs de domaine principaux et secondaires (ADNSG)
- 1 NSG pour les serveurs SQL Server et le Témoin cloud (SQLNSG)
- 1 NSG pour la couche web (WEBNSG)

### <a name="data-in-transit"></a>Données en transit
Par défaut, Azure chiffre toutes les communications avec les centres de données Azure. De plus, toutes les transactions en direction du Stockage Azure passant par le portail Azure se produisent via HTTPS.

### <a name="data-at-rest"></a>Données au repos
L’architecture protège les données au repos à l’aide de diverses mesures, dont le chiffrement et l’audit de base de données.

**Stockage Azure** : pour répondre aux exigences de chiffrement des données au repos, l’ensemble du service [Stockage Azure](https://azure.microsoft.com/services/storage/) utilise la fonctionnalité de chiffrement [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Celui-ci permet de protéger et de sauvegarder les données personnelles dans le respect des engagements de l’organisation en matière de sécurité et des exigences de conformité définies par le RGPD.

**Azure Disk Encryption** : Azure Disk Encryption permet de chiffrer les disques de machines virtuelles IaaS Windows. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) exploite la fonctionnalité BitLocker de Windows pour fournir le chiffrement des volumes des disques de données et du système d’exploitation. La solution est intégrée à Azure Key Vault pour faciliter le contrôle et la gestion des clés de chiffrement de disque.

**SQL Server** : l’instance Azure SQL Database utilise les mesures suivantes pour la sécurité des bases de données :
-   La solution [d’authentification et d’autorisation Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permet la gestion des identités des utilisateurs de bases de données et d’autres services Microsoft dans un emplacement central.
-   L’[audit Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) suit les événements de base de données et consigne ceux-ci dans un journal d’audit conservé dans votre compte de stockage Azure.
-   SQL Database est configuré pour utiliser la technologie [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) qui assure le chiffrement et le déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux de transactions afin de protéger les informations au repos. TDE garantit que nul ne peut accéder sans autorisation aux données personnelles stockées.
-   Des [règles de pare-feu](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) empêchent tout accès aux serveurs de base de données sans autorisation appropriée. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande.
-   La [détection des menaces SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permet de détecter et traiter des menaces potentielles à mesure qu’elles surviennent en déclenchant des alertes de sécurité en lien avec des activités suspectes de base de données, des vulnérabilités potentielles, des attaques par injection de code SQL et des modèles d’accès anormaux aux bases de données.
-   Les [colonnes chiffrées par la fonctionnalité Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantissent que les données personnelles sensibles n’apparaissent jamais sous forme de texte en clair à l’intérieur du système de base de données. Une fois le chiffrement des données activé, seules des applications clientes ou des serveurs d’applications ayant accès aux clés peuvent accéder aux données en texte clair.
- La fonctionnalité [Propriétés étendues](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) permet aux utilisateurs d’interrompre le traitement d’objets de données en ajoutant des propriétés personnalisées à des objets de base de données et en marquant des données comme « Abandonnées » pour aider la logique d’application à empêcher le traitement de données personnelles associées.
- La [Sécurité au niveau des lignes](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permet aux utilisateurs de définir des stratégies de restriction d’accès aux données afin d’interrompre le traitement de celles-ci.
- [SQL Database Dynamic Data Masking (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limite l’exposition des données personnelles sensibles en masquant celles-ci aux utilisateurs ou aux applications dépourvus de privilèges. DDM peut détecter automatiquement les données potentiellement sensibles et suggérer les masques appropriés à appliquer. Cette fonctionnalité aide à identifier les données personnelles à protéger conformément au RGPD, ainsi qu’à réduire l’accès à ces données afin qu’elles ne puissent pas sortir de la base de données par le biais d’un accès non autorisé. **Remarque : les clients doivent ajuster les paramètres de DDM pour respecter leur schéma de base de données.**

### <a name="identity-management"></a>Gestion des identités
Les technologies suivantes fournissent des fonctionnalités pour gérer l’accès aux données personnelles dans l’environnement Azure :
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) est le service mutualisé basé sur le cloud de Microsoft pour la gestion des annuaires et des identités. Tous les utilisateurs de cette solution, y compris ceux qui accèdent à SQL Server, sont créés dans AAD.
-   L’authentification auprès de l’application est effectuée à l’aide d’AAD. Pour plus d’informations, consultez [Intégration d’applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). De plus, le chiffrement des colonnes de base de données utilise également AAD pour authentifier l’application auprès de SQL Server. Pour plus d’informations, découvrez comment [protéger des données sensibles dans SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   Le [contrôle d’accès en fonction du rôle (RBAC) Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permet aux administrateurs de définir des autorisations d’accès affinées pour n’accorder aux utilisateurs que l’accès dont ils ont besoin pour accomplir leur travail. Plutôt que d’attribuer à tous les utilisateurs des autorisations d’accès illimitées aux ressources Azure, les administrateurs peuvent n’autoriser que certaines actions d’accès aux données personnelles. L’accès aux abonnements est limité à l’administrateur d’abonnements.
- La solution [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) permet aux clients de minimiser le nombre d’utilisateurs qui ont accès à certaines ressources.  Les administrateurs peuvent utiliser Azure Active Directory Privileged Identity Management pour découvrir, restreindre et surveiller les identités privilégiées et leur accès aux ressources. Cette fonctionnalité permet également d’appliquer un accès administratif juste-à-temps à la demande si nécessaire.
- La solution [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) détecte les vulnérabilités risquant d’affecter les identités d’une organisation, configure des réponses automatiques aux actions suspectes détectées en lien avec les identités d’une organisation, et examine les incidents suspects afin de prendre les mesures appropriées pour résoudre ces derniers.
- Une instance Active Directory IaaS déployée fournit la gestion des identités au niveau du système d’exploitation pour les machines virtuelles IaaS déployées.

### <a name="security"></a>Sécurité
**Gestion des secrets** : la solution utilise [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pour gérer les clés et les secrets. Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. Les fonctionnalités suivantes d’Azure Key Vault aident les clients à protéger les données personnelles et l’accès à celles-ci :
- Les stratégies d’accès avancées sont configurées en fonction des besoins.
- Les stratégies d’accès Key Vault sont définies avec des autorisations minimales requises pour les clés et les secrets.
- L’ensemble des clés et secrets figurant dans Key Vault présentent des dates d’expiration.
- Toutes les clés stockées dans Key Vault sont protégées par des modules de sécurité matériels (HSM) spécialisés. La clé est de type RSA 2 048 bits protégé par HSM.
- Les autorisations minimales requises sont accordées à l’ensemble des utilisateurs et identités à l’aide d’un contrôle d’accès en fonction du rôle (RBAC).
- Les journaux de diagnostics pour Key Vault sont activés avec une période de rétention d’au moins 365 jours.
- Les opérations de chiffrement autorisées pour les clés sont restreintes à celles qui sont nécessaires.
- La solution est intégrée à Azure Key Vault pour gérer les secrets et clés de chiffrement de disque de machine virtuelle IaaS.

**Gestion des correctifs** : les machines virtuelles Windows déployées dans le cadre de cette architecture de référence sont configurées par défaut pour recevoir des mises à jour automatiques du service Windows Update. Cette solution inclut également le service [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro), qui permet de créer des déploiements de mises à jour pour corriger des machines virtuelles si nécessaire.

**Protection contre les programmes malveillants** : [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) pour Machines Virtuelles fournit une protection en temps réel qui permet d’identifier et de supprimer des virus, des logiciels espions et d’autres logiciels malveillants grâce à des alertes configurables vous avertissant quand des logiciels malveillants ou indésirables connus tentent de s’installer ou de s’exécuter sur des machines virtuelles protégées.

**Alertes de sécurité** : [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) permet aux clients de surveiller le trafic, de collecter des journaux et d’analyser les sources de données pour y rechercher d’éventuelles menaces. En outre, Azure Security Center accède à une configuration existante des services Azure pour fournir des recommandations en matière de configuration et de service afin de contribuer à l’amélioration de la situation de sécurité et à la protection des données personnelles. Azure Security Center génère un [rapport d’intelligence des menaces](https://docs.microsoft.com/azure/security-center/security-center-threat-report) pour chaque menace détectée afin d’aider les équipes chargées de répondre aux incidents à examiner et à contrecarrer les menaces.

**Application Gateway** : l’architecture réduit le risque de failles de sécurité à l’aide d’Application Gateway avec le pare-feu d’applications web (WAF) et l’ensemble de règles OWASP activé. Les autres fonctionnalités incluent notamment :

- [SSL de bout en bout](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Activation du [déchargement SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Désactivation de [TLS versions 1.0 et 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Pare-feu d’application web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (mode WAF)
- [Mode Prévention](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) avec l’ensemble de règles OWASP 3.0
- Activation de la [journalisation des diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Sondes d’intégrité personnalisées](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) et [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fournissent une protection supplémentaire et des notifications. Azure Security Center fournit également un système de réputation.

### <a name="business-continuity"></a>Continuité de l’activité

**Haute disponibilité** : la solution déploie toutes les machines virtuelles sur un [groupe à haute disponibilité](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Les groupes à haute disponibilité veillent à ce que les machines virtuelles soient distribuées sur plusieurs clusters matériels isolés pour améliorer la disponibilité. Au moins une machine virtuelle est disponible pendant un événement de maintenance planifié ou non, ce qui est conforme au contrat de niveau de service Azure garantissant une disponibilité de 99,95 %.

**Coffre Recovery Services** : le [coffre Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) héberge les données de sauvegarde et protège toutes les configurations des machines virtuelles Azure dans cette architecture. Grâce au coffre Recovery Services, les clients peuvent restaurer des fichiers et dossiers d’une machine virtuelle IaaS sans avoir à restaurer l’intégralité de celle-ci, ce qui permet d’accélérer les temps de restauration.

**Témoin cloud** : le [Témoin cloud](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) est un type de témoin de quorum de cluster de basculement dans Windows Server 2016, qui utilise Azure comme point d’arbitrage. Comme n’importe quel autre témoin de quorum, le Témoin cloud obtient un vote et peut être utilisé dans les calculs de quorum, mais il utilise le stockage Blob Azure standard disponible publiquement. Cela élimine le travail de maintenance supplémentaire des machines virtuelles hébergées dans un cloud public.

### <a name="logging-and-auditing"></a>Journalisation et audit

Log Analytics fournit une journalisation complète de l’activité système et utilisateur, ainsi que de l’intégrité du système. La solution [Log Analytics](https://azure.microsoft.com/services/log-analytics/) collecte et analyse les données générées par les ressources dans les environnements Azure et locaux.
- **Journaux d’activité :** les [journaux d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fournissent des informations sur les opérations effectuées sur les ressources d’un abonnement. Les journaux d’activité peuvent aider à déterminer l’initiateur, l’heure d’exécution et l’état d’une opération.
- **Journaux de diagnostic :** les [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) sont tous les journaux émis par toutes les ressources. Ils incluent les journaux des événements système de Windows, les journaux de Stockage Azure, les journaux d’audit du Key Vault, ainsi que les journaux de pare-feu et d’accès d’Application Gateway.
- **Archivage des journaux** : tous les journaux de diagnostic sont consignés sur un compte de stockage Azure centralisé et chiffré à des fins d’archivage. L’utilisateur peut configurer la rétention jusqu’à 730 jours pour répondre aux exigences de rétention spécifiques de l’organisation. Ces journaux se connectent à Azure Log Analytics à des fins de traitement, de stockage et de génération de rapports de tableau de bord.

Les solutions de surveillance suivantes sont également incluses dans cette architecture :
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment) : la solution Active Directory Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit une liste hiérarchisée de recommandations spécifiques pour l’infrastructure de serveur déployée.
-   [Antimalware Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware) : cette solution signale les programmes malveillants, les menaces et l’état de protection.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) : cette solution stocke, exécute et gère les runbooks.
-   [Security and Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) : le tableau de bord Security and Audit donne une vue d’ensemble de l’état de sécurité des ressources en affichant des métriques concernant des domaines de sécurité, des problèmes notables, des détections, des menaces et des requêtes de sécurité courantes.
-   [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment) : la solution SQL Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit aux clients une liste hiérarchisée de recommandations spécifiques pour l’infrastructure de serveur déployée.
-   [Update Management](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management) : cette solution permet au client de gérer les mises à jour de sécurité du système d’exploitation, notamment en lui indiquant l’état des mises à jour disponibles et le processus d’installation des mises à jour requises.
-   [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth) : cette solution indique le nombre d’agents déployés et leur répartition géographique, ainsi que le nombre d’agents qui ne répondent pas ou qui envoient des données opérationnelles.
-   [Journaux d’activité Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity) : la solution Activity Log Analytics facilite l’analyse des journaux d’activité Azure de tous les abonnements Azure d’un client.
-   [Change Tracking](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity) : la solution de suivi des modifications permet aux clients d’identifier facilement des modifications dans l’environnement.

## <a name="threat-model"></a>Modèle de menace

Le diagramme de flux de données (DFD) pour cette architecture de référence figure ci-dessous et est également disponible en [téléchargement](https://aka.ms/gdprIaaSdfd). Ce modèle peut aider les clients à comprendre les points de risque potentiel de l’infrastructure du système lors de l’apport de modifications.

![Application web IaaS pour le modèle de menace du RGPD](images/gdpr-iaaswa-threat-model.png?raw=true "Application web IaaS pour le modèle de menace du RGPD")

## <a name="compliance-documentation"></a>Documentation sur la conformité

L’[Azure Blueprint Sécurité et conformité - Matrice de responsabilités des clients en lien avec le RGPD](https://aka.ms/gdprCRM) répertorie les responsabilités du contrôleur et du processeur pour tous les articles du RGPD. Notez qu’en règle générale, pour les services Azure, un client est le contrôleur et Microsoft le processeur.

L’[Azure Blueprint Sécurité et conformité - Matrice d’implémentation d’application web IaaS en lien avec le RGPD](https://aka.ms/gdprIaaSweb) fournit des informations sur les articles du RGPD que prend en compte l’architecture d’application web IaaS, dont une description détaillée de la façon dont l’implémentation répond aux exigences de chaque article couvert.

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
