---
title: Azure Blueprint Sécurité et conformité - Application web PaaS pour le RGPD
description: Azure Blueprint Sécurité et conformité - Application web PaaS pour le RGPD
services: security
author: jomolesk
ms.assetid: 229759a1-f984-4985-a3fd-3719a7d1c8ff
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: 02c8d5b40315f5612564b6ae11bd9cf1861708a9
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297875"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-gdpr"></a>Azure Blueprint Sécurité et conformité - Application web PaaS pour le RGPD

## <a name="overview"></a>Vue d'ensemble
Le Règlement général sur la protection des données (RGPD) contient de nombreuses prescriptions concernant la collecte, le stockage et l’utilisation des informations personnelles, notamment la façon dont les organisations identifient et sécurisent les données personnelles, respectent les exigences de transparence, détectent et signalent les violations de données personnelles, et forment leur personnel, en particulier celui chargé de veiller sur la confidentialité des données. Le RGPD offre aux individus un plus grand contrôle sur leurs données personnelles, et impose de nombreuses obligations nouvelles aux organisations qui collectent, traitent ou analysent ces données. Le RGPD impose de nouvelles règles aux organisations qui proposent des biens et des services aux citoyens de l’Union européenne (UE), ou qui recueillent et analysent des données les concernant. Le RGPD s’applique, quel que soit l’endroit où se trouvent les organisations.

