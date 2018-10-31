---
title: 'Blueprint sur la sécurité et la conformité d’Azure : Entrepôt de données pour NIST SP 800-171'
description: 'Blueprint sur la sécurité et la conformité d’Azure : Entrepôt de données pour NIST SP 800-171'
services: security
author: jomolesk
ms.assetid: dbc9cafe-115d-4965-b0d4-fcf235a064c8
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: ec608964190c65d8d064582920e53545b9ee62a6
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49404000"
---
# <a name="azure-security-and-compliance-blueprint---data-warehouse-for-nist-sp-800-171"></a>Blueprint sur la sécurité et la conformité d’Azure : Entrepôt de données pour NIST SP 800-171

## <a name="overview"></a>Vue d’ensemble
[NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) fournit des instructions pour protéger les informations non classifiées contrôlées qui se trouvent dans des systèmes d’information et des organisations qui ne font pas partie du secteur public. NIST SP 800-171 établit 14 familles d’exigences de sécurité pour protéger la confidentialité des informations non classifiées contrôlées.

Ce blueprint sur la sécurité et la conformité d’Azure fournit des conseils pour aider les utilisateurs à déployer une architecture d’entrepôt de données dans Azure qui implémente une partie des contrôles NIST SP 800-171. Cette solution montre comment les clients peuvent répondre à des exigences de conformité et de sécurité spécifiques. Elle sert également de base pour les clients qui souhaitent générer et configurer leurs propres solutions d’entrepôt de données dans Azure.

Cette architecture de référence, ainsi que le guide d‘implémentation et le modèle de menace associés, sont destinés à servir de base aux clients qui doivent les adapter à leurs besoins spécifiques. Ils ne doivent pas être utilisés tels quels dans un environnement de production. Il incombe aux clients d‘effectuer des contrôles de conformité et de sécurité appropriés de toute solution générée à l‘aide de cette architecture. Les exigences peuvent varier selon les spécificités de l‘implémentation de chaque client.

## <a name="architecture-diagram-and-components"></a>Diagramme et composants de l’architecture
Cette solution fournit une architecture de référence qui implémente un entrepôt de données cloud à hautes performances et sécurisé. Il existe deux niveaux de données distincts dans cette architecture. Dans un niveau, les données sont importées, stockées et mises en lots dans un environnement SQL en cluster. Un autre niveau est destiné à l’entrepôt de données SQL. Avec ce niveau, les données sont chargées à l’aide d’un outil d’extraction, de transformation et de chargement (ETL) (par exemple, les requêtes T-SQL [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)) pour le traitement. Une fois que les données sont stockées dans SQL Data Warehouse, des analyses peuvent être exécutées à très grande échelle.

Azure offre divers services de création de rapports et d’analyse pour le client. Cette solution inclut SQL Server Reporting Services, qui permet de créer rapidement des rapports à partir de l’entrepôt de données SQL. La totalité du trafic SQL est chiffrée avec SSL grâce à l’inclusion de certificats auto-signés. En guise de bonne pratique, nous recommandons l’utilisation d’une autorité de certification de confiance afin de renforcer la sécurité.

Azure SQL Data Warehouse stocke les données dans des tables relationnelles avec un stockage en colonnes. Ce format réduit considérablement les coûts de stockage des données et améliore les performances des requêtes. Selon les exigences d’utilisation, les ressources de calcul de SQL Data Warehouse peuvent faire l’objet d’un scale-up ou d’un scale-down, ou complètement arrêtées si aucun processus actif n’a besoin de ressources de calcul.

Un équilibreur de charge SQL Server gère le trafic SQL pour garantir la haute performance. Toutes les machines virtuelles (VM) de cette architecture de référence se déploient dans un groupe à haute disponibilité avec des instances SQL Server configurées dans un groupe de disponibilité Always On. Cette configuration vous fournit la haute disponibilité et des fonctionnalités de récupération d’urgence.

Cette architecture de référence d’entrepôt de données inclut également une couche Active Directory (AD) pour gérer les ressources au sein de l’architecture. Le sous-réseau Active Directory permet une adoption facile sous une structure de forêt Active Directory plus importante. De cette façon, l’environnement peut fonctionner en permanence, même lorsque l’accès à la plus grande forêt est indisponible. Toutes les machines virtuelles sont jointes par un domaine à la couche Active Directory. Elles utilisent des stratégies de groupe Active Directory pour appliquer des configurations de sécurité et de conformité au niveau du système d’exploitation.

