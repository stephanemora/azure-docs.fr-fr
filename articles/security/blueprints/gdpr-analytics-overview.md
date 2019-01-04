---
title: Azure Blueprint Sécurité et conformité - Analytique pour le RGPD
description: Azure Blueprint Sécurité et conformité - Analytique pour le RGPD
services: security
author: jomolesk
ms.assetid: fe97b5e5-72d8-4930-bbe9-ead2b6ef3542
ms.service: security
ms.topic: article
ms.date: 05/14/2018
ms.author: jomolesk
ms.openlocfilehash: 8e4245de5d6a025fa87c8644678896596b07c49e
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001135"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-gdpr"></a>Blueprint de sécurité et de conformité Azure : Analytique pour le Règlement général sur la protection des données

## <a name="overview"></a>Vue d’ensemble
Le Règlement général sur la protection des données (RGPD) contient de nombreuses prescriptions concernant la collecte, le stockage et l’utilisation des informations personnelles, notamment la façon dont les organisations identifient et sécurisent les données personnelles, respectent les exigences de transparence, détectent et signalent les violations de données personnelles, et forment leur personnel, en particulier celui chargé de veiller sur la confidentialité des données. Le RGPD offre aux individus un plus grand contrôle sur leurs données personnelles, et impose de nombreuses obligations nouvelles aux organisations qui collectent, traitent ou analysent ces données. Le RGPD impose de nouvelles règles aux organisations qui proposent des biens et des services aux citoyens de l’Union européenne (UE), ou qui recueillent et analysent des données les concernant. Le RGPD s’applique, quel que soit l’endroit où se trouvent les organisations.

