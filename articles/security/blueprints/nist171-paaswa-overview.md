---
title: Blueprint sur la sécurité et la conformité Azure - Application web PaaS pour NIST SP 800-171
description: Blueprint sur la sécurité et la conformité Azure - Application web PaaS pour NIST SP 800-171
services: security
author: jomolesk
ms.assetid: eea21a0a-5930-43e8-937f-5419c20744c9
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: f9773c3b372ab22cbcd99828e147d23c185c4eb6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127325"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-nist-special-publication-800-171"></a>Blueprint sur la sécurité et la conformité Azure - Application web PaaS pour NIST Special Publication 800-171

## <a name="overview"></a>Présentation
[NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) fournit des instructions pour protéger les informations non classifiées contrôlées qui se trouvent dans des systèmes d’information et des organisations qui ne font pas partie du secteur public. NIST SP 800-171 établit 14 familles d’exigences de sécurité pour protéger la confidentialité des informations non classifiées contrôlées.

Ce blueprint sur la sécurité et la conformité d’Azure fournit des conseils pour aider les clients à déployer une plateforme PaaS dans Azure qui implémente une partie des contrôles de NIST SP 800-171. Cette solution montre comment les clients peuvent répondre à des exigences de conformité et de sécurité spécifiques. Elle sert également de base pour les clients qui souhaitent générer et configurer leur propre application web dans Azure.

Cette architecture de référence, ainsi que le guide d‘implémentation et le modèle de menace associés, sont destinés à servir de base aux clients qui doivent les adapter à leurs besoins spécifiques. Ils ne doivent pas être utilisés tels quels dans un environnement de production. Le déploiement de cette architecture sans modification est insuffisant pour répondre entièrement aux exigences de NIST SP 800-171. Il incombe aux clients d‘effectuer des contrôles de conformité et de sécurité appropriés de toute solution générée à l‘aide de cette architecture. Les exigences peuvent varier selon les spécificités de l‘implémentation de chaque client.

## <a name="architecture-diagram-and-components"></a>Diagramme et composants de l’architecture

Ce blueprint sur la sécurité et la conformité d‘Azure fournit une architecture de référence pour une application web PaaS avec un back-end Azure SQL Database. L‘application web est hébergée dans un environnement App Service dédié, privé et isolé dans un centre de données Azure. L‘environnement équilibre la charge du trafic pour l‘application web entre des machines virtuelles (VM) gérées par Azure. Cette architecture inclut également des groupes de sécurité réseau (NSG), une passerelle d‘application Azure, Azure DNS et Azure Load Balancer.

Les bases de données Azure SQL peuvent être configurées avec des index columnstore afin d’améliorer l’analytique et la création de rapports. Les bases de données Azure SQL peuvent faire l’objet d’un scale-up ou d’un scale-down ou bien complètement arrêtées selon l’utilisation du client. La totalité du trafic SQL est chiffrée avec SSL grâce à l’inclusion de certificats auto-signés. En guise de meilleure pratique, Azure préconise l’utilisation d’une autorité de certification de confiance afin de renforcer la sécurité.

La solution utilise des comptes de stockage Azure que les clients peuvent configurer pour utiliser Storage Service Encryption afin de maintenir la confidentialité des données au repos. Azure stocke trois copies des données dans un centre de données sélectionné par le client pour assurer la résilience. Le stockage géoredondant garantit que les données sont répliquées dans un centre de données secondaire situé à des centaines de kilomètres, et stockées à nouveau sous forme de trois copies dans ce centre de données. Cette organisation empêche qu‘un événement dommageable se produisant dans le centre de données principal du client entraîne une perte de données.

Pour renforcer la sécurité, toutes les ressources dans cette solution sont gérées sous forme de groupe de ressources dans Azure Resource Manager. Le contrôle d‘accès en fonction du rôle Azure Active Directory (Azure AD) permet de contrôler l‘accès aux ressources déployées. Ces ressources incluent les clés de client dans Azure Key Vault. L’intégrité du système est surveillée dans Azure Monitor. Les clients configurent ce service de supervision pour capturer les journaux d’activité. L‘intégrité du système est affichée dans un tableau de bord unique qui est facile à utiliser.

Azure SQL Database est généralement géré via SQL Server Management Studio. Il s‘exécute à partir d‘une machine locale configurée pour accéder à la base de données SQL via une connexion VPN ou Azure ExpressRoute sécurisée.

