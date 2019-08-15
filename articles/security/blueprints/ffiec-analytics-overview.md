---
title: Blueprint de sécurité et de conformité Azure - Analytique des données pour FFIEC
description: Blueprint de sécurité et de conformité Azure - Analytique des données pour FFIEC
services: security
author: meladie
ms.assetid: 31b70690-682c-4c38-9bbb-14dce162867a
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: meladie
ms.openlocfilehash: 474ab3ddd6c931b17b2ece1e22e1e1d4b62f0cdb
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946788"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-ffiec-financial-services"></a>Blueprint de sécurité et de conformité Azure : Analytique pour les services financiers FFIEC

## <a name="overview"></a>Vue d'ensemble

Ce blueprint de sécurité et de conformité Azure fournit des conseils pour déployer une architecture d’analytique des données dans Azure adapté à la collecte, le stockage et la récupération des données financières régies par le FFIEC (Federal Financial Institution Examination Council).

L’architecture de référence, le guide d’implémentation et le modèle de risque constituent une base pour se conformer aux exigences du FFIEC. Cette solution fournit une base de référence pour aider les clients à déployer des charges de travail sur Azure conformément au FFIEC. Toutefois, elle ne doit pas être utilisée en l’état dans un environnement de production, pour lequel une configuration supplémentaire est nécessaire.

La mise en conformité par rapport aux exigences du FFIEC nécessite que des auditeurs qualifiés certifient une solution cliente de production. Les audits sont supervisés par des examinateurs issus d’agences membre du FFIEC, notamment le Conseil des gouverneurs de la Réserve fédérale des États-Unis (FRB), la FDIC (Federal Deposit Insurance Corporation), la NCUA (National Credit Union Administration), l’OCC (Office of the Comptroller of the Currency) et le Bureau américain de protection des consommateurs (CFPB). Ces examinateurs certifient que les audits sont effectués par des évaluateurs indépendants de l’institution auditée. Il incombe aux clients d’évaluer de façon appropriée la sécurité et la conformité de toute solution basée sur cette architecture, car les exigences peuvent varier en fonction des spécificités de chaque implémentation.

## <a name="architecture-diagram-and-components"></a>Diagramme et composants de l’architecture

Ce blueprint de sécurité et de conformité Azure offre une plateforme analytique à partir de laquelle les clients peuvent créer leurs propres outils d’analyse. L’architecture de référence présente un cas d’usage générique dans le cadre duquel les clients entrent leurs données soit par le biais d’importations de données en bloc effectuées par l’administrateur SQL/de données, soit par l’intermédiaire de mises à jour de données opérationnelles mises en œuvre par un utilisateur du secteur opérationnel. Ces deux axes de travail incorporent Azure Functions pour l’importation des données dans Azure SQL Database. Chaque client doit configurer Azure Functions par le biais du Portail Azure pour la gestion des tâches d’importation conformément aux exigences d’analyse qui lui sont propres.

Azure offre divers services de création de rapports et d’analyse pour le client. Cette solution incorpore les services Azure Machine Learning parallèlement à Azure SQL Database pour parcourir rapidement les données et accélérer la fourniture de résultats par le biais d’une modélisation plus intelligente. Azure Machine Learning accélère la vitesse des requêtes en découvrant de nouvelles relations entre les jeux de données. Une fois cet apprentissage des données effectué par le biais de diverses fonctions statistiques, vous pouvez synchroniser jusqu’à 7 pools de requêtes supplémentaires (8 au total en comptant le serveur du client) avec les mêmes modèles tabulaires pour répartir la charge de travail des requêtes et réduire les temps de réponse.

Les bases de données Azure SQL peuvent être configurées avec des index columnstore afin d’améliorer l’analytique et la création de rapports. Azure Machine Learning et les bases de données SQL peuvent faire l’objet d’un scale-up ou d’un scale-down ou être complètement arrêtés en fonction de l’utilisation du client. La totalité du trafic SQL est chiffrée avec SSL grâce à l’inclusion de certificats auto-signés. En guise de meilleure pratique, Azure préconise l’utilisation d’une autorité de certification de confiance afin de renforcer la sécurité.

