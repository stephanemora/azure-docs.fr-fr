---
title: Blueprint sur la sécurité et la conformité Azure - Analytique des données pour NIST SP 800-171
description: Blueprint sur la sécurité et la conformité Azure - Analytique des données pour NIST SP 800-171
services: security
author: jomolesk
ms.assetid: ca75d2a8-4e20-489b-9a9f-3a61e74b032d
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: f79ba9ae60454d4e73c914fc1c8af675a6d07d5d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60608874"
---
# <a name="azure-security-and-compliance-blueprint---data-analytics-for-nist-sp-800-171"></a>Blueprint sur la sécurité et la conformité Azure - Analytique des données pour NIST SP 800-171

## <a name="overview"></a>Vue d'ensemble
[NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) fournit des instructions pour protéger les informations non classifiées contrôlées qui se trouvent dans des systèmes d’information et des organisations qui ne font pas partie du secteur public. NIST SP 800-171 établit 14 familles d’exigences de sécurité pour protéger la confidentialité des informations non classifiées contrôlées.

Ce blueprint sur la sécurité et la conformité d’Azure fournit des conseils pour aider les utilisateurs à déployer une architecture d’analytique des données dans Azure qui implémente une partie des contrôles NIST SP 800-171. Cette solution montre comment les clients peuvent répondre à des exigences de conformité et de sécurité spécifiques. Elle sert également de base pour les clients qui souhaitent générer et configurer leurs propres solutions d’analyse de données dans Azure.

Cette architecture de référence, ainsi que le guide d‘implémentation et le modèle de menace associés, sont destinés à servir de base aux clients qui doivent les adapter à leurs besoins spécifiques. Ils ne doivent pas être utilisés tels quels dans un environnement de production. Le déploiement de cette architecture sans modification est insuffisant pour répondre entièrement aux exigences de NIST SP 800-171. Il incombe aux clients d‘effectuer des contrôles de conformité et de sécurité appropriés de toute solution générée qui utilise cette architecture. Les exigences peuvent varier selon les spécificités de l‘implémentation de chaque client.

## <a name="architecture-diagram-and-components"></a>Diagramme et composants de l’architecture
Cette solution offre une plateforme analytique sur la base de laquelle les clients peuvent créer leurs propres outils d’analyse. L’architecture de référence présente un cas d’usage générique. Les clients peuvent l’utiliser pour entrer des données via des importations de données en bloc par l’administrateur de données/SQL. Ils peuvent également l’utiliser pour entrer des données à l’aide de mises à jour de données opérationnelles via un utilisateur opérationnel. Ces deux axes de travail incorporent la solution Azure Functions pour l’importation des données dans Azure SQL Database. Chaque client doit configurer Azure Functions par le biais du portail Azure pour la gestion des tâches d’importation conformément à ses exigences d’analyse.

Azure offre divers services de création de rapports et d’analyse pour le client. Cette solution utilise les services Azure Machine Learning et SQL Database pour parcourir rapidement les données et fournir plus rapidement des résultats par le biais d’une modélisation plus intelligente des données. Machine Learning a pour but d’accroître la vitesse des requêtes en découvrant de nouvelles relations entre les jeux de données. Initialement, l’apprentissage des données est effectué par le biais de plusieurs fonctions statistiques. Par la suite, il est possible de synchroniser jusqu’à sept pools de requêtes supplémentaires avec les mêmes modèles tabulaires pour répartir la charge de travail des requêtes et réduire les temps de réponse. Le serveur du client porte ce total de pools de requêtes à huit.

SQL Database est configurable avec des index de stockage de colonnes afin d’améliorer les fonctions d’analyse et de génération de rapports. Machine Learning et SQL Database peuvent faire l’objet d’un scale-up ou d’un scale-down, ou être complètement arrêtés en fonction de l’utilisation du client. La totalité du trafic SQL est chiffrée avec SSL grâce à l’inclusion de certificats auto-signés. En guise de bonne pratique, nous recommandons l’utilisation d’une autorité de certification de confiance afin de renforcer la sécurité.