Microsoft a conçu Azure en y intégrant des mesures de sécurité et des politiques de confidentialité de pointe pour protéger les données dans le cloud, notamment les catégories de données personnelles visées par le RGPD. Les [clauses contractuelles](http://aka.ms/Online-Services-Terms) de Microsoft contraignent l’entreprise à respecter certaines exigences applicables aux sous-traitants.

Cet Azure Blueprint Sécurité et conformité fournit des conseils pour déployer un environnement de plateforme en tant que service (PaaS, Platform-as-a-Service) adapté pour une application web simple accessible sur Internet. Cette solution montre aux clients comment respecter les exigences de sécurité et de conformité spécifiques du RGPD, et leur sert de base pour construire et configurer leurs propres solutions applicatives web PaaS dans Azure. Les clients peuvent utiliser cette architecture de référence et suivre le [processus en quatre étapes](https://aka.ms/gdprebook) préconisé par Microsoft pour leur parcours vers la mise en conformité avec le RGPD :
1. Découvrir : identifier les données personnelles existantes, ainsi que l’emplacement où elles résident.
2. Gérer : régir la manière dont les données personnelles peuvent être consultées et utilisées.
3. Protéger : établir des contrôles de sécurité pour prévenir, détecter et traiter les vulnérabilités et les violations de données.
4. Rapporter : conserver la documentation requise et gérer les demandes de données ainsi que les notifications de violation.

Cette architecture de référence, ainsi que le guide de mise en œuvre et le modèle de menace associés, sont destinés à servir de base aux clients qui doivent les adapter à leurs besoins spécifiques et non les utiliser tels quels en environnement de production. Notez les points suivants :
- L’architecture constitue un point de départ pour aider les clients à déployer des charges de travail sur Azure d’une manière conforme au RGPD.
- Il incombe aux clients de d’évaluer de façon appropriée la sécurité et la conformité de toute solution basée sur cette architecture, car les exigences peuvent varier en fonction des spécificités de son implémentation.

## <a name="architecture-diagram-and-components"></a>Diagramme et composants de l’architecture
Cette solution fournit une architecture de référence pour une application web PaaS avec un serveur principal Azure SQL Database. L’application web est hébergée dans un environnement Azure App Service dédié, privé et isolé dans un centre de données Azure. L’environnement équilibre la charge du trafic généré par l’application web entre des machines virtuelles gérées par Azure. Cette architecture inclut également des groupes de sécurité réseau, une passerelle d’application (Azure Application Gateway), un DNS (Azure DNS) et un équilibreur de charge (Azure Load Balancer). En outre, Application Insights assure la gestion des performances d’applications et l’analytique via Operations Management Suite (OMS). **Azure recommande de configurer une connexion VPN ou ExpressRoute pour la gestion et l’importation de données dans le sous-réseau de l’architecture de référence.**

![Diagramme de l’architecture de référence de l’application web PaaS pour le RGPD](images/gdpr-paaswa-architecture.png?raw=true "Diagramme de l’architecture de référence de l’application web PaaS pour le RGPD")

Cette solution utilise les services Azure suivants. Les informations détaillées concernant l’architecture de déploiement se trouvent dans la section [Architecture de déploiement](#deployment-architecture).

- Azure Active Directory (AAD)
- Azure Key Vault
- Azure SQL Database
- Application Gateway
    - (1) WAF Application Gateway activé
        - mode pare-feu : prévention
        - ensemble de règles : OWASP 3.0
        - écouteur : port 443
- réseau virtuel Azure
- groupes de sécurité réseau
- Azure DNS
- Stockage Azure
- Operations Management Suite (OMS)
- Azure Monitor
- Application Insights
- Azure Security Center
- App Service Environment v2
- Azure Load Balancer
- Application web Azure
- Azure Resource Manager

## <a name="deployment-architecture"></a>Architecture de déploiement
La section suivante décrit en détail les éléments nécessaires au déploiement et à l’implémentation.

**Azure Resource Manager** : [Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) permet aux clients d’utiliser les ressources de la solution en tant que groupe. Les clients peuvent déployer, mettre à jour ou supprimer toutes les ressources de la solution en une seule opération coordonnée. Les clients utilisent pour le déploiement un modèle pouvant fonctionner avec différents environnements, par exemple de test, intermédiaire et de production. Le Gestionnaire des ressources Azure Resource Manager offre des fonctionnalités de sécurité, d’audit et de marquage pour aider les clients à gérer leurs ressources après le déploiement de celles-ci.

**App Service Environment v2** : l’[environnement Azure App Service](https://docs.microsoft.com/en-us/azure/app-service/environment/intro) est une fonctionnalité d’Azure App Service qui fournit un environnement dédié et totalement isolé pour l’exécution sécurisée d’applications App Service à grande échelle.

Un environnement App Service est isolé de façon à n’exécuter les applications que d’un seul client, et est toujours déployé dans un réseau virtuel. Les clients ont un contrôle affiné sur le trafic réseau entrant et sortant des applications, et les applications peuvent établir des connexions sécurisées à haut débit sur les réseaux virtuels avec les ressources d’entreprise locales.

L’utilisation d’un environnement App Service est autorisée pour les contrôles et configurations suivants :

- Hébergement dans un réseau virtuel Azure sécurisé avec application de règles de sécurité réseau
- Configuration des environnements ASE avec un certificat ILB auto-signé pour la communication HTTPS
- [Mode Équilibrage de charge interne](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-environment-with-internal-load-balancer) (mode 3)
- Désactivation de [TLS 1.0](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Modification du [chiffrement TLS](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Contrôle des [ports réseau de trafic entrant](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [WAF – Restreindre les données](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Autorisation du [trafic Azure SQL Database](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

**Azure Web Apps** : [Azure Web Apps](https://docs.microsoft.com/en-us/azure/app-service/) permet aux clients de créer et d’héberger des applications web écrites dans le langage de programmation de leur choix sans devoir gérer l’infrastructure. Il offre une mise à l’échelle automatique et une haute disponibilité, prend en charge à la fois Windows et Linux et permet des déploiements automatisés à partir de GitHub, Azure DevOps ou n’importe quel référentiel Git.

### <a name="virtual-network"></a>Réseau virtuel
L’architecture définit un réseau privé virtuel avec un espace d’adressage de 10.200.0.0/16.

**Groupes de sécurité réseau**: les [groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contiennent des listes de contrôle d’accès (ACL) qui autorisent ou refusent le trafic au sein d’un réseau virtuel. Les groupes de sécurité réseau peuvent être utilisés pour sécuriser le trafic au niveau d’un sous-réseau ou d’une machine virtuelle. Les groupes de sécurité réseau disponibles sont les suivants :
- 1 groupe de sécurité réseau pour la passerelle d’application (Application Gateway) ;
- 1 groupe de sécurité réseau pour l’environnement App Service (App Service Environment) ;
- 1 groupe de sécurité réseau pour Azure SQL Database.

Chaque groupe de sécurité du réseau a des ports et protocoles spécifiques ouverts afin que la solution puisse fonctionner correctement et en toute sécurité. En outre, les configurations suivantes sont activées pour chaque groupe de sécurité réseau :
  - Les [événements et journaux de diagnostic](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-nsg-manage-log) sont activés et stockés dans un compte de stockage.
  - La solution Log Analytics d’OMS est connectée aux [diagnostics de NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Sous-réseaux** : vérifiez que chaque sous-réseau est associé au groupe de sécurité réseau qui lui correspond.

**Azure DNS** : le DNS (Domain Name System) se charge de traduire (ou résoudre) un nom de site web ou de service en une adresse IP. [Azure DNS](https://docs.microsoft.com/en-us/azure/dns/dns-overview) est un service d’hébergement pour domaines DNS, qui assure la résolution de noms à l’aide de l’infrastructure Azure. En hébergeant des domaines dans Azure, les utilisateurs peuvent gérer des enregistrements DNS en utilisant les mêmes informations d’identification, API, outils et modes de facturation que pour d’autres services Azure. Azure DNS prend désormais également en charge les domaines DNS privés.

**Azure Load Balancer** : l’[équilibreur de charge Azure](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview) permet aux clients de mettre à l’échelle leurs applications et de créer une haute disponibilité pour les services. Load Balancer prend en charge les scénarios entrants et sortants, et offre une latence faible, un débit élevé et une montée en puissance jusqu’à plusieurs millions de flux pour toutes les applications TCP et UDP.

### <a name="data-in-transit"></a>Données en transit
Par défaut, Azure chiffre toutes les communications avec les centres de données Azure. Toutes les transactions en direction du Stockage Azure passant par le portail Azure se produisent via HTTPS.

### <a name="data-at-rest"></a>Données au repos

L’architecture protège les données au repos à l’aide d’un chiffrement, d’un audit de base de données et d’autres mesures.

**Stockage Azure** : pour répondre aux exigences du chiffrement des données au repos, l’ensemble du [Stockage Azure](https://azure.microsoft.com/services/storage/) utilise le chiffrement [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Celui-ci permet de protéger et de sauvegarder les données personnelles dans le respect des engagements de l’organisation en matière de sécurité et des exigences de conformité définies par le RGPD.

**Azure Disk Encryption** : 
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) tire profit de la fonctionnalité BitLocker de Windows pour assurer le chiffrement de volume des disques de données. La solution s’intègre à Azure Key Vault pour faciliter le contrôle et la gestion des clés de chiffrement de disque.

**Azure SQL Database** : l’instance Azure SQL Database utilise les mesures de sécurité de base de données suivantes :
-   Les mécanismes [d’authentification et d’autorisation Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permettent de centraliser la gestion des identités des utilisateurs de bases de données et d’autres services Microsoft.
-   [L’audit Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) suit les événements de base de données et les consigne dans un journal d’audit conservé dans un compte de stockage Azure.
-   Azure SQL Database est configuré pour utiliser la technologie [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) qui assure le chiffrement et le déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux de transactions afin de protéger les informations au repos. TDE garantit que nul ne peut accéder sans autorisation aux données personnelles stockées.
-   Des [règles de pare-feu](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) empêchent tout accès aux serveurs de base de données sans autorisation appropriée. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande.
-   La [détection des menaces SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permet de détecter et traiter des menaces potentielles à mesure qu’elles surviennent en déclenchant des alertes de sécurité en lien avec des activités suspectes de base de données, des vulnérabilités potentielles, des attaques par injection de code SQL et des modèles d’accès anormaux aux bases de données.
-   Les [colonnes chiffrées par la fonctionnalité Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantissent que les données personnelles sensibles n’apparaissent jamais sous forme de texte en clair à l’intérieur du système de base de données. Une fois le chiffrement des données activé, seules des applications clientes ou des serveurs d’applications ayant accès aux clés peuvent accéder aux données en texte clair.
- Les [propriétés étendues](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) permettent aux utilisateurs d’interrompre le traitement d’objets de données en ajoutant des propriétés personnalisées à des objets de base de données et en marquant des données comme « Abandonnées » pour aider la logique d’application à empêcher le traitement de données personnelles associées.
- Une [sécurité au niveau des lignes](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permet aux utilisateurs de définir des stratégies de restriction d’accès aux données afin d’interrompre le traitement de celles-ci.
- [SQL Database Dynamic Data Masking (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limite l’exposition des données personnelles sensibles en masquant celles-ci aux utilisateurs ou aux applications dépourvus de privilèges. DDM peut détecter automatiquement les données potentiellement sensibles et suggérer les masques appropriés à appliquer. Cette fonctionnalité aide à identifier les données personnelles à protéger conformément au RGPD, ainsi qu’à réduire l’accès à ces données afin qu’elles ne puissent pas sortir de la base de données par le biais d’un accès non autorisé. **Remarque : les clients doivent ajuster les paramètres de DDM pour respecter leur schéma de base de données.**

### <a name="identity-management"></a>Gestion des identités
Les technologies suivantes fournissent des fonctionnalités pour gérer l’accès aux données personnelles dans l’environnement Azure :
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) est le service mutualisé basé sur le cloud de Microsoft pour la gestion des annuaires et des identités. Tous les utilisateurs de cette solution, y compris ceux qui accèdent à Azure SQL Database, sont créés dans AAD.
-   L’authentification auprès de l’application est effectuée à l’aide d’AAD. Pour plus d’informations, consultez [Intégration d’applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). De plus, le chiffrement des colonnes de base de données utilise également AAD pour authentifier l’application auprès d’Azure SQL Database. Pour plus d’informations, découvrez comment [protéger des données sensibles dans Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   Le [contrôle d’accès en fonction du rôle (RBAC) Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permet aux administrateurs de définir des autorisations d’accès affinées pour n’accorder aux utilisateurs que l’accès dont ils ont besoin pour accomplir leur travail. Plutôt que d’attribuer à tous les utilisateurs des autorisations d’accès illimitées aux ressources Azure, les administrateurs peuvent n’autoriser que certaines actions d’accès aux données personnelles. Par exemple, l’accès aux abonnements est limité à l’administrateur d’abonnements.
- La solution [Azure Active Directory Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) permet aux clients de limiter au minimum le nombre d’utilisateurs qui ont accès à des informations telles que des données personnelles.  Les administrateurs peuvent utiliser AAD Privileged Identity Management pour découvrir, restreindre et surveiller les identités privilégiées et leur accès aux ressources. Cette fonctionnalité permet également d’appliquer un accès administratif juste-à-temps à la demande si nécessaire.
- La solution [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) détecte les vulnérabilités pouvant affecter les identités d’une organisation, et configure des réponses automatiques à des actions suspectes détectées en lien avec les identités de votre organisation. Elle examine aussi les incidents suspects et prend des mesures appropriées pour les résoudre.

### <a name="security"></a>Sécurité
La solution **Gestion des secrets** utilise [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pour gérer les clés et secrets. Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. Les fonctionnalités suivantes d’Azure Key Vault aident les clients à protéger les données personnelles et l’accès à celles-ci :
- Les stratégies d’accès avancées sont configurées en fonction des besoins.
- Les stratégies d’accès Key Vault sont définies avec des autorisations minimales requises pour les clés et les secrets.
- L’ensemble des clés et secrets figurant dans Key Vault présentent des dates d’expiration.
- Toutes les clés stockées dans Key Vault sont protégées par des modules de sécurité matériels (HSM) spécialisés. La clé est de type RSA 2 048 bits protégé par HSM.
- Les autorisations minimales requises sont accordées à l’ensemble des utilisateurs et identités à l’aide d’un contrôle d’accès en fonction du rôle (RBAC).
- Les journaux de diagnostics pour Key Vault sont activés avec une période de rétention d’au moins 365 jours.
- Les opérations de chiffrement autorisées pour les clés sont limitées à celles qui sont indispensables.

**Alertes de sécurité** : [Azure Security Center](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro) permet aux clients de surveiller le trafic, de collecter des journaux et d’analyser les sources de données en lien avec d’éventuelles menaces. En outre, Azure Security Center accède à une configuration existante des services Azure pour fournir des recommandations en matière de configuration et de service afin de contribuer à l’amélioration de la situation de sécurité et à la protection des données personnelles. Azure Security Center génère un [rapport d’intelligence des menaces](https://docs.microsoft.com/en-us/azure/security-center/security-center-threat-report) pour chaque menace détectée afin d’aider les équipes chargées de répondre aux incidents à examiner et à contrecarrer les menaces.

**Application Gateway** : l’architecture réduit le risque de failles de sécurité à l’aide d’Application Gateway avec le pare-feu d’applications web (WAF) et l’ensemble de règles OWASP activés. Les autres fonctionnalités incluent notamment :

- [SSL de bout en bout](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Activation du [déchargement SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Désactivation de [TLS versions 1.0 et 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Pare-feu d’applications web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (mode WAF)
- [Mode Prévention](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) avec l’ensemble de règles OWASP 3.0
- Activation de la [journalisation des diagnostics](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-diagnostics)
- [Sondes d’intégrité personnalisées](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) et [Azure Advisor](https://docs.microsoft.com/en-us/azure/advisor/advisor-security-recommendations) fournissent une protection supplémentaire et des notifications. Azure Security Center fournit également un système de réputation.

### <a name="logging-and-auditing"></a>Journalisation et audit

Microsoft Operations Management Suite assure une journalisation complète de l’activité du système et des utilisateurs, ainsi que de l’intégrité du système. La solution [Log Analytics](https://azure.microsoft.com/services/log-analytics/) de Microsoft Operations Management Suite collecte et analyse les données générées par les ressources des environnements Azure et locaux.
- **Journaux d’activité :** les [journaux d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fournissent des informations sur les opérations effectuées sur les ressources d’un abonnement. Les journaux d’activité peuvent aider à déterminer l’initiateur, l’heure d’exécution et l’état d’une opération.
- **Journaux de diagnostic :** les [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) sont tous les journaux émis par toutes les ressources. Ils incluent les journaux des événements système de Windows, les journaux de Stockage Azure, les journaux d’audit du Key Vault, ainsi que les journaux de pare-feu et d’accès d’Application Gateway.
- **Archivage des journaux** : tous les journaux de diagnostic sont consignés sur un compte de stockage Azure centralisé et chiffré à des fins d’archivage. L’utilisateur peut configurer la rétention jusqu’à 730 jours pour répondre aux exigences de rétention spécifiques de l’organisation. Ces journaux se connectent à Azure Log Analytics à des fins de traitement, de stockage et de génération de rapports de tableau de bord.

En outre, cette architecture inclut les solutions OMS suivantes :
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment) : la solution Active Directory Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit une liste hiérarchisée de recommandations spécifiques pour l’infrastructure de serveur déployée.
-   [Antimalware Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware) : cette solution signale les programmes malveillants, les menaces et l’état de protection.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) : la solution d’automatisation Azure stocke, exécute et gère les runbooks. Dans cette solution, des runbooks aident à collecter les journaux d’Application Insights et d’Azure SQL Database.
-   [Sécurité et audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) : le tableau de bord Sécurité et audit donne une vue d’ensemble de l’état de sécurité des ressources en affichant des métriques concernant des domaines de sécurité, des problèmes notables, des détections, des menaces et des requêtes de sécurité courantes.
-   [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment) : la solution SQL Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit aux clients une liste hiérarchisée de recommandations spécifiques pour l’infrastructure de serveur déployée.
-   [Update Management](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management) : cette solution permet au client de gérer les mises à jour de sécurité du système d’exploitation, notamment en lui indiquant l’état des mises à jour disponibles et le processus d’installation des mises à jour requises.
-   [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth) : cette solution indique le nombre d’agents déployés et leur répartition géographique, ainsi que le nombre d’agents qui ne répondent pas ou qui envoient des données opérationnelles.
-   [Journaux d’activité Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity) : la solution Activity Log Analytics facilite l’analyse des journaux d’activité Azure de tous les abonnements Azure d’un client.
-   [Change Tracking](https://docs.microsoft.com/en-us/azure/automation/automation-change-tracking) : la solution de suivi des modifications permet aux clients d’identifier facilement les modifications dans l’environnement.

**Azure Monitor**
 : la solution [Azure Monitor](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/) permet aux utilisateurs de suivre les performances, de tenir à jour la sécurité et d’identifier des tendances en permettant aux organisations d’auditer, de créer des alertes et d’archiver des données, notamment en lien avec le suivi des appels d’API dans les ressources Azure des clients.

**Application Insights**
 : la solution [Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-overview) est un service extensible de gestion des performances d’applications destiné aux développeurs web sur diverses plateformes. La solution Application Insights détecte des anomalies de performances et permet aux clients de surveiller leurs applications web dynamiques. Elle inclut de puissants outils d’analyse qui aident les clients à diagnostiquer les problèmes et à comprendre ce que les utilisateurs font réellement avec leurs applications. Elle a été conçue pour aider les clients à améliorer continuellement les performances et l’ergonomie des applications.

## <a name="threat-model"></a>Modèle de menace

Le diagramme de flux de données de cette architecture de référence figure ci-dessous et est également disponible en [téléchargement](https://aka.ms/gdprPaaSdfd). Ce modèle peut aider les clients à comprendre les points de risque potentiel de l’infrastructure du système lors de l’apport de modifications.

![Application web PaaS pour le modèle de menace du RGPD](images/gdpr-paaswa-threat-model.png?raw=true "Application web PaaS pour le modèle de menace du RGPD")

## <a name="compliance-documentation"></a>Documentation sur la conformité
L’[Azure Blueprint Sécurité et conformité – Matrice de responsabilités des clients en lien avec le RGPD](https://aka.ms/gdprCRM) répertorie les responsabilités du contrôleur et du processeur pour tous les articles du RGPD. Notez qu’en règle générale, pour les services Azure, un client est le contrôleur et Microsoft le processeur.

L’[Azure Blueprint Sécurité et conformité - Matrice d’implémentation d’application web PaaS en lien avec le RGPD](https://aka.ms/gdprPaaSWeb) fournit des informations sur les articles du RGPD que prend en compte l’architecture d’application web PaaS, dont une description détaillée de la façon dont l’implémentation répond aux exigences de chaque article couvert.


## <a name="guidance-and-recommendations"></a>Instructions et recommandations
### <a name="vpn-and-expressroute"></a>VPN et ExpressRoute
Un tunnel VPN sécurisé ou un routage [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) doivent être configurés pour établir une connexion sécurisée aux ressources déployées en lien avec cette architecture de référence d’application web PaaS. En configurant correctement un VPN ou un routage ExpressRoute, les clients peuvent ajouter une couche de protection des données en transit.

En implémentant un tunnel VPN sécurisé avec Azure, il est possible de créer une connexion privée virtuelle entre un réseau local et un réseau virtuel Azure. Cette connexion via Internet permet aux clients de « tunneler » des informations en toute sécurité par le biais d’une liaison chiffrée entre leur réseau et Azure. La technologie de réseau privé virtuel de site à site, aussi sécurisée qu’éprouvée, est déployée par des entreprises de toutes tailles depuis des décennies. Le [mode tunnel IPsec](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) est utilisé dans cette option comme un mécanisme de chiffrement.

Étant donné que le trafic à l’intérieur du tunnel VPN transite par Internet avec un VPN de site à site, Microsoft offre une autre option de connexion encore plus sécurisée. Azure ExpressRoute est une liaison réseau étendu dédiée entre Azure et un emplacement local ou un fournisseur d’hébergement Exchange. Les connexions ExpressRoute ne transitent pas par Internet, et offrent de meilleurs niveaux de fiabilité, de rapidité, de latence et de sécurité que les connexions classiques via Internet. En outre, comme il s’agit d’une connexion directe du fournisseur de télécommunications du client, les données ne circulent pas sur Internet et n’y sont donc pas exposées.

Les meilleures pratiques pour l’implémentation d’un réseau hybride sécurisé qui étend un réseau local à Azure sont [disponibles ici](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Clause d'exclusion de responsabilité

 - Ce document est fourni à titre d’information uniquement. MICROSOFT N’ACCORDE AUCUNE GARANTIE EXPRESSE, IMPLICITE OU STATUTAIRE EN LIEN AVEC LES INFORMATIONS CONTENUES DANS CE DOCUMENT. Ce document est fourni « en l’état ». Les informations et les points de vue exprimés dans ce document, y compris les URL et autres références à des sites web, peuvent être modifiés sans préavis. Les clients qui lisent ce document assument les risques liés à son utilisation.
 - Ce document n’accorde aux clients aucun droit légal de propriété intellectuelle sur les produits ou solutions Microsoft.
 - Les clients peuvent copier et utiliser ce document pour un usage interne, à titre de référence.
 - Certaines recommandations contenues dans ce document peuvent entraîner une augmentation des taux d’utilisation des données, des réseaux ou des ressources de calcul dans Azure, débouchant sur des coûts de licence ou d’abonnement supplémentaires.
 - Cette architecture constitue une base que les clients peuvent modifier en fonction de leurs besoins. Elle ne doit pas être utilisée telle quelle dans un environnement de production.
 - Développé en tant que référence, ce document ne doit pas être utilisé pour définir tous les moyens par lesquels un client peut satisfaire à des réglementations et exigences de conformité spécifiques. Les clients doivent rechercher une assistance juridique auprès de leur organisation pour connaître les implémentations client approuvées.
