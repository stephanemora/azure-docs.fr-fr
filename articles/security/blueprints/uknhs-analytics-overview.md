---
title: Blueprint de sécurité et de conformité Azure - Analytique données pour UK NHS
description: Blueprint de sécurité et de conformité Azure - Analytique données pour UK NHS
services: security
author: jomolesk
ms.assetid: 103dff31-e262-44a6-9b50-3b3467c0cb3a
ms.service: security
ms.topic: article
ms.date: 06/15/2018
ms.author: jomolesk
ms.openlocfilehash: 8885eba0d69c869ad5d298094b835f0351d8d94d
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2018
ms.locfileid: "37343201"
---
# <a name="azure-security-and-compliance-blueprint-data-analytics-for-uk-nhs"></a>Blueprint de sécurité et de conformité Azure : Analytique données pour UK NHS

## <a name="overview"></a>Vue d'ensemble

Cette solution Azure Blueprint Sécurité et conformité fournit une architecture de référence et des instructions pour une solution d’analytique données adaptée à la collecte, au stockage, à l’analyse et à la récupération de données de santé. Cette solution montre les façons dont les clients peuvent se conformer aux instructions fournies dans le [Guide de bonne pratique de la sécurité cloud](https://digital.nhs.uk/data-and-information/looking-after-information/data-security-and-information-governance/nhs-and-social-care-data-off-shoring-and-the-use-of-public-cloud-services/health-and-social-care-cloud-security-good-practice-guide) publié par [NHS Digital](https://digital.nhs.uk/), un partenaire du Ministère de la santé et des affaires sociales du Royaume-Uni. Le guide de bonne pratique de la sécurité Cloud est basé sur les 14 [principes de la sécurité cloud](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) publiés par le centre national de cybersécurité du Royaume-Uni.

Cette architecture de référence, le guide d’implémentation et le modèle de menace, sont destinés à servir de base aux clients qui doivent les ajuster à leurs besoins spécifiques et non les utiliser tels quels en environnement de production sans configuration supplémentaire. Il incombe aux clients d’évaluer de façon appropriée la sécurité et la conformité de toute solution basée sur cette architecture, car les exigences peuvent varier en fonction des spécificités de chaque implémentation.

## <a name="architecture-diagram-and-components"></a>Diagramme et composants de l’architecture

Cette solution offre une plateforme analytique sur la base de laquelle les clients peuvent créer leurs propres outils d’analyse. L’architecture de référence présente un cas d’usage générique dans le cadre duquel les clients entrent leurs données soit par le biais d’importations de données en bloc effectuées par l’administrateur SQL/de données, soit par l’intermédiaire de mises à jour de données opérationnelles mises en œuvre par un utilisateur du secteur opérationnel. Ces deux axes de travail incorporent la solution Azure Functions pour l’importation des données dans Azure SQL Database et toutes les connexions externes requièrent TLSv1.2. Chaque client doit configurer Azure Functions par le biais du portail Azure pour la gestion des tâches d’importation conformément à ses exigences d’analyse.

Azure offre aux clients une multitude de services de génération de rapports et d’analyse ; toutefois, cette solution incorpore les services Azure Analysis Services parallèlement à Azure SQL Database pour parcourir rapidement les données et accélérer la fourniture de résultats par le biais d’une modélisation plus efficace des données client. Azure Analytics Services est une forme d’apprentissage automatique destinée à accroître la vitesse des requêtes en découvrant de nouvelles relations entre les jeux de données. Une fois cet apprentissage effectué par le biais de diverses fonctions statistiques, il est possible de synchroniser jusqu’à 7 pools de requêtes supplémentaires (8 au total en comptant le serveur du client) avec les mêmes modèles tabulaires pour répartir la charge de travail des requêtes et réduire les temps de réponse.

Les services Azure SQL Database sont configurables avec des index columnstore afin d’améliorer les fonctions d’analyse et de génération de rapports. Azure Analytics Services et Azure SQL Database peuvent faire l’objet d’une montée ou descente en puissance ou être complètement arrêtés en fonction de l’utilisation du client. La totalité du trafic SQL est chiffrée avec SSL grâce à l’inclusion de certificats auto-signés. En guise de meilleure pratique, Azure préconise l’utilisation d’une autorité de certification de confiance afin de renforcer la sécurité.

Une fois les données chargées dans Azure SQL Database et assimilées par Azure Analysis Services, elles sont synthétisées par l’utilisateur du secteur opérationnel et par l’administrateur SQL/de données à l’aide de Power BI. Power BI assure un affichage intuitif des données et fusionne les informations de plusieurs jeux de données afin d’offrir un meilleur aperçu de l’activité. Grâce à son haut niveau d’adaptabilité et à sa simplicité d’intégration à Azure SQL Database, Power BI est configurable pour la prise en charge d’un large éventail de scénarios basés sur les besoins métiers des clients.

La solution utilise des comptes de stockage Azure que les clients peuvent configurer pour utiliser Storage Service Encryption afin de maintenir la confidentialité des données au repos. Azure stocke trois copies des données dans un centre de données choisi par le client pour assurer la résilience. Le stockage géographiquement redondant garantit que les données sont répliquées dans un centre de données secondaire situé à des centaines de kilomètres et stockées en trois copies dans ce centre de données, ce qui empêche une perte de données en cas de dysfonctionnement au niveau du centre de données principal du client.

Pour renforcer la sécurité, toutes les ressources dans cette solution sont gérées sous forme de groupe de ressources dans Azure Resource Manager. Le contrôle d’accès en fonction du rôle Azure Active Directory est utilisé pour contrôler l’accès aux ressources déployées ainsi qu’à leurs clés dans Azure Key Vault. L’intégrité du système est surveillée dans Azure Security Center et Azure Monitor. Les clients configurent ces deux services de surveillance pour la capture de journaux et la centralisation des informations concernant l’intégrité du système dans un tableau de bord facilement consultable.

Azure SQL Database est généralement géré dans SQL Server Management Studio, qui s’exécute à partir d’une machine locale configurée pour accéder à Azure SQL Database via une connexion VPN ou ExpressRoute sécurisée. **Microsoft recommande de configurer une connexion VPN ou ExpressRoute pour la gestion et l’importation de données dans le groupe de ressources de l’architecture de référence**.

![Diagramme de l’architecture de référence en matière d’analytique des données pour le UK NHS](images/uknhs-analytics-architecture.png?raw=true "Diagramme de l’architecture de référence en matière d’analytique des données pour le UK NHS")

Cette solution utilise les services Azure suivants. Les informations détaillées concernant l’architecture de déploiement figurent à la section [Architecture de déploiement](#deployment-architecture).

- Azure Active Directory
- Azure Analysis Services
- Azure Automation
- Azure Data Catalog
- Azure Disk Encryption
- Azure Event Grid
- Azure Functions
- Azure Key Vault
- Azure Monitor
- Azure Security Center
- Azure SQL Database
- Stockage Azure
- Réseau virtuel Azure
    - (1) /16 réseau
    - (2) /24 réseaux
    - (2) Groupes de sécurité réseau
- tableau de bord Power BI

## <a name="deployment-architecture"></a>Architecture de déploiement

La section ci-après décrit en détail les éléments nécessaires au déploiement et à l’implémentation.

**Azure Functions** : [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) est un service de calcul serverless qui permet aux utilisateurs d’exécuter du code à la demande sans provisionner ou gérer explicitement l’infrastructure. Utilisez Azure Functions pour exécuter un script ou du code en réponse à un grand nombre d’événements.

**Azure Analysis Service** : [Azure Analysis Service](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) fournit une modélisation des données d’entreprise et une intégration aux services de plateforme de données Azure. Azure Analysis Service accélère la navigation dans de gros volumes de données en combinant les données provenant de plusieurs sources dans un seul modèle de données.

### <a name="virtual-network"></a>Réseau virtuel

L’architecture définit un réseau privé virtuel avec l’espace d’adressage 10.200.0.0/16.

**Groupes de sécurité réseau** : les [groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contiennent des listes de contrôle d’accès qui autorisent ou refusent le trafic au sein d’un réseau virtuel. Les groupes de sécurité réseau peuvent être utilisés pour sécuriser le trafic au niveau d’un sous-réseau ou d’une machine virtuelle individuelle. Les groupes de sécurité réseau suivants existent :

  - 1 groupe de sécurité réseau pour Active Directory
  - 1 groupe de sécurité réseau pour la charge de travail

Chaque groupe de sécurité réseau a des ports et protocoles spécifiques ouverts pour que la solution fonctionne correctement et de manière sécurisée. Par ailleurs, les configurations suivantes sont activées pour chaque groupe de sécurité réseau :

- Les [événements et journaux de diagnostic](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) sont activés et stockés dans un compte de stockage.
- Log Analytics est connecté aux [journaux de diagnostic du groupe de sécurité réseau](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Sous-réseaux** : chaque sous-réseau est associé au groupe de sécurité réseau qui lui correspond.

### <a name="data-in-transit"></a>Données en transit

Par défaut, Azure chiffre toutes les communications avec les centres de données Azure. Toutes les transactions en direction du service Stockage Azure par le biais du Portail Azure s’effectuent via HTTPS.

### <a name="data-at-rest"></a>Données au repos

L’architecture protège les données au repos à l’aide d’un chiffrement, d’un audit de base de données et d’autres mesures.

**Stockage Azure** : pour répondre aux exigences de chiffrement des données au repos, l’ensemble du service [Stockage Azure](https://azure.microsoft.com/services/storage/) utilise la fonctionnalité de chiffrement [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Celle-ci permet de protéger et de sauvegarder les données dans le respect des engagements de l’organisation en matière de sécurité et des exigences de conformité définies par NHS Digital.

**Azure Disk Encryption** : [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) exploite la fonctionnalité BitLocker de Windows pour fournir le chiffrement des volumes des disques de données. La solution s’intègre à Azure Key Vault pour faciliter le contrôle et la gestion des clés de chiffrement de disque.

**Azure SQL Database** : l’instance Azure SQL Database utilise les mesures de sécurité de base de données suivantes :

- [L’authentification et l’autorisation Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permettent la gestion des identités des utilisateurs de base de données et d’autres services Microsoft dans un emplacement central.
- L’[audit Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) suit les événements de base de données et consigne ceux-ci dans un journal d’audit conservé dans un compte de stockage Azure.
- Azure SQL Database est configuré pour utiliser la technologie [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) qui assure le chiffrement et le déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux de transactions afin de protéger les informations au repos. Transparent Data Encryption protège les données stockées des accès non autorisés.
- Des [règles de pare-feu](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) empêchent tout accès aux serveurs de base de données sans autorisation appropriée. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande.
- La [détection des menaces SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permet de détecter et traiter les menaces potentielles à mesure qu’elles surviennent en déclenchant des alertes de sécurité en cas d’activités de base de données suspectes, de vulnérabilités potentielles, d’attaques par injection de code SQL et de modèles d’accès anormaux aux bases de données.
- Des [colonnes chiffrées](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantissent que les données sensibles n’apparaissent jamais sous forme de texte en clair à l’intérieur du système de base de données. Une fois le chiffrement des données activé, seules des applications clientes ou des serveurs d’applications ayant accès aux clés peuvent accéder aux données en texte clair.
- [SQL Database Dynamic Data Masking](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limite l’exposition des données sensibles en masquant celles-ci aux utilisateurs ou aux applications qui n’ont pas de privilèges. Le masquage dynamique des données peut découvrir automatiquement les données potentiellement sensibles et suggérer les masques appropriés à appliquer. Cela permet d’identifier et de réduire l’accès aux données pour qu’elles ne quittent pas la base de données via un accès non autorisé. Les clients doivent ajuster les paramètres de masquage de données dynamiques pour respecter leur schéma de base de données.

### <a name="identity-management"></a>Gestion des identités

Les technologies suivantes offrent des fonctionnalités de gestion de l’accès aux données dans l’environnement Azure :

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) est le service mutualisé basé sur le cloud de Microsoft pour la gestion des répertoires et des identités. Tous les utilisateurs de cette solution sont créés dans Azure Active Directory, notamment ceux qui accèdent à Azure SQL Database.
- L’authentification auprès de l’application est effectuée à l’aide d’Azure Active Directory. Pour plus d’informations, consultez [Intégration d’applications à Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). En outre, le chiffrement des colonnes de base de données utilise Azure Active Directory pour authentifier l’application auprès d’Azure SQL Database. Pour plus d’informations, découvrez comment [protéger des données sensibles dans Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Le [contrôle d’accès en fonction du rôle Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permet aux administrateurs de définir des autorisations d’accès affinées pour n’accorder aux utilisateurs que l’accès dont ils ont besoin pour accomplir leur travail. Au lieu d’accorder à tous les utilisateurs des autorisations illimitées sur les ressources Azure, les administrateurs peuvent autoriser seulement certaines actions pour accéder aux données. Par exemple, l’accès aux abonnements est limité à l’administrateur d’abonnements.
- La solution [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) permet aux clients de minimiser le nombre d’utilisateurs qui ont accès à des informations spécifiques. Les administrateurs peuvent utiliser Azure Active Directory Privileged Identity Management pour découvrir, restreindre et superviser les identités privilégiées et leur accès aux ressources. Cette fonctionnalité permet également d’appliquer un accès administratif juste-à-temps à la demande si nécessaire.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) détecte les vulnérabilités risquant d’affecter les identités d’une organisation, configure des réponses automatiques aux actions suspectes détectées en lien avec les identités d’une organisation, et examine les incidents suspects afin de prendre les mesures appropriées pour résoudre ces derniers.

### <a name="security"></a>Sécurité

**Gestion des secrets** : la solution utilise [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pour gérer les clés et les secrets. Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. Les fonctionnalités suivantes d’Azure Key Vault aident les clients à protéger les données et à y accéder :

- Les stratégies d’accès avancées sont configurées en fonction des besoins.
- Les stratégies d’accès Key Vault sont définies avec des autorisations minimales requises pour les clés et les secrets.
- L’ensemble des clés et secrets figurant dans Key Vault présentent des dates d’expiration.
- Toutes les clés stockées dans Key Vault sont protégées par des modules de sécurité matériels spécialisés. Le type de clé est une clé RSA 2048 bits protégée par un module de sécurité matériel.
- Des autorisations minimales requises sont accordées à tous les utilisateurs et à toutes les identités à l’aide du contrôle d’accès en fonction rôle.
- Les journaux de diagnostics pour Key Vault sont activés avec une période de rétention d’au moins 365 jours.
- Les opérations de chiffrement autorisées pour les clés sont restreintes à celles qui sont nécessaires.

**Azure Security Center** : avec [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), les clients peuvent appliquer et gérer de façon centralisée des stratégies de sécurité entre les charges de travail, limiter l’exposition aux menaces, ainsi que détecter les attaques et y répondre. Par ailleurs, Azure Security Center accède aux configurations existantes des services Azure pour fournir des recommandations de configuration et de service dans le but d’améliorer la situation de sécurité et de protéger les données.

Azure Security Center utilise diverses fonctionnalités de détection pour avertir les clients des attaques potentielles qui ciblent leur environnement. Ces alertes fournissent de précieuses informations sur le déclencheur de l’alerte, les ressources ciblées et la source de l’attaque. Azure Security Center a un ensemble [d’alertes de sécurité prédéfinies](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), qui sont déclenchées en cas de menace ou d’activité suspecte. Les [règles d’alerte personnalisées](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) d’Azure Security Center permettent aux clients de définir de nouvelles alertes de sécurité basées sur les données déjà collectées dans leur environnement.

Azure Security Center fournit des incidents et des alertes de sécurité hiérarchisés, pour simplifier la découverte et la résolution des problèmes potentiels de sécurité. Un [rapport d’intelligence des menaces](https://docs.microsoft.com/azure/security-center/security-center-threat-report) est généré pour chaque menace détectée afin d’aider les équipes chargées de traiter les incidents à examiner et à contrecarrer les menaces.

### <a name="logging-and-auditing"></a>Journalisation et audit

Les services Azure assurent une journalisation complète de l’activité du système et des utilisateurs, ainsi que de l’intégrité du système :
- **Journaux d’activité :** les [journaux d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fournissent des informations sur les opérations effectuées sur les ressources d’un abonnement. Les journaux d’activité peuvent aider à déterminer l’initiateur, l’heure d’exécution et l’état d’une opération.
- **Journaux de diagnostic :** les [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) sont tous les journaux émis par toutes les ressources. Ils incluent les journaux des événements système de Windows, les journaux de Stockage Azure, les journaux d’audit du Key Vault, ainsi que les journaux de pare-feu et d’accès d’Application Gateway. Tous les journaux de diagnostic sont consignés dans un compte de stockage Azure centralisé et chiffré pour l’archivage. L’utilisateur peut configurer la rétention jusqu’à 730 jours pour répondre aux exigences de rétention spécifiques de l’entreprise.

**Log Analytics** : ces journaux sont consolidés dans [Log Analytics](https://azure.microsoft.com/services/log-analytics/) à des fins de traitement, de stockage et de génération de rapports basés sur des tableaux de bord. Une fois collectées, les données sont organisées dans différents tableaux en fonction du type de données. Toutes les données sont ainsi analysées ensemble, quelle que soit leur source d’origine. Par ailleurs, Azure Security Center s’intègre à Log Analytics pour permettre aux clients d’utiliser des requêtes Log Analytics afin d’accéder à leurs données d’événement de sécurité et de les combiner avec des données provenant d’autres services.

Les [solutions de gestion](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Log Analytics suivantes sont également incluses dans cette architecture :
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment) : la solution Active Directory Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit une liste hiérarchisée de suggestions propres à l’infrastructure de serveur déployée.
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment) : la solution SQL Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit aux clients une liste hiérarchisée de recommandations spécifiques pour l’infrastructure de serveur déployée.
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth) : la solution de contrôle d’intégrité des agents indique le nombre d’agents déployés et leur répartition géographique, ainsi que le nombre d’agents qui ne répondent pas et qui envoient des données opérationnelles.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity) : la solution Activity Log Analytics facilite l’analyse des journaux d’activité de tous les abonnements Azure d’un client.

**Azure Automation** : [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) stocke, exécute et gère les runbooks. Dans cette solution, les runbooks aident à collecter les journaux d’Azure SQL Database. La solution Automation [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) permet aux clients d’identifier facilement les changements dans l’environnement.

**Azure Monitor** : [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) permet aux utilisateurs de suivre les performances, d’assurer la sécurité et d’identifier les tendances en permettant aux organisations d’auditer, de créer des alertes et d’archiver des données, y compris de suivre les appels d’API dans leurs ressources Azure.

## <a name="threat-model"></a>Modèle de menace

Le diagramme de flux de données de cette architecture de référence figure ci-dessous et est également disponible en [téléchargement](https://aka.ms/uknhs-analytics-tm). Ce modèle peut aider les clients à comprendre les points de risque potentiel de l’infrastructure du système lors de l’apport de modifications.

![Modèle de menace en matière d’analytique des données pour le UK NHS](images/uknhs-analytics-threat-model.png?raw=true "Modèle de menace en matière d’analytique des données pour le UK NHS")

## <a name="compliance-documentation"></a>Documentation sur la conformité

Le [Blueprint de sécurité et de conformité – Matrice de responsabilités des clients du UK NHS](https://aka.ms/uknhs-crm) répertorie tous les principes de sécurité exigés par le UK NHS. La matrice indique si l’implémentation de chacun des principes revient à Microsoft, au client ou aux deux.

Le [Blueprint de sécurité et de conformité Azure - Matrice d’implémentation d’analytique données pour le UK NHS](https://aka.ms/uknhs-analytics-cim) fournit des informations sur les exigences du UK NHS que prend en compte l’architecture d’analytique données, y compris une description détaillée de la façon dont l’implémentation répond aux exigences de chaque principe traité.


## <a name="guidance-and-recommendations"></a>Instructions et recommandations

### <a name="vpn-and-expressroute"></a>VPN et ExpressRoute

Il convient de configurer un tunnel VPN sécurisé ou un routage [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) pour établir une connexion sécurisée aux ressources déployées dans le cadre de cette architecture de référence en matière d’analytique des données. En configurant correctement un VPN ou un routage ExpressRoute, les clients peuvent ajouter une couche de protection au niveau des données en transit.

En implémentant un tunnel VPN sécurisé avec Azure, il est possible de créer une connexion privée virtuelle entre un réseau local et un réseau virtuel Azure. Cette connexion via Internet permet aux clients de &quot;tunneliser&quot; des informations de manière sécurisée dans une liaison chiffrée entre leur réseau et Azure. La technologie de VPN de site à site, aussi sécurisée qu’aguerrie, est déployée par des entreprises de toutes tailles depuis des décennies. Le [mode tunnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) est utilisé dans cette option comme un mécanisme de chiffrement.

Étant donné que le trafic à l’intérieur du tunnel VPN transite par Internet avec un VPN de site à site, Microsoft offre une autre option de connexion encore plus sécurisée. Azure ExpressRoute est une liaison réseau étendu dédiée entre Azure et un emplacement local ou un fournisseur d’hébergement Exchange. Les connexions ExpressRoute ne transitent pas par Internet, et offrent de meilleurs niveaux de fiabilité, de rapidité, de latence et de sécurité que les connexions classiques via Internet. Par ailleurs, comme il s’agit d’une connexion directe du fournisseur de télécommunications du client, les données ne circulent pas sur Internet et n’y sont donc pas exposées.

Les meilleures pratiques concernant l’implémentation d’un réseau hybride sécurisé qui étend un réseau local à Azure sont [décrites ici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Processus d’extraction, de transformation et de chargement

[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) peut charger des données dans Azure SQL Database sans outil spécifique pour extraire, transformer, charger ou importer ces données. PolyBase permet d’accéder aux données par le biais de requêtes T-SQL. Il est possible d’utiliser PolyBase avec une pile de décisionnel et d’analyse Microsoft, ainsi qu’avec des outils tiers compatibles avec SQL Server.

### <a name="azure-active-directory-setup"></a>Installation d’Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) joue un rôle essentiel dans la gestion du déploiement et de la configuration des accès des membres du personnel qui interagissent avec l’environnement. Une instance Windows Server Active Directory existante peut être intégrée à Azure Active Directory en [quatre clics](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Les clients peuvent également associer l’infrastructure (contrôleurs de domaine) Active Directory déployée à un service Azure Active Directory existant en définissant cette infrastructure comme sous-domaine d’une forêt Azure Active Directory.

## <a name="disclaimer"></a>Clause d'exclusion de responsabilité

 - Ce document est fourni à titre d’information uniquement. MICROSOFT N’ACCORDE AUCUNE GARANTIE EXPRESSE, IMPLICITE OU STATUTAIRE EN LIEN AVEC LES INFORMATIONS CONTENUES DANS CE DOCUMENT. Ce document est fourni « en l’état ». Les informations et les points de vue exprimés dans ce document, y compris les URL et autres références à des sites web, peuvent être modifiés sans préavis. Les clients qui lisent ce document assument les risques liés à son utilisation.
 - Ce document n’accorde aux clients aucun droit légal de propriété intellectuelle sur les produits ou solutions Microsoft.
 - Les clients peuvent copier et utiliser ce document pour un usage interne, à titre de référence.
 - Certaines recommandations contenues dans ce document peuvent entraîner une augmentation des taux d’utilisation des données, des réseaux ou des ressources de calcul dans Azure, débouchant sur des coûts de licence ou d’abonnement supplémentaires.
 - Cette architecture constitue une base que les clients peuvent modifier en fonction de leurs besoins. Elle ne doit pas être utilisée telle quelle dans un environnement de production.
 - Développé en tant que référence, ce document ne doit pas être utilisé pour définir tous les moyens par lesquels un client peut satisfaire à des réglementations et exigences de conformité spécifiques. Les clients doivent rechercher une assistance juridique auprès de leur organisation pour connaître les implémentations client approuvées.