Une fois les données chargées dans la base de données SQL et assimilées par Machine Learning, elles sont synthétisées par l’utilisateur opérationnel et par l’administrateur de données/SQL avec Power BI. Power BI assure un affichage intuitif des données et fusionne les informations de plusieurs jeux de données afin d’offrir un meilleur aperçu de l’activité. Power BI a un haut niveau d’adaptabilité et s’intègre facilement à SQL Database. Les clients peuvent le configurer pour gérer un large éventail de scénarios basés sur les besoins métiers des clients.

L’ensemble de la solution repose sur le service Stockage Azure, que les clients configurent à partir du portail Azure. Le service Stockage chiffre toutes les données à l’aide de la fonctionnalité Storage Service Encryption pour garantir la confidentialité des données au repos. Le stockage géoredondant garantit qu’un événement indésirable survenu dans le centre de données principal du client n’occasionne aucune perte de données. Une seconde copie est stockée à un autre emplacement distant de plusieurs centaines de kilomètres.

Pour renforcer la sécurité, toutes les ressources dans cette solution sont gérées sous forme de groupe de ressources dans Azure Resource Manager. Le contrôle d‘accès en fonction du rôle Azure Active Directory (Azure AD) permet de contrôler l‘accès aux ressources déployées. Ces ressources incluent les clés de client dans Azure Key Vault. L’intégrité du système est surveillée dans Azure Security Center et Azure Monitor. Les clients configurent ces deux services de supervision pour capturer les journaux d’activité. L‘intégrité du système est affichée dans un tableau de bord unique qui est facile à utiliser.

SQL Database est généralement géré via SQL Server Management Studio. Il s‘exécute à partir d‘une machine locale configurée pour accéder à la base de données SQL via une connexion VPN ou Azure ExpressRoute sécurisée. *Nous vous recommandons de configurer une connexion VPN ou ExpressRoute pour la gestion et l’importation de données dans le groupe de ressources*.

![Diagramme de l’architecture de référence Analytique données pour NIST SP 800-171](images/nist171-analytics-architecture.png "Diagramme de l’architecture de référence Analytique données pour NIST SP 800-171")

