---
title: Programme Blueprint Security and Compliance Azure - Application web PaaS pour FedRAMP
description: Programme Blueprint Security and Compliance Azure - Application web PaaS pour FedRAMP
services: security
author: jomolesk
ms.assetid: 41570fc1-4d74-48ed-afb0-ef1be857029e
ms.service: security
ms.topic: article
ms.date: 06/01/2018
ms.author: jomolesk
ms.openlocfilehash: 9bfd748e6b10106587e3fce64e15d7f1f6d50e59
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54261585"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-fedramp"></a>Blueprint de sécurité et de conformité Azure : Application web PaaS pour FedRAMP

## <a name="overview"></a>Vue d’ensemble

[FedRAMP (Federal Risk and Authorization Management Program)](https://www.fedramp.gov/) est un programme déployé à l’échelle de l’administration américaine, visant à rationaliser l’approche en matière d’évaluation de la sécurité, d’autorisation et de surveillance continue des services et produits cloud. Ce document Azure Blueprint Security and Compliance vous fournit des instructions pour proposer une architecture de plateforme en tant que service (PaaS) Microsoft Azure permettant d’implémenter un sous-ensemble de contrôles FedRAMP High. Cette solution fournit des instructions pour le déploiement et la configuration des ressources Azure pour une architecture de référence commune, illustrant diverses façons dont les clients peuvent satisfaire à des exigences de conformité et de sécurité spécifiques, et sert de base aux clients souhaitant générer et configurer leurs propres solutions sur Azure.

Cette architecture de référence, ainsi que le guide de mise en œuvre du contrôle associé et les modèles de menace, sont destinés à servir de base aux clients qui doivent les adapter à leurs besoins spécifiques et non les utiliser tels quels dans un environnement de production. Le déploiement d’une application dans cet environnement, sans y apporter de modifications, ne permet pas de répondre entièrement aux exigences de la ligne de base FedRAMP High. Notez les points suivants :
- L’architecture constitue un point de départ pour aider les clients à déployer des charges de travail sur Azure d’une manière conforme à FedRAMP.
- Il incombe aux clients d’évaluer de façon appropriée la sécurité et la conformité de toute solution basée sur cette architecture, car les exigences peuvent varier en fonction des spécificités de chaque implémentation.

## <a name="architecture-diagram-and-components"></a>Diagramme et composants de l’architecture
Cette solution fournit une architecture de référence pour une application web PaaS avec un serveur principal Azure SQL Database. L’application web est hébergée dans un environnement Azure App Service dédié, privé et isolé dans un centre de données Azure. L’environnement équilibre la charge du trafic généré par l’application web entre des machines virtuelles gérées par Azure. Cette architecture inclut également des groupes de sécurité réseau, une passerelle d’application (Azure Application Gateway), un DNS (Azure DNS) et un équilibreur de charge (Azure Load Balancer). En outre, Azure Monitor fournit une analyse en temps réel de l’intégrité du système. **Azure recommande de configurer une connexion VPN ou ExpressRoute pour la gestion et l’importation de données dans le sous-réseau de l’architecture de référence.**

![Diagramme de l’architecture de référence Application web PaaS pour FedRAMP](images/fedramp-paaswa-architecture.png?raw=true "Diagramme de l’architecture de référence Application web PaaS pour FedRAMP")

Cette solution utilise les services Azure suivants. Les informations détaillées concernant l’architecture de déploiement se trouvent dans la section [Architecture de déploiement](#deployment-architecture).

- Azure Active Directory
- Azure Key Vault
- Azure SQL Database
- Application Gateway
    - (1) Pare-feu d’applications web
        - Mode de pare-feu : prévention
        - Ensemble de règles : OWASP 3.0
        - Écouteur : port 443
- réseau virtuel Azure
- Groupes de sécurité réseau
- Azure DNS
- Stockage Azure
- Azure Monitor
- App Service Environment v2
- Azure Load Balancer
- Application web Azure
- Azure Resource Manager

## <a name="deployment-architecture"></a>Architecture de déploiement
La section ci-après décrit en détail les éléments nécessaires au déploiement et à l’implémentation.

**Azure Resource Manager** : [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) permet aux clients d’utiliser les ressources de la solution en tant que groupe. Les clients peuvent déployer, mettre à jour ou supprimer toutes les ressources de la solution en une seule opération coordonnée. Les clients utilisent pour le déploiement un modèle pouvant fonctionner avec différents environnements, par exemple de test, intermédiaire et de production. Le Gestionnaire des ressources Azure Resource Manager offre des fonctionnalités de sécurité, d’audit et de marquage pour aider les clients à gérer leurs ressources après le déploiement de celles-ci.

**App Service Environment v2** : [Azure App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro) est une fonctionnalité App Service qui fournit un environnement entièrement isolé et dédié pour l’exécution sécurisée de vos applications App Service à grande échelle.

Un environnement App Service est isolé de façon à n’exécuter les applications que d’un seul client, et est toujours déployé dans un réseau virtuel. Les clients ont un contrôle affiné sur le trafic réseau entrant et sortant des applications, et les applications peuvent établir des connexions sécurisées à haut débit sur les réseaux virtuels avec les ressources d’entreprise locales.

L’utilisation d’un environnement App Service est autorisée pour les contrôles et configurations suivants :

- Hébergement dans un réseau virtuel Azure sécurisé avec application de règles de sécurité réseau
- Configuration des environnements ASE avec un certificat ILB auto-signé pour la communication HTTPS
- [Mode d’équilibrage de charge interne](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer)
- Désactivation de [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Modification du [chiffrement TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Contrôle des [ports réseau de trafic entrant](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Pare-feu d’applications web – Restreindre les données](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Autorisation du [trafic Azure SQL Database](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

La section [Instructions et recommandations](#guidance-and-recommendations) contient des informations supplémentaires sur les ASE.

**Azure Web App** : [Azure App Service](https://docs.microsoft.com/azure/app-service/) permet aux clients de créer et d’héberger des applications web dans le langage de programmation de leur choix sans gérer l’infrastructure. Il offre une mise à l’échelle automatique et une haute disponibilité, prend en charge à la fois Windows et Linux et permet des déploiements automatisés à partir de GitHub, Azure DevOps ou n’importe quel référentiel Git.

### <a name="virtual-network"></a>Réseau virtuel
L’architecture définit un réseau privé virtuel avec l’espace d’adressage 10.200.0.0/16.

**Groupes de sécurité réseau** : Les [groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) contiennent des listes de contrôle d’accès qui autorisent ou interdisent le trafic au sein d’un réseau virtuel. Les groupes de sécurité réseau peuvent être utilisés pour sécuriser le trafic au niveau d’un sous-réseau ou d’une machine virtuelle. Les groupes de sécurité réseau disponibles sont les suivants :
- 1 groupe de sécurité réseau pour la passerelle d’application (Application Gateway) ;
- 1 groupe de sécurité réseau pour l’environnement App Service (App Service Environment) ;
- 1 groupe de sécurité réseau pour Azure SQL Database.

Chaque groupe de sécurité du réseau a des ports et protocoles spécifiques ouverts afin que la solution puisse fonctionner correctement et en toute sécurité. En outre, les configurations suivantes sont activées pour chaque groupe de sécurité réseau :
  - Les [événements et journaux de diagnostic](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) sont activés et stockés dans un compte de stockage.
  - La solution Log Analytics est connectée aux [diagnostics du groupe de sécurité réseau](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Sous-réseaux** : Chaque sous-réseau est associé à son groupe de sécurité réseau correspondant.

**Azure DNS** : Le DNS (Domain Name System) se charge de traduire (ou résoudre) un nom de site web ou de service en une adresse IP. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) est un service d’hébergement pour domaines DNS, qui assure la résolution de noms à l’aide de l’infrastructure Azure. En hébergeant des domaines dans Azure, les utilisateurs peuvent gérer des enregistrements DNS en utilisant les mêmes informations d’identification, API, outils et modes de facturation que pour d’autres services Azure. Azure DNS prend désormais également en charge les domaines DNS privés.

**Azure Load Balancer** : [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) permet aux clients de procéder à la mise à l’échelle de leurs applications et de créer une haute disponibilité pour les services. Load Balancer prend en charge les scénarios entrants et sortants, et offre une latence faible, un débit élevé et une montée en puissance jusqu’à plusieurs millions de flux pour toutes les applications TCP et UDP.

### <a name="data-in-transit"></a>Données en transit
Par défaut, Azure chiffre toutes les communications avec les centres de données Azure. Toutes les transactions en direction du Stockage Azure passant par le portail Azure se produisent via HTTPS.

### <a name="data-at-rest"></a>Données au repos
L’architecture protège les données au repos à l’aide d’un chiffrement, d’un audit de base de données et d’autres mesures.

**Stockage Azure** : pour répondre aux exigences du chiffrement des données au repos, l’ensemble du [Stockage Azure](https://azure.microsoft.com/services/storage/) utilise la fonctionnalité [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Azure Disk Encryption** : 
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) tire profit de la fonctionnalité BitLocker de Windows pour assurer le chiffrement de volume des disques de données. La solution s’intègre à Azure Key Vault pour faciliter le contrôle et la gestion des clés de chiffrement de disque.

**Azure SQL Database** : L’instance Azure SQL Database utilise les mesures suivantes pour la sécurité des bases de données :
-   La solution [d’authentification et d’autorisation Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permet la gestion des identités des utilisateurs de bases de données et d’autres services Microsoft dans un emplacement central.
-   [L’audit Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) suit les événements de base de données et les consigne dans un journal d’audit conservé dans un compte de stockage Azure.
-   Azure SQL Database est configuré pour utiliser la technologie [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) qui assure le chiffrement et le déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux de transactions afin de protéger les informations au repos.
-   Des [règles de pare-feu](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) empêchent tout accès aux serveurs de base de données sans autorisation appropriée. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande.
-   La [détection des menaces SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permet de détecter et traiter les menaces potentielles à mesure qu’elles surviennent en déclenchant des alertes de sécurité en cas d’activités de base de données suspectes, de vulnérabilités potentielles, d’attaques par injection de code SQL et de modèles d’accès anormaux aux bases de données.
-   Des [colonnes Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantissent que les données sensibles n’apparaissent jamais en tant que texte en clair à l’intérieur du système de base de données. Une fois le chiffrement des données activé, seules des applications clientes ou des serveurs d’applications ayant accès aux clés peuvent accéder aux données en texte clair.
- La [Sécurité au niveau des lignes](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permet aux utilisateurs de définir des stratégies de restriction d’accès aux données afin d’interrompre le traitement de celles-ci.
- Le [masquage de données dynamiques SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) peut être effectué après le déploiement de l’architecture de référence. Les clients doivent ajuster les paramètres de masquage de données dynamiques pour respecter leur schéma de base de données.

### <a name="identity-management"></a>Gestion des identités
Les technologies suivantes fournissent des fonctionnalités de gestion des identités dans l’environnement Azure :
-   [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) est le service mutualisé basé sur le cloud de Microsoft pour la gestion des annuaires et des identités. Tous les utilisateurs de cette solution, y compris ceux qui accèdent à Azure SQL Database, sont créés dans AAD.
-   L’authentification auprès de l’application est effectuée à l’aide d’AAD. Pour plus d’informations, consultez [Intégration d’applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). En outre, le chiffrement des colonnes de base de données utilise Azure Active Directory pour authentifier l’application auprès d’Azure SQL Database. Pour plus d’informations, découvrez comment [protéger des données sensibles dans Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   Le [contrôle d’accès en fonction du rôle Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permet une gestion précise de l’accès pour Azure. L’accès aux abonnements est limité à l’administrateur des abonnements, tandis que l’accès aux ressources peut être limité en fonction du rôle d’utilisateur.
- La solution [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) permet aux clients de minimiser le nombre d’utilisateurs qui ont accès à des informations spécifiques.  Les administrateurs peuvent utiliser AAD Privileged Identity Management pour découvrir, restreindre et surveiller les identités privilégiées et leur accès aux ressources. Cette fonctionnalité permet également d’appliquer un accès administratif juste-à-temps à la demande si nécessaire.
- La solution [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) détecte les vulnérabilités risquant d’affecter les identités d’une organisation, configure des réponses automatiques aux actions suspectes détectées en lien avec les identités d’une organisation, et examine les incidents suspects afin de prendre les mesures appropriées pour résoudre ces derniers.

### <a name="security"></a>Sécurité
La solution **Gestion des secrets** utilise [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pour gérer les clés et secrets. Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. Les fonctionnalités d’Azure Key Vault ci-après aident les clients à protéger les données ainsi que l’accès à ces dernières :
- Les stratégies d’accès avancées sont configurées en fonction des besoins.
- Les stratégies d’accès Key Vault sont définies avec des autorisations minimales requises pour les clés et les secrets.
- L’ensemble des clés et secrets figurant dans Key Vault présentent des dates d’expiration.
- Toutes les clés stockées dans Key Vault sont protégées par des modules de sécurité matériels (HSM) spécialisés. La clé est de type RSA 2 048 bits protégé par HSM.
- Des autorisations minimales requises sont accordées à tous les utilisateurs et à toutes les identités à l’aide du contrôle d’accès en fonction rôle.
- Les journaux de diagnostics pour Key Vault sont activés avec une période de rétention d’au moins 365 jours.
- Les opérations de chiffrement autorisées pour les clés sont restreintes à celles qui sont nécessaires.

**Application Gateway** : l’architecture réduit le risque de failles de sécurité à l’aide d’Application Gateway avec le pare-feu d’applications web et l’ensemble de règles OWASP activé. Les autres fonctionnalités incluent notamment :
- [SSL de bout en bout](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Activation du [déchargement SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Désactivation de [TLS versions 1.0 et 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Pare-feu d’applications web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)
- [Mode Prévention](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) avec l’ensemble de règles OWASP 3.0
- Activation de la [journalisation des diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Sondes d’intégrité personnalisées](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) et [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fournissent une protection supplémentaire et des notifications. Azure Security Center fournit également un système de réputation.

### <a name="logging-and-auditing"></a>Journalisation et audit
Azure Monitor assure une journalisation complète de l’activité du système et des utilisateurs, ainsi que de l’intégrité du système. Ce service collecte et analyse les données générées par les ressources des environnements Azure et locaux.
- **Journaux d’activité** : les [journaux d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fournissent des insights sur les opérations ayant été effectuées sur les ressources d’un abonnement. Les journaux d’activité peuvent aider à déterminer l’initiateur, l’heure d’exécution et l’état d’une opération.
- **Journaux de diagnostic** : les [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluent l’ensemble des journaux générés par chaque ressource. Ils incluent les journaux des événements système de Windows, les journaux de Stockage Azure, les journaux d’audit du Key Vault, ainsi que les journaux de pare-feu et d’accès d’Application Gateway.
- **Archivage des journaux** : Tous les journaux de diagnostic sont consignés dans un compte de stockage Azure centralisé et chiffré pour l’archivage. L’utilisateur peut configurer la rétention jusqu’à 730 jours pour répondre aux exigences de rétention spécifiques de l’entreprise. Ces journaux se connectent à Azure Log Analytics à des fins de traitement, de stockage et de génération de rapports de tableau de bord.

En outre, cette architecture intègre également les solutions de supervision suivantes :
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment) : la solution Active Directory Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit une liste hiérarchisée de suggestions spécifiques pour l’infrastructure de serveur déployée.
-   [Antimalware Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware) : la solution de logiciel anti-programme malveillant signale les programmes malveillants, les menaces et l’état de protection.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) : la solution Azure Automation stocke, exécute et gère les runbooks. Dans cette solution, des runbooks aident à collecter les journaux d’Application Insights et d’Azure SQL Database.
-   [Security and Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) : le tableau de bord Security and Audit donne une vue d’ensemble de l’état de sécurité des ressources en affichant des métriques concernant des domaines de sécurité, des problèmes notables, des détections, des menaces et des requêtes de sécurité courantes.
-   [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment) : la solution SQL Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit aux clients une liste hiérarchisée de suggestions spécifiques pour l’infrastructure de serveur déployée.
-   [Update Management](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management) : cette solution de gestion des mises à jour aide les clients à gérer les mises à jour de sécurité du système d’exploitation, notamment en leur indiquant l’état des mises à jour disponibles et le processus d’installation des mises à jour requises.
-   [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth) : cette solution de contrôle d’intégrité des agents indique le nombre d’agents déployés et leur répartition géographique, ainsi que le nombre d’agents qui ne répondent pas ou qui envoient des données opérationnelles.
-   [Journaux d’activité Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity) : la solution Activity Log Analytics facilite l’analyse des journaux d’activité Azure de tous les abonnements Azure d’un client.
-   [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) : cette solution de suivi des modifications permet aux clients d’identifier facilement des modifications dans l’environnement.

**Azure Monitor**
la solution [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) permet aux utilisateurs de suivre les performances, de tenir à jour la sécurité et d’identifier les tendances en permettant aux entreprises d’auditer, de créer des alertes et d’archiver des données, notamment en lien avec le suivi des appels d’API dans les ressources Azure des clients.

## <a name="threat-model"></a>Modèle de menace

Le diagramme de flux de données de cette architecture de référence figure ci-dessous et est également disponible en [téléchargement](https://aka.ms/fedrampPaaSWebAppDFD). Ce modèle peut aider les clients à comprendre les points de risque potentiel de l’infrastructure du système lors de l’apport de modifications.

![Modèle de menace Application web IaaS pour FedRAMP](images/fedramp-paaswa-threat-model.png?raw=true "Modèle de menace Application web IaaS pour FedRAMP")

## <a name="compliance-documentation"></a>Documentation sur la conformité
Le document [Azure Blueprint Sécurité et conformité - Matrice de responsabilités des clients FedRAMP High](https://aka.ms/blueprinthighcrm) répertorie tous les contrôles de sécurité requis par la ligne de base FedRAMP High. La matrice indique si l’implémentation de chacun des contrôles revient à Microsoft, au client ou aux deux.

Le document [Azure Security and Compliance Blueprint - FedRAMP PaaS WebApp High Control Implementation Matrix](https://aka.ms/fedrampPaaSWebAppCIM) (Programme Blueprint Security and Compliance Azure - Matrice de mise en œuvre des contrôles des applications web PaaS FedRAMP High) répertorie tous les contrôles de sécurité requis par la ligne de base FedRAMP High. La matrice indique quels contrôles sont couverts par l’architecture d’application web PaaS, et inclut une description détaillée de la façon dont l’implémentation est conforme aux exigences de chacun des contrôles.

## <a name="guidance-and-recommendations"></a>Instructions et recommandations
### <a name="vpn-and-expressroute"></a>VPN et ExpressRoute
Un tunnel VPN sécurisé ou un routage [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) doivent être configurés pour établir une connexion sécurisée aux ressources déployées en lien avec cette architecture de référence d’application web PaaS. En configurant correctement un VPN ou un routage ExpressRoute, les clients peuvent ajouter une couche de protection au niveau des données en transit.

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
