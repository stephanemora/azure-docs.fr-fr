---
title: 'Blueprint de sécurité et de conformité Azure : application web IaaS pour Australia PROTECTED'
description: 'Blueprint de sécurité et de conformité Azure : application web IaaS pour Australia PROTECTED'
services: security
author: meladie
ms.assetid: f53a25c4-1c75-42d6-a0e7-a91661673891
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: 50e410fc439be7b3a5f4c1e8d4bab5d60c3c4f52
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946924"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-australia-protected"></a>Blueprint de sécurité et de conformité Azure : application web IaaS pour Australia Protected

## <a name="overview"></a>Vue d'ensemble
Ce Blueprint de sécurité et de conformité Azure fournit des conseils pour le déploiement d’un environnement IaaS adapté à la collecte, au stockage et à la récupération des données du secteur public AU-PROTECTED, et conforme aux objectifs du manuel ISM (Information Security Manual) de l’administration australienne, produit par l’organisation ASD (Australian Signals Directorate). Ce blueprint présente une architecture de référence courante et montre une gestion appropriée des données sensibles du secteur public dans un environnement multiniveau sécurisé et conforme.

Cette architecture de référence, ce guide d’implémentation et ce modèle de menaces constituent une base permettant aux clients de procéder à leur propre processus de planification et d’accréditation du système, ce qui les aide à déployer des charges de travail sur Azure de façon conforme à ASD. Les clients peuvent choisir d’implémenter une passerelle VPN Azure ou ExpressRoute pour utiliser des services fédérés et pour intégrer des ressources locales à des ressources Azure. Les clients doivent prendre en compte les implications de l’utilisation de ressources locales sur la sécurité. Une configuration supplémentaire est nécessaire pour remplir toutes les conditions, car elles peuvent varier selon les spécificités de l’implémentation de chaque client.

La mise en conformité à ASD nécessite que le système soit audité par un auditeur ayant le titre de « Information Security Registered Assessor ». Il incombe aux clients d’évaluer de façon appropriée la sécurité et la conformité de toute solution basée sur cette architecture, car les exigences peuvent varier en fonction des spécificités de chaque implémentation.

## <a name="architecture-diagram-and-components"></a>Diagramme et composants de l’architecture
Cette solution déploie une architecture de référence pour une application web IaaS avec un serveur principal SQL Server. L’architecture inclut une couche web, une couche Données, une infrastructure Active Directory, une passerelle d’application (Application Gateway) et un équilibreur de charge (Load Balancer). Les machines virtuelles déployées sur les couches web et données sont configurées dans un groupe à haute disponibilité, tandis que les instances SQL Server sont configurées dans un groupe de disponibilité AlwaysOn pour une haute disponibilité. Les machines virtuelles sont jointes au domaine, et des stratégies de groupe Active Directory sont utilisées pour appliquer des configurations de sécurité et de conformité au niveau du système d’exploitation. Un hôte bastion de gestion fournit une connexion sécurisée permettant aux administrateurs d’accéder aux ressources déployées.

L’architecture peut fournir un environnement hybride sécurisé qui étend un réseau local sur Azure, ce qui permet aux utilisateurs professionnels d’accéder aux charges de travail web de façon sécurisée via le réseau local privé d’une organisation ou depuis Internet. Pour les solutions locales, le client est comptable et responsable de tous les aspects de la sécurité, de l’exploitation et de la conformité.

Les ressources Azure incluses dans cette solution peuvent se connecter à une installation de partage d’emplacement de réseau local ou de centre de données (par exemple CDC à Canberra) via un VPN IPSec avec la passerelle VPN Azure ou via ExpressRoute. La décision d’utiliser un réseau privé virtuel doit se prendre en fonction de la classification des données transmises et du chemin réseau. Les clients qui exécutent des charges de travail critiques à grande échelle avec les exigences du Big Data doivent envisager d’utiliser une architecture réseau hybride avec ExpressRoute pour la connexion d’un réseau privé aux services Azure. Pour plus d’informations sur les mécanismes de connexion à Azure, reportez-vous à la section des conseils et des recommandations.

