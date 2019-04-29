---
title: 'Blueprint sur la sécurité et la conformité Azure : application web IaaS pour NIST SP 800-171'
description: 'Blueprint sur la sécurité et la conformité Azure : application web IaaS pour NIST SP 800-171'
services: security
author: jomolesk
ms.assetid: 1f1ad27f-32c3-4e76-abb9-ea768d01747f
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: b30094e264086f018acbf84144300df46c60ac4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610266"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-nist-sp-800-171"></a>Blueprint sur la sécurité et la conformité Azure : application web IaaS pour NIST SP 800-171

## <a name="overview"></a>Présentation
[NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) fournit des instructions pour protéger les informations non classifiées contrôlées qui se trouvent dans des systèmes d’information et des organisations qui ne font pas partie du secteur public. NIST SP 800-171 établit 14 familles d’exigences de sécurité pour protéger la confidentialité des informations non classifiées contrôlées.

Ce blueprint sur la sécurité et la conformité d’Azure fournit des conseils pour aider les clients à déployer une architecture d’application web dans Azure qui implémente une partie des contrôles NIST SP 800-171. Cette solution montre comment les clients peuvent répondre à des exigences de conformité et de sécurité spécifiques. Elle sert également de base pour les clients qui souhaitent générer et configurer leurs propres applications web dans Azure.

Cette architecture de référence, ainsi que le guide d‘implémentation et le modèle de menace associés, sont destinés à servir de base aux clients qui doivent les adapter à leurs besoins spécifiques. Ils ne doivent pas être utilisés tels quels dans un environnement de production. Le déploiement de cette architecture sans modification est insuffisant pour répondre entièrement aux exigences de NIST SP 800-171. Il incombe aux clients d‘effectuer des contrôles de conformité et de sécurité appropriés de toute solution générée à l‘aide de cette architecture. Les exigences peuvent varier selon les spécificités de l‘implémentation de chaque client.

## <a name="architecture-diagram-and-components"></a>Diagramme et composants de l’architecture
Ce blueprint de sécurité et de conformité Azure déploie une architecture de référence pour une application web IaaS avec un backend SQL Server. L’architecture inclut une couche web, une couche Données, une infrastructure Active Directory, Azure Application Gateway et Azure Load Balancer. Les machines virtuelles (VM) déployées sur les couches web et Données sont configurées dans un groupe à haute disponibilité. Les instances SQL Server sont configurées dans un groupe de disponibilité Always On pour la haute disponibilité. Les machines virtuelles sont jointes au domaine. Des stratégies de groupe Active Directory appliquent des configurations de sécurité et de conformité au niveau du système d’exploitation.

L’ensemble de la solution repose sur le service Stockage Azure, que les clients configurent à partir du portail Azure. Le service Stockage chiffre toutes les données à l’aide de la fonctionnalité Storage Service Encryption pour garantir la confidentialité des données au repos. Le stockage géoredondant garantit qu’un événement indésirable survenu dans le centre de données principal du client n’occasionne aucune perte de données. Une seconde copie est stockée à un autre emplacement distant de plusieurs centaines de kilomètres.

Pour renforcer la sécurité, toutes les ressources dans cette solution sont gérées sous forme de groupe de ressources dans Azure Resource Manager. Le contrôle d‘accès en fonction du rôle Azure Active Directory (Azure AD) permet de contrôler l‘accès aux ressources déployées et les clés dans Azure Key Vault. L’intégrité du système est surveillée dans Azure Monitor. Les clients configurent ces deux services de supervision pour capturer les journaux d’activité. L‘intégrité du système est affichée dans un tableau de bord unique qui est facile à utiliser.

Un hôte bastion de gestion fournit une connexion sécurisée permettant aux administrateurs d’accéder aux ressources déployées. *Microsoft vous recommande de configurer une connexion VPN ou Azure ExpressRoute pour la gestion et l‘importation de données dans le sous-réseau de l‘architecture de référence.*


![Diagramme de l’architecture de référence des applications web IaaS pour NIST SP 800-171](images/nist171-iaaswa-architecture.png "Diagramme de l’architecture de référence des applications web IaaS pour NIST SP 800-171")