Une fois les données chargées dans Azure SQL Database et assimilées par Azure Machine Learning, elles sont synthétisées par l’utilisateur du secteur opérationnel et par l’administrateur SQL/de données à l’aide de Power BI. Power BI assure un affichage intuitif des données et fusionne les informations de plusieurs jeux de données afin d’offrir un meilleur aperçu de l’activité. Grâce à son haut niveau d’adaptabilité et à sa simplicité d’intégration à Azure SQL Database, Power BI est configurable pour la prise en charge d’un large éventail de scénarios basés sur les besoins métiers des clients.

La solution utilise des comptes de stockage Azure que les clients peuvent configurer pour utiliser Storage Service Encryption afin de maintenir la confidentialité des données au repos. Azure stocke trois copies des données dans un centre de données sélectionné par le client pour assurer la résilience. Le stockage géographiquement redondant garantit que les données sont répliquées dans un centre de données secondaire situé à des centaines de kilomètres et stockées en trois copies dans ce centre de données, ce qui empêche une perte de données en cas de dysfonctionnement au niveau du centre de données principal du client.

Pour renforcer la sécurité, toutes les ressources dans cette solution sont gérées sous forme de groupe de ressources dans Azure Resource Manager. Le contrôle d’accès en fonction du rôle Azure Active Directory est utilisé pour contrôler l’accès aux ressources déployées ainsi qu’à leurs clés dans Azure Key Vault. L’intégrité du système est surveillée dans Azure Security Center et Azure Monitor. Les clients configurent ces deux services de supervision pour la capture de journaux d’activité et la centralisation des informations concernant l’intégrité du système dans un tableau de bord facilement consultable.

Azure SQL Database est généralement géré par le biais de la suite d’outils SQL Server Management Studio (SSMS), qui s’exécute à partir d’une machine locale configurée pour accéder à Azure SQL Database par l’intermédiaire d’une connexion VPN ou ExpressRoute sécurisée. **Microsoft recommande de configurer une connexion VPN ou ExpressRoute pour la gestion et l’importation de données dans le groupe de ressources de l’architecture de référence**.

![Diagramme de l’architecture de référence d’analytique pour le FFIEC](images/ffiec-analytics-architecture.png "Diagramme de l’architecture de référence d’analytique pour le FFIEC")

