---
title: Programme Blueprint Security and Compliance Azure - Application web IaaS pour FedRAMP
description: Programme Blueprint Security and Compliance Azure - Application web IaaS pour FedRAMP
services: security
documentationcenter: na
author: jomolesk
manager: barbkess
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: jomolesk
ms.openlocfilehash: 1ba5b813843ce2f5d31f337ab4d3d94e521b0e0c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57864471"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-fedramp"></a>Blueprint de sécurité et de conformité Azure : Application web IaaS pour FedRAMP

## <a name="overview"></a>Présentation

[FedRAMP (Federal Risk and Authorization Management Program)](https://www.fedramp.gov) est un programme déployé à l’échelle de l’administration américaine, visant à rationaliser l’approche en matière d’évaluation de la sécurité, d’autorisation et de surveillance continue des services et produits cloud. Cette solution Azure Blueprint Security & Compliance fournit des conseils pour le déploiement d’un environnement IaaS (Infrastructure as a Service) conforme à FedRAMP adapté à une application web simple accessible sur Internet. Cette solution automatise le déploiement et la configuration des ressources Azure pour une architecture de référence commune, illustrant diverses façons dont les clients peuvent satisfaire à des exigences de conformité et de sécurité spécifiques, et sert de base aux clients souhaitant générer et configurer leurs propres solutions sur Azure. La solution implémente un sous-ensemble des contrôles définis dans le document FedRAMP High Baseline, basé sur la publication NIST SP 800-53. Pour plus d’informations sur les exigences de FedRAMP et cette solution, consultez la [documentation sur la conformité](#compliance-documentation).
> [!NOTE]
> Cette solution se déploie sur Azure Government.

Cette solution Azure Blueprint Security & Compliance déploie automatiquement une architecture de référence d’application web IaaS avec des contrôles de sécurité préconfigurés pour aider les clients à se conformer aux exigences FedRAMP. La solution se compose de modèles Azure Resource Manager et de scripts PowerShell qui guident le déploiement et la configuration des ressources.

Cette architecture constitue une base que les clients peuvent modifier en fonction de leurs besoins. Elle ne doit pas être utilisée telle quelle dans un environnement de production. Le déploiement d’une application dans cet environnement, sans y apporter de modifications, ne permet pas de répondre entièrement aux exigences du document FedRAMP High Baseline. Notez les points suivants :
- Cette architecture constitue une base de référence qui permet aux clients d’utiliser Azure conformément aux exigences FedRAMP.
- Les clients doivent réaliser des évaluations de sécurité et de conformité pour toute solution créée à l’aide de cette architecture, car les exigences peuvent varier selon leur implémentation.

Pour obtenir une présentation rapide du fonctionnement de cette solution, regardez cette [vidéo](https://aka.ms/fedrampblueprintvideo) qui explique son déploiement.

Cliquez [ici](https://aka.ms/fedrampblueprintrepo) pour obtenir des instructions de déploiement.

## <a name="architecture-diagram-and-components"></a>Diagramme et composants de l’architecture
Cette solution déploie une architecture de référence pour une application web IaaS avec un serveur principal SQL Server. L’architecture inclut une couche web, une couche données, une infrastructure Active Directory, une passerelle d’application et un équilibreur de charge. Les machines virtuelles déployées sur les couches web et données sont configurées dans un groupe à haute disponibilité, tandis que les instances de SQL Server sont configurées dans un groupe de disponibilité AlwaysOn pour une haute disponibilité. Les machines virtuelles sont jointes au domaine, et des stratégies de groupe Active Directory sont utilisées pour appliquer des configurations de sécurité et de conformité au niveau du système d’exploitation. Un hôte bastion fournit une connexion sécurisée permettant aux administrateurs d’accéder aux ressources déployées. **Azure recommande de configurer une connexion VPN ou Azure ExpressRoute pour la gestion et l’importation de données dans le sous-réseau de l’architecture de référence.**

![Diagramme de l’architecture de référence Application web IaaS pour FedRAMP](images/fedramp-iaaswa-architecture.png?raw=true "Diagramme de l’architecture de référence Application web IaaS pour FedRAMP")

Cette solution utilise les services Azure suivants. Les informations détaillées concernant l’architecture de déploiement se trouvent dans la section [Architecture de déploiement](#deployment-architecture).

- Machines virtuelles Azure
    - (1) Hôte bastion (Windows Server 2016 Datacenter)
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
        - Ensemble de règles : OWASP 3.0
        - écouteur : port 443
- Stockage Azure
    - (7) Comptes de stockage géoredondant
- Azure Cloud Witness
- Coffre Recovery Services
- Azure Key Vault
- Azure Active Directory (Azure AD)
- Azure Resource Manager
- Azure Monitor (journaux)

## <a name="deployment-architecture"></a>Architecture de déploiement

La section suivante décrit en détail les éléments de développement et d’implémentation.

**Hôte bastion** : un hôte bastion désigne le point de contact unique qui fournit une connexion sécurisée permettant aux administrateurs d’accéder aux ressources déployées. Le groupe de sécurité réseau de l’hôte bastion autorise uniquement les connexions au port TCP 3389 du protocole RDP. Les clients peuvent également configurer l’hôte bastion afin de répondre aux exigences de renforcement du système de l’organisation.

### <a name="virtual-network"></a>Réseau virtuel
L’architecture définit un réseau privé virtuel avec l’espace d’adressage 10.200.0.0/16.

**Groupes de sécurité réseau** : Cette solution déploie des ressources dans une architecture où figurent un sous-réseau web, un sous-réseau de base de données, un sous-réseau Active Directory et un sous-réseau de gestion à l’intérieur d’un réseau virtuel. Des règles de groupe de sécurité réseau appliquées aux différents sous-réseaux permettent de séparer ces derniers logiquement afin de limiter le trafic entre eux au seul trafic nécessaire pour les fonctionnalités système et de gestion.

Consultez la configuration des [groupes de sécurité réseau](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) déployés avec cette solution. Les clients peuvent configurer des groupes de sécurité réseau en modifiant le fichier ci-dessus et en suivant [cette documentation](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).

Chaque sous-réseau dispose d’un groupe de sécurité réseau (NSG) dédié :
- 1 NSG pour la passerelle d’application (LBNSG)
- 1 NSG pour l’hôte bastion (MGTNSG)
- 1 NSG pour les contrôleurs de domaine principaux et secondaires (ADNSG)
- 1 NSG pour les serveurs SQL (SQLNSG)
- 1 NSG pour la couche web (WEBNSG)

**Sous-réseaux** : Chaque sous-réseau est associé à son groupe de sécurité réseau correspondant.

### <a name="data-at-rest"></a>Données au repos

L’architecture protège les données au repos à l’aide de plusieurs mesures de chiffrement.

**Stockage Azure** : Pour répondre aux exigences du chiffrement des données au repos, l’ensemble des comptes de stockage utilisent le [chiffrement du service de stockage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

**SQL Server** : SQL Server est configuré pour utiliser [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), qui effectue le chiffrement et le déchiffrement en temps réel des données et fichiers journaux pour protéger les informations au repos. TDE protège les données des accès non autorisés.

Les clients peuvent également configurer les mesures de sécurité SQL Server suivantes :
-   La solution [d’authentification et d’autorisation Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permet la gestion des identités des utilisateurs de bases de données et d’autres services Microsoft dans un emplacement central.
-   L’[audit Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) suit les événements de base de données et consigne ceux-ci dans un journal d’audit conservé dans un compte de stockage Azure.
-   Des [règles de pare-feu](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) empêchent tout accès aux serveurs de base de données sans autorisation appropriée. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande.
-   La [détection des menaces SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permet de détecter et traiter les menaces potentielles à mesure qu’elles surviennent en déclenchant des alertes de sécurité en cas d’activités de base de données suspectes, de vulnérabilités potentielles, d’attaques par injection de code SQL et de modèles d’accès anormaux aux bases de données.
-   Des [colonnes Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantissent que les données sensibles n’apparaissent jamais en tant que texte en clair à l’intérieur du système de base de données. Une fois le chiffrement des données activé, seules des applications clientes ou des serveurs d’applications ayant accès aux clés peuvent accéder aux données en texte clair.
-   Le [masquage de données dynamiques SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) peut être effectué après le déploiement de l’architecture de référence. Les clients doivent ajuster les paramètres de masquage de données dynamiques pour respecter leur schéma de base de données.

**Azure Disk Encryption** : Azure Disk Encryption permet de chiffrer les disques de machines virtuelles IaaS Windows. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) exploite la fonctionnalité BitLocker de Windows pour fournir le chiffrement des volumes des disques de données et du système d’exploitation. La solution est intégrée à Azure Key Vault pour faciliter le contrôle et la gestion des clés de chiffrement de disque.

### <a name="identity-management"></a>Gestion des identités

Les technologies suivantes fournissent des fonctionnalités de gestion des identités dans l’environnement Azure :
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) est le service Microsoft de gestion des annuaires et des identités, basé sur le cloud, multilocataire.
- L’authentification auprès d’une application web déployée par le client peut être effectuée à l’aide d’Azure AD. Pour plus d’informations, consultez [Intégration d’applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- Le [contrôle d’accès en fonction du rôle (RBAC) Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) permet une gestion précise de l’accès pour Azure. L’accès aux abonnements est limité à l’administrateur des abonnements, tandis que l’accès aux ressources peut être limité en fonction du rôle d’utilisateur.
- Une instance Active Directory IaaS déployée fournit la gestion des identités au niveau du système d’exploitation pour les machines virtuelles IaaS déployées.

### <a name="security"></a>Sécurité
**Gestion des secrets** : la solution utilise [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pour gérer les clés et les secrets. Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. Azure Key Vault aide à gérer les secrets et clés de chiffrement de disque de machine virtuelle IaaS de cette architecture de référence.

**Gestion des correctifs** : Les machines virtuelles Windows déployées par cette solution Azure Security and Compliance Blueprint Automation sont configurées par défaut pour recevoir des mises à jour automatiques du service Windows Update. Cette solution déploie également la solution Azure Automation par le biais de laquelle des déploiements de mise à jour peuvent être créés pour déployer les correctifs sur les serveurs Windows si nécessaire.

**Protection contre les programmes malveillants** : [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) pour Machines Virtuelles fournit une protection en temps réel qui permet d’identifier et de supprimer les virus, les logiciels espions et autres logiciels malveillants grâce à des alertes configurables vous avertissant quand des logiciels malveillants ou indésirables connus tentent de s’installer ou de s’exécuter sur des machines virtuelles protégées.

**Application Gateway** : L’architecture réduit le risque de failles de sécurité à l’aide d’Application Gateway et de son pare-feu d’applications web (WAF), dans lequel est activé l’ensemble de règles OWASP. Les autres fonctionnalités incluent notamment :

- [SSL de bout en bout](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Activation du [déchargement SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Désactivation de [TLS versions 1.0 et 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Pare-feu d’application web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (mode WAF)
- [Mode Prévention](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) avec l’ensemble de règles OWASP 3.0

### <a name="business-continuity"></a>Continuité de l’activité

**Haute disponibilité** : Au moins une machine virtuelle est disponible pendant un événement de maintenance planifié ou non, ce qui est conforme au contrat de niveau de service Azure garantissant une disponibilité de 99,95 %. La solution déploie toutes les machines virtuelles de la couche web et données sur un [groupe à haute disponibilité](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Les groupes à haute disponibilité veillent à ce que les machines virtuelles soient distribuées sur plusieurs clusters matériels isolés pour améliorer la disponibilité. De plus, la solution déploie les machines virtuelles SQL Server sur un groupe à haute disponibilité en tant que [groupe de disponibilité AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). La fonctionnalité de groupe de disponibilité Always On fournit des fonctionnalités de haute disponibilité et de récupération d’urgence.

**Coffre Recovery Services** : le [coffre Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) héberge les données de sauvegarde et protège toutes les configurations des machines virtuelles Azure dans cette architecture. Grâce au coffre Recovery Services, les clients peuvent restaurer des fichiers et dossiers d’une machine virtuelle IaaS sans avoir à restaurer l’intégralité de celle-ci, ce qui permet d’accélérer les temps de restauration.

**Témoin cloud** : le [Témoin cloud](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) est un type de témoin de quorum de cluster de basculement dans Windows Server 2016, qui utilise Azure comme point d’arbitrage. Comme n’importe quel autre témoin de quorum, le Témoin cloud obtient un vote et peut être utilisé dans les calculs de quorum, mais il utilise le stockage Blob Azure standard disponible publiquement. Cela élimine le travail de maintenance supplémentaire des machines virtuelles hébergées dans un cloud public.

### <a name="logging-and-auditing"></a>Journalisation et audit

Journaux d’Azure Monitor fournit une journalisation complète de l’activité système et utilisateur, ainsi que l’intégrité du système. Le [Azure Monitor enregistre](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) solution collecte et analyse les données générées par les ressources dans Azure et environnements locaux.

- **Journaux d’activité :**  les [journaux d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fournissent des insights sur les opérations ayant été effectuées sur les ressources d’un abonnement. Les journaux d’activité peuvent aider à déterminer l’initiateur, l’heure d’exécution et l’état d’une opération.
- **Journaux de diagnostic :**  Les [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) correspondent à l’ensemble des journaux émis par chaque ressource. Ces journaux incluent les journaux système des événements Windows, les journaux de stockage Azure, les journaux d’audit Key Vault, ainsi que les journaux de pare-feu et d’accès Application Gateway.
- **Archivage des journaux** :  Tous les journaux de diagnostic sont consignés dans un compte de stockage Azure centralisé et chiffré pour l’archivage. L’utilisateur peut configurer la rétention jusqu’à 730 jours pour répondre aux exigences de rétention spécifiques de l’entreprise. Ces journaux se connecter aux journaux d’Azure Monitor pour traitement, de stockage et les rapports de tableau de bord.

Les solutions de surveillance suivantes sont également incluses dans cette architecture. Notez qu’il incombe au client de configurer ces solutions pour s’aligner avec les contrôles de sécurité FedRAMP :
-   [AD Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment) : la solution Active Directory Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit une liste hiérarchisée de suggestions spécifiques pour l’infrastructure de serveur déployée.
-   [Antimalware Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware) : la solution de logiciel anti-programme malveillant signale les programmes malveillants, les menaces et l’état de protection.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) : la solution Azure Automation stocke, exécute et gère les runbooks.
-   [Security and Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) : le tableau de bord Security and Audit donne une vue d’ensemble de l’état de sécurité des ressources en affichant des métriques concernant des domaines de sécurité, des problèmes notables, des détections, des menaces et des requêtes de sécurité courantes.
-   [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment) : la solution SQL Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit aux clients une liste hiérarchisée de suggestions spécifiques pour l’infrastructure de serveur déployée.
-   [Update Management](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management) : cette solution de gestion des mises à jour aide les clients à gérer les mises à jour de sécurité du système d’exploitation, notamment en leur indiquant l’état des mises à jour disponibles et le processus d’installation des mises à jour requises.
-   [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth) : cette solution de contrôle d’intégrité des agents indique le nombre d’agents déployés et leur répartition géographique, ainsi que le nombre d’agents qui ne répondent pas ou qui envoient des données opérationnelles.
-   [Journaux d’activité Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity) : la solution Activity Log Analytics facilite l’analyse des journaux d’activité Azure de tous les abonnements Azure d’un client.
-   [Change Tracking](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity) : cette solution de suivi des modifications permet aux clients d’identifier facilement des modifications dans l’environnement.

**Azure Monitor**
la solution [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) permet aux utilisateurs de suivre les performances, de tenir à jour la sécurité et d’identifier les tendances en permettant aux entreprises d’auditer, de créer des alertes et d’archiver des données, notamment en lien avec le suivi des appels d’API dans les ressources Azure des clients.

## <a name="threat-model"></a>Modèle de menace
Le diagramme de flux de données de cette architecture de référence figure ci-dessous et est également disponible en [téléchargement](https://aka.ms/fedrampWAdfd). Ce modèle peut aider les clients à comprendre les points de risque potentiel de l’infrastructure du système lors de l’apport de modifications.

![Modèle de menace Application web IaaS pour FedRAMP](images/fedramp-iaaswa-threat-model.png?raw=true "Modèle de menace Application web IaaS pour FedRAMP")

## <a name="compliance-documentation"></a>Documentation sur la conformité

Le document [Azure Blueprint Sécurité et conformité - Matrice de responsabilités des clients FedRAMP High](https://aka.ms/blueprinthighcrm) répertorie tous les contrôles de sécurité requis par la ligne de base FedRAMP High. La matrice indique si l’implémentation de chacun des contrôles revient à Microsoft, au client ou aux deux.

Le document [Azure Blueprint Sécurité et conformité - Matrice de mise en œuvre des contrôles des applications web IaaS FedRAMP High](https://aka.ms/blueprintwacim) répertorie tous les contrôles de sécurité requis par la ligne de base FedRAMP High. La matrice indique quels contrôles sont couverts par l’architecture d’application web IaaS, et inclut une description détaillée de la façon dont l’implémentation est conforme aux exigences de chacun des contrôles.

## <a name="deploy-the-solution"></a>Déployer la solution

Cette solution Azure Security and Compliance Blueprint Automation se compose de fichiers de configuration JSON et de scripts PowerShell qui sont gérés par le service d’API d’Azure Resource Manager pour déployer des ressources dans Azure. Des instructions détaillées sur le déploiement sont disponibles [ici](https://aka.ms/fedrampblueprintrepo).
> [!NOTE]
> Cette solution se déploie sur Azure Government.

#### <a name="quickstart"></a>Démarrage rapide
1. Clonez ou téléchargez [ce dépôt GitHub](https://aka.ms/fedrampblueprintrepo) sur votre station de travail locale.

2. Exécutez le script PowerShell de prédéploiement : azure-blueprint/predeploy/Orchestration_InitialSetup.ps1.

3. Cliquez sur le bouton ci-dessous, connectez-vous au portail Azure, entrez les paramètres de modèle ARM requis, puis cliquez sur **Acheter**.

    [![Déployer sur Azure](https://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

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
