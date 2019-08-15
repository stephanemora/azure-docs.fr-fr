---
title: Programme Blueprint Security and Compliance Azure - Analyse pour FedRAMP
description: Programme Blueprint Security and Compliance Azure - Analyse pour FedRAMP
services: security
author: jomolesk
ms.assetid: b4675715-668e-4557-9c1c-698aabf62ceb
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 7b07fee46bce4c7b80346eb0b4c0fccd5245d87f
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946875"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-fedramp"></a>Blueprint de sécurité et de conformité Azure : Analytique pour FedRAMP

## <a name="overview"></a>Vue d'ensemble

[FedRAMP (Federal Risk and Authorization Management Program)](https://www.fedramp.gov/) est un programme déployé à l’échelle de l’administration américaine, visant à rationaliser l’approche en matière d’évaluation de la sécurité, d’autorisation et de surveillance continue des services et produits cloud. Ce document Azure Blueprint Security and Compliance vous fournit des instructions pour proposer une architecture d’analyse Microsoft Azure permettant d’implémenter un sous-ensemble de contrôles FedRAMP High. Cette solution fournit des instructions pour le déploiement et la configuration des ressources Azure pour une architecture de référence commune, illustrant diverses façons dont les clients peuvent satisfaire à des exigences de conformité et de sécurité spécifiques, et sert de base aux clients souhaitant générer et configurer leurs propres solutions d’analyse dans Azure.

Cette architecture de référence, ainsi que le guide de mise en œuvre du contrôle associé et les modèles de menace, sont destinés à servir de base aux clients qui doivent les adapter à leurs besoins spécifiques et non les utiliser tels quels dans un environnement de production. Le déploiement d’une application dans cet environnement, sans y apporter de modifications, ne permet pas de répondre entièrement aux exigences de la ligne de base FedRAMP High. Notez les points suivants :
- L’architecture constitue un point de départ pour aider les clients à déployer des charges de travail sur Azure d’une manière conforme à FedRAMP.
- Il incombe aux clients d’évaluer de façon appropriée la sécurité et la conformité de toute solution basée sur cette architecture, car les exigences peuvent varier en fonction des spécificités de chaque implémentation.

## <a name="architecture-diagram-and-components"></a>Diagramme et composants de l’architecture

Cette solution offre une plateforme analytique sur la base de laquelle les clients peuvent créer leurs propres outils d’analyse. L’architecture de référence présente un cas d’usage générique dans le cadre duquel les clients entrent leurs données soit par le biais d’importations de données en bloc effectuées par l’administrateur SQL/de données, soit par l’intermédiaire de mises à jour de données opérationnelles mises en œuvre par un utilisateur du secteur opérationnel. Ces deux axes de travail incorporent la solution [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) pour l’importation des données dans SQL Database. Chaque client doit configurer Azure Functions par le biais du portail Azure pour la gestion des tâches d’importation conformément aux exigences d’analyse qui lui sont propres.

Microsoft Azure offre aux clients divers services de création de rapports et d’analytique ; toutefois, cette solution incorpore les services Azure Analysis Services parallèlement à Azure SQL Database pour parcourir rapidement les données et accélérer l’obtention de résultats par le biais d’une modélisation plus efficace des données client. Azure Analytics Services est une forme d’apprentissage automatique destinée à accroître la vitesse des requêtes en découvrant de nouvelles relations entre les jeux de données. Une fois cet apprentissage effectué par le biais de diverses fonctions statistiques, il est possible de synchroniser jusqu’à 7 pools de requêtes supplémentaires (8 au total en comptant le serveur du client) avec les mêmes modèles tabulaires pour répartir la charge de travail des requêtes et réduire les temps de réponse.

Les services SQL Database sont configurables avec des index columnstore afin d’améliorer les fonctions d’analyse et de génération de rapports. Azure Analytics Services et SQL Database peuvent faire l’objet d’une montée ou descente en puissance ou être complètement arrêtés en fonction de l’utilisation du client. La totalité du trafic SQL est chiffrée avec SSL grâce à l’inclusion de certificats auto-signés. En guise de meilleure pratique, Azure préconise l’utilisation d’une autorité de certification de confiance afin de renforcer la sécurité.

Une fois les données chargées dans Azure SQL Database et assimilées par Azure Analysis Services, elles sont synthétisées par l’utilisateur du secteur opérationnel et par l’administrateur SQL/de données à l’aide de Power BI. Power BI assure un affichage intuitif des données et fusionne les informations de plusieurs jeux de données afin d’offrir un meilleur aperçu de l’activité. Grâce à son haut niveau d’adaptabilité et à sa simplicité d’intégration à Azure SQL Database, Power BI est configurable pour la prise en charge d’un large éventail de scénarios basés sur les besoins métiers des clients.

L’ensemble de la solution repose sur le service Stockage Azure que les clients de compte configurent à partir du portail Azure. Le service Stockage Azure chiffre toutes les données à l’aide de la fonctionnalité Storage Service Encryption pour garantir la confidentialité des données au repos.  Le stockage géoredondant (GRS) garantit qu’un événement indésirable survenu dans le centre de données principal du client n’occasionnera aucune perte de données, grâce au stockage d’une seconde copie des données à un autre emplacement distant de plusieurs centaines de kilomètres.

Pour renforcer la sécurité, cette architecture gère les ressources à l’aide d’Azure Active Directory et d’Azure Key Vault. L’intégrité du système est surveillée dans Azure Monitor. Les clients configurent ces deux services de supervision pour la capture de journaux d’activité et la centralisation des informations concernant l’intégrité du système dans un tableau de bord facilement consultable.

Azure SQL Database est généralement géré par le biais de la suite d’outils SQL Server Management Studio (SSMS), qui s’exécute à partir d’une machine locale configurée pour accéder à Azure SQL Database par l’intermédiaire d’une connexion VPN ou ExpressRoute sécurisée. **Azure recommande de configurer une connexion VPN ou Azure ExpressRoute pour la gestion et l’importation de données dans le groupe de ressources de l’architecture de référence.**

![Diagramme de l’architecture de référence en matière d’analytique des données pour FedRAMP](images/fedramp-analytics-reference-architecture.png?raw=true "Diagramme de l’architecture de référence en matière d’analytique des données pour FedRAMP")

### <a name="roles"></a>contrôleur
Le plan d’analytique décrit un scénario avec trois types d’utilisateur généraux : l’utilisateur opérationnel, l’administrateur de données/SQL et l’ingénieur système. Le contrôle d’accès en fonction du rôle Azure (RBAC) permet d’implémenter une gestion des accès précise via des rôles personnalisés intégrés. Les ressources sont disponibles pour la configuration du [contrôle d’accès en fonction du rôle](../../role-based-access-control/role-assignments-portal.md) et pour la définition et l’implémentation de [rôles prédéfinis](../../role-based-access-control/built-in-roles.md).

#### <a name="systems-engineer"></a>Ingénieur système
L’ingénieur système est propriétaire de l’abonnement du client pour Azure et configure le déploiement de la solution via le portail Azure.

#### <a name="sqldata-administrator"></a>Administrateur de données/SQL
L’administrateur de données/SQL établit la fonction d’importation de données en bloc et la fonction de mise à jour des données opérationnelles pour téléchargement vers Azure SQL Database. L’administrateur de données/SQL n’est pas responsable des mises à jour des données opérationnelles dans la base de données, mais peut consulter les données via Power BI.

#### <a name="operational-user"></a>Utilisateur opérationnel
L’utilisateur opérationnel met régulièrement à jour les données et est responsable de la génération quotidienne des données. L’utilisateur opérationnel devra également interpréter les résultats via Power BI.

### <a name="azure-services"></a>Services Azure

Cette solution utilise les services Azure suivants. Les informations détaillées concernant l’architecture de déploiement figurent à la section [Architecture de déploiement](#deployment-architecture).
- Azure Functions
- Azure SQL Database
- Azure Analysis Services
- Azure Active Directory
- Azure Key Vault
- Azure Monitor (journaux)
- Stockage Azure
- ExpressRoute/Passerelle VPN
- tableau de bord Power BI

## <a name="deployment-architecture"></a>Architecture de déploiement
La section suivante décrit en détail les éléments de développement et d’implémentation.

![texte de remplacement](images/fedramp-analytics-components.png?raw=true "Diagramme d’analytique des composants FedRAMP")

**Azure Functions** : [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) regroupe des solutions pour l’exécution de petits segments de code dans le cloud en utilisant la plupart des langages de programmation. Les fonctions de cette solution s’intègrent avec Stockage Azure pour extraire automatiquement les données des clients dans le cloud, facilitant ainsi l’intégration à d’autres services Azure. Les fonctions, très évolutives, sont facturées uniquement lorsqu’elles sont exécutées.

**Azure Analysis Services** : [Azure Analysis Service](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) fournit une modélisation des données d’entreprise et une intégration aux services de plateforme de données Azure. Azure Analysis Service accélère la navigation dans de gros volumes de données en combinant les données provenant de plusieurs sources dans un seul modèle de données.

**Power BI** : [Power BI](https://docs.microsoft.com/power-bi/service-azure-and-power-bi) fournit des fonctions d’analytique et de création de rapports aux clients qui souhaitent tirer le meilleur parti de leurs efforts de traitement des données.

### <a name="networking"></a>Mise en réseau
**Groupes de sécurité réseau** : les [NSG](../../virtual-network/virtual-network-vnet-plan-design-arm.md) sont configurés pour gérer le trafic dirigé vers les services et les ressources déployés. Les groupes de sécurité réseau sont définis selon un schéma de refus par défaut et autorisent uniquement le trafic figurant dans la liste de contrôle d’accès (ACL) préconfigurée.

Chaque groupe de sécurité réseau a des ports et protocoles spécifiques ouverts afin que la solution puisse fonctionner correctement et en toute sécurité. En outre, les configurations suivantes sont activées pour chaque groupe de sécurité réseau :
  - Les [événements et journaux de diagnostic](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) sont activés et stockés dans un compte de stockage.
  - La solution [Journaux Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md) est connectée aux journaux de diagnostic du groupe de sécurité réseau.

### <a name="data-at-rest"></a>Données au repos
L’architecture protège les données au repos à l’aide d’un chiffrement, d’un audit de base de données et d’autres mesures.

La **réplication des données** d’Azure Government offre deux options de [réplication des données](https://docs.microsoft.com/azure/storage/common/storage-redundancy) :
 - Le paramètre par défaut pour la réplication des données est le **stockage géo-redondant (GRS)** , qui stocke de manière asynchrone les données du client dans un autre centre de données, situé hors de la région primaire. Cela garantit la récupération des données en cas de perte totale du centre de données principal.
 - Le **stockage localement redondant (LRS)** peut également être configuré via le compte de stockage Azure. Le LRS réplique les données au sein d’une unité d’échelle de stockage, qui est hébergée dans la même région que celle dans laquelle le client crée son compte. Toutes les données sont répliquées simultanément, garantissant ainsi qu’aucune donnée de sauvegarde n’est perdue en cas de panne d’une unité d’échelle de stockage principale.

**Stockage Azure** Pour répondre aux exigences en matière de données chiffrées au repos, tous les services déployés dans cette architecture de référence tirent parti de [Stockage Azure](https://azure.microsoft.com/services/storage/), qui stocke les données avec [Storage Service Encryption](../../storage/common/storage-service-encryption.md).

**Azure Disk Encryption**
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) tire profit de la fonctionnalité BitLocker de Windows pour assurer le chiffrement de volume du SE et des disques de données. La solution s’intègre à Azure Key Vault pour faciliter le contrôle et la gestion des clés de chiffrement de disque.

**Azure SQL Database** L’instance Azure SQL Database utilise les mesures de sécurité de base de données suivantes :
-   La solution [d’authentification et d’autorisation Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permet la gestion des identités des utilisateurs de bases de données et d’autres services Microsoft dans un emplacement central.
-   L’[audit Azure SQL Database](../../sql-database/sql-database-auditing.md) suit les événements de base de données et consigne ceux-ci dans un journal d’audit conservé dans un compte de stockage Azure.
-   SQL Database est configuré pour utiliser [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), qui effectue le chiffrement et le déchiffrement en temps réel des données et fichiers journaux pour protéger les informations au repos. TDE protège les données des accès non autorisés.
-   Des [règles de pare-feu](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) empêchent tout accès aux serveurs de base de données sans autorisation appropriée. Le pare-feu octroie l’accès à la base de données en fonction de l’adresse IP d’origine de chaque demande.
-   La [détection des menaces SQL](../../sql-database/sql-database-threat-detection.md) permet de détecter et traiter les menaces potentielles à mesure qu’elles surviennent en déclenchant des alertes de sécurité en cas d’activités de base de données suspectes, de vulnérabilités potentielles, d’attaques par injection de code SQL et de modèles d’accès anormaux aux bases de données.
-   Des [colonnes Always Encrypted](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantissent que les données sensibles n’apparaissent jamais en tant que texte en clair à l’intérieur du système de base de données. Une fois le chiffrement des données activé, seules des applications clientes ou des serveurs d’applications ayant accès aux clés peuvent accéder aux données en texte clair.
-   Le [masquage de données dynamiques SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) peut être effectué après le déploiement de l’architecture de référence. Les clients doivent ajuster les paramètres de masquage de données dynamiques pour respecter leur schéma de base de données.

### <a name="logging-and-audit"></a>Journalisation et audit
[Azure Monitor](../../azure-monitor/overview.md) génère un affichage de toutes les données de supervision, y compris les journaux d’activité, les métriques et les données de diagnostic, ce qui permet aux clients de créer une image complète de l’intégrité du système.  
[Journaux Azure Monitor](../azure-security-disk-encryption-overview.md) assure une journalisation complète de l’activité du système et des utilisateurs, ainsi que de l’intégrité du système. Ce service collecte et analyse les données générées par les ressources des environnements Azure et locaux.
- **Journaux d’activité** : les [journaux d’activité](../../azure-monitor/platform/activity-logs-overview.md) fournissent des insights sur les opérations ayant été effectuées sur les ressources d’un abonnement.
- **Journaux de diagnostic** : les [journaux de diagnostic](../../azure-monitor/platform/diagnostic-logs-overview.md) incluent l’ensemble des journaux d’activité générés par chaque ressource. Ces journaux d’activité incluent les journaux des événements système Windows, ainsi que les journaux d’activité du service Stockage Blob Azure, des tables et des files d’attente.
- **Journaux d’activité du pare-feu** : Application Gateway fournit des journaux de diagnostic et d’accès complets. Les journaux d’activité de pare-feu sont disponibles pour les ressources Application Gateway pour lesquelles WAF est activé.
- **Archivage des journaux** : tous les journaux de diagnostic sont archivés dans un compte de stockage Azure centralisé et chiffré, et sont conservés pendant une période prédéfinie de deux jours. Ces journaux se connectent à Journaux Azure Monitor à des fins de traitement, de stockage et de génération de rapports de tableau de bord.

En outre, cette architecture intègre également les solutions de supervision suivantes :
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) : la solution Azure Automation stocke, exécute et gère les runbooks.
-   [Security and Audit](../../security-center/security-center-intro.md) : le tableau de bord Security and Audit donne une vue d’ensemble de l’état de sécurité des ressources en affichant des métriques concernant des domaines de sécurité, des problèmes notables, des détections, des menaces et des requêtes de sécurité courantes.
-   [SQL Assessment](../../azure-monitor/insights/sql-assessment.md) : la solution SQL Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit aux clients une liste hiérarchisée de suggestions spécifiques pour l’infrastructure de serveur déployée.
-   [Journaux d’activité Azure](../../azure-monitor/platform/collect-activity-logs.md) : la solution Activity Log Analytics facilite l’analyse des journaux d’activité Azure de tous les abonnements Azure d’un client.

### <a name="identity-management"></a>Gestion des identités
-   L’authentification auprès de l’application est effectuée à l’aide d’Azure AD. Pour plus d’informations, consultez [Intégration d’applications dans Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). En outre, le chiffrement des colonnes de base de données utilise Azure AD pour authentifier l’application auprès d’Azure SQL Database. Pour plus d’informations, découvrez comment [protéger les données sensibles dans SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   La solution [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) détecte les vulnérabilités risquant d’affecter les identités d’une organisation, configure des réponses automatiques aux actions suspectes détectées en lien avec les identités d’une organisation, et examine les incidents suspects afin de prendre les mesures appropriées pour résoudre ces derniers.
-   Le [contrôle d’accès en fonction du rôle (RBAC) Azure](../../role-based-access-control/role-assignments-portal.md) permet une gestion précise de l’accès pour Azure. Par exemple, l’accès aux abonnements est limité à l’administrateur d’abonnements.

Pour plus d’informations sur l’utilisation des fonctionnalités de sécurité d’Azure SQL Database, consultez l’exemple [Application de démonstration Contoso Clinic](https://github.com/Microsoft/azure-sql-security-sample).

### <a name="security"></a>Sécurité
**Gestion des secrets** : la solution utilise [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) pour gérer les clés et les secrets. Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud.

## <a name="guidance-and-recommendations"></a>Instructions et recommandations

### <a name="expressroute-and-vpn"></a>ExpressRoute et VPN
Il convient de configurer [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou un tunnel VPN sécurisé pour établir une connexion sécurisée aux ressources déployées dans le cadre de cette architecture de référence en matière d’analytique des données. Les connexions ExpressRoute ne transitent pas par Internet, et offrent de meilleurs niveaux de fiabilité, de rapidité, de latence et de sécurité que les connexions classiques via Internet. En configurant correctement un VPN ou un routage ExpressRoute, les clients peuvent ajouter une couche de protection au niveau des données en transit.

### <a name="azure-active-directory-setup"></a>Installation d’Azure Active Directory
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) joue un rôle essentiel dans la gestion du déploiement et de la configuration des accès des membres du personnel qui interagissent avec l’environnement. Une instance Windows Server Active Directory existante peut être intégrée à AAD en [quatre clics](../../active-directory/hybrid/how-to-connect-install-express.md). Les clients peuvent également associer l’infrastructure (contrôleurs de domaine) Active Directory déployée à un service AAD existant en définissant cette infrastructure comme sous-domaine d’une forêt AAD.

### <a name="additional-services"></a>Services supplémentaires
#### <a name="iaas---vm-considerations"></a>Iaas - Considérations relatives à la machine virtuelle
Cette solution PaaS n’intègre pas toutes les machines virtuelles Azure IaaS. Un client peut créer une machine virtuelle Azure pour exécuter la plupart de ces services PaaS. Dans ce cas, il est possible de tirer profit de fonctionnalités et de services spécifiques pour la continuité d’activité et les journaux Azure Monitor :

##### <a name="business-continuity"></a>Continuité de l’activité
- **Haute disponibilité** : les charges de travail serveur sont regroupées dans un [groupe à haute disponibilité](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pour garantir la très grande disponibilité des machines virtuelles dans Azure. Au moins une machine virtuelle est disponible pendant un événement de maintenance planifié ou non, ce qui est conforme au contrat de niveau de service Azure garantissant une disponibilité de 99,95 %.

- **Coffre Recovery Services** : le [coffre Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) héberge les données de sauvegarde et protège toutes les configurations des machines virtuelles Azure dans cette architecture. Grâce au coffre Recovery Services, les clients peuvent restaurer des fichiers et dossiers d’une machine virtuelle IaaS sans avoir à restaurer l’intégralité de celle-ci, ce qui permet d’accélérer les temps de restauration.

##### <a name="monitoring-solutions"></a>Solutions de supervision
-   [AD Assessment](../../azure-monitor/insights/ad-assessment.md) : la solution Active Directory Health Check évalue les risques et l’intégrité des environnements de serveur à intervalles réguliers, et fournit une liste hiérarchisée de suggestions spécifiques pour l’infrastructure de serveur déployée.
-   [Antimalware Assessment](../../security-center/security-center-install-endpoint-protection.md) : la solution de logiciel anti-programme malveillant signale les programmes malveillants, les menaces et l’état de protection.
-   [Update Management](../../automation/automation-update-management.md) : cette solution de gestion des mises à jour aide les clients à gérer les mises à jour de sécurité du système d’exploitation, notamment en leur indiquant l’état des mises à jour disponibles et le processus d’installation des mises à jour requises.
-   [Agent Health](../../monitoring/monitoring-solution-agenthealth.md) : cette solution de contrôle d’intégrité des agents indique le nombre d’agents déployés et leur répartition géographique, ainsi que le nombre d’agents qui ne répondent pas ou qui envoient des données opérationnelles.
-   [Change Tracking](../../automation/change-tracking.md) : cette solution de suivi des modifications permet aux clients d’identifier facilement des modifications dans l’environnement.

##### <a name="security"></a>Sécurité
- **Protection contre les programmes malveillants** : [Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) pour Machines Virtuelles fournit une protection en temps réel qui permet d’identifier et de supprimer les virus, les logiciels espions et autres logiciels malveillants grâce à des alertes configurables vous avertissant quand des logiciels malveillants ou indésirables connus tentent de s’installer ou de s’exécuter sur des machines virtuelles protégées.
- **Gestion des correctifs** : les machines virtuelles Windows déployées dans le cadre de cette architecture de référence sont configurées par défaut pour recevoir des mises à jour automatiques du service Windows Update. Cette solution inclut également le service [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) qui permet de créer des déploiements mis à jour pour corriger des machines virtuelles si nécessaire.

#### <a name="azure-commercial"></a>Azure Commercial
Bien que cette architecture d’analytique des données ne soit pas conçue pour être déployée dans l’environnement [Azure Commercial](https://azure.microsoft.com/overview/what-is-azure/), des résultats similaires peuvent être obtenus en utilisant les services décrits dans cette architecture de référence, ainsi que par le biais de services uniquement disponibles dans l’environnement Azure Commercial. Veuillez noter qu’Azure Commercial conserve une autorisation provisoire d’exploitation (P-ATO) JAB FedRamp au niveau Moderate Impact (impact modéré), ce qui permet aux partenaires et organismes gouvernementaux de déployer de façon modérée des informations sensibles dans le cloud en utilisant l’environnement Azure Commercial.

Azure Commercial offre une grande variété de services d’analytique permettant d’extraire des informations à partir de grandes quantités de données :
-   [Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/what-is-ml-studio), un composant de [Cortana Intelligence Suite](https://azure.microsoft.com/overview/ai-platform/), développe un modèle d’analyse prédictive à partir d’une ou plusieurs sources de données. Les fonctions statistiques permettent de générer un modèle efficace à partir de plusieurs itérations, que les applications telles que Power BI peuvent ensuite utiliser.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview), qui permet de capturer les données quels que soient la taille, le type et la vitesse d’ingestion dans un emplacement unique en vue d’une analyse opérationnelle et exploratoire. Azure Data Lake Store est compatible avec la plupart des composants open source dans l’écosystème Hadoop et s’intègre facilement à d’autres services Azure.

## <a name="threat-model"></a>Modèle de menace

Le diagramme de flux de données (DFD) pour cette architecture de référence figure ci-dessous et est également disponible en [téléchargement](https://aka.ms/blueprintanalyticsthreatmodel) :

## <a name="compliance-documentation"></a>Documentation sur la conformité
Le document [Azure Security and Compliance Blueprint – FedRAMP High Customer Responsibility Matrix](https://aka.ms/blueprinthighcrm) (Azure Blueprint Sécurité et conformité - Matrice de responsabilités des clients FedRAMP High) répertorie tous les contrôles de sécurité requis par la ligne de base FedRAMP High. De la même façon, le document [Azure Security and Compliance Blueprint – FedRAMP Moderate Customer Responsibility Matrix](https://aka.ms/blueprintanalyticscimmod) (Azure Blueprint Sécurité et conformité - Matrice de responsabilités des clients FedRAMP Moderate) répertorie tous les contrôles de sécurité requis par la ligne de base FedRAMP Moderate. Ces deux documents indiquent si l’implémentation de chacun des contrôles revient à Microsoft, au client ou aux deux.

Les documents [Azure Security and Compliance Blueprint - FedRAMP High Control Implementation Matrix](https://aka.ms/blueprintanalyticscimhigh) (Azure Blueprint Sécurité et conformité - Matrice d’implémentation de contrôle FedRAMP High) et [Azure Security and Compliance Blueprint - FedRAMP Moderate Control Implementation Matrix](https://aka.ms/blueprintanalyticscimmod) (Azure Blueprint Sécurité et conformité - Matrice d’implémentation de contrôle FedRAMP Moderate) fournissent des informations sur les contrôles couverts par l’architecture d’analyse de données pour chaque ligne de base FedRAMP, y compris des descriptions détaillées sur la façon dont l’implémentation satisfait aux exigences de chaque contrôle couvert.

## <a name="disclaimer"></a>Clause d'exclusion de responsabilité

 - Ce document est fourni à titre d’information uniquement. MICROSOFT N’ACCORDE AUCUNE GARANTIE EXPRESSE, IMPLICITE OU STATUTAIRE EN LIEN AVEC LES INFORMATIONS CONTENUES DANS CE DOCUMENT. Ce document est fourni « en l’état ». Les informations et les points de vue exprimés dans ce document, y compris les URL et autres références à des sites web, peuvent être modifiés sans préavis. Les clients qui lisent ce document assument les risques liés à son utilisation.
 - Ce document n’accorde aux clients aucun droit légal de propriété intellectuelle sur les produits ou solutions Microsoft.
 - Les clients peuvent copier et utiliser ce document pour un usage interne, à titre de référence.
 - Certaines recommandations contenues dans ce document peuvent entraîner une augmentation des taux d’utilisation des données, des réseaux ou des ressources de calcul dans Azure, débouchant sur des coûts de licence ou d’abonnement supplémentaires.
 - Cette architecture constitue une base que les clients peuvent modifier en fonction de leurs besoins. Elle ne doit pas être utilisée telle quelle dans un environnement de production.
 - Développé en tant que référence, ce document ne doit pas être utilisé pour définir tous les moyens par lesquels un client peut satisfaire à des réglementations et exigences de conformité spécifiques. Les clients doivent rechercher une assistance juridique auprès de leur organisation pour connaître les implémentations client approuvées.