La solution utilise des comptes de stockage Azure que les clients peuvent configurer pour utiliser Storage Service Encryption afin de maintenir la confidentialité des données au repos. Azure stocke trois copies des données dans un centre de données sélectionné par le client pour assurer la résilience. Le stockage géoredondant garantit que les données sont répliquées dans un centre de données secondaire situé à des centaines de kilomètres, et stockées à nouveau sous forme de trois copies dans ce centre de données. Cette organisation empêche qu‘un événement dommageable se produisant dans le centre de données principal du client entraîne une perte de données.

Pour renforcer la sécurité, toutes les ressources dans cette solution sont gérées sous forme de groupe de ressources dans Azure Resource Manager. Le contrôle d‘accès en fonction du rôle Azure Active Directory (Azure AD) permet de contrôler l‘accès aux ressources déployées. Ces ressources incluent les clés de client dans Azure Key Vault. L’intégrité du système est surveillée dans Azure Security Center et Azure Monitor. Les clients configurent ces deux services de surveillance pour capturer les journaux. L‘intégrité du système est affichée dans un tableau de bord unique qui est facile à utiliser.

Une machine virtuelle sert d’hôte bastion de gestion. Elle fournit une connexion sécurisée permettant aux administrateurs d’accéder aux ressources déployées. Les données sont chargées dans la zone de mise en lots par le biais de cet hôte bastion de gestion. *Nous vous recommandons de configurer une connexion VPN ou Azure ExpressRoute pour la gestion et l’importation de données dans le sous-réseau de l’architecture de référence.*

![Diagramme de l’architecture de référence des entrepôts de données pour NIST SP 800-171](images/nist171-dw-architecture.png "Diagramme de l’architecture de référence des entrepôts de données pour NIST SP 800-171")

Cette solution utilise les services Azure suivants. Pour plus d‘informations, consultez la section sur l‘[architecture de déploiement](#deployment-architecture).

- Groupes à haute disponibilité
    - contrôleurs de domaine Active Directory ;
    - Témoin et nœuds de cluster SQL Server
- Azure Active Directory
- Azure Data Catalog
- Azure Key Vault
- Azure Monitor
- Azure Security Center
- Azure Load Balancer
- Stockage Azure
- Azure Log Analytics
- Machines virtuelles Azure
    - (1) Hôte bastion
    - (2) Contrôleur de domaine Active Directory
    - (2) Nœud de cluster SQL Server
    - (1) Témoin SQL Server
- Réseau virtuel Azure
    - (1) réseau /16
    - (4) réseaux /24
    - (4) Groupes de sécurité réseau
- Coffre Recovery Services
- SQL Data Warehouse
- SQL Server Reporting Services

## <a name="deployment-architecture"></a>Architecture de déploiement
La section ci-après décrit en détail les éléments nécessaires au déploiement et à l’implémentation.

**Azure SQL Data Warehouse** : [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) est un entrepôt de données d’entreprise qui tire profit du traitement massivement parallèle pour exécuter rapidement des requêtes complexes sur des pétaoctets de données. Les utilisateurs peuvent importer des données du Big Data dans l’entrepôt de données SQL à l’aide de requêtes T-SQL PolyBase simples, puis utiliser la puissance du traitement massivement parallèle pour exécuter des analyses hautes performances.

**SQL Server Reporting Services** : [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) permet de créer rapidement des rapports avec des tables, des graphiques, des cartes, des jauges, des matrices et bien d’autres éléments pour SQL Data Warehouse.

**Azure Data Catalog** : [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) permet aux utilisateurs qui gèrent les données de détecter et comprendre facilement les sources de données. Les sources de données courantes peuvent être inscrites, étiquetées et soumises à des recherches de données. Les données restent à leur emplacement existant, mais une copie de leurs métadonnées est ajoutée à Data Catalog. Une référence à l’emplacement de source de données est incluse. Les métadonnées sont indexées pour faciliter la découverte via la recherche de chaque source de données. L’indexation permet aussi aux utilisateurs qui la découvrent de la comprendre.

**Hôte bastion** : point d‘entrée unique qui permet aux utilisateurs d‘accéder aux ressources déployées dans cet environnement. L‘hôte bastion fournit une connexion sécurisée à des ressources déployées en autorisant uniquement le trafic distant provenant d‘adresses IP publiques figurant sur une liste verte. Pour autoriser le trafic Bureau à distance, la source du trafic doit être définie dans le groupe de sécurité réseau.

Cette solution crée une machine virtuelle en tant qu‘hôte bastion joint au domaine avec les configurations suivantes :
-   [Extension Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware).
-   [Extension Azure Diagnostics](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) avec Key Vault.
-   [Stratégie d‘arrêt automatique](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) pour réduire la consommation des ressources de machine virtuelle non utilisées.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) est activé pour que les informations d‘identification et autres secrets s‘exécutent dans un environnement protégé, isolé du système d‘exploitation exécuté.