Cette solution utilise les services Azure suivants. Pour plus d‘informations, consultez la section sur l‘[architecture de déploiement](#deployment-architecture).

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

**Azure Event Grid** : avec [Event Grid](https://docs.microsoft.com/azure/event-grid/overview), les clients peuvent créer facilement des applications avec des architectures basées sur des événements. Les utilisateurs sélectionnent la ressource Azure à laquelle ils veulent s’abonner. Ensuite, ils fournissent au gestionnaire d’événements ou webhook un point de terminaison vers lequel envoyer l’événement. Les clients peuvent sécuriser les points de terminaison webhook en ajoutant des paramètres de requête à l’URL du webhook lorsqu’ils créent un abonnement à un événement. Event Grid ne prend en charge que les points de terminaison webhook HTTPS. Avec Event Grid, les clients peuvent contrôler le niveau d’accès accordé à différents utilisateurs pour effectuer diverses opérations de gestion. Les utilisateurs peuvent lister les abonnements aux événements, en créer de nouveaux et générer des clés. Event Grid utilise RBAC Azure.

**Azure Functions** : [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) est un service de calcul serverless qui exécute du code à la demande. Vous n’êtes pas obligé de configurer ou gérer explicitement l’infrastructure. Utilisez Azure Functions pour exécuter un script ou du code en réponse à un grand nombre d’événements.

**Azure Machine Learning service** : [Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/) est une technique de science des données qui permet aux ordinateurs d’utiliser des données existantes afin de prévoir les tendances, les résultats et les comportements futurs.

**Azure Data Catalog** : [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) permet aux utilisateurs qui gèrent les données de détecter et comprendre facilement les sources de données. Les sources de données courantes peuvent être inscrites, étiquetées et soumises à des recherches de données. Les données restent à leur emplacement existant, mais une copie de leurs métadonnées est ajoutée à Data Catalog. Une référence à l’emplacement de source de données est incluse. Les métadonnées sont indexées pour faciliter la découverte via la recherche de chaque source de données. L’indexation permet aussi aux utilisateurs qui la découvrent de la comprendre.

### <a name="virtual-network"></a>Réseau virtuel
Cette architecture de référence définit un réseau privé virtuel avec un espace d’adressage de 10.0.0.0/16.

**Groupes de sécurité réseau** : les [groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) contiennent des listes de contrôle d’accès qui autorisent ou interdisent le trafic au sein d’un réseau virtuel. Les groupes de sécurité réseau peuvent être utilisés pour sécuriser le trafic au niveau d’un sous-réseau ou d’une machine virtuelle individuelle. Les NSG disponibles sont les suivants :
  - un NSG pour Active Directory ;
  - un NSG pour la charge de travail.

Chaque groupe de sécurité réseau a des ports et protocoles spécifiques ouverts afin que la solution puisse fonctionner correctement et en toute sécurité. En outre, les configurations suivantes sont activées pour chaque groupe de sécurité réseau :
  - Les [événements et journaux de diagnostic](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) sont activés et stockés dans un compte de stockage.
  - La solution Journaux Azure Monitor est connectée aux [diagnostics du groupe de sécurité réseau](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Sous-réseaux** : Chaque sous-réseau est associé à son groupe de sécurité réseau correspondant.

### <a name="data-in-transit"></a>Données en transit
Par défaut, Azure chiffre toutes les communications avec les centres de données Azure. Toutes les transactions en direction du service Stockage par le biais du portail Azure s’effectuent via HTTPS.

### <a name="data-at-rest"></a>Données au repos

L’architecture protège les données au repos à l’aide d’un chiffrement, d’un audit de base de données et d’autres mesures.

**Stockage Azure** : pour satisfaire aux exigences de chiffrement des données au repos, l’ensemble du service [Stockage](https://azure.microsoft.com/services/storage/) utilise la fonctionnalité de chiffrement [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption). Cette fonctionnalité permet de protéger et de sauvegarder les données dans le respect des engagements de l’organisation en matière de sécurité et des exigences de conformité définies par NIST SP 800-171.

**Azure Disk Encryption** : [Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) utilise la fonctionnalité BitLocker de Windows pour chiffrer les volumes des disques de données. La solution s’intègre à Azure Key Vault pour faciliter le contrôle et la gestion des clés de chiffrement de disque.

**Azure SQL Database** : L’instance SQL Database utilise les mesures suivantes pour la sécurité des bases de données :
-   La solution [d’authentification et d’autorisation Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permet de gérer les identités des utilisateurs de bases de données et d’autres services Microsoft dans un emplacement central.
-   L’[audit Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) suit les événements de base de données et consigne ceux-ci dans un journal d’audit conservé dans un compte de stockage Azure.
-   SQL Database est configuré pour utiliser [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Il effectue le chiffrement et le déchiffrement en temps réel de la base de données, des sauvegardes associées et des fichiers journaux de transactions, afin de protéger les informations au repos. Transparent Data Encryption garantit que les données stockées ne font pas l’objet d’accès non autorisés.
-   Des [règles de pare-feu](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) empêchent tout accès aux serveurs de base de données sans autorisation appropriée. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande.
-   [SQL Threat Detection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permet la détection et la réponse aux menaces potentielles lorsqu’elles se produisent. Il fournit des alertes de sécurité en cas d’activités de base de données suspectes, de vulnérabilités potentielles, d’attaques par injection de code SQL et de modalités d’accès anormales aux bases de données.
-   Les [colonnes chiffrées](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantissent que les données sensibles n’apparaissent jamais en texte clair à l’intérieur du système de base de données. Une fois le chiffrement des données activé, seules des applications clientes ou des serveurs d’applications ayant accès aux clés peuvent accéder aux données en texte clair.
- [SQL Database Dynamic Data Masking](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limite l’exposition des données sensibles en masquant celles-ci aux utilisateurs ou aux applications dépourvus de privilèges. Il peut détecter automatiquement les données potentiellement sensibles et suggérer les masques appropriés à appliquer. Dynamic Data Masking permet de réduire l’accès afin que les données sensibles ne sortent pas de la base de données via un accès non autorisé. *Les clients doivent ajuster les paramètres pour respecter leur schéma de base de données.*

### <a name="identity-management"></a>Gestion des identités
Les technologies suivantes offrent des fonctionnalités de gestion de l’accès aux données dans l’environnement Azure :
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) est le service multilocataire basé sur le cloud de Microsoft pour la gestion des identités et des annuaires. Tous les utilisateurs de cette solution sont créés dans Azure AD et incluent les utilisateurs qui accèdent à SQL Database.
-   L’authentification auprès de l’application est effectuée à l’aide d’Azure AD. Pour plus d’informations, consultez [Intégrer des applications à Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Le chiffrement des colonnes de base de données utilise également Azure AD pour authentifier l’application auprès de SQL Database. Pour plus d’informations, découvrez comment [protéger les données sensibles dans SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [RBAC Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) peut être utilisé par les administrateurs pour définir des autorisations d’accès précises. Avec cela, ils peuvent accorder uniquement les droits d’accès dont les utilisateurs ont besoin pour effectuer leur travail. Au lieu d’accorder à tous les utilisateurs des autorisations illimitées sur les ressources Azure, les administrateurs peuvent autoriser seulement certaines actions pour accéder aux données. Par exemple, l’accès aux abonnements est limité à l’administrateur d’abonnements.
- La solution [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) permet aux clients de minimiser le nombre d’utilisateurs qui ont accès à des informations spécifiques, telles que des données. Les administrateurs peuvent utiliser Azure AD Privileged Identity Management pour découvrir, restreindre et superviser les identités privilégiées et leur accès aux ressources. Cette fonctionnalité permet également d’appliquer un accès administratif juste-à-temps à la demande, si nécessaire.
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) détecte les vulnérabilités potentielles susceptibles d’affecter les identités d’une organisation. Il configure des réponses automatiques aux actions suspectes détectées et liées aux identités d’une organisation. Il examine également les incidents suspects pour prendre les mesures appropriées afin de les résoudre.

### <a name="security"></a>Sécurité
**Gestion des secrets** : la solution utilise [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pour gérer les clés et les secrets. Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. Les fonctionnalités Key Vault suivantes aident les clients à protéger les données :
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

### <a name="logging-and-auditing"></a>Journalisation et audit

Les services Azure assurent une journalisation complète de l’activité du système et des utilisateurs, ainsi que de l’intégrité du système :
- **Journaux d’activité** : les [journaux d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fournissent des insights sur les opérations ayant été effectuées sur les ressources d’un abonnement. Les journaux d’activité peuvent aider à déterminer l’initiateur, l’heure d’exécution et l’état d’une opération.
- **Journaux de diagnostic** : les [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluent l’ensemble des journaux d’activité générés par chaque ressource. Ils incluent les journaux des événements système de Windows, les journaux d’activité de Stockage, les journaux d’audit de Key Vault, ainsi que les journaux d’activité de pare-feu et d’accès d’Azure Application Gateway. Tous les journaux de diagnostic sont consignés dans un compte de stockage Azure centralisé et chiffré pour l’archivage. Les utilisateurs peuvent configurer la période de rétention jusqu‘à 730 jours, pour répondre à leurs besoins spécifiques.

**Journaux d’activité Azure Monitor** : les journaux sont regroupés dans [Journaux Azure Monitor](https://azure.microsoft.com/services/log-analytics/) à des fins de traitement, de stockage et de génération de rapports de tableau de bord. Une fois les données collectées, elles sont organisées dans des tables distinctes pour chaque type de données au sein des espaces de travail Log Analytics. De cette façon, toutes les données peuvent être analysées ensemble, quelle que soit leur source d’origine. Security Center s’intègre à Journaux Azure Monitor. Les clients peuvent utiliser des requêtes Kusto pour accéder à leurs données d’événement de sécurité et les combiner avec des données provenant d’autres services.

Cette architecture intègre également les [solutions de supervision](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) suivantes :
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment) : la solution Active Directory Health Check permet d’évaluer les risques et l’intégrité des environnements de serveur à intervalles réguliers. Elle fournit une liste hiérarchisée de suggestions propres à l’infrastructure de serveurs déployée.
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment) : la solution SQL Health Check permet d’évaluer les risques et l’intégrité des environnements de serveur à intervalles réguliers. Elle fournit aux clients une liste hiérarchisée de suggestions propres à l’infrastructure de serveurs déployée.
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth) : la solution Agent Health signale le nombre d’agents déployés et leur répartition géographique. Elle signale également le nombre d’agents qui ne répondent pas et le nombre d’agents qui envoient des données opérationnelles.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity) : la solution Activity Log Analytics facilite l’analyse des journaux d’activité Azure de tous les abonnements Azure d’un client.

**Azure Automation** : [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) stocke, exécute et gère les runbooks. Dans cette solution, les runbooks aident à collecter les journaux d’activité de SQL Database. Les clients peuvent utiliser la solution Automation [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) pour identifier facilement les changements dans l’environnement.

**Azure Monitor** : [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) aide les utilisateurs à suivre les performances, à garantir la sécurité et à identifier les tendances. Les organisations peuvent l’utiliser pour auditer, créer des alertes et archiver les données. Elles peuvent également suivre les appels d’API dans leurs ressources Azure.

**Application Insights** : [Application Insights](https://docs.microsoft.com/azure/application-insights/) est un service extensible de gestion des performances des applications destiné aux développeurs web sur de multiples plateformes. Il détecte les anomalies de performances et inclut de puissants outils d’analyse. Ces outils aident à diagnostiquer les problèmes et aident les clients à comprendre ce que font les utilisateurs avec l’application. Ce service a été conçu pour permettre aux utilisateurs d’améliorer continuellement le niveau de performance et la convivialité.

## <a name="threat-model"></a>Modèle de menace

Le diagramme de flux de données de cette architecture de référence figure ci-dessous et est également disponible en [téléchargement](https://aka.ms/nist171-analytics-tm). Ce modèle peut aider les clients à comprendre les points de risque potentiel de l’infrastructure du système lors de l’apport de modifications.

![Modèle de risque Analytique données pour NIST SP 800-171](images/nist171-analytics-threat-model.png "Modèle de risque Analytique données pour NIST SP 800-171")

## <a name="compliance-documentation"></a>Documentation sur la conformité
Le [Blueprint sur la sécurité et la conformité Azure - Matrice de responsabilités des clients de NIST SP 800-171](https://aka.ms/nist171-crm) liste tous les contrôles de sécurité exigés par NIST SP 800-171. La matrice indique si l’implémentation de chaque contrôle revient à Microsoft, au client, ou est partagé entre les deux.

Le [blueprint sur la sécurité et la conformité Azure - Matrice d’implémentation des contrôles d’analyse des données pour NIST SP 800-171](https://aka.ms/nist171-analytics-cim) fournit des informations sur les contrôles de NIST SP 800-171 que prend en compte l’architecture d’analyse des données. Il comprend une description détaillée de la façon dont l’implémentation répond aux exigences de chaque contrôle couvert.


## <a name="guidance-and-recommendations"></a>Instructions et recommandations
### <a name="vpn-and-expressroute"></a>VPN et ExpressRoute
Il faut configurer un tunnel VPN sécurisé ou un routage [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) pour établir une connexion sécurisée aux ressources déployées dans le cadre de cette architecture de référence en matière d’analyse des données. En configurant correctement un VPN ou un routage ExpressRoute, les clients peuvent ajouter une couche de protection au niveau des données en transit.

En implémentant un tunnel VPN sécurisé avec Azure, il est possible de créer une connexion privée virtuelle entre un réseau local et un réseau virtuel Azure. Cette connexion s’effectue via Internet. Les clients peuvent l’utiliser pour créer un « tunnel » pour le transit sécurisé des informations au sein d’une liaison chiffrée entre leur réseau et Azure. La technologie de VPN de site à site, aussi sécurisée qu’aguerrie, est déployée par des entreprises de toutes tailles depuis des décennies. Le [mode tunnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) est utilisé dans cette option comme un mécanisme de chiffrement.

Étant donné que le trafic à l’intérieur du tunnel VPN transite par Internet avec un VPN de site à site, Microsoft offre une autre option de connexion encore plus sécurisée. ExpressRoute est une liaison réseau étendu dédiée entre Azure et un emplacement local ou un fournisseur d’hébergement Exchange. Les connexions ExpressRoute se raccordent directement au fournisseur de télécommunications d’un client. Par conséquent, les données ne transitent pas par Internet et ne sont pas exposées à ce dernier. Ces connexions offrent davantage de fiabilité, des vitesses supérieures, des latences inférieures et une sécurité renforcée par rapport aux connexions standard. 

Les meilleures pratiques concernant l’implémentation d’un réseau hybride sécurisé qui étend un réseau local à Azure sont [décrites ici](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Processus d’extraction, de transformation et de chargement
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) peut charger des données dans SQL Database sans outil spécifique pour extraire, transformer, charger ni importer ces données. PolyBase permet d’accéder aux données par le biais de requêtes T-SQL. Il est possible d’utiliser PolyBase avec une pile d’analyse et d’aide à la décision Microsoft, ainsi qu’avec des outils tiers compatibles avec SQL Server.

### <a name="azure-ad-setup"></a>Configuration d’Azure AD
[Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) joue un rôle essentiel dans la gestion du déploiement et de la configuration des accès des membres du personnel qui interagissent avec l’environnement. Active Directory local peut être intégré à Azure AD en [quatre clics](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Les clients peuvent également lier l’infrastructure Active Directory déployée (contrôleurs de domaine) à Azure AD. Pour ce faire, configurez l’infrastructure Active Directory déployée en tant que sous-domaine d’une forêt Azure AD.

## <a name="disclaimer"></a>Clause d'exclusion de responsabilité

 - Ce document est fourni à titre d’information uniquement. MICROSOFT N’ACCORDE AUCUNE GARANTIE EXPRESSE, IMPLICITE OU STATUTAIRE EN LIEN AVEC LES INFORMATIONS CONTENUES DANS CE DOCUMENT. Ce document est fourni « en l’état ». Les informations et les points de vue exprimés dans ce document, y compris les URL et autres références à des sites web, peuvent être modifiés sans préavis. Les clients qui lisent ce document assument les risques liés à son utilisation.
 - Ce document n’accorde aux clients aucun droit légal de propriété intellectuelle sur les produits ou solutions Microsoft.
 - Les clients peuvent copier et utiliser ce document pour un usage interne, à titre de référence.
 - Certaines recommandations contenues dans ce document peuvent entraîner une augmentation des taux d’utilisation des données, des réseaux ou des ressources de calcul dans Azure, débouchant sur des coûts de licence ou d’abonnement supplémentaires.
 - Cette architecture constitue une base que les clients peuvent modifier en fonction de leurs besoins. Elle ne doit pas être utilisée telle quelle dans un environnement de production.
 - Développé en tant que référence, ce document ne doit pas être utilisé pour définir tous les moyens par lesquels un client peut satisfaire à des réglementations et exigences de conformité spécifiques. Les clients doivent rechercher une assistance juridique auprès de leur organisation pour connaître les implémentations client approuvées.