Cette solution utilise les services Azure suivants. Les informations détaillées concernant l’architecture de déploiement figurent à la section [Architecture de déploiement](#deployment-architecture).

- Application Insights
- Azure Active Directory
- Azure Data Catalog
- Azure Disk Encryption
- Azure Event Grid
- Azure Functions
- Azure Key Vault
- Azure Machine Learning
- Azure Monitor (journaux)
- Azure Security Center
- Azure SQL Database
- Stockage Azure
- Réseau virtuel Azure
    - (1) réseau /16
    - (2) /24 réseaux
    - (2) Groupes de sécurité réseau
- tableau de bord Power BI

## <a name="deployment-architecture"></a>Architecture de déploiement

La section ci-après décrit en détail les éléments nécessaires au déploiement et à l’implémentation.

**Azure Event Grid** : [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) permet aux clients de créer facilement des applications avec les architectures basées sur des événements. Les utilisateurs sélectionnent la ressource Azure à laquelle ils veulent s’abonner et donnent au gestionnaire d’événements ou au webhook un point de terminaison auquel envoyer l’événement. Les clients peuvent sécuriser les points de terminaison Webhook en ajoutant des paramètres de requête à l’URL du Webhook lorsqu’ils créent un abonnement à un événement. Azure Event Grid ne prend en charge que les points de terminaison Webhook HTTPS. Azure Event Grid permet aux clients de contrôler le niveau d’accès offert aux différents utilisateurs pour l’exécution de diverses opérations de gestion, telles que l’énumération et la création des abonnements aux événements ou la génération de clés. Event Grid utilise le contrôle d’accès en fonction du rôle Azure.

**Azure Functions** : [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) est un service de calcul serverless qui permet aux utilisateurs d’exécuter du code à la demande sans avoir à provisionner ou gérer explicitement l’infrastructure. Utilisez Azure Functions pour exécuter un script ou du code en réponse à un grand nombre d’événements.

**Azure Machine Learning service** : [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/) est une technique de science des données qui permet aux ordinateurs d’utiliser des données existantes afin de prévoir les tendances, les résultats et les comportements futurs.

**Azure Data Catalog** : [Data Catalog](../../data-catalog/overview.md) rend les sources de données facilement détectables et compréhensibles par les utilisateurs qui gèrent les données. Les sources de données courantes peuvent être inscrites, étiquetées et explorées à la recherche de données financières. Les données restent à leur emplacement existant, mais une copie de leurs métadonnées, ainsi qu’une référence à l’emplacement de la source de données, sont ajoutées à Data Catalog. Les métadonnées sont également indexées de manière à ce que chaque source de données soit facilement détectable via la recherche, et compréhensible pour les utilisateurs qui la découvrent.

### <a name="virtual-network"></a>Réseau virtuel

L’architecture définit un réseau privé virtuel avec l’espace d’adressage 10.200.0.0/16.

**Groupes de sécurité réseau** : les [groupes de sécurité réseau](../../virtual-network/virtual-network-vnet-plan-design-arm.md) contiennent des listes de contrôle d’accès qui autorisent ou interdisent le trafic au sein d’un réseau virtuel. Les groupes de sécurité réseau peuvent être utilisés pour sécuriser le trafic au niveau d’un sous-réseau ou d’une machine virtuelle individuelle. Les groupes de sécurité réseau suivants existent :

  - Un groupe de sécurité réseau pour Active Directory
  - Un groupe de sécurité réseau pour la charge de travail

Chaque groupe de sécurité réseau a des ports et protocoles spécifiques ouverts pour que la solution fonctionne correctement et de manière sécurisée. Par ailleurs, les configurations suivantes sont activées pour chaque groupe de sécurité réseau :

- Les [événements et journaux de diagnostic](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) sont activés et stockés dans un compte de stockage.
- La solution Journaux Azure Monitor est connectée aux [journaux de diagnostic du groupe de sécurité réseau](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Sous-réseaux** : chaque sous-réseau est associé au groupe de sécurité réseau qui lui correspond.

### <a name="data-in-transit"></a>Données en transit

Par défaut, Azure chiffre toutes les communications avec les centres de données Azure. Toutes les transactions en direction du Stockage Azure passant par le portail Azure se produisent via HTTPS.

### <a name="data-at-rest"></a>Données au repos

L’architecture protège les données au repos à l’aide d’un chiffrement, d’un audit de base de données et d’autres mesures.

**Stockage Azure** : pour satisfaire aux exigences du chiffrement des données au repos, l’ensemble du service [Stockage Azure](https://azure.microsoft.com/services/storage/) utilise la fonctionnalité [Storage Service Encryption](../../storage/common/storage-service-encryption.md). Celle-ci permet de protéger et de sauvegarder les données dans le respect des engagements de l’organisation en matière de sécurité et des exigences de conformité définies par le FFIEC.

**Azure Disk Encryption** : [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) exploite la fonctionnalité BitLocker de Windows pour chiffrer les volumes des disques de données. La solution s’intègre à Azure Key Vault pour faciliter le contrôle et la gestion des clés de chiffrement de disque.

**Azure SQL Database** : L’instance Azure SQL Database utilise les mesures suivantes pour la sécurité des bases de données :

- La solution [d’authentification et d’autorisation Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permet de gérer les identités des utilisateurs de bases de données et d’autres services Microsoft dans un emplacement central.
- L’[audit Azure SQL Database](../../sql-database/sql-database-auditing.md) suit les événements de base de données et consigne ceux-ci dans un journal d’audit conservé dans un compte de stockage Azure.
- Azure SQL Database est configuré pour utiliser la technologie [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) qui assure le chiffrement et le déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux de transactions afin de protéger les informations au repos. Transparent Data Encryption protège les données des accès non autorisés.
- Des [règles de pare-feu](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) empêchent tout accès aux serveurs de base de données sans autorisation appropriée. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande.
- La [détection des menaces SQL](../../sql-database/sql-database-threat-detection.md) permet de détecter et traiter les menaces potentielles à mesure qu’elles surviennent en déclenchant des alertes de sécurité en cas d’activités de base de données suspectes, de vulnérabilités potentielles, d’attaques par injection de code SQL et de modèles d’accès anormaux aux bases de données.
- Des [colonnes Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantissent que les données sensibles n’apparaissent jamais en tant que texte en clair à l’intérieur du système de base de données. Une fois le chiffrement des données activé, seules des applications clientes ou des serveurs d’applications ayant accès aux clés peuvent accéder aux données en texte clair.
- Les [propriétés étendues](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) permettent aux utilisateurs d’interrompre le traitement d’objets de données en ajoutant des propriétés personnalisées à ces objets de base de données et en étiquetant les données comme « Abandonnées » pour aider la logique d’application à empêcher le traitement des données financières associées.
- La [Sécurité au niveau des lignes](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permet aux utilisateurs de définir des stratégies de restriction d’accès aux données afin d’interrompre le traitement de celles-ci.
- [SQL Database Dynamic Data Masking](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limite l’exposition des données sensibles en masquant celles-ci aux utilisateurs ou aux applications qui n’ont pas de privilège. Le masquage dynamique des données peut découvrir automatiquement les données potentiellement sensibles et suggérer les masques appropriés à appliquer. Cela permet d’identifier et de réduire l’accès aux données afin que celles-ci ne quittent pas la base de données via un accès non autorisé. Les clients doivent ajuster les paramètres de masquage dynamique des données pour respecter leur schéma de base de données.

### <a name="identity-management"></a>Gestion des identités

Les technologies suivantes offrent des fonctionnalités de gestion de l’accès aux données dans l’environnement Azure :

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) est le répertoire cloud multi-locataire et le service de gestion des identités de Microsoft. Tous les utilisateurs de cette solution sont créés dans Azure Active Directory, notamment ceux qui accèdent à Azure SQL Database.
- L’authentification auprès de l’application est effectuée à l’aide d’Azure Active Directory. Pour plus d’informations, consultez [Intégration d’applications à Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). En outre, le chiffrement des colonnes de base de données utilise Azure Active Directory pour authentifier l’application auprès d’Azure SQL Database. Pour plus d’informations, découvrez comment [protéger des données sensibles dans Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- [Le contrôle d’accès en fonction du rôle Azure](../../role-based-access-control/role-assignments-portal.md) permet aux administrateurs de définir des autorisations affinées pour accorder uniquement le nombre d’accès dont les utilisateurs ont besoin pour leur travail. Au lieu d’accorder à tous les utilisateurs des autorisations illimitées sur les ressources Azure, les administrateurs peuvent autoriser seulement certaines actions pour accéder aux données. Par exemple, l’accès aux abonnements est limité à l’administrateur d’abonnements.
- La solution [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) permet aux clients de minimiser le nombre d’utilisateurs qui ont accès à des informations spécifiques. Les administrateurs peuvent utiliser Azure Active Directory Privileged Identity Management pour découvrir, restreindre et superviser les identités privilégiées et leur accès aux ressources. Cette fonctionnalité permet également d’appliquer un accès administratif juste-à-temps à la demande si nécessaire.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) détecte les vulnérabilités risquant d’affecter les identités d’une organisation, configure des réponses automatiques aux actions suspectes détectées en lien avec les identités d’une organisation, et examine les incidents suspects afin de prendre les mesures appropriées pour résoudre ces derniers.

### <a name="security"></a>Sécurité

**Gestion des secrets** : la solution utilise [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pour gérer les clés et les secrets. Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. Les fonctionnalités Azure Key Vault ci-après aident les clients à protéger les données et à y accéder :

- Les stratégies d’accès avancées sont configurées en fonction des besoins.
- Les stratégies d’accès Key Vault sont définies avec des autorisations minimales requises pour les clés et les secrets.
- L’ensemble des clés et secrets figurant dans Key Vault présentent des dates d’expiration.
- Toutes les clés dans Key Vault sont protégées par des modules de sécurité matériels spécialisés. La clé est de type RSA 2 048 bits protégé par HSM.
- Des autorisations minimales requises sont accordées à tous les utilisateurs et à toutes les identités à l’aide du contrôle d’accès en fonction rôle.
- Les journaux de diagnostics pour Key Vault sont activés avec une période de rétention d’au moins 365 jours.
- Les opérations de chiffrement autorisées pour les clés sont restreintes à celles qui sont nécessaires.

**Azure Security Center** : avec [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), les clients peuvent appliquer et gérer de façon centralisée des stratégies de sécurité sur les charges de travail, limiter l’exposition aux menaces ainsi que détecter les attaques et y répondre. Par ailleurs, Azure Security Center accède aux configurations existantes des services Azure pour fournir des recommandations en matière de configuration et de service. Cela contribue à l’amélioration du dispositif de sécurité et à la protection des données.

Azure Security Center utilise diverses fonctionnalités de détection pour avertir les clients des attaques potentielles qui ciblent leur environnement. Ces alertes fournissent de précieuses informations sur le déclencheur de l’alerte, les ressources ciblées et la source de l’attaque. Azure Security Center a un ensemble [d’alertes de sécurité prédéfinies](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), qui sont déclenchées en cas de menace ou d’activité suspecte. Les [règles d’alerte personnalisées](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) d’Azure Security Center permettent aux clients de définir de nouvelles alertes de sécurité basées sur les données déjà collectées dans leur environnement.

Azure Security Center fournit des incidents et des alertes de sécurité hiérarchisés, ce qui permet aux clients de découvrir et de résoudre plus facilement les problèmes de sécurité potentiels. Un [rapport de Threat intelligence](https://docs.microsoft.com/azure/security-center/security-center-threat-report) est généré pour chaque menace détectée afin d’aider les équipes chargées de répondre aux incidents à examiner et à contrecarrer les menaces.

### <a name="logging-and-auditing"></a>Journalisation et audit

Les services Azure assurent une journalisation complète de l’activité du système et des utilisateurs, ainsi que de l’intégrité du système :
- **Journaux d’activité** : les [journaux d’activité](../../azure-monitor/platform/activity-logs-overview.md) fournissent des insights sur les opérations ayant été effectuées sur les ressources d’un abonnement. Les journaux d’activité peuvent aider à déterminer l’initiateur, l’heure d’exécution et l’état d’une opération.
- **Journaux de diagnostic** : les [journaux de diagnostic](../../azure-monitor/platform/diagnostic-logs-overview.md) incluent l’ensemble des journaux d’activité générés par chaque ressource. Ils incluent les journaux des événements système de Windows, les journaux d’activité de Stockage Azure, les journaux d’audit du Key Vault, ainsi que les journaux d’activité de pare-feu et d’accès d’Application Gateway. Tous les journaux de diagnostic sont consignés dans un compte de stockage Azure centralisé et chiffré pour l’archivage. L’utilisateur peut configurer la rétention jusqu’à 730 jours pour répondre aux exigences de rétention spécifiques de l’entreprise.

**Journaux d’activité Azure Monitor** : ces journaux sont regroupés dans [Journaux Azure Monitor](https://azure.microsoft.com/services/log-analytics/) à des fins de traitement, de stockage et de génération de rapports de tableau de bord. Une fois collectées, les données sont organisées dans différentes tables en fonction du type de données dans des espaces de travail Log Analytics. Toutes les données sont ainsi analysées ensemble, quelle que soit leur source d’origine. Par ailleurs, Azure Security Center s’intègre à Journaux Azure Monitor pour permettre aux clients d’utiliser des requêtes Kusto ; ils peuvent alors accéder à leurs données d’événement de sécurité et les combiner avec des données provenant d’autres services.

Cette architecture intègre également les [solutions de supervision](../../monitoring/monitoring-solutions.md) suivantes :
-   [Active Directory Assessment](../../azure-monitor/insights/ad-assessment.md) : la solution Active Directory Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit une liste hiérarchisée de suggestions spécifiques pour l’infrastructure de serveur déployée.
- [SQL Assessment](../../azure-monitor/insights/sql-assessment.md) : la solution SQL Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit aux clients une liste hiérarchisée de suggestions spécifiques pour l’infrastructure de serveur déployée.
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md) : cette solution de contrôle d’intégrité des agents indique le nombre d’agents déployés et leur répartition géographique, ainsi que le nombre d’agents qui ne répondent pas ou qui envoient des données opérationnelles.
-   [Activity Log Analytics](../../azure-monitor/platform/collect-activity-logs.md) : la solution Activity Log Analytics facilite l’analyse des journaux d’activité Azure de tous les abonnements Azure d’un client.

**Azure Automation** : [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) stocke, exécute et gère les runbooks. Dans cette solution, les runbooks aident à collecter les journaux d’activité d’Azure SQL Database. La solution Automation [Change Tracking](../../automation/change-tracking.md) permet aux clients d’identifier facilement les changements dans l’environnement.

**Azure Monitor** : [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) aide les utilisateurs à suivre les performances, garantir la sécurité et identifier les tendances en permettant aux organisations d’auditer, de créer des alertes et d’archiver des données, y compris le suivi des appels d’API dans leurs ressources Azure.

**Application Insights** : [Application Insights](https://docs.microsoft.com/azure/application-insights/) est un service extensible de gestion des performances des applications (APM) destiné aux développeurs web sur de multiples plateformes. Il détecte les anomalies de performances et intègre de puissants outils d’analyse conçus pour aider à diagnostiquer les problèmes et à comprendre l’usage que les utilisateurs font d’une application. Ce service a été conçu pour permettre aux utilisateurs d’améliorer continuellement le niveau de performance et la convivialité.

## <a name="threat-model"></a>Modèle de menace

Le diagramme de flux de données de cette architecture de référence figure ci-dessous et est également disponible en [téléchargement](https://aka.ms/ffiec-analytics-tm). Ce modèle peut aider les clients à comprendre les points de risque potentiel de l’infrastructure du système lors de l’apport de modifications.

![Modèle de risque d’analytique pour le FFIEC](images/ffiec-analytics-threat-model.png "Modèle de risque d’analytique pour le FFIEC")

## <a name="compliance-documentation"></a>Documentation sur la conformité

Le [Blueprint de sécurité et de conformité – Matrice de responsabilités des clients du FFIEC](https://aka.ms/ffiec-crm) répertorie tous les objectifs exigés par le FFIEC. La matrice indique si l’implémentation de chaque objectif revient à Microsoft, au client ou aux deux.

Le [Blueprint de sécurité et de conformité Azure - Matrice d’implémentation d’analytique des données pour le FFIEC](https://aka.ms/ffiec-analytics-cim) fournit des informations sur les objectifs du FFIEC que prend en compte l’architecture d’analytique données, y compris une description détaillée de la façon dont l’implémentation répond aux exigences de chaque objectif traité.


## <a name="guidance-and-recommendations"></a>Instructions et recommandations

### <a name="vpn-and-expressroute"></a>VPN et ExpressRoute

Il convient de configurer un tunnel VPN sécurisé ou un routage [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) pour établir une connexion sécurisée aux ressources déployées dans le cadre de cette architecture de référence en matière d’analytique des données. En configurant correctement un VPN ou un routage ExpressRoute, les clients peuvent ajouter une couche de protection au niveau des données en transit.

En implémentant un tunnel VPN sécurisé avec Azure, il est possible de créer une connexion privée virtuelle entre un réseau local et un réseau virtuel Azure. Cette connexion via Internet permet aux clients de &quot;tunneliser&quot; des informations de manière sécurisée dans une liaison chiffrée entre leur réseau et Azure. La technologie de VPN de site à site, aussi sécurisée qu’aguerrie, est déployée par des entreprises de toutes tailles depuis des décennies. Le [mode tunnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) est utilisé dans cette option comme un mécanisme de chiffrement.

Étant donné que le trafic à l’intérieur du tunnel VPN transite par Internet avec un VPN de site à site, Microsoft offre une autre option de connexion encore plus sécurisée. Azure ExpressRoute est une liaison réseau étendu dédiée entre Azure et un emplacement local ou un fournisseur d’hébergement Exchange. Les connexions ExpressRoute ne transitent pas par Internet, et offrent de meilleurs niveaux de fiabilité, de rapidité, de latence et de sécurité que les connexions classiques via Internet. Par ailleurs, comme il s’agit d’une connexion directe du fournisseur de télécommunications du client, les données ne circulent pas sur Internet et n’y sont pas exposées.

Les meilleures pratiques concernant l’implémentation d’un réseau hybride sécurisé qui étend un réseau local à Azure sont [décrites ici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Processus d’extraction, de transformation et de chargement

[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) peut charger des données dans Azure SQL Database sans outil spécifique pour extraire, transformer, charger ou importer ces données. PolyBase permet d’accéder aux données par le biais de requêtes T-SQL. Il est possible d’utiliser PolyBase avec une pile de décisionnel et d’analyse Microsoft, ainsi qu’avec des outils tiers compatibles avec SQL Server.

### <a name="azure-active-directory-setup"></a>Installation d’Azure Active Directory
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) joue un rôle essentiel dans la gestion du déploiement et de la configuration des accès des membres du personnel qui interagissent avec l’environnement. Une instance Windows Server Active Directory existante peut être intégrée à Azure Active Directory en [quatre clics](../../active-directory/hybrid/how-to-connect-install-express.md). Les clients peuvent également associer l’infrastructure (contrôleurs de domaine) Active Directory déployée à un service Azure Active Directory existant en définissant cette infrastructure comme sous-domaine d’une forêt Azure Active Directory.

## <a name="disclaimer"></a>Clause d'exclusion de responsabilité

 - Ce document est fourni à titre d’information uniquement. MICROSOFT N’ACCORDE AUCUNE GARANTIE EXPRESSE, IMPLICITE OU STATUTAIRE EN LIEN AVEC LES INFORMATIONS CONTENUES DANS CE DOCUMENT. Ce document est fourni « en l’état ». Les informations et les points de vue exprimés dans ce document, y compris les URL et autres références à des sites web, peuvent être modifiés sans préavis. Les clients qui lisent ce document assument les risques liés à son utilisation.
 - Ce document n’accorde aux clients aucun droit légal de propriété intellectuelle sur les produits ou solutions Microsoft.
 - Les clients peuvent copier et utiliser ce document pour un usage interne, à titre de référence.
 - Certaines recommandations contenues dans ce document peuvent entraîner une augmentation des taux d’utilisation des données, des réseaux ou des ressources de calcul dans Azure, débouchant sur des coûts de licence ou d’abonnement supplémentaires.
 - Cette architecture constitue une base que les clients peuvent modifier en fonction de leurs besoins. Elle ne doit pas être utilisée telle quelle dans un environnement de production.
 - Développé en tant que référence, ce document ne doit pas être utilisé pour définir tous les moyens par lesquels un client peut satisfaire à des réglementations et exigences de conformité spécifiques. Les clients doivent rechercher une assistance juridique auprès de leur organisation pour connaître les implémentations client approuvées.