Microsoft a conçu Azure en y intégrant des mesures de sécurité et des politiques de confidentialité de pointe pour protéger les données dans le cloud, notamment les catégories de données personnelles visées par le RGPD. Les [clauses contractuelles](https://aka.ms/Online-Services-Terms) de Microsoft contraignent l’entreprise à respecter certaines exigences applicables aux responsables du traitement.

Ce document Azure Blueprint Sécurité et conformité fournit des conseils en matière de déploiement d’une architecture d’analytique des données dans Azure qui contribuent au respect des exigences du RGPD. Cette solution indique aux clients comment respecter les différentes exigences de sécurité et de conformité, et leur sert de base pour créer et configurer leurs propres solutions d’analytique des données dans Azure. Les clients peuvent utiliser cette architecture de référence et suivre le [processus en quatre étapes](https://aka.ms/gdprebook) préconisé par Microsoft pour leur parcours vers la mise en conformité avec le RGPD :
1. Découvrir : identifier les données personnelles existantes ainsi que l’emplacement où elles résident.
2. Gérer : régir la manière dont les données personnelles peuvent être consultées et utilisées.
3. Protéger : établir des contrôles de sécurité pour prévenir, détecter et traiter les failles de sécurité et les violations de données.
4. Signaler : conserver la documentation requise et gérer les demandes de données ainsi que les notifications de violations.

Cette architecture de référence, ainsi que le guide de mise en œuvre et le modèle de menace associés, sont destinés à servir de base aux clients qui doivent les adapter à leurs besoins spécifiques et non les utiliser tels quels en environnement de production. Notez les points suivants :
- L’architecture constitue un point de départ pour aider les clients à déployer des charges de travail sur Azure d’une manière conforme au RGPD.
- Il incombe aux clients de d’évaluer de façon appropriée la sécurité et la conformité de toute solution basée sur cette architecture, car les exigences peuvent varier en fonction des spécificités de son implémentation.

## <a name="architecture-diagram-and-components"></a>Diagramme et composants de l’architecture
Cette solution offre une plateforme analytique sur la base de laquelle les clients peuvent créer leurs propres outils d’analyse. L’architecture de référence présente un cas d’usage générique dans le cadre duquel les clients entrent leurs données soit par le biais d’importations de données en bloc effectuées par l’administrateur SQL/de données, soit par l’intermédiaire de mises à jour de données opérationnelles mises en œuvre par un utilisateur du secteur opérationnel. Ces deux axes de travail incorporent la solution Azure Functions pour l’importation des données dans Azure SQL Database. Chaque client doit configurer Azure Functions par le biais du Portail Azure pour la gestion des tâches d’importation conformément aux exigences d’analyse qui lui sont propres.

Azure offre aux clients une multitude de services de génération de rapports et d’analyse ; toutefois, cette solution incorpore les services Azure Machine Learning parallèlement à Azure SQL Database pour parcourir rapidement les données et accélérer la fourniture de résultats par le biais d’une modélisation plus efficace des données client. Azure Machine Learning est une forme d’apprentissage automatique destinée à accroître la vitesse des requêtes en découvrant de nouvelles relations entre les jeux de données. Une fois cet apprentissage effectué par le biais de diverses fonctions statistiques, il est possible de synchroniser jusqu’à 7 pools de requêtes supplémentaires (8 au total en comptant le serveur du client) avec les mêmes modèles tabulaires pour répartir la charge de travail des requêtes et réduire les temps de réponse.

Les bases de données SQL Azure peuvent être configurées avec des index columnstore afin d’améliorer les fonctions d’analyse et de génération de rapports. Azure Machine Learning et les bases de données SQL peuvent faire l’objet d’une montée ou descente en puissance ou être complètement arrêtés en fonction de l’utilisation du client. La totalité du trafic SQL est chiffrée avec SSL grâce à l’inclusion de certificats auto-signés. En guise de meilleure pratique, Azure préconise l’utilisation d’une autorité de certification de confiance afin de renforcer la sécurité.

Une fois les données chargées dans Azure SQL Database et assimilées par Azure Machine Learning, elles sont synthétisées par l’utilisateur du secteur opérationnel et par l’administrateur SQL/de données à l’aide de Power BI. Power BI assure un affichage intuitif des données et fusionne les informations de plusieurs jeux de données afin d’offrir un meilleur aperçu de l’activité. Grâce à son haut niveau d’adaptabilité et à sa simplicité d’intégration à Azure SQL Database, Power BI est configurable pour la prise en charge d’un large éventail de scénarios basés sur les besoins métiers des clients.

L’ensemble de la solution repose sur le service Stockage Azure que les clients configurent à partir du Portail Azure. Le service Stockage Azure chiffre toutes les données à l’aide de la fonctionnalité Storage Service Encryption pour garantir la confidentialité des données au repos. Le stockage géoredondant (GRS) garantit qu’un événement indésirable survenu dans le centre de données principal du client n’occasionnera aucune perte de données, grâce au stockage d’une seconde copie des données à un autre emplacement distant de plusieurs centaines de kilomètres.

Pour renforcer la sécurité, cette architecture gère les ressources à l’aide d’Azure Active Directory et d’Azure Key Vault. L’intégrité du système est supervisée par le biais de Log Analytics et d’Azure Monitor. Les clients configurent ces deux services de surveillance pour la capture de journaux et la centralisation des informations concernant l’intégrité du système dans un tableau de bord facilement consultable.

Le service Azure SQL Database est généralement géré par le biais de la suite d’outils SQL Server Management Studio (SSMS), qui s’exécute à partir d’une machine locale configurée pour accéder à Azure SQL Database par l’intermédiaire d’une connexion VPN ou ExpressRoute sécurisée. **Azure recommande de configurer une connexion VPN ou ExpressRoute pour la gestion et l’importation de données dans le groupe de ressources de l’architecture de référence.**

![Diagramme de l’architecture de référence en matière d’analytique des données pour le RGPD](images/gdpr-analytics-architecture.png?raw=true "Diagramme de l’architecture de référence en matière d’analytique des données pour le RGPD")

Cette solution utilise les services Azure suivants. Les informations détaillées concernant l’architecture de déploiement figurent à la section [Architecture de déploiement](#deployment-architecture).

- Azure Functions
- Azure SQL Database
- Azure Machine Learning
- Azure Active Directory
- Azure Key Vault
- Log Analytics
- Azure Monitor
- Stockage Azure
- tableau de bord Power BI
- Azure Data Catalog
- Azure Security Center
- Application Insights
- Azure Event Grid
- Groupes de sécurité réseau

## <a name="deployment-architecture"></a>Architecture de déploiement
La section ci-après décrit en détail les éléments nécessaires au déploiement et à l’implémentation.

**Azure Event Grid** : 
[Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) permet aux clients de créer facilement des applications avec les architectures basées sur des événements. Les utilisateurs sélectionnent la ressource Azure à laquelle ils souhaitent s’abonner et donnent au gestionnaire d’événements ou au Webhook un point de terminaison auquel envoyer l’événement. Les clients peuvent sécuriser les points de terminaison Webhook en ajoutant des paramètres de requête à l’URL du Webhook lorsqu’ils créent un abonnement à un événement. Azure Event Grid ne prend en charge que les points de terminaison Webhook HTTPS. Azure Event Grid permet aux clients de contrôler le niveau d’accès offert aux différents utilisateurs pour l’exécution de diverses opérations de gestion, telles que l’énumération et la création des abonnements aux événements ou la génération de clés. Event Grid utilise le contrôle d’accès en fonction du rôle (RBAC) Azure.

**Azure Functions** : 
[Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) est un service de calcul sans serveur qui permet aux utilisateurs d’exécuter du code à la demande sans approvisionner ou gérer explicitement l’infrastructure. Utilisez Azure Functions pour exécuter un script ou du code en réponse à un grand nombre d’événements.

**Azure Machine Learning service**
[Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/) est une technique de science des données qui permet aux ordinateurs d’utiliser des données existantes pour prévoir les tendances, les résultats et les comportements futurs.

**Azure Data Catalog** : [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) rend les sources de données facilement détectables et compréhensibles par les utilisateurs qui gèrent les données. Les sources de données courantes peuvent être enregistrées, balisées et soumises à des recherches de données personnelles. Les données restent à leur emplacement existant, mais une copie de leurs métadonnées, ainsi qu’une référence à l’emplacement de la source de données, sont ajoutées à Data Catalog. Les métadonnées sont également indexées de manière à ce que chaque source de données soit facilement détectable via la recherche, et compréhensible pour les utilisateurs qui la découvrent.

### <a name="virtual-network"></a>Réseau virtuel
Cette architecture de référence définit un réseau privé virtuel avec un espace d’adressage de 10.0.0.0/16.

**Groupes de sécurité réseau** : les [groupes de sécurité réseau (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contiennent des listes de contrôle d’accès (ACL) qui autorisent ou refusent le trafic au sein d’un réseau virtuel. Les groupes de sécurité réseau peuvent être utilisés pour sécuriser le trafic au niveau d’un sous-réseau ou d’une machine virtuelle. Les NSG disponibles sont les suivants :
  - un NSG pour Active Directory ;
  - un NSG pour la charge de travail.

Chaque groupe de sécurité réseau a des ports et protocoles spécifiques ouverts afin que la solution puisse fonctionner correctement et en toute sécurité. En outre, les configurations suivantes sont activées pour chaque groupe de sécurité réseau :
  - Les [événements et journaux de diagnostic](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) sont activés et stockés dans un compte de stockage.
  - La solution Log Analytics est connectée aux [diagnostics du groupe de sécurité réseau](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Sous-réseaux** : Chaque sous-réseau est associé à son groupe de sécurité réseau correspondant.

### <a name="data-in-transit"></a>Données en transit
Par défaut, Azure chiffre toutes les communications avec les centres de données Azure. Toutes les transactions en direction du Stockage Azure passant par le portail Azure se produisent via HTTPS.

### <a name="data-at-rest"></a>Données au repos

L’architecture protège les données au repos à l’aide d’un chiffrement, d’un audit de base de données et d’autres mesures.

**Stockage Azure** : pour répondre aux exigences de chiffrement des données au repos, l’ensemble du service [Stockage Azure](https://azure.microsoft.com/services/storage/) utilise la fonctionnalité de chiffrement [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Celle-ci permet de protéger et de sauvegarder les données personnelles dans le respect des engagements de l’organisation en matière de sécurité et des exigences de conformité définies par le RGPD.

**Azure Disk Encryption** : 
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) tire profit de la fonctionnalité BitLocker de Windows pour assurer le chiffrement de volume des disques de données. La solution s’intègre à Azure Key Vault pour faciliter le contrôle et la gestion des clés de chiffrement de disque.

**Azure SQL Database** : L’instance Azure SQL Database utilise les mesures suivantes pour la sécurité des bases de données :
-   La solution [d’authentification et d’autorisation Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permet la gestion des identités des utilisateurs de bases de données et d’autres services Microsoft dans un emplacement central.
-   [L’audit Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) suit les événements de base de données et les consigne dans un journal d’audit conservé dans un compte de stockage Azure.
-   Azure SQL Database est configuré pour utiliser la technologie [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) qui assure le chiffrement et le déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux de transactions afin de protéger les informations au repos. TDE garantit que nul ne peut accéder sans autorisation aux données personnelles stockées.
-   Des [règles de pare-feu](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) empêchent tout accès aux serveurs de base de données sans autorisation appropriée. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande.
-   La [détection des menaces SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permet de détecter et traiter des menaces potentielles à mesure qu’elles surviennent en déclenchant des alertes de sécurité en lien avec des activités suspectes de base de données, des vulnérabilités potentielles, des attaques par injection de code SQL et des modèles d’accès anormaux aux bases de données.
-   Les [colonnes chiffrées par la fonctionnalité Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantissent que les données personnelles sensibles n’apparaissent jamais sous forme de texte en clair à l’intérieur du système de base de données. Une fois le chiffrement des données activé, seules des applications clientes ou des serveurs d’applications ayant accès aux clés peuvent accéder aux données en texte clair.
- Les [propriétés étendues](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) permettent aux utilisateurs d’interrompre le traitement d’objets de données en ajoutant des propriétés personnalisées à ces objets et en balisant des données comme « Abandonnées » pour aider la logique d’application à empêcher le traitement des données personnelles associées.
- La [Sécurité au niveau des lignes](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permet aux utilisateurs de définir des stratégies de restriction d’accès aux données afin d’interrompre le traitement de celles-ci.
- [SQL Database Dynamic Data Masking (DDM)](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limite l’exposition des données personnelles sensibles en masquant celles-ci aux utilisateurs ou aux applications dépourvus de privilèges. DDM peut détecter automatiquement les données potentiellement sensibles et suggérer les masques appropriés à appliquer. Cette fonctionnalité aide à identifier les données personnelles à protéger conformément au RGPD, ainsi qu’à réduire l’accès à ces données afin qu’elles ne puissent pas sortir de la base de données par le biais d’un accès non autorisé. **Remarque : Les clients doivent ajuster les paramètres DDM de façon à respecter leur schéma de base de données.**

### <a name="identity-management"></a>Gestion des identités
Les technologies suivantes fournissent des fonctionnalités pour gérer l’accès aux données personnelles dans l’environnement Azure :
-   [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) est le service mutualisé basé sur le cloud de Microsoft pour la gestion des annuaires et des identités. Tous les utilisateurs de cette solution, y compris ceux qui accèdent à Azure SQL Database, sont créés dans AAD.
-   L’authentification auprès de l’application est effectuée à l’aide d’AAD. Pour plus d’informations, consultez [Intégration d’applications dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). En outre, le chiffrement des colonnes de base de données utilise AAD pour authentifier l’application auprès d’Azure SQL Database. Pour plus d’informations, découvrez comment [protéger les données sensibles dans SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   Le [contrôle d’accès en fonction du rôle (RBAC) Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permet aux administrateurs de définir des autorisations d’accès affinées pour n’accorder aux utilisateurs que l’accès dont ils ont besoin pour accomplir leur travail. Plutôt que d’attribuer à tous les utilisateurs des autorisations d’accès illimitées aux ressources Azure, les administrateurs peuvent n’autoriser que certaines actions d’accès aux données personnelles. Par exemple, l’accès aux abonnements est limité à l’administrateur d’abonnements.
- La solution [Azure Active Directory Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) permet aux clients de limiter au minimum le nombre d’utilisateurs qui ont accès à des informations telles que des données personnelles.  Les administrateurs peuvent utiliser AAD Privileged Identity Management pour découvrir, restreindre et surveiller les identités privilégiées et leur accès aux ressources. Cette fonctionnalité permet également d’appliquer un accès administratif juste-à-temps à la demande si nécessaire.
-   La solution [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) détecte les vulnérabilités pouvant affecter les identités d’une organisation, et configure des réponses automatiques à des actions suspectes détectées en lien avec les identités de votre organisation. Elle examine aussi les incidents suspects et prend des mesures appropriées pour les résoudre.

### <a name="security"></a>Sécurité
**Gestion des secrets** : cette solution utilise [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pour gérer les clés et les secrets. Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. Les fonctionnalités suivantes d’Azure Key Vault aident les clients à protéger les données personnelles et l’accès à celles-ci :
- Les stratégies d’accès avancées sont configurées en fonction des besoins.
- Les stratégies d’accès Key Vault sont définies avec des autorisations minimales requises pour les clés et les secrets.
- L’ensemble des clés et secrets figurant dans Key Vault présentent des dates d’expiration.
- Toutes les clés stockées dans Key Vault sont protégées par des modules de sécurité matériels (HSM) spécialisés. La clé est de type RSA 2 048 bits protégé par HSM.
- Les autorisations minimales requises sont accordées à l’ensemble des utilisateurs et identités à l’aide d’un contrôle d’accès en fonction du rôle (RBAC).
- Les journaux de diagnostics pour Key Vault sont activés avec une période de rétention d’au moins 365 jours.
- Les opérations de chiffrement autorisées pour les clés sont restreintes à celles qui sont nécessaires.

**Alertes de sécurité** : [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) permet aux clients de surveiller le trafic, de collecter des journaux et d’analyser les sources de données pour y rechercher d’éventuelles menaces. En outre, Azure Security Center accède à une configuration existante des services Azure pour fournir des recommandations en matière de configuration et de service afin de contribuer à l’amélioration de la situation de sécurité et à la protection des données personnelles. Azure Security Center génère un [rapport d’intelligence des menaces](https://docs.microsoft.com/azure/security-center/security-center-threat-report) pour chaque menace détectée afin d’aider les équipes chargées de répondre aux incidents à examiner et à contrecarrer les menaces.

### <a name="logging-and-auditing"></a>Journalisation et audit

[Log Analytics](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) assure une journalisation complète de l’activité système et utilisateur, ainsi que de l’intégrité du système. La solution [Log Analytics](https://azure.microsoft.com/services/log-analytics/) collecte et analyse les données générées par les ressources dans les environnements Azure et locaux.
- **Journaux d’activité** : les [journaux d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fournissent des insights sur les opérations ayant été effectuées sur les ressources d’un abonnement. Les journaux d’activité peuvent aider à déterminer l’initiateur, l’heure d’exécution et l’état d’une opération.
- **Journaux de diagnostic** : les [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluent l’ensemble des journaux générés par chaque ressource. Ces journaux incluent les journaux système des événements Windows, ainsi que les journaux du service Stockage Blob Azure, des tables et des files d’attente.
- **Archivage des journaux** : tous les journaux de diagnostic sont archivés dans un compte de stockage Azure centralisé et chiffré, et sont conservés pendant une période prédéfinie de deux jours. Ces journaux se connectent à Azure Log Analytics à des fins de traitement, de stockage et de génération de rapports de tableau de bord.

En outre, cette architecture intègre également les solutions de supervision suivantes :
-   [AD Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment) : la solution Active Directory Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit une liste hiérarchisée de suggestions spécifiques pour l’infrastructure de serveur déployée.
-   [Antimalware Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware) : la solution de logiciel anti-programme malveillant signale les programmes malveillants, les menaces et l’état de protection.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) : la solution Azure Automation stocke, exécute et gère les runbooks.
-   [Security and Audit](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) : le tableau de bord Security and Audit donne une vue d’ensemble de l’état de sécurité des ressources en affichant des métriques concernant des domaines de sécurité, des problèmes notables, des détections, des menaces et des requêtes de sécurité courantes.
-   [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment) : la solution SQL Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit aux clients une liste hiérarchisée de suggestions spécifiques pour l’infrastructure de serveur déployée.
-   [Update Management](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management) : cette solution de gestion des mises à jour aide les clients à gérer les mises à jour de sécurité du système d’exploitation, notamment en leur indiquant l’état des mises à jour disponibles et le processus d’installation des mises à jour requises.
-   [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth) : cette solution de contrôle d’intégrité des agents indique le nombre d’agents déployés et leur répartition géographique, ainsi que le nombre d’agents qui ne répondent pas ou qui envoient des données opérationnelles.
-   [Journaux d’activité Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity) : la solution Activity Log Analytics facilite l’analyse des journaux d’activité Azure de tous les abonnements Azure d’un client.
-   [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) : cette solution de suivi des modifications permet aux clients d’identifier facilement des modifications dans l’environnement.

**Azure Monitor** : 
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) aide les utilisateurs à suivre les performances, à garantir la sécurité et à identifier les tendances en permettant aux entreprises d’effectuer des audits, de créer des alertes et d’archiver des données, notamment en assurant le suivi des appels d’API dans les ressources Azure des clients.

**Application Insights** : 
[Application Insights](https://docs.microsoft.com/azure/application-insights/) est un service extensible de gestion des performances d’applications destiné aux développeurs web sur diverses plateformes. Ce service permet de surveiller une application web en direct. Il détecte les anomalies de performances et intègre de puissants outils d’analyse conçus pour aider à diagnostiquer les problèmes et à comprendre l’usage que les utilisateurs font d’une application. Ce service a été conçu pour permettre aux utilisateurs d’améliorer continuellement le niveau de performance et la convivialité.

## <a name="threat-model"></a>Modèle de menace

Le diagramme de flux de données de cette architecture de référence figure ci-dessous et est également disponible en [téléchargement](https://aka.ms/gdprAnalyticsdfd). Ce modèle peut aider les clients à comprendre les points de risque potentiel de l’infrastructure du système lors de l’apport de modifications.

![Modèle de menace en matière d’analytique des données pour le RGPD](images/gdpr-analytics-threat-model.png?raw=true "Modèle de menace en matière d’analytique des données pour le RGPD")

## <a name="compliance-documentation"></a>Documentation sur la conformité
L’[Azure Blueprint Sécurité et conformité – Matrice de responsabilités des clients en lien avec le RGPD](https://aka.ms/gdprCRM) répertorie les responsabilités du contrôleur et du processeur pour tous les articles du RGPD. Notez que dans le cas des services Azure, le contrôleur désigne généralement un client, et que le rôle de responsable du traitement incombe à Microsoft.

Le document [Azure Security and Compliance Blueprint - GDPR Data Analytics Implementation Matrix](https://aka.ms/gdprAnalytics) (Azure Blueprint Sécurité et conformité - Matrice d’implémentation d’analytique des données en lien avec le RGPD) fournit des informations sur les articles du RGPD que prend en compte l’architecture d’analytique des données, dont des descriptions détaillée de la façon dont l’implémentation répond aux exigences de chaque article couvert.


## <a name="guidance-and-recommendations"></a>Instructions et recommandations
### <a name="vpn-and-expressroute"></a>VPN et ExpressRoute
Il convient de configurer un tunnel VPN sécurisé ou un routage [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) pour établir une connexion sécurisée aux ressources déployées dans le cadre de cette architecture de référence en matière d’analytique des données. En configurant correctement un VPN ou un routage ExpressRoute, les clients peuvent ajouter une couche de protection au niveau des données en transit.

En implémentant un tunnel VPN sécurisé avec Azure, il est possible de créer une connexion privée virtuelle entre un réseau local et un réseau virtuel Azure. Cette connexion via Internet permet aux clients de « tunneler » des informations en toute sécurité par le biais d’une liaison chiffrée entre leur réseau et Azure. La technologie de réseau privé virtuel de site à site, aussi sécurisée qu’éprouvée, est déployée par des entreprises de toutes tailles depuis des décennies. Le [mode tunnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) est utilisé dans cette option comme un mécanisme de chiffrement.

Étant donné que le trafic à l’intérieur du tunnel VPN transite par Internet avec un VPN de site à site, Microsoft offre une autre option de connexion encore plus sécurisée. Azure ExpressRoute est une liaison réseau étendu dédiée entre Azure et un emplacement local ou un fournisseur d’hébergement Exchange. Les connexions ExpressRoute ne transitent pas par Internet, et offrent de meilleurs niveaux de fiabilité, de rapidité, de latence et de sécurité que les connexions classiques via Internet. En outre, comme il s’agit d’une connexion directe du fournisseur de télécommunications du client, les données ne circulent pas sur Internet et n’y sont donc pas exposées.

Les meilleures pratiques concernant l’implémentation d’un réseau hybride sécurisé qui étend un réseau local à Azure sont [décrites ici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-etl-process"></a>Processus ETL (extraction, transformation et chargement)
La technologie [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) peut charger des données dans Azure SQL Database sans avoir besoin de recourir à un processus ETL ou à un outil d’importation distincts. PolyBase permet d’accéder aux données par le biais de requêtes T-SQL. Il est possible d’utiliser PolyBase avec une pile de décisionnel et d’analyse Microsoft, ainsi qu’avec des outils tiers compatibles avec SQL Server.

### <a name="azure-active-directory-setup"></a>Installation d’Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) joue un rôle essentiel dans la gestion du déploiement et de la configuration des accès des membres du personnel qui interagissent avec l’environnement. Une instance Windows Server Active Directory existante peut être intégrée à AAD en [quatre clics](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Les clients peuvent également associer l’infrastructure (contrôleurs de domaine) Active Directory déployée à un service AAD existant en définissant cette infrastructure comme sous-domaine d’une forêt AAD.

## <a name="disclaimer"></a>Clause d'exclusion de responsabilité

 - Ce document est fourni à titre d’information uniquement. MICROSOFT N’ACCORDE AUCUNE GARANTIE EXPRESSE, IMPLICITE OU STATUTAIRE EN LIEN AVEC LES INFORMATIONS CONTENUES DANS CE DOCUMENT. Ce document est fourni « en l’état ». Les informations et les points de vue exprimés dans ce document, y compris les URL et autres références à des sites web, peuvent être modifiés sans préavis. Les clients qui lisent ce document assument les risques liés à son utilisation.
 - Ce document n’accorde aux clients aucun droit légal de propriété intellectuelle sur les produits ou solutions Microsoft.
 - Les clients peuvent copier et utiliser ce document pour un usage interne, à titre de référence.
 - Certaines recommandations contenues dans ce document peuvent entraîner une augmentation des taux d’utilisation des données, des réseaux ou des ressources de calcul dans Azure, débouchant sur des coûts de licence ou d’abonnement supplémentaires.
 - Cette architecture constitue une base que les clients peuvent modifier en fonction de leurs besoins. Elle ne doit pas être utilisée telle quelle dans un environnement de production.
 - Développé en tant que référence, ce document ne doit pas être utilisé pour définir tous les moyens par lesquels un client peut satisfaire à des réglementations et exigences de conformité spécifiques. Les clients doivent rechercher une assistance juridique auprès de leur organisation pour connaître les implémentations client approuvées.