### <a name="virtual-network"></a>Réseau virtuel
Cette architecture de référence définit un réseau privé virtuel avec un espace d’adressage de 10.0.0.0/16.

**Groupes de sécurité réseau** : les [groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) contiennent des listes de contrôle d’accès qui autorisent ou refusent le trafic au sein d’un réseau virtuel. Les groupes de sécurité réseau peuvent être utilisés pour sécuriser le trafic au niveau d’un sous-réseau ou d’une machine virtuelle. Les NSG disponibles sont les suivants :
  - Un groupe de sécurité réseau pour la couche Données (clusters SQL Server, témoin SQL Server et équilibreur de charge SQL)
  - un NSG pour l’hôte bastion de gestion ;
  - un NSG pour Active Directory ;
  - un NSG pour SQL Server Reporting Services.

Chaque groupe de sécurité réseau a des ports et protocoles spécifiques ouverts afin que la solution puisse fonctionner correctement et en toute sécurité. En outre, les configurations suivantes sont activées pour chaque groupe de sécurité réseau :
  - Les [événements et journaux de diagnostic](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) sont activés et stockés dans un compte de stockage.
  - La solution Log Analytics est connectée aux [diagnostics du groupe de sécurité réseau](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Sous-réseaux** : chaque sous-réseau est associé au NSG qui lui correspond.

### <a name="data-at-rest"></a>Données au repos
L’architecture protège les données au repos par le biais de plusieurs mesures. Ces mesures incluent le chiffrement et l’audit de la base de données.

**Stockage Azure** : pour répondre aux exigences de chiffrement des données au repos, l’ensemble du service [Stockage](https://azure.microsoft.com/services/storage/) utilise la fonctionnalité de chiffrement [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Celle fonctionnalité permet de protéger et de sauvegarder les données dans le respect des engagements de l’organisation en matière de sécurité et des exigences de conformité.

**Azure Disk Encryption** : [Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utilise la fonctionnalité BitLocker de Windows pour assurer le chiffrement de volume du système d’exploitation et des disques de données. La solution s’intègre à Key Vault pour favoriser le contrôle et la gestion des clés de chiffrement de disque.

**Azure SQL Database** : l’instance SQL Database utilise les mesures de sécurité de base de données suivantes :
-   La solution [d’authentification et d’autorisation Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permet de gérer les identités des utilisateurs de bases de données et d’autres services Microsoft dans un emplacement central.
-   [L’audit SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) suit les événements de base de données et les consigne dans un journal d’audit conservé dans un compte de stockage Azure.
-   SQL Database est configuré pour utiliser [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Il effectue le chiffrement et le déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux de transactions, afin de protéger les informations au repos. Transparent Data Encryption garantit que les données stockées ne font pas l’objet d’accès non autorisés.
-   Des [règles de pare-feu](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) empêchent tout accès aux serveurs de base de données sans autorisation appropriée. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande.
-   [SQL Threat Detection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permet la détection et la réponse aux menaces potentielles lorsqu’elles se produisent. Il fournit des alertes de sécurité en cas d’activités de base de données suspectes, de vulnérabilités potentielles, d’attaques par injection de code SQL et de modalités d’accès anormales aux bases de données.
-   Les [colonnes chiffrées](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantissent que les données sensibles n’apparaissent jamais en texte clair à l’intérieur du système de base de données. Une fois le chiffrement des données activé, seules des applications clientes ou des serveurs d’applications ayant accès aux clés peuvent accéder aux données en texte clair.
- Les [propriétés étendues](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) peuvent être utilisées pour interrompre le traitement des personnes concernées. Les utilisateurs peuvent ajouter des propriétés personnalisées aux objets de base de données. Ils peuvent également étiqueter des données comme étant « Interrompues » pour prendre en charge la logique d’application afin d’empêcher le traitement des données financières associées.
- La [Sécurité au niveau des lignes](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permet aux utilisateurs de définir des stratégies de restriction d’accès aux données afin d’interrompre le traitement de celles-ci.
- [SQL Database Dynamic Data Masking](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limite l’exposition des données sensibles en masquant celles-ci aux utilisateurs ou aux applications dépourvus de privilèges. Il peut détecter automatiquement les données potentiellement sensibles et suggérer les masques appropriés à appliquer. Dynamic Data Masking permet de réduire l’accès afin que les données sensibles ne sortent pas de la base de données via un accès non autorisé. *Les clients doivent ajuster les paramètres pour respecter leur schéma de base de données.*

### <a name="identity-management"></a>Gestion des identités
Les technologies suivantes offrent des fonctionnalités de gestion de l’accès aux données dans l’environnement Azure :
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) est le service multilocataire basé sur le cloud de Microsoft pour la gestion des identités et des annuaires. Tous les utilisateurs de cette solution sont créés dans Azure AD et incluent les utilisateurs qui accèdent à la base de données SQL.
-   L’authentification auprès de l’application est effectuée à l’aide d’Azure AD. Pour plus d’informations, consultez [Intégrer des applications à Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Le chiffrement des colonnes de base de données utilise également Azure AD pour authentifier l’application auprès de SQL Database. Pour plus d’informations, découvrez comment [protéger les données sensibles dans SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [RBAC Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) peut être utilisé par les administrateurs pour définir des autorisations d’accès précises. Avec cela, ils peuvent accorder uniquement les droits d’accès dont les utilisateurs ont besoin pour effectuer leur travail. Au lieu d’attribuer à tous les utilisateurs des autorisations d’accès illimitées aux ressources Azure, les administrateurs peuvent autoriser seulement certaines actions d’accès aux ressources et aux données. Par exemple, l’accès aux abonnements est limité à l’administrateur d’abonnements.
- La solution [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) permet aux clients de minimiser le nombre d’utilisateurs qui ont accès à des informations spécifiques, telles que des données. Les administrateurs peuvent utiliser Azure AD Privileged Identity Management pour découvrir, restreindre et superviser les identités privilégiées et leur accès aux ressources. Cette fonctionnalité permet également d’appliquer un accès administratif juste-à-temps à la demande, si nécessaire.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) détecte les vulnérabilités potentielles susceptibles d’affecter les identités d’une organisation. Il configure des réponses automatiques aux actions suspectes détectées et liées aux identités d’une organisation. Il examine également les incidents suspects pour prendre les mesures appropriées afin de les résoudre.

### <a name="security"></a>Sécurité
**Gestion des secrets** : la solution utilise [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pour gérer les clés et les secrets. Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. Les fonctionnalités Key Vault suivantes aident les clients à protéger les données :
- Les stratégies d’accès avancées sont configurées en fonction des besoins.
- Les stratégies d’accès Key Vault sont définies avec des autorisations minimales requises pour les clés et les secrets.
- L’ensemble des clés et secrets figurant dans Key Vault présentent des dates d’expiration.
- Toutes les clés dans Key Vault sont protégées par des modules de sécurité matériels spécialisés. Le type de clé est une clé RSA de 2 048 bits protégée par un module de sécurité matériel.
- Les autorisations minimales requises sont accordées à l’ensemble des utilisateurs et identités à l’aide d’un contrôle d’accès en fonction du rôle (RBAC).
- Les journaux de diagnostics pour Key Vault sont activés avec une période de rétention d’au moins 365 jours.
- Les opérations de chiffrement autorisées pour les clés sont restreintes à celles qui sont nécessaires.

**Gestion des correctifs** : les machines virtuelles Windows déployées dans le cadre de cette architecture de référence sont configurées par défaut pour recevoir des mises à jour automatiques du service Windows Update. Cette solution inclut également le service [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) qui permet de créer des déploiements mis à jour pour corriger des machines virtuelles si nécessaire.

**Protection contre les programmes malveillants** : [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) pour machines virtuelles offre une fonctionnalité de protection en temps réel qui permet d’identifier et de supprimer les virus, logiciels espions et autres logiciels malveillants. Les clients peuvent configurer des alertes qui se déclenchent lorsque des logiciels malveillants ou indésirables connus tentent de s’installer ou de s’exécuter sur des machines virtuelles protégées.

**Azure Security Center** : avec [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), les clients peuvent appliquer et gérer de façon centralisée des stratégies de sécurité entre charges de travail, limiter l’exposition aux menaces, ainsi que détecter les attaques et y répondre. Security Center accède également aux configurations existantes des services Azure pour fournir des suggestions en matière de configuration et de service. Cela contribue à l’amélioration du dispositif de sécurité et à la protection des données.

Security Center utilise diverses fonctionnalités de détection pour avertir les clients des attaques potentielles qui ciblent leurs environnements. Ces alertes fournissent de précieuses informations sur le déclencheur de l’alerte, les ressources ciblées et la source de l’attaque. Security Center possède un ensemble d’[alertes de sécurité prédéfinies](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), qui sont déclenchées en cas de menace ou d’activité suspecte. Les clients peuvent utiliser les [règles d’alerte personnalisées](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) pour définir de nouvelles alertes de sécurité basées sur les données déjà collectées dans leur environnement.

Security Center fournit des alertes et incidents de sécurité classés par ordre de priorité. Security Center permet aux clients de découvrir et de résoudre plus facilement les problèmes de sécurité potentiels. Un [rapport d’informations sur les menaces](https://docs.microsoft.com/azure/security-center/security-center-threat-report) est généré pour chaque menace détectée. Les équipes de réponse aux incidents peuvent utiliser ces rapports lorsqu’elles enquêtent pour corriger des menaces.

Cette architecture de référence utilise également la fonction d’[évaluation des vulnérabilités](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) dans Security Center. Une fois sa configuration terminée, un agent partenaire (par exemple, Qualys) commence à signaler les données de vulnérabilité à la plateforme de gestion du partenaire. En retour, la plateforme de gestion du partenaire fournit des données de surveillance de l’intégrité et des vulnérabilités à Security Center. Les clients peuvent utiliser ces informations pour identifier rapidement les machines virtuelles vulnérables.

### <a name="business-continuity"></a>Continuité de l’activité
**Haute disponibilité** : les charges de travail serveur sont regroupées dans un [groupe à haute disponibilité](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pour garantir la haute disponibilité des machines virtuelles dans Azure. Au moins une machine virtuelle est disponible pendant un événement de maintenance planifié ou non, ce qui est conforme au contrat de niveau de service Azure garantissant une disponibilité de 99,95 %.

**Coffre Recovery Services** : le [coffre Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) héberge les données de sauvegarde et protège toutes les configurations des machines virtuelles de cette architecture. Grâce au coffre Recovery Services, les clients peuvent restaurer des fichiers et dossiers d’une machine virtuelle IaaS sans avoir à restaurer l’intégralité de celle-ci. Ce processus accélère les temps de restauration.

### <a name="logging-and-auditing"></a>Journalisation et audit

Les services Azure assurent une journalisation complète de l’activité du système et des utilisateurs, ainsi que de l’intégrité du système :
- **Journaux d’activité :** les [journaux d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fournissent des informations sur les opérations effectuées sur les ressources d’un abonnement. Les journaux d’activité peuvent aider à déterminer l’initiateur, l’heure d’exécution et l’état d’une opération.
- **Journaux de diagnostic :** les [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) sont tous les journaux émis par toutes les ressources. Ils incluent les journaux des événements système de Windows, les journaux de Stockage, les journaux d’audit de Key Vault, ainsi que les journaux de pare-feu et d’accès d’Azure Application Gateway. Tous les journaux de diagnostic sont consignés dans un compte de stockage Azure centralisé et chiffré pour l’archivage. Les utilisateurs peuvent configurer la période de rétention jusqu‘à 730 jours, pour répondre à leurs besoins spécifiques.

**Log Analytics** : ces journaux sont consolidés dans [Log Analytics](https://azure.microsoft.com/services/log-analytics/) à des fins de traitement, de stockage et de génération de tableaux de bord. Une fois les données collectées, elles sont organisées dans des tables distinctes pour chaque type de données au sein des espaces de travail Log Analytics. De cette façon, toutes les données peuvent être analysées ensemble, quelle que soit leur source d’origine. Security Center s’intègre avec Log Analytics. Les clients peuvent utiliser des requêtes Log Analytics pour accéder à leurs données d’événement de sécurité et de les combiner avec des données provenant d’autres services.

Les [solutions de gestion](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Log Analytics suivantes sont également incluses dans cette architecture :
-   [Évaluation d‘Active Directory](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment) : la solution Active Directory Health Check permet d’évaluer les risques et l’intégrité des environnements de serveurs à intervalles réguliers. Elle fournit une liste hiérarchisée de suggestions propres à l’infrastructure de serveurs déployée.
- [Évaluation SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment) : la solution SQL Health Check permet d’évaluer les risques et l’intégrité des environnements de serveurs à intervalles réguliers. Elle fournit aux clients une liste hiérarchisée de suggestions propres à l’infrastructure de serveurs déployée.
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth) : la solution Agent Health signale le nombre d’agents déployés et leur répartition géographique. Elle signale également le nombre d’agents qui ne répondent pas et le nombre d’agents qui envoient des données opérationnelles.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity) : la solution Activity Log Analytics facilite l’analyse des journaux d’activité Azure de tous les abonnements Azure d’un client.

**Azure Automation** : [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) stocke, exécute et gère les runbooks. Dans cette solution, les runbooks aident à collecter les journaux de SQL Database. Les clients peuvent utiliser la solution Automation [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) pour identifier facilement les changements dans l’environnement.

**Azure Monitor** : [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) aide les utilisateurs à suivre les performances, à gérer la sécurité et à identifier les tendances. Les organisations peuvent l’utiliser pour auditer, créer des alertes et archiver les données. Elles peuvent également suivre les appels d’API dans leurs ressources Azure.

## <a name="threat-model"></a>Modèle de menace

Le diagramme de flux de données de cette architecture de référence figure ci-dessous et est également disponible en [téléchargement](https://aka.ms/nist171-dw-tm). Ce modèle peut aider les clients à comprendre les points de risque potentiel de l’infrastructure du système lors de l’apport de modifications.

![Modèle de risque des entrepôts de données pour NIST SP 800-171](images/nist171-dw-threat-model.png "Modèle de risque des entrepôts de données pour NIST SP 800-171")

## <a name="compliance-documentation"></a>Documentation sur la conformité
Le [Blueprint sur la sécurité et la conformité Azure – Matrice de responsabilités des clients de NIST SP 800-171](https://aka.ms/nist171-crm) liste tous les contrôles de sécurité exigés par NIST SP 800-171. La matrice indique si l’implémentation de chaque contrôle revient à Microsoft, au client, ou est partagé entre les deux.

Le [blueprint sur la sécurité et la conformité Azure - Matrice d’implémentation des contrôles d’entrepôt de données pour NIST SP 800-171](https://aka.ms/nist171-dw-cim) fournit des informations sur les contrôles de NIST SP 800-171 que prend en compte l’architecture de l’entrepôt de données. Il comprend une description détaillée de la façon dont l’implémentation répond aux exigences de chaque contrôle couvert.

## <a name="guidance-and-recommendations"></a>Instructions et recommandations

### <a name="vpn-and-expressroute"></a>VPN et ExpressRoute
Il faut configurer un tunnel VPN sécurisé ou un routage [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) pour établir une connexion sécurisée aux ressources déployées dans le cadre de cette architecture de référence d’entrepôt de données. En configurant correctement un VPN ou un routage ExpressRoute, les clients peuvent ajouter une couche de protection au niveau des données en transit.

En implémentant un tunnel VPN sécurisé avec Azure, il est possible de créer une connexion privée virtuelle entre un réseau local et un réseau virtuel Azure. Cette connexion s’effectue via Internet. Les clients peuvent l’utiliser pour créer un « tunnel » pour le transit sécurisé des informations au sein d’une liaison chiffrée entre leur réseau et Azure. La technologie de VPN de site à site, aussi sécurisée qu’aguerrie, est déployée par des entreprises de toutes tailles depuis des décennies. Le [mode tunnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) est utilisé dans cette option comme un mécanisme de chiffrement.

Étant donné que le trafic à l’intérieur du tunnel VPN transite par Internet avec un VPN de site à site, Microsoft offre une autre option de connexion encore plus sécurisée. ExpressRoute est une liaison réseau étendu dédiée entre Azure et un emplacement local ou un fournisseur d’hébergement Exchange. Les connexions ExpressRoute se raccordent directement au fournisseur de télécommunications du client. Par conséquent, les données ne transitent pas par Internet et ne sont pas exposées à ce dernier. Ces connexions offrent davantage de fiabilité, des vitesses supérieures, des latences inférieures et une sécurité renforcée par rapport aux connexions standard.

Les meilleures pratiques concernant l’implémentation d’un réseau hybride sécurisé qui étend un réseau local à Azure sont [décrites ici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Processus d’extraction, de transformation et de chargement
La technologie [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) peut charger des données dans SQL Data Warehouse sans avoir besoin de recourir à un processus ETL ni à un outil d’importation distincts. PolyBase permet d’accéder aux données par le biais de requêtes T-SQL. Il est possible d’utiliser PolyBase avec une pile d’analyse et d’aide à la décision Microsoft, ainsi qu’avec des outils tiers compatibles avec SQL Server.

### <a name="azure-ad-setup"></a>Configuration d’Azure AD
[Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) joue un rôle essentiel dans la gestion du déploiement et de la configuration des accès des membres du personnel qui interagissent avec l’environnement. Active Directory local peut être intégré à Azure AD en [quatre clics](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Les clients peuvent également lier l’infrastructure Active Directory déployée (contrôleurs de domaine) à Azure AD. Pour ce faire, configurez l’infrastructure Active Directory déployée en tant que sous-domaine d’une forêt Azure AD.

### <a name="optional-services"></a>Services facultatifs
Azure offre différents services conçus pour faciliter le stockage et la mise en lots de données mises en forme et sans mises en forme. Les services pouvant être ajoutés à cette architecture de référence en fonction des exigences du client sont les suivants :
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) est un service cloud géré conçu pour des projets complexes hybrides ETL (extraction, chargement et transformation) et d’intégration de données. Data Factory offre des fonctionnalités facilitant le suivi et la localisation des données. Des outils de visualisation et de surveillance identifient le moment de l’arrivée des données, ainsi que leur provenance. Les clients peuvent créer et planifier des flux de travail pilotés par les données, appelés pipelines, qui ingèrent des données provenant de différents magasins de données. Ils peuvent utiliser ces pipelines pour ingérer des données provenant de sources internes et externes. Les clients peuvent ensuite traiter et transformer les données pour les stocker dans des magasins de données comme SQL Data Warehouse.
- Les clients peuvent mettre en lots des données non structurées dans [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview), afin de capturer les données quels que soient leur taille, leur type et leur vitesse d’ingestion dans un seul emplacement en vue d’une analyse opérationnelle et exploratoire. Azure Data Lake offre des possibilités d’extraction et de conversion de données. Data Lake Store est compatible avec la plupart des composants open source de l’écosystème Hadoop. Par ailleurs, il s’intègre facilement avec d’autres services Azure, tels que SQL Data Warehouse.

## <a name="disclaimer"></a>Clause d'exclusion de responsabilité

 - Ce document est fourni à titre d’information uniquement. MICROSOFT N’ACCORDE AUCUNE GARANTIE EXPRESSE, IMPLICITE OU STATUTAIRE EN LIEN AVEC LES INFORMATIONS CONTENUES DANS CE DOCUMENT. Ce document est fourni « en l’état ». Les informations et les points de vue exprimés dans ce document, y compris les URL et autres références à des sites web, peuvent être modifiés sans préavis. Les clients qui lisent ce document assument les risques liés à son utilisation.
 - Ce document n’accorde aux clients aucun droit légal de propriété intellectuelle sur les produits ou solutions Microsoft.
 - Les clients peuvent copier et utiliser ce document pour un usage interne, à titre de référence.
 - Certaines recommandations contenues dans ce document peuvent entraîner une augmentation des taux d’utilisation des données, des réseaux ou des ressources de calcul dans Azure, débouchant sur des coûts de licence ou d’abonnement supplémentaires.
 - Cette architecture constitue une base que les clients peuvent modifier en fonction de leurs besoins. Elle ne doit pas être utilisée telle quelle dans un environnement de production.
 - Développé en tant que référence, ce document ne doit pas être utilisé pour définir tous les moyens par lesquels un client peut satisfaire à des réglementations et exigences de conformité spécifiques. Les clients doivent rechercher une assistance juridique auprès de leur organisation pour connaître les implémentations client approuvées.