Cette solution utilise les services Azure suivants. Pour plus d‘informations, consultez la section sur l‘[architecture de déploiement](#deployment-architecture).

- Machines virtuelles Azure
    - (1) Gestion/bastion (Windows Server 2016 Datacenter)
    - (2) Contrôleur de domaine Active Directory (Windows Server 2016 Datacenter)
    - (2) Nœud de cluster SQL Server (SQL Server 2017 sur Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Réseau virtuel Azure
    - (1) réseau /16
    - (5) réseaux /24
    - (5) Groupes de sécurité réseau
- Groupes à haute disponibilité
    - (1) Contrôleurs de domaine Active Directory
    - (1) Nœuds de cluster SQL
    - (1) Web/IIS
- Azure Application Gateway
    - (1) Pare-feu d’applications web
        - Mode de pare-feu : prévention
        - Ensemble de règles : OWASP 3.0
        - Écouteur : 443
- Azure Active Directory
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor (journaux)
- Azure Resource Manager
- Azure Security Center
- Stockage Azure
- Azure Automation
- Témoin cloud
- Coffre Recovery Services

## <a name="deployment-architecture"></a>Architecture de déploiement
La section ci-après décrit en détail les éléments nécessaires au déploiement et à l’implémentation.

**Hôte bastion** : L’hôte bastion est le seul point d’entrée que les utilisateurs peuvent utiliser pour accéder aux ressources déployées dans cet environnement. L‘hôte bastion fournit une connexion sécurisée à des ressources déployées en autorisant uniquement le trafic distant provenant d‘adresses IP publiques figurant sur une liste verte. Pour autoriser le trafic Bureau à distance, la source du trafic doit être définie dans le groupe de sécurité réseau (NSG).

Cette solution crée une machine virtuelle en tant qu‘hôte bastion joint au domaine avec les configurations suivantes :
-   [Extension Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware).
-   [Extension Azure Diagnostics](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) avec Key Vault.
-   [Stratégie d‘arrêt automatique](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) pour réduire la consommation des ressources de machine virtuelle non utilisées.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) est activé pour que les informations d‘identification et autres secrets s‘exécutent dans un environnement protégé, isolé du système d‘exploitation exécuté.

### <a name="virtual-network"></a>Réseau virtuel
L’architecture définit un réseau privé virtuel avec l’espace d’adressage 10.200.0.0/16.

**Groupes de sécurité réseau** : Cette solution déploie des ressources dans une architecture avec des sous-réseaux distincts pour le web, base de données, Active Directory et gestion à l’intérieur d’un réseau virtuel. Les sous-réseaux sont séparés logiquement par des règles de groupe de sécurité réseau appliquées aux sous-réseaux individuels. Ces règles limitent le trafic entre les sous-réseaux au seul trafic requis pour les fonctionnalités système et de gestion.

Consultez la configuration des [groupes de sécurité réseau](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) déployés avec cette solution. Les organisations peuvent configurer des groupes de sécurité réseau en modifiant le fichier précédent et en suivant [cette documentation](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).

Chaque sous-réseau a un groupe de sécurité réseau (NSG) dédié :
- Un NSG pour Application Gateway (LBNSG)
- Un NSG pour l’hôte bastion (MGTNSG)
- Un NSG pour les contrôleurs de domaine principaux et secondaires (ADNSG)
- Un NSG pour les serveurs SQL Server et le témoin cloud (SQLNSG)
- Un NSG pour la couche web (WEBNSG)

### <a name="data-in-transit"></a>Données en transit
Par défaut, Azure chiffre toutes les communications avec les centres de données Azure. De plus, toutes les transactions en direction du Stockage passant par le portail Azure se produisent via HTTPS.

### <a name="data-at-rest"></a>Données au repos
L’architecture protège les données au repos par le biais de plusieurs mesures. Ces mesures incluent le chiffrement et l’audit de la base de données.

**Stockage Azure** : pour satisfaire aux exigences de chiffrement des données au repos, l’ensemble du service [Stockage](https://azure.microsoft.com/services/storage/) utilise la fonctionnalité de chiffrement [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Cette fonctionnalité permet de protéger et de sauvegarder les données dans le respect des engagements de l’organisation en matière de sécurité et des exigences de conformité définies par NIST SP 800-171.

**Azure Disk Encryption** : Chiffrement de disque est utilisé pour les disques de machine virtuelle IaaS de Windows chiffrés. [Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utilise la fonctionnalité BitLocker de Windows pour fournir le chiffrement des volumes des disques de données et du système d’exploitation. La solution est intégrée à Key Vault pour faciliter le contrôle et la gestion des clés de chiffrement de disque.

**SQL Server** : l’instance SQL Server utilise les mesures suivantes pour la sécurité des bases de données :
-   [L’audit SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) suit les événements de base de données et les écrit dans des journaux d’audit.
-   [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) effectue le chiffrement et le déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux de transactions afin de protéger les informations au repos. Transparent Data Encryption garantit que les données stockées ne font pas l’objet d’accès non autorisés.
-   Des [règles de pare-feu](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) empêchent tout accès aux serveurs de base de données sans autorisation appropriée. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande.
-   Les [colonnes chiffrées](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) garantissent que les données sensibles n’apparaissent jamais en texte clair à l’intérieur du système de base de données. Une fois le chiffrement des données activé, seules des applications clientes ou des serveurs d’applications ayant accès aux clés peuvent accéder aux données en texte clair.
- [Dynamic Data Masking](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) limite l’exposition des données sensibles en masquant celles-ci aux utilisateurs ou aux applications dépourvus de privilèges. Il peut détecter automatiquement les données potentiellement sensibles et suggérer les masques appropriés à appliquer. Dynamic Data Masking permet de réduire l’accès afin que les données sensibles ne sortent pas de la base de données via un accès non autorisé. *Les clients doivent ajuster les paramètres pour respecter leur schéma de base de données.*

### <a name="identity-management"></a>Gestion des identités
Les technologies suivantes offrent des fonctionnalités de gestion de l’accès aux données dans l’environnement Azure :
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) est le service multilocataire basé sur le cloud de Microsoft pour la gestion des identités et des annuaires. Tous les utilisateurs de cette solution sont créés dans Azure AD et incluent les utilisateurs qui accèdent à l’instance SQL Server.
-   L’authentification auprès de l’application est effectuée à l’aide d’Azure AD. Pour plus d’informations, consultez [Intégrer des applications à Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
-   [RBAC Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permet aux administrateurs de définir des autorisations d’accès affinées pour n’accorder aux utilisateurs que l’accès dont ils ont besoin pour accomplir leur travail. Au lieu d’accorder à tous les utilisateurs des autorisations illimitées sur les ressources Azure, les administrateurs peuvent autoriser seulement certaines actions pour accéder aux données. Par exemple, l’accès aux abonnements est limité à l’administrateur d’abonnements.
- La solution [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) permet aux clients de minimiser le nombre d’utilisateurs qui ont accès à des ressources spécifiques. Les administrateurs peuvent utiliser Azure AD Privileged Identity Management pour découvrir, restreindre et surveiller les identités privilégiées et leur accès aux ressources. Cette fonctionnalité permet également d’appliquer un accès administratif juste-à-temps à la demande, si nécessaire.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) détecte les vulnérabilités potentielles susceptibles d’affecter les identités d’une organisation. Il configure des réponses automatiques aux actions suspectes détectées et liées aux identités d’une organisation. Il examine également les incidents suspects pour prendre les mesures appropriées afin de les résoudre.

### <a name="security"></a>Sécurité
**Gestion des secrets** : La solution utilise [Key Vault](https://azure.microsoft.com/services/key-vault/) pour la gestion des clés et secrets. Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. Les fonctionnalités Key Vault suivantes aident les clients à protéger les données :
- Les stratégies d’accès avancées sont configurées en fonction des besoins.
- Les stratégies d’accès Key Vault sont définies avec des autorisations minimales requises pour les clés et les secrets.
- L’ensemble des clés et secrets figurant dans Key Vault présentent des dates d’expiration.
- Toutes les clés dans Key Vault sont protégées par des modules de sécurité matériels spécialisés. Le type de clé est une clé RSA de 2 048 bits protégée par un module de sécurité matériel.
- Les autorisations minimales requises sont accordées à l’ensemble des utilisateurs et identités à l’aide d’un contrôle d’accès en fonction du rôle (RBAC).
- Les journaux de diagnostics pour Key Vault sont activés avec une période de rétention d’au moins 365 jours.
- Les opérations de chiffrement autorisées pour les clés sont restreintes à celles qui sont nécessaires.
- Cette solution est intégrée à Azure Key Vault pour gérer les secrets et clés de chiffrement de disque des machines virtuelles IaaS.

**Gestion des correctifs** : Les machines virtuelles Windows déployées dans le cadre de cette architecture de référence sont configurés par défaut pour recevoir des mises à jour automatiques du Service de mise à jour de Windows. Cette solution inclut également le service [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) qui permet de créer des déploiements mis à jour pour corriger des machines virtuelles si nécessaire.

**Protection contre les programmes malveillants** : [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) pour les machines virtuelles fournit une fonctionnalité de protection en temps réel que vous aide à identifier et supprimer les virus, logiciels espions et autres logiciels malveillants. Les clients peuvent configurer des alertes qui se déclenchent lorsque des logiciels malveillants ou indésirables connus tentent de s’installer ou de s’exécuter sur des machines virtuelles protégées.

**Azure Security Center** : avec [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), les clients peuvent appliquer et gérer de façon centralisée des stratégies de sécurité sur les charges de travail, limiter l’exposition aux menaces ainsi que détecter les attaques et y répondre. Security Center accède également aux configurations existantes des services Azure pour fournir des suggestions en matière de configuration et de service. Cela contribue à l’amélioration du dispositif de sécurité et à la protection des données.

Security Center utilise diverses fonctionnalités de détection pour avertir les clients des attaques potentielles qui ciblent leurs environnements. Ces alertes fournissent de précieuses informations sur le déclencheur de l’alerte, les ressources ciblées et la source de l’attaque. Security Center possède un ensemble d’[alertes de sécurité prédéfinies](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), qui sont déclenchées en cas de menace ou d’activité suspecte. Les clients peuvent utiliser les [règles d’alerte personnalisées](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) pour définir de nouvelles alertes de sécurité basées sur les données déjà collectées dans leur environnement.

Security Center fournit des alertes et incidents de sécurité classés par ordre de priorité. Security Center permet aux clients de découvrir et de résoudre plus facilement les problèmes de sécurité potentiels. Un [rapport d’informations sur les menaces](https://docs.microsoft.com/azure/security-center/security-center-threat-report) est généré pour chaque menace détectée. Les équipes de réponse aux incidents peuvent utiliser ces rapports lorsqu’elles enquêtent pour corriger des menaces.

Cette architecture de référence utilise la fonction d’[évaluation des vulnérabilités](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) dans Security Center. Une fois sa configuration terminée, un agent partenaire (par exemple, Qualys) commence à signaler les données de vulnérabilité à la plateforme de gestion du partenaire. En retour, la plateforme de gestion du partenaire fournit des données de surveillance de l’intégrité et des vulnérabilités à Security Center. Les clients peuvent utiliser ces informations pour identifier rapidement les machines virtuelles vulnérables.

**Azure Application Gateway** : L’architecture réduit le risque de failles de sécurité à l’aide d’une passerelle d’application avec un pare-feu d’applications web configuré et activé l’ensemble de règles OWASP. Les autres fonctionnalités incluent notamment :

- [SSL de bout en bout](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Activation du [déchargement SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal).
- Désactivation de [TLS versions 1.0 et 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Pare-feu d’applications web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (mode de prévention).
- [Mode de prévention](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) avec l’ensemble de règles OWASP 3.0.
- Activation de la [journalisation des diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Sondes d’intégrité personnalisées](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal).
- [Security Center](https://azure.microsoft.com/services/security-center) et [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fournissent une protection et des notifications supplémentaires. Security Center fournit également un système de réputation.

### <a name="business-continuity"></a>Continuité de l’activité

**Haute disponibilité** : La solution déploie toutes les machines virtuelles dans un [à haute disponibilité](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Les groupes à haute disponibilité veillent à ce que les machines virtuelles soient distribuées sur plusieurs clusters matériels isolés pour améliorer la disponibilité. Au moins une machine virtuelle est disponible pendant un événement de maintenance planifié ou non, ce qui est conforme au contrat de niveau de service Azure garantissant une disponibilité de 99,95 %.

**Coffre Recovery Services**: le [coffre Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) héberge les données de sauvegarde et protège toutes les configurations des machines virtuelles Azure dans cette architecture. Grâce au coffre Recovery Services, les clients peuvent restaurer des fichiers et dossiers d’une machine virtuelle IaaS sans avoir à restaurer l’intégralité de celle-ci. Ce processus accélère les temps de restauration.

**Témoin cloud** : [Témoin de cloud](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) est un type de témoin de quorum de cluster de basculement dans Windows Server 2016 qui utilise Azure comme point d’arbitrage. Comme n’importe quel autre témoin de quorum, le témoin cloud obtient un vote et peut être utilisé dans les calculs de quorum. Il utilise le stockage Blob Azure standard disponible publiquement. Cette organisation élimine le travail de maintenance supplémentaire des machines virtuelles hébergées dans un cloud public.

### <a name="logging-and-auditing"></a>Journalisation et audit

Les services Azure assurent une journalisation complète de l’activité du système et des utilisateurs, ainsi que de l’intégrité du système :
- **Journaux d’activité** : les [journaux d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fournissent des insights sur les opérations ayant été effectuées sur les ressources d’un abonnement. Les journaux d’activité peuvent aider à déterminer l’initiateur, l’heure d’exécution et l’état d’une opération.
- **Journaux de diagnostic** : les [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluent l’ensemble des journaux générés par chaque ressource. Ils incluent les journaux d’activité système des événements Windows, les journaux d’activité de stockage, les journaux d’audit Key Vault, ainsi que les journaux d’activité de pare-feu et d’accès d’Application Gateway. Tous les journaux de diagnostic sont consignés dans un compte de stockage Azure centralisé et chiffré pour l’archivage. Les utilisateurs peuvent configurer la période de rétention jusqu‘à 730 jours, pour répondre à leurs besoins spécifiques.

**Journaux Azure Monitor** : Ces journaux sont consolidés dans [Azure Monitor enregistre](https://azure.microsoft.com/services/log-analytics/) pour le traitement, le stockage et la création de rapports de tableau de bord. Une fois les données collectées, elles sont organisées dans des tables distinctes pour chaque type de données au sein des espaces de travail Log Analytics. De cette façon, toutes les données peuvent être analysées ensemble, quelle que soit leur source d’origine. Security Center s’intègre avec les journaux d’Azure Monitor. Les clients peuvent utiliser des requêtes de Kusto pour accéder à leurs données d’événement de sécurité et de les combiner avec des données provenant d’autres services.

Azure suivant [solutions de surveillance](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) sont inclus dans le cadre de cette architecture :
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment) : la solution Active Directory Health Check permet d’évaluer les risques et l’intégrité des environnements de serveur à intervalles réguliers. Elle fournit une liste hiérarchisée de suggestions propres à l’infrastructure de serveurs déployée.
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment) : la solution SQL Health Check permet d’évaluer les risques et l’intégrité des environnements de serveur à intervalles réguliers. Elle fournit aux clients une liste hiérarchisée de suggestions propres à l’infrastructure de serveurs déployée.
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth) : la solution Agent Health signale le nombre d’agents déployés et leur répartition géographique. Elle signale également le nombre d’agents qui ne répondent pas et le nombre d’agents qui envoient des données opérationnelles.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity) : la solution Activity Log Analytics facilite l’analyse des journaux d’activité Azure de tous les abonnements Azure d’un client.

**Azure Automation** : [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) stocke, exécute et gère les runbooks. Dans cette solution, les runbooks aident à collecter les journaux d’activité de SQL Server. Les clients peuvent utiliser la solution Automation [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) pour identifier facilement les changements dans l’environnement.

**Azure Monitor** : [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) aide les utilisateurs à suivre les performances, à garantir la sécurité et à identifier les tendances. Les organisations peuvent l’utiliser pour auditer, créer des alertes et archiver les données. Elles peuvent également suivre les appels d’API dans leurs ressources Azure.

## <a name="threat-model"></a>Modèle de menace

Le diagramme de flux de données de cette architecture de référence figure ci-dessous et est également disponible en [téléchargement](https://aka.ms/nist171-iaaswa-tm). Ce modèle peut aider les clients à comprendre les points de risque potentiel de l’infrastructure du système lors de l’apport de modifications.

![Modèle de risque des applications web IaaS pour NIST SP 800-171](images/nist171-iaaswa-threat-model.png "Modèle de risque des applications web IaaS pour NIST SP 800-171")

## <a name="compliance-documentation"></a>Documentation sur la conformité

Le [Blueprint sur la sécurité et la conformité Azure - Matrice de responsabilités des clients de NIST SP 800-171](https://aka.ms/nist171-crm) liste tous les contrôles de sécurité exigés par NIST SP 800-171. La matrice indique si l’implémentation de chaque contrôle revient à Microsoft, au client, ou est partagé entre les deux.

Le [blueprint sur la sécurité et la conformité Azure - Matrice d’implémentation des contrôles des applications web IaaS pour NIST SP 800-171](https://aka.ms/nist171-iaaswa-cim) fournit des informations sur les contrôles de NIST SP 800-171 que prend en compte l’architecture des applications web IaaS. Il comprend une description détaillée de la façon dont l’implémentation répond aux exigences de chaque contrôle couvert.

## <a name="guidance-and-recommendations"></a>Instructions et recommandations
### <a name="vpn-and-expressroute"></a>VPN et ExpressRoute
Un tunnel VPN sécurisé ou un routage [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) doivent être configurés pour établir une connexion sécurisée aux ressources déployées en lien avec cette architecture de référence d’application web IaaS. En configurant correctement un VPN ou un routage ExpressRoute, les clients peuvent ajouter une couche de protection au niveau des données en transit.

En implémentant un tunnel VPN sécurisé avec Azure, il est possible de créer une connexion privée virtuelle entre un réseau local et un réseau virtuel Azure. Cette connexion s’effectue via Internet. Les clients peuvent l’utiliser pour créer un « tunnel » pour le transit sécurisé des informations au sein d’une liaison chiffrée entre leur réseau et Azure. La technologie de VPN de site à site, aussi sécurisée qu’aguerrie, est déployée par des entreprises de toutes tailles depuis des décennies. Le [mode tunnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) est utilisé dans cette option comme un mécanisme de chiffrement.

Étant donné que le trafic à l’intérieur du tunnel VPN transite par Internet avec un VPN de site à site, Microsoft offre une autre option de connexion encore plus sécurisée. ExpressRoute est une liaison réseau étendu dédiée entre Azure et un emplacement local ou un fournisseur d’hébergement Exchange. Les connexions ExpressRoute se raccordent directement au fournisseur de télécommunications du client. Par conséquent, les données ne transitent pas par Internet et ne sont pas exposées à ce dernier. Ces connexions offrent davantage de fiabilité, des vitesses supérieures, des latences inférieures et une sécurité renforcée par rapport aux connexions standard. 

Les meilleures pratiques concernant l’implémentation d’un réseau hybride sécurisé qui étend un réseau local à Azure sont [décrites ici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Clause d'exclusion de responsabilité

- Ce document est fourni à titre d’information uniquement. MICROSOFT N’ACCORDE AUCUNE GARANTIE EXPRESSE, IMPLICITE OU STATUTAIRE EN LIEN AVEC LES INFORMATIONS CONTENUES DANS CE DOCUMENT. Ce document est fourni « en l’état ». Les informations et les points de vue exprimés dans ce document, y compris les URL et autres références à des sites web, peuvent être modifiés sans préavis. Les clients qui lisent ce document assument les risques liés à son utilisation. 
- Ce document n’accorde aux clients aucun droit légal de propriété intellectuelle sur les produits ou solutions Microsoft. 
- Les clients peuvent copier et utiliser ce document pour un usage interne, à titre de référence. 
- Certaines recommandations contenues dans ce document peuvent entraîner une augmentation des taux d’utilisation des données, des réseaux ou des ressources de calcul dans Azure, débouchant sur des coûts de licence ou d’abonnement supplémentaires. 
- Cette architecture constitue une base que les clients peuvent modifier en fonction de leurs besoins. Elle ne doit pas être utilisée telle quelle dans un environnement de production.
- Développé en tant que référence, ce document ne doit pas être utilisé pour définir tous les moyens par lesquels un client peut satisfaire à des réglementations et exigences de conformité spécifiques. Les clients doivent rechercher une assistance juridique auprès de leur organisation pour connaître les implémentations client approuvées.