La fédération avec Azure Active Directory doit être utilisée pour permettre aux utilisateurs de s’authentifier avec des informations d’identification locales et d’accéder à toutes les ressources dans le cloud avec une infrastructure de services de fédération Active Directory (AD FS). Les services de fédération Active Directory (AD FS) peuvent fournir des identités simplifiées et sécurisées, et des fonctionnalités web d’authentification unique pour cet environnement hybride. Pour plus d’informations sur la configuration d’Azure Active Directory, reportez-vous à la section des conseils et des recommandations.

La solution utilise des comptes de stockage Azure que les clients peuvent configurer pour utiliser Storage Service Encryption afin de maintenir la confidentialité des données au repos. Azure stocke trois copies des données dans la région sélectionnée par le client pour assurer la résilience. Les régions Azure sont déployées dans des paires de régions résilientes, et le stockage géoredondant garantit que les données sont répliquées dans la deuxième région avec trois copies également. Ceci empêche qu’un événement dommageable se produisant à l’emplacement principal des données du client entraîne une perte de données.

Pour renforcer la sécurité, toutes les ressources dans cette solution sont gérées sous forme de groupe de ressources dans Azure Resource Manager. Le contrôle d’accès en fonction du rôle Azure Active Directory est utilisé pour contrôler l’accès aux données déployées et aux clés dans Azure Key Vault. L’intégrité du système est surveillée dans Azure Security Center et Azure Monitor. Les clients configurent ces deux services de supervision pour la capture de journaux d’activité et la centralisation des informations concernant l’intégrité du système dans un tableau de bord facilement consultable. Azure Application Gateway est configuré comme pare-feu en mode prévention et impose un trafic TLS version 1.2 au minimum.

![Diagramme de l’architecture de référence d’application web IaaS pour AU-PROTECTED](images/au-protected-iaaswa-architecture.png?raw=true "Diagramme de l’architecture de référence d’application web IaaS pour AU-PROTECTED") 

