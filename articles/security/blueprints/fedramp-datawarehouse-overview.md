---
title: Azure Blueprint Sécurité et conformité - Automatisation Data Warehouse pour FedRAMP
description: Azure Blueprint Sécurité et conformité - Automatisation Data Warehouse pour FedRAMP
services: security
author: jomolesk
ms.assetid: 834d1ff6-8369-455f-b052-1ef301e3d7e6
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 3c78aed2f30ea85f5bc16a8c0fb270bb1c761be8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539511"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-fedramp-automation"></a>Blueprint de sécurité et de conformité Azure : Entrepôt de données pour l’automatisation de FedRAMP

## <a name="overview"></a>Présentation

[FedRAMP (Federal Risk and Authorization Management Program)](https://www.fedramp.gov/) est un programme déployé à l’échelle de l’administration américaine, visant à rationaliser l’approche en matière d’évaluation de la sécurité, d’autorisation et de surveillance continue des services et produits cloud. Ce document Azure Blueprint Sécurité et conformité vous fournit des instructions pour proposer une architecture d’entrepôt de données Azure permettant d’implémenter un sous-ensemble de contrôles FedRAMP High. Cette solution fournit des instructions pour le déploiement et la configuration des ressources Azure pour une architecture de référence commune, illustrant diverses façons dont les clients peuvent satisfaire à des exigences de conformité et de sécurité spécifiques, et sert de base aux clients souhaitant générer et configurer leurs propres solutions d’entrepôt de données dans Azure.

Cette architecture de référence, ainsi que le guide de mise en œuvre du contrôle associé et les modèles de menace, sont destinés à servir de base aux clients qui doivent les adapter à leurs besoins spécifiques et non les utiliser tels quels dans un environnement de production. Le déploiement d’une application dans cet environnement, sans y apporter de modifications, ne permet pas de répondre entièrement aux exigences de la ligne de base FedRAMP High. Notez les points suivants :
- L’architecture constitue un point de départ pour aider les clients à déployer des charges de travail sur Azure d’une manière conforme à FedRAMP.
- Il incombe aux clients d’évaluer de façon appropriée la sécurité et la conformité de toute solution basée sur cette architecture, car les exigences peuvent varier en fonction des spécificités de chaque implémentation.

## <a name="architecture-diagram-and-components"></a>Composants et schéma de l’architecture

Cette solution offre une architecture de référence d’entrepôt de données qui implémente un entrepôt de données cloud hautes performances et sécurisé. Il existe deux couches Données distinctes dans cette architecture : une où les données sont importées, stockées et mises en lots au sein d’un environnement SQL en cluster et une autre pour Azure SQL Data Warehouse où les données sont chargées à l’aide d’un outil ETL (par exemple, des requêtes T-SQL [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)) pour le traitement. Une fois que les données sont stockées dans Azure SQL Data Warehouse, des analyses peuvent être exécutées à très grande échelle.

Microsoft Azure offre divers services de création de rapports et d’analyse pour le client. Cette solution inclut SQL Server Reporting Services (SSRS), qui permet de créer rapidement des rapports à partir d’Azure SQL Data Warehouse. La totalité du trafic SQL est chiffrée avec SSL grâce à l’inclusion de certificats auto-signés. En guise de meilleure pratique, Azure préconise l’utilisation d’une autorité de certification de confiance afin de renforcer la sécurité.

Les données dans Azure SQL Data Warehouse sont conservées dans des tables relationnelles avec stockage en colonnes, ce format réduisant considérablement les coûts de stockage de données tout en améliorant les performances des requêtes.  Selon les exigences d’utilisation, les ressources de calcul Azure SQL Data Warehouse peuvent être montées ou descendues en puissance, ou complètement arrêtées si aucun processus actif n’a besoin de ressources de calcul.

Un équilibreur de charge SQL gère le trafic de SQL pour garantir des performances élevées. Toutes les machines virtuelles de cette architecture de référence effectuent un déploiement dans un groupe à haute disponibilité avec des instances de SQL Server configurées dans un groupe de disponibilité AlwaysOn pour les fonctionnalités de haute disponibilité et de récupération d’urgence.

Cette architecture de référence d’entrepôt de données inclut également un niveau Active Directory (AD) pour la gestion des ressources au sein de l’architecture. Le sous-réseau Active Directory permet une adoption facile sous une structure de forêt AD plus importante, ce qui permet à l’environnement de fonctionner en continu même lorsque l’accès à la forêt plus importante n’est pas disponible. Toutes les machines virtuelles sont jointes à un domaine du niveau Active Directory et utilisent des stratégies de groupe Active Directory pour appliquer des configurations de sécurité et de conformité au niveau du système d’exploitation.

Une machine virtuelle fait figure d’hôte bastion de gestion pour fournir une connexion sécurisée permettant aux administrateurs d’accéder aux ressources déployées. Les données sont chargées dans la zone de mise en lots par le biais de cet hôte bastion de gestion. **Azure recommande de configurer une connexion VPN ou Azure ExpressRoute pour la gestion et l’importation de données dans le sous-réseau de l’architecture de référence.**

![Schéma de l’architecture de référence Data Warehouse pour FedRAMP](images/fedramp-datawarehouse-architecture.png?raw=true "Schéma de l’architecture de référence Data Warehouse pour FedRAMP")

Cette solution utilise les services Azure suivants. Les informations détaillées concernant l’architecture de déploiement figurent à la section [Architecture de déploiement](#deployment-architecture).

Machines virtuelles Azure
-   (1) Hôte bastion
-   (2) Contrôleur de domaine Active Directory
-   (2) Nœud de cluster SQL Server
-   (1) Témoin SQL Server

Groupes à haute disponibilité
-   (1) Contrôleurs de domaine Active Directory
-   (1) Nœuds de cluster SQL et témoin

Réseau virtuel
-   (4) Sous-réseaux
-   (4) Groupes de sécurité réseau

SQL Data Warehouse

SQL Server Reporting Services

Azure SQL Load Balancer

Azure Active Directory

Coffre Recovery Services

Azure Key Vault

Journaux Azure Monitor

## <a name="deployment-architecture"></a>Architecture de déploiement

La section suivante décrit en détail les éléments de développement et d’implémentation.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) est un entrepôt de données d’entreprise (EDW) qui s’appuie sur le traitement massivement parallèle (MPP) pour exécuter rapidement des requêtes complexes parmi des pétaoctets de données. Importez des données volumineuses dans SQL Data Warehouse avec des requêtes T-SQL PolyBase simples, puis utilisez le traitement massivement parallèle pour exécuter des analyses hautes performances.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) permet de créer rapidement des rapports avec des tables, des graphiques, des cartes, des jauges, des matrices et plus pour Azure SQL Data Warehouse.