Application Insights fournit la gestion des performances des applications en temps réel et analytique via Azure Monitor journaux *Microsoft recommande de configurer une connexion VPN ou ExpressRoute pour l’importation de données et de gestion dans la référence sous-réseau de l’architecture.*

![Diagramme de l’architecture de référence des applications web PaaS pour NIST SP 800-171](images/nist171-paaswa-architecture.png "Diagramme de l’architecture de référence des applications web PaaS pour NIST SP 800-171")

Cette solution utilise les services Azure suivants. Pour plus d‘informations, consultez la section sur l‘[architecture de déploiement](#deployment-architecture).

- Machines virtuelles Azure
    - (1) Gestion/bastion (Windows Server 2016 Datacenter)
- Réseau virtuel Azure
    - (1) réseau /16
    - (4) réseaux /24
    - (4) Groupes de sécurité réseau
- Azure Application Gateway
    - Pare-feu d’application web
        - Mode de pare-feu : prévention
        - Ensemble de règles : OWASP
        - Port de l’écouteur : 443
- Application Insights
- Azure Active Directory
- App Service Environment v2
- Azure Automation
- Azure DNS
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor (journaux)
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Stockage Azure
- Azure Automation
- Azure Web Apps 

## <a name="deployment-architecture"></a>Architecture de déploiement
La section ci-après décrit en détail les éléments nécessaires au déploiement et à l’implémentation.

**Azure Resource Manager** : [Le Gestionnaire de ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) peut être utilisé par les clients pour travailler avec les ressources de la solution en tant que groupe. Les clients peuvent déployer, mettre à jour ou supprimer toutes les ressources de la solution en une seule opération coordonnée. Les clients utilisent un modèle pour le déploiement. Ce modèle peut fonctionner pour différents environnements, par exemple des environnements de test, de préproduction et de production. Le Gestionnaire des ressources Azure Resource Manager offre des fonctionnalités de sécurité, d’audit et de marquage pour aider les clients à gérer leurs ressources après le déploiement de celles-ci.

**Hôte bastion** : L’hôte bastion est le seul point d’entrée que les utilisateurs peuvent utiliser pour accéder aux ressources déployées dans cet environnement. L‘hôte bastion fournit une connexion sécurisée à des ressources déployées en autorisant uniquement le trafic distant provenant d‘adresses IP publiques figurant sur une liste verte. Pour autoriser le trafic de Bureau à distance, la source du trafic doit être définie dans le groupe de sécurité réseau.

Cette solution crée une machine virtuelle en tant qu‘hôte bastion joint au domaine avec les configurations suivantes :
-   [Extension Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware).
-   [Extension Azure Diagnostics](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) avec Key Vault.
-   [Stratégie d‘arrêt automatique](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) pour réduire la consommation des ressources de machine virtuelle non utilisées.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) est activé pour que les informations d‘identification et autres secrets s‘exécutent dans un environnement protégé, isolé du système d‘exploitation exécuté.