Cette solution utilise les services Azure suivants. Pour plus d’informations, consultez la section [Architecture de déploiement](#deployment-architecture).

- Groupes à haute disponibilité
    - (1) Nœuds de cluster SQL
    - (1) Web/IIS
- Azure Active Directory
- Azure Application Gateway
    - (1) Pare-feu d’applications web
        - Mode de pare-feu : prévention
        - Ensemble de règles : OWASP 3.0
        - Écouteur : 443
- Azure Cloud Witness
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Journaux d’activité Azure Monitor
- Stockage Azure
- Machines virtuelles Azure
    - (1) Gestion/bastion (Windows Server 2016 Datacenter)
    - (2) Nœud de cluster SQL Server (SQL Server 2017 sur Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Réseau virtuel Azure
    - (4) Groupes de sécurité réseau
    - Azure Network Watcher
- Coffre Recovery Services

Ce blueprint contient des services Azure qui n’ont pas été certifiés par ACSC (Australian Cyber Security Centre) pour être utilisés au niveau de classification « Protected » (Protégé). Tous les services inclus dans cette architecture de référence ont été certifiés par ACSC au niveau DLM (Dissemination Limiting Markers). Microsoft recommande aux clients d’examiner les rapports de sécurité et d’audit publiés à propos de ces services Azure, et d’utiliser leur infrastructure de gestion des risques pour déterminer si le service Azure convient pour leur accréditation interne et l’utilisation au niveau de classification « Protected » (Protégé).

## <a name="deployment-architecture"></a>Architecture de déploiement
La section ci-après décrit en détail les éléments nécessaires au déploiement et à l’implémentation.

**Hôte bastion** : point d’entrée unique qui permet aux utilisateurs d’accéder aux ressources déployées dans cet environnement. L’hôte bastion fournit une connexion sécurisée à des ressources déployées en autorisant uniquement le trafic distant provenant d’adresses IP publiques figurant sur une liste verte. Pour autoriser le trafic RDP (Remote Desktop Protocol), la source du trafic doit être définie dans le groupe de sécurité réseau.

Cette solution crée une machine virtuelle en tant qu’hôte bastion joint au domaine avec les configurations suivantes :
-   [Extension Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Extension Diagnostics Azure](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) avec Azure Key Vault
-   [Stratégie d’arrêt automatique](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) pour réduire la consommation des ressources de machine virtuelle non utilisées

### <a name="virtual-network"></a>Réseau virtuel
L’architecture définit un réseau privé virtuel avec l’espace d’adressage 10.200.0.0/16.

**Groupes de sécurité réseau** : Cette solution déploie des ressources dans une architecture où figurent un sous-réseau web, un sous-réseau de base de données, un sous-réseau Active Directory et un sous-réseau de gestion à l’intérieur d’un réseau virtuel. Des règles de groupe de sécurité réseau appliquées aux différents sous-réseaux permettent de séparer ces derniers logiquement afin de limiter le trafic entre eux au seul trafic nécessaire pour les fonctionnalités système et de gestion.

Consultez la configuration des [groupes de sécurité réseau](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) déployés avec cette solution. Les organisations peuvent configurer des groupes de sécurité réseau en modifiant le fichier ci-dessus et en suivant [cette documentation](../../virtual-network/virtual-network-vnet-plan-design-arm.md).

Chaque sous-réseau a un groupe de sécurité réseau dédié :
- 1 groupe de sécurité réseau pour Application Gateway (LBNSG)
- 1 groupe de sécurité réseau pour le bastion (MGTNSG)
- 1 groupe de sécurité réseau pour les serveurs SQL Server et le Témoin cloud (SQLNSG)
- 1 groupe de sécurité réseau pour la couche web (WEBNSG)

### <a name="data-in-transit"></a>Données en transit
Par défaut, Azure chiffre toutes les communications avec les centres de données Azure. 

Pour les données « Protected » (Protégé) en transit depuis des réseaux détenus par le client, l’architecture utilise Internet ou ExpressRoute avec une passerelle VPN configurée pour IPSec.

De plus, toutes les transactions effectuées sur Azure par le biais du portail de gestion Azure se font via HTTPS avec TLS 1.2.

### <a name="data-at-rest"></a>Données au repos
L’architecture protège les données au repos à l’aide d’un chiffrement, d’un audit de base de données et d’autres mesures.

**Stockage Azure** : pour répondre aux exigences du chiffrement des données au repos, l’ensemble du [Stockage Azure](https://azure.microsoft.com/services/storage/) utilise la fonctionnalité [Storage Service Encryption](../../storage/common/storage-service-encryption.md). Ceci permet de protéger et de sauvegarder les données dans le respect des engagements de l’organisation en matière de sécurité et des exigences de conformité définies par le manuel ISM de l’administration australienne.

**Azure Disk Encryption** : [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) exploite la fonctionnalité BitLocker de Windows pour chiffrer les volumes des disques de données. La solution s’intègre à Azure Key Vault pour faciliter le contrôle et la gestion des clés de chiffrement de disque.

**SQL Server** : l’instance SQL Server utilise les mesures suivantes pour la sécurité des bases de données :
-   [L’audit SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) suit les événements de base de données et les écrit dans des journaux d’audit.
-   [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) effectue le chiffrement et le déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux de transactions afin de protéger les informations au repos. Transparent Data Encryption protège les données des accès non autorisés.
-   Des [règles de pare-feu](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) empêchent tout accès aux serveurs de base de données sans autorisation appropriée. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande.
-   Des [colonnes Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) garantissent que les données sensibles n’apparaissent jamais en tant que texte en clair à l’intérieur du système de base de données. Une fois le chiffrement des données activé, seules des applications clientes ou des serveurs d’applications ayant accès aux clés peuvent accéder aux données en texte clair.
- [Dynamic Data Masking](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) limite l’exposition des données sensibles en masquant celles-ci aux utilisateurs ou aux applications dépourvus de privilèges. Le masquage dynamique des données peut découvrir automatiquement les données potentiellement sensibles et suggérer les masques appropriés à appliquer. Ceci permet de réduire l’accès à ces données sensibles afin qu’elles ne quittent pas la base de données via un accès non autorisé. **Les clients doivent ajuster les paramètres de masquage dynamique des données pour respecter leur schéma de base de données.**

### <a name="identity-management"></a>Gestion des identités
Les clients peuvent utiliser les services de fédération Active Directory (AD FS) locaux pour la fédération avec [Azure Active Directory](https://azure.microsoft.com/services/active-directory/), qui est le service de gestion des annuaires et des identités cloud multilocataire de Microsoft. [Azure Active Directory Connect](../../active-directory/hybrid/whatis-hybrid-identity.md) intègre vos annuaires locaux à Azure Active Directory. Des comptes Azure Active Directory sont nécessaires pour tous les utilisateurs de cette solution. Avec l’authentification de fédération, les utilisateurs peuvent se connecter à Azure Active Directory et s’authentifier auprès des ressources Azure avec des informations d’identification locales.

De plus, les fonctionnalités Azure Active Directory suivantes aident à gérer l’accès aux données dans l’environnement Azure :
- L’authentification auprès de l’application est effectuée à l’aide d’Azure Active Directory. Pour plus d’informations, consultez [Intégration d’applications à Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).
- Le [contrôle d’accès en fonction du rôle Azure](../../role-based-access-control/role-assignments-portal.md) permet aux administrateurs de définir des autorisations d’accès affinées pour n’accorder aux utilisateurs que l’accès dont ils ont besoin pour accomplir leur travail. Au lieu d’accorder à tous les utilisateurs des autorisations illimitées sur les ressources Azure, les administrateurs peuvent autoriser seulement certaines actions pour accéder aux données. Par exemple, l’accès aux abonnements est limité à l’administrateur d’abonnements.
- La solution [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) permet aux clients de minimiser le nombre d’utilisateurs qui ont accès à des informations spécifiques. Les administrateurs peuvent utiliser Azure Active Directory Privileged Identity Management pour découvrir, restreindre et superviser les identités privilégiées et leur accès aux ressources. Cette fonctionnalité permet également d’appliquer un accès administratif juste-à-temps à la demande si nécessaire.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) détecte les vulnérabilités risquant d’affecter les identités d’une organisation, configure des réponses automatiques aux actions suspectes détectées en lien avec les identités d’une organisation, et examine les incidents suspects afin de prendre les mesures appropriées pour résoudre ces derniers.

**Azure Multi-Factor Authentication** : Pour protéger les identités, l’authentification multifacteur doit être implémentée. [Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) est une solution facile à utiliser, évolutive et fiable, qui fournit une deuxième méthode d’authentification permettant de protéger vos utilisateurs. Azure Multi-Factor Authentication utilise la puissance du cloud, et s’intègre à Active Directory local et à vos applications personnalisées. Cette protection est étendue aux scénarios critiques impliquant de grands volumes.

### <a name="security"></a>Sécurité
**Gestion des secrets** : la solution utilise [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pour gérer les clés et les secrets. Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. Les fonctionnalités Azure Key Vault ci-après aident les clients à protéger les données et à y accéder :

- Les stratégies d’accès avancées sont configurées en fonction des besoins.
- Les stratégies d’accès Key Vault sont définies avec des autorisations minimales requises pour les clés et les secrets.
- L’ensemble des clés et secrets figurant dans Key Vault présentent des dates d’expiration.
- Toutes les clés dans Key Vault sont protégées par des modules de sécurité matériels spécialisés. Le type de clé est une clé RSA de 2048 bits protégée par un module de sécurité matériel.
- Des autorisations minimales requises sont accordées à tous les utilisateurs et à toutes les identités à l’aide du contrôle d’accès en fonction rôle.
- Les journaux de diagnostics pour Key Vault sont activés avec une période de rétention d’au moins 365 jours.
- Les opérations de chiffrement autorisées pour les clés sont restreintes à celles qui sont nécessaires.
- La solution est intégrée à Azure Key Vault pour gérer les secrets et clés de chiffrement de disque de machine virtuelle IaaS.

**Gestion des correctifs** : les machines virtuelles Windows déployées dans le cadre de cette architecture de référence sont configurées par défaut pour recevoir des mises à jour automatiques du service Windows Update. Cette solution inclut également le service [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) qui permet de créer des déploiements mis à jour pour corriger des machines virtuelles si nécessaire.

**Protection contre les programmes malveillants** : [Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) pour Machines Virtuelles fournit une protection en temps réel qui permet d’identifier et de supprimer les virus, les logiciels espions et autres logiciels malveillants grâce à des alertes configurables vous avertissant quand des logiciels malveillants ou indésirables connus tentent de s’installer ou de s’exécuter sur des machines virtuelles protégées.

**Azure Security Center** : avec [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), les clients peuvent appliquer et gérer de façon centralisée des stratégies de sécurité sur les charges de travail, limiter l’exposition aux menaces ainsi que détecter les attaques et y répondre. Par ailleurs, Azure Security Center accède aux configurations existantes des services Azure pour fournir des recommandations en matière de configuration et de service. Cela contribue à l’amélioration du dispositif de sécurité et à la protection des données.

Azure Security Center utilise diverses fonctionnalités de détection pour avertir les clients des attaques potentielles qui ciblent leur environnement. Ces alertes fournissent de précieuses informations sur le déclencheur de l’alerte, les ressources ciblées et la source de l’attaque. Azure Security Center a un ensemble [d’alertes de sécurité prédéfinies](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), qui sont déclenchées en cas de menace ou d’activité suspecte. Les [règles d’alerte personnalisées](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) d’Azure Security Center permettent aux clients de définir de nouvelles alertes de sécurité basées sur les données déjà collectées dans leur environnement.

Azure Security Center fournit des incidents et des alertes de sécurité hiérarchisés, ce qui permet aux clients de découvrir et de résoudre plus facilement les problèmes de sécurité potentiels. Un [rapport d’intelligence des menaces](https://docs.microsoft.com/azure/security-center/security-center-threat-report) est généré pour chaque menace détectée afin d’aider les équipes chargées de traiter les incidents à examiner et à contrecarrer les menaces.

En outre, cette architecture de référence utilise [l’évaluation des vulnérabilités](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) dans Azure Security Center. Une fois sa configuration terminée, un agent partenaire (par ex. Qualys) commence à signaler les données de vulnérabilité à la plateforme de gestion du partenaire. La plateforme de gestion du partenaire fournit à son tour des données sur la vulnérabilité et la surveillance des données à Azure Security Center, permettant aux clients d’identifier rapidement les machines virtuelles vulnérables.

**Azure Application Gateway** : l’architecture réduit le risque de failles de sécurité en utilisant une passerelle d’application avec un pare-feu d’applications web Azure configuré et l’ensemble de règles OWASP activé. Les autres fonctionnalités incluent notamment :

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Activation du [déchargement SSL](../../application-gateway/create-ssl-portal.md)
- Désactivation de [TLS versions 1.0 et 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Pare-feu d’applications web](../../application-gateway/waf-overview.md) (mode de prévention)
- [Mode Prévention](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) avec l’ensemble de règles OWASP 3.0
- Activation de la [journalisation des diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Sondes d’intégrité personnalisées](../../application-gateway/quick-create-portal.md)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) et [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fournissent une protection supplémentaire et des notifications. Azure Security Center fournit également un système de réputation.

### <a name="business-continuity"></a>Continuité de l’activité
**Haute disponibilité** : La solution déploie toutes les machines virtuelles sur un [groupe à haute disponibilité](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Les groupes à haute disponibilité veillent à ce que les machines virtuelles soient distribuées sur plusieurs clusters matériels isolés pour améliorer la disponibilité. Au moins une machine virtuelle est disponible pendant un événement de maintenance planifié ou non, ce qui est conforme au contrat de niveau de service Azure garantissant une disponibilité de 99,95 %.

**Coffre Recovery Services** : le [coffre Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) héberge les données de sauvegarde et protège toutes les configurations des machines virtuelles Azure dans cette architecture. Grâce au coffre Recovery Services, les clients peuvent restaurer des fichiers et dossiers d’une machine virtuelle IaaS sans avoir à restaurer l’intégralité de celle-ci, ce qui permet d’accélérer les temps de restauration.

**Témoin cloud** : le [Témoin cloud](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) est un type de témoin de quorum de cluster de basculement dans Windows Server 2016, qui utilise Azure comme point d’arbitrage. Comme n’importe quel autre témoin de quorum, le Témoin cloud obtient un vote et peut être utilisé dans les calculs de quorum, mais il utilise le stockage Blob Azure standard disponible publiquement. Cela élimine le travail de maintenance supplémentaire des machines virtuelles hébergées dans un cloud public.

### <a name="logging-and-auditing"></a>Journalisation et audit
Les services Azure assurent une journalisation complète de l’activité du système et des utilisateurs, ainsi que de l’intégrité du système :
- **Journaux d’activité** : les [journaux d’activité](../../azure-monitor/platform/activity-logs-overview.md) fournissent des insights sur les opérations ayant été effectuées sur les ressources d’un abonnement. Les journaux d’activité peuvent aider à déterminer l’initiateur, l’heure d’exécution et l’état d’une opération.
- **Journaux de diagnostic** : les [journaux de diagnostic](../../azure-monitor/platform/diagnostic-logs-overview.md) incluent l’ensemble des journaux d’activité générés par chaque ressource. Ils incluent les journaux des événements système de Windows, les journaux d’activité de Stockage Azure, les journaux d’audit du Key Vault, ainsi que les journaux d’activité de pare-feu et d’accès d’Application Gateway. Tous les journaux de diagnostic sont consignés dans un compte de stockage Azure centralisé et chiffré pour l’archivage. L’utilisateur peut configurer la rétention jusqu’à 730 jours pour répondre aux exigences de rétention spécifiques de l’entreprise.

**Journaux d’activité Azure Monitor** : ces journaux sont regroupés dans [Journaux Azure Monitor](https://azure.microsoft.com/services/log-analytics/) à des fins de traitement, de stockage et de génération de rapports de tableau de bord. Une fois collectées, les données sont organisées dans différents tableaux en fonction du type de données. Toutes les données sont ainsi analysées ensemble, quelle que soit leur source d’origine. Par ailleurs, Azure Security Center s’intègre à Journaux Azure Monitor pour permettre aux clients d’utiliser des requêtes Kusto ; ils peuvent alors accéder à leurs données d’événement de sécurité et les combiner avec des données provenant d’autres services.

Cette architecture intègre également les [solutions de supervision](../../monitoring/monitoring-solutions.md) suivantes :
-   [Active Directory Assessment](../../azure-monitor/insights/ad-assessment.md) : la solution Active Directory Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit une liste hiérarchisée de suggestions spécifiques pour l’infrastructure de serveur déployée.
- [SQL Assessment](../../azure-monitor/insights/sql-assessment.md) : la solution SQL Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit aux clients une liste hiérarchisée de suggestions spécifiques pour l’infrastructure de serveur déployée.
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md) : cette solution de contrôle d’intégrité des agents indique le nombre d’agents déployés et leur répartition géographique, ainsi que le nombre d’agents qui ne répondent pas ou qui envoient des données opérationnelles.
-   [Activity Log Analytics](../../azure-monitor/platform/collect-activity-logs.md) : la solution Activity Log Analytics facilite l’analyse des journaux d’activité Azure de tous les abonnements Azure d’un client.

**Azure Automation** : [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) stocke, exécute et gère les runbooks. Dans cette solution, des runbooks aident à collecter les journaux d’activité d’Azure SQL Server. La solution Automation [Change Tracking](../../automation/change-tracking.md) permet aux clients d’identifier facilement les changements dans l’environnement.

**Azure Monitor** : [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) aide les utilisateurs à suivre les performances, garantir la sécurité et identifier les tendances en permettant aux organisations d’auditer, de créer des alertes et d’archiver des données, y compris le suivi des appels d’API dans leurs ressources Azure.

[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) : Azure Network Watcher offre des outils permettant d’effectuer un monitoring et des diagnostics, d’afficher les métriques et d’activer et de désactiver les journaux d’activité pour les ressources se trouvant sur un réseau virtuel Azure.  Les entités du Commonwealth doivent implémenter les journaux d’activité des flux Network Watcher pour les groupes de sécurité réseau et les machines virtuelles. Ces journaux d’activité doivent être stockés dans un compte de stockage dédié où seuls les journaux d’activité de sécurité sont stockés, et l’accès au compte de stockage doit être sécurisé avec des contrôles d’accès en fonction du rôle.

## <a name="threat-model"></a>Modèle de menace
Le diagramme de flux de données de cette architecture de référence figure ci-dessous et est également disponible en [téléchargement](https://aka.ms/au-protected-iaaswa-tm). Ce modèle peut aider les clients à comprendre les points de risque potentiel de l’infrastructure du système lors de l’apport de modifications.

![Modèle de risque d’application web IaaS pour AU-PROTECTED](images/au-protected-iaaswa-threat-model.png?raw=true "Diagramme du modèle de risque d’application web IaaS pour AU-PROTECTED")

## <a name="compliance-documentation"></a>Documentation sur la conformité
Cette documentation sur la conformité est produite par Microsoft en fonction des plateformes et services disponibles auprès de Microsoft. En raison de la grande variété des déploiements des clients, cette documentation fournit une approche généralisée seulement pour une solution hébergée dans l’environnement Azure. Les clients peuvent identifier et utiliser d’autres produits et services en fonction de leurs propres environnements et de leurs objectifs métier. Les clients choisissant d’utiliser des ressources locales doivent traiter les questions de sécurité et d’exploitation pour ces ressources. La solution documentée peut être personnalisée par les clients pour l’adapter à leurs exigences locales et de sécurité spécifiques.

Le [Blueprint de sécurité et de conformité Azure - Matrice de responsabilités des clients AU-PROTECTED](https://aka.ms/au-protected-crm) liste tous les contrôles de sécurité exigés par AU-PROTECTED. La matrice indique si l’implémentation de chaque contrôle revient à Microsoft, au client, ou est partagé entre les deux.

Le [Blueprint de sécurité et de conformité Azure - Matrice d’implémentation des contrôles des applications web IaaS pour AU-PROTECTED](https://aka.ms/au-protected-iaaswa-cim) fournit des informations sur les contrôles AU-PROTECTED que prend en compte l’architecture des applications web IaaS, notamment une description détaillée de la façon dont l’implémentation répond aux exigences de chaque contrôle couvert.

## <a name="guidance-and-recommendations"></a>Instructions et recommandations
### <a name="vpn-and-expressroute"></a>VPN et ExpressRoute
Pour les informations classifiées, un tunnel VPN IPSec sécurisé doit être configuré pour établir une connexion sécurisée aux ressources déployées en lien avec cette architecture de référence d’application web IaaS. En configurant correctement un VPN IPSec, les clients peuvent ajouter une couche de protection pour les données en transit.

En implémentant un tunnel VPN IPSec sécurisé avec Azure, il est possible de créer une connexion privée virtuelle entre un réseau local et un réseau virtuel Azure. Cette connexion peut se faire via Internet et permet aux clients de « tunneler » des informations de façon sécurisée par le biais d’une liaison chiffrée entre leur réseau et Azure. La technologie de VPN de site à site, aussi sécurisée qu’aguerrie, est déployée par des entreprises de toutes tailles depuis des décennies. 

Comme le trafic à l’intérieur du tunnel VPN transite par Internet avec un VPN de site à site, Microsoft offre une option de connexion privée. Azure ExpressRoute est une liaison dédiée entre Azure et un emplacement local ou un fournisseur d’hébergement Exchange ; il est considéré comme un réseau privé. Les connexions ExpressRoute ne transitant pas par Internet, elles offrent de meilleurs niveaux de fiabilité, de rapidité et de latence que les connexions classiques via Internet. En outre, comme il s’agit d’une connexion directe du fournisseur de télécommunications du client, les données ne circulent pas sur Internet et n’y sont donc pas exposées.

Les meilleures pratiques concernant l’implémentation d’un réseau hybride sécurisé qui étend un réseau local à Azure sont [décrites ici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid). 

Pour protéger les données classifiées, qu’ils utilisent Internet ou Azure ExpressRoute, les clients doivent configurer leurs VPN IPSec en appliquant les paramètres suivants :

•   L’initiateur VPN du client doit être configuré pour une durée de vie d’association de sécurité ne dépassant pas 14 400 secondes.
•   L’initiateur VPN du client doit désactiver le mode agressif d’IKEv1.
•   L’initiateur VPN du client doit configurer la confidentialité persistante parfaite (Perfect Forward Secrecy).
•   L’initiateur VPN du client doit configurer l’utilisation de HMAC-SHA256 ou supérieur.

Les options de configuration pour les appareils VPN et les paramètres IPSec/ IKE sont [disponibles](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) pour révision.

### <a name="azure-active-directory-setup"></a>Installation d’Azure Active Directory
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) joue un rôle essentiel dans la gestion du déploiement et de la configuration des accès des membres du personnel qui interagissent avec l’environnement. Une instance Windows Server Active Directory existante peut être intégrée à Azure Active Directory en [quatre clics](../../active-directory/hybrid/how-to-connect-install-express.md).

En outre, [Azure Active Directory Connect](../../active-directory/hybrid/whatis-hybrid-identity.md) permet aux clients de configurer la fédération avec les [services de fédération Active Directory (AD FS)]( ../../active-directory/hybrid/how-to-connect-fed-azure-adfs.md) et Azure Active Directory locaux. Avec l’authentification de fédération, les clients peuvent autoriser les utilisateurs à se connecter aux services Azure Active Directory avec leurs mots de passe locaux, sans avoir à les entrer à nouveau, et ce alors qu’ils sont sur le réseau d’entreprise. En utilisant l’option de fédération avec les services de fédération Active Directory (AD FS), vous pouvez déployer une nouvelle installation des services de fédération Active Directory (AD FS) ou spécifier une installation existante dans une batterie de serveurs Windows Server 2012 R2.

Pour empêcher la synchronisation des données classifiées avec Azure Active Directory, les clients peuvent limiter les attributs qui sont répliqués vers Azure Active Directory en appliquant les paramètres suivants dans Azure Active Directory Connect :
- [Activer le filtrage](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md)
- [Désactiver la synchronisation de hachage de mot de passe](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- [Désactiver la réécriture du mot de passe](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
- [Désactiver la réécriture d’appareil](../../active-directory/hybrid/how-to-connect-device-writeback.md)
- Conservez les paramètres par défaut pour [empêcher les suppressions accidentelles](../../active-directory/hybrid/how-to-connect-sync-feature-prevent-accidental-deletes.md) et la [mise à niveau automatique](../../active-directory/hybrid/how-to-connect-install-automatic-upgrade.md)

## <a name="disclaimer"></a>Clause d'exclusion de responsabilité
- Ce document est fourni à titre d’information uniquement. MICROSOFT N’ACCORDE AUCUNE GARANTIE EXPRESSE, IMPLICITE OU STATUTAIRE EN LIEN AVEC LES INFORMATIONS CONTENUES DANS CE DOCUMENT. Ce document est fourni « en l’état ». Les informations et les points de vue exprimés dans ce document, y compris les URL et autres références à des sites web, peuvent être modifiés sans préavis. Les clients qui lisent ce document assument les risques liés à son utilisation.
- Ce document n’accorde aux clients aucun droit légal de propriété intellectuelle sur les produits ou solutions Microsoft.
- Les clients peuvent copier et utiliser ce document pour un usage interne, à titre de référence.
- Certaines recommandations contenues dans ce document peuvent entraîner une augmentation des taux d’utilisation des données, des réseaux ou des ressources de calcul dans Azure, débouchant sur des coûts de licence ou d’abonnement supplémentaires.
- Cette architecture constitue une base que les clients peuvent modifier en fonction de leurs besoins. Elle ne doit pas être utilisée telle quelle dans un environnement de production.
- Développé en tant que référence, ce document ne doit pas être utilisé pour définir tous les moyens par lesquels un client peut satisfaire à des réglementations et exigences de conformité spécifiques. Les clients doivent rechercher une assistance juridique auprès de leur organisation pour connaître les implémentations client approuvées.
