---
title: Azure Blueprint Sécurité et conformité - Application web PaaS pour le UK NHS
description: Azure Blueprint Sécurité et conformité - Application web PaaS pour le UK NHS
services: security
author: jomolesk
ms.assetid: fe409add-6d09-4062-b3c8-d74574747739
ms.service: security
ms.topic: article
ms.date: 06/15/2018
ms.author: jomolesk
ms.openlocfilehash: 8af6998c8e9fbfb230c00597600b3aa4f3991f03
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "62112576"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-uk-nhs"></a>Blueprint de sécurité et de conformité Azure : Application Web PaaS pour le service national de santé du Royaume-Uni

## <a name="overview"></a>Vue d'ensemble

Cette solution Azure Blueprint Sécurité et conformité fournit une architecture de référence et des instructions pour une solution de plateforme en tant que service (PaaS) adaptée à la collecte, au stockage et à la récupération de données de santé. Cette solution montre les façons dont les clients peuvent se conformer aux instructions fournies dans le [Guide de bonne pratique de la sécurité cloud](https://digital.nhs.uk/data-and-information/looking-after-information/data-security-and-information-governance/nhs-and-social-care-data-off-shoring-and-the-use-of-public-cloud-services/health-and-social-care-cloud-security-good-practice-guide) publié par [NHS Digital](https://digital.nhs.uk/), un partenaire du Ministère de la santé et des affaires sociales du Royaume-Uni. Le guide de bonne pratique de la sécurité cloud est basé sur les 14 [principes de la sécurité cloud](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) publiés par le centre national de cybersécurité du Royaume-Uni.

Cette architecture de référence, le guide d’implémentation et le modèle de menace, sont destinés à servir de base aux clients qui doivent les ajuster à leurs besoins spécifiques et non les utiliser tels quels en environnement de production sans configuration supplémentaire. Il incombe aux clients d’évaluer de façon appropriée la sécurité et la conformité de toute solution basée sur cette architecture, car les exigences peuvent varier en fonction des spécificités de chaque implémentation.

## <a name="architecture-diagram-and-components"></a>Diagramme et composants de l’architecture

Cette solution fournit une architecture de référence pour une application web PaaS avec un back-end Azure SQL Database. L’application web est hébergée dans un environnement Azure App Service dédié, privé et isolé dans un centre de données Azure. L’environnement équilibre la charge du trafic pour l’application web entre des machines virtuelles gérées par Azure. Toutes les connexions externes requièrent TLSv1.2. Cette architecture inclut également des groupes de sécurité réseau, une passerelle d’application (Azure Application Gateway), un DNS (Azure DNS) et un équilibreur de charge (Azure Load Balancer).

La solution utilise des comptes de stockage Azure que les clients peuvent configurer pour utiliser Storage Service Encryption afin de maintenir la confidentialité des données au repos. Azure enregistre trois copies des données au sein d’un centre de données sélectionné par le client pour assurer la résilience. Le stockage géographiquement redondant permet de s’assurer de la réplication des données vers un centre de données secondaire situé à des centaines de kilomètres et du stockage de trois copies dans ce centre de données, empêchant ainsi une perte de données en cas de dysfonctionnement au niveau du centre de données primaire du client.

Pour renforcer la sécurité, toutes les ressources dans cette solution sont gérées comme un groupe de ressources via Azure Resource Manager. Le contrôle d’accès en fonction du rôle Azure Active Directory est utilisé pour contrôler l’accès aux données déployées et aux clés dans Azure Key Vault. L’intégrité du système est surveillée dans Azure Security Center et Azure Monitor. Les clients configurent ces deux services de supervision pour la capture de journaux d’activité et la centralisation des informations concernant l’intégrité du système dans un tableau de bord facilement consultable. Azure Application Gateway est configuré comme un pare-feu en mode prévention et n’autorise pas le trafic qui n’est pas TLSv1.2. La solution utilise Azure Application Service Environment v2 pour isoler la couche web dans un environnement non mutualisé.

**Microsoft recommande de configurer une connexion VPN ou ExpressRoute pour la gestion et l’importation de données dans le sous-réseau de l’architecture de référence.**

![Diagramme de l’architecture de référence Application web IaaS pour UK NHS](images/uknhs-paaswa-architecture.png?raw=true "Diagramme de l’architecture de référence Application web IaaS pour UK NHS")

Cette solution utilise les services Azure suivants. Les détails de l’architecture de déploiement figurent dans la section [Architecture de déploiement](#deployment-architecture).

- Application Gateway
    - Pare-feu d’application web
        - Mode de pare-feu : prévention
        - Ensemble de règles : OWASP
        - Port de l’écouteur : 443
- Azure Active Directory
- Azure Application Service Environment v2
- Azure Automation
- Azure DNS
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Stockage Azure
- Réseau virtuel Azure
    - Groupes de sécurité réseau
- Application web Azure

## <a name="deployment-architecture"></a>Architecture de déploiement

La section ci-après décrit en détail les éléments nécessaires au déploiement et à l’implémentation.

**Azure Resource Manager** : [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) permet aux clients d’utiliser les ressources de la solution en tant que groupe. Les clients peuvent déployer, mettre à jour ou supprimer toutes les ressources de la solution en une seule opération coordonnée. Les clients utilisent pour le déploiement un modèle pouvant fonctionner avec différents environnements, par exemple de test, intermédiaire et de production. Le Gestionnaire des ressources Azure Resource Manager offre des fonctionnalités de sécurité, d’audit et de marquage pour aider les clients à gérer leurs ressources après le déploiement de celles-ci.

**App Service Environment v2** : l’environnement Azure App Service est une fonctionnalité App Service qui fournit un environnement totalement isolé et dédié pour l’exécution sécurisée de vos applications App Service à grande échelle.

L’environnement App Service est isolé pour exécuter une seule application et il est toujours déployé dans un réseau virtuel. Cette fonctionnalité d’isolation permet à l’architecture de référence de disposer d’une isolation complète des locataires, la supprimant de l’environnement d’architecture mutualisée d’Azure. Cette fonctionnalité d’isolation est nécessaire pour répondre aux exigences du principe 3 de UK NHS. Les clients ont un contrôle affiné sur le trafic réseau entrant et sortant des applications, et les applications peuvent établir des connexions sécurisées à haut débit sur les réseaux virtuels avec les ressources d’entreprise locales. Les clients peuvent faire une « mise à l’échelle automatique » avec l’environnement App Service basé sur les mesures de charge, le budget disponible ou une planification définie.


L’utilisation de App Service Environment pour cette architecture permet les contrôles et configurations suivants :

- Hébergement dans un réseau virtuel Azure sécurisé avec application de règles de sécurité réseau
- Certificat d’équilibreur de charge interne auto-signé pour la communication HTTPS. En guise de meilleure pratique, Microsoft préconise l’utilisation d’une autorité de certification de confiance afin de renforcer la sécurité.
- [Mode d’équilibrage de charge interne](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer)
- Désactivation de [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Changement du [chiffrement TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Contrôle des [ports réseau de trafic entrant](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Pare-feu d’applications web – Restreindre les données](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Autorisation du [trafic Azure SQL Database](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

**Azure Web App** : [Azure App Service](https://docs.microsoft.com/azure/app-service/) permet aux clients de créer et d’héberger des applications web dans le langage de programmation de leur choix sans gérer l’infrastructure. Il offre une mise à l’échelle automatique et une haute disponibilité, prend en charge à la fois Windows et Linux et permet des déploiements automatisés à partir de GitHub, Azure DevOps ou n’importe quel référentiel Git.

### <a name="virtual-network"></a>Réseau virtuel

L’architecture définit un réseau privé virtuel avec l’espace d’adressage 10.200.0.0/16.

**Groupes de sécurité réseau** : les [groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contiennent des listes de contrôle d’accès qui autorisent ou interdisent le trafic au sein d’un réseau virtuel. Les groupes de sécurité réseau peuvent être utilisés pour sécuriser le trafic au niveau d’un sous-réseau ou d’une machine virtuelle individuelle. Les groupes de sécurité réseau suivants existent :

- un groupe de sécurité réseau pour Application Gateway
- un groupe de sécurité réseau pour App Service Environment
- 1 groupe de sécurité réseau pour Azure SQL Database

Chaque groupe de sécurité réseau a des ports et protocoles spécifiques ouverts afin que la solution puisse fonctionner correctement et en toute sécurité. Par ailleurs, les configurations suivantes sont activées pour chaque groupe de sécurité réseau :

- Les [événements et journaux de diagnostic](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) sont activés et stockés dans un compte de stockage.
- Les journaux d’activité Azure Monitor sont connectés aux [journaux de diagnostic du groupe de sécurité réseau](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Sous-réseaux** : chaque sous-réseau est associé au groupe de sécurité réseau qui lui correspond.

**Azure DNS** : Le DNS (Domain Name System) se charge de traduire (ou résoudre) un nom de site web ou de service en une adresse IP. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) est un service d’hébergement pour domaines DNS, qui assure la résolution de noms à l’aide de l’infrastructure Azure. En hébergeant des domaines dans Azure, les utilisateurs peuvent gérer des enregistrements DNS en utilisant les mêmes informations d’identification, API, outils et modes de facturation que pour d’autres services Azure. Azure DNS prend désormais également en charge les domaines DNS privés.

**Azure Load Balancer** : [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) permet aux clients de procéder à la mise à l’échelle de leurs applications et de créer une haute disponibilité pour les services. Load Balancer prend en charge les scénarios entrants et sortants, et offre une latence faible, un débit élevé et une montée en puissance jusqu’à plusieurs millions de flux pour toutes les applications TCP et UDP.

### <a name="data-in-transit"></a>Données en transit

Par défaut, Azure chiffre toutes les communications avec les centres de données Azure. Toutes les transactions en direction du Stockage Azure passant par le portail Azure se produisent via HTTPS.

### <a name="data-at-rest"></a>Données au repos

L’architecture protège les données au repos à l’aide d’un chiffrement, d’un audit de base de données et d’autres mesures.

**Stockage Azure** : pour répondre aux exigences du chiffrement des données au repos, l’ensemble du [Stockage Azure](https://azure.microsoft.com/services/storage/) utilise la fonctionnalité [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Celle-ci permet de protéger et de sauvegarder les données dans le respect des engagements de l’organisation en matière de sécurité et des exigences de conformité définies par NHS Digital.

**Azure Disk Encryption** : [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) exploite la fonctionnalité BitLocker de Windows pour chiffrer les volumes des disques de données. La solution s’intègre à Azure Key Vault pour faciliter le contrôle et la gestion des clés de chiffrement de disque.

**Azure SQL Database** : L’instance Azure SQL Database utilise les mesures suivantes pour la sécurité des bases de données :

- La solution [d’authentification et d’autorisation Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permet de gérer les identités des utilisateurs de bases de données et d’autres services Microsoft dans un emplacement central.
- L’[audit Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) suit les événements de base de données et consigne ceux-ci dans un journal d’audit conservé dans un compte de stockage Azure.
- Azure SQL Database est configuré pour utiliser la technologie [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) qui assure le chiffrement et le déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux de transactions afin de protéger les informations au repos. Transparent Data Encryption protège les données des accès non autorisés.
- Des [règles de pare-feu](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) empêchent tout accès aux serveurs de base de données sans autorisation appropriée. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande.
- La [détection des menaces SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permet de détecter et traiter les menaces potentielles à mesure qu’elles surviennent en déclenchant des alertes de sécurité en cas d’activités de base de données suspectes, de vulnérabilités potentielles, d’attaques par injection de code SQL et de modèles d’accès anormaux aux bases de données.
- Des [colonnes Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantissent que les données sensibles n’apparaissent jamais en tant que texte en clair à l’intérieur du système de base de données. Une fois le chiffrement des données activé, seules des applications clientes ou des serveurs d’applications ayant accès aux clés peuvent accéder aux données en texte clair.
- [SQL Database Dynamic Data Masking](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limite l’exposition des données sensibles en masquant celles-ci aux utilisateurs ou aux applications dépourvus de privilèges. Le masquage dynamique des données peut découvrir automatiquement les données potentiellement sensibles et suggérer les masques appropriés à appliquer. Cela permet d’identifier et de réduire l’accès aux données afin que celles-ci ne quittent pas la base de données via un accès non autorisé. Les clients doivent ajuster les paramètres de masquage dynamique des données pour respecter leur schéma de base de données.

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
- Toutes les clés dans Key Vault sont protégées par des modules de sécurité matériels spécialisés. Le type de clé est une clé RSA de 2048 bits protégée par un module de sécurité matériel.
- Des autorisations minimales requises sont accordées à tous les utilisateurs et à toutes les identités à l’aide du contrôle d’accès en fonction rôle.
- Les journaux de diagnostics pour Key Vault sont activés avec une période de rétention d’au moins 365 jours.
- Les opérations de chiffrement autorisées pour les clés sont restreintes à celles qui sont nécessaires.

**Azure Security Center** : avec [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), cette solution peut appliquer et gérer de façon centralisée des stratégies de sécurité sur les charges de travail, limiter l’exposition aux menaces ainsi que détecter les attaques et y répondre. Par ailleurs, Azure Security Center accède aux configurations existantes des services Azure pour fournir des recommandations en matière de configuration et de service. Cela contribue à l’amélioration du dispositif de sécurité et à la protection des données.

Azure Security Center utilise diverses fonctionnalités de détection pour avertir les clients des attaques potentielles qui ciblent leur environnement. Ces alertes fournissent de précieuses informations sur le déclencheur de l’alerte, les ressources ciblées et la source de l’attaque. Azure Security Center a un ensemble [d’alertes de sécurité prédéfinies](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), qui sont déclenchées en cas de menace ou d’activité suspecte. Les [règles d’alerte personnalisées](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) d’Azure Security Center permettent aux clients de définir de nouvelles alertes de sécurité basées sur les données déjà collectées dans leur environnement.

Azure Security Center fournit des incidents et des alertes de sécurité hiérarchisés, pour simplifier la découverte et la résolution des problèmes potentiels de sécurité. Un [rapport de Threat intelligence](https://docs.microsoft.com/azure/security-center/security-center-threat-report) est généré pour chaque menace détectée afin d’aider les équipes chargées de répondre aux incidents à examiner et à contrecarrer les menaces.

**Azure Application Gateway** : l’architecture réduit le risque de failles de sécurité en utilisant une passerelle d’application avec un pare-feu d’applications web Azure configuré et l’ensemble de règles OWASP activé. Les autres fonctionnalités incluent notamment :

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Activation du [déchargement SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Désactivation de [TLS versions 1.0 et 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Pare-feu d’applications web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (mode de prévention)
- [Mode Prévention](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) avec l’ensemble de règles OWASP 3.0
- Activation de la [journalisation des diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Sondes d’intégrité personnalisées](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) et [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fournissent une protection supplémentaire et des notifications. Azure Security Center fournit également un système de réputation.

### <a name="logging-and-auditing"></a>Journalisation et audit

Les services Azure assurent une journalisation complète de l’activité du système et des utilisateurs, ainsi que de l’intégrité du système :
- **Journaux d’activité** : les [journaux d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fournissent des insights sur les opérations ayant été effectuées sur les ressources d’un abonnement. Les journaux d’activité peuvent aider à déterminer l’initiateur, l’heure d’exécution et l’état d’une opération.
- **Journaux de diagnostic** : les [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluent l’ensemble des journaux d’activité générés par chaque ressource. Ils incluent les journaux des événements système de Windows, les journaux d’activité de Stockage Azure, les journaux d’audit du Key Vault, ainsi que les journaux d’activité de pare-feu et d’accès d’Application Gateway. Tous les journaux de diagnostic sont consignés dans un compte de stockage Azure centralisé et chiffré pour l’archivage. L’utilisateur peut configurer la rétention jusqu’à 730 jours pour répondre aux exigences de rétention spécifiques de l’entreprise.

**Journaux d’activité Azure Monitor** : Ces journaux d’activité sont consolidés dans des [journaux d’activité Azure Monitor](https://azure.microsoft.com/services/log-analytics/) à des fins de traitement, de stockage et de génération de rapports de tableau de bord. Une fois collectées, les données sont organisées dans différents tableaux en fonction du type de données. Toutes les données sont ainsi analysées ensemble, quelle que soit leur source d’origine. Par ailleurs, Azure Security Center s’intègre aux journaux d’activité Azure Monitor pour permettre aux clients d’utiliser des requêtes Kusto afin d’accéder à leurs données d’événement de sécurité et de les combiner avec des données provenant d’autres services.

Cette architecture intègre également les [solutions de supervision](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) suivantes :
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment) : la solution Active Directory Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit une liste hiérarchisée de suggestions spécifiques pour l’infrastructure de serveur déployée.
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment) : la solution SQL Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit aux clients une liste hiérarchisée de suggestions spécifiques pour l’infrastructure de serveur déployée.
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth) : cette solution de contrôle d’intégrité des agents indique le nombre d’agents déployés et leur répartition géographique, ainsi que le nombre d’agents qui ne répondent pas ou qui envoient des données opérationnelles.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity) : la solution Activity Log Analytics facilite l’analyse des journaux d’activité Azure de tous les abonnements Azure d’un client.

**Azure Automation** : [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) stocke, exécute et gère les runbooks. Dans cette solution, les runbooks aident à collecter les journaux d’activité d’Azure SQL Database. La solution Automation [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) permet aux clients d’identifier facilement les changements dans l’environnement.

**Azure Monitor** : [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) aide les utilisateurs à suivre les performances, garantir la sécurité et identifier les tendances en permettant aux organisations d’auditer, de créer des alertes et d’archiver des données, y compris le suivi des appels d’API dans leurs ressources Azure.

## <a name="threat-model"></a>Modèle de menace

Le diagramme de flux de données de cette architecture de référence figure ci-dessous et est également disponible en [téléchargement](https://aka.ms/uknhs-paaswa-tm). Ce modèle peut aider les clients à comprendre les points de risque potentiel de l’infrastructure du système lors de l’apport de modifications.

![Modèle de menace Application web IaaS pour UK NHS](images/uknhs-paaswa-threat-model.png?raw=true "Modèle de menace Application web IaaS pour UK NHS")

## <a name="compliance-documentation"></a>Documentation sur la conformité

[L’Azure Blueprint Sécurité et conformité – Matrice de responsabilités des clients du UK NHS](https://aka.ms/uknhs-crm) répertorie toutes les exigences du UK NHS. La matrice indique si l’implémentation de chacun des principes revient à Microsoft, au client ou aux deux.

[L’Azure Blueprint Sécurité et conformité - Matrice d’implémentation d’application web IaaS en lien avec le RGPD](https://aka.ms/uknhs-paaswa-cim) fournit des informations sur les articles du RGPD que prend en compte l’architecture d’application web IaaS, dont une description détaillée de la façon dont l’implémentation répond aux exigences de chaque article couvert.

## <a name="guidance-and-recommendations"></a>Instructions et recommandations

### <a name="vpn-and-expressroute"></a>VPN et ExpressRoute

Un tunnel VPN sécurisé ou un routage [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) doivent être configurés pour établir une connexion sécurisée aux ressources déployées en lien avec cette architecture de référence d’application web PaaS. En configurant correctement un VPN ou un routage ExpressRoute, les clients peuvent ajouter une couche de protection au niveau des données en transit.

En implémentant un tunnel VPN sécurisé avec Azure, il est possible de créer une connexion privée virtuelle entre un réseau local et un réseau virtuel Azure. Cette connexion via Internet permet aux clients de &quot;tunneliser&quot; des informations de manière sécurisée dans une liaison chiffrée entre leur réseau et Azure. La technologie de VPN de site à site, aussi sécurisée qu’aguerrie, est déployée par des entreprises de toutes tailles depuis des décennies. Le [mode tunnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) est utilisé dans cette option comme un mécanisme de chiffrement.

Étant donné que le trafic à l’intérieur du tunnel VPN transite par Internet avec un VPN de site à site, Microsoft offre une autre option de connexion encore plus sécurisée. Azure ExpressRoute est une liaison réseau étendu dédiée entre Azure et un emplacement local ou un fournisseur d’hébergement Exchange. Les connexions ExpressRoute ne transitent pas par Internet, et offrent de meilleurs niveaux de fiabilité, de rapidité, de latence et de sécurité que les connexions classiques via Internet. Par ailleurs, comme il s’agit d’une connexion directe du fournisseur de télécommunications du client, les données ne circulent pas sur Internet et n’y sont pas exposées.

Les meilleures pratiques concernant l’implémentation d’un réseau hybride sécurisé qui étend un réseau local à Azure sont [décrites ici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Clause d'exclusion de responsabilité

- Ce document est fourni à titre d’information uniquement. MICROSOFT N’ACCORDE AUCUNE GARANTIE EXPRESSE, IMPLICITE OU STATUTAIRE EN LIEN AVEC LES INFORMATIONS CONTENUES DANS CE DOCUMENT. Ce document est fourni &quot;en l’état.&quot; Les informations et les points de vue exprimés dans ce document, y compris les URL et autres références à des sites web, peuvent être modifiés sans préavis. Les clients qui lisent ce document assument les risques liés à son utilisation.
- Ce document n’accorde aux clients aucun droit légal de propriété intellectuelle sur les produits ou solutions Microsoft.
- Les clients peuvent copier et utiliser ce document pour un usage interne, à titre de référence.
- Certaines recommandations contenues dans ce document peuvent entraîner une augmentation de l’utilisation des données, du réseau ou des ressources de calcul dans Azure, entraînant éventuellement des coûts de licence ou d’abonnement supplémentaires pour le client.
- Cette architecture constitue une base que les clients peuvent modifier en fonction de leurs besoins. Elle ne doit pas être utilisée telle quelle dans un environnement de production.
- Développé en tant que référence, ce document ne doit pas être utilisé pour définir tous les moyens par lesquels un client peut satisfaire à des réglementations et exigences de conformité spécifiques. Les clients doivent rechercher une assistance juridique auprès de leur organisation pour connaître les implémentations client approuvées.