**Applications Web**: [Applications Web](https://docs.microsoft.com/azure/app-service/) est une fonctionnalité Azure App Service. Les clients peuvent l‘utiliser pour créer et héberger des applications web dans le langage de programmation de leur choix sans gérer l‘infrastructure. Elle fournit la mise à l‘échelle automatique et la haute disponibilité. Elle prend en charge Windows et Linux, et permet des déploiements automatisés à partir de GitHub, Azure DevOps ou n‘importe quel dépôt Git.

**Azure App Service Environment** : [Environnement App Service](https://docs.microsoft.com/azure/app-service/environment/intro) est une fonctionnalité App Service. Elle fournit un environnement totalement isolé et dédié pour l‘exécution sécurisée des applications App Service à grande échelle.

L‘environnement App Service est isolé pour exécuter une seule application. Il est toujours déployé dans un réseau virtuel. En raison de cette fonctionnalité d‘isolation, l‘architecture de référence dispose d‘une isolation complète des locataires, et elle est supprimée de l‘environnement multilocataire d‘Azure. Les clients ont un contrôle affiné sur le trafic réseau entrant et sortant des applications. Les applications peuvent établir des connexions sécurisées à haute vitesse via des réseaux virtuels aux ressources d’entreprise locales. Les clients peuvent faire une « mise à l’échelle automatique » avec l’environnement App Service basé sur les mesures de charge, le budget disponible ou une planification définie.

L’utilisation de l’environnement App Service pour cette architecture fournit les contrôles et les configurations suivants :

- Hébergement dans un réseau virtuel Azure sécurisé avec application de règles de sécurité réseau.
- Certificat d’équilibreur de charge interne auto-signé pour la communication HTTPS. Microsoft préconise l’utilisation d’une autorité de certification approuvée pour renforcer la sécurité.
- [Mode d’équilibrage de charge interne](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (mode 3).
- Désactivation de [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings).
- Changement du [chiffrement TLS](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings).
- Contrôle des [ports réseau de trafic entrant](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic).
- [Pare-feu d’applications web – Restreindre les données](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall).
- Autorisation du [trafic Azure SQL Database](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview).

### <a name="virtual-network"></a>Réseau virtuel
L’architecture définit un réseau privé virtuel avec l’espace d’adressage 10.200.0.0/16.

**Groupes de sécurité réseau** : [Groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contiennent des listes de contrôle d’accès qui autorisent ou refusent le trafic au sein d’un réseau virtuel. Les groupes de sécurité réseau peuvent être utilisés pour sécuriser le trafic au niveau d’un sous-réseau ou d’une machine virtuelle. Les NSG disponibles sont les suivants :
- Un groupe de sécurité réseau pour la passerelle d’application (Application Gateway)
- Un groupe de sécurité réseau pour l’environnement App Service
- Un groupe de sécurité réseau pour SQL Database
- Un groupe de sécurité réseau pour l’hôte bastion

Chaque groupe de sécurité réseau a des ports et protocoles spécifiques ouverts afin que la solution puisse fonctionner correctement et en toute sécurité. En outre, les configurations suivantes sont activées pour chaque groupe de sécurité réseau :
  - Les [événements et journaux de diagnostic](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) sont activés et stockés dans un compte de stockage.
  - Journaux d’Azure Monitor est connecté à la [diagnostics des groupes de sécurité réseau](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Sous-réseaux** : Chaque sous-réseau est associé à son groupe de sécurité réseau correspondant.

**Azure DNS** : Le système DNS (Domain Name) est chargé de traduire (ou résoudre) un nom de site Web ou service en son adresse IP. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) est un service d’hébergement pour domaines DNS, qui assure la résolution de noms à l’aide de l’infrastructure Azure. En hébergeant des domaines dans Azure, les utilisateurs peuvent gérer des enregistrements DNS en utilisant les mêmes informations d’identification, API, outils et modes de facturation que pour d’autres services Azure. Azure DNS prend désormais également en charge les domaines DNS privés.

**Azure Load Balancer** : [L’équilibreur de charge](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) peut être utilisé par les clients à l’échelle leurs applications et de créer une haute disponibilité pour les services. Load Balancer prend en charge les scénarios entrants et sortants. Il offre une latence faible et un débit élevé, et peut effectuer un scale-up jusqu’à des millions de flux pour toutes les applications TCP et UDP.

### <a name="data-in-transit"></a>Données en transit
Par défaut, Azure chiffre toutes les communications avec les centres de données Azure. Toutes les transactions en direction du service Stockage Azure par le biais du Portail Azure s’effectuent via HTTPS.

### <a name="data-at-rest"></a>Données au repos

L’architecture protège les données au repos à l’aide d’un chiffrement, d’un audit de base de données et d’autres mesures.

**Stockage Azure** : pour satisfaire aux exigences de chiffrement des données au repos, l’ensemble du service [Stockage](https://azure.microsoft.com/services/storage/) utilise la fonctionnalité de chiffrement [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Cette fonctionnalité permet de protéger et de sauvegarder les données dans le respect des engagements de l’organisation en matière de sécurité et des exigences de conformité définies par NIST SP 800-171.

**Azure Disk Encryption** : [Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utilise la fonctionnalité BitLocker de Windows pour chiffrer les volumes des disques de données. La solution s’intègre à Key Vault pour favoriser le contrôle et la gestion des clés de chiffrement de disque.

**Azure SQL Database** : L’instance SQL Database utilise les mesures suivantes pour la sécurité des bases de données :
-   La solution [d’authentification et d’autorisation Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) permet de gérer les identités des utilisateurs de bases de données et d’autres services Microsoft dans un emplacement central.
-   L’[audit Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) suit les événements de base de données et consigne ceux-ci dans un journal d’audit conservé dans un compte de stockage Azure.
-   SQL Database est configuré pour utiliser [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Il effectue le chiffrement et le déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux de transactions, afin de protéger les informations au repos. Transparent Data Encryption garantit que les données stockées ne font pas l’objet d’accès non autorisés.
-   Des [règles de pare-feu](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) empêchent tout accès aux serveurs de base de données sans autorisation appropriée. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande.
-   [SQL Threat Detection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permet la détection et la réponse aux menaces potentielles lorsqu’elles se produisent. Il fournit des alertes de sécurité en cas d’activités de base de données suspectes, de vulnérabilités potentielles, d’attaques par injection de code SQL et de modalités d’accès anormales aux bases de données.
-   Les [colonnes chiffrées](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantissent que les données sensibles n’apparaissent jamais en texte clair à l’intérieur du système de base de données. Une fois le chiffrement des données activé, seules des applications clientes ou des serveurs d’applications ayant accès aux clés peuvent accéder aux données en texte clair.
- [Dynamic Data Masking](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limite l’exposition des données sensibles en masquant celles-ci aux utilisateurs ou aux applications dépourvus de privilèges. Il peut détecter automatiquement les données potentiellement sensibles et suggérer les masques appropriés à appliquer. Dynamic Data Masking permet de réduire l’accès afin que les données sensibles ne sortent pas de la base de données via un accès non autorisé. *Les clients doivent ajuster les paramètres pour respecter leur schéma de base de données.*

### <a name="identity-management"></a>Gestion des identités
Les technologies suivantes offrent des fonctionnalités de gestion de l’accès aux données dans l’environnement Azure :
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) est le service multilocataire basé sur le cloud de Microsoft pour la gestion des identités et des annuaires. Tous les utilisateurs de cette solution sont créés dans Azure AD et incluent les utilisateurs qui accèdent à la base de données SQL.
-   L’authentification auprès de l’application est effectuée à l’aide d’Azure AD. Pour plus d’informations, consultez [Intégrer des applications à Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Le chiffrement des colonnes de base de données utilise également Azure AD pour authentifier l’application auprès de SQL Database. Pour plus d’informations, découvrez comment [protéger les données sensibles dans SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [RBAC Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) peut être utilisé par les administrateurs pour définir des autorisations d’accès précises. Avec cela, ils peuvent accorder uniquement les droits d’accès dont les utilisateurs ont besoin pour effectuer leur travail. Au lieu d’attribuer à tous les utilisateurs des autorisations d’accès illimitées aux ressources Azure, les administrateurs peuvent autoriser seulement certaines actions d’accès aux ressources et aux données. Par exemple, l’accès aux abonnements est limité à l’administrateur d’abonnements.
- La solution [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) permet aux clients de minimiser le nombre d’utilisateurs qui ont accès à des informations spécifiques. Les administrateurs peuvent utiliser Azure AD Privileged Identity Management pour découvrir, restreindre et surveiller les identités privilégiées et leur accès aux ressources. Cette fonctionnalité permet également d’appliquer un accès administratif juste-à-temps à la demande, si nécessaire.
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

**Azure Security Center** : avec [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), les clients peuvent appliquer et gérer de façon centralisée des stratégies de sécurité sur les charges de travail, limiter l’exposition aux menaces ainsi que détecter les attaques et y répondre. Security Center accède également aux configurations existantes des services Azure pour fournir des suggestions en matière de configuration et de service. Cela contribue à l’amélioration du dispositif de sécurité et à la protection des données.

Security Center utilise diverses fonctionnalités de détection pour avertir les clients des attaques potentielles qui ciblent leurs environnements. Ces alertes fournissent de précieuses informations sur le déclencheur de l’alerte, les ressources ciblées et la source de l’attaque. Security Center possède un ensemble d’[alertes de sécurité prédéfinies](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), qui sont déclenchées en cas de menace ou d’activité suspecte. Les clients peuvent utiliser les [règles d’alerte personnalisées](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) pour définir de nouvelles alertes de sécurité basées sur les données déjà collectées dans leur environnement.

Security Center fournit des alertes et incidents de sécurité classés par ordre de priorité. Security Center permet aux clients de découvrir et de résoudre plus facilement les problèmes de sécurité potentiels. Un [rapport d’informations sur les menaces](https://docs.microsoft.com/azure/security-center/security-center-threat-report) est généré pour chaque menace détectée. Les équipes de réponse aux incidents peuvent utiliser ces rapports lorsqu’elles enquêtent pour corriger des menaces.

**Azure Application Gateway** : L’architecture réduit le risque de failles de sécurité à l’aide d’une passerelle d’application avec un pare-feu d’applications web configuré et activé l’ensemble de règles OWASP. Les autres fonctionnalités incluent notamment :

- [SSL de bout en bout](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Activation du [déchargement SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal).
- Désactivation de [TLS versions 1.0 et 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Pare-feu d’applications web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (mode de prévention).
- [Mode de prévention](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) avec l’ensemble de règles OWASP 3.0.
- Activation de la [journalisation des diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Sondes d’intégrité personnalisées](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal).
- [Security Center](https://azure.microsoft.com/services/security-center) et [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) fournissent une protection et des notifications supplémentaires. Security Center fournit également un système de réputation.

### <a name="logging-and-auditing"></a>Journalisation et audit

Les services Azure assurent une journalisation complète de l’activité du système et des utilisateurs, ainsi que de l’intégrité du système :
- **Journaux d’activité** : les [journaux d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fournissent des insights sur les opérations ayant été effectuées sur les ressources d’un abonnement. Les journaux d’activité peuvent aider à déterminer l’initiateur, l’heure d’exécution et l’état d’une opération.
- **Journaux de diagnostic** : les [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluent l’ensemble des journaux générés par chaque ressource. Ils incluent les journaux d’activité système des événements Windows, les journaux d’activité de stockage, les journaux d’audit Key Vault, ainsi que les journaux d’activité de pare-feu et d’accès d’Application Gateway. Tous les journaux de diagnostic sont consignés dans un compte de stockage Azure centralisé et chiffré pour l’archivage. Les utilisateurs peuvent configurer la période de rétention jusqu‘à 730 jours, pour répondre à leurs besoins spécifiques.

**Journaux Azure Monitor** : Les journaux sont consolidés dans [Azure Monitor enregistre](https://azure.microsoft.com/services/log-analytics/) pour le traitement, le stockage et la création de rapports de tableau de bord. Une fois les données collectées, elles sont organisées dans des tables distinctes pour chaque type de données au sein des espaces de travail Log Analytics. De cette façon, toutes les données peuvent être analysées ensemble, quelle que soit leur source d’origine. Security Center s’intègre avec les journaux d’Azure Monitor. Les clients peuvent utiliser des requêtes de Kusto pour accéder à leurs données d’événement de sécurité et de les combiner avec des données provenant d’autres services.

Azure suivant [solutions de surveillance](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) sont inclus dans le cadre de cette architecture :
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment) : la solution Active Directory Health Check permet d’évaluer les risques et l’intégrité des environnements de serveur à intervalles réguliers. Elle fournit une liste hiérarchisée de suggestions propres à l’infrastructure de serveurs déployée.
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment) : la solution SQL Health Check permet d’évaluer les risques et l’intégrité des environnements de serveur à intervalles réguliers. Elle fournit aux clients une liste hiérarchisée de suggestions propres à l’infrastructure de serveurs déployée.
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth) : la solution Agent Health signale le nombre d’agents déployés et leur répartition géographique. Elle signale également le nombre d’agents qui ne répondent pas et le nombre d’agents qui envoient des données opérationnelles.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity) : la solution Activity Log Analytics facilite l’analyse des journaux d’activité Azure de tous les abonnements Azure d’un client.

**Azure Automation** : [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) stocke, exécute et gère les runbooks. Dans cette solution, les runbooks aident à collecter les journaux d’activité de SQL Database. Les clients peuvent utiliser la solution Automation [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) pour identifier facilement les changements dans l’environnement.

**Azure Monitor** : [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) aide les utilisateurs à suivre les performances, à garantir la sécurité et à identifier les tendances. Les organisations peuvent l’utiliser pour auditer, créer des alertes et archiver les données. Elles peuvent également suivre les appels d’API dans leurs ressources Azure.

**Application Insights** : [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) est un service de gestion des performances d’application extensible pour les développeurs web sur plusieurs plateformes. Application Insights détecte les anomalies de performances. Les clients peuvent l’utiliser pour surveiller l’application web en direct. Application Insights inclut de puissants outils d’analyse qui aident les clients à diagnostiquer les problèmes et à comprendre ce que les utilisateurs font avec leurs applications. Elle a été conçue pour aider les clients à améliorer continuellement les performances et l’ergonomie des applications.

## <a name="threat-model"></a>Modèle de menace

Le diagramme de flux de données de cette architecture de référence figure ci-dessous et est également disponible en [téléchargement](https://aka.ms/nist171-paaswa-tm). Ce modèle peut aider les clients à comprendre les points de risque potentiel de l’infrastructure du système lors de l’apport de modifications.

![Modèle de risque des applications web PaaS pour NIST SP 800-171](images/nist171-paaswa-threat-model.png "Modèle de risque des applications web PaaS pour NIST SP 800-171")

## <a name="compliance-documentation"></a>Documentation sur la conformité
Le [Blueprint sur la sécurité et la conformité Azure - Matrice de responsabilités des clients de NIST SP 800-171](https://aka.ms/nist171-crm) liste tous les contrôles de sécurité exigés par NIST SP 800-171. La matrice indique si l’implémentation de chaque contrôle revient à Microsoft, au client, ou est partagé entre les deux.

Le [blueprint sur la sécurité et la conformité Azure - Matrice d’implémentation des contrôles des applications web PaaS pour NIST SP 800-171](https://aka.ms/nist171-paaswa-cim) fournit des informations sur les contrôles de NIST SP 800-171 que prend en compte l’architecture des applications web PaaS. Il comprend une description détaillée de la façon dont l’implémentation répond aux exigences de chaque contrôle couvert.

## <a name="guidance-and-recommendations"></a>Instructions et recommandations
### <a name="vpn-and-expressroute"></a>VPN et ExpressRoute
Un tunnel VPN sécurisé ou un routage [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) doivent être configurés pour établir une connexion sécurisée aux ressources déployées en lien avec cette architecture de référence d’application web PaaS. En configurant correctement un VPN ou un routage ExpressRoute, les clients peuvent ajouter une couche de protection au niveau des données en transit.

En implémentant un tunnel VPN sécurisé avec Azure, il est possible de créer une connexion privée virtuelle entre un réseau local et un réseau virtuel Azure. Cette connexion via Internet permet aux clients de « tunneler » des informations en toute sécurité par le biais d’une liaison chiffrée entre leur réseau et Azure. La technologie de VPN de site à site, aussi sécurisée qu’aguerrie, est déployée par des entreprises de toutes tailles depuis des décennies. Le [mode tunnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) est utilisé dans cette option comme un mécanisme de chiffrement.

Étant donné que le trafic à l’intérieur du tunnel VPN transite par Internet avec un VPN de site à site, Microsoft offre une autre option de connexion encore plus sécurisée. ExpressRoute est une liaison réseau étendu dédiée entre Azure et un emplacement local ou un fournisseur d’hébergement Exchange. Les connexions ExpressRoute se raccordent directement au fournisseur de télécommunications d’un client. Par conséquent, les données ne transitent pas par Internet et ne sont pas exposées à ce dernier. Ces connexions offrent davantage de fiabilité, des vitesses supérieures, des latences inférieures et une sécurité renforcée par rapport aux connexions standard.

Les meilleures pratiques concernant l’implémentation d’un réseau hybride sécurisé qui étend un réseau local à Azure sont [décrites ici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Clause d'exclusion de responsabilité

 - Ce document est fourni à titre d’information uniquement. MICROSOFT N’ACCORDE AUCUNE GARANTIE EXPRESSE, IMPLICITE OU STATUTAIRE EN LIEN AVEC LES INFORMATIONS CONTENUES DANS CE DOCUMENT. Ce document est fourni « en l’état ». Les informations et les points de vue exprimés dans ce document, y compris les URL et autres références à des sites web, peuvent être modifiés sans préavis. Les clients qui lisent ce document assument les risques liés à son utilisation.
 - Ce document n’accorde aux clients aucun droit légal de propriété intellectuelle sur les produits ou solutions Microsoft.
 - Les clients peuvent copier et utiliser ce document pour un usage interne, à titre de référence.
 - Certaines recommandations contenues dans ce document peuvent entraîner une augmentation des taux d’utilisation des données, des réseaux ou des ressources de calcul dans Azure, débouchant sur des coûts de licence ou d’abonnement supplémentaires.
 - Cette architecture constitue une base que les clients peuvent modifier en fonction de leurs besoins. Elle ne doit pas être utilisée telle quelle dans un environnement de production.
 - Développé en tant que référence, ce document ne doit pas être utilisé pour définir tous les moyens par lesquels un client peut satisfaire à des réglementations et exigences de conformité spécifiques. Les clients doivent rechercher une assistance juridique auprès de leur organisation pour connaître les implémentations client approuvées.