**Hôte bastion** : point d’entrée unique qui permet aux utilisateurs d’accéder aux ressources déployées dans cet environnement. L’hôte bastion fournit une connexion sécurisée à des ressources déployées en autorisant uniquement le trafic distant provenant d’adresses IP publiques figurant sur une liste verte. Pour autoriser le trafic RDP (Remote Desktop Protocol), la source du trafic doit être définie dans le groupe de sécurité réseau (NSG).

Une machine virtuelle a été créée en tant qu’hôte bastion joint à un domaine avec les configurations suivantes :
-   [Extension Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Azure Monitor enregistre l’extension](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Extension Diagnostics Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) avec Azure Key Vault (conforme à Azure Government, à la norme PCI DSS, à la loi américaine HIPAA et à d’autres exigences)
-   [Stratégie d’arrêt automatique](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) pour réduire la consommation des ressources de machine virtuelle non utilisées
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) activé afin que les informations d’identification et autres secrets soient utilisés dans un environnement protégé, isolé du système d’exploitation en cours d’exécution

### <a name="virtual-network"></a>Réseau virtuel
Cette architecture de référence définit un réseau privé virtuel avec un espace d’adressage de 10.0.0.0/16.

**Groupes de sécurité réseau** : les [groupes de sécurité réseau (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contiennent des listes de contrôle d’accès (ACL) qui autorisent ou refusent le trafic au sein d’un réseau virtuel. Les groupes de sécurité réseau peuvent être utilisés pour sécuriser le trafic au niveau d’un sous-réseau ou d’une machine virtuelle. Les NSG disponibles sont les suivants :
  - Un groupe de sécurité réseau pour la couche Données (clusters SQL Server, témoin SQL Server et SQL Server Load Balancer)
  - un NSG pour l’hôte bastion de gestion ;
  - un NSG pour Active Directory ;
  - un NSG pour SQL Server Reporting Services.

Chaque groupe de sécurité réseau a des ports et protocoles spécifiques ouverts afin que la solution puisse fonctionner correctement et en toute sécurité. En outre, les configurations suivantes sont activées pour chaque groupe de sécurité réseau :
  - Les [événements et journaux de diagnostic](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) sont activés et stockés dans un compte de stockage.
  - Journaux d’Azure Monitor est connecté à la [diagnostics des groupes de sécurité réseau](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Sous-réseaux** : Chaque sous-réseau est associé à son groupe de sécurité réseau correspondant.

### <a name="data-at-rest"></a>Données au repos
L’architecture protège les données au repos à l’aide d’un chiffrement, d’un audit de base de données et d’autres mesures.

**Stockage Azure** : pour répondre aux exigences de chiffrement des données au repos, l’ensemble du service [Stockage Azure](https://azure.microsoft.com/services/storage/) utilise la fonctionnalité de chiffrement [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) tire profit de la fonctionnalité BitLocker de Windows pour assurer le chiffrement de volume du SE et des disques de données. La solution s’intègre à Azure Key Vault pour faciliter le contrôle et la gestion des clés de chiffrement de disque.

**Azure SQL Database** L’instance Azure SQL Database utilise les mesures de sécurité de base de données suivantes :
-   La solution [d’authentification et d’autorisation Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permet la gestion des identités des utilisateurs de bases de données et d’autres services Microsoft dans un emplacement central.
-   L’[audit Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) suit les événements de base de données et consigne ceux-ci dans un journal d’audit conservé dans un compte de stockage Azure.
-   SQL Database est configuré pour utiliser [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), qui effectue le chiffrement et le déchiffrement en temps réel des données et fichiers journaux pour protéger les informations au repos. TDE protège les données des accès non autorisés.
-   Des [règles de pare-feu](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) empêchent tout accès aux serveurs de base de données sans autorisation appropriée. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande.
-   La [détection des menaces SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permet de détecter et traiter les menaces potentielles à mesure qu’elles surviennent en déclenchant des alertes de sécurité en cas d’activités de base de données suspectes, de vulnérabilités potentielles, d’attaques par injection de code SQL et de modèles d’accès anormaux aux bases de données.
-   Des [colonnes Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantissent que les données sensibles n’apparaissent jamais en tant que texte en clair à l’intérieur du système de base de données. Une fois le chiffrement des données activé, seules des applications clientes ou des serveurs d’applications ayant accès aux clés peuvent accéder aux données en texte clair.
-   Le [masquage de données dynamiques SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) peut être effectué après le déploiement de l’architecture de référence. Les clients doivent ajuster les paramètres de masquage de données dynamiques pour respecter leur schéma de base de données.

### <a name="business-continuity"></a>Continuité de l’activité
**Haute disponibilité** : Charges de travail serveur sont regroupés dans un [à haute disponibilité](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) afin de garantir une haute disponibilité des machines virtuelles dans Azure. Au moins une machine virtuelle est disponible pendant un événement de maintenance planifié ou non, ce qui est conforme au contrat de niveau de service Azure garantissant une disponibilité de 99,95 %.

**Coffre Recovery Services** : le [coffre Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) héberge les données de sauvegarde et protège toutes les configurations des machines virtuelles Azure dans cette architecture. Grâce au coffre Recovery Services, les clients peuvent restaurer des fichiers et dossiers d’une machine virtuelle IaaS sans avoir à restaurer l’intégralité de celle-ci, ce qui permet d’accélérer les temps de restauration.

### <a name="logging-and-audit"></a>Journalisation et audit
[Journaux d’analyse Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) fournit une journalisation complète de l’activité système et utilisateur, ainsi que l’intégrité du système. Le [Azure Monitor enregistre](https://azure.microsoft.com/services/log-analytics/) solution collecte et analyse les données générées par les ressources dans Azure et environnements locaux.
- **Journaux d’activité** : les [journaux d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fournissent des insights sur les opérations ayant été effectuées sur les ressources d’un abonnement.
- **Journaux de diagnostic** : les [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluent l’ensemble des journaux générés par chaque ressource. Ces journaux incluent les journaux système des événements Windows, ainsi que les journaux du service Stockage Blob Azure, des tables et des files d’attente.
- **Journaux du pare-feu** : Application Gateway fournit des journaux de diagnostic et d’accès complets. Les journaux de pare-feu sont disponibles pour les ressources Application Gateway pour lesquelles WAF est activé.
- **Archivage des journaux** : tous les journaux de diagnostic sont archivés dans un compte de stockage Azure centralisé et chiffré, et sont conservés pendant une période prédéfinie de deux jours. Ces journaux se connecter aux journaux d’Azure Monitor pour traitement, de stockage et les rapports de tableau de bord.

En outre, cette architecture intègre également les solutions de supervision suivantes :
-   [AD Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment) : la solution Active Directory Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit une liste hiérarchisée de suggestions spécifiques pour l’infrastructure de serveur déployée.
-   [Antimalware Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware) : la solution de logiciel anti-programme malveillant signale les programmes malveillants, les menaces et l’état de protection.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) : la solution Azure Automation stocke, exécute et gère les runbooks.
-   [Security and Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) : le tableau de bord Security and Audit donne une vue d’ensemble de l’état de sécurité des ressources en affichant des métriques concernant des domaines de sécurité, des problèmes notables, des détections, des menaces et des requêtes de sécurité courantes.
-   [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment) : la solution SQL Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit aux clients une liste hiérarchisée de suggestions spécifiques pour l’infrastructure de serveur déployée.
-   [Update Management](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management) : cette solution de gestion des mises à jour aide les clients à gérer les mises à jour de sécurité du système d’exploitation, notamment en leur indiquant l’état des mises à jour disponibles et le processus d’installation des mises à jour requises.
-   [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth) : cette solution de contrôle d’intégrité des agents indique le nombre d’agents déployés et leur répartition géographique, ainsi que le nombre d’agents qui ne répondent pas ou qui envoient des données opérationnelles.
-   [Journaux d’activité Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity) : la solution Activity Log Analytics facilite l’analyse des journaux d’activité Azure de tous les abonnements Azure d’un client.
-   [Change Tracking](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity) : cette solution de suivi des modifications permet aux clients d’identifier facilement des modifications dans l’environnement.

### <a name="identity-management"></a>Gestion des identités
Les technologies suivantes fournissent des fonctionnalités de gestion des identités dans l’environnement Azure :
-   [Active Directory (AD)](https://azure.microsoft.com/services/active-directory/) peut être le service mutualisé basé sur le cloud de Microsoft pour la gestion des répertoires et des identités. Tous les utilisateurs de la solution ont été créés dans Azure Active Directory, y compris ceux qui accèdent à la base de données SQL.
-   L’authentification auprès de l’application est effectuée à l’aide d’Azure AD. Pour plus d’informations, consultez [Intégration d’applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). En outre, le chiffrement des colonnes de base de données utilise Azure AD pour authentifier l’application auprès d’Azure SQL Database. Pour plus d’informations, découvrez comment [protéger les données sensibles dans SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   La solution [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) détecte les vulnérabilités risquant d’affecter les identités d’une organisation, configure des réponses automatiques aux actions suspectes détectées en lien avec les identités d’une organisation, et examine les incidents suspects afin de prendre les mesures appropriées pour résoudre ces derniers.
-   Le [contrôle d’accès en fonction du rôle (RBAC) Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permet une gestion précise de l’accès pour Azure. Par exemple, l’accès aux abonnements est limité à l’administrateur d’abonnements.

Pour plus d’informations sur l’utilisation des fonctionnalités de sécurité d’Azure SQL Database, consultez l’exemple [Contoso Clinic demo application](https://github.com/Microsoft/azure-sql-security-sample) (Application de démonstration Contoso Clinic).

### <a name="security"></a>Sécurité
**Gestion des secrets** : la solution utilise [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pour gérer les clés et les secrets. Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud.

**Protection contre les programmes malveillants** : [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) pour Machines Virtuelles fournit une protection en temps réel qui permet d’identifier et de supprimer les virus, les logiciels espions et autres logiciels malveillants grâce à des alertes configurables vous avertissant quand des logiciels malveillants ou indésirables connus tentent de s’installer ou de s’exécuter sur des machines virtuelles protégées.

**Gestion des correctifs** : les machines virtuelles Windows déployées dans le cadre de cette architecture de référence sont configurées par défaut pour recevoir des mises à jour automatiques du service Windows Update. Cette solution inclut également le service [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) qui permet de créer des déploiements mis à jour pour corriger des machines virtuelles si nécessaire.


## <a name="guidance-and-recommendations"></a>Instructions et recommandations
### <a name="expressroute-and-vpn"></a>ExpressRoute et VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou un tunnel VPN sécurisé doivent être configurés pour établir une connexion sûre aux ressources déployées dans le cadre de cette architecture de référence d’entrepôt de données. Étant donné que les connexions ExpressRoute ne transitent pas par Internet, elles offrent une plus grande fiabilité, des débits plus importants, des latences moindres et une sécurité accrue par rapport aux connexions classiques sur Internet. En configurant correctement un VPN ou un routage ExpressRoute, les clients peuvent ajouter une couche de protection au niveau des données en transit.

### <a name="extract-transform-load-etl-process"></a>Processus ETL (extraction, transformation et chargement)
La technologie [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) peut charger des données dans Azure SQL Data Warehouse sans avoir besoin de recourir à un processus ETL ou à un outil d’importation distincts. PolyBase permet d’accéder aux données par le biais de requêtes T-SQL. Il est possible d’utiliser PolyBase avec une pile de décisionnel et d’analyse Microsoft, ainsi qu’avec des outils tiers compatibles avec SQL Server.

### <a name="azure-active-directory-setup"></a>Installation d’Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) joue un rôle essentiel dans la gestion du déploiement et de la configuration des accès des membres du personnel qui interagissent avec l’environnement. Une instance Windows Server Active Directory existante peut être intégrée à AAD en [quatre clics](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Les clients peuvent également associer l’infrastructure (contrôleurs de domaine) Active Directory déployée à un service AAD existant en définissant cette infrastructure comme sous-domaine d’une forêt AAD.

### <a name="additional-services"></a>Services supplémentaires
Bien que cette architecture d’entrepôt de données ne soit pas conçue pour être déployée dans l’environnement [Azure Commercial](https://azure.microsoft.com/overview/what-is-azure/), des résultats similaires peuvent être obtenus en utilisant les services décrits dans cette architecture de référence, ainsi que par le biais de services uniquement disponibles dans l’environnement Azure Commercial. Veuillez noter qu’Azure Commercial conserve une autorisation provisoire d’exploitation (P-ATO) JAB FedRamp au niveau Moderate Impact (impact modéré), ce qui permet aux partenaires et organismes gouvernementaux de déployer de façon modérée des informations sensibles dans le cloud en utilisant l’environnement Azure Commercial.

Azure Commercial propose un large éventail de services qui gèrent le stockage de données mises en forme ou non, ainsi que leur mise en lots pour qu’elles puissent être utilisées dans l’entrepôt de données. Ces services incluent notamment :
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction), qui est un service cloud géré créé pour des projets complexes d’extraction, de transformation et de chargement (ETL), d’extraction, de chargement et de transformation (ELT) et d’intégration des données. Il permet aux clients de créer et de planifier des workflows pilotés par les données, nommés pipelines, qui ingèrent des données provenant de différents magasins de données. Les clients peuvent ensuite traiter et transformer les données pour les stocker dans des magasins de données comme Azure SQL Data Warehouse.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview), qui permet de capturer les données quels que soient la taille, le type et la vitesse d’ingestion dans un emplacement unique en vue d’une analyse opérationnelle et exploratoire. Azure Data Lake Store est compatible avec la plupart des composants open source dans l’écosystème Hadoop et s’intègre facilement à d’autres services Azure comme Azure SQL Data Warehouse.

## <a name="threat-model"></a>Modèle de menace

Le diagramme de flux de données (DFD) pour cette architecture de référence figure ci-dessous et est également disponible en [téléchargement](https://aka.ms/blueprintdwthreatmodel) :

![Modèle de menace Data Warehouse pour FedRAMP](images/fedramp-datawarehouse-threat-model.png?raw=true "Modèle de menace Data Warehouse pour FedRAMP")

## <a name="compliance-documentation"></a>Documentation sur la conformité
Le document [Azure Security and Compliance Blueprint – FedRAMP High Customer Responsibility Matrix](https://aka.ms/blueprinthighcrm) (Azure Blueprint Sécurité et conformité - Matrice de responsabilités des clients FedRAMP High) répertorie tous les contrôles de sécurité requis par la ligne de base FedRAMP High. De la même façon, le document [Azure Security and Compliance Blueprint – FedRAMP Moderate Customer Responsibility Matrix](https://aka.ms/blueprintcrmmod) (Azure Blueprint Sécurité et conformité - Matrice de responsabilités des clients FedRAMP Moderate) répertorie tous les contrôles de sécurité requis par la ligne de base FedRAMP Moderate. Ces deux documents indiquent si l’implémentation de chacun des contrôles revient à Microsoft, au client ou aux deux.

Les documents [Azure Security and Compliance Blueprint - FedRAMP High Control Implementation Matrix](https://aka.ms/blueprintdwcimhigh) (Azure Blueprint Sécurité et conformité - Matrice d’implémentation de contrôle FedRAMP High) et [Azure Security and Compliance Blueprint - FedRAMP Moderate Control Implementation Matrix](https://aka.ms/blueprintdwcimmod) (Azure Blueprint Sécurité et conformité - Matrice d’implémentation de contrôle FedRAMP Moderate) fournissent des informations sur les contrôles couverts par l’architecture d’entrepôt de données pour chaque ligne de base FedRAMP, y compris des descriptions détaillées sur la façon dont l’implémentation satisfait aux exigences de chaque contrôle couvert.

## <a name="disclaimer"></a>Clause d'exclusion de responsabilité

 - Ce document est fourni à titre d’information uniquement. MICROSOFT N’ACCORDE AUCUNE GARANTIE EXPRESSE, IMPLICITE OU STATUTAIRE EN LIEN AVEC LES INFORMATIONS CONTENUES DANS CE DOCUMENT. Ce document est fourni « en l’état ». Les informations et les points de vue exprimés dans ce document, y compris les URL et autres références à des sites web, peuvent être modifiés sans préavis. Les clients qui lisent ce document assument les risques liés à son utilisation.
 - Ce document n’accorde aux clients aucun droit légal de propriété intellectuelle sur les produits ou solutions Microsoft.
 - Les clients peuvent copier et utiliser ce document pour un usage interne, à titre de référence.
 - Certaines recommandations contenues dans ce document peuvent entraîner une augmentation des taux d’utilisation des données, des réseaux ou des ressources de calcul dans Azure, débouchant sur des coûts de licence ou d’abonnement supplémentaires.
 - Cette architecture constitue une base que les clients peuvent modifier en fonction de leurs besoins. Elle ne doit pas être utilisée telle quelle dans un environnement de production.
 - Développé en tant que référence, ce document ne doit pas être utilisé pour définir tous les moyens par lesquels un client peut satisfaire à des réglementations et exigences de conformité spécifiques. Les clients doivent rechercher une assistance juridique auprès de leur organisation pour connaître les implémentations client approuvées.
